---
title: Creare host Docker in Azure con Docker Machine | Documentazione Microsoft
description: Descrive l&quot;uso di Docker Machine per creare host Docker in Azure.
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: 
ms.assetid: 7a3ff6e1-fa93-4a62-b524-ab182d2fea08
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2016
ms.author: mlearned
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: a5b845a93f318b991e14705f0fadea3acd802ced
ms.lasthandoff: 03/21/2017


---
# <a name="create-docker-hosts-in-azure-with-docker-machine"></a>Creare host Docker in Azure con Docker Machine
L'esecuzione dei contenitori [Docker](https://www.docker.com/) richiede una VM host che esegue il daemon Docker.
Questo argomento descrive come usare il comando [docker-machine](https://docs.docker.com/machine/) per creare nuove macchine virtuali Linux, configurate con il daemon Docker, in esecuzione in Azure. 

**Nota:** 

* *Questo articolo si basa sulla versione di Docker Machine 0.9.0-rc2 o versione successiva*
* *I contenitori Windows saranno supportati tramite Docker Machine nel prossimo futuro*

## <a name="create-vms-with-docker-machine"></a>Creare VM con Docker Machine
Creare macchine virtuali host di Docker in Azure con il comando `docker-machine create` usando il driver `azure`. 

Il driver Azure necessita dell'ID sottoscrizione. È possibile usare l'[interfaccia della riga di comando di Azure](cli-install-nodejs.md) o il [Portale di Azure](https://portal.azure.com) per recuperare la sottoscrizione di Azure. 

**Uso del portale di Azure**

* Selezionare le sottoscrizioni nella pagina di esplorazione a sinistra e copiare l'ID sottoscrizione.

**Uso dell'interfaccia della riga di comando di Azure**

* Digitare ```azure account list``` e copiare l'ID sottoscrizione.

Digitare `docker-machine create --driver azure` per visualizzare le opzioni e i relativi valori predefiniti.
È anche possibile visualizzare la [documentazione del driver di Azure per Docker](https://docs.docker.com/machine/drivers/azure/) per altre informazioni. 

L'esempio seguente si basa sui [valori predefiniti](https://github.com/docker/machine/blob/master/drivers/azure/azure.go#L22), ma imposta facoltativamente questi valori: 

* azure-dns per il nome associato all'indirizzo IP pubblico e ai certificati generati.  La macchina virtuale può quindi arrestare e rilasciare in modo sicuro l'indirizzo IP dinamico e consentire di riconnettersi dopo che una macchina virtuale viene avviata nuovamente con un nuovo indirizzo IP.  Il prefisso del nome deve essere univoco per tale area, ovvero UNIQUE_DNSNAME_PREFIX.westus.cloudapp.azure.com.
* porta 80 aperta sulla macchina virtuale per l'accesso Internet in uscita
* dimensioni della macchina virtuale per usare l'archiviazione Premium più veloce
* archiviazione Premium usata per il disco della macchina virtuale

```
docker-machine create -d azure --azure-subscription-id <Your AZURE_SUBSCRIPTION_ID> --azure-dns <Your UNIQUE_DNSNAME_PREFIX> --azure-open-port 80 --azure-size Standard_DS1_v2 --azure-storage-type "Premium_LRS" mydockerhost 
```

## <a name="choose-a-docker-host-with-docker-machine"></a>Scegliere un host di Docker con Docker Machine
Dopo aver creato un ingresso nella Docker Machine per l'host, è possibile impostare l'host predefinito quando si eseguono comandi di Docker.

## <a name="using-powershell"></a>Tramite PowerShell
```powershell
docker-machine env MyDockerHost | Invoke-Expression 
```

## <a name="using-bash"></a>Tramite Bash
```bash
eval $(docker-machine env MyDockerHost)
```

È ora possibile eseguire i comandi di Docker con l'host specificato

```
docker ps
docker info
```

## <a name="run-a-container"></a>Eseguire un contenitore
Dopo aver configurato l'host, è possibile eseguire un semplice server Web per verificare che la configurazione dell'host sia corretta.
In questo esempio viene usata un'immagine nginx standard. Specificare che deve essere in ascolto sulla porta 80 e che, se la VM dell'host viene riavviata, anche il contenitore deve essere riavviato (`--restart=always`). 

```bash
docker run -d -p 80:80 --restart=always nginx
```

L'output dovrebbe essere simile a quanto segue.

```
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
83f52fbfa5f8: Pull complete
fa664caa1402: Pull complete
Digest: sha256:12127e07a75bda1022fbd4ea231f5527a1899aad4679e3940482db3b57383b1d
Status: Downloaded newer image for nginx:latest
25942c35d86fe43c688d0c03ad478f14cc9c16913b0e1c2971cb32eb4d0ab721
```

## <a name="test-the-container"></a>Eseguire il test del contenitore
Esaminare i contenitori in esecuzione usando `docker ps`:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                         NAMES
d5b78f27b335        nginx               "nginx -g 'daemon off"   5 minutes ago       Up 5 minutes        0.0.0.0:80->80/tcp, 443/tcp   goofy_mahavira
```

Quindi verificare il contenitore in esecuzione digitando `docker-machine ip <VM name>` per trovare l'indirizzo IP da immettere nel browser:

```
PS C:\> docker-machine ip MyDockerHost
191.237.46.90
```

![Esecuzione di un contenitore ngnix](./media/vs-azure-tools-docker-machine-azure-config/nginxsuccess.png)

## <a name="summary"></a>Riepilogo
Docker Machine consente di eseguire facilmente il provisioning di host Docker in Azure per le convalide di host Docker singoli.
Per l'hosting di produzione di contenitori, vedere l'articolo sul [servizio contenitore di Azure](http://aka.ms/AzureContainerService)

Per sviluppare applicazioni .NET Core con Visual Studio, vedere gli [Strumenti Docker per Visual Studio](http://aka.ms/DockerToolsForVS)


