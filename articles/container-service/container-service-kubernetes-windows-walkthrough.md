---
title: Cluster Azure Kubernetes per Windows | Microsoft Docs
description: Distribuire e iniziare a usare un cluster Kubernetes per contenitori Windows nel servizio contenitore di Azure
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/03/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: ef1e790edc4cd329245331bf1178ed1f610e914c
ms.lasthandoff: 03/06/2017


---

# <a name="get-started-with-kubernetes-and-windows-containers-in-container-service"></a>Introduzione ai contenitori Kubernetes e Windows nel servizio contenitore


In questo articolo viene illustrato come creare un cluster Kubernetes per eseguire contenitori Windows nel servizio contenitore di Azure che contiene nodi Windows. 

> [!NOTE]
> Il supporto per i contenitori Windows con Kubernetes è disponibile in anteprima nel servizio contenitore di Azure. Usare il Portale di Azure o un modello di Resource Manager per creare un cluster Kubernetes con nodi di Windows. Questa funzionalità non è attualmente supportata nell'interfaccia della riga di comando di Azure 2.0.



Nell'immagine seguente è illustrata l'architettura di un cluster Kubernetes nel servizio contenitore di Azure con un nodo master Linux e due nodi agente Windows. 

* Il nodo master Linux è usato dall'API REST Kubernetes ed è accessibile da SSH alla porta 22 o da `kubectl` alla porta 443. 
* I nodi agente Windows vengono raggruppati in un set di disponibilità di Azure ed eseguono i contenitori. I nodi Windows sono accessibili tramite un tunnel SSH RDP tramite il nodo master. Le regole del servizio di bilanciamento del carico di Azure vengono aggiunte in modo dinamico al cluster in base ai servizi esposti.


![Immagine del cluster Kubernetes in Azure](media/container-service-kubernetes-windows-walkthrough/kubernetes-windows.png)

Tutte le macchine virtuali si trovano nella stessa rete privata virtuale e sono completamente accessibili le une alle altre. Tutte le macchine virtuali eseguono kubelet, Docker e un proxy.

## <a name="prerequisites"></a>Prerequisiti


* **Chiave pubblica SSH RSA**: durante la distribuzione tramite il portale o uno dei modelli di avvio rapido di Azure, è necessario fornire la chiave pubblica SSH RSA per l'autenticazione nelle macchine virtuali del servizio contenitore di Azure. Per creare chiavi SSH (Secure Shell) RSA, vedere le informazioni aggiuntive per [OS X e Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) e [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md). 

* **ID client e segreto dell'entità servizio**: per altre informazioni e istruzioni, vedere [Informazioni sull'entità servizio per un cluster Kubernetes](container-service-kubernetes-service-principal.md).




## <a name="create-the-cluster"></a>Creare il cluster

