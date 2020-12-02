---
title: Abilitare un registro contenitori perimetrale sul dispositivo GPU Azure Stack Edge Pro
description: Viene descritto come abilitare un registro contenitori perimetrali locale nel dispositivo GPU Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 11/11/2020
ms.author: alkohli
ms.openlocfilehash: bccb6fa33007082737997c7282fb286c38e3bbd7
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466465"
---
# <a name="enable-edge-container-registry-on-your-azure-stack-edge-pro-gpu-device"></a>Abilitare il registro contenitori perimetrale sul dispositivo GPU Azure Stack Edge Pro

Questo articolo descrive come abilitare il registro contenitori perimetrale e usarlo dall'interno del cluster Kubernetes nel dispositivo Azure Stack Edge Pro. L'esempio usato nell'articolo descrive come eseguire il push di un'immagine da un registro di sistema di origine, in questo caso Microsoft container Registry, al registro di sistema nel dispositivo Azure Stack Edge, il registro contenitori perimetrale.

### <a name="about-edge-container-registry"></a>Informazioni sul registro contenitori Edge

Le applicazioni di calcolo in contenitori vengono eseguite sulle immagini del contenitore e queste immagini vengono archiviate in registri. I registri possono essere pubblici, ad esempio hub Docker, privato o provider cloud gestito, ad esempio Azure Container Registry. Per ulteriori informazioni, vedere [informazioni sui registri, i repository e le immagini](../container-registry/container-registry-concepts.md).

Un registro contenitori perimetrale fornisce un repository al perimetro, sul dispositivo Azure Stack Edge Pro. È possibile usare questo registro per archiviare e gestire le immagini del contenitore privato.

In un ambiente a più nodi, le immagini del contenitore possono essere scaricate e inviate al registro contenitori perimetrale una sola volta. Tutte le applicazioni Edge possono usare il registro contenitori perimetrale per le distribuzioni successive.


## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare che:

1. È possibile accedere a un dispositivo Azure Stack Edge Pro.

1. Aver attivato il dispositivo Azure Stack Edge Pro come descritto in [Attivare il dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md).

1. Il ruolo di calcolo è stato abilitato nel dispositivo. Un cluster Kubernetes è stato creato anche nel dispositivo quando è stato configurato il calcolo nel dispositivo in base alle istruzioni riportate in [configurare il calcolo nel dispositivo Azure stack Edge Pro](azure-stack-edge-gpu-deploy-configure-compute.md).

1. L'endpoint dell'API Kubernetes è presente nella pagina **dispositivo** dell'interfaccia utente Web locale. Per altre informazioni, vedere le istruzioni in [ottenere l'endpoint dell'API Kubernetes](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints).

