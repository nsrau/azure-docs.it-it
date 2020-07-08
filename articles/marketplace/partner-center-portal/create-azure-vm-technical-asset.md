---
title: Creare risorse tecniche della macchina virtuale di Azure
description: Informazioni su come creare e configurare le risorse tecniche necessarie per un'offerta di macchina virtuale (VM) per Azure Marketplace.
author: dannyevers
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: d43015c86976594e8d5077a11cbdad27668b343c
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2020
ms.locfileid: "85957700"
---
# <a name="create-your-azure-virtual-machine-technical-assets"></a>Creare risorse tecniche della macchina virtuale di Azure

L'articolo descrive come creare e configurare le risorse tecniche necessarie per un'offerta di macchina virtuale (VM) per Azure Marketplace. Una macchina virtuale è costituita da due componenti: il disco rigido virtuale (VHD, Virtual Hard Disk) del sistema operativo e dischi rigidi virtuali facoltativi dei dischi dati associati:

* **Disco rigido virtuale del sistema operativo**: contiene il sistema operativo e la soluzione distribuiti con l'offerta. Il processo di preparazione del disco rigido virtuale è diverso a seconda che si tratti di una macchina virtuale basata su Linux, su Windows o personalizzata.
* **Dischi rigidi virtuali dei dischi dati**: archivio permanente e dedicato per una macchina virtuale. Non usare il disco rigido virtuale del sistema operativo (ad esempio, l'unità C:) per archiviare informazioni permanenti.

Un'immagine di macchina virtuale contiene un disco del sistema operativo e fino a 16 dischi dati. Usare un disco rigido virtuale per ogni disco dati, anche se il disco è vuoto.

> [!NOTE]
> Indipendentemente dal sistema operativo usato, aggiungere solo il numero minimo di dischi dati necessari per la soluzione. In fase di distribuzione i clienti non possono rimuovere dischi che fanno parte di un'immagine ma possono sempre aggiungerne altri durante o dopo la distribuzione.

> [!IMPORTANT]
> Ogni immagine di macchina virtuale in un piano deve avere lo stesso numero di dischi dati.

## <a name="fundamental-technical-knowledge"></a>Conoscenze tecniche fondamentali

La progettazione, la compilazione e il test di queste risorse richiedono tempo e conoscenze tecniche sia della piattaforma di Azure che delle tecnologie usate per creare l'offerta. Oltre alle conoscenze relative al dominio della soluzione, il team tecnico deve avere il livello di conoscenza seguente delle tecnologie Microsoft:

* Conoscenza di base di [Servizi di Azure](https://azure.microsoft.com/services/)
* Capacità di [progettare applicazioni di Azure per architetture diverse](https://azure.microsoft.com/solutions/architecture/)
* Conoscenza pratica di [Macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/), [Archiviazione di Azure](https://azure.microsoft.com/services/?filter=storage) e [Rete di Azure](https://azure.microsoft.com/services/?filter=networking)
* Conoscenza pratica di [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
* Conoscenza pratica di [JSON](https://www.json.org/)

## <a name="suggested-tools--optional"></a>Strumenti suggeriti, facoltativo

Per la gestione di macchine virtuali e dischi rigidi virtuali, prendere in considerazione l'uso di uno degli ambienti di scripting seguenti:

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Interfaccia della riga di comando di Azure](https://code.visualstudio.com/)

Inoltre, è consigliabile aggiungere gli strumenti seguenti all'ambiente di sviluppo in uso:

* [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Visual Studio Code](https://code.visualstudio.com/)
  * Estensione: [Strumenti di Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  * Estensione: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  * Estensione: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Rivedere gli strumenti disponibili nella pagina [Strumenti di sviluppo di Azure](https://azure.microsoft.com/product-categories/developer-tools/) e, se si usa Visual Studio, in [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-a-vm-image-using-an-approved-base"></a>Creare un'immagine di macchina virtuale usando una base approvata

> [!NOTE]
> Per creare le risorse tecniche della macchina virtuale usando un'immagine creata in locale, passare a [Creare una macchina virtuale usando l'immagine](#create-a-vm-using-your-own-image).

Questa sezione descrive vari aspetti relativi all'uso di una base approvata, ad esempio l'uso di Remote Desktop Protocol (RDP), la selezione di una dimensione per la macchina virtuale, l'installazione degli aggiornamenti di Windows più recenti e la generalizzazione dell'immagine del disco rigido virtuale.

Le sezioni seguenti si concentrano principalmente sui dischi rigidi virtuali basati su Windows. Per altre informazioni sulla creazione di dischi rigidi virtuali basati su Linux, vedere [Linux in distribuzioni approvate da Azure](../../virtual-machines/linux/endorsed-distros.md).

> [!WARNING]
> Seguire le indicazioni presenti in questo argomento per usare Azure per la creazione di una macchina virtuale che contiene un sistema operativo pre-configurato e approvato. Se tali indicazioni non sono compatibili con la soluzione in uso, è possibile creare e configurare una macchina virtuale in locale con un sistema operativo approvato. È quindi possibile configurare e prepararla per il caricamento, come descritto in [Preparare un disco rigido virtuale Windows o VHDX prima del caricamento in Azure](../../virtual-machines/windows/prepare-for-upload-vhd-image.md).

### <a name="select-an-approved-base"></a>Selezionare una base approvata

Selezionare il sistema operativo Windows o Linux come base.

#### <a name="windows"></a>Windows

Il disco rigido virtuale del sistema operativo per l'immagine di macchina virtuale basata su Windows deve essere basato su un'immagine di base approvata per Azure contenente Windows Server o SQL Server. Per iniziare, creare una macchina virtuale da una delle immagini seguenti, dal portale di Azure:

* Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016), [2012 R2 Datacenter](https://www.microsoft.com/cloud-platform/windows-server-pricing), [2012 Datacenter](https://www.microsoft.com/cloud-platform/windows-server-pricing), [2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
* [SQL Server 2014](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance.md) (Enterprise, Standard, Web)
* [SQL Server 2012 SP2](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance.md) (Enterprise, Standard, Web)

> [!NOTE]
> Se si usa l'attuale portale di Azure o Azure PowerShell, allora sono approvate le immagini di Windows Server pubblicate a partire dall'8 settembre 2014.

#### <a name="linux"></a>Linux

Azure offre una gamma di distribuzioni Linux approvate. Per un elenco aggiornato, vedere [Linux in distribuzioni approvate da Azure](../../virtual-machines/linux/endorsed-distros.md).

### <a name="create-vm-in-the-azure-portal"></a>Creare una macchina virtuale nel portale di Azure

Seguire questa procedura per creare l'immagine di base della macchina virtuale nel [portale di Azure](https://ms.portal.azure.com/):

1. Accedere al [portale di Azure](https://ms.portal.azure.com/) con l'account Microsoft associato alla sottoscrizione di Azure che si intende usare per pubblicare l'offerta di macchina virtuale.
2. Creare un nuovo gruppo di risorse e specificare i valori per **Nome del gruppo di risorse**, **Sottoscrizione** e **Località del gruppo di risorse**. Per informazioni dettagliate, vedere [Gestire le risorse](../../azure-resource-manager/resource-group-portal.md).
3. Selezionare **Macchine virtuali** a sinistra per visualizzare la pagina dei dettagli delle macchine virtuali.
4. Selezionare **+ Aggiungi** per aprire **Create a virtual machine experience (Crea un'esperienza di macchina virtuale)** .
5. Selezionare l'immagine nell'elenco a discesa o fare clic su **Esplora tutte le immagini pubbliche e private** per cercare o sfogliare tutte le immagini di macchine virtuali disponibili.
6. Selezionare le dimensioni della macchina virtuale per eseguire la distribuzione seguendo queste indicazioni:
    * Se si prevede di sviluppare il disco rigido virtuale in locale, la dimensione non è rilevante. Valutare l'opportunità di usare una delle macchine virtuali di dimensioni minori.
    * Se si prevede di sviluppare l'immagine in Azure, valutare l'opportunità di usare una delle dimensioni di macchina virtuale consigliate per l'immagine selezionata.

7. In **Dischi** espandere la sezione **Avanzata** e impostare l'opzione **Usa dischi gestiti** su **No**.
8. Indicare gli altri dettagli necessari per creare la macchina virtuale.
9. Selezionare **Review + create (Revisione e creazione)** per rivedere le scelte effettuate. Quando viene visualizzato il messaggio **Convalida superata**, selezionare **Crea**.

Azure avvia il provisioning della macchina virtuale specificata. È possibile tenere traccia dello stato di avanzamento selezionando la scheda **Macchine virtuali** a sinistra. Al termine della creazione, lo stato viene modificato in **In esecuzione**.

Se si verificano problemi durante la creazione del nuovo disco rigido virtuale basato su Azure, vedere [Common issues during VHD creation (FAQs)](common-issues-during-vhd-creation.md) (Problemi comuni durante la creazione di un disco rigido virtuale, domande frequenti).

### <a name="connect-to-your-azure-vm"></a>Connettersi alla macchina virtuale di Azure

Questa sezione illustra come connettersi e accedere alla macchina virtuale creata in Azure. Dopo aver eseguito correttamente la connessione, è possibile usare la macchina virtuale come se si fosse connessi in locale al server host.

#### <a name="connect-to-a-windows-based-vm"></a>Connettersi a una macchina virtuale basata su Windows

Usare il client per desktop remoto per connettersi alla macchina virtuale basata su Windows ospitata in Azure. La maggior parte delle versioni di Windows in modalità nativa dispone del supporto per il protocollo RDP (Remote Desktop Protocol). Per sistemi operativi diversi, altre informazioni sui client sono disponibili in [Client Desktop remoto](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).

L'articolo illustra come usare il supporto di Windows RDP incorporato per connettersi alla macchina virtuale: [Come connettersi e accedere a una macchina virtuale di Azure che esegue Windows](../../virtual-machines/windows/connect-logon.md).

> [!TIP]
> Durante il processo è possibile che vengano visualizzati avvisi di sicurezza. Ad esempio, "The .rdp file is from an unknown publisher (Il file con estensione rdp proviene da un autore sconosciuto)" o "Your user credentials cannot be verified. (Impossibile verificare le credenziali utente)". È opportuno ignorare questi avvisi.

#### <a name="connect-to-a-linux-based-vm"></a>Connettersi a una macchina virtuale basata su Linux

Per connettersi a una macchina virtuale basata su Linux, è necessario disporre di un client con protocollo Secure Shell (SSH). I passaggi seguenti usano il terminale SHH [PuTTY](https://www.ssh.com/ssh/putty/) gratuito.

1. Accedere al [portale di Azure](https://ms.portal.azure.com/).
2. Cercare e selezionare **Macchine virtuali**.
3. Selezionare la macchina virtuale a cui ci si vuole connettere.
4. Avviare la macchina virtuale se è già in esecuzione.
5. Selezionare il nome della macchina virtuale da aprire nella pagina **Panoramica**.
6. Annotare l'indirizzo IP pubblico e il nome DNS della macchina virtuale (se questi valori non sono impostati, è necessario [creare un'interfaccia di rete](../../virtual-network/virtual-network-network-interface.md#create-a-network-interface)).
7. Aprire l'applicazione PuTTY.
8. Nella finestra di dialogo per la configurazione di PuTTY immettere l'indirizzo IP e il nome DNS della macchina virtuale.

    :::image type="content" source="media/avm-putty.png" alt-text="Illustra le impostazioni del terminale PuTTY. Il nome host o l'indirizzo IP e le caselle porta sono evidenziati.":::

9. Selezionare **Apri** per aprire un terminale PuTTY.
10. Quando richiesto, immettere il nome account e la password dell'account di macchina virtuale di Linux.

In caso di problemi di connessione, fare riferimento alla documentazione per il client SSH. Ad esempio, [Capitolo 10: Messaggi di errore comuni](https://www.ssh.com/ssh/putty/putty-manuals).

Per informazioni dettagliate, ad esempio sul modo in cui aggiungere un desktop a una macchina virtuale Linux di cui è stato eseguito il provisioning, vedere [Installare e configurare Desktop remoto per connettersi a una macchina virtuale Linux di Azure](../../virtual-machines/linux/use-remote-desktop.md).

## <a name="create-a-vm-using-your-own-image"></a>Creare una macchina virtuale usando un'immagine personalizzata

Questa sezione descrive come creare e distribuire un'immagine di macchina virtuale (VM) fornita dall'utente. A tale scopo, è possibile fornire immagini del disco rigido virtuale del disco dati e del sistema operativo da un disco rigido virtuale (VHD) distribuito da Azure.

> [!NOTE]
> Per usare facoltativamente un'immagine di base approvata, seguire le istruzioni riportate in [Creare un'immagine di macchina virtuale usando una base approvata](#create-a-vm-image-using-an-approved-base).

1. Caricare le immagini nell'account di archiviazione di Azure.
2. Distribuire l'immagine della macchina virtuale.
3. Acquisire l'immagine della macchina virtuale.

### <a name="upload-your-images-to-an-azure-storage-account"></a>Caricare le immagini in un account di archiviazione di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Caricare il disco rigido virtuale del sistema operativo generalizzato e i dischi rigidi virtuali del disco dati nell'account di archiviazione di Azure.

### <a name="deploy-your-image"></a>Distribuire l'immagine

Creare l'immagine usando il portale di Azure o Azure PowerShell.

#### <a name="deploy-using-the-azure-portal"></a>Distribuire tramite il portale di Azure

1. Nella home page selezionare **Crea una risorsa**, cercare "Distribuzione modelli" e selezionare **Crea**.
2. Scegliere **Creare un modello personalizzato nell'editor**.

    :::image type="content" source="media/avm-custom-deployment.png" alt-text="Illustra la pagina di distribuzione personalizzata.":::

3. Incollare questo [modello JSON](../partner-center-portal/azure-vm-image-certification.md) nell'editor e quindi selezionare **Salva**.
4. Specificare i valori dei parametri per le pagine delle proprietà di **Distribuzione personalizzata**.

    | Parametro | Descrizione |
    | ------------ | ------------- |
    | Nome account di archiviazione utente | Contenuto della cella 2 |
    | Nome contenitore di archiviazione utente | Nome dell'account di archiviazione in cui si trova il disco rigido virtuale generalizzato |
    | Nome DNS per indirizzo IP pubblico | Nome DNS dell'indirizzo IP pubblico. Definire il nome DNS per l'indirizzo IP pubblico nel portale di Azure dopo la distribuzione dell'offerta. |
    | Nome utente amministratore | Nome utente dell'account di amministratore per la nuova macchina virtuale |
    | Password amministratore | Password dell'account di amministratore per la nuova macchina virtuale |
    | Tipo di sistema operativo | Sistema operativo della macchina virtuale: Windows o Linux |
    | ID sottoscrizione | Identificatore della sottoscrizione selezionata |
    | Location | Località geografica della distribuzione |
    | Dimensioni macchina virtuale | [Dimensioni della macchina virtuale di Azure](../../virtual-machines/windows/sizes.md), ad esempio Standard_A2 |
    | Nome indirizzo IP pubblico | Nome dell'indirizzo IP pubblico dell'utente |
    | Nome macchina virtuale | Nome della nuova macchina virtuale |
    | Nome della rete virtuale | Nome della rete virtuale usata dalla macchina virtuale |
    | Nome NIC | Nome della scheda di rete in esecuzione nella rete virtuale |
    | URL VHD | URL del disco rigido virtuale del sistema operativo completo |
    |  |  |

5. Dopo aver immesso questi valori, selezionare **Acquisto**.

Azure inizierà la distribuzione. Crea una nuova macchina virtuale con il disco rigido virtuale non gestito specificato nel percorso di account di archiviazione indicato. È possibile monitorare lo stato nel portale di Azure selezionando **Macchine virtuali** nel lato sinistro del portale. Dopo che la macchina virtuale è stata creata, lo stato verrà modificato da Avvio in corso a In esecuzione.

#### <a name="deploy-using-azure-powershell"></a>Eseguire la distribuzione tramite Azure PowerShell

```powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```

### <a name="capture-the-vm-image"></a>Acquisire l'immagine della macchina virtuale

Usare le istruzioni seguenti che corrispondono all'approccio:

* Azure PowerShell: [Come creare un'immagine di macchina virtuale non gestita da una macchina virtuale di Azure](../../virtual-machines/windows/capture-image-resource.md)
* Interfaccia della riga di comando di Azure: [Come creare un'immagine di una macchina virtuale o un disco rigido virtuale](../../virtual-machines/linux/capture-image.md)
* API: [Virtual Machines - Capture](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture) (Macchine virtuali - Acquisizione)

## <a name="configure-the-virtual-machine"></a>Configurare la macchina virtuale

Questa sezione descrive come ridimensionare, aggiornare e generalizzare una macchina virtuale di Azure. Questa procedura è necessaria per preparare la macchina virtuale alla distribuzione in Azure Marketplace.

### <a name="sizing-the-vhds"></a>Ridimensionamento dei dischi rigidi virtuali

Se è stata selezionata una delle macchine virtuali preconfigurate con un sistema operativo (e, facoltativamente, se sono stati selezionati altri servizi), è già stata scelta una dimensione di macchina virtuale di Azure standard. Avviare una soluzione con un sistema operativo preconfigurato è l'approccio consigliato. Se tuttavia si intende installare il sistema operativo manualmente, è necessario ridimensionare il disco rigido virtuale principale nell'immagine di macchina virtuale:

* Per Windows, il disco rigido virtuale del sistema operativo deve essere creato con formato fisso da 127-128 GB.
* Per Linux, questo disco rigido virtuale deve essere creato con formato fisso da 30-50 GB.

Se la dimensione fisica è inferiore a 127-128 GB, il disco rigido virtuale deve essere espandibile (sparse/dinamico). Le immagini Windows e SQL Server di base fornite soddisfano già questi requisiti, quindi non modificare il formato o la dimensione del disco rigido virtuale.

I dischi dati possono avere dimensioni fino a 1 TB. Nello stabilire le dimensioni, tenere presente che i clienti non possono ridimensionare i dischi rigidi virtuali all'interno di un'immagine in fase di distribuzione. I dischi rigidi virtuali per i dischi dati devono essere creati come dischi rigidi virtuali con formato fisso, Devono inoltre essere espandibili (sparse/dinamiche). I dischi dati inizialmente possono essere vuoti o contenere dati.

### <a name="install-the-most-current-updates"></a>Installare gli aggiornamenti più recenti

Le immagini di base delle macchine virtuali del sistema operativo devono contenere gli aggiornamenti più recenti fino alla data di pubblicazione. Prima di pubblicare il disco rigido virtuale del sistema operativo creato, assicurarsi di aggiornare il sistema operativo e tutti i servizi installati con tutte le patch di sicurezza e manutenzione più recenti.

Per Windows Server, eseguire il comando **Controlla aggiornamenti**.

Per le distribuzioni Linux, gli aggiornamenti vengono in genere scaricati e installati tramite uno strumento da riga di comando o un'utilità grafica. Ubuntu Linux, ad esempio, per l'aggiornamento del sistema operativo mette a disposizione il comando [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) e lo strumento [Update Manager](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html).

### <a name="perform-additional-security-checks"></a>Eseguire controlli di sicurezza aggiuntivi

Mantenere un livello di sicurezza elevato per le immagini delle soluzioni in Azure Marketplace. L'articolo seguente offre un elenco di controllo con configurazioni e procedure di sicurezza a cui attenersi: [Raccomandazioni sulla sicurezza per le immagini di Azure Marketplace](../../security/security-recommendations-azure-marketplace-images.md). Alcune di queste indicazioni sono destinate specificamente a immagini basate su Linux, ma la maggior parte si applica a qualsiasi immagine di macchina virtuale.

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Eseguire una configurazione personalizzata e attività pianificate

Se si rendono necessarie configurazioni aggiuntive, usare un'attività pianificata da eseguire all'avvio per apportare eventuali modifiche finali alla macchina virtuale dopo la distribuzione. Tenere anche conto dei consigli seguenti:

* Per le attività da eseguire una sola volta, l'attività dovrebbe eliminarsi automaticamente dopo il completamento.
* Le configurazioni non devono basarsi su unità diverse da C o D, perché solo queste due unità sono sempre disponibili (l'unità C è il disco del sistema operativo e l'unità D è il disco locale temporaneo).

Per altre informazioni sulle personalizzazioni di Linux, vedere [Estensioni della macchina virtuale e funzionalità per Linux](../../virtual-machines/extensions/features-linux.md).

## <a name="generalize-the-image"></a>Generalizzare l'immagine

Tutte le immagini in Azure Marketplace devono poter essere riutilizzate in modo generico. Per ottenere questo risultato, il disco rigido virtuale del sistema operativo deve essere generalizzato. La generalizzazione è un'operazione che rimuove tutti gli identificatori specifici di istanze e tutti i driver software da una macchina virtuale.

### <a name="windows"></a>Windows

La generalizzazione dei dischi con sistema operativo Windows viene eseguita con lo [strumento sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview). Se in seguito si aggiorna o si riconfigura il sistema operativo, è necessario eseguire nuovamente sysprep.

> [!WARNING]
> Poiché gli aggiornamenti possono essere eseguiti automaticamente, dopo aver eseguito sysprep, arrestare la macchina virtuale finché non viene distribuita. Questo arresto eviterà che gli aggiornamenti successivi possano apportare modifiche specifiche di istanza al sistema operativo o ai servizi installati. Per ulteriori informazioni sull'esecuzione di Sysprep, vedere la pagina relativa [alla procedura per generalizzare un disco rigido virtuale](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep).

### <a name="linux"></a>Linux

Il processo seguente generalizza una macchina virtuale Linux e la ridistribuisce come macchina virtuale separata. Per informazioni dettagliate, vedere [Come creare un'immagine di una macchina virtuale o un disco rigido virtuale](../../virtual-machines/linux/capture-image.md). Arrivare fino alla sezione "Creare una macchina virtuale dall'immagine acquisita".

1. **Rimuovere l'agente Linux di Azure**

    1. Connettersi alla VM Linux tramite un client SSH.
    2. Nella finestra SSH digitare il comando seguente: `sudo waagent -deprovision+user`.
    3. Digitare **Y** per continuare (è possibile aggiungere il parametro **-force** al comando precedente per evitare il passaggio di conferma).
    d. Al termine dell'esecuzione del comando, digitare **Exit (Esci)** per chiudere il client SSH.

2. **Arrestare la macchina virtuale**

    1. Nel portale di Azure selezionare il gruppo di risorse (RG) e deallocare la macchina virtuale.
    2. Il disco rigido virtuale è ora generalizzato ed è possibile usarlo per creare una nuova macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi

Se si verificano problemi durante la creazione del nuovo disco rigido virtuale basato su Azure, vedere [Common issues during VHD creation](common-issues-during-vhd-creation.md) (Problemi comuni durante la creazione di un disco rigido virtuale).

In caso contrario:

* [Certificare l'immagine di macchina virtuale](get-sas-uri.md) spiega come testare e inviare un'immagine di macchina virtuale per la certificazione di Azure Marketplace, inclusa la posizione in cui ottenere lo *Strumento di test di certificazione per Azure Certified* e come usarlo per certificare l'immagine di macchina virtuale.
