---
title: Installare l'agente di Microsoft Azure Recovery Services (MARS)
description: Informazioni su come installare l'agente di Microsoft Azure Recovery Services (MARS) per eseguire il backup dei computer Windows.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: d3932b66dbc41ff2631e2cccbe716c0877a509d3
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422924"
---
# <a name="install-the-azure-backup-mars-agent"></a>Installare l'agente MARS di Backup di AzureInstall the Azure Backup MARS agent

In questo articolo viene illustrato come installare l'agente di Microsoft Azure Recovery Services (MARS). MARS è anche noto come agente di Backup di Azure.MARS is also known as the Azure Backup agent.

## <a name="about-the-mars-agent"></a>Informazioni sull'agente di Servizi di ripristino di Microsoft Azure

Azure Backup uses the MARS agent to back up files, folders, and system state from on-premises machines and Azure VMs. Tali backup vengono archiviati in un insieme di credenziali di Servizi di ripristino in Azure.Those backups are stored in a Recovery Services vault in Azure. È possibile eseguire l'agente:

* Direttamente nei computer Windows locali. Questi computer possono eseguire il backup direttamente in un insieme di credenziali di Servizi di ripristino in Azure.These machines can back up directly to a Recovery Services vault in Azure.
* Nelle macchine virtuali di Azure che eseguono Windows side-by-side con l'estensione di backup della macchina virtuale di Azure.On Azure VMs that run Windows side by side with the Azure VM backup extension. L'agente esegue il backup di file e cartelle specifici nella macchina virtuale.
* In un'istanza del server di backup di Microsoft Azure (MABS) o in un server System Center Data Protection Manager (DPM). In questo scenario, i computer e i carichi di lavoro vengono esati a MABS o Data Protection Manager. MABS o Data Protection Manager usa l'agente MARS per eseguire il backup in un insieme di credenziali in Azure.Then MABS or Data Protection Manager uses the MARS agent to back up to a vault in Azure.

I dati disponibili per il backup dipendono dalla posizione in cui è installato l'agente.

