Environment:
- Dell Precision 3610
- 64GB DDR3 @ 1333 MHz
- CPU is a Xeon E5-2680v2 @ 2.8GHz with 10 cores and virtualization enabled providing 20 logical processors
- Windows 10
- Docker Desktop

Started off by downloading talosctl-windows-amd64.exe from https://github.com/siderolabs/talos/releases/tag/v1.6.7 and renaming it talosctl.exe same for kubectl by following the instructions at https://kubernetes.io/docs/tasks/tools/install-kubectl-windows/

```
PS S:\Kubernetes\talos>.\talosctl.exe cluster create
validating CIDR and reserving IPs
```

After a few moments I was able to see the VMs created in Docker Desktop and run some kubectl commands

```
PS S:\Kubernetes\talos> kubectl config current-context
admin@talos-default
PS S:\Kubernetes\talos> kubectl get nodes -o wide
NAME                           STATUS   ROLES           AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE         KERNEL-VERSION                       CONTAINER-RUNTIME
talos-default-controlplane-1   Ready    control-plane   24h   v1.29.3   10.5.0.2      <none>        Talos (v1.6.7)   5.15.146.1-microsoft-standard-WSL2   containerd://1.7.13
talos-default-worker-1         Ready    <none>          24h   v1.29.3   10.5.0.3      <none>        Talos (v1.6.7)   5.15.146.1-microsoft-standard-WSL2   containerd://1.7.13
talos-default-worker-2         Ready    <none>          24h   v1.29.3   10.5.0.4      <none>        Talos (v1.6.7)   5.15.146.1-microsoft-standard-WSL2   containerd://1.7.13
talos-default-worker-3         Ready    <none>          24h   v1.29.3   10.5.0.5      <none>        Talos (v1.6.7)   5.15.146.1-microsoft-standard-WSL2   containerd://1.7.13
```

Next I wanted to see the mounts available

```
PS S:\Kubernetes\talos> .\talosctl.exe mounts -n 10.5.0.3
NODE       FILESYSTEM   SIZE(GB)   USED(GB)   AVAILABLE(GB)   PERCENT USED   MOUNTED ON
10.5.0.3   overlay      269.43     35.45      233.98          13.16%         /
10.5.0.3   tmpfs        0.07       0.00       0.07            0.00%          /dev
10.5.0.3   tmpfs        16.81      0.00       16.81           0.00%          /sys/fs/cgroup
10.5.0.3   shm          0.07       0.00       0.07            0.00%          /dev/shm
10.5.0.3   /dev/sde     269.43     35.45      233.98          13.16%         /opt
10.5.0.3   /dev/sde     269.43     35.45      233.98          13.16%         /var
10.5.0.3   tmpfs        16.81      0.00       16.81           0.00%          /system
10.5.0.3   tmpfs        16.81      0.00       16.81           0.00%          /run
10.5.0.3   tmpfs        16.81      0.00       16.81           0.00%          /tmp
10.5.0.3   /dev/sde     269.43     35.45      233.98          13.16%         /system/state
10.5.0.3   /dev/sde     269.43     35.45      233.98          13.16%         /etc/cni
10.5.0.3   /dev/sde     269.43     35.45      233.98          13.16%         /etc/kubernetes
10.5.0.3   /dev/sde     269.43     35.45      233.98          13.16%         /usr/libexec/kubernetes
10.5.0.3   tmpfs        16.81      0.00       16.81           0.00%          /etc/cri/conf.d/hosts
10.5.0.3   overlay      269.43     35.45      233.98          13.16%         /run/containerd/io.containerd.runtime.v2.task/system/kubelet/rootfs
10.5.0.3   tmpfs        16.81      0.00       16.81           0.00%          /sys/fs/cgroup
10.5.0.3   tmpfs        33.32      0.00       33.32           0.00%          /var/lib/kubelet/pods/ddee29d2-93b9-4930-a9cc-aa8e1f4a7f02/volumes/kubernetes.io~projected/kube-api-access-js8bb
10.5.0.3   tmpfs        33.32      0.00       33.32           0.00%          /var/lib/kubelet/pods/caf579eb-2515-4cfa-a43c-0981d85dd15a/volumes/kubernetes.io~projected/kube-api-access-26lwg
10.5.0.3   shm          0.07       0.00       0.07            0.00%          /run/containerd/io.containerd.grpc.v1.cri/sandboxes/39c995b9b24136eda7e9d174364fa5cb6665b3b266a7c07fd9084cd55d3b6543/shm
10.5.0.3   shm          0.07       0.00       0.07            0.00%          /run/containerd/io.containerd.grpc.v1.cri/sandboxes/208909683a9bd134dfc96c4aa9b5053797889e6ec0e6cc33d0a0cb981d2fe46b/shm
10.5.0.3   overlay      269.43     35.45      233.98          13.16%         /run/containerd/io.containerd.runtime.v2.task/k8s.io/39c995b9b24136eda7e9d174364fa5cb6665b3b266a7c07fd9084cd55d3b6543/rootfs
10.5.0.3   overlay      269.43     35.45      233.98          13.16%         /run/containerd/io.containerd.runtime.v2.task/k8s.io/208909683a9bd134dfc96c4aa9b5053797889e6ec0e6cc33d0a0cb981d2fe46b/rootfs
10.5.0.3   overlay      269.43     35.45      233.98          13.16%         /run/containerd/io.containerd.runtime.v2.task/k8s.io/f06e6d083f676ff31bd03538dc4c8b500c79cd22777f74de7f5654f6c2cfb0d6/rootfs
10.5.0.3   overlay      269.43     35.45      233.98          13.16%         /run/containerd/io.containerd.runtime.v2.task/k8s.io/5b83d4489c5f8959b8b8fc781d3ebff77040c2568355f13a0304dc02f8e3315c/rootfs
```

