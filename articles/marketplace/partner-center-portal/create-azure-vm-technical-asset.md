---
title: Creare le risorse tecniche della macchina virtuale di Azure
description: Informazioni su come creare e configurare risorse tecniche per un'offerta di macchina virtuale (VM) per Azure Marketplace.
author: dannyevers
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: 4d2d33f9d83132147b5b257ffcd6d659f272b8ec
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81730725"
---
# <a name="create-your-azure-virtual-machine-technical-assets"></a>Creare le risorse tecniche della macchina virtuale di Azure

> [!IMPORTANT]
> Stiamo muovendo la gestione delle offerte di macchine virtuali di Azure dal portale Cloud Partner al centro per i partner. Fino a quando non viene eseguita la migrazione delle offerte, seguire le istruzioni riportate in [creare risorse tecniche per un'offerta di macchina virtuale per portale cloud partner](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-technical-assets) per gestire le offerte.

Questo articolo descrive come creare e configurare risorse tecniche per un'offerta di macchina virtuale (VM) per Azure Marketplace. Una macchina virtuale contiene due componenti: il disco rigido virtuale (VHD) del sistema operativo e i dischi rigidi virtuali dei dischi dati associati facoltativi:

* **VHD del sistema operativo** : contiene il sistema operativo e la soluzione distribuiti con l'offerta. Il processo di preparazione del disco rigido virtuale varia a seconda che si tratti di una macchina virtuale basata su Linux, basata su Windows o personalizzata.
* **Dischi dati VHD** : archiviazione persistente e dedicata per una macchina virtuale. Non usare il VHD del sistema operativo, ad esempio l'unità C:, per archiviare le informazioni persistenti.

Un'immagine di macchina virtuale contiene un disco del sistema operativo e un massimo di 16 dischi dati. Usare un disco rigido virtuale per ogni disco dati, anche se il disco è vuoto.

> [!NOTE]
> Indipendentemente dal sistema operativo utilizzato, aggiungere solo il numero minimo di dischi dati necessari per la soluzione. I clienti non possono rimuovere dischi che fanno parte di un'immagine al momento della distribuzione, ma possono sempre aggiungere dischi durante o dopo la distribuzione.

> [!IMPORTANT]
> Ogni immagine di macchina virtuale in un piano deve avere lo stesso numero di dischi dati.

## <a name="fundamental-technical-knowledge"></a>Conoscenze tecniche fondamentali

La progettazione, la compilazione e il test di questi asset richiedono tempo e richiedono conoscenze tecniche sulla piattaforma Azure e sulle tecnologie usate per creare l'offerta. Oltre al dominio della soluzione, il team di progettazione deve conoscere le tecnologie Microsoft seguenti:

* Conoscenza di base di [Servizi di Azure](https://azure.microsoft.com/services/)
* Capacità di [progettare applicazioni di Azure per architetture diverse](https://azure.microsoft.com/solutions/architecture/)
* Conoscenza pratica di [Macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/), [Archiviazione di Azure](https://azure.microsoft.com/services/?filter=storage) e [Rete di Azure](https://azure.microsoft.com/services/?filter=networking)
* Conoscenza pratica di [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
* Conoscenza pratica di [JSON](https://www.json.org/)

## <a name="suggested-tools--optional"></a>Strumenti suggeriti-facoltativo

Prendere in considerazione l'uso di uno degli ambienti di scripting seguenti per semplificare la gestione di macchine virtuali e VHD:

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Interfaccia della riga di comando di Azure](https://code.visualstudio.com/)

Inoltre, è consigliabile aggiungere gli strumenti seguenti all'ambiente di sviluppo:

* [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Visual Studio Code](https://code.visualstudio.com/)
  * Estensione: [Strumenti di Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  * Estensione: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  * Estensione: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Esaminare gli strumenti disponibili nella pagina [strumenti di sviluppo di Azure](https://azure.microsoft.com/product-categories/developer-tools/) e, se si usa Visual Studio, il [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-a-vm-image-using-an-approved-base"></a>Creare un'immagine di macchina virtuale usando una base approvata

> [!NOTE]
> Per creare le risorse tecniche della macchina virtuale usando un'immagine creata in locale, vedere [creare una macchina virtuale usando un'immagine personalizzata](#create-a-vm-using-your-own-image).

In questa sezione vengono descritti i vari aspetti dell'utilizzo di una base approvata, ad esempio l'utilizzo del Remote Desktop Protocol (RDP), la selezione di una dimensione per la macchina virtuale, l'installazione degli ultimi aggiornamenti di Windows e la generalizzazione dell'immagine del disco rigido virtuale.

Le sezioni seguenti si concentrano principalmente sui VHD basati su Windows. Per altre informazioni sulla creazione di dischi rigidi virtuali basati su Linux, vedere [Linux in distribuzioni approvate da Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).

> [!WARNING]
> Seguire le istruzioni riportate in questo argomento per usare Azure per creare una macchina virtuale contenente un sistema operativo già configurato e approvato. Se questo non è compatibile con la soluzione, è possibile creare e configurare una macchina virtuale locale usando un sistema operativo approvato. È quindi possibile configurare e prepararla per il caricamento, come descritto in [Preparare un disco rigido virtuale Windows o VHDX prima del caricamento in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).

### <a name="select-an-approved-base"></a>Selezionare una base approvata

Selezionare il sistema operativo Windows o Linux come base.

#### <a name="windows"></a>Windows

Il VHD del sistema operativo per l'immagine di macchina virtuale basata su Windows deve essere basato su un'immagine di base approvata da Azure che contiene Windows Server o SQL Server. Per iniziare, creare una macchina virtuale da una delle immagini seguenti dal portale di Azure:

* Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016), [2012 R2 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2012 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
* [SQL Server 2014](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, standard, Web)
* [SQL Server 2012 SP2](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, standard, Web)

> [!NOTE]
> Se si usa la portale di Azure o la Azure PowerShell corrente, le immagini di Windows Server pubblicate a partire dall'8 settembre 2014 e versioni successive verranno approvate.

#### <a name="linux"></a>Linux

Azure offre una gamma di distribuzioni Linux approvate. Per un elenco aggiornato, vedere [Linux in distribuzioni approvate da Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).

### <a name="create-vm-in-the-azure-portal"></a>Creare una macchina virtuale nel portale di Azure

Seguire questa procedura per creare l'immagine di macchina virtuale di base nel [portale di Azure](https://ms.portal.azure.com/):

1. Accedere al [portale di Azure](https://ms.portal.azure.com/) con il account Microsoft associato alla sottoscrizione di Azure che si vuole usare per pubblicare l'offerta di VM.
2. Creare un nuovo gruppo di risorse e specificare i valori per **Nome del gruppo di risorse**, **Sottoscrizione** e **Località del gruppo di risorse**. Per informazioni dettagliate, vedere [gestire le risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).
3. Selezionare **macchine virtuali** a sinistra per visualizzare la pagina dei dettagli delle macchine virtuali.
4. Selezionare **+ Aggiungi** per aprire l' **esperienza di creazione di una macchina virtuale**.
5. Selezionare l'immagine nell'elenco a discesa o fare clic su **Sfoglia tutte le immagini pubbliche e private** per cercare o esplorare tutte le immagini di macchine virtuali disponibili.
6. Selezionare le dimensioni della macchina virtuale per eseguire la distribuzione seguendo queste indicazioni:
    * Se si prevede di sviluppare il VHD in locale, le dimensioni non sono importanti. Valutare l'opportunità di usare una delle macchine virtuali di dimensioni minori.
    * Se si prevede di sviluppare l'immagine in Azure, valutare l'opportunità di usare una delle dimensioni di macchina virtuale consigliate per l'immagine selezionata.

7. Nella sezione **dischi** espandere la sezione **Avanzate** e impostare l'opzione **Usa dischi gestiti** su **No**.
8. Fornire gli altri dettagli necessari per creare la macchina virtuale.
9. Selezionare **Verifica + crea** per rivedere le scelte effettuate. Quando viene visualizzato il messaggio **Convalida superata**, selezionare **Crea**.

Azure inizia il provisioning della macchina virtuale specificata. È possibile tenere traccia dello stato di avanzamento selezionando la scheda **macchine virtuali** a sinistra. Dopo la creazione, lo stato cambierà in **in esecuzione**.

Se si verificano difficoltà nella creazione del nuovo VHD basato su Azure, vedere [problemi comuni durante la creazione del disco rigido virtuale (domande frequenti)](https://docs.microsoft.com/azure/marketplace/partner-center-portal/common-issues-during-vhd-creation).

### <a name="connect-to-your-azure-vm"></a>Connettersi alla macchina virtuale di Azure

Questa sezione illustra come connettersi e accedere alla macchina virtuale creata in Azure. Una volta stabilita la connessione, è possibile usare la macchina virtuale come se si fosse connessi localmente al server host.

#### <a name="connect-to-a-windows-based-vm"></a>Connettersi a una macchina virtuale basata su Windows

Usare il client desktop remoto per connettersi alla macchina virtuale basata su Windows ospitata in Azure. La maggior parte delle versioni di Windows in modalità nativa dispone del supporto per il protocollo RDP (Remote Desktop Protocol). Per altri sistemi operativi, è possibile trovare altre informazioni sui client in [Desktop remoto client](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).

Questo articolo illustra come usare il supporto di Windows RDP incorporato per connettersi alla VM: [come connettersi e accedere a una macchina virtuale di Azure che esegue Windows](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).

> [!TIP]
> Durante il processo è possibile che vengano visualizzati avvisi di sicurezza. Ad esempio, avvisi come "il file con estensione RDP è da un server di pubblicazione sconosciuto" o "Impossibile verificare le credenziali utente". È opportuno ignorare questi avvisi.

#### <a name="connect-to-a-linux-based-vm"></a>Connettersi a una macchina virtuale basata su Linux

Per connettersi a una macchina virtuale basata su Linux, è necessario un client SSH (Secure Shell Protocol). Nei passaggi seguenti viene usato il terminale [Putty](https://www.ssh.com/ssh/putty/) Shh gratuito.

1. Accedere al [portale di Azure](https://ms.portal.azure.com/).
2. Cercare e selezionare **Macchine virtuali**.
3. Selezionare la macchina virtuale a cui ci si vuole connettere.
4. Avviare la macchina virtuale se non è già in esecuzione.
5. Selezionare il nome della macchina virtuale per aprire la relativa pagina di **Panoramica** .
6. Annotare l'indirizzo IP pubblico e il nome DNS della macchina virtuale (se questi valori non sono impostati, è necessario [creare un'interfaccia di rete](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface)).
7. Aprire l'applicazione PuTTY.
8. Nella finestra di dialogo per la configurazione di PuTTY immettere l'indirizzo IP e il nome DNS della macchina virtuale.

    :::image type="content" source="media/avm-putty.png" alt-text="Illustra le impostazioni del terminale PuTTy. il nome host o l'indirizzo IP e le caselle porta sono evidenziati.":::

9. Selezionare **Apri** per aprire un terminale Putty.
10. Quando richiesto, immettere il nome dell'account e la password dell'account VM Linux.

In caso di problemi di connessione, fare riferimento alla documentazione per il client SSH. Ad esempio, [capitolo 10: messaggi di errore comuni](https://www.ssh.com/ssh/putty/putty-manuals).

Per informazioni dettagliate, tra cui come aggiungere un desktop a una VM Linux con provisioning, vedere [installare e configurare Desktop remoto per connettersi a una VM Linux in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/use-remote-desktop).

## <a name="create-a-vm-using-your-own-image"></a>Creare una macchina virtuale usando un'immagine personalizzata

Questa sezione descrive come creare e distribuire un'immagine di macchina virtuale (VM) fornita dall'utente. A tale scopo, è possibile fornire immagini del sistema operativo e del disco rigido virtuale del disco dati da un disco rigido virtuale (VHD) distribuito da Azure.

> [!NOTE]
> Per usare facoltativamente un'immagine di base approvata, seguire le istruzioni riportate in [creare un'immagine di macchina virtuale usando una base approvata](#create-a-vm-image-using-an-approved-base).

1. Caricare le immagini nell'account di archiviazione di Azure.
2. Distribuire l'immagine di macchina virtuale.
3. Acquisire l'immagine della macchina virtuale.

### <a name="upload-your-images-to-an-azure-storage-account"></a>Caricare le immagini in un account di archiviazione di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Caricare il VHD del sistema operativo generalizzato e i dischi rigidi virtuali del disco dati nell'account di archiviazione di Azure.

### <a name="deploy-your-image"></a>Distribuire l'immagine

Creare l'immagine usando il portale di Azure o Azure PowerShell.

#### <a name="deploy-using-the-azure-portal"></a>Distribuire tramite il portale di Azure

1. Nella home page selezionare **Crea una risorsa**, cercare "distribuzione modello" e selezionare **Crea**.
2. Scegliere **Compila modello personalizzato nell'editor**.

    :::image type="content" source="media/avm-custom-deployment.png" alt-text="Viene illustrata la pagina di distribuzione personalizzata.":::

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
    | Percorso | Località geografica della distribuzione |
    | Dimensioni macchina virtuale | [Dimensioni della macchina virtuale di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes), ad esempio Standard_A2 |
    | Nome indirizzo IP pubblico | Nome dell'indirizzo IP pubblico dell'utente |
    | Nome macchina virtuale | Nome della nuova macchina virtuale |
    | Nome della rete virtuale | Nome della rete virtuale usata dalla macchina virtuale |
    | Nome NIC | Nome della scheda di rete in esecuzione nella rete virtuale |
    | URL VHD | URL del disco rigido virtuale del sistema operativo completo |
    |  |  |

5. Dopo aver fornito questi valori, selezionare **Acquista**.

Azure inizierà la distribuzione. Crea una nuova macchina virtuale con il VHD non gestito specificato nel percorso dell'account di archiviazione specificato. È possibile tenere traccia dello stato di avanzamento nel portale di Azure selezionando **macchine virtuali** sul lato sinistro del portale. Quando viene creata la macchina virtuale, lo stato passa da avvio a in esecuzione.

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

* Azure PowerShell: [come creare un'immagine di macchina virtuale non gestita da una macchina virtuale di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)
* Interfaccia della riga di comando di Azure: [Come creare un'immagine di una macchina virtuale o un disco rigido virtuale](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)
* API: [Macchine virtuali - Acquisizione](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)

## <a name="configure-the-virtual-machine"></a>Configurare la macchina virtuale

Questa sezione descrive come ridimensionare, aggiornare e generalizzare una macchina virtuale di Azure. Questi passaggi sono necessari per preparare la macchina virtuale per la distribuzione in Azure Marketplace.

### <a name="sizing-the-vhds"></a>Ridimensionamento dei dischi rigidi virtuali

Se è stata selezionata una delle VM preconfigurate con un sistema operativo e, facoltativamente, altri servizi, è già stata selezionata una dimensione standard per le VM di Azure. Avviare una soluzione con un sistema operativo preconfigurato è l'approccio consigliato. Tuttavia, se si installa un sistema operativo manualmente, è necessario ridimensionare il disco rigido virtuale primario nell'immagine di macchina virtuale:

* Per Windows, il disco rigido virtuale del sistema operativo deve essere creato come VHD in formato fisso da 127 a 128 GB.
* Per Linux, questo disco rigido virtuale deve essere creato come VHD a formato fisso da 30 a 50 GB.

Se la dimensione fisica è inferiore a 127-128 GB, il disco rigido virtuale deve essere espandibile (sparse/dinamico). Le immagini di base di Windows e SQL Server fornite soddisfano già questi requisiti, pertanto non modificare il formato o le dimensioni del disco rigido virtuale.

I dischi dati possono avere dimensioni fino a 1 TB. Quando si decidono le dimensioni, tenere presente che i clienti non possono ridimensionare i VHD all'interno di un'immagine al momento della distribuzione. I dischi rigidi virtuali per i dischi dati devono essere creati come dischi rigidi virtuali con formato fisso, Devono inoltre essere espandibili (sparse/dinamiche). I dischi dati inizialmente possono essere vuoti o contenere dati.

### <a name="install-the-most-current-updates"></a>Installare gli aggiornamenti più recenti

Le immagini di base delle VM del sistema operativo devono contenere gli aggiornamenti più recenti fino alla data di pubblicazione. Prima di pubblicare il VHD del sistema operativo creato, assicurarsi di aggiornare il sistema operativo e tutti i servizi installati con tutte le patch di sicurezza e manutenzione più recenti.

Per Windows Server, eseguire il comando **Controlla aggiornamenti** .

Per le distribuzioni Linux, gli aggiornamenti vengono in genere scaricati e installati tramite uno strumento da riga di comando o un'utilità grafica. Ubuntu Linux, ad esempio, per l'aggiornamento del sistema operativo mette a disposizione il comando [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) e lo strumento [Update Manager](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html).

### <a name="perform-additional-security-checks"></a>Eseguire controlli di sicurezza aggiuntivi

Mantenere un livello elevato di sicurezza per le immagini della soluzione in Azure Marketplace. L'articolo seguente fornisce un elenco di controllo delle configurazioni di sicurezza e delle procedure per fornire assistenza: [consigli sulla sicurezza per le immagini di Azure Marketplace](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images). Alcune di queste indicazioni sono destinate specificamente a immagini basate su Linux, ma la maggior parte si applica a qualsiasi immagine di macchina virtuale.

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Eseguire una configurazione personalizzata e attività pianificate

Se è necessaria una configurazione aggiuntiva, usare un'attività pianificata che viene eseguita all'avvio per apportare eventuali modifiche finali alla macchina virtuale dopo la distribuzione. Tenere anche conto dei consigli seguenti:

* Se si tratta di un'attività eseguita una sola volta, l'attività deve essere eliminata dopo che è stata completata correttamente.
* Le configurazioni non devono basarsi su unità diverse da C o D, perché solo queste due unità sono sempre sicuramente disponibili (l'unità C è il disco del sistema operativo e l'unità D è il disco locale temporaneo).

Per altre informazioni sulle personalizzazioni di Linux, vedere [Estensioni della macchina virtuale e funzionalità per Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux).

## <a name="generalize-the-image"></a>Generalizzare l'immagine

Tutte le immagini in Azure Marketplace devono poter essere riutilizzate in modo generico. A tale scopo, il disco rigido virtuale del sistema operativo deve essere generalizzato, un'operazione che rimuove tutti gli identificatori e i driver software specifici dell'istanza da una macchina virtuale.

### <a name="windows"></a>Windows

La generalizzazione dei dischi con sistema operativo Windows viene eseguita con lo [strumento sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview). Se in seguito si aggiorna o si riconfigura il sistema operativo, è necessario eseguire nuovamente sysprep.

> [!WARNING]
> Poiché gli aggiornamenti possono essere eseguiti automaticamente, dopo l'esecuzione di Sysprep spegnere la macchina virtuale fino a quando non viene distribuita. Questo arresto eviterà agli aggiornamenti successivi di apportare modifiche specifiche dell'istanza al sistema operativo o ai servizi installati. Per ulteriori informazioni sull'esecuzione di Sysprep, vedere la pagina relativa [alla procedura per generalizzare un disco rigido virtuale](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep).

### <a name="linux"></a>Linux

Il processo seguente generalizza una VM Linux e la ridistribuisce come macchina virtuale separata. Per informazioni dettagliate, vedere [come creare un'immagine di una macchina virtuale o di un disco rigido](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)virtuale. È possibile arrestare quando si raggiunge la sezione "creare una macchina virtuale dall'immagine acquisita".

1. **Rimuovere l'agente Linux di Azure**

    1. Connettersi alla VM Linux tramite un client SSH.
    2. Nella finestra SSH immettere il comando seguente: `sudo waagent -deprovision+user`.
    3. Digitare **Y** per continuare (è possibile aggiungere il parametro **-Force** al comando precedente per evitare il passaggio di conferma).
    d. Al termine del comando, digitare **Exit** per chiudere il client SSH.

2. **Arrestare la macchina virtuale**

    1. Nella portale di Azure selezionare il gruppo di risorse (RG) e deallocare la VM.
    2. Il disco rigido virtuale è ora generalizzato ed è possibile creare una nuova macchina virtuale usando questo disco rigido virtuale.

## <a name="next-steps"></a>Passaggi successivi

Se si verificano problemi durante la creazione del nuovo disco rigido virtuale basato su Azure, vedere [Common issues during VHD creation](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-vhd-creation-issues) (Problemi comuni durante la creazione di un disco rigido virtuale).

In caso contrario:

* [Certificare l'immagine di macchina virtuale](https://docs.microsoft.com/azure/marketplace/partner-center-portal/get-sas-uri) spiega come testare e inviare un'immagine di macchina virtuale per la certificazione di Azure Marketplace, inclusa la posizione in cui ottenere lo *strumento di test di certificazione per Azure Certified* Tool e come usarlo per certificare l'immagine di macchina virtuale.
