---
title: rmw_create_node failed to create domain, error Error failed to increase socket receive buffer size to at least xxx bytes, current is yyy bytes.
date: 2026-02-15 20:45:00 +0900
categories: [ROS2]
tags: [jazzy, rmw, cyclonedds]
pin: false
toc: true
--- 


## 環境

| 項目 | 内容 |
|------|------|
| CPU | 11th Gen Intel(R) Core(TM) i9-11900H @ 2.50GHz |
| OS | Ubuntu 24.04.4 LTS |

## 問題

- Node実行時にUDPソケットのバッファサイズの増加リクエストが失敗し、Nodeの作成に失敗してしまう。
    ```bash
    [test-node] [0] test_node: failed to increase socket receive buffer size to at least xxx bytes, current is yyy bytes

    [test_node] [ERROR] [1771113583.072708065] [rmw_cyclonedds_cpp] [./src/rmw_node.cpp:1242:check_create_domain]: rmw_create_node: failed to create domain, error Error
    [test_node] /opt/ros/jazzy/lib/python3.12/site-packages/rclpy/node.py:188: RuntimeWarning: Failed to fini rosout publisher: rcl node implementation is invalid, at ./src/rcl/node.c:390
    [test_node]   self.__node = _rclpy.Node(
    [test_node] Traceback (most recent call last):
    [test_node]   File "/opt/ros/jazzy/lib/test_node/test_node", line 33, in <module>
    [test_node]     sys.exit(load_entry_point('test_node', 'console_scripts', 'test_node')())
    [test_node]              ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    [test_node]   File "/opt/ros/jazるpython3.12/site-packages/test_node/test_node.py", line 284, in main
    [test_node]     JointStatePublisher(parsed_args.urdf_file))
    [test_node]     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    [test_node]   File "/opt/ros/jazzy/lib/python3.12/site-packages/test_node/test_node.py", line 266, in __init__
    [test_node]     super().__init__('test-node', automatically_declare_parameters_from_overrides=True)
    [test_node]   File "/opt/ros/jazzy/lib/python3.12/site-packages/rclpy/node.py", line 188, in __init__
    [test_node]     self.__node = _rclpy.Node(
    [test_node]                   ^^^^^^^^^^^^
    [test_node] rclpy._rclpy_pybind11.RCLError: error creating node: error not set, at ./src/rcl/node.c:252
    [ERROR] [test_node]: process has died [pid 4644, exit code 1, cmd '/opt/ros/jazzy/lib/test_node/test_node --ros-args'].
    ```
## 原因
- OSで設定しているUDPソケットの受信バッファの最大サイズが小さい。

## 対処 
- net.core.rmem_maxとnet.core.rmem_defaultの値をCycloneDDSの設定より大きい値に設定する。
    ```bash
    echo "net.core.rmem_max=11534336\nnet.core.rmem_default=11534336\n" | sudo tee /etc/sysctl.d/60-cyclonedds.conf
    sudo sysctl --system
    ```


## 詳細
### CycloneDDS
- CycloneDDSはECLIPS Foundationが開発・公開しているDDS。
    - 参考：https://projects.eclipse.org/projects/iot.cyclonedds
    - ライセンスは、Eclipse Distribution License 1.0 (BSD-3-Clause)、Eclipse Public License 2.0
    - ソースコードはGithubで公開されている。
        - https://github.com/eclipse-cyclonedds/cyclonedds

- JazzyでCycloneDDSを使用する場合には、aptからros-jazzy-cyclone-ddsとros-jazzy-cyclonedds-cppをインストールする。
    - ros-jazzy-cyclone-dds
        ```bash
        $ apt info ros-jazzy-cyclonedds
        Package: ros-jazzy-cyclonedds
        Version: 0.10.5-1noble.20260121.181209
        Priority: optional
        Section: misc
        Maintainer: Eclipse Foundation, Inc. <cyclonedds-dev@eclipse.org>
        Installed-Size: 3882 kB
        Depends: libc6 (>= 2.38), libssl3t64 (>= 3.0.0), openssl, ros-jazzy-iceoryx-binding-c, ros-jazzy-iceoryx-hoofs, ros-jazzy-iceoryx-posh, ros-jazzy-ros-workspace
        Homepage: https://projects.eclipse.org/projects/iot.cyclonedds
        Download-Size: 1037 kB
        APT-Manual-Installed: no
        APT-Sources: http://packages.ros.org/ros2/ubuntu noble/main amd64 Packages
        Description: Eclipse Cyclone DDS is a very performant and robust open-source DDS implementation.
         Cyclone DDS is developed completely in the open as an Eclipse IoT project.
        ```
    - ros-jazzy-rmw-cyclonedds-cpp
        ```bash
        $ apt info ros-jazzy-rmw-cyclonedds-cpp
        Package: ros-jazzy-rmw-cyclonedds-cpp
        Version: 2.2.3-1noble.20260126.172802
        Priority: optional
        Section: misc
        Maintainer: Erik Boasson <erik.boasson@adlinktech.com>
        Installed-Size: 548 kB
        Depends: libc6 (>= 2.32), libgcc-s1 (>= 3.3.1), libstdc++6 (>= 13.1), ros-jazzy-cyclonedds, ros-jazzy-iceoryx-binding-c, ros-jazzy-rcpputils, ros-jazzy-rcutils, ros-jazzy-rmw, ros-jazzy-rmw-dds-common, ros-jazzy-rosidl-runtime-c, ros-jazzy-rosidl-typesupport-introspection-c, ros-jazzy-rosidl-typesupport-introspection-cpp, ros-jazzy-tracetools, ros-jazzy-ros-workspace
        Download-Size: 176 kB
        APT-Manual-Installed: yes
        APT-Sources: http://packages.ros.org/ros2/ubuntu noble/main amd64 Packages
        Description: Implement the ROS middleware interface using Eclipse CycloneDDS in C++.
        ```
