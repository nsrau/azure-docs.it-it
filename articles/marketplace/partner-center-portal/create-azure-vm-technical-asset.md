---
title: Creare asset tecnici di macchine virtuali di Azure
description: Informazioni su come creare e configurare le risorse tecniche necessarie per un'offerta di macchina virtuale (VM) per Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 08/14/2020
ms.openlocfilehash: 07c8de2a9d94b51f7183829466bd68d56e19efba
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89646795"
---
# <a name="create-azure-virtual-machine-technical-assets"></a>Creare asset tecnici di macchine virtuali di Azure

Quando si pubblicano le immagini della macchina virtuale (VM) in Azure Marketplace, il team di Azure convalida l'immagine della VM per garantirne l'avvio, la sicurezza e la compatibilità con Azure. Se uno dei test di qualità elevata ha esito negativo, la pubblicazione avrà esito negativo con un messaggio contenente l'errore e i [passaggi di rettifica](https://docs.microsoft.com/azure/marketplace/partner-center-portal/vm-certification-issues-solutions)possibili.

L'articolo descrive come creare e configurare le risorse tecniche necessarie per un'offerta di macchina virtuale (VM) per Azure Marketplace. Una macchina virtuale è costituita da due componenti: il disco rigido virtuale (VHD, Virtual Hard Disk) del sistema operativo e dischi rigidi virtuali facoltativi dei dischi dati associati:

1. **Disco rigido virtuale del sistema operativo**: contiene il sistema operativo e la soluzione distribuiti con l'offerta. Il processo di preparazione del disco rigido virtuale varia a seconda che si tratti di una macchina virtuale basata su Linux o basata su Windows o personalizzata.

