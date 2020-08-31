---
title: Connettersi e gestire Microsoft Azure Stack dispositivo perimetrale tramite l'interfaccia di Windows PowerShell | Microsoft Docs
description: Viene descritto come connettersi e gestire Azure Stack Edge tramite l'interfaccia di Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 26d4476217d5a6f6abea6093f453cd52d654911e
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89086155"
---
# <a name="manage-an-azure-stack-edge-gpu-device-via-windows-powershell"></a>Gestire un dispositivo GPU Azure Stack Edge tramite Windows PowerShell

Azure Stack soluzione Edge consente di elaborare i dati e inviarli tramite la rete ad Azure. Questo articolo descrive alcune delle attività di configurazione e gestione per il dispositivo Azure Stack Edge. È possibile usare la portale di Azure, l'interfaccia utente Web locale o l'interfaccia di Windows PowerShell per gestire il dispositivo.

Questo articolo è incentrato sulla modalità di connessione all'interfaccia di PowerShell del dispositivo e sulle attività che è possibile eseguire tramite questa interfaccia. 


## <a name="connect-to-the-powershell-interface"></a>Connettersi all'interfaccia di PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Creare un pacchetto di supporto

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Caricamento del certificato

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

È anche possibile caricare IoT Edge certificati per abilitare una connessione sicura tra il dispositivo IoT Edge e i dispositivi downstream che possono connettersi ad esso. Sono disponibili tre certificati IoT Edge (formato*PEM* ) che è necessario installare:

- Certificato CA radice o CA proprietario
- Certificato CA del dispositivo
- Certificato chiave del dispositivo

Nell'esempio seguente viene illustrato l'utilizzo di questo cmdlet per installare i certificati IoT Edge:

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username"
```
Quando si esegue questo cmdlet, verrà richiesto di specificare la password per la condivisione di rete.

Per altre informazioni sui certificati, vedere [Azure IOT Edge Certificates](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) o [Install certificates on a gateway](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway).

## <a name="view-device-information"></a>Visualizza informazioni sul dispositivo
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="view-gpu-driver-information"></a>Visualizza informazioni sul driver GPU

Se il ruolo di calcolo è configurato nel dispositivo, è anche possibile ottenere le informazioni sul driver GPU tramite l'interfaccia di PowerShell. 

1. [Connettersi all'interfaccia di PowerShell](#connect-to-the-powershell-interface).
2. Usare `Get-HcsGpuNvidiaSmi` per ottenere le informazioni sul driver GPU per il dispositivo.

    Nell'esempio seguente viene illustrato l'utilizzo di questo cmdlet:

    ```powershell
    Get-HcsGpuNvidiaSmi
    ```
    Prendere nota delle informazioni sul driver nell'output di esempio di questo cmdlet.

    ```powershell    
    +-----------------------------------------------------------------------------+    
    | NVIDIA-SMI 440.64.00    Driver Version: 440.64.00    CUDA Version: 10.2     |    
    |-------------------------------+----------------------+----------------------+    
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |    
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |    
    |===============================+======================+======================|    
    |   0  Tesla T4            On   | 000029CE:00:00.0 Off |                    0 |    
    | N/A   60C    P0    29W /  70W |   1539MiB / 15109MiB |      0%      Default |    
    +-------------------------------+----------------------+----------------------+    
    |   1  Tesla T4           On  | 0000AD50:00:00.0 Off |                    0 |
    | N/A   58C    P0    29W /  70W |    330MiB / 15109MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    ```

## <a name="enable-multi-process-service-mps"></a>Abilita servizio a più processi (MP)

Un servizio multi-process (MP) su GPU NVIDIA fornisce un meccanismo in cui le GPU possono essere condivise da più processi, in cui a ogni processo viene allocata una percentuale delle risorse della GPU. Per abilitare MP nel dispositivo Azure Stack Edge, seguire questa procedura:

1. Prima di iniziare, verificare che: 

    1. [Il dispositivo Azure stack Edge](azure-stack-edge-gpu-deploy-activate.md) è stato configurato e attivato con una risorsa Azure stack edge/Data Box gateway in Azure.
    1. [Il calcolo è stato configurato in questo dispositivo nel portale di Azure](azure-stack-edge-deploy-configure-compute.md#configure-compute).
    
1. [Connettersi all'interfaccia di PowerShell](#connect-to-the-powershell-interface).
1. Usare il comando seguente per abilitare i Management Pack nel dispositivo.

    ```powershell
    Start-HcsGpuMPS
    ```

## <a name="reset-your-device"></a>Reset your device (Reimpostare il dispositivo)

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Ottenere i log di calcolo

Se il ruolo di calcolo è configurato nel dispositivo, è anche possibile ottenere i log di calcolo tramite l'interfaccia di PowerShell.

1. [Connettersi all'interfaccia di PowerShell](#connect-to-the-powershell-interface).
2. Usare `Get-AzureDataBoxEdgeComputeRoleLogs` per ottenere i log di calcolo per il dispositivo.

    Nell'esempio seguente viene illustrato l'utilizzo di questo cmdlet:

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection    
    ```

    Ecco una descrizione dei parametri usati per il cmdlet:
    - `Path`: Specificare un percorso di rete per la condivisione in cui si vuole creare il pacchetto di log di calcolo.
    - `Credential`: Specificare il nome utente per la condivisione di rete. Quando si esegue questo cmdlet, sarà necessario specificare la password di condivisione.
    - `FullLogCollection`: Questo parametro garantisce che il pacchetto di log conterrà tutti i log di calcolo. Per impostazione predefinita, il pacchetto di log contiene solo un subset di log.



