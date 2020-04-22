---
title: Creare gli asset tecnici della macchina virtuale di AzureCreate your Azure Virtual Machine technical assets
description: Informazioni su come creare e configurare asset tecnici per un'offerta di macchina virtuale (VM) per Azure Marketplace.Learn how to create and configure technical assets for a virtual machine (VM) offer for Azure Marketplace.
author: dannyevers
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: 4d2d33f9d83132147b5b257ffcd6d659f272b8ec
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730725"
---
# <a name="create-your-azure-virtual-machine-technical-assets"></a>Creare gli asset tecnici della macchina virtuale di AzureCreate your Azure Virtual Machine technical assets

> [!IMPORTANT]
> Stiamo spostando la gestione delle offerte di Macchine virtuali di Azure dal portale Cloud Partner al Centro per i partner. Fino a quando non viene eseguita la migrazione delle offerte, seguire le istruzioni in [Creare asset tecnici per un'offerta](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-technical-assets) di macchine virtuali per il portale Cloud Partner per gestire le offerte.

Questo articolo descrive come creare e configurare asset tecnici per un'offerta di macchina virtuale (VM) per Azure Marketplace.This article describes how to create and configure technical assets for a virtual machine (VM) offer for Azure Marketplace. Una macchina virtuale contiene due componenti: il disco rigido virtuale (VHD) del sistema operativo e i dischi dati associati facoltativi:

* **VHD del sistema operativo:** contiene il sistema operativo e la soluzione distribuiti con l'offerta. Il processo di preparazione del disco rigido virtuale varia a seconda che si tratti di una macchina virtuale basata su Linux, basata su Windows o personalizzata.
* **Dischi dati VHD** - Archiviazione dedicata e permanente per una macchina virtuale. Non usare il disco rigido virtuale del sistema operativo (ad esempio, l'unità C:) per archiviare informazioni permanenti.

Un'immagine di macchina virtuale contiene un disco del sistema operativo e fino a 16 dischi dati. Usare un disco rigido virtuale per ogni disco dati, anche se il disco è vuoto.

> [!NOTE]
> Indipendentemente dal sistema operativo utilizzato, aggiungere solo il numero minimo di dischi dati necessari per la soluzione. I clienti non possono rimuovere dischi che fanno parte di un'immagine al momento della distribuzione, ma possono sempre aggiungere dischi durante o dopo la distribuzione.

> [!IMPORTANT]
> Ogni immagine di macchina virtuale in un piano deve avere lo stesso numero di dischi dati.

## <a name="fundamental-technical-knowledge"></a>Conoscenze tecniche fondamentali

La progettazione, la creazione e il test di questi asset richiedono tempo e richiedono conoscenze tecniche sia della piattaforma Azure che delle tecnologie usate per creare l'offerta. Oltre al dominio della soluzione, il team di progettazione deve conoscere le seguenti tecnologie Microsoft:

* Conoscenza di base di [Servizi di Azure](https://azure.microsoft.com/services/)
* Capacità di [progettare applicazioni di Azure per architetture diverse](https://azure.microsoft.com/solutions/architecture/)
* Conoscenza pratica di [Macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/), [Archiviazione di Azure](https://azure.microsoft.com/services/?filter=storage) e [Rete di Azure](https://azure.microsoft.com/services/?filter=networking)
* Conoscenza pratica di [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
* Conoscenza pratica di [JSON](https://www.json.org/)

## <a name="suggested-tools--optional"></a>Strumenti suggeriti – opzionali

Prendere in considerazione l'uso di uno degli ambienti di scripting seguenti per semplificare la gestione di macchine virtuali e di schivare:Consider using one of the following scripting environments to help manage VMs and VHDs:

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Interfaccia della riga di comando di Azure](https://code.visualstudio.com/)

Inoltre, è consigliabile aggiungere i seguenti strumenti all'ambiente di sviluppo:

* [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Visual Studio Code](https://code.visualstudio.com/)
  * Estensione: [Strumenti di Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  * Estensione: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  * Estensione: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Esaminare gli strumenti disponibili nella pagina Strumenti di sviluppo di [Azure](https://azure.microsoft.com/product-categories/developer-tools/) e, se si utilizza Visual Studio, Visual [Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-a-vm-image-using-an-approved-base"></a>Creare un'immagine di macchina virtuale usando una base approvataCreate a VM image using an approved base

> [!NOTE]
> Per creare le risorse tecniche della macchina virtuale usando un'immagine creata in locale, passare a [Creare una macchina virtuale usando un'immagine personalizzata.](#create-a-vm-using-your-own-image)

In questa sezione vengono descritti vari aspetti dell'utilizzo di una base approvata, ad esempio l'utilizzo del protocollo Remote Desktop Protocol (RDP), la selezione di una dimensione per la macchina virtuale, l'installazione degli aggiornamenti di Windows più recenti e la generalizzazione dell'immagine VHD.

Le sezioni seguenti si concentrano principalmente sui dischi rigidi virtuali basati su Windows.The following sections focus mainly on Windows-based VHDs. Per altre informazioni sulla creazione di dischi rigidi virtuali basati su Linux, vedere [Linux sulle distribuzioni approvate da Azure.For](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)more information about creating Linux-based VHDs, see Linux on distributions endorsed by Azure .

> [!WARNING]
> Seguire le indicazioni in questo argomento per usare Azure per creare una macchina virtuale contenente un sistema operativo preconfigurato e approvato. Se non è compatibile con la soluzione, è possibile creare e configurare una macchina virtuale locale usando un sistema operativo approvato. È quindi possibile configurare e prepararla per il caricamento, come descritto in [Preparare un disco rigido virtuale Windows o VHDX prima del caricamento in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).

### <a name="select-an-approved-base"></a>Selezionare una base approvata

Selezionare il sistema operativo Windows o Linux come base.

#### <a name="windows"></a>WINDOWS

Il disco rigido virtuale del sistema operativo per l'immagine della macchina virtuale basata su Windows deve essere basato su un'immagine di base approvata da Azure che contiene Windows Server o SQL Server.The operating system VHD for your Windows-based VM image must be based on an Azure-approved base image that contains Windows Server or SQL Server. Per iniziare, creare una macchina virtuale da una delle immagini seguenti dal portale di Azure:To begin, create a VM from one of the following images from the Azure portal:

* Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016), [2012 R2 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2012 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
* [SQL Server 2014](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, Standard, Web)
* [SQL Server 2012 SP2](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, Standard, Web)

> [!NOTE]
> Se si usa il portale di Azure corrente o Azure PowerShell, le immagini di Windows Server pubblicate l'8 settembre 2014 e versioni successive vengono approvate.

#### <a name="linux"></a>Linux

Azure offre una gamma di distribuzioni Linux approvate. Per un elenco aggiornato, vedere [Linux in distribuzioni approvate da Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).

### <a name="create-vm-in-the-azure-portal"></a>Creare una macchina virtuale nel portale di Azure

Seguire questi passaggi per creare l'immagine della macchina virtuale di base nel portale di Azure:Follow these steps to create the base VM image in the [Azure portal:](https://ms.portal.azure.com/)

1. Accedere al [portale](https://ms.portal.azure.com/) di Azure con l'account Microsoft associato alla sottoscrizione di Azure che si vuole usare per pubblicare l'offerta di macchina virtuale.
2. Creare un nuovo gruppo di risorse e specificare i valori per **Nome del gruppo di risorse**, **Sottoscrizione** e **Località del gruppo di risorse**. Per informazioni dettagliate, vedere [Gestire le risorse.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)
3. Selezionare **Macchine virtuali** a sinistra per visualizzare la pagina dei dettagli Macchine virtuali.
4. Selezionare **Aggiungi** per aprire l'esperienza **Crea una macchina virtuale**.
5. Selezionare l'immagine dall'elenco a discesa o fare clic su **Sfoglia tutte le immagini pubbliche e private** per cercare o sfogliare tutte le immagini di macchine virtuali disponibili.
6. Selezionare le dimensioni della macchina virtuale per eseguire la distribuzione seguendo queste indicazioni:
    * Se si prevede di sviluppare il disco rigido virtuale in locale, la dimensione non è rilevante. Valutare l'opportunità di usare una delle macchine virtuali di dimensioni minori.
    * Se si prevede di sviluppare l'immagine in Azure, valutare l'opportunità di usare una delle dimensioni di macchina virtuale consigliate per l'immagine selezionata.

7. Nella sezione **Dischi** espandere la sezione **Avanzate** e impostare l'opzione **Usa dischi gestiti** su **No**.
8. Fornire gli altri dettagli necessari per creare la macchina virtuale.
9. Selezionare **Revisione : crea** per rivedere le scelte effettuate. Quando viene visualizzato il messaggio **Convalida superata**, selezionare **Crea**.

Azure avvia il provisioning della macchina virtuale specificata. È possibile tenere traccia dello stato di avanzamento selezionando la scheda **Macchine virtuali** a sinistra. Una volta creato, lo stato cambierà in **In esecuzione**.

Se si riscontrano difficoltà nella creazione del nuovo disco rigido virtuale basato su Azure, vedere [Problemi comuni durante](https://docs.microsoft.com/azure/marketplace/partner-center-portal/common-issues-during-vhd-creation)la creazione di un disco rigido virtuale .

### <a name="connect-to-your-azure-vm"></a>Connettersi alla macchina virtuale di Azure

Questa sezione illustra come connettersi e accedere alla macchina virtuale creata in Azure.This section explains how to connect to and sign into the VM you created on Azure. Dopo aver eseguito correttamente la connessione, è possibile usare la macchina virtuale come se si fosse connessi localmente al relativo server host.

#### <a name="connect-to-a-windows-based-vm"></a>Connettersi a una macchina virtuale basata su Windows

Usare il client desktop remoto per connettersi alla macchina virtuale basata su Windows ospitata in Azure.Use the remote desktop client to connect to the Windows-based VM hosted on Azure. La maggior parte delle versioni di Windows in modalità nativa dispone del supporto per il protocollo RDP (Remote Desktop Protocol). Per altri sistemi operativi, è possibile trovare ulteriori informazioni sui client nei [client Desktop remoto](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).

Questo articolo descrive in dettaglio come usare il supporto predefinito di Windows RDP per connettersi alla macchina virtuale: [Come connettersi e accedere a una macchina virtuale di Azure che esegue Windows.](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon)

> [!TIP]
> Durante il processo potrebbero essere generati avvisi di sicurezza. Ad esempio, avvisi come "Il file con estensione rdp proviene da un autore sconosciuto" o "Impossibile verificare le credenziali utente". È opportuno ignorare questi avvisi.

#### <a name="connect-to-a-linux-based-vm"></a>Connettersi a una macchina virtuale basata su Linux

Per connettersi a una macchina virtuale basata su Linux, è necessario un client SSH (Secure Shell Protocol). I seguenti passaggi utilizzano il terminale [PuTTY](https://www.ssh.com/ssh/putty/) SHH gratuito.

1. Accedere al [portale di Azure](https://ms.portal.azure.com/).
2. Cercare e selezionare **Macchine virtuali**.
3. Selezionare la macchina virtuale a cui connettersi.
4. Avviare la macchina virtuale se non è già in esecuzione.
5. Selezionare il nome della macchina virtuale per aprire la relativa pagina **Panoramica.Select** the name of the VM to open its Overview page.
6. Si noti l'indirizzo IP pubblico e il nome DNS della macchina virtuale (se questi valori non sono impostati, è necessario [creare un'interfaccia](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface)di rete ).
7. Aprire l'applicazione PuTTY.
8. Nella finestra di dialogo per la configurazione di PuTTY immettere l'indirizzo IP e il nome DNS della macchina virtuale.

    :::image type="content" source="media/avm-putty.png" alt-text="Illustra le impostazioni del terminale PuTTY. vengono evidenziate le caselle Nome host o Indirizzo IP e Porta.":::

9. Selezionare **Apri** per aprire un terminale PuTTY.
10. Quando richiesto, immettere il nome account e la password dell'account VM Linux.

In caso di problemi di connessione, fare riferimento alla documentazione del client SSH. Ad esempio, [Capitolo 10: Messaggi](https://www.ssh.com/ssh/putty/putty-manuals)di errore comuni .

Per informazioni dettagliate, incluso come aggiungere un desktop a una macchina virtuale Linux di cui è stato eseguito il provisioning, vedere [Installare e configurare Desktop remoto per la connessione a una macchina virtuale Linux in Azure.For](https://docs.microsoft.com/azure/virtual-machines/linux/use-remote-desktop)details, including how to add a desktop to a provisioned Linux VM, see Install and configure Remote Desktop to connect to a Linux VM in Azure.

## <a name="create-a-vm-using-your-own-image"></a>Creare una macchina virtuale usando un'immagine personalizzataCreate a VM using your own image

In questa sezione viene descritto come creare e distribuire un'immagine di macchina virtuale (VM) fornita dall'utente. A tale scopo, è possibile fornire immagini del sistema operativo e del disco dati da un disco rigido virtuale (VHD) distribuito da Azure.

> [!NOTE]
> Per usare facoltativamente un'immagine di base approvata, seguire le istruzioni in [Creare un'immagine VM usando una base approvata.](#create-a-vm-image-using-an-approved-base)

1. Caricare le immagini nell'account di archiviazione di Azure.Upload your images to Azure Storage account.
2. Distribuire l'immagine della macchina virtuale.
3. Acquisire l'immagine della macchina virtuale.

### <a name="upload-your-images-to-an-azure-storage-account"></a>Caricare le immagini in un account di archiviazione di AzureUpload your images to an Azure storage account

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Caricare il disco rigido virtuale e il disco rigido virtuale del sistema operativo generalizzati nell'account di archiviazione di Azure.Upload your generalized operating system VHD and data disk VHDs to your Azure storage account.

### <a name="deploy-your-image"></a>Distribuire l'immagine

Creare l'immagine usando il portale di Azure o Azure PowerShell.Create your image using either the Azure portal or Azure PowerShell.

#### <a name="deploy-using-the-azure-portal"></a>Distribuire tramite il portale di Azure

1. Nella home page selezionare **Crea una risorsa**, cercare "Distribuzione modello" e quindi **Selezionare Crea**.
2. Scegliere **Crea modello personalizzato nell'editor.**

    :::image type="content" source="media/avm-custom-deployment.png" alt-text="Illustra la pagina Distribuzione personalizzata.":::

3. Incollare questo [modello JSON](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-deploy-json-template) nell'editor e selezionare **Salva**.
4. Specificare i valori dei parametri per le pagine delle proprietà di **Distribuzione personalizzata**.

    | Parametro | Descrizione |
    | ------------ | ------------- |
    | Nome account di archiviazione utente | Contenuto della cella 2 |
    | Nome contenitore di archiviazione utente | Nome dell'account di archiviazione in cui si trova il disco rigido virtuale generalizzato |
    | Nome DNS per indirizzo IP pubblico | Nome DNS IP pubblico. Definire il nome DNS per l'indirizzo IP pubblico nel portale di Azure dopo la distribuzione dell'offerta. |
    | Nome utente amministratore | Nome utente dell'account di amministratore per la nuova macchina virtuale |
    | Password amministratore | Password dell'account di amministratore per la nuova macchina virtuale |
    | Tipo di sistema operativo | Sistema operativo VM: Windows o Linux |
    | ID sottoscrizione | Identificatore della sottoscrizione selezionata |
    | Location | Località geografica della distribuzione |
    | Dimensioni macchina virtuale | [Dimensioni della macchina virtuale di Azure,](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)ad esempio Standard_A2 |
    | Nome indirizzo IP pubblico | Nome dell'indirizzo IP pubblico dell'utente |
    | Nome macchina virtuale | Nome della nuova macchina virtuale |
    | Nome della rete virtuale | Nome della rete virtuale usata dalla macchina virtuale |
    | Nome NIC | Nome della scheda di rete in esecuzione nella rete virtuale |
    | URL VHD | URL del disco rigido virtuale del sistema operativo completo |
    |  |  |

5. Dopo aver fornito questi valori, selezionare **Acquista**.

Azure inizierà la distribuzione. Crea una nuova macchina virtuale con il disco rigido virtuale non gestito specificato nel percorso dell'account di archiviazione specificato. È possibile tenere traccia dello stato di avanzamento nel portale di Azure selezionando **Macchine virtuali** sul lato sinistro del portale. Quando viene creata la macchina virtuale, lo stato cambierà da Avvio a In esecuzione.

#### <a name="deploy-using-azure-powershell"></a>Eseguire la distribuzione tramite Azure PowerShell

```powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```

### <a name="capture-the-vm-image"></a>Acquisire l'immagine della macchina virtuale

Utilizzare le seguenti istruzioni che corrispondono al proprio approccio:Use the following instructions that correspond to your approach:

* Azure PowerShell: [Come creare un'immagine di macchina virtuale non gestita da una macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource) di AzureAzure PowerShell: How to create an unmanaged VM image from an Azure VM
* Interfaccia della riga di comando di Azure: [Come creare un'immagine di una macchina virtuale o un disco rigido virtuale](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)
* API: [Macchine virtuali - Acquisizione](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)

## <a name="configure-the-virtual-machine"></a>Configurare la macchina virtuale

Questa sezione descrive come ridimensionare, aggiornare e generalizzare una macchina virtuale di Azure.This section describes how to size, update, and generalize an Azure VM. Questi passaggi sono necessari per preparare la macchina virtuale da distribuire in Azure Marketplace.These steps are necessary to prepare your VM to be deployed on Azure Marketplace.

### <a name="sizing-the-vhds"></a>Ridimensionamento dei dischi rigidi virtuali

Se è stata selezionata una delle macchine virtuali preconfigurata con un sistema operativo (e facoltativamente servizi aggiuntivi), è già stata scelta una dimensione standard della macchina virtuale di Azure.If you selected one of the VMs pre-configured with an operating system (and optionally additional services), you have already picked a standard Azure VM size. Avviare una soluzione con un sistema operativo preconfigurato è l'approccio consigliato. Tuttavia, se si installa un sistema operativo manualmente, è necessario ridimensionare il disco rigido virtuale primario nell'immagine della macchina virtuale:However, if you are installing an OS manually, you must size your primary VHD in your VM image:

* Per Windows, il disco rigido virtuale del sistema operativo deve essere creato come un disco rigido virtuale in formato fisso da 127 a 128 GB.
* Per Linux, questo disco rigido virtuale deve essere creato come un disco rigido virtuale di formato fisso da 30 a 50 GB.

Se la dimensione fisica è inferiore a 127–128 GB, il disco rigido virtuale deve essere espandibile (sparse/dinamico). Le immagini di base di Windows e SQL Server fornite soddisfano già questi requisiti, pertanto non modificare il formato o le dimensioni del disco rigido virtuale.

I dischi dati possono avere dimensioni fino a 1 TB. Quando si decide le dimensioni, tenere presente che i clienti non possono ridimensionare i dischi rigidi virtuali all'interno di un'immagine al momento della distribuzione. I dischi rigidi virtuali per i dischi dati devono essere creati come dischi rigidi virtuali con formato fisso, Devono inoltre essere espandibili (sparse/dinamici). I dischi dati inizialmente possono essere vuoti o contenere dati.

### <a name="install-the-most-current-updates"></a>Installare gli aggiornamenti più recenti

Le immagini di base delle macchine virtuali del sistema operativo devono contenere gli aggiornamenti più recenti fino alla data di pubblicazione. Prima di pubblicare il disco rigido virtuale del sistema operativo creato, assicurarsi di aggiornare il sistema operativo e tutti i servizi installati con tutte le patch di sicurezza e manutenzione più recenti.

Per Windows Server, eseguire il comando **Controlla aggiornamenti.**

Per le distribuzioni Linux, gli aggiornamenti vengono in genere scaricati e installati tramite uno strumento da riga di comando o un'utilità grafica. Ubuntu Linux, ad esempio, per l'aggiornamento del sistema operativo mette a disposizione il comando [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) e lo strumento [Update Manager](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html).

### <a name="perform-additional-security-checks"></a>Eseguire controlli di sicurezza aggiuntivi

Mantenere un livello elevato di sicurezza per le immagini della soluzione in Azure Marketplace.Maintain a high level of security for your solution images in the Azure Marketplace. L'articolo seguente fornisce un elenco di controllo delle configurazioni e delle procedure di sicurezza per facilitare l'utente: [Security Recommendations for Azure Marketplace Images](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images). Alcune di queste indicazioni sono destinate specificamente a immagini basate su Linux, ma la maggior parte si applica a qualsiasi immagine di macchina virtuale.

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Eseguire una configurazione personalizzata e attività pianificate

Se è necessaria una configurazione aggiuntiva, usare un'attività pianificata che viene eseguita all'avvio per apportare eventuali modifiche finali alla macchina virtuale dopo la distribuzione. Tenere anche conto dei consigli seguenti:

* Se si tratta di un'attività eseguita una sola volta, l'attività deve essere eliminata dopo il completamento.
* Le configurazioni non devono basarsi su unità diverse da C o D, perché solo queste due unità sono sempre garantite per esistere (unità C è il disco del sistema operativo e l'unità D è il disco locale temporaneo).

Per altre informazioni sulle personalizzazioni di Linux, vedere [Estensioni della macchina virtuale e funzionalità per Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux).

## <a name="generalize-the-image"></a>Generalizzare l'immagine

Tutte le immagini in Azure Marketplace devono poter essere riutilizzate in modo generico. A tale scopo, il disco rigido virtuale del sistema operativo deve essere generalizzato, un'operazione che rimuove tutti gli identificatori specifici dell'istanza e i driver software da una macchina virtuale.

### <a name="windows"></a>WINDOWS

La generalizzazione dei dischi con sistema operativo Windows viene eseguita con lo [strumento sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview). Se in seguito si aggiorna o si riconfigura il sistema operativo, è necessario eseguire nuovamente sysprep.

> [!WARNING]
> Poiché gli aggiornamenti possono essere eseguiti automaticamente, dopo aver eseguito sysprep, disattivare la macchina virtuale fino alla relativa distribuzione. Questo arresto eviterà che gli aggiornamenti successivi apportino modifiche specifiche dell'istanza al sistema operativo o ai servizi installati. Per ulteriori informazioni sull'esecuzione di sysprep, vedere [Passaggi per generalizzare un disco rigido virtuale](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep).

### <a name="linux"></a>Linux

Il processo seguente generalizza una macchina virtuale Linux e la ridistribuisce come macchina virtuale separata. Per informazioni dettagliate, vedere [Come creare un'immagine di una macchina virtuale o di un disco rigido virtuale](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image). È possibile interrompere quando si raggiunge la sezione "Creare una macchina virtuale dall'immagine acquisita".

1. **Rimuovere l'agente Linux di Azure**

    1. Connettersi alla VM Linux tramite un client SSH.
    2. Nella finestra SSH, immettere `sudo waagent -deprovision+user`il seguente comando: .
    3. Digitare **Y** per continuare (è possibile aggiungere il parametro **-force** al comando precedente per evitare il passaggio di conferma).
    d. Al termine del comando, digitare **Exit** per chiudere il client SSH.

2. **Arrestare la macchina virtuale**

    1. Nel portale di Azure selezionare il gruppo di risorse (RG) e disallocare la macchina virtuale.
    2. Il disco rigido virtuale è ora generalizzato ed è possibile creare una nuova macchina virtuale usando questo disco rigido virtuale.

## <a name="next-steps"></a>Passaggi successivi

Se si verificano problemi durante la creazione del nuovo disco rigido virtuale basato su Azure, vedere [Common issues during VHD creation](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-vhd-creation-issues) (Problemi comuni durante la creazione di un disco rigido virtuale).

In caso contrario:

* [Certificare l'immagine della macchina virtuale](https://docs.microsoft.com/azure/marketplace/partner-center-portal/get-sas-uri) spiega come testare e inviare un'immagine della macchina virtuale per la certificazione di Azure Marketplace, inclusa dove ottenere lo strumento di test di *certificazione per lo* strumento certificato di Azure e come usarlo per certificare l'immagine della macchina virtuale.