Next install Prometheus and Grafana

```
PS S:\Kubernetes\talos> helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
"prometheus-community" has been added to your repositories
PS S:\Kubernetes\talos> helm repo add grafana https://grafana.github.io/helm-charts
"grafana" has been added to your repositories
PS S:\Kubernetes\talos> helm repo update
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "grafana" chart repository
...Successfully got an update from the "prometheus-community" chart repository
PS S:\Kubernetes> helm install prometheus prometheus-community/prometheus -n monitoring --create-namespace --set server.persistentVolume.enabled=false --set alertmanager.enabled=false
W0401 13:48:59.882754   41572 warnings.go:70] would violate PodSecurity "restricted:latest": host namespaces (hostNetwork=true, hostPID=true), unrestricted capabilities (container "node-exporter" must set securityContext.capabilities.drop=["ALL"]), restricted volume types (volumes "proc", "sys", "root" use restricted volume type "hostPath"), seccompProfile (pod or container "node-exporter" must set securityContext.seccompProfile.type to "RuntimeDefault" or "Localhost")
W0401 13:48:59.893757   41572 warnings.go:70] would violate PodSecurity "restricted:latest": allowPrivilegeEscalation != false (container "pushgateway" must set securityContext.allowPrivilegeEscalation=false), unrestricted capabilities (container "pushgateway" must set securityContext.capabilities.drop=["ALL"]), seccompProfile (pod or container "pushgateway" must set securityContext.seccompProfile.type to "RuntimeDefault" or "Localhost")
W0401 13:48:59.896758   41572 warnings.go:70] would violate PodSecurity "restricted:latest": allowPrivilegeEscalation != false (containers "prometheus-server-configmap-reload", "prometheus-server" must set securityContext.allowPrivilegeEscalation=false), unrestricted capabilities (containers "prometheus-server-configmap-reload", "prometheus-server" must set securityContext.capabilities.drop=["ALL"]), seccompProfile (pod or containers "prometheus-server-configmap-reload", "prometheus-server" must set securityContext.seccompProfile.type to "RuntimeDefault" or "Localhost")
NAME: prometheus
LAST DEPLOYED: Mon Apr  1 13:48:58 2024
NAMESPACE: monitoring
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The Prometheus server can be accessed via port 80 on the following DNS name from within your cluster:
prometheus-server.monitoring.svc.cluster.local


Get the Prometheus server URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace monitoring -l "app.kubernetes.io/name=prometheus,app.kubernetes.io/instance=prometheus" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace monitoring port-forward $POD_NAME 9090
#################################################################################
######   WARNING: Persistence is disabled!!! You will lose your data when   #####
######            the Server pod is terminated.                             #####
#################################################################################


#################################################################################
######   WARNING: Pod Security Policy has been disabled by default since    #####
######            it deprecated after k8s 1.25+. use                        #####
######            (index .Values "prometheus-node-exporter" "rbac"          #####
###### .          "pspEnabled") with (index .Values                         #####
######            "prometheus-node-exporter" "rbac" "pspAnnotations")       #####
######            in case you still need it.                                #####
#################################################################################


The Prometheus PushGateway can be accessed via port 9091 on the following DNS name from within your cluster:
prometheus-prometheus-pushgateway.monitoring.svc.cluster.local


Get the PushGateway URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace monitoring -l "app=prometheus-pushgateway,component=pushgateway" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace monitoring port-forward $POD_NAME 9091

For more information on running Prometheus, visit:
https://prometheus.io/
PS S:\Kubernetes\talos> cd ..
PS S:\Kubernetes> helm install grafana grafana/grafana -n monitoring --create-namespace
NAME: grafana
LAST DEPLOYED: Mon Apr  1 14:01:08 2024
NAMESPACE: monitoring
STATUS: deployed
REVISION: 1
NOTES:
1. Get your 'admin' user password by running:

   kubectl get secret --namespace monitoring grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo


2. The Grafana server can be accessed via port 80 on the following DNS name from within your cluster:

   grafana.monitoring.svc.cluster.local

   Get the Grafana URL to visit by running these commands in the same shell:
     export POD_NAME=$(kubectl get pods --namespace monitoring -l "app.kubernetes.io/name=grafana,app.kubernetes.io/instance=grafana" -o jsonpath="{.items[0].metadata.name}")
     kubectl --namespace monitoring port-forward $POD_NAME 3000

3. Login with the password from step 1 and the username: admin
#################################################################################
######   WARNING: Persistence is disabled!!! You will lose your data when   #####
######            the Grafana pod is terminated.                            #####
#################################################################################
```