## <a name="debug-kubernetes-issues-related-to-iot-edge"></a>Debug dei problemi di Kubernetes correlati all'IoT Edge

Quando viene creato il cluster Kubernetes, `aseuser` viene creato anche un utente predefinito associato a uno spazio dei nomi System `iotedge` . Per eseguire il debug di eventuali problemi correlati alla IoT Edge, è possibile usare questo utente e lo spazio dei nomi di sistema.  

### <a name="create-config-file-for-system-namespace"></a>Crea file di configurazione per lo spazio dei nomi System

Per risolvere il problemi, creare innanzitutto il `config` file corrispondente allo `iotedge` spazio dei nomi con `aseuser` .

Eseguire il `Get-HcsKubernetesUserConfig -AseUser` comando e salvare l'output come `config` file (nessuna estensione di file). Salvare il file nella `.kube` cartella del profilo utente nel computer locale.

Di seguito è riportato l'output di esempio del `Get-HcsKubernetesUserConfig` comando.

```PowerShell
[10.100.10.10]: PS>Get-HcsKubernetesUserConfig -AseUser
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01EVXhNekl4TkRRME5sb1hEVE13TURVeE1USXhORFEwTmxvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBS0M1CjlJbzRSU2hudG90QUdxdjNTYmRjOVd4UmJDYlRzWXU5S0RQeU9xanVoZE1UUE9PcmROOGNoa0x4NEFyZkZaU1AKZithUmhpdWZqSE56bWhucnkvZlprRGdqQzQzRmV5UHZzcTZXeVVDV0FEK2JBdi9wSkJDbkg2MldoWGNLZ1BVMApqU1k0ZkpXenNFbzBaREhoeUszSGN3MkxkbmdmaEpEanBQRFJBNkRWb2pIaktPb29OT1J1dURvUHpiOTg2dGhUCkZaQXJMZjRvZXRzTEk1ZzFYRTNzZzM1YVhyU0g3N2JPYVVsTGpYTzFYSnpFZlZWZ3BMWE5xR1ZqTXhBMVU2b1MKMXVJL0d1K1ArY
===========CUT=========================================CUT===================
    server: https://compute.myasegpu1.wdshcsso.com:6443
    name: kubernetes
contexts:
- context:
    cluster: kubernetes
    user: aseuser
    name: aseuser@kubernetes
current-context: aseuser@kubernetes
kind: Config
preferences: {}
users:
- name: aseuser
    user:
    client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwRENDQWJpZ0F3SUJBZ0lJY1hOTXRPU2VwbG93RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURBMU1UTXlNVFEwTkRaYUZ3MHlNVEExTVRNeU1UVXhNVEphTUJJeApFREFPQmdOVkJBTVRCMkZ6WlhWelpYSXdnZ0VpTUEwR0NTcUdTSWIzRFFFQkFRVUFBNElCRHdBd2dnRUtBb0lCCkFRRHVjQ1pKdm9qNFIrc0U3a1EyYmVjNEJkTXdpUEhmU2R2WnNDVVY0aTRRZGY1Yzd0dkE3OVRSZkRLQTY1d08Kd0h0QWdlK3lLK0hIQ1Qyd09RbWtNek1RNjZwVFEzUlE0eVdtRDZHR1cWZWMExBR1hFUUxWWHRuTUdGCi0tLS0tRU5EIFJTQSBQUklWQVRFIEtFWS0tLS0tCg==

[10.100.10.10]: PS>
```

