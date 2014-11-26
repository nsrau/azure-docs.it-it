<properties title="Come usare l'estensione della VM Docker con il portale di Azure" pageTitle="Uso dell'estensione della VM Docker per Linux in Azure" description="Descrive Docker e le estensioni di Macchine virtuali di Azure e mostra come creare a livello di codice le macchine virtuali host di Docker su Azure dalla riga di comando usando l'interfaccia della riga di comando azure-cli." metaKeywords="linux, virtual machines, vm, azure, docker, linux containers,  lxc, virtualization" services="virtual-machines" solutions="dev-test" documentationCenter="virtual-machines" authors="rasquill" videoId="" scriptId="" manager="timlt" />

<tags ms.service="virtual-machines" ms.devlang="multiple" ms.topic="article" ms.tgt_pltfrm="vm-linux" ms.workload="infrastructure-services" ms.date="10/21/2014" ms.author="rasquill" />
<!--The next line, with one pound sign at the beginning, is the page title-->

# Uso dell'estensione della VM Docker con il portale di Azure

[Docker][Docker] è uno dei più popolari approcci alla virtualizzazione che usa [contenitori Linux][contenitori Linux] invece di macchine virtuali allo scopo di isolare i dati ed eseguire i calcoli su risorse condivise. È possibile usare l'estensione della macchina virtuale Docker per l'[Agente Linux di Azure][Agente Linux di Azure] per creare una VM Docker che ospiti un numero qualsiasi di contenitori per le applicazioni su Azure.

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->

Contenuto della sezione

-   [Creare una nuova VM dalla Raccolta immagini][Creare una nuova VM dalla Raccolta immagini]
-   [Creare i certificati Docker][Creare i certificati Docker]
-   [Aggiungere l'estensione della VM Docker][Aggiungere l'estensione della VM Docker]
-   [Testare il client Docker e l'host Docker di Azure][Testare il client Docker e l'host Docker di Azure]
-   [Passaggi successivi][Passaggi successivi]

> [WACOM.NOTE] In questo argomento viene descritto come creare una VM Docker nel portale di Azure. Per scoprire come creare una VM Docker nella riga di comando, vedere [Come usare l'estensione della VM Docker dall'interfaccia multipiattaforma di Azure (xplat-cli)][Come usare l'estensione della VM Docker dall'interfaccia multipiattaforma di Azure (xplat-cli)]. Per assistere a una discussione di alto livello sui contenitori e i relativi vantaggi, guardare questa [sessione con lavagna condivisa relativa a Docker][sessione con lavagna condivisa relativa a Docker].

## <span id="createvm"></span>Creare una nuova VM dalla Raccolta immagini</a>

Il primo passaggio richiede una VM di Azure da un'immagine Linux che supporti l'estensione della VM Docker, usando un'immagine di Ubuntu 14.04 LTS dalla Raccolta immagini come immagine del server di esempio e Ubuntu 14.04 Desktop come client. Nel portale, fare clic su **+ Nuovo** in basso a sinistra per creare una nuova istanza di VM, quindi selezionare un'immagine di Ubuntu 14.04 LTS dalle opzioni disponibili oppure dalla Raccolta immagini completa, come illustrato di seguito.

> [WACOM.NOTE] Attualmente, solo le immagini di Ubuntu 14.04 LTS successive al mese di luglio 2014 supportano l'estensione della VM Docker.

![Creare una nuova immagine Ubuntu][Creare una nuova immagine Ubuntu]

## <a id'dockercerts'>Creare i certificati Docker</a>

Dopo aver creato la VM, assicurarsi di aver installato Docker sul computer client (per informazioni dettagliate, vedere le [istruzioni di installazione di Docker][istruzioni di installazione di Docker]).

Creare il certificato e i file di chiave per la comunicazione Docker seguendo le istruzioni di [esecuzione di Docker con https][esecuzione di Docker con https], quindi inserirli nella directory **`~/.docker`** sul computer client.

> [WACOM.NOTE] L'estensione della VM Docker nel portale attualmente richiede credenziali con codifica Base 64.

Nella riga di comando, usare **`base64`** o un altro strumento di codifica preferito per creare argomenti con codifica Base 64. L'esecuzione di questa operazione con un semplice set di file di chiave e certificati potrebbe avere un aspetto simile al seguente:

     ~/.docker$ l
     ca-key.pem  ca.pem  cert.pem  key.pem  server-cert.pem  server-key.pem
     ~/.docker$ base64 ca.pem > ca64.pem
     ~/.docker$ base64 server-cert.pem > server-cert64.pem
     ~/.docker$ base64 server-key.pem > server-key64.pem
     ~/.docker$ l
     ca64.pem    ca.pem    key.pem            server-cert.pem   server-key.pem
     ca-key.pem  cert.pem  server-cert64.pem  server-key64.pem

