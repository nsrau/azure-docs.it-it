---
title: Connettersi a un cluster del servizio contenitore di Azure | Documentazione Microsoft
description: Connettersi a un cluster Kubernetes, DC/OS o Docker Swarm nel servizio contenitore di Azure da un computer remoto
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, contenitori, Micro-Service, Kubernetes, DC/OS, Azure
ms.assetid: ff8d9e32-20d2-4658-829f-590dec89603d
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2017
ms.author: rogardle
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f291186c6a68dea8aa00b846a2e6f3ad0d7996c
ms.openlocfilehash: 317399c04afa9f81e5916c08b750d494dd7704dc
ms.contentlocale: it-it
ms.lasthandoff: 04/28/2017


---
# <a name="make-a-remote-connection-to-a-kubernetes-dcos-or-docker-swarm-cluster"></a>Stabilire una connessione remota a un cluster Kubernetes, DC/OS o Docker Swarm
Dopo aver creato un cluster del servizio contenitore di Azure è necessario connettersi al cluster per distribuire e gestire i carichi di lavoro. Questo articolo descrive come connettersi alla VM master del cluster da un computer remoto. 

I cluster Kubernetes, DC/OS e Docker Swarm forniscono localmente endpoint HTTP. Per Kubernetes, questo endpoint viene esposto in modo sicuro in Internet ed è possibile accedervi eseguendo lo strumento da riga di comando `kubectl` da qualsiasi computer connesso a Internet. 

Per DC/OS e Docker Swarm, è consigliabile creare un tunnel Secure Shell (SSH) dal computer locale al sistema di gestione cluster. Dopo la creazione del tunnel, è possibile eseguire comandi che usano gli endpoint HTTP e visualizzare l'interfaccia Web dell'agente di orchestrazione (se disponibile) dal sistema locale. 


## <a name="prerequisites"></a>Prerequisiti

* Un cluster Kubernetes, DC/OS o Docker Swarm [distribuito nel servizio contenitore di Azure](container-service-deployment.md).
* File di chiave privata SSH RSA, corrispondente alla chiave pubblica aggiunta al cluster durante la distribuzione. Questi comandi presuppongono che la chiave privata SSH si trovi in `$HOME/.ssh/id_rsa` nel computer. Per altre informazioni, vedere le istruzioni per [macOS e Linux](../virtual-machines/linux/mac-create-ssh-keys.md) o per [Windows](../virtual-machines/linux/ssh-from-windows.md). Se la connessione SSH non funziona, potrebbe essere necessario [reimpostare le chiavi SSH](../virtual-machines/linux/troubleshoot-ssh-connection.md).

## <a name="connect-to-a-kubernetes-cluster"></a>Connettersi a un cluster Kubernetes

Seguire questi passaggi per installare e configurare `kubectl` nel computer.

> [!NOTE] 
> In Linux o macOS potrebbe essere necessario eseguire i comandi in questa sezione usando `sudo`.
> 

### <a name="install-kubectl"></a>Installare kubectl
Un modo per installare questo strumento è usare il comando `az acs kubernetes install-cli` dell'interfaccia della riga di comando di Azure 2.0. Per eseguire questo comando, assicurarsi di avere [installato](/cli/azure/install-az-cli2) la versione più recente dell'interfaccia della riga di comando di Azure 2.0 e di avere effettuato l'accesso a un account Azure (`az login`).

```azurecli
# Linux or macOS
az acs kubernetes install-cli [--install-location=/some/directory/kubectl]

# Windows
az acs kubernetes install-cli [--install-location=C:\some\directory\kubectl.exe]
```