È possibile usare il Portale di Azure per [creare un cluster Kubernetes](container-service-deployment.md#create-a-cluster-by-using-the-azure-portal) con nodi agente Windows. Quando si crea il cluster, tenere presente le seguenti impostazioni:

* Nel pannello **Basics** (Impostazioni di base), in **Agente di orchestrazione** selezionare **Kubernetes**. 

  ![Selezionare l'agente di orchestrazione Kubernetes](media/container-service-kubernetes-windows-walkthrough/portal-select-kubernetes.png)

* Nel pannello **Master configuration** (Configurazione master) immettere le credenziali dell'utente e dell'entità servizio per i nodi master Linux. Selezionare 1, 3 o 5 master.

* Nel pannello **Configurazione agente**, in **Sistema operativo** selezionare **Windows (anteprima)**. Immettere le credenziali di amministratore per i nodi agente Windows.

  ![Selezionare gli agenti di Windows](media/container-service-kubernetes-windows-walkthrough/portal-select-windows.png)

Per altri dettagli, vedere [Distribuire un cluster del servizio contenitore di Azure](container-service-deployment.md).

## <a name="connect-to-the-cluster"></a>Connettersi al cluster

Usare lo strumento `kubectl` da riga di comando per connettersi dal computer locale al nodo master del cluster Kubernetes. Per i passaggi di installazione e configurazione di `kubectl`, vedere [Connettersi a un cluster del servizio contenitore di Azure](container-service-connect.md#connect-to-a-kubernetes-cluster). È possibile usare i comandi `kubectl` per accedere all'interfaccia utente Web Kubernetes e per creare e gestire carichi di lavoro dei contenitori Windows.

## <a name="create-your-first-kubernetes-service"></a>Creare il primo servizio Kubernetes

Dopo aver creato il cluster e aver eseguito la connessione con `kubectl`, è possibile provare ad avviare un'app Web Windows di base e a esporla online. In questo esempio, si specificano le risorse del contenitore usando un file YAML e quindi lo si crea usando `kubctl apply`.

1. Per visualizzare un elenco dei nodi digitare `kubectl get nodes`. Se si vogliono visualizzare i dettagli completi sui nodi, digitare:  

  ```
  kubectl get nodes -o yaml
  ```

2. Creare un file denominato `simpleweb.yaml` e copiare quanto segue. Questo file configura un'app Web usando l'immagine del sistema operativo di base Windows Server 2016 Server Core da [Docker Hub](https://hub.docker.com/r/microsoft/windowsservercore/).  

  ```yaml
  apiVersion: v1
  kind: Service
  metadata:
    name: win-webserver
    labels:
      app: win-webserver
  spec:
    ports:
      # the port that this service should serve on
    - port: 80
      targetPort: 80
    selector:
      app: win-webserver
    type: LoadBalancer
  ---
  apiVersion: extensions/v1beta1
  kind: Deployment
  metadata:
    labels:
      app: win-webserver
    name: win-webserver
  spec:
    replicas: 1
    template:
      metadata:
        labels:
          app: win-webserver
        name: win-webserver
      spec:
        containers:
        - name: windowswebserver
          image: microsoft/windowsservercore
          command:
          - powershell.exe
          - -command
          - "<#code used from https://gist.github.com/wagnerandrade/5424431#> ; $$ip = (Get-NetIPAddress | where {$$_.IPAddress -Like '*.*.*.*'})[0].IPAddress ; $$url = 'http://'+$$ip+':80/' ; $$listener = New-Object System.Net.HttpListener ; $$listener.Prefixes.Add($$url) ; $$listener.Start() ; $$callerCounts = @{} ; Write-Host('Listening at {0}...' -f $$url) ; while ($$listener.IsListening) { ;$$context = $$listener.GetContext() ;$$requestUrl = $$context.Request.Url ;$$clientIP = $$context.Request.RemoteEndPoint.Address ;$$response = $$context.Response ;Write-Host '' ;Write-Host('> {0}' -f $$requestUrl) ;  ;$$count = 1 ;$$k=$$callerCounts.Get_Item($$clientIP) ;if ($$k -ne $$null) { $$count += $$k } ;$$callerCounts.Set_Item($$clientIP, $$count) ;$$header='<html><body><H1>Windows Container Web Server</H1>' ;$$callerCountsString='' ;$$callerCounts.Keys | % { $$callerCountsString+='<p>IP {0} callerCount {1} ' -f $$_,$$callerCounts.Item($$_) } ;$$footer='</body></html>' ;$$content='{0}{1}{2}' -f $$header,$$callerCountsString,$$footer ;Write-Output $$content ;$$buffer = [System.Text.Encoding]::UTF8.GetBytes($$content) ;$$response.ContentLength64 = $$buffer.Length ;$$response.OutputStream.Write($$buffer, 0, $$buffer.Length) ;$$response.Close() ;$$responseStatus = $$response.StatusCode ;Write-Host('< {0}' -f $$responseStatus)  } ; "
        nodeSelector:
          beta.kubernetes.io/os: windows
  ```

3. Per avviare l'applicazione, digitare:

  ```
  kubectl apply -f simpleweb.yaml
  ```
  
  > [!NOTE] 
  > La configurazione include `type: LoadBalancer`. Questa impostazione espone il servizio online tramite un servizio di bilanciamento del carico di Azure. Per altre informazioni, vedere [Bilanciare il carico dei contenitori in un cluster Kubernetes nel servizio contenitore di Azure](container-service-kubernetes-load-balancing.md).
  
4. Per verificare la distribuzione del servizio, un'operazione che richiede circa 30 secondi, digitare:

  ```
  kubectl get pods
  ```

5. Dopo aver eseguito il servizio, per visualizzare i relativi indirizzi IP interni ed esterni, digitare:

  ```
  kubectl get svc
  ``` 

  ![Indirizzi IP del servizio Windows](media/container-service-kubernetes-windows-walkthrough/externalipa.png)

  L'aggiunta di un indirizzo IP esterno richiede diversi minuti. Prima che l'indirizzo esterno venga configurato dal servizio di bilanciamento del carico, viene visualizzato come `<pending>`.


6. Quando l'indirizzo IP esterno è disponibile, è possibile raggiungere il servizio nel Web browser.

  ![App di Windows Server nel browser](media/container-service-kubernetes-windows-walkthrough/wincontainerwebserver.png)


## <a name="access-the-windows-nodes"></a>Accedere ai nodi Windows
È possibile accedere ai nodi Windows da un computer Windows locale tramite Connessione Desktop remoto. È consigliabile usare un tunnel SSH RDP tramite il nodo master. 

Esistono più opzioni per creare i tunnel SSH in Windows. Questa sezione descrive come usare PuTTY per creare il tunnel.

1. [Scaricare PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) nel sistema Windows.

2. Eseguire l'applicazione.

3. Immettere un nome host che include il nome utente dell'amministratore cluster e il nome DNS pubblico del primo master nel cluster. Il **nome host** è simile a `adminuser@PublicDNSName`. Immettere 22 nel campo **Port**.

    ![Configurazione PuTTY 1](media/container-service-kubernetes-windows-walkthrough/putty1.png)

4. Selezionare **SSH > Auth**. Aggiungere un percorso al file di chiave privata (formato ppk) per l'autenticazione. È possibile usare uno strumento come [PuTTYgen](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) per generare questo file dalla chiave SSH usata per creare il cluster.

    ![Configurazione PuTTY 2](media/container-service-kubernetes-windows-walkthrough/putty2.png)

5. Selezionare **SSH > Tunnels** (Tunnel) e configurare le porte inoltrate. Poiché il computer Windows locale usa già la porta 3389, è consigliabile usare le impostazioni seguenti per raggiungere i nodi Windows 0 e 1. Continuare con questo modello in caso di nodi Windows aggiuntivi.

  **Nodo Windows 0**

  * **Porta di origine:** 3390
  * **Destinazione:** 10.240.245.5:3389

  **Nodo Windows 1**

  * **Porta di origine:** 3391
  * **Destinazione:** 10.240.245.6:3389

  ![Immagine di tunnel RDP Windows](media/container-service-kubernetes-windows-walkthrough/rdptunnels.png)

6. Al termine, fare clic su **Session > Save** (Sessione > Salva) per salvare la configurazione di connessione.

7. Fare clic su **Open** (Apri) per connettersi alla sessione PuTTY. Completare la connessione al nodo master.

8. Avvia connessione Desktop remoto. Per connettersi al primo nodo Windows, per **Computer**, specificare `localhost:3390` e fare clic su **Connetti**. Per connettersi al secondo, specificare `localhost:3390` e così via. Per completare la connessione, fornire la password dell'amministratore Windows locale configurato durante la distribuzione.








## <a name="next-steps"></a>Passaggi successivi

Ecco i collegamenti consigliati per ottenere altre informazioni su Kubernetes:

* [Kubernetes Bootcamp](https://kubernetesbootcamp.github.io/kubernetes-bootcamp/index.html): illustra come distribuire, ridimensionare ed eseguire il debug di applicazione inserite in contenitori.
* [Guida dell'utente di Kubernetes](http://kubernetes.io/docs/user-guide/): offre informazioni sull'esecuzione di programmi in un cluster Kubernetes esistente.
* [Esempi di Kubernetes](https://github.com/kubernetes/kubernetes/tree/master/examples): fornisce esempi relativi all'esecuzione di applicazioni effettive con Kubernetes.
