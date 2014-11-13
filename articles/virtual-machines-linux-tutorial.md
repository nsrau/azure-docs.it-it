<properties urlDisplayName="Create a virtual machine" pageTitle="Creare una macchina virtuale che esegue Linux in Azure" metaKeywords="Azure Linux vm, Linux vm" description="Informazioni su come creare una macchina virtuale di Azure che esegue Linux usando un'immagine da Azure. " metaCanonical="" services="virtual-machines" documentationCenter="" title="" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="10/9/2014" ms.author="kathydav" />

# Creare una macchina virtuale che esegue Linux

Creare una macchina virtuale che esegue Linux è facile quando si usa la raccolta immagini presente nel portale di gestione di Azure. In questa guida, in cui viene spiegato come eseguire questa operazione, non si presuppone la conoscenza delle procedure di Azure.

> [WACOM.NOTE] Anche se per completare questa esercitazione, non è necessario essere esperti di macchine virtuali di Azure, è necessario disporre di un account Azure. È possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni, vedere [Creare un account Azure][Creare un account Azure].

Contenuto dell'esercitazione:

-   [Informazioni sulle macchine virtuali in Azure][Informazioni sulle macchine virtuali in Azure]
-   [Come creare la macchina virtuale][Come creare la macchina virtuale]
-   [Come accedere alla macchina virtuale dopo averla creata][Come accedere alla macchina virtuale dopo averla creata]
-   [Come collegare un disco dati alla nuova macchina virtuale][Come collegare un disco dati alla nuova macchina virtuale]

**Importante**: in questa esercitazione viene creata una macchina virtuale non connessa a una rete virtuale. Se si vuole che una macchina virtuale usi una rete virtuale, è necessario specificare la rete quando si crea la macchina. Per altre informazioni sulle reti virtuali, vedere [Informazioni generali su Rete virtuale di Azure][Informazioni generali su Rete virtuale di Azure].

## <span id="virtualmachine"></span> </a>Informazioni sulle macchine virtuali in Azure

In Azure una macchina virtuale è un server disponibile nel cloud che è possibile controllare e gestire. Dopo aver creato una macchina virtuale in Azure, è possibile eliminarla e ricrearla ogni volta che è necessario ed è possibile accedervi con le stesse modalità con cui si accederebbe a un server nel proprio ufficio. Per creare una macchina virtuale, vengono usati file VHD (Virtual Hard Disk, disco rigido virtuale). Per una macchina virtuale, vengono usati i seguenti tipi di dischi rigidi virtuali:

-   **Immagine** - Un disco rigido virtuale usato come modello per creare una nuova macchina virtuale. Un'immagine è un modello perché non dispone di impostazioni specifiche come nel caso di una macchina virtuale in esecuzione, ad esempio il nome del computer e le impostazioni dell'account utente. Se si crea una macchina virtuale usando un'immagine, viene creato automaticamente un disco del sistema operativo per la nuova macchina.
-   **Disco** - per disco si intende un disco rigido virtuale che è possibile avviare e montare come versione in esecuzione di un sistema operativo. Una volta eseguito il provisioning, un'immagine diventa un disco. Viene sempre creato un disco quando si usa un'immagine per creare una macchina virtuale. Qualsiasi disco rigido virtuale collegato all'hardware virtualizzato e in esecuzione come parte di un servizio è un disco.

Per la creazione di una macchina virtuale tramite immagini sono disponibili le opzioni seguenti:

-   Creare una macchina virtuale usando un'immagine fornita nella raccolta immagini del portale di gestione di Azure.
-   Creare e caricare in Azure un file .vhd contenente un'immagine, quindi creare una macchina virtuale usando l'immagine. Per altre informazioni sulla creazione e il caricamento di un'immagine personalizzata, vedere [Creazione e caricamento di un disco rigido virtuale che contiene il sistema operativo Linux][Creazione e caricamento di un disco rigido virtuale che contiene il sistema operativo Linux].

Ogni macchina virtuale risiede in un servizio cloud, da sola oppure insieme ad altre macchine virtuali. È possibile inserire le macchine virtuali nello stesso servizio cloud per consentire loro di comunicare, per bilanciare il carico del traffico di rete tra le macchine virtuali e per garantire una disponibilità elevata delle macchine. Per altre informazioni sui servizi cloud e sulle macchine virtuali, vedere la sezione relativa ai modelli di esecuzione in [Introduzione ad Azure][Introduzione ad Azure].

## <span id="custommachine"></span> </a>Come creare la macchina virtuale

In questa esercitazione viene usato il metodo **Da raccolta** per creare una macchina virtuale perché consente di configurare un numero maggiore di opzioni rispetto al metodo **Creazione rapida**. Se necessario, è possibile scegliere le risorse connesse, il nome DNS e la connettività di rete.

[WACOM.INCLUDE [virtual-machines-create-LinuxVM](../includes/virtual-machines-create-LinuxVM.md)]

[WACOM.INCLUDE [virtual-machines-Linux-tutorial-log-on-attach-disk](../includes/virtual-machines-Linux-tutorial-log-on-attach-disk.md)]

## Passaggi successivi

Per altre informazioni su Linux in Azure, vedere gli articoli seguenti:

-   [Introduzione a Linux in Azure][Introduzione a Linux in Azure]

-   [Come usare gli strumenti da riga di comando di Azure per Mac e Linux][Come usare gli strumenti da riga di comando di Azure per Mac e Linux]

-   [Informazioni sulle impostazioni di configurazione delle macchine virtuali di Azure][Informazioni sulle impostazioni di configurazione delle macchine virtuali di Azure]

  [Creare un account Azure]: http://www.windowsazure.com/it-it/develop/php/tutorials/create-a-windows-azure-account/
  [Informazioni sulle macchine virtuali in Azure]: #virtualmachine
  [Come creare la macchina virtuale]: #custommachine
  [Come accedere alla macchina virtuale dopo averla creata]: #logon
  [Come collegare un disco dati alla nuova macchina virtuale]: #attachdisk
  [Informazioni generali su Rete virtuale di Azure]: http://go.microsoft.com/fwlink/p/?LinkID=294063
  [Creazione e caricamento di un disco rigido virtuale che contiene il sistema operativo Linux]: /it-it/manage/linux/common-tasks/upload-a-vhd/
  [Introduzione ad Azure]: http://go.microsoft.com/fwlink/p/?LinkId=311926
  [Introduzione a Linux in Azure]: http://www.windowsazure.com/it-it/documentation/articles/introduction-linux/
  [Come usare gli strumenti da riga di comando di Azure per Mac e Linux]: http://www.windowsazure.com/it-it/documentation/articles/xplat-cli/
  [Informazioni sulle impostazioni di configurazione delle macchine virtuali di Azure]: http://msdn.microsoft.com/library/azure/dn763935.aspx
