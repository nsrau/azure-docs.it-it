---
title: Uso di estensioni VM Docker per Linux | Microsoft Docs
description: Descrive Docker e le estensioni di Macchine virtuali di Azure e come creare le macchine virtuali di Azure che siano host Docker usando l'interfaccia della riga di comando di Azure nel modello di distribuzione classica.
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 19cf64e8-f92c-43ad-a120-8976cd9102ac
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/27/2016
ms.author: rasquill
ms.openlocfilehash: 932744208d9d53c87e31dcdf9e34539750be4bdb
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="using-the-docker-vm-extension-with-the-azure-classic-portal"></a>Come usare l'estensione della VM Docker con il portale di Azure classico
> [!IMPORTANT] 
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../../../resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti.

[Docker](https://www.docker.com/) è uno dei più popolari approcci alla virtualizzazione che usa [contenitori Linux](http://en.wikipedia.org/wiki/LXC) invece di macchine virtuali allo scopo di isolare i dati ed eseguire i calcoli su risorse condivise. È possibile usare l'estensione della VM Docker per l' [Agente Linux di Azure] per creare una macchina virtuale Docker che ospiti un numero qualsiasi di contenitori per le applicazioni in Azure.

> [!NOTE]
> Questo argomento descrive come creare una VM Docker dal portale di Azure classico. Per scoprire come creare una macchina virtuale Docker nella riga di comando, vedere [Come usare l'estensione della VM Docker dall'interfaccia della riga di comando di Azure]. Per assistere a una discussione di alto livello sui contenitori e i relativi vantaggi, guardare questa [sessione con lavagna condivisa relativa a Docker](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).
> 
> 

## <a name="create-a-new-vm-from-the-image-gallery"></a>Creare una nuova VM dalla Raccolta immagini
Il primo passaggio richiede una VM di Azure da un'immagine Linux che supporti l'estensione della VM Docker, usando un'immagine di Ubuntu 14.04 LTS dalla Raccolta immagini come immagine del server di esempio e Ubuntu 14.04 Desktop come client. Nel portale, fare clic su **+ Nuovo** in basso a sinistra per creare una nuova istanza di VM, quindi selezionare un'immagine di Ubuntu 14.04 LTS dalle opzioni disponibili oppure dalla Raccolta immagini completa, come illustrato di seguito.

> [!NOTE]
> Attualmente, solo le immagini di Ubuntu 14.04 LTS successive al mese di luglio 2014 supportano l'estensione della VM Docker.
> 
> 

![Creare una nuova immagine Ubuntu](./media/portal-use-docker/ChooseUbuntu.png)

## <a name="create-docker-certificates"></a>Creare i certificati Docker
Dopo aver creato la VM, assicurarsi di aver installato Docker sul computer client (per informazioni dettagliate, vedere le [istruzioni di installazione di Docker](https://docs.docker.com/installation/#installation)).

Creare il certificato e i file di chiave per la comunicazione di Docker seguendo le istruzioni di [Esecuzione di Docker con https], quindi inserirli nella directory **`~/.docker`** sul computer client.

> [!NOTE]
> L'estensione della VM Docker nel portale attualmente richiede credenziali con codifica Base 64.
> 
> 

Nella riga di comando, usare **`base64`** o  un altro strumento di codifica per creare argomenti con codifica Base 64. L'esecuzione di questa operazione con un semplice set di file di chiave e certificati potrebbe avere un aspetto simile al seguente:

```
 ~/.docker$ ls
 ca-key.pem  ca.pem  cert.pem  key.pem  server-cert.pem  server-key.pem
 ~/.docker$ base64 ca.pem > ca64.pem
 ~/.docker$ base64 server-cert.pem > server-cert64.pem
 ~/.docker$ base64 server-key.pem > server-key64.pem
 ~/.docker$ ls
 ca64.pem    ca.pem    key.pem            server-cert.pem   server-key.pem
 ca-key.pem  cert.pem  server-cert64.pem  server-key64.pem
```

## <a name="add-the-docker-vm-extension"></a>Aggiungere l'estensione della VM Docker
Per aggiungere l'estensione della VM Docker, individuare l'istanza della VM creata e scorrere verso il basso fino a **Estensioni** , quindi fare clic per visualizzare le estensioni della VM, come illustrato di seguito.

> [!NOTE]
> Questa funzionalità è supportata solo nel portale di anteprima: https://portal.azure.com/
> 
> 

![](media/portal-use-docker/ClickExtensions.png)

### <a name="add-an-extension"></a>Aggiungere un'estensione
Fare clic su **+ Aggiungi** per visualizzare le potenziali estensioni che è possibile aggiungere a questa VM.

![](media/portal-use-docker/ClickAdd.png)

### <a name="select-the-docker-vm-extension"></a>Selezionare l'estensione della VM Docker
Scegliere l'estensione della VM Docker che visualizza la descrizione di Docker e collegamenti importanti, quindi fare clic su **Crea** nella parte inferiore per iniziare la procedura di installazione.

![](media/portal-use-docker/ChooseDockerExtension.png)

![](media/portal-use-docker/CreateButtonFocus.png)

### <a name="add-your-certificate-and-key-files"></a>Aggiungere il certificato e i file di chiave:
Nei campi modulo, immettere le versioni con codifica Base 64 del certificato della CA, del certificato del server e della chiave del server, come illustrato nella figura seguente.

![](media/portal-use-docker/AddExtensionFormFilled.png)

> [!NOTE]
> Notare che (come nell'immagine precedente) il valore 2376 è inserito per impostazione predefinita. È possibile immettere qualsiasi endpoint in questa fase, ma il passaggio successivo consiste nell'aprire l'endpoint corrispondente. Se si modifica il valore predefinito, assicurarsi di aprire l'endpoint corrispondente nel passaggio successivo.
> 
> 

## <a name="add-the-docker-communication-endpoint"></a>Aggiungere l'endpoint di comunicazione del Docker
Quando si visualizza il gruppo di risorse creato, selezionare il gruppo di sicurezza di rete associato alla VM, quindi fare clic su **Regole di sicurezza in ingresso** per visualizzare le regole, come illustrato di seguito.

![](media/portal-use-docker/AddingEndpoint.png)

Fare clic su **+ Aggiungi** per aggiungere un'altra regola e, in caso di impostazione predefinita, immettere un nome per l'endpoint (in questo esempio **Docker**) e 2376 per 'Intervallo di porte di destinazione'. Impostare il valore del protocollo su **TCP**, quindi fare clic su **OK** per creare la regola.

![](media/portal-use-docker/AddEndpointFormFilledOut.png)

## <a name="test-your-docker-client-and-azure-docker-host"></a>Testare il client Docker e l'host Docker di Azure
Individuare e copiare il nome del dominio della macchina virtuale e, nella riga di comando del computer client, digitare `docker --tls -H tcp://`*dockerextension*`.cloudapp.net:2376 info` , dove *dockerextension* verrà sostituito con il sottodominio della propria macchina virtuale.

Il risultato sarà simile al seguente:

```
$ docker --tls -H tcp://dockerextension.cloudapp.net:2376 info
Containers: 0
Images: 0
Storage Driver: devicemapper
 Pool Name: docker-8:1-131214-pool
 Pool Blocksize: 65.54 kB
 Data file: /var/lib/docker/devicemapper/devicemapper/data
 Metadata file: /var/lib/docker/devicemapper/devicemapper/metadata
 Data Space Used: 305.7 MB
 Data Space Total: 107.4 GB
 Metadata Space Used: 729.1 kB
 Metadata Space Total: 2.147 GB
 Library Version: 1.02.82-git (2013-10-04)
Execution Driver: native-0.2
Kernel Version: 3.13.0-36-generic
WARNING: No swap limit support
```

Dopo avere completato i passaggi sopra elencati, si sarà ottenuto un host Docker completamente funzionante eseguito in una macchina virtuale di Azure e configurato per accettare connessioni remote da altri client.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passaggi successivi
È ora possibile passare alla [guida dell'utente di Docker] e usare la VM Docker. Per automatizzare la creazione di host Docker in macchine virtuali di Azure tramite l'interfaccia della riga di comando, vedere [Come usare l'estensione della VM Docker dall'interfaccia della riga di comando di Azure]

<!--Anchors-->
[Create a new VM from the Image Gallery]:#createvm
[Create Docker Certificates]:#dockercerts
[Add the Docker VM Extension]:#adddockerextension
[Test Docker Client and Azure Docker Host]:#testclientandserver
[Next steps]:#next-steps

<!--Image references-->
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[6]:./media/markdown-template-for-new-articles/pretty49.png
[7]:./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[Come usare l'estensione della VM Docker dall'interfaccia della riga di comando di Azure]:http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-xplat-cli/
[Agente Linux di Azure]:../agent-user-guide.md
[Link 3 to another azure.microsoft.com documentation topic]:../storage-whatis-account.md

[Esecuzione di Docker con https]:http://docs.docker.com/articles/https/
[guida dell'utente di Docker]:https://docs.docker.com/userguide/