> [!NOTE]
> In genere, si esegue il backup di una macchina virtuale di Azure usando un'estensione di Backup di Azure nella macchina virtuale. Questo metodo esegue il backup dell'intera macchina virtuale. Se si desidera eseguire il backup di file e cartelle specifici nella macchina virtuale, installare e usare l'agente MARS insieme all'estensione. Per altre informazioni, vedere Architettura di un backup di macchine virtuali di [Azure incorporato.](backup-architecture.md#architecture-built-in-azure-vm-backup)

![Passaggi del processo di backup](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>Prima di iniziare

* Informazioni su come [Azure Backup usa l'agente MARS per eseguire il backup](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)dei computer Windows.
* Informazioni [sull'architettura](backup-architecture.md#architecture-back-up-to-dpmmabs) di backup che esegue l'agente MARS su un server MABS o Data Protection Manager secondario.
* Esaminare gli elementi supportati e gli elementi di [cui è possibile eseguire](backup-support-matrix-mars-agent.md) il backup dall'agente MARS.
* Assicurarsi di disporre di un account Azure se è necessario eseguire il backup di un server o di un client in Azure.Make sure that you have an Azure account if you need to back up a server or client to Azure. Se non hai un account, puoi crearne [uno gratuito](https://azure.microsoft.com/free/) in pochi minuti.
* Verificare l'accesso a Internet nei computer di cui si desidera eseguire il backup.
* Assicurarsi che l'utente che esegue l'installazione e la configurazione dell'agente MARS disponga dei privilegi di amministratore locale sul server da proteggere.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Modificare la replica di archiviazioneModify storage replication

Per impostazione predefinita, gli insiemi di credenziali utilizzano [l'archiviazione con ridondanza geografica (GRS, Geoperidere)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

* Se l'insieme di credenziali è il meccanismo di backup principale, si consiglia di utilizzare GRS.
* È possibile usare [l'archiviazione con ridondanza locale (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) per ridurre i costi di archiviazione di Azure.You can use locally redundant storage (LRS) to reduce Azure storage costs.

Per modificare il tipo di replica di archiviazione:

1. Nel nuovo vault, selezionare **Proprietà** nella sezione **Impostazioni.**

1. Nella pagina **Proprietà,** in **Configurazione backup,** selezionare **Aggiorna.**

1. Selezionare il tipo di replica di archiviazione e selezionare **Salva**.

    ![Aggiorna configurazione backup](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> Non è possibile modificare il tipo di replica di archiviazione dopo l'impostazione dell'insieme di credenziali e contiene elementi di backup. Se si desidera eseguire questa operazione, è necessario ricreare il vault.
>

### <a name="verify-internet-access"></a>Verificare l'accesso a Internet

Se il computer dispone di un accesso a Internet limitato, verificare che le impostazioni del firewall nel computer o nel proxy consentano i seguenti URL e indirizzi IP:

* URL
  * `www\.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
* Indirizzi IP
  * 20.190.128.0/18
  * 40.126.0.0/18

### <a name="use-azure-expressroute"></a>Usare Azure ExpressRouteUse Azure ExpressRoute

È possibile eseguire il backup dei dati tramite Azure ExpressRoute usando il peering pubblico (disponibile per i circuiti precedenti) e il peering Microsoft.You can back up your data over Azure ExpressRoute by using public peering (available for old circuits) and Microsoft peering. Il backup tramite peering privato non è supportato.

Per utilizzare il peering pubblico, assicurarsi innanzitutto l'accesso ai domini e agli indirizzi seguenti:

* `http://www.msftncsi.com/ncsi.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`

Per usare il peering Microsoft, selezionare i servizi, le aree geografiche e i valori della community pertinenti seguenti:

* Azure Active Directory (12076:5060)
* Area di Azure, in base alla posizione dell'insieme di credenziali dei servizi di ripristinoAzure region, according to the location of your Recovery Services vault
* Archiviazione di Azure, in base alla posizione dell'insieme di credenziali dei servizi di ripristinoAzure Storage, according to the location of your Recovery Services vault

Per altre informazioni, vedere Requisiti di routing di ExpressRoute .For more information, see [ExpressRoute routing requirements](https://docs.microsoft.com/azure/expressroute/expressroute-routing).

> [!NOTE]
> Il peering pubblico è deprecato per i nuovi circuiti.

Tutti gli URL e gli indirizzi IP precedenti utilizzano il protocollo HTTPS sulla porta 443.

### <a name="private-endpoints"></a>Endpoint privati

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

## <a name="download-the-mars-agent"></a>Scaricare l'agente MARS

Scaricare l'agente MARS in modo che sia possibile installarlo nei computer di cui si desidera eseguire il backup.

Se l'agente è già stato installato in qualsiasi computer, assicurarsi di eseguire la versione più recente dell'agente. Trovare la versione più recente nel portale o andare direttamente al [download](https://aka.ms/azurebackup_agent).

1. Nell'insieme di credenziali, in **Guida introduttiva**, selezionare **Backup**.

    ![Aprire l'obiettivo di backup](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

1. In **Dove è in esecuzione il carico di lavoro?** selezionare **Locale**. Selezionare questa opzione anche se si vuole installare l'agente MARS in una macchina virtuale di Azure.Select this option even if you want to install the MARS agent on an Azure VM.
1. In **Cosa si desidera eseguire il backup?** selezionare File e **cartelle**. È inoltre possibile selezionare **Stato del sistema**. Sono disponibili molte altre opzioni, ma queste opzioni sono supportate solo se si esegue un server di backup secondario. Selezionare **Prepara infrastruttura**.

    ![Configurare file e cartelle](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

1. Per **Preparare l'infrastruttura**, in **Installa agente Servizi di ripristino**scaricare l'agente MARS.

    ![Preparare l'infrastruttura](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

1. Nel menu di download, selezionare **Salva**. Per impostazione predefinita, il file *MARSagentinstaller.exe* viene salvato nella cartella Downloads.

1. Selezionare **Scarica già o utilizza l'agente dei servizi**di ripristino più recente , quindi scaricare le credenziali dell'insieme di credenziali.

    ![Scaricare le credenziali dell’insieme di credenziali](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

1. Selezionare **Salva**. Il file viene scaricato nella cartella Download. Non è possibile aprire il file delle credenziali del vault.

## <a name="install-and-register-the-agent"></a>Installare e registrare l'agente

1. Eseguire il file *MARSagentinstaller.exe* sui computer di cui si desidera eseguire il backup.
1. Nell'Installazione guidata agente MARS selezionare **Impostazioni di installazione**. Scegliere quindi dove installare l'agente e scegliere un percorso per la cache. Quindi selezionare **Avanti**.
   * Azure Backup uses the cache to store data snapshots before sending them to Azure.
   * Il percorso della cache deve avere spazio libero pari ad almeno il 5% delle dimensioni dei dati di cui verrà eseguito il backup.

    ![Scegliere le impostazioni di installazione nell'Installazione guidata agente MARS](./media/backup-configure-vault/mars1.png)

1. Per **Configurazione proxy**, specificare come l'agente in esecuzione nel computer Windows si connetterà a Internet. Quindi selezionare **Avanti**.

   * Se si utilizza un proxy personalizzato, specificare le credenziali e le impostazioni proxy necessarie.
   * Tenere presente che l'agente deve accedere a [URL specifici.](#before-you-start)

    ![Configurare l'accesso a Internet nella procedura guidata MARS](./media/backup-configure-vault/mars2.png)

1. Per **Installazione**, esaminare i prerequisiti e selezionare **Installa**.
1. Dopo aver installato l'agente, selezionare **Procedi alla registrazione**.
1. In Registra**identificazione del** **Vault del Server** > individuare e selezionare il file delle credenziali scaricato. Quindi selezionare **Avanti**.

    ![Aggiungere le credenziali dell'insieme di credenziali tramite la Registrazione guidata server](./media/backup-configure-vault/register1.png)

1. Nella pagina **Impostazioni di crittografia** specificare una passphrase che verrà utilizzata per crittografare e decrittografare i backup per il computer.

    * Salvare la passphrase in un percorso sicuro. Ne hai bisogno per ripristinare un backup.
    * Se perdi o dimentichi la passphrase, Microsoft non può aiutarti a recuperare i dati di backup.

1. Fare clic su **Fine**. L'agente è ora installato e la macchina è registrata nel vault. Ora è possibile configurare e pianificare il backup.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come eseguire il backup di [computer Windows usando l'agente MARS](backup-windows-with-mars-agent.md) di Backup di Azure