In un dispositivo Azure Stack Edge in cui è configurato il ruolo di calcolo, tutti i `kubectl` comandi sono disponibili per monitorare o risolvere i problemi relativi ai moduli. Per visualizzare un elenco dei comandi disponibili, eseguire `kubectl --help` dalla finestra di comando.

```PowerShell
C:\Users\myuser>kubectl --help
kubectl controls the Kubernetes cluster manager.

Find more information at: https://kubernetes.io/docs/reference/kubectl/overview/

Basic Commands (Beginner):
    create         Create a resource from a file or from stdin.
    expose         Take a replication controller, service, deployment or pod and expose it as a new Kubernetes Service
    run            Run a particular image on the cluster
    set            Set specific features on objects
    run-container  Run a particular image on the cluster. This command is deprecated, use "run" instead
==============CUT=============CUT============CUT========================

Usage:
    kubectl [flags] [options]

Use "kubectl <command> --help" for more information about a given command.
Use "kubectl options" for a list of global command-line options (applies to all commands).

C:\Users\myuser>
```

Per un elenco completo dei `kubectl` comandi, passare a [ `kubectl` foglio informativo](https://kubernetes.io/docs/reference/kubectl/cheatsheet/). 

### <a name="to-get-ip-of-service-or-module-exposed-outside-of-kubernetes-cluster"></a>Per ottenere l'indirizzo IP del servizio o del modulo esposto all'esterno del cluster Kubernetes

Per ottenere l'indirizzo IP di un servizio di bilanciamento del carico o di moduli esposti all'esterno di Kubernetes, eseguire il comando seguente:

`kubectl get svc -n iotedge`

Di seguito è riportato un esempio di output di tutti i servizi o i moduli esposti all'esterno del cluster Kubernetes. 


```powershell
C:\Users\user>kubectl get svc -n iotedge
NAME           TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)                                       AGE
edgehub        LoadBalancer   10.103.52.225   10.128.44.243   443:31987/TCP,5671:32336/TCP,8883:30618/TCP   34h
iotedged       ClusterIP      10.107.236.20   <none>          35000/TCP,35001/TCP                           3d8h
webserverapp   LoadBalancer   10.105.186.35   10.128.44.244   8080:30976/TCP                                16h

C:\Users\user>
```
L'indirizzo IP nella colonna IP esterno corrisponde all'endpoint esterno per il servizio o il modulo. È anche possibile [ottenere l'indirizzo IP esterno nel dashboard di Kubernetes](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#get-ip-address-for-services-or-modules).


### <a name="to-check-if-module-deployed-successfully"></a>Per verificare se il modulo è stato distribuito correttamente

I moduli di calcolo sono contenitori per i quali è implementata una logica di business. Un pod Kubernetes può avere più contenitori in esecuzione. Per verificare se un modulo di calcolo è stato distribuito correttamente, eseguire il `get pods` comando e verificare se il contenitore, corrispondente al modulo di calcolo, è in esecuzione.

Per ottenere l'elenco di tutti i pod in esecuzione in uno spazio dei nomi specifico, eseguire il comando seguente:

`get pods -n <namespace>`

Di seguito è riportato un output di esempio di tutti i pod in esecuzione nello `iotedge` spazio dei nomi.

```
C:\Users\myuser>kubectl get pods -n iotedge
NAME                        READY   STATUS    RESTARTS   AGE
edgeagent-cf6d4ffd4-q5l2k   2/2     Running   0          20h
edgehub-8c9dc8788-2mvwv     2/2     Running   0          56m
filemove-66c49984b7-h8lxc   2/2     Running   0          56m
iotedged-675d7f4b5f-9nml4   1/1     Running   0          20h

C:\Users\myuser>
```

Lo **stato** indica che tutti i pod nello spazio dei nomi sono in esecuzione e che il **pronto** indica il numero di contenitori distribuiti in un pod. Nell'esempio precedente, tutti i pod sono in esecuzione e tutti i moduli distribuiti in ogni pod sono in esecuzione. 

Per un output più dettagliato di un pod specifico per un determinato spazio dei nomi, è possibile eseguire il comando seguente:

`kubectl describe pod <pod name> -n <namespace>` 

L'output di esempio è illustrato di seguito.

```
C:\Users\myuser>kubectl describe pod filemove-66c49984b7 -n iotedge
Name:           filemove-66c49984b7-h8lxc
Namespace:      iotedge
Priority:       0
Node:           k8s-1hwf613cl-1hwf613/10.139.218.12
Start Time:     Thu, 14 May 2020 12:46:28 -0700
Labels:         net.azure-devices.edge.deviceid=myasegpu-edge
                net.azure-devices.edge.hub=myasegpu2iothub.azure-devices.net
                net.azure-devices.edge.module=filemove
                pod-template-hash=66c49984b7
Annotations:    net.azure-devices.edge.original-moduleid: filemove
Status:         Running
IP:             172.17.75.81
IPs:            <none>
Controlled By:  ReplicaSet/filemove-66c49984b7
Containers:
    proxy:
    Container ID:   docker://fd7975ca78209a633a1f314631042a0892a833b7e942db2e7708b41f03e8daaf
    Image:          azureiotedge/azureiotedge-proxy:0.1.0-beta8
    Image ID:       docker://sha256:5efbf6238f13d24bab9a2b499e5e05bc0c33ab1587d6cf6f289cdbe7aa667563
    Port:           <none>
    Host Port:      <none>
    State:          Running
        Started:      Thu, 14 May 2020 12:46:30 -0700
    Ready:          True
    Restart Count:  0
    Environment:
        PROXY_LOG:  Debug
=============CUT===============================CUT===========================
Volumes:
    config-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      iotedged-proxy-config
    Optional:  false
    trust-bundle-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      iotedged-proxy-trust-bundle
    Optional:  false
    myasesmb1local:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  myasesmb1local
    ReadOnly:   false
    myasesmb1:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  myasesmb1
    ReadOnly:   false
    filemove-token-pzvw8:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  filemove-token-pzvw8
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                    node.kubernetes.io/unreachable:NoExecute for 300s
Events:          <none>


C:\Users\myuser>
```

### <a name="to-get-container-logs"></a>Per ottenere i log del contenitore

Per ottenere i log per un modulo, eseguire il comando seguente:

`kubectl logs <pod_name> -n <namespace> --all-containers` 

Poiché il `all-containers` flag eseguirà il dump di tutti i log per tutti i contenitori, un modo efficace per visualizzare gli errori recenti consiste nell'usare l'opzione `--tail 10` .

Di seguito è riportato un esempio di output. 

```
C:\Users\myuser>kubectl logs filemove-66c49984b7-h8lxc -n iotedge --all-containers --tail 10
DEBUG 2020-05-14T20:40:42Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 1 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:12Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 1 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
05/14/2020 19:46:44: Info: Opening module client connection.
05/14/2020 19:46:45: Info: Open done.
05/14/2020 19:46:45: Info: Initializing with input: /home/input, output: /home/output, protocol: Amqp.
05/14/2020 19:46:45: Info: IoT Hub module client initialized.

C:\Users\myuser>
```

## <a name="exit-the-remote-session"></a>Uscire dalla sessione remota

Per uscire dalla sessione remota di PowerShell, chiudere la finestra di PowerShell.

## <a name="next-steps"></a>Passaggi successivi

- Distribuire [Azure stack Edge](azure-stack-edge-gpu-deploy-prep.md) nel portale di Azure.