2. Dischi **rigidi virtuali del disco dati** : archiviazione permanente dedicata per una macchina virtuale. Non usare il disco rigido virtuale del sistema operativo (ad esempio, l'unità C:) per archiviare informazioni permanenti.

Un'immagine di macchina virtuale contiene un disco del sistema operativo e fino a 16 dischi dati. Usare un disco rigido virtuale per ogni disco dati, anche se il disco è vuoto.

> [!NOTE]
> Indipendentemente dal sistema operativo usato, aggiungere solo il numero minimo di dischi dati necessari per la soluzione. In fase di distribuzione i clienti non possono rimuovere dischi che fanno parte di un'immagine ma possono sempre aggiungerne altri durante o dopo la distribuzione.

> [!IMPORTANT]
> Ogni immagine di macchina virtuale in un piano deve avere lo stesso numero di dischi dati.

## <a name="fundamental-technical-knowledge"></a>Conoscenze tecniche fondamentali

La progettazione, la compilazione e il test di queste risorse richiedono tempo e conoscenze tecniche sia della piattaforma di Azure che delle tecnologie usate per creare l'offerta. Oltre alle conoscenze relative al dominio della soluzione, il team tecnico deve avere il livello di conoscenza seguente delle tecnologie Microsoft:

- Conoscenza di base di [Servizi di Azure](https://azure.microsoft.com/services/)
- Capacità di [progettare applicazioni di Azure per architetture diverse](https://azure.microsoft.com/solutions/architecture/)
- Conoscenza pratica di [Macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/), [Archiviazione di Azure](https://azure.microsoft.com/services/?filter=storage) e [Rete di Azure](https://azure.microsoft.com/services/?filter=networking)
- Conoscenza pratica di [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
- Conoscenza pratica di [JSON](https://www.json.org/)

### <a name="optional-suggested-tools"></a>Strumenti opzionali suggeriti

Per la gestione di macchine virtuali e dischi rigidi virtuali, prendere in considerazione l'uso di uno degli ambienti di scripting seguenti:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
- [Interfaccia della riga di comando di Azure](https://code.visualstudio.com/)

Inoltre, è consigliabile aggiungere gli strumenti seguenti all'ambiente di sviluppo in uso:

- [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
- [Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-a-vm-image-using-an-approved-base"></a>Creare un'immagine di macchina virtuale usando una base approvata

Per creare le risorse tecniche della macchina virtuale usando un'immagine creata in locale, vedere [creare un'immagine di VM usando una base approvata](#create-a-vm-image-using-an-approved-base) di seguito.

Questa sezione descrive vari aspetti relativi all'uso di una base approvata, ad esempio l'uso di Remote Desktop Protocol (RDP), la selezione di una dimensione per la macchina virtuale, l'installazione degli aggiornamenti di Windows più recenti e la generalizzazione dell'immagine del disco rigido virtuale.

Seguire le istruzioni riportate in questo articolo per usare Azure per creare una macchina virtuale contenente un sistema operativo già configurato e approvato. Se tali indicazioni non sono compatibili con la soluzione in uso, è possibile creare e configurare una macchina virtuale in locale con un sistema operativo approvato. È quindi possibile configurare e prepararla per il caricamento, come descritto in [Preparare un disco rigido virtuale Windows o VHDX prima del caricamento in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).

### <a name="select-an-approved-base-image"></a>Selezionare un'immagine di base approvata

Selezionare il sistema operativo Windows o Linux come base.

Il VHD del sistema operativo per l'immagine di macchina virtuale deve essere basato su un'immagine di base approvata per Azure contenente Windows Server o SQL Server.

Quando si invia una richiesta di ripubblicazione dell'immagine con gli aggiornamenti, il test case di verifica del numero di parte potrebbe non riuscire. In tale istanza, l'immagine non verrà approvata.

Questo errore si verifica quando si usa un'immagine di base che appartiene a un altro server di pubblicazione ed è stata aggiornata l'immagine. In questo caso, non sarà consentito pubblicare l'immagine.

Per risolvere questo problema, recuperare l'immagine più recente da Azure Marketplace e apportare le modifiche all'immagine. Per visualizzare le immagini di base approvate in cui è possibile cercare l'immagine, vedere gli argomenti seguenti:

#### <a name="windows"></a>Windows

- Windows Server ([2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2012 R2 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2012 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
- SQL Server 2019 ([Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview), [standard](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview), [Web](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview))
- SQL Server 2014 ([Enterprise](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance), [standard](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance), [Web](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance))
- SQL Server 2012 SP2 ([Enterprise](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance), [standard](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance), [Web](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance))

#### <a name="linux"></a>Linux

Azure offre una gamma di distribuzioni Linux approvate. Per un elenco aggiornato, vedere [Linux in distribuzioni approvate da Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).

### <a name="create-vm-on-the-azure-portal"></a>Creare una macchina virtuale nella portale di Azure

Seguire questa procedura per creare l'immagine di macchina virtuale di base nel [portale di Azure](https://ms.portal.azure.com/):

1. Accedere al [portale di Azure](https://ms.portal.azure.com/) con l'account Microsoft associato alla sottoscrizione di Azure che si intende usare per pubblicare l'offerta di macchina virtuale.
2. Creare un nuovo gruppo di risorse e specificare i valori per **Nome del gruppo di risorse**, **Sottoscrizione** e **Località del gruppo di risorse**. Per informazioni dettagliate, vedere [Gestire le risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

    :::image type="content" source="media/vm/create-resource-group.png" alt-text="Mostra l'inizio della creazione di un gruppo di risorse.":::

3. Selezionare **macchine virtuali** nel riquadro di spostamento a sinistra per visualizzare la pagina dei dettagli delle macchine virtuali.
4. Selezionare **+ Aggiungi** per aprire **Create a virtual machine experience (Crea un'esperienza di macchina virtuale)** .
5. Selezionare l'immagine nell'elenco a discesa oppure selezionare **Sfoglia tutte le immagini pubbliche e private** per cercare o esplorare tutte le immagini di macchine virtuali disponibili. Esempio:

    :::image type="content" source="media/vm/create-resource-group-example.png" alt-text="Mostra un'immagine di macchina virtuale di esempio.":::

6. Selezionare le dimensioni della macchina virtuale per eseguire la distribuzione seguendo queste indicazioni:
    1. Se si prevede di sviluppare il disco rigido virtuale in locale, la dimensione non è rilevante. Valutare l'opportunità di usare una delle macchine virtuali di dimensioni minori.
    2. Se si prevede di sviluppare l'immagine in Azure, valutare l'opportunità di usare una delle dimensioni di macchina virtuale consigliate per l'immagine selezionata.

    :::image type="content" source="media/vm/create-virtual-machine.png" alt-text="Mostra la selezione delle dimensioni della macchina virtuale.":::

7. In **Dischi** espandere la sezione **Avanzata** e impostare l'opzione **Usa dischi gestiti** su **No**.

    :::image type="content" source="media/vm/use-managed-disks.png" alt-text="Mostra l'opzione per l'uso di Managed Disks.":::

8. Indicare gli altri dettagli necessari per creare la macchina virtuale.
9. Selezionare **Review + create (Revisione e creazione)** per rivedere le scelte effettuate. Quando viene visualizzato il messaggio **Convalida superata**, selezionare **Crea**.

Azure avvia il provisioning della macchina virtuale specificata. È possibile tenere traccia dello stato di avanzamento selezionando la scheda **Macchine virtuali** a sinistra. Al termine della creazione, lo stato viene modificato in **In esecuzione**.

### <a name="create-a-generation-2-vm-on-the-azure-portal"></a>Creare una macchina virtuale di seconda generazione nella portale di Azure

Creare una macchina virtuale di generazione 2 (Gen2) in portale di Azure.

1. Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com/).
2. Selezionare **Crea una risorsa**.
3. Selezionare **Visualizza tutto** da Azure Marketplace sulla sinistra.
4. Selezionare un'immagine che supporti Gen2.
5. Selezionare **Create** (Crea).
6. Nella scheda **Avanzate**, nella sezione **Generazione macchina virtuale** selezionare l'opzione **Gen 2**.
7. Nella scheda **Impostazioni di base**, in **Dettagli istanza** passare a **Dimensioni** e aprire il pannello **Selezionare le dimensioni di macchina virtuale**.
8. Selezionare le dimensioni consigliate per le [macchine virtuali di seconda generazione](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2#generation-2-vm-sizes) e le dimensioni supportate.
9. Eseguire le operazioni del [flusso di creazione del portale di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal) per completare la creazione della macchina virtuale.

    :::image type="content" source="media/vm/vm-generation.png" alt-text="Mostra l'opzione per selezionare la generazione della macchina virtuale.":::

## <a name="connect-to-your-azure-vm"></a>Connettersi alla macchina virtuale di Azure

Questa sezione illustra come connettersi e accedere alla macchina virtuale creata in Azure. Dopo aver eseguito correttamente la connessione, è possibile usare la macchina virtuale come se si fosse connessi in locale al server host.

### <a name="connect-to-a-windows-based-vm"></a>Connettersi a una macchina virtuale basata su Windows

Usare il client per desktop remoto per connettersi alla macchina virtuale basata su Windows ospitata in Azure. La maggior parte delle versioni di Windows in modalità nativa dispone del supporto per il protocollo RDP (Remote Desktop Protocol). Per sistemi operativi diversi, altre informazioni sui client sono disponibili in [Client Desktop remoto](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).

Questo articolo illustra come usare il supporto di Windows RDP incorporato per connettersi alla VM: [come connettersi e accedere a una macchina virtuale di Azure che esegue Windows](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).

> [!TIP]
> Durante il processo è possibile che vengano visualizzati avvisi di sicurezza. Ad esempio, "The .rdp file is from an unknown publisher (Il file con estensione rdp proviene da un autore sconosciuto)" o "Your user credentials cannot be verified. (Impossibile verificare le credenziali utente)". È opportuno ignorare questi avvisi.

### <a name="connect-to-a-linux-based-vm"></a>Connettersi a una macchina virtuale basata su Linux

Per connettersi a una macchina virtuale basata su Linux, è necessario disporre di un client con protocollo Secure Shell (SSH). I passaggi seguenti usano il terminale SHH [PuTTY](https://www.ssh.com/ssh/putty/) gratuito.

1. Accedere al [portale di Azure](https://ms.portal.azure.com/).
2. Cercare e selezionare Macchine virtuali.
3. Selezionare la macchina virtuale a cui ci si vuole connettere.
4. Avviare la macchina virtuale se è già in esecuzione.
5. Selezionare il nome della macchina virtuale da aprire nella pagina Panoramica.
6. Annotare l'indirizzo IP pubblico e il nome DNS della macchina virtuale (se questi valori non sono impostati, è necessario [creare un'interfaccia di rete](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface).
7. Aprire l'applicazione PuTTY.
8. Nella finestra di dialogo per la configurazione di PuTTY immettere l'indirizzo IP e il nome DNS della macchina virtuale.

    :::image type="content" source="media/vm/putty-configuration.png" alt-text="Illustra le impostazioni del terminale PuTTy, evidenziando i campi nome host e porta.":::

9. Selezionare **Apri** per aprire un terminale PuTTY.
10. Quando richiesto, immettere il nome account e la password dell'account di macchina virtuale di Linux.

## <a name="configure-the-virtual-machine"></a>Configurare la macchina virtuale

Questa sezione descrive come ridimensionare, aggiornare e generalizzare una macchina virtuale di Azure. Questa procedura è necessaria per preparare la macchina virtuale alla distribuzione in Azure Marketplace.

### <a name="sizing-the-vhds"></a>Ridimensionamento dei dischi rigidi virtuali

Le seguenti regole sono destinate alle limitazioni sulle dimensioni del disco del sistema operativo. Quando si invia una richiesta, verificare che le dimensioni del disco del sistema operativo siano comprese nel limite per Linux o Windows.

| OS | Dimensioni del disco rigido virtuale consigliate |
| --- | --- |
| Linux | da 30 a 1023 GB |
| Windows | da 30 a 250 GB |

Poiché le macchine virtuali consentono l'accesso al sistema operativo sottostante, verificare che le dimensioni del disco rigido virtuale siano sufficienti per il disco rigido virtuale. Poiché i dischi non sono espandibili senza tempi di inattività, utilizzare dimensioni del disco comprese tra 30 e 50 &nbsp; GB.

| Dimensioni VHD | Dimensioni effettive occupate | Soluzione |
| --- | --- | --- |
| >500 TB | n/d | Contattare il team di supporto per l'approvazione di un'eccezione. |
| 250-500 TB | >200 GB diversi dalla dimensione del BLOB | Contattare il team di supporto per l'approvazione di un'eccezione. |

### <a name="install-the-most-current-updates"></a>Installare gli aggiornamenti più recenti

Le immagini di base delle macchine virtuali del sistema operativo devono contenere gli aggiornamenti più recenti fino alla data di pubblicazione. Prima di pubblicare il disco rigido virtuale del sistema operativo creato, assicurarsi di aggiornare il sistema operativo e tutti i servizi installati con tutte le patch di sicurezza e manutenzione più recenti.

- Per Windows Server, eseguire il comando Controlla aggiornamenti.
- Per le distribuzioni Linux, gli aggiornamenti vengono in genere scaricati e installati tramite uno strumento da riga di comando o un'utilità grafica. Ubuntu Linux, ad esempio, per l'aggiornamento del sistema operativo mette a disposizione il comando [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) e lo strumento [Update Manager](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html).

#### <a name="perform-additional-security-checks"></a>Eseguire controlli di sicurezza aggiuntivi

Mantenere un livello di sicurezza elevato per le immagini delle soluzioni in Azure Marketplace. Per un elenco di controllo delle procedure e delle configurazioni di sicurezza, vedere [raccomandazioni sulla sicurezza per le immagini di Azure Marketplace](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images). Alcune di queste indicazioni sono destinate specificamente a immagini basate su Linux, ma la maggior parte si applica a qualsiasi immagine di macchina virtuale.

#### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Eseguire una configurazione personalizzata e attività pianificate

Se è necessaria una configurazione aggiuntiva, usare un'attività pianificata che viene eseguita all'avvio per apportare le modifiche finali alla macchina virtuale dopo la distribuzione. Tenere inoltre in considerazione quanto segue:

- Per le attività da eseguire una sola volta, l'attività dovrebbe eliminarsi automaticamente dopo il completamento.
- Le configurazioni non devono basarsi su unità diverse da C o D, perché solo queste due unità sono sempre disponibili (l'unità C è il disco del sistema operativo e l'unità D è il disco locale temporaneo).

Per altre informazioni sulle personalizzazioni di Linux, vedere [Estensioni della macchina virtuale e funzionalità per Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux).

## <a name="generalize-the-image"></a>Generalizzare l'immagine

Tutte le immagini in Azure Marketplace devono poter essere riutilizzate in modo generico. Per ottenere questo risultato, il disco rigido virtuale del sistema operativo deve essere generalizzato. La generalizzazione è un'operazione che rimuove tutti gli identificatori specifici di istanze e tutti i driver software da una macchina virtuale.

### <a name="for-windows"></a>Per Windows

I dischi del sistema operativo Windows sono generalizzati con lo strumento [Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) . Se successivamente si aggiorna o si riconfigura il sistema operativo, è necessario eseguire di nuovo Sysprep.

> [!WARNING]
> Dopo aver eseguito Sysprep, spegnere la macchina virtuale fino a quando non viene distribuita perché gli aggiornamenti possono essere eseguiti automaticamente. Questo arresto eviterà che gli aggiornamenti successivi possano apportare modifiche specifiche di istanza al sistema operativo o ai servizi installati. Per altre informazioni sull'esecuzione di sysprep, vedere [Procedura per generalizzare un disco rigido virtuale](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep).

### <a name="for-linux"></a>Per Linux

Il processo seguente generalizza una macchina virtuale Linux e la ridistribuisce come macchina virtuale separata. Per informazioni dettagliate, vedere [Come creare un'immagine di una macchina virtuale o un disco rigido virtuale](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image). È possibile arrestare quando si raggiunge la sezione denominata "creare una macchina virtuale dall'immagine acquisita".

1. Rimuovere l'agente Linux di Azure

    1. Connettersi alla VM Linux tramite un client SSH
    2. Nella finestra SSH digitare il comando seguente: `sudo waagent –deprovision+user`.
    3. Digitare Y per continuare (è possibile aggiungere il parametro -force al comando precedente per evitare il passaggio di conferma).
    4. Al termine dell'esecuzione del comando, digitare Exit (Esci) per chiudere il client SSH.

2. Arrestare la macchina virtuale

    1. Nel portale di Azure selezionare il gruppo di risorse (RG) e deallocare la macchina virtuale.
    2. Il disco rigido virtuale è ora generalizzato ed è possibile usarlo per creare una nuova macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi

- Se si verificano problemi durante la creazione del nuovo disco rigido virtuale basato su Azure, vedere [Common issues during VHD creation](common-issues-during-vhd-creation.md) (Problemi comuni durante la creazione di un disco rigido virtuale).
- In caso contrario, la [macchina virtuale (VM) di test distribuita da VHD](azure-vm-image-certification.md) spiega come testare e inviare un'immagine di macchina virtuale per la certificazione di Azure Marketplace, inclusa la posizione in cui ottenere lo strumento di test di certificazione per Azure Certified Tool e come usarlo per certificare l'immagine di macchina virtuale.