1. È possibile accedere a un sistema client con un [sistema operativo supportato](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device). Se si usa un client Windows, il sistema deve eseguire PowerShell 5,0 o versione successiva per accedere al dispositivo.

    1. Se si vuole eseguire il pull e il push delle immagini del contenitore, verificare che nel sistema sia installato il client docker. Se si usa un client Windows, [installare Docker desktop in Windows](https://docs.docker.com/docker-for-windows/install/).  


## <a name="enable-container-registry-as-add-on"></a>Abilita il registro contenitori come componente aggiuntivo

Il primo passaggio consiste nell'abilitare il registro contenitori perimetrale come componente aggiuntivo.

1. [Connettersi all'interfaccia di PowerShell del dispositivo](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface). 

1. Per abilitare il registro contenitori come componente aggiuntivo, digitare: 

    `Set-HcsKubernetesContainerRegistry`
    
    Il completamento dell'operazione può richiedere alcuni minuti.

    Ecco l'output di esempio di questo comando:  
            
    ```powershell
    [10.128.44.40]: PS>Set-HcsKubernetesContainerRegistry
    Operation completed successfully. Use Get-HcsKubernetesContainerRegistryInfo for credentials    
    ```
            
1. Per ottenere i dettagli del registro contenitori, digitare:

    `Get-HcsKubernetesContainerRegistryInfo`

    Di seguito è riportato l'esempio di questo comando:  
    
    ```powershell
    [10.128.44.40]: PS> Get-HcsKubernetesContainerRegistryInfo
                
    Endpoint                                   IPAddress    Username     Password
    --------                                   ---------    --------     --------
    ecr.dbe-hw6h1t2.microsoftdatabox.com:31001 10.128.44.41 ase-ecr-user i3eTsU4zGYyIgxV
    ``` 

1. Prendere nota del nome utente e della password dall'output di `Get-HcsKubernetesContainerRegistryInfo` . Queste credenziali vengono usate per accedere al registro contenitori perimetrale durante il push di immagini.         


## <a name="manage-container-registry-images"></a>Gestire le immagini del registro contenitori

Potrebbe essere necessario accedere al registro contenitori dall'esterno del dispositivo Azure Stack Edge. È anche possibile eseguire il push o il pull delle immagini nel registro di sistema.

Seguire questa procedura per accedere al registro contenitori perimetrale:

1. Ottenere i dettagli dell'endpoint per il registro contenitori perimetrale.
    1. Nell'interfaccia utente locale del dispositivo andare al **dispositivo**.
    1. Individuare l' **endpoint del registro contenitori Edge**.
        ![Endpoint del registro contenitori perimetrale nella pagina del dispositivo](media/azure-stack-edge-gpu-edge-container-registry/get-edge-container-registry-endpoint-1.png) 
    1. Copiare questo endpoint e creare una voce DNS corrispondente nel `C:\Windows\System32\Drivers\etc\hosts` file del client per connettersi all'endpoint del registro contenitori Edge. 

        <IP address of the Kubernetes main node>    <Edge container registry endpoint> 
        
        ![Aggiungere una voce DNS per l'endpoint del registro contenitori Edge](media/azure-stack-edge-gpu-edge-container-registry/add-domain-name-service-entry-hosts-1.png)    

1. Scaricare il certificato del registro contenitori perimetrale dall'interfaccia utente locale. 
    1. Nell'interfaccia utente locale del dispositivo passare a **certificati**.
    1. Individuare la voce per il **certificato del registro contenitori Edge**. A destra di questa voce, selezionare il **download** per scaricare il certificato del registro contenitori perimetrale sul sistema client che verrà usato per accedere al dispositivo. 

        ![Scaricare il certificato dell'endpoint del registro contenitori Edge](media/azure-stack-edge-gpu-edge-container-registry/download-edge-container-registry-endpoint-certificate-1.png)  

1. Installare il certificato scaricato nel client. Se si usa un client Windows, attenersi alla procedura seguente: 
    1. Selezionare il certificato e nell' **importazione guidata certificati** selezionare Archivia percorso come **computer locale**. 

        ![Installare il certificato 1](media/azure-stack-edge-gpu-edge-container-registry/install-certificate-1.png) 
    
    1. Installare il certificato nel computer locale nell'archivio radice attendibile. 

        ![Installare il certificato 2](media/azure-stack-edge-gpu-edge-container-registry/install-certificate-2.png) 

1. Dopo l'installazione del certificato, riavviare il client Docker nel sistema.

1. Accedere al registro contenitori perimetrale. Digitare:

    `docker login <Edge container registry endpoint> -u <username> -p <password>`

    Fornire l'endpoint del registro contenitori perimetrale dalla pagina **dispositivi** e il nome utente e la password ottenuti dall'output di `Get-HcsKubernetesContainerRegistryInfo` . 

1. Usare i comandi push o pull di Docker per eseguire il push o il pull delle immagini del contenitore dal registro contenitori.
 
    1. Effettuare il pull di un'immagine dall'immagine di Microsoft Container Registry. Digitare:
        
        `docker pull <Full path to the container image in the Microsoft Container Registry>`
       
    1. Creare un alias dell'immagine di cui è stato effettuato il pull con il percorso completo del registro.

        `docker tag <Path to the image in the Microsoft container registry> <Path to the image in the Edge container registry/Image name with tag>`

    1. Eseguire il push dell'immagine nel registro.
    
        `docker push <Path to the image in the Edge container registry/Image name with tag>`

    1. Eseguire l'immagine inserita nel registro di sistema.
    
        `docker run -it --rm -p 8080:80 <Path to the image in the Edge container registry/Image name with tag>`

        Di seguito è riportato un esempio di output dei comandi pull e push:

        ```powershell
        PS C:\WINDOWS\system32> docker login ecr.dbe-hw6h1t2.microsoftdatabox.com:31001 -u ase-ecr-user -p 3bbo2sOtDe8FouD
        WARNING! Using --password via the CLI is insecure. Use --password-stdin.
        Login Succeeded
        PS C:\WINDOWS\system32> docker pull mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine
        1.17.5-alpine: Pulling from oss/nginx/nginx
        Digest: sha256:5466bbc0a989bd1cd283c0ba86d9c2fc133491ccfaea63160089f47b32ae973b
        Status: Image is up to date for mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine
        mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine
        PS C:\WINDOWS\system32> docker tag mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        PS C:\WINDOWS\system32> docker push ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        The push refers to repository [ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx]
        bba7d2385bc1: Pushed
        77cae8ab23bf: Pushed
        2.0: digest: sha256:b4c0378c841cd76f0b75bc63454bfc6fe194a5220d4eab0d75963bccdbc327ff size: 739
        PS C:\WINDOWS\system32> docker run -it --rm -p 8080:80 ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        2020/11/10 00:00:49 [error] 6#6: *1 open() "/usr/share/nginx/html/favicon.ico" failed (2: No such file or directory), client: 172.17.0.1, server: localhost, request: "GET /favicon.ico HTTP/1.1", host: "localhost:8080", referrer: "http://localhost:8080/"
        172.17.0.1 - - [10/Nov/2020:00:00:49 +0000] "GET /favicon.ico HTTP/1.1" 404 555 "http://localhost:8080/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/86.0.4240.183 Safari/537.36" "-"
        ^C
        PS C:\WINDOWS\system32>    
        ```
    
1. Passare a `http://localhost:8080` per visualizzare il contenitore in esecuzione. In questo caso, verrà visualizzato il server web Nginx che esegue.

    ![Visualizzare il contenitore in esecuzione](media/azure-stack-edge-gpu-edge-container-registry/view-running-container-1.png)

    Per arrestare e rimuovere il contenitore, premere `Control+C`.

 

## <a name="use-edge-container-registry-images-via-kubernetes-pods"></a>Usare le immagini del registro contenitori Edge tramite i pod Kubernetes

È ora possibile distribuire l'immagine di cui è stato eseguito il push nel registro contenitori perimetrale dall'interno dei Pod Kubernetes.

1. Per distribuire l'immagine, è necessario configurare l'accesso al cluster tramite *kubectl*. Creare uno spazio dei nomi, un utente, concedere all'utente l'accesso allo spazio dei nomi e ottenere un file di *configurazione* . Verificare che sia possibile connettersi ai pod Kubernetes. 
    
    Seguire tutti i passaggi in [connettersi a un cluster Kubernetes e gestirlo tramite kubectl sul dispositivo GPU Pro Azure stack Edge](azure-stack-edge-gpu-create-kubernetes-cluster.md). 

    Di seguito è riportato un esempio di output per uno spazio dei nomi nel dispositivo da cui l'utente può accedere al cluster Kubernetes.

    ```powershell
    [10.128.44.40]: PS>New-HcsKubernetesNamespace -Namespace myecr
    [10.128.44.40]: PS>New-HcsKubernetesUser -UserName ecruser
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01URXdOVEF6TkRJek1Gb1hEVE13TVRFd016QXpOREl6TUZvd0ZURVRNQkVnNjOVRLWndCQ042cm1XQms2eXFwcXI1MUx6bApTaXMyTy91UEJ2YXNSSUUzdzgrbmEwdG1aTERZZ2F6MkQwMm42Q29mUmtyUTR2d1lLTnR1MlpzR3pUdz0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
        server: https://compute.dbe-hw6h1t2.microsoftdatabox.com:6443
        name: kubernetes
        ===================CUT=========================================CUT==============
        client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwRENDQWJpZ0F3SUJBZ0lJYmVWRGJSTzZ3ell3RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURFeE1EVXdNelF5TXpCYUZ3MHlNVEV4TURreU16UTRNal
        ===================CUT=========================================CUT==============
        DMVUvN3lFOG5UU3k3b2VPWitUeHdzCjF1UDByMjhDZ1lCdHdRY0ZpcFh1blN5ak16dTNIYjhveFI2V3VWWmZldFFKNElKWEFXOStVWGhKTFhyQ2x4bUcKWHRtbCt4UU5UTzFjQVNKRVZWVDd6Tjg2ay9kSU43S3JIVkdUdUxlUDd4eGVjV2VRcWJrZEVScUsxN0liTXpiVApmbnNxc0dobEdmLzdmM21kTGtyOENrcWs5TU5aM3MvUVIwRlFCdk94ZVpuUlpTeDVGbUR5S1E9PQotLS0tLUVORCBSU0EgUFJJVkFURSBLRVktLS0tLQo=

    [10.128.44.40]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace myecr -UserName ecruser
    [10.128.44.40]: PS>kubectl get pods -n "myecr"
    No resources found.
    PS C:\WINDOWS\system32>
    ```  

2. I segreti di pull dell'immagine sono già impostati in tutti gli spazi dei nomi Kubernetes nel dispositivo. È possibile ottenere i segreti usando il `get secrets` comando. Di seguito è riportato un output di esempio:

    ```powershell
    PS C:\WINDOWS\system32> .\kubectl.exe get secrets -n myecr
    NAME                  TYPE                                  DATA   AGE
    ase-ecr-credentials   kubernetes.io/dockerconfigjson        1      99m
    default-token-c7kww   kubernetes.io/service-account-token   3      107m
    sec-smbcredentials    microsoft.com/smb                     2      99m
    PS C:\WINDOWS\system32>   
    ```    

3. Distribuire un pod nello spazio dei nomi usando kubectl. Usare il comando seguente `yaml` . 

    Sostituire l'immagine: `<image-name>` con l'immagine inserita nel registro contenitori. Fare riferimento ai segreti negli spazi dei nomi usando imagePullSecrets con un nome: `ase-ecr-credentials` .
    
    ```yml
    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx
    spec:
      containers:
      - name: nginx
        image: ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        imagePullPolicy: Always
      imagePullSecrets:
      - name: ase-ecr-credentials
    ```

4. Applicare la distribuzione nello spazio dei nomi creato con il comando Apply. Verificare che il contenitore sia in esecuzione. Di seguito è riportato un output di esempio:
   
    ```yml
    PS C:\Windows\System32> .\kubectl.exe apply -f .\deployment.yml -n myecr
    pod/nginx configured
    PS C:\Windows\System32> .\kubectl.exe get pods -n myecr
    NAME    READY   STATUS    RESTARTS   AGE
    nginx   1/1     Running   0          27m
    PS C:\Windows\System32>
    ```

## <a name="delete-container-registry-images"></a>Elimina immagini del registro contenitori

L'archiviazione Container Registry Edge è ospitata in una condivisione locale all'interno del dispositivo Pro Azure Stack Edge, che è limitata dallo spazio di archiviazione disponibile nel dispositivo. È responsabilità dell'utente eliminare le immagini Docker inutilizzate dal registro contenitori usando l'API HTTP V2 di Docker ( https://docs.docker.com/registry/spec/api/) .  

Per rimuovere una o più immagini del contenitore, attenersi alla seguente procedura:

1. Impostare il nome dell'immagine sull'immagine che si desidera eliminare.

    ```powershell
    PS C:\WINDOWS\system32> $imageName="nginx"    
    ```

1. Impostare il nome utente e la password del registro contenitori come credenziale PS

    ```powershell
    PS C:\WINDOWS\system32> $username="ase-ecr-user"
    PS C:\WINDOWS\system32> $password="3bbo2sOtDe8FouD"
    PS C:\WINDOWS\system32> $securePassword = ConvertTo-SecureString $password -AsPlainText -Force
    PS C:\WINDOWS\system32> $credential = New-Object System.Management.Automation.PSCredential ($username, $securePassword)    
    ```

1. Elenca i tag associati all'immagine

    ```powershell
    PS C:\WINDOWS\system32> $tags = Invoke-RestMethod -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/nginx/tags/list" | Select-Object -ExpandProperty tags
    PS C:\WINDOWS\system32> $tags
    2.0
    PS C:\WINDOWS\system32> $tags = Invoke-RestMethod -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/$imageName/tags/list" | Select-Object -ExpandProperty tags
    PS C:\WINDOWS\system32> $tags
    2.0
    PS C:\WINDOWS\system32>    
    ```
  
1. Elencare il digest associato al tag che si desidera eliminare. USA $tags dall'output del comando precedente. Se sono presenti più tag, selezionarne uno e usarlo nel comando successivo.

    ```powershell
    PS C:\WINDOWS\system32> $response = Invoke-WebRequest -Method Head -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/$imageName/manifests/$tags" -Headers @{ 'Accept' = 'application/vnd.docker.distribution.manifest.v2+json' }
    PS C:\WINDOWS\system32> $digest = $response.Headers['Docker-Content-Digest']
    PS C:\WINDOWS\system32> $digest
    sha256:b4c0378c841cd76f0b75bc63454bfc6fe194a5220d4eab0d75963bccdbc327ff
    PS C:\WINDOWS\system32>    
    ```
1. Eliminare l'immagine usando il digest dell'immagine: Tag

    ```powershell
    PS C:\WINDOWS\system32> Invoke-WebRequest -Method Delete -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/$imageName/manifests/$digest" | Select-Object -ExpandProperty StatusDescription    
    ```

Dopo aver eliminato le immagini inutilizzate, lo spazio associato alle immagini senza riferimenti viene recuperato automaticamente da un processo che viene eseguito ogni notte. 

## <a name="next-steps"></a>Passaggi successivi

- [Distribuire un'applicazione senza stato nel Azure stack Edge Pro](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md).