- "failed to increase socket receive buffer size to at least xxx bytes, current is yyy bytes"はCycloneDDSから出力されている。
    - src/core/ddsi/src/ddsi_udp.cの520行目において、受信バッファサイズを設定する関数（set_rcvbuf）があり、この中でデフォルトのバッファサイズを1048576としている。
        ```c
        static dds_return_t set_rcvbuf (struct ddsi_domaingv const * const gv, ddsrt_socket_t sock, const struct ddsi_config_socket_buf_size *config)
        {
          return set_socket_buffer (gv, sock, SO_RCVBUF, "SO_RCVBUF", "receive", config, 1048576);
        }
        ```
    - set_socket_bufferはddsi_udp.cの451行目で定義されており、この関数のif (actsize >= socket_req_buf_size)のチェックで引っかかり、上記ログが出力されている。
        ```c
        if (actsize >= socket_req_buf_size)
          GVLOG (DDS_LC_CONFIG, "socket %s buffer size set to %"PRIu32" bytes\n", name, actsize);
        else if (actsize >= socket_min_buf_size)
          GVLOG (DDS_LC_CONFIG,
                 "failed to increase socket %s buffer size to %"PRIu32" bytes, continuing with %"PRIu32" bytes\n",
                 name, socket_req_buf_size, actsize);
        else　<-- この分岐
        {
          /* If the configuration states it must be >= X, then error out if the
             kernel doesn't give us at least X */
          GVLOG (DDS_LC_CONFIG | DDS_LC_ERROR,
                 "failed to increase socket %s buffer size to at least %"PRIu32" bytes, current is %"PRIu32" bytes\n",
                 name, socket_min_buf_size, actsize);
          rc = DDS_RETCODE_NOT_ENOUGH_SPACE;
        }
        ```
    - このとき、actsizeには425984が値として入っており、またsocket_req_buf_sizeには10485760が入っている。
        - socket_req_buf_sizeは、ddsi_udp.cの465行目で定義されており、バッファのMaxサイズが指定されていれば、Maxサイズを使用し、Minサイズが設定されていればMinサイズを使用する。どちらも設定されていない場合には、default_min_size(set_socket_bufferの最後の引数で、1048576が設定されている)が使用される。
            ```c
            const uint32_t socket_req_buf_size = // size to request
            (!config->max.isdefault && config->max.value > socket_min_buf_size) ? config->max.value
            : !config->min.isdefault ? config->min.value
            : default_min_size;
            ```
        - actsizeはddsrt_getsockoptで値が設定される。ddsrt_getsockoptは、/src/ddsrt/src/sockets/posix/socket.cで定義されており、中でgetsockoptを呼んでいる。
            ```c 
            dds_return_t
            ddsrt_getsockopt(
              ddsrt_socket_t sock,
              int32_t level,
              int32_t optname,
              void *optval,
              socklen_t *optlen)
            {
            #if defined(__ZEPHYR__)
              if (optname == IP_ADD_MEMBERSHIP || optname == IP_DROP_MEMBERSHIP)
              {
                /* note ddsrt_getsockopt never called with this optname */
                return DDS_RETCODE_UNSUPPORTED;
              }
            #endif

              if (getsockopt(sock, level, optname, optval, optlen) == 0)
                return DDS_RETCODE_OK;

              switch (errno) {
                case EBADF:
                case EFAULT:
                case EINVAL:
                case ENOTSOCK:
                  return DDS_RETCODE_BAD_PARAMETER;
                case ENOPROTOOPT:
                  return DDS_RETCODE_UNSUPPORTED;
                default:
                  break;
              }

              return DDS_RETCODE_ERROR;
            }
            ```
            - getsocketoptはLinuxカーネルの関数であり、実態はnet/core/sock.cで定義されている[sk_getsockopt](https://github.com/torvalds/linux/blob/ca4ee40bf13dbd3a4be3b40a00c33a1153d487e5/net/core/sock.c#L1725)で、そこで、sk_rcvbufから値を読み取る。
                ```c
                int sk_getsockopt(struct sock *sk, int level, int optname,
		        sockptr_t optval, sockptr_t optlen)
                {
                    ~~~
                	switch (optname) {
                    ~~~
                	case SO_RCVBUF:
                		v.val = READ_ONCE(sk->sk_rcvbuf);
                		break;
                ```
            - sk_rcvbufはソケットの作成時の[init](https://github.com/torvalds/linux/blob/ca4ee40bf13dbd3a4be3b40a00c33a1153d487e5/net/core/sock.c#L3696)で設定され、system_rmem_defaultの値が設定される。
                ```c
                void sock_init_data_uid(struct socket *sock, struct sock *sk, kuid_t uid)
                {
                	sk_init_common(sk);
                	sk->sk_send_head	=	NULL;

                	timer_setup(&sk->sk_timer, NULL, 0);

                	sk->sk_allocation	=	GFP_KERNEL;
                	sk->sk_rcvbuf		=	READ_ONCE(sysctl_rmem_default);
                ```
            - sysctl_rmem_defaultはnet.core.rmem_defaultであり、現環境では212992が設定されている
                ```bash
                $ sysctl net.core.rmem_default
                net.core.rmem_default = 212992
                ```
            - net/core/sock.c の sk_setsockoptで[__sock_set_rcvbuf](https://github.com/torvalds/linux/blob/ca4ee40bf13dbd3a4be3b40a00c33a1153d487e5/net/core/sock.c#L1367)が呼ばれ、ソケットバッファサイズがnet.core.rmem_defaultの2倍確保される。
                - min_t(u32, val, READ_ONCE(sysctl_rmem_max))のvalにはCycloneDDSで設定されている10485760が入り、sysctl_rmem_maxの値が選択される。
                ```c
                int sk_setsockopt(struct sock *sk, int level, int optname,
		        sockptr_t optval, unsigned int optlen)
                {
                ~~~~
	            case SO_RCVBUF:
	            	/* Don't error on this BSD doesn't and if you think
	            	 * about it this is right. Otherwise apps have to
	            	 * play 'guess the biggest size' games. RCVBUF/SNDBUF
	            	 * are treated in BSD as hints
	            	 */
	            	__sock_set_rcvbuf(sk, min_t(u32, val, READ_ONCE(sysctl_rmem_max)));
	            	break;
                ```
                - 2倍にしている理由は、実データのメタデータ（sk_buff）が実データと同じ程度バッファを使用するためである。

                ```c
                static void __sock_set_rcvbuf(struct sock *sk, int val)
                {
                	/* Ensure val * 2 fits into an int, to prevent max_t() from treating it
                	 * as a negative value.
                	 */
                	val = min_t(int, val, INT_MAX / 2);
                	sk->sk_userlocks |= SOCK_RCVBUF_LOCK;

                	/* We double it on the way in to account for "struct sk_buff" etc.
                	 * overhead.   Applications assume that the SO_RCVBUF setting they make
                	 * will allow that much actual data to be received on that socket.
                	 *
                	 * Applications are unaware that "struct sk_buff" and other overheads
                	 * allocate from the receive buffer during socket buffer allocation.
                	 *
                	 * And after considering the possible alternatives, returning the value
                	 * we actually used in getsockopt is the most desirable behavior.
                	 */
                	WRITE_ONCE(sk->sk_rcvbuf, max_t(int, val * 2, SOCK_MIN_RCVBUF));
                }
                ```

            - 上記により、212992の2倍の425984がactsizeに入り、actsize >= socket_req_buf_sizeにおいて、elseの分岐に入る。
                ```c
                /* 425984 >= 10485760となり、Falseとなる*/       
                actsize >= socket_req_buf_size
                ```
- rmw_cycloneddsのREADMEでは、[通信遅延が大きい場合には、rmem_maxとrmem_defaultを大きくすることを推奨している](https://github.com/ros2/rmw_cyclonedds?tab=readme-ov-file#performance-recommendations)。
    ```bash
    # Permanently Settings
    echo "net.core.rmem_max=8388608\nnet.core.rmem_default=8388608\n" | sudo tee /etc/sysctl.d/60-cyclonedds.conf
    ```
