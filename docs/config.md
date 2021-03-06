# CRI Plugin Config Guide
This document provides the description of the CRI plugin configuration.
The CRI plugin config is part of the containerd config (default
path: `/etc/containerd/config.toml`).

See [here](https://github.com/containerd/containerd/blob/master/docs/ops.md)
for more information about containerd config.

The explanation and default value of each configuration item are as follows:
```toml
# Use config version 2 to enable new configuration fields.
# Config file is parsed as version 1 by default.
version = 2

# The 'plugins."io.containerd.grpc.v1.cri"' table contains all of the server options.
[plugins."io.containerd.grpc.v1.cri"]

  # disable_tcp_service disables serving CRI on the TCP server.
  # Note that a TCP server is enabled for containerd if TCPAddress is set in section [grpc].
  disable_tcp_service = true

  # stream_server_address is the ip address streaming server is listening on.
  stream_server_address = "127.0.0.1"

  # stream_server_port is the port streaming server is listening on.
  stream_server_port = "0"

  # stream_idle_timeout is the maximum time a streaming connection can be
  # idle before the connection is automatically closed.
  # The string is in the golang duration format, see:
  #   https://golang.org/pkg/time/#ParseDuration
  stream_idle_timeout = "4h"

  # enable_selinux indicates to enable the selinux support.
  enable_selinux = false

  # sandbox_image is the image used by sandbox container.
  sandbox_image = "k8s.gcr.io/pause:3.1"

  # stats_collect_period is the period (in seconds) of snapshots stats collection.
  stats_collect_period = 10

  # enable_tls_streaming enables the TLS streaming support.
  # It generates a self-sign certificate unless the following x509_key_pair_streaming are both set.
  enable_tls_streaming = false

  # 'plugins."io.containerd.grpc.v1.cri".x509_key_pair_streaming' contains a x509 valid key pair to stream with tls.
  [plugins."io.containerd.grpc.v1.cri".x509_key_pair_streaming]
    # tls_cert_file is the filepath to the certificate paired with the "tls_key_file"
    tls_cert_file = ""

    # tls_key_file is the filepath to the private key paired with the "tls_cert_file"
    tls_key_file = ""

  # max_container_log_line_size is the maximum log line size in bytes for a container.
  # Log line longer than the limit will be split into multiple lines. -1 means no
  # limit.
  max_container_log_line_size = 16384

  # disable_cgroup indicates to disable the cgroup support.
  # This is useful when the daemon does not have permission to access cgroup.
  disable_cgroup = false

  # disable_apparmor indicates to disable the apparmor support.
  # This is useful when the daemon does not have permission to access apparmor.
  disable_apparmor = false

  # restrict_oom_score_adj indicates to limit the lower bound of OOMScoreAdj to
  # the containerd's current OOMScoreAdj.
  # This is useful when the containerd does not have permission to decrease OOMScoreAdj.
  restrict_oom_score_adj = false

  # max_concurrent_downloads restricts the number of concurrent downloads for each image.
  max_concurrent_downloads = 3

  # disable_proc_mount disables Kubernetes ProcMount support. This MUST be set to `true`
  # when using containerd with Kubernetes <=1.11.
  disable_proc_mount = false

  # 'plugins."io.containerd.grpc.v1.cri".containerd' contains config related to containerd
  [plugins."io.containerd.grpc.v1.cri".containerd]

    # snapshotter is the snapshotter used by containerd.
    snapshotter = "overlayfs"

    # no_pivot disables pivot-root (linux only), required when running a container in a RamDisk with runc.
    # This only works for runtime type "io.containerd.runtime.v1.linux".
    no_pivot = false

    # default_runtime_name is the default runtime name to use.
    default_runtime_name = "runc"

    # 'plugins."io.containerd.grpc.v1.cri".containerd.default_runtime' is the runtime to use in containerd.
    # DEPRECATED: use `default_runtime_name` and `plugins."io.containerd.grpc.v1.cri".runtimes` instead.
    # Remove in containerd 1.4.
    [plugins."io.containerd.grpc.v1.cri".containerd.default_runtime]

    # 'plugins."io.containerd.grpc.v1.cri".containerd.untrusted_workload_runtime' is a runtime to run untrusted workloads on it.
    # DEPRECATED: use `untrusted` runtime in `plugins."io.containerd.grpc.v1.cri".runtimes` instead.
    # Remove in containerd 1.4.
    [plugins."io.containerd.grpc.v1.cri".containerd.untrusted_workload_runtime]

    # 'plugins."io.containerd.grpc.v1.cri".containerd.runtimes' is a map from CRI RuntimeHandler strings, which specify types
    # of runtime configurations, to the matching configurations.
    # In this example, 'runc' is the RuntimeHandler string to match.
    [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc]
      # runtime_type is the runtime type to use in containerd e.g. io.containerd.runtime.v1.linux
      runtime_type = "io.containerd.runc.v1"

      # pod_annotations is a list of pod annotations passed to both pod
      # sandbox as well as container OCI annotations. Pod_annotations also
      # supports golang path match pattern - https://golang.org/pkg/path/#Match.
      # e.g. ["runc.com.*"], ["*.runc.com"], ["runc.com/*"].
      #
      # For the naming convention of annotation keys, please reference:
      # * Kubernetes: https://kubernetes.io/docs/concepts/overview/working-with-objects/annotations/#syntax-and-character-set
      # * OCI: https://github.com/opencontainers/image-spec/blob/master/annotations.md
      pod_annotations = []

      # privileged_without_host_devices allows overloading the default behaviour of passing host
      # devices through to privileged containers. This is useful when using a runtime where it does
      # not make sense to pass host devices to the container when privileged. Defaults to false -
      # i.e pass host devices through to privileged containers.
      privileged_without_host_devices = false

      # 'plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options' is options specific to
      # "io.containerd.runc.v1". Its corresponding options type is:
      #   https://github.com/containerd/containerd/blob/v1.2.0-rc.1/runtime/v2/runc/options/oci.pb.go#L39.
      [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
        # NoPivotRoot disables pivot root when creating a container.
        NoPivotRoot = false

        # NoNewKeyring disables new keyring for the container.
        NoNewKeyring = false

        # ShimCgroup places the shim in a cgroup.
        ShimCgroup = ""

        # IoUid sets the I/O's pipes uid.
        IoUid = 0

        # IoGid sets the I/O's pipes gid.
        IoGid = 0

        # BinaryName is the binary name of the runc binary.
        BinaryName = ""

        # Root is the runc root directory.
        Root = ""

        # CriuPath is the criu binary path.
        CriuPath = ""

        # SystemdCgroup enables systemd cgroups.
        SystemdCgroup = false

  # 'plugins."io.containerd.grpc.v1.cri".cni' contains config related to cni
  [plugins."io.containerd.grpc.v1.cri".cni]
    # bin_dir is the directory in which the binaries for the plugin is kept.
    bin_dir = "/opt/cni/bin"

    # conf_dir is the directory in which the admin places a CNI conf.
    conf_dir = "/etc/cni/net.d"

    # max_conf_num specifies the maximum number of CNI plugin config files to
    # load from the CNI config directory. By default, only 1 CNI plugin config
    # file will be loaded. If you want to load multiple CNI plugin config files
    # set max_conf_num to the number desired. Setting max_config_num to 0 is
    # interpreted as no limit is desired and will result in all CNI plugin
    # config files being loaded from the CNI config directory. 
    max_conf_num = 1

    # conf_template is the file path of golang template used to generate
    # cni config.
    # If this is set, containerd will generate a cni config file from the
    # template. Otherwise, containerd will wait for the system admin or cni
    # daemon to drop the config file into the conf_dir.
    # This is a temporary backward-compatible solution for kubenet users
    # who don't have a cni daemonset in production yet.
    # This will be deprecated when kubenet is deprecated.
    conf_template = ""

  # 'plugins."io.containerd.grpc.v1.cri".registry' contains config related to the registry
  [plugins."io.containerd.grpc.v1.cri".registry]

    # 'plugins."io.containerd.grpc.v1.cri".registry.mirrors' are namespace to mirror mapping for all namespaces.
    [plugins."io.containerd.grpc.v1.cri".registry.mirrors]
      [plugins."io.containerd.grpc.v1.cri".registry.mirrors."docker.io"]
        endpoint = ["https://registry-1.docker.io", ]
```

## Untrusted Workload

The recommended way to run untrusted workload is to use
[`RuntimeClass`](https://kubernetes.io/docs/concepts/containers/runtime-class/) api
introduced in Kubernetes 1.12 to select RuntimeHandlers configured to run
untrusted workload in `plugins."io.containerd.grpc.v1.cri".containerd.runtimes`.

However, if you are using the legacy `io.kubernetes.cri.untrusted-workload`pod annotation
to request a pod be run using a runtime for untrusted workloads, the RuntimeHandler
`plugins."io.containerd.grpc.v1.cri"cri.containerd.runtimes.untrusted` must be defined first.
When the annotation `io.kubernetes.cri.untrusted-workload` is set to `true` the `untrusted`
runtime will be used. For example, see
[Create an untrusted pod using Kata Containers](https://github.com/kata-containers/documentation/blob/master/how-to/how-to-use-k8s-with-cri-containerd-and-kata.md#create-an-untrusted-pod-using-kata-containers).

## Deprecation
The config options of the CRI plugin follow the [Kubernetes deprecation
policy of "admin-facing CLI components"](https://kubernetes.io/docs/reference/using-api/deprecation-policy/#deprecating-a-flag-or-cli).

In summary, when a config option is announced to be deprecated:
* It is kept functional for 6 months or 1 release (whichever is longer);
* A warning is emitted when it is used.
