---
title: Provisioning di una macchina virtuale di SQL Server | Microsoft Docs
description: "Creare e connettersi a una macchina virtuale di SQL Server in Azure usando il portale. Questa esercitazione usa la modalità Resource Manager."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
tags: azure-resource-manager
ms.assetid: 1aff691f-a40a-4de2-b6a0-def1384e086e
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 08/08/2017
ms.author: jroth
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: 98e307bc3b2dd876b74d64fbdeec162827a2e86e
ms.contentlocale: it-it
ms.lasthandoff: 08/09/2017

---
# <a name="provision-a-sql-server-virtual-machine-in-the-azure-portal"></a>Effettuare il provisioning di una macchina virtuale di SQL Server nel portale di Azure
> [!div class="op_single_selector"]
> * [Portale](virtual-machines-windows-portal-sql-server-provision.md)
> * [PowerShell](virtual-machines-windows-ps-sql-create.md)
> 
> 

Questa esercitazione end-to-end illustra come usare il portale di Azure per effettuare il provisioning di una macchina virtuale che esegue SQL Server.

La raccolta di macchine virtuali (VM) di Azure include numerose immagini che contengono Microsoft SQL Server. Sono sufficienti pochi clic per selezionare una delle immagini di VM di SQL dalla raccolta ed effettuarne il provisioning nell'ambiente Azure.

In questa esercitazione si apprenderà come:

* [Selezionare un'immagine di VM di SQL dalla raccolta](#select-a-sql-vm-image-from-the-gallery)
* [Configurare e creare la macchina virtuale](#configure-the-vm)
* [Aprire la macchina virtuale tramite Desktop remoto](#open-the-vm-with-remote-desktop)
* [Connettersi a SQL Server in modalità remota](#connect-to-sql-server-remotely)

## <a name="select-a-sql-vm-image-from-the-gallery"></a>Selezionare un'immagine di VM di SQL dalla raccolta

1. Accedere al [portale di Azure](https://portal.azure.com) con il proprio account.

   > [!NOTE]
   > Se non si dispone di un account Azure, provare la [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

2. Nel portale di Azure fare clic su **Nuovo**. Nel portale verrà aperto il pannello **Nuovo** .

3. Nel pannello **Nuovo** fare clic su **Calcolo** e quindi su **Visualizza tutto**.

   ![Pannelli Nuovo e Calcolo in Azure](./media/virtual-machines-windows-portal-sql-server-provision/azure-new-compute-blade.png)

4. Nel campo di ricerca digitare **SQL Server** e premere INVIO.

5. Fare quindi clic sull'icona **Filtro** e selezionare **Microsoft** come editore. Fare clic su **Fatto** nel pannello Filtro per filtrare i risultati e ottenere le immagini di SQL Server pubblicate da Microsoft.

   ![Pannello Macchine virtuali di Azure](./media/virtual-machines-windows-portal-sql-server-provision/azure-compute-blade2.png)

5. Esaminare le immagini di SQL Server disponibili. Ogni immagine identifica una versione di SQL Server e un sistema operativo.

6. Selezionare l'immagine denominata **Free License: SQL Server 2016 SP1 Developer on Windows Server 2016**.

   > [!TIP]
   > L'edizione per sviluppatori viene usata in questa esercitazione perché è una versione completa di SQL Serve gratuita a scopo di test per lo sviluppo. Si paga solo il costo dell'esecuzione della macchina virtuale. Per questa esercitazione è tuttavia possibile scegliere e usare qualsiasi immagine.

   > [!TIP]
   > Le immagini VM di SQL includono i costi di licenza per SQL Server nei prezzi al minuto della VM creata (ad eccezione delle versioni Developer ed Express). SQL Server per sviluppatori è gratuito per sviluppo/test (non per la produzione), mentre SQL Express è gratuito per carichi di lavoro leggeri (inferiori a 1 GB di memoria e a 10 GB di archiviazione). Esiste un'altra opzione per Bring Your Own License (BYOL) e il pagamento della sola VM. Tali nomi di immagine hanno il prefisso {BYOL}. 
   >
   > Per altre informazioni su queste opzioni, vedere [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Guida ai prezzi per le VM di SQL Server in Azure).

7. In **Selezionare un modello di distribuzione** assicurarsi che l'opzione **Resource Manager** sia selezionata. Resource Manager è il modello di distribuzione consigliato per le nuove macchine virtuali. 

8. Fare clic su **Crea**.

    ![Creare VM di SQL con Resource Manager](./media/virtual-machines-windows-portal-sql-server-provision/azure-compute-sql-deployment-model.png)

## <a name="configure-the-vm"></a>Configurare la VM
Sono disponibili cinque pannelli per la configurazione di una macchina virtuale di SQL Server.

| Passaggio | Descrizione |
| --- | --- |
| **Nozioni di base** |[Configurare le impostazioni di base](#1-configure-basic-settings) |
| **Dimensione** |[Scegliere le dimensioni della macchina virtuale](#2-choose-virtual-machine-size) |
| **Impostazioni** |[Configurare le funzionalità facoltative](#3-configure-optional-features) |
| **Impostazioni di SQL Server** |[Configurare le impostazioni di SQL Server](#4-configure-sql-server-settings) |
| **Riepilogo** |[Esaminare il riepilogo](#5-review-the-summary) |

## <a name="1-configure-basic-settings"></a>1. Configurare le impostazioni di base

Nel pannello **Nozioni di base** specificare le informazioni seguenti:

* Immettere un **Nome**univoco per la macchina virtuale.

* Selezionare **SSD** come tipo di disco della macchina virtuale per ottenere prestazioni ottimali.

* Specificare un **Nome utente** per l'account amministratore locale nella VM. Questo account viene aggiunto anche al ruolo del server fisso **sysadmin** di SQL Server.

* Specificare una **Password**complessa.

* Se sono disponibili più sottoscrizioni, verificare che la sottoscrizione sia corretta per la nuova VM.

* Nella casella **Gruppo di risorse** digitare un nome per il nuovo gruppo di risorse. In alternativa fare clic su **Usa esistente** per usare un gruppo di risorse esistente. Un gruppo di risorse è una raccolta di risorse correlate in Azure, ovvero macchine virtuali, account di archiviazione, reti virtuali e così via.

  > [!NOTE]
  > L'uso di un nuovo gruppo di risorse risulta utile se si stanno solo eseguendo test o se si sta iniziando a usare le distribuzioni di SQL Server in Azure. Dopo aver completato il test, eliminare il gruppo di risorse per eliminare automaticamente la macchina virtuale e tutte le risorse associate a tale gruppo di risorse. Per altre informazioni sui gruppi di risorse, vedere [Panoramica di Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md).

* Selezionare un'opzione in **Località** per l'area di Azure in cui verrà ospitata la distribuzione.

* Fare clic su **OK** per salvare le impostazioni.

    ![Pannello Informazioni di base di SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-basic.png)

## <a name="2-choose-virtual-machine-size"></a>2. Scegliere le dimensioni della macchina virtuale

Nel passaggio **Dimensioni** scegliere le dimensioni della macchina virtuale nel pannello **Scegli una dimensione**. Inizialmente il pannello visualizza le dimensioni della macchina virtuale consigliate in base all'immagine selezionata.

> [!IMPORTANT]
> Il costo mensile stimato visualizzato nel pannello **Scegli una dimensione** non include i costi di licenza di SQL Server, ma solo il costo della VM. Per le edizioni Express e Developer di SQL Server, si tratta del costo stimato totale. Per altre edizioni, vedere la [pagina dei prezzi delle macchine virtuali Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) e selezionare l'edizione di SQL Server di interesse. Vedere anche [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Guida ai prezzi per le VM di SQL Server in Azure).

![Opzioni per le dimensioni di VM di SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-vm-choose-a-size.png)

Per i carichi di lavoro di produzione, vedere le dimensioni e la configurazione di VM consigliate in [Procedure consigliate per le prestazioni per SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-performance.md). Se le dimensioni di VM necessarie non sono elencate, fare clic sul pulsante **Visualizza tutto**.

> [!NOTE]
> Per altre informazioni sulle dimensioni di macchine virtuali, vedere [Dimensioni delle macchine virtuali](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Scegliere le dimensioni della macchina virtuale e quindi fare clic su **Seleziona**.

## <a name="3-configure-optional-features"></a>3. Configurare le funzionalità facoltative

Nel pannello **Impostazioni** configurare l'archiviazione di Azure, la rete e il monitoraggio per la macchina virtuale.

* In **Archiviazione** selezionare **Sì** sotto **Usa il servizio Managed Disks**.

   > [!NOTE]
   > Per SQL Server è consigliabile usare Managed Disks. Managed Disks gestisce automaticamente le risorse di archiviazione. Inoltre, quando le macchine virtuali con Managed Disks sono nello stesso set di disponibilità, Azure distribuisce le risorse di archiviazione in modo da garantire la ridondanza appropriata. Per altre informazioni, vedere [Panoramica di Azure Managed Disks](../../../storage/storage-managed-disks-overview.md). Per informazioni dettagliate sull'uso di Managed Disks in un set di disponibilità, vedere [Usare Managed Disks per le macchine virtuali nel set di disponibilità](../manage-availability.md).

* In **Rete**è possibile accettare i valori popolati automaticamente. È anche possibile fare clic sulle singole funzionalità per configurare manualmente la **Rete virtuale**, la **Subnet**, l'**Indirizzo IP pubblico** e il **Gruppo di sicurezza di rete**. Ai fini di questa esercitazione, è possibile mantenere i valori predefiniti.

* Azure abilita l'opzione **Monitoraggio** per impostazione predefinita con lo stesso account di archiviazione designato per la VM. Queste impostazioni possono essere modificate qui, se necessario.

* Per questa esercitazione, in **Set di disponibilità** è possibile lasciare l'impostazione predefinita **nessuno**. Se si prevede di impostare i gruppi di disponibilità SQL AlwaysOn, configurare la disponibilità per evitare di ricreare la macchina virtuale.  Per altre informazioni, vedere [Gestione della disponibilità delle macchine virtuali](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Al termine della configurazione di queste impostazioni, fare clic su **OK**.

## <a name="4-configure-sql-server-settings"></a>4. Configurare le impostazioni di SQL Server
Nel pannello **Impostazioni di SQL Server** configurare le impostazioni e le ottimizzazioni specifiche per SQL Server. Le impostazioni che è possibile configurare per SQL Server includono le seguenti:

| Impostazione |
| --- |
| [Connettività](#connectivity) |
| [Autenticazione](#authentication) |
| [Configurazione dell'archiviazione](#storage-configuration) |
| [Applicazione automatica delle patch](#automated-patching) |
| [Backup automatico](#automated-backup) |
| [Integrazione dell'insieme di credenziali delle chiavi di Azure](#azure-key-vault-integration) |
| [Servizi R](#r-services) |

### <a name="connectivity"></a>Connettività

In **Connettività SQL**specificare il tipo di accesso da assegnare all'istanza di SQL Server nella VM. Ai fini di questa esercitazione, selezionare **Pubblica (Internet)** per consentire connessioni a SQL Server da macchine virtuali o servizi su Internet. Se si seleziona questa opzione, Azure configura automaticamente il firewall e il gruppo di sicurezza di rete per consentire il traffico sulla porta 1433.

![Opzioni di connettività di SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-connectivity-alt.png)

> [!TIP]
> Per impostazione predefinita, SQL Server è in ascolto su una porta nota, la porta **1433**. Per aumentare la sicurezza, modificare la porta nella finestra di dialogo precedente impostando l'ascolto su una porta non predefinita, ad esempio la 1401. In questo caso, è necessario usare tale porta per la connessione da qualsiasi strumento client, ad esempio SSMS.

Per connettersi a SQL Server tramite Internet, è necessario abilitare anche l'Autenticazione di SQL Server, illustrata nella sezione successiva.

Se si preferisce non abilitare le connessioni al motore di database tramite Internet, scegliere una delle opzioni seguenti:

* **Locale (solo all'interno della macchina virtuale)** per consentire le connessioni a SQL Server solo dalla VM.
* **Privata (solo all'interno della rete virtuale)** per consentire le connessioni a SQL Server da computer o servizi nella stessa rete virtuale.

È in genere possibile migliorare la sicurezza scegliendo la connettività più restrittiva consentita dallo scenario specifico. Tutte le opzioni possono essere comunque protette tramite le regole del gruppo di sicurezza di rete e l'Autenticazione di SQL o Windows. È possibile modificare il gruppo di sicurezza di rete dopo la creazione della VM. Per altre informazioni, vedere [Considerazioni relative alla sicurezza per SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-security.md).

> [!NOTE]
> L'immagine della macchina virtuale per SQL Server Express Edition non abilita automaticamente il protocollo TCP/IP. Lo stesso avviene anche per le opzioni di connettività pubblica e privata. Per Express Edition, è necessario usare Gestione configurazione SQL Server per [abilitare manualmente il protocollo TCP/IP](#configure-sql-server-to-listen-on-the-tcp-protocol) dopo avere creato la VM.

### <a name="authentication"></a>Autenticazione

Se è necessaria l'autenticazione di SQL Server, fare clic su **Abilita** under **Autenticazione SQL**.

![Autenticazione di SQL Server](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-authentication.png)

> [!NOTE]
> Se si prevede di accedere a SQL Server tramite Internet, ovvero l'opzione di connettività Pubblica, è necessario abilitare l'autenticazione di SQL in questa area. L'accesso pubblico a SQL Server richiede l'uso dell'autenticazione di SQL.
> 
> 

Se si abilita l'autenticazione di SQL Server, specificare un **Nome di accesso** e una **Password**. Questo nome utente viene configurato come account di accesso di Autenticazione di SQL Server e membro del ruolo del server fisso **sysadmin** . Per altre informazioni sulle modalità di autenticazione, vedere [Scegliere una modalità di autenticazione](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode) .

Se non si abilita l'autenticazione di SQL Server, è possibile usare l'account amministratore locale nella macchina virtuale per connettersi all'istanza di SQL Server.

### <a name="storage-configuration"></a>Configurazione dell'archiviazione

Fare clic su **Configurazione dell'archiviazione** per specificare i requisiti di archiviazione.

![Configurazione dell'archiviazione SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-storage.png)

> [!NOTE]
> Se la VM è stata configurata manualmente per usare l'archiviazione Standard, questa opzione non è disponibile. L'ottimizzazione automatica delle risorse di archiviazione è disponibile solo per l'Archiviazione Premium.

> [!TIP]
> Il numero di cursori e il limite massimo di ogni dispositivo di scorrimento dipendono dalle dimensioni di VM selezionate. Per una VM più grande e potente è possibile aumentare le prestazioni in misura maggiore.

È possibile specificare requisiti come operazioni di I/O al secondo, velocità effettiva in Mbps e dimensioni di archiviazione totali. Configurare questi valori usando i dispositivi di scorrimento. È possibile modificare queste impostazioni di archiviazione in base al carico di lavoro. Il portale calcola automaticamente il numero di dischi da collegare e configurare in base a questi requisiti.

In **Ottimizzazione dell'archiviazione**selezionare una delle opzioni seguenti:

* **Generale** : è l'impostazione predefinita e supporta la maggior parte dei carichi di lavoro.
* **Elaborazione transazionale** : ottimizza l'archiviazione per carichi di lavoro OLTP di database tradizionali.
* **Data warehousing** : ottimizza l'archiviazione per i carichi di lavoro di analisi e creazione di report.

### <a name="automated-patching"></a>Applicazione automatica delle patch

**Automated patching** è abilitata per impostazione predefinita. Questa opzione consente ad Azure di applicare automaticamente le patch a SQL Server e al sistema operativo. Specificare un giorno della settimana, un'ora e una durata per la finestra di manutenzione. Durante la finestra di manutenzione Azure esegue l'applicazione delle patch. La pianificazione dell'ora, la finestra di manutenzione usa le impostazioni locali della macchina virtuale. Se non si vuole consentire ad Azure di applicare automaticamente le patch per SQL Server e per il sistema operativo, fare clic su **Disabilita**.  

![Applicazione automatica delle patch di SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-patching.png)

Per altre informazioni, vedere [Applicazione automatica delle patch per SQL Server nelle macchine virtuali di Azure (Resource Manager)](virtual-machines-windows-sql-automated-patching.md).

### <a name="automated-backup"></a>Backup automatico

Abilitare i backup automatici dei database per tutti i database in **Backup automatico**. L'opzione Backup automatico è disabilitata per impostazione predefinita.

Quando si abilita il backup automatico di SQL è possibile configurare quanto segue:

* Periodo di conservazione (giorni) per i backup
* Account di archiviazione da usare per i backup
* Opzione di crittografia e password per i backup
* Backup dei database di sistema
* Pianificazione dei backup

Per crittografare il backup, fare clic su **Abilita**. Specificare quindi la **Password**. Azure crea un certificato per crittografare i backup e usa la password specificata per proteggere il certificato.

![Backup automatico di SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-autobackup2.png)

 Per altre informazioni, vedere [Backup automatizzato per SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-automated-backup.md).

### <a name="azure-key-vault-integration"></a>Integrazione dell'insieme di credenziali delle chiavi di Azure

Per archiviare i segreti di sicurezza in Azure per la crittografia, fare clic su **Integrazione dell'insieme di credenziali delle chiavi di Azure** e quindi su **Abilita**.

![Integrazione dell'insieme di credenziali delle chiavi di Azure per SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-akv.png)

La tabella seguente include l'elenco dei parametri necessari per configurare l'integrazione dell'insieme di credenziali delle chiavi di Azure.

| PARAMETRO | Descrizione | ESEMPIO |
| --- | --- | --- |
| **URL dell'insieme di credenziali delle chiavi** |Percorso dell'insieme di credenziali delle chiavi. |https://contosokeyvault.vault.azure.net/ |
| **Nome dell'entità** |Nome dell'entità servizio di Azure Active Directory. È detto anche ID client. |fde2b411-33d5-4e11-af04eb07b669ccf2 |
| **Segreto dell'entità** |Nome dell'entità servizio di Azure Active Directory. È detto anche Segreto client. |9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM= |
| **Nome credenziali** |**Nome della credenziale**: l'integrazione di AKV crea una credenziale all'interno di SQL Server, consentendo alla macchina virtuale di avere accesso all'insieme di credenziali delle chiavi. Scegliere un nome per la credenziale. |mycred1 |

Per altre informazioni, vedere [Configurare l'integrazione dell'insieme di credenziali delle chiavi di Azure per SQL Server in macchine virtuali di Azure (Resource Manager)](virtual-machines-windows-ps-sql-keyvault.md).

### <a name="r-services"></a>Servizi R

È possibile abilitare i [servizi R di SQL Server](https://msdn.microsoft.com/library/mt604845.aspx). Questo consente di usare l'analisi avanzata con SQL Server 2016. Fare clic su **Abilita** on the **SQL Server Settings** .

> [!NOTE]
> Per SQL Server 2016 Developer Edition, questa opzione viene erroneamente disabilitata dal portale. Per tale edizione è necessario abilitare manualmente i servizi R dopo la creazione della VM.

![Abilitare i servizi R di SQL Server](./media/virtual-machines-windows-portal-sql-server-provision/azure-vm-sql-server-r-services.png)

Al termine della configurazione delle impostazioni di SQL Server, fare clic su **OK**.

## <a name="5-review-the-summary"></a>5. Esaminare il riepilogo

Nel pannello **Riepilogo** esaminare le informazioni e fare clic su **Acquista** per creare l'istanza di SQL Server, il gruppo di risorse e le risorse specificati per questa VM.

È possibile monitorare la distribuzione dal portale di Azure. Il pulsante **Notifiche** nella parte superiore della schermata mostra lo stato di base della distribuzione.

> [!NOTE]
> Per dare un'idea dei tempi di distribuzione, è stata distribuita una macchina virtuale di SQL nell'area Stati Uniti orientali con le impostazioni predefinite. Il completamento della distribuzione di prova ha richiesto in totale 26 minuti. È possibile che i tempi di distribuzione specifici siano minori o maggiori in base all'area in cui ci si trova e alle impostazioni selezionate.

## <a name="open-the-vm-with-remote-desktop"></a>Aprire la macchina virtuale tramite Desktop remoto

> [!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

## <a name="connect-to-sql-server-remotely"></a>Connettersi a SQL Server in modalità remota

In questa esercitazione è stato selezionato l'accesso **Pubblico** per la macchina virtuale ed è stata scelta l'**Autenticazione di SQL Server**. Queste impostazioni hanno configurato automaticamente la macchina virtuale in modo da consentire connessioni a SQL Server da qualsiasi client su Internet, purché abbiano l'account di accesso SQL corretto.

> [!NOTE]
> Se non è stato selezionato Pubblico durante il provisioning, sono necessari altri passaggi per accedere all'istanza di SQL Server tramite Internet. Per altre informazioni, vedere [Connettersi a una macchina virtuale di SQL Server](virtual-machines-windows-sql-connect.md).
> 
> 

Le sezioni seguenti illustrano come connettersi all'istanza di SQL Server nella VM da un computer diverso tramite Internet.

> [!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]
> 
> 

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sull'uso di SQL Server in Azure, vedere [SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md) e le [domande frequenti](virtual-machines-windows-sql-server-iaas-faq.md).

Per una panoramica su SQL Server in Macchine virtuali di Azure, guardare il video [Azure VM is the best platform for SQL Server 2016](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/Azure-VM-is-the-best-platform-for-SQL-Server-2016)(VM di Azure come piattaforma ottimale per SQL Server 2016).

[Esplorare il percorso di apprendimento](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) per SQL Server in macchine virtuali di Azure.