In alternativa, è possibile scaricare il client `kubectl` più recente direttamente dalla [pagina delle versioni di Kubernetes](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG.md). Per altre informazioni, vedere [Installing and Setting up kubectl](https://kubernetes.io/docs/tasks/kubectl/install/) (Installazione e configurazione di kubectl).

### <a name="download-cluster-credentials"></a>Scaricare le credenziali del cluster
Dopo aver installato `kubectl`, è necessario copiare le credenziali del cluster nel computer. Un modo per ottenere le credenziali è con il comando `az acs kubernetes get-credentials`. Passare il nome del gruppo di risorse e il nome della risorsa del servizio contenitore:


```azurecli
az acs kubernetes get-credentials --resource-group=<cluster-resource-group> --name=<cluster-name>
```

Questo comando scarica le credenziali del cluster in `$HOME/.kube/config` dove si prevede che sia disponibile `kubectl`.

In alternativa è possibile usare `scp` per copiare in modo sicuro i file da `$HOME/.kube/config` nella VM master al computer locale. Ad esempio:

```bash
mkdir $HOME/.kube
scp azureuser@<master-dns-name>:.kube/config $HOME/.kube/config
```

Se si esegue questa operazione in Windows, è possibile usare Bash su Ubuntu in Windows, il client di copia file sicura PuTTY o uno strumento simile.



### <a name="use-kubectl"></a>Usare kubectl

Dopo aver configurato `kubectl`, testare la connessione elencando i nodi nel cluster:

```bash
kubectl get nodes
```

È possibile provare altri comandi `kubectl`. È ad esempio possibile visualizzare il dashboard Kubernetes. Eseguire prima un proxy per il server API Kubernetes:

```bash
kubectl proxy
```

L'interfaccia utente di Kubernetes è ora disponibile all'indirizzo: `http://localhost:8001/ui`.

Per altre informazioni, vedere la [Guida rapida di Kubernetes](http://kubernetes.io/docs/user-guide/quick-start/).

## <a name="connect-to-a-dcos-or-swarm-cluster"></a>Connettersi a un cluster DC/OS o Swarm

Per usare i cluster DC/OS e Docker Swarm distribuiti dal servizio contenitore di Azure, seguire queste istruzioni per creare un tunnel SSH dal sistema Linux, macOS o Windows locale. 

> [!NOTE]
> Queste istruzioni sono incentrate sul tunneling del traffico TCP su SSH. È anche possibile avviare una sessione SSH interattiva con uno dei sistemi interni di gestione dei cluster, ma questa opzione non è consigliabile. L'intervento diretto su un sistema interno comporta il rischio di modifiche accidentali alla configurazione.  
> 

### <a name="create-an-ssh-tunnel-on-linux-or-macos"></a>Creare un tunnel SSH in Linux o macOS
Quando si crea un tunnel SSH in Linux o macOS, prima di tutto è necessario individuare il nome DNS pubblico dei master con carico bilanciato. A tale scopo, seguire questa procedura:


1. Nel [portale di Azure](https://portal.azure.com) passare al gruppo di risorse che contiene il cluster del servizio contenitore. Espandere il gruppo di risorse in modo che vengano visualizzate tutte le risorse. 

2. Fare clic sulla risorsa del **servizio contenitore** e quindi su **Panoramica**. Viene visualizzato il **Nome di dominio completo master** del cluster in **Informazioni di base**. Salvare questo nome per usarlo in seguito. 

    ![Nome DNS pubblico](media/pubdns.png)

    In alternativa, eseguire il comando `az acs show` nel servizio contenitore. Cercare la proprietà **Master Profile:fqdn** nell'output del comando.

3. Aprire ora una shell ed eseguire il comando `ssh` specificando i valori seguenti: 

    **LOCAL_PORT** è la porta TCP sul lato server del tunnel a cui connettersi. Per Swarm, impostare questo valore su 2375. Per DC/OS, impostare questo valore su 80. 
    **REMOTE_PORT** è la porta dell'endpoint da esporre. Per Swarm, usare la porta 2375. Per DC/OS usare la porta 80.  
    **USERNAME** è il nome utente specificato al momento della distribuzione del cluster.  
    **DNSPREFIX** è il prefisso DNS specificato al momento della distribuzione del cluster.  
    **REGION** è l'area in cui si trova il gruppo di risorse.  
    **PATH_TO_PRIVATE_KEY** [FACOLTATIVO] è il percorso della chiave privata che corrisponde alla chiave pubblica specificata durante la creazione del cluster. Usare questa opzione con il flag `-i`.

    ```bash
    ssh -fNL LOCAL_PORT:localhost:REMOTE_PORT -p 2200 [USERNAME]@[DNSPREFIX]mgmt.[REGION].cloudapp.azure.com
    ```
  
  > [!NOTE]
  > La porta di connessione SSH è la 2200, non la porta 22 standard. In un cluster con più di una VM master, si tratta della porta di connessione per la prima VM master.
  > 

  Il comando viene completato senza output.

Vedere gli esempi per DC/OS e Swarm nelle sezioni seguenti.    

### <a name="dcos-tunnel"></a>Tunnel DC/OS
Per aprire un tunnel per endpoint DC/OS, eseguire un comando simile al seguente:

```bash
sudo ssh -fNL 80:localhost:80 -p 2200 azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com 
```

> [!NOTE]
> Verificare che non sia presente un altro processo locale che associa la porta 80. Se necessario, è possibile specificare una porta locale diversa dalla porta 80, ad esempio la porta 8080. È tuttavia possibile che alcuni collegamenti dell'interfaccia utente Web non funzionino quando si usa questa porta.
>

È ora possibile accedere agli endpoint DC/OS dal sistema locale tramite gli URL (presupponendo che la porta locale sia 80):

* Controller di dominio/sistema operativo: `http://localhost:80/`
* Marathon: `http://localhost:80/marathon`
* Mesos: `http://localhost:80/mesos`

In modo analogo, è possibile raggiungere le API REST per ogni applicazione attraverso questo tunnel.

### <a name="swarm-tunnel"></a>Tunnel Swarm
Per aprire un tunnel per gli endpoint Swarm, eseguire un comando simile al seguente:

```bash
ssh -fNL 2375:localhost:2375 -p 2200 azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com
```
> [!NOTE]
> Verificare che non sia presente un altro processo locale che associa la porta 2375. Se il daemon Docker viene eseguito in locale, ad esempio, per impostazione predefinita usa la porta 2375. Se necessario, è possibile specificare una porta locale diversa dalla porta 2375.
>

È ora possibile accedere al cluster Docker Swarm usando l'interfaccia della riga di comando di Docker nel sistema locale. Per istruzioni sull'installazione, vedere [Install Docker](https://docs.docker.com/engine/installation/) (Installare Docker).

Impostare la variabile di ambiente DOCKER_HOST sulla porta locale configurata per il tunnel. 

```bash
export DOCKER_HOST=:2375
```

Eseguire i comandi di Docker che effettuano il tunneling al cluster Docker Swarm, ad esempio:

```bash
docker info
```



### <a name="create-an-ssh-tunnel-on-windows"></a>Creare un tunnel SSH in Windows
Esistono più opzioni per creare i tunnel SSH in Windows. Se si esegue Bash su Ubuntu in Windows o uno strumento simile, è possibile seguire le istruzioni relative al tunneling SSH riportate più indietro in questo articolo per macOS e Linux. Come alternativa in Windows, questa sezione descrive come usare PuTTY per creare il tunnel.

1. [Scaricare PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) nel sistema Windows.

2. Eseguire l'applicazione.

3. Immettere un nome host che include il nome utente dell'amministratore cluster e il nome DNS pubblico del primo master nel cluster. Il **nome host** è simile a `azureuser@PublicDNSName`. Immettere 2200 nel campo **Port**.

    ![Configurazione PuTTY 1](media/putty1.png)

4. Selezionare **SSH > Auth**. Aggiungere un percorso al file di chiave privata (formato ppk) per l'autenticazione. È possibile usare uno strumento come [PuTTYgen](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) per generare questo file dalla chiave SSH usata per creare il cluster.

    ![Configurazione PuTTY 2](media/putty2.png)

5. Selezionare **SSH > Tunnels** (Tunnel) e configurare le porte inoltrate seguenti:

    * **Source Port** (Porta di origine): usare 80 per DC/OS o 2375 per Swarm.
    * **Destination:** usare localhost:80 per il controller di dominio/sistema operativo o localhost:2375 per Swarm.

    L'esempio seguente è configurato per DC/OS, ma avrà un aspetto simile anche per Docker Swarm.

    > [!NOTE]
    > La porta 80 non deve essere usata quando si crea il tunnel.
    > 

    ![Configurazione PuTTY 3](media/putty3.png)

6. Al termine, fare clic su **Session > Save** (Sessione > Salva) per salvare la configurazione di connessione.

7. Fare clic su **Open** (Apri) per connettersi alla sessione PuTTY. Dopo la connessione, è possibile visualizzare la configurazione della porta nel registro eventi di PuTTY.

    ![Log eventi di PuTTY](media/putty4.png)

Dopo aver configurato il tunnel per DC/OS, è possibile accedere agli endpoint correlati all'indirizzo:

* Controller di dominio/sistema operativo: `http://localhost/`
* Marathon: `http://localhost/marathon`
* Mesos: `http://localhost/mesos`

Dopo aver configurato il tunnel per Docker Swarm, aprire le impostazioni di Windows per configurare una variabile di ambiente di sistema denominata `DOCKER_HOST` con un valore di `:2375`. Sarà quindi possibile accedere al cluster Swarm dall'interfaccia della riga di comando di Docker.

## <a name="next-steps"></a>Passaggi successivi
Distribuire e gestire contenitori nel cluster:

* [Uso del servizio contenitore di Azure e Kubernetes](container-service-kubernetes-ui.md)
* [Gestione di contenitori tramite l'API REST](container-service-mesos-marathon-rest.md)
* [Gestione dei contenitori con Docker Swarm](container-service-docker-swarm.md)


