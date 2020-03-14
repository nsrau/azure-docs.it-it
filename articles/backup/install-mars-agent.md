---
title: Installare l'agente di Servizi di ripristino di Microsoft Azure (MARS)
description: Informazioni su come installare l'agente di Servizi di ripristino di Microsoft Azure (MARS) per eseguire il backup di computer Windows.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: b9a6791709d5aff82d11bbf10e5f084fd8c1a000
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79247761"
---
# <a name="install-the-azure-backup-mars-agent"></a>Installare l'agente MARS di backup di Azure

Questo articolo illustra come installare l'agente di Servizi di ripristino di Microsoft Azure (MARS). MARS è anche noto come Azure Backup Agent.

## <a name="about-the-mars-agent"></a>Informazioni sull'agente di Servizi di ripristino di Microsoft Azure

Backup di Azure usa l'agente MARS per eseguire il backup di file, cartelle e stato del sistema da computer locali e VM di Azure. Questi backup vengono archiviati in un insieme di credenziali di servizi di ripristino in Azure. È possibile eseguire l'agente:

* Direttamente nei computer Windows locali. Questi computer possono eseguire il backup direttamente in un insieme di credenziali di servizi di ripristino in Azure.
* Nelle VM di Azure che eseguono Windows affiancato con l'estensione di backup delle VM di Azure. L'agente esegue il backup di file e cartelle specifici nella macchina virtuale.
* In un'istanza di Backup di Microsoft Azure Server (MAB) o in un server System Center Data Protection Manager (DPM). In questo scenario, i computer e i carichi di lavoro eseguono il backup in MAB o Data Protection Manager. Quindi, MAB o Data Protection Manager usa l'agente MARS per eseguire il backup in un insieme di credenziali in Azure.

I dati disponibili per il backup dipendono dalla posizione in cui è installato l'agente.