There are a few projects out there that provide a GUI interface for Kubernetes.
- [Lens](https://k8slens.dev)
- [OpenLens](https://github.com/MuhammedKalkan/OpenLens?tab=readme-ov-file)
- [K8 Studio](https://k8studio.io)
- [Headlamp](https://headlamp.dev)

```
PS S:\Kubernetes\talos> winget install headlamp
Found Headlamp [Headlamp.Headlamp] Version 0.23.1
This application is licensed to you by its owner.
Microsoft is not responsible for, nor does it grant any licenses to, third-party packages.
Downloading https://github.com/headlamp-k8s/headlamp/releases/download/v0.23.1/Headlamp-0.23.1-win-x64.exe
  ██████████████████████████████   107 MB /  107 MB
Successfully verified installer hash
Starting package install...
Successfully installed
```

In Headlamp, I was able to navigate to the talos cluster -> Configuration -> Secrets and select grafana to retrieve the username and password to access the dashboard. Before I could access the grafana dashboard I needed to enable port forwarding at navigate to the talos cluster -> Network -> Services and select grafana and under the Ports heading press the Forward port button under 80 > 3000. This gave me the link to http://127.0.0.1:64814 where I was able to enter the username and password I retrieved earlier and confirm Grafana was working.

Now to install [ArgoCD](https://argo-cd.readthedocs.io/en/release-2.5/getting_started)

```
PS S:\Kubernetes\talos> kubectl create namespace argocd
namespace/argocd created
PS S:\Kubernetes\talos> kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/core-install.yaml
customresourcedefinition.apiextensions.k8s.io/applications.argoproj.io created
customresourcedefinition.apiextensions.k8s.io/applicationsets.argoproj.io created
customresourcedefinition.apiextensions.k8s.io/appprojects.argoproj.io created
serviceaccount/argocd-application-controller created
serviceaccount/argocd-applicationset-controller created
serviceaccount/argocd-redis created
serviceaccount/argocd-repo-server created
role.rbac.authorization.k8s.io/argocd-application-controller created
role.rbac.authorization.k8s.io/argocd-applicationset-controller created
clusterrole.rbac.authorization.k8s.io/argocd-application-controller created
rolebinding.rbac.authorization.k8s.io/argocd-application-controller created
rolebinding.rbac.authorization.k8s.io/argocd-applicationset-controller created
clusterrolebinding.rbac.authorization.k8s.io/argocd-application-controller created
configmap/argocd-cm created
configmap/argocd-cmd-params-cm created
configmap/argocd-gpg-keys-cm created
configmap/argocd-rbac-cm created
configmap/argocd-ssh-known-hosts-cm created
configmap/argocd-tls-certs-cm created
secret/argocd-secret created
service/argocd-applicationset-controller created
service/argocd-metrics created
service/argocd-redis created
service/argocd-repo-server created
deployment.apps/argocd-applicationset-controller created
deployment.apps/argocd-redis created
deployment.apps/argocd-repo-server created
statefulset.apps/argocd-application-controller created
networkpolicy.networking.k8s.io/argocd-application-controller-network-policy created
networkpolicy.networking.k8s.io/argocd-applicationset-controller-network-policy created
networkpolicy.networking.k8s.io/argocd-redis-network-policy created
networkpolicy.networking.k8s.io/argocd-repo-server-network-policy created
```

When I went to view the dashboard I realized I selected core-install.yaml. Now, redo the install but this time with install.yaml

```
PS S:\Kubernetes\talos> kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
customresourcedefinition.apiextensions.k8s.io/applications.argoproj.io unchanged
customresourcedefinition.apiextensions.k8s.io/applicationsets.argoproj.io unchanged
customresourcedefinition.apiextensions.k8s.io/appprojects.argoproj.io unchanged
serviceaccount/argocd-application-controller unchanged
serviceaccount/argocd-applicationset-controller unchanged
serviceaccount/argocd-dex-server created
serviceaccount/argocd-notifications-controller created
serviceaccount/argocd-redis unchanged
serviceaccount/argocd-repo-server unchanged
serviceaccount/argocd-server created
role.rbac.authorization.k8s.io/argocd-application-controller unchanged
role.rbac.authorization.k8s.io/argocd-applicationset-controller unchanged
role.rbac.authorization.k8s.io/argocd-dex-server created
role.rbac.authorization.k8s.io/argocd-notifications-controller created
role.rbac.authorization.k8s.io/argocd-server created
clusterrole.rbac.authorization.k8s.io/argocd-application-controller unchanged
clusterrole.rbac.authorization.k8s.io/argocd-applicationset-controller created
clusterrole.rbac.authorization.k8s.io/argocd-server created
rolebinding.rbac.authorization.k8s.io/argocd-application-controller unchanged
rolebinding.rbac.authorization.k8s.io/argocd-applicationset-controller unchanged
rolebinding.rbac.authorization.k8s.io/argocd-dex-server created
rolebinding.rbac.authorization.k8s.io/argocd-notifications-controller created
rolebinding.rbac.authorization.k8s.io/argocd-server created
clusterrolebinding.rbac.authorization.k8s.io/argocd-application-controller unchanged
clusterrolebinding.rbac.authorization.k8s.io/argocd-applicationset-controller created
clusterrolebinding.rbac.authorization.k8s.io/argocd-server created
configmap/argocd-cm unchanged
configmap/argocd-cmd-params-cm unchanged
configmap/argocd-gpg-keys-cm unchanged
configmap/argocd-notifications-cm created
configmap/argocd-rbac-cm unchanged
configmap/argocd-ssh-known-hosts-cm unchanged
configmap/argocd-tls-certs-cm unchanged
secret/argocd-notifications-secret created
secret/argocd-secret unchanged
service/argocd-applicationset-controller unchanged
service/argocd-dex-server created
service/argocd-metrics unchanged
service/argocd-notifications-controller-metrics created
service/argocd-redis unchanged
service/argocd-repo-server unchanged
service/argocd-server created
service/argocd-server-metrics created
deployment.apps/argocd-applicationset-controller unchanged
deployment.apps/argocd-dex-server created
deployment.apps/argocd-notifications-controller created
deployment.apps/argocd-redis unchanged
deployment.apps/argocd-repo-server unchanged
deployment.apps/argocd-server created
statefulset.apps/argocd-application-controller unchanged
networkpolicy.networking.k8s.io/argocd-application-controller-network-policy configured
networkpolicy.networking.k8s.io/argocd-applicationset-controller-network-policy unchanged
networkpolicy.networking.k8s.io/argocd-dex-server-network-policy created
networkpolicy.networking.k8s.io/argocd-notifications-controller-network-policy created
networkpolicy.networking.k8s.io/argocd-redis-network-policy unchanged
networkpolicy.networking.k8s.io/argocd-repo-server-network-policy configured
networkpolicy.networking.k8s.io/argocd-server-network-policy created
```

Now make it a load balancer

```
PS S:\Kubernetes\talos> kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
service/argocd-server patched
```

In Headlamp, I was able to navigate to the talos cluster -> Configuration -> Secrets and select argocd-initial-admin-secret to retrieve the password to access the dashboard for the admin user. Before I could access the ArgoCD dashboard I needed to enable port forwarding at navigate to the talos cluster -> Network -> Services and select argocd-server and under the Ports heading press the Forward port button under 80 > 8080. This gave me the link to [http://127.0.0.1:2823](http://127.0.0.1:2823) where I was able to enter the username and password I retrieved earlier and confirm ArgoCD was working.

In Headlamp I started to Events appearing about MountVolume. I figure it was because I don't have any storage classes setup yet. So, next up was to create the NFS storage on the NAS which is on the network.

```
PS S:\Kubernetes\talos> helm repo update nfs-subdir-external-provisioner
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "nfs-subdir-external-provisioner" chart repository
Update Complete. ⎈Happy Helming!⎈
PS S:\Kubernetes\talos> helm install nfs-subdir-external-provisioner nfs-subdir-external-provisioner/nfs-subdir-external-provisioner --create-namespace -n storage --set nfs.server=192.168.0.116 --set nfs.path=/volume1/storage-class
W0403 11:53:26.468761   31720 warnings.go:70] would violate PodSecurity "restricted:latest": allowPrivilegeEscalation != false (container "nfs-subdir-external-provisioner" must set securityContext.allowPrivilegeEscalation=false), unrestricted capabilities (container "nfs-subdir-external-provisioner" must set securityContext.capabilities.drop=["ALL"]), restricted volume types (volume "nfs-subdir-external-provisioner-root" uses restricted volume type "nfs"), runAsNonRoot != true (pod or container "nfs-subdir-external-provisioner" must set securityContext.runAsNonRoot=true), seccompProfile (pod or container "nfs-subdir-external-provisioner" must set securityContext.seccompProfile.type to "RuntimeDefault" or "Localhost")
NAME: nfs-subdir-external-provisioner
LAST DEPLOYED: Wed Apr  3 11:53:26 2024
NAMESPACE: storage
STATUS: deployed
REVISION: 1
TEST SUITE: None
PS S:\Kubernetes\talos> notepad.exe storage-class-nfs-storage.yml
PS S:\Kubernetes\talos> kubectl apply -f storage-class-nfs-storage.yml
namespace/storage-nfs created
storageclass.storage.k8s.io/nfs-storage created
PS S:\Kubernetes\talos> kubectl patch storageclass nfs-storage -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
storageclass.storage.k8s.io/nfs-storage patched
```

The warning about violating PodSecurity "restricted:latest" led me to read up more on Security Context and start looking closer at the nfs-subdir-external-provisioner on Github for SecurityContext. After reading the issues and messages for nfs-subdir-external-provisioner I saw that the project hasn't had very much developer activity. Another project, [csi-driver-nfs](https://github.com/kubernetes-csi/csi-driver-nfs), has getting more attention. So, I decided to follow the article [Installing the NFS CSI Driver on a Kubernetes cluster to allow for dynamic provisioning of Persistent Volumes](https://rudimartinsen.com/2024/01/09/nfs-csi-driver-kubernetes/) and try out that CSI project too. Looking closer you'll see that the csi-driver-nfs is placed in the namespace kube-system which is privledged.

```
PS S:\Kubernetes\talos> helm repo add csi-driver-nfs https://raw.githubusercontent.com/kubernetes-csi/csi-driver-nfs/master/charts
"csi-driver-nfs" has been added to your repositories
PS S:\Kubernetes\talos> helm repo update csi-driver-nfs
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "csi-driver-nfs" chart repository
Update Complete. ⎈Happy Helming!⎈
PS S:\Kubernetes\talos> helm install csi-driver-nfs csi-driver-nfs/csi-driver-nfs --namespace kube-system --version v4.6.0
NAME: csi-driver-nfs
LAST DEPLOYED: Wed Apr  3 13:57:13 2024
NAMESPACE: kube-system
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The CSI NFS Driver is getting deployed to your cluster.

To check CSI NFS Driver pods status, please run:

  kubectl --namespace=kube-system get pods --selector="app.kubernetes.io/instance=csi-driver-nfs" --watch
PS S:\Kubernetes\talos>  kubectl --namespace=kube-system get pods --selector="app.kubernetes.io/instance=csi-driver-nfs" --watch
NAME                                  READY   STATUS    RESTARTS   AGE
csi-nfs-controller-76dcdb7f75-qwm8r   4/4     Running   0          23s
csi-nfs-node-68dm5                    3/3     Running   0          23s
csi-nfs-node-6m5l7                    3/3     Running   0          23s
csi-nfs-node-79gbk                    3/3     Running   0          23s
csi-nfs-node-plx9c                    3/3     Running   0          23s
PS S:\Kubernetes\talos> notepad.exe .\storage-class-nfs-storage.yml
PS S:\Kubernetes\talos> kubectl apply -f .\storage-class-nfs-csi.yml
storageclass.storage.k8s.io/nfs-csi created
PS S:\Kubernetes\talos> kubectl get storageclasses
NAME                PROVISIONER                                     RECLAIMPOLICY   VOLUMEBINDINGMODE   ALLOWVOLUMEEXPANSION   AGE
nfs-client          cluster.local/nfs-subdir-external-provisioner   Delete          Immediate           true                   24h
nfs-csi (default)   nfs.csi.k8s.io                                  Retain          Immediate           false                  84s
nfs-storage         cluster.local/nfs-subdir-external-provisioner   Retain          Immediate           false                  24h
PS S:\Kubernetes\talos> kubectl describe storageclasses nfs-csi
Name:            nfs-csi
IsDefaultClass:  Yes
Annotations:     kubectl.kubernetes.io/last-applied-configuration={"apiVersion":"storage.k8s.io/v1","kind":"StorageClass","metadata":{"annotations":{"storageclass.kubernetes.io/is-default-class":"true"},"name":"nfs-csi"},"mountOptions":["nfsvers=4.1"],"parameters":{"server":"192.168.0.116","share":"/volume1/storage-class"},"provisioner":"nfs.csi.k8s.io","reclaimPolicy":"Retain","volumeBindingMode":"Immediate"}
,storageclass.kubernetes.io/is-default-class=true
Provisioner:           nfs.csi.k8s.io
Parameters:            server=192.168.0.116,share=/volume1/storage-class
AllowVolumeExpansion:  <unset>
MountOptions:
  nfsvers=4.1
ReclaimPolicy:      Retain
VolumeBindingMode:  Immediate
Events:             <none>
PS S:\Kubernetes\talos> notepad.exe .\pvc-nfs-csi-dynamic.yml
PS S:\Kubernetes\talos> kubectl apply -f .\pvc-nfs-csi-dynamic.yml
persistentvolumeclaim/pvc-nfs-csi-dynamic created
PS S:\Kubernetes\talos> kubectl get persistentvolumeclaims
NAME                  STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   VOLUMEATTRIBUTESCLASS   AGE
pvc-nfs-csi-dynamic   Bound    pvc-9d09aee9-98f8-481e-af4b-b6fb369166a8   2Gi        RWX            nfs-csi        <unset>                 20s
PS S:\Kubernetes\talos> kubectl describe pvc pvc-nfs-csi-dynamic
Name:          pvc-nfs-csi-dynamic
Namespace:     default
StorageClass:  nfs-csi
Status:        Bound
Volume:        pvc-9d09aee9-98f8-481e-af4b-b6fb369166a8
Labels:        <none>
Annotations:   pv.kubernetes.io/bind-completed: yes
               pv.kubernetes.io/bound-by-controller: yes
               volume.beta.kubernetes.io/storage-provisioner: nfs.csi.k8s.io
               volume.kubernetes.io/storage-provisioner: nfs.csi.k8s.io
Finalizers:    [kubernetes.io/pvc-protection]
Capacity:      2Gi
Access Modes:  RWX
VolumeMode:    Filesystem
Used By:       <none>
Events:
  Type    Reason                 Age   From                                                                              Message
  ----    ------                 ----  ----                                                                              -------
  Normal  ExternalProvisioning   36s   persistentvolume-controller                                                       Waiting for a volume to be created either by the external provisioner 'nfs.csi.k8s.io' or manually by the system administrator. If volume creation is delayed, please verify that the provisioner is running and correctly registered.
  Normal  Provisioning           36s   nfs.csi.k8s.io_talos-default-controlplane-1_736fff9c-023d-4781-8a7e-b7040d881c55  External provisioner is provisioning volume for claim "default/pvc-nfs-csi-dynamic"
  Normal  ProvisioningSucceeded  36s   nfs.csi.k8s.io_talos-default-controlplane-1_736fff9c-023d-4781-8a7e-b7040d881c55  Successfully provisioned volume pvc-9d09aee9-98f8-481e-af4b-b6fb369166a8
```

At this point everything looked to be good. I had the Storage Class show up, I created a persistent volume claim and the persistent volume was created. I accessed the NFS server and found the newly created folder. Next up is to create a pod and have it write the the PV.

```
PS S:\Kubernetes\talos> notepad.exe .\nginx-pvc.yaml
PS S:\Kubernetes\talos> kubectl apply -f nginx-pvc.yaml
Warning: would violate PodSecurity "restricted:latest": allowPrivilegeEscalation != false (container "nginx-nfs" must set securityContext.allowPrivilegeEscalation=false), unrestricted capabilities (container "nginx-nfs" must set securityContext.capabilities.drop=["ALL"]), runAsNonRoot != true (pod or container "nginx-nfs" must set securityContext.runAsNonRoot=true), seccompProfile (pod or container "nginx-nfs" must set securityContext.seccompProfile.type to "RuntimeDefault" or "Localhost")
pod/nginx-nfs created
PS S:\Kubernetes\talos> kubectl get pod
NAME        READY   STATUS              RESTARTS   AGE
nginx-nfs   0/1     ContainerCreating   0          0s
```

Well that's not what I expected! But wait, the message is just a warning. The pod was created and the pod was able to write create and write to the file. I then deleted the pod and added securityContext settings. 

```
      securityContext:
        runAsNonRoot: true
        allowPrivilegeEscalation: false
        capabilities:
          drop:
            - ALL
```

Which initially looked good at the CLI but resulted in an event 'container has runAsNonRoot and image will run as root (pod: "nginx-nfs_default(b302af89-e100-4d92-8500-516fb42cdbde)", container: nginx-nfs)'

```
PS S:\Kubernetes\talos> kubectl apply -f nginx-pvc.yaml
pod/nginx-nfs created
```

After searching for a bit I figured out that the image being used for the pod required root privledges! With a bit of a cheat I asked [Pi AI](https://pi.ai/talk) 'Is there an nginx image which can runAsNonRoot?'. The response was 'Yes, there are Nginx images that can run as a non-root user. One example is the nginxinc/nginx-unprivileged image, which is officially maintained by Nginx. This image is specifically designed to run as a non-root user, making it a more secure option for running Nginx in a container.'. I modified the yaml file with this image, applied it and voila it worked!

Earlier when I had installed Prometheus and Grafana I didn't pay attention to the all the warnings in the messages. 

```
W0401 13:48:59.882754   41572 warnings.go:70] would violate PodSecurity "restricted:latest": host namespaces (hostNetwork=true, hostPID=true), unrestricted capabilities (container "node-exporter" must set securityContext.capabilities.drop=["ALL"]), restricted volume types (volumes "proc", "sys", "root" use restricted volume type "hostPath"), seccompProfile (pod or container "node-exporter" must set securityContext.seccompProfile.type to "RuntimeDefault" or "Localhost")
W0401 13:48:59.893757   41572 warnings.go:70] would violate PodSecurity "restricted:latest": allowPrivilegeEscalation != false (container "pushgateway" must set securityContext.allowPrivilegeEscalation=false), unrestricted capabilities (container "pushgateway" must set securityContext.capabilities.drop=["ALL"]), seccompProfile (pod or container "pushgateway" must set securityContext.seccompProfile.type to "RuntimeDefault" or "Localhost")
W0401 13:48:59.896758   41572 warnings.go:70] would violate PodSecurity "restricted:latest": allowPrivilegeEscalation != false (containers "prometheus-server-configmap-reload", "prometheus-server" must set securityContext.allowPrivilegeEscalation=false), unrestricted capabilities (containers "prometheus-server-configmap-reload", "prometheus-server" must set securityContext.capabilities.drop=["ALL"]), seccompProfile (pod or containers "prometheus-server-configmap-reload", "prometheus-server" must set securityContext.seccompProfile.type to "RuntimeDefault" or "Localhost")
```

The problem I was having with the NFS CSI led me to an answer for the warnings with Prometheus. The documentation on [Talos Pod Security](https://www.talos.dev/v1.6/kubernetes-guides/configuration/pod-security/) provided the answer. 'Some applications (e.g. Prometheus node exporter or storage solutions) require more relaxed Pod Security Standards, which can be configured by either updating the Pod Security Admission plugin configuration, or by using the pod-security.kubernetes.io/enforce label on the namespace level:'. So, I applied the labeling. In hindsight I should have check the Talos site sooner. I may remove the csi-driver-nfs from kube-system and place it in a separate namespace as I've done previously with the nfs-subdir-external-provisioner.

```
PS S:\Kubernetes\talos> kubectl get ds -n monitoring
NAME                                  DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR            AGE
prometheus-prometheus-node-exporter   4         0         0       0            0           kubernetes.io/os=linux   3d22h
PS S:\Kubernetes\talos> kubectl label namespace monitoring pod-security.kubernetes.io/enforce=privileged
namespace/monitoring labeled
PS S:\Kubernetes\talos> kubectl get ds -n monitoring
NAME                                  DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR            AGE
prometheus-prometheus-node-exporter   4         4         4       4            4           kubernetes.io/os=linux   3d22h
```