## <a id'adddockerextension'>Aggiungere l'estensione della VM Docker</a>

Per aggiungere l'estensione della VM Docker, individuare l'istanza della VM creata e scorrere verso il basso fino a **Estensioni**, quindi fare clic per visualizzare le estensioni della VM, come illustrato di seguito.

![][0]

### Aggiungere un'estensione

Fare clic su **+ Aggiungi** per visualizzare le potenziali estensioni che è possibile aggiungere a questa VM.

![][1]

### Selezionare l'estensione della VM Docker

Scegliere l'estensione della VM Docker che visualizza la descrizione di Docker e collegamenti importanti, quindi fare clic su **Crea** nella parte inferiore per iniziare la procedura di installazione.

![][2]

![][3]

### Aggiungere il certificato e i file di chiave:

Nei campi modulo, immettere le versioni con codifica Base 64 del certificato della CA, del certificato del server e della chiave del server, come illustrato nella figura seguente.

![][4]

> [WACOM.NOTE] Notare che (come nell'immagine precedente) il campo della porta è precompilato con 4243 per impostazione predefinita. È possibile immettere qualsiasi endpoint in questa fase, ma il passaggio successivo consiste nell'aprire l'endpoint corrispondente. Se si modifica il valore predefinito, assicurarsi di aprire l'endpoint corrispondente nel passaggio successivo.

## Aggiungere l'endpoint di comunicazione del Docker

Quando si visualizza la VM nel gruppo di risorse creato, scorrere verso il basso e fare clic su **Endpoint** per visualizzare gli endpoint sulla VM, come qui illustrato.

![][5]

Fare clic su **+ Aggiungi** per aggiungere un altro endpoint e, in caso di impostazione predefinita, immettere un nome per l'endpoint (in questo esempio, **docker**) e 4243 per entrambe le porte (privata e pubblica). Lasciare invariato il valore del protocollo **TCP** e fare clic su **OK** per creare l'endpoint.

![][6]

## <span id="testclientandserver"></span>Testare il client Docker e l'host Docker di Azure</a>

Individuare e copiare il nome del dominio della VM e, nella riga di comando del computer client, digitare `docker --tls -H tcp://`*dockerextension*`.cloudapp.net:4243 info` (dove *dockerextension* verrà sostituito con il sottodominio della propria VM).

Il risultato sarà simile al seguente:

    $ docker --tls -H tcp://dockerextension.cloudapp.net:4243 info
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

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## Passaggi successivi

È ora possibile passare alla [guida dell'utente di Docker][guida dell'utente di Docker] e usare la VM Docker. Per iniziare a creare le VM Docker in maniera rapida e frequente, vedere [Come usare l'estensione della VM Docker dall'interfaccia multipiattaforma di Azure (xplat-cli)][Come usare l'estensione della VM Docker dall'interfaccia multipiattaforma di Azure (xplat-cli)].

<!--Anchors-->
<!--Image references-->
<!--Link references-->

  [Docker]: https://www.docker.com/
  [contenitori Linux]: http://en.wikipedia.org/wiki/LXC
  [Agente Linux di Azure]: ../virtual-machines-linux-agent-user-guide/
  [Creare una nuova VM dalla Raccolta immagini]: #createvm
  [Creare i certificati Docker]: #dockercerts
  [Aggiungere l'estensione della VM Docker]: #adddockerextension
  [Testare il client Docker e l'host Docker di Azure]: #testclientandserver
  [Passaggi successivi]: #next-steps
  [Come usare l'estensione della VM Docker dall'interfaccia multipiattaforma di Azure (xplat-cli)]: http://azure.microsoft.com/it-it/documentation/articles/virtual-machines-docker-with-xplat-cli/
  [sessione con lavagna condivisa relativa a Docker]: http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard
  [Creare una nuova immagine Ubuntu]: ./media/virtual-machines-docker-with-portal/ChooseUbuntu.png
  [istruzioni di installazione di Docker]: https://docs.docker.com/installation/#installation
  [esecuzione di Docker con https]: http://docs.docker.com/articles/https/
  [0]: ./media/virtual-machines-docker-with-portal/ClickExtensions.png
  [1]: ./media/virtual-machines-docker-with-portal/ClickAdd.png
  [2]: ./media/virtual-machines-docker-with-portal/ChooseDockerExtension.png
  [3]: ./media/virtual-machines-docker-with-portal/CreateButtonFocus.png
  [4]: ./media/virtual-machines-docker-with-portal/AddExtensionFormFilled.png
  [5]: ./media/virtual-machines-docker-with-portal/AddingEndpoint.png
  [6]: ./media/virtual-machines-docker-with-portal/AddEndpointFormFilledOut.png
  [guida dell'utente di Docker]: https://docs.docker.com/userguide/