> [!NOTE]
> In genere si esegue il backup di una macchina virtuale di Azure usando un'estensione di backup di Azure nella macchina virtuale. Questo metodo esegue il backup dell'intera macchina virtuale. Se si vuole eseguire il backup di file e cartelle specifici nella macchina virtuale, installare e usare l'agente MARS insieme all'estensione. Per altre informazioni, vedere [architettura di un backup di VM di Azure predefinito](backup-architecture.md#architecture-built-in-azure-vm-backup).

![Passaggi del processo di backup](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>Prima di iniziare

* Informazioni [su come backup di Azure usa l'agente Mars per eseguire il backup dei computer Windows](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
* Informazioni sull' [architettura di backup](backup-architecture.md#architecture-back-up-to-dpmmabs) che esegue l'agente Mars in un server mab o Data Protection Manager secondario.
* Esaminare gli [elementi supportati e gli elementi di cui è possibile eseguire il backup](backup-support-matrix-mars-agent.md) dall'agente Mars.
* Assicurarsi di disporre di un account Azure se è necessario eseguire il backup di un server o di un client in Azure. Se non si dispone di un account, è possibile crearne uno [gratuito](https://azure.microsoft.com/free/) in pochi minuti.
* Verificare l'accesso a Internet nei computer di cui si vuole eseguire il backup.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Modificare la replica di archiviazione

Per impostazione predefinita, gli insiemi di credenziali usano l' [archiviazione con ridondanza geografica (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

* Se l'insieme di credenziali è il meccanismo di backup principale, si consiglia di usare GRS.
* È possibile usare l' [archiviazione con ridondanza locale (con ridondanza locale)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) per ridurre i costi di archiviazione di Azure.

Per modificare il tipo di replica di archiviazione:

1. Nel nuovo insieme di credenziali selezionare **Proprietà** nella sezione **Impostazioni** .

1. Nella pagina **Proprietà** , in **configurazione backup**, selezionare **Aggiorna**.

1. Selezionare il tipo di replica di archiviazione e selezionare **Salva**.

    ![Aggiorna configurazione backup](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> Non è possibile modificare il tipo di replica di archiviazione dopo che l'insieme di credenziali è stato configurato e contiene elementi di backup. Se si vuole eseguire questa operazione, è necessario creare nuovamente l'insieme di credenziali.
>

### <a name="verify-internet-access"></a>Verificare l'accesso a Internet

Se il computer ha accesso a Internet limitato, verificare che le impostazioni del firewall nel computer o nel proxy consentano gli URL e gli indirizzi IP seguenti:

* URL
  * `www\.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
* indirizzi IP
  * 20.190.128.0/18
  * 40.126.0.0/18

### <a name="use-azure-expressroute"></a>Usare Azure ExpressRoute

È possibile eseguire il backup dei dati tramite Azure ExpressRoute usando il peering pubblico (disponibile per i circuiti precedenti) e il peering Microsoft. Il backup sul peering privato non è supportato.

Per usare il peering pubblico, assicurarsi innanzitutto di accedere ai domini e agli indirizzi seguenti:

* `http://www.msftncsi.com/ncsi.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`

Per usare il peering Microsoft, selezionare i servizi, le aree e i valori della community pertinenti seguenti:

* Azure Active Directory (12076:5060)
* Area di Azure, in base alla posizione dell'insieme di credenziali di servizi di ripristino
* Archiviazione di Azure, in base alla posizione dell'insieme di credenziali di servizi di ripristino

Per ulteriori informazioni, vedere [ExpressRoute routing requirements](https://docs.microsoft.com/azure/expressroute/expressroute-routing).

> [!NOTE]
> Il peering pubblico è deprecato per i nuovi circuiti.

Tutti gli URL e gli indirizzi IP precedenti utilizzano il protocollo HTTPS sulla porta 443.

### <a name="private-endpoints"></a>Endpoint privati

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

## <a name="download-the-mars-agent"></a>Scaricare l'agente MARS

Scaricare l'agente MARS in modo che sia possibile installarlo nei computer di cui si vuole eseguire il backup.

Se l'agente è già stato installato in qualsiasi computer, verificare che sia in esecuzione la versione più recente dell'agente. Trovare la versione più recente nel portale oppure passare direttamente al [download](https://aka.ms/azurebackup_agent).

1. Nell'insieme di credenziali, in **Introduzione**, selezionare **backup**.

    ![Aprire l'obiettivo del backup](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

1. In **dove è in esecuzione il carico di lavoro?** selezionare **locale**. Selezionare questa opzione anche se si vuole installare l'agente MARS in una macchina virtuale di Azure.
1. In **che cosa si vuole eseguire il backup?** selezionare **file e cartelle**. È anche possibile selezionare **stato del sistema**. Sono disponibili molte altre opzioni, ma queste opzioni sono supportate solo se si esegue un server di backup secondario. Selezionare **prepara infrastruttura**.

    ![Configurare file e cartelle](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

1. Per **preparare l'infrastruttura**, in **Install Recovery Services Agent**scaricare l'agente Mars.

    ![Preparare l'infrastruttura](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

1. Nel menu Scarica selezionare **Salva**. Per impostazione predefinita, il file *MARSagentinstaller.exe* viene salvato nella cartella Downloads.

1. Selezionare **già scaricare o usare l'agente di servizi di ripristino più recente**, quindi scaricare le credenziali dell'insieme di credenziali.

    ![Scaricare le credenziali dell’insieme di credenziali](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

1. Selezionare **Salva**. Il file viene scaricato nella cartella Downloads. Non è possibile aprire il file dell'insieme di credenziali.

## <a name="install-and-register-the-agent"></a>Installare e registrare l'agente

1. Eseguire il file *MARSagentinstaller. exe* nei computer di cui si desidera eseguire il backup.
1. Nell'installazione guidata dell'agente MARS selezionare **impostazioni di installazione**. Scegliere la posizione in cui installare l'agente e scegliere un percorso per la cache. Fare quindi clic su **Avanti**.
   * Backup di Azure usa la cache per archiviare gli snapshot dei dati prima di inviarli ad Azure.
   * Il percorso della cache deve avere uno spazio disponibile pari almeno al 5% delle dimensioni dei dati di cui eseguire il backup.

    ![Scegliere le impostazioni di installazione nell'installazione guidata dell'agente MARS](./media/backup-configure-vault/mars1.png)

1. Per la **configurazione del proxy**, specificare il modo in cui l'agente in esecuzione nel computer Windows si connetterà a Internet. Fare quindi clic su **Avanti**.

   * Se si usa un proxy personalizzato, specificare le credenziali e le impostazioni proxy necessarie.
   * Tenere presente che l'agente deve accedere a [URL specifici](#before-you-start).

    ![Configurare l'accesso a Internet nella procedura guidata MARS](./media/backup-configure-vault/mars2.png)

1. Per l' **installazione**, esaminare i prerequisiti e selezionare **Installa**.
1. Dopo l'installazione dell'agente, selezionare **procedi alla registrazione**.
1. In **registrazione guidata Server** > l' **Identificazione**dell'insieme di credenziali individuare e selezionare il file delle credenziali scaricato. Fare quindi clic su **Avanti**.

    ![Aggiungere le credenziali dell'insieme di credenziali tramite la registrazione guidata server](./media/backup-configure-vault/register1.png)

1. Nella pagina **impostazione crittografia** specificare una passphrase che verrà usata per crittografare e decrittografare i backup per il computer.

    * Salvare la passphrase in una posizione sicura. È necessario ripristinare un backup.
    * Se la passphrase viene persa o dimenticata, Microsoft non può essere utile per ripristinare i dati di backup.

1. Fare clic su **Fine**. L'agente è ora installato e il computer è registrato nell'insieme di credenziali. Ora è possibile configurare e pianificare il backup.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [eseguire il backup di computer Windows con l'agente Mars di backup di Azure](backup-windows-with-mars-agent.md)
