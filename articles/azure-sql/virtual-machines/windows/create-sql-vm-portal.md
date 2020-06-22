---
title: Effettuare il provisioning della macchina virtuale con il portale di Azure
description: Questa guida dettagliata descrive le opzioni per la creazione di macchine virtuali Windows di SQL Server 2017 nel portale di Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: 1aff691f-a40a-4de2-b6a0-def1384e086e
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 11/07/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 0c534b87250a4cc17ec9dd67c7a4ab5af19b3384
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84032802"
---
# <a name="how-to-provision-a-windows-sql-server-virtual-machine-in-the-azure-portal"></a>Come effettuare il provisioning di una macchina virtuale Windows di SQL Server nel portale di Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Questa guida fornisce dettagli sulle diverse opzioni disponibili quando si crea una macchina virtuale Windows di SQL Server nel portale di Azure. Questo articolo descrive più opzioni di configurazione rispetto alla [Guida introduttiva alle VM di SQL Server](sql-vm-create-portal-quickstart.md), che approfondisce maggiormente una possibile attività di provisioning. 

Usare questa guida per creare la propria VM di SQL Server. In alternativa, è possibile usarla come riferimento per le opzioni disponibili nel portale di Azure.

> [!TIP]
> In caso di domande sulle macchine virtuali SQL Server, vedere le [domande frequenti](frequently-asked-questions-faq.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="sql-server-virtual-machine-gallery-images"></a><a id="select"></a>Immagini della raccolta di macchine virtuali di SQL Server

Quando si crea una macchina virtuale di SQL Server, è possibile selezionare una delle numerose immagini configurate in precedenza dalla raccolta delle macchine virtuali. La procedura seguente spiega come selezionare una delle immagini di SQL Server 2017.

1. Selezionare **Azure SQL** nel menu a sinistra nel portale di Azure. Se **Azure SQL** non è presente nell'elenco, selezionare **Tutti i servizi** e quindi digitare Azure SQL nella casella di ricerca. (Facoltativo) Selezionare la stella accanto ad **Azure SQL** per aggiungerlo ai Preferiti e come elemento del riquadro di spostamento sinistro. 
1. Selezionare **+ Aggiungi** per aprire la pagina **Selezionare l'opzione di distribuzione SQL**. È possibile visualizzare informazioni aggiuntive selezionando **Mostra dettagli**. 
1. Digitare `2017` nella casella di ricerca dell'immagine di SQL Server nel riquadro **Macchine virtuali SQL** e quindi selezionare **Free SQL Server License: SQL Server 2017 Developer on Windows Server 2016** (Licenza gratuita di SQL Server: SQL Server 2017 Developer in Windows Server 2016) nell'elenco a discesa. 


   ![Selezionare l'immagine della macchina virtuale SQL](./media/create-sql-vm-portal/select-sql-vm-image-portal.png)

   > [!TIP]
   > L'edizione per sviluppatori viene usata in questa procedura dettagliata perché è una versione completa di SQL Serve gratuita per i test di sviluppo. Si paga solo il costo dell'esecuzione della macchina virtuale. Per questa procedura dettagliata è tuttavia possibile scegliere e usare qualsiasi immagine. Per una descrizione delle immagini disponibili, vedere la [panoramica delle macchine virtuali di Microsoft SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo).

   > [!TIP]
   > I costi delle licenze per SQL Server sono incorporati nel prezzo al secondo della macchina virtuale creata e variano a seconda dell'edizione e dei core. Tuttavia, SQL l'edizione di Server per sviluppatori è gratuita per sviluppo/test (non per la produzione), mentre SQL Express è gratuito per carichi di lavoro leggeri (inferiori a 1 GB di memoria e a 10 GB di archiviazione). Si può anche usare l'opzione Bring Your Own License (BYOL) e procedere con il pagamento della sola VM. Tali nomi di immagine hanno il prefisso {BYOL}. 
   >
   > Per altre informazioni su queste opzioni, vedere [Pricing guidance for SQL Server Azure VMs](pricing-guidance.md) (Guida ai prezzi per le VM di SQL Server in Azure).


1. Selezionare **Crea**.


## <a name="1-configure-basic-settings"></a>1. Configurare le impostazioni di base


Nella scheda **Nozioni di base** specificare le informazioni seguenti:

* In **Dettagli progetto** verificare che sia selezionata la sottoscrizione corretta. 
*  Nella sezione **Gruppo di risorse** selezionare un gruppo di risorse esistente dall'elenco oppure scegliere **Crea nuovo** per creare un nuovo gruppo di risorse. Un gruppo di risorse è una raccolta di risorse correlate in Azure, ovvero macchine virtuali, account di archiviazione, reti virtuali e così via. 

    ![Subscription](./media/create-sql-vm-portal/basics-project-details.png)

  > [!NOTE]
  > L'uso di un nuovo gruppo di risorse risulta utile se si stanno solo eseguendo test o se si sta iniziando a usare le distribuzioni di SQL Server in Azure. Dopo aver completato il test, eliminare il gruppo di risorse per eliminare automaticamente la macchina virtuale e tutte le risorse associate a tale gruppo di risorse. Per altre informazioni sui gruppi di risorse, vedere [Panoramica di Azure Resource Manager](../../../active-directory-b2c/overview.md).


* In **Dettagli istanza**:
    1. Immettere un **nome macchina virtuale** univoco.  
    1. Scegliere una posizione per l'**area**. 
    1. Ai fini di questa guida lasciare **Opzioni di disponibilità** impostato su _La ridondanza dell'infrastruttura non è richiesta_. Per altre informazioni sulle opzioni di disponibilità, vedere [Disponibilità](../../../virtual-machines/windows/availability.md). 
    1. Nell'elenco **Immagine** selezionare _Free SQL Server License: SQL Server 2017 Developer in Windows Server 2016_.  
    1. Scegliere **Modifica dimensioni** per modificare le **dimensioni** della macchina virtuale e selezionare l'offerta **A2 Basic**. Assicurarsi di cancellare le risorse quando non sono più necessarie per evitare eventuali addebiti imprevisti. Per i carichi di lavoro di produzione, vedere le dimensioni e la configurazione di VM consigliate in [Procedure consigliate per le prestazioni per SQL Server in Macchine virtuali di Azure](performance-guidelines-best-practices.md).

    ![Dettagli dell'istanza](./media/create-sql-vm-portal/basics-instance-details.png)

> [!IMPORTANT]
> Il costo mensile stimato visualizzato nella finestra **Scegli una dimensione** non include i costi di licenza di SQL Server, Questa stima rappresenta solo il costo della VM. Per le edizioni Express e Developer di SQL Server, la stima indica il costo stimato totale. Per altre edizioni, vedere la [pagina dei prezzi delle macchine virtuali Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) e selezionare l'edizione di SQL Server di interesse. Vedere anche [Guida ai prezzi per le macchine virtuali SQL Server in Azure](pricing-guidance.md) e [Dimensioni per le macchine virtuali](../../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* In **Account amministratore** indicare un nome utente e una password. La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](../../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

   ![Account amministratore](./media/create-sql-vm-portal/basics-administrator-account.png)

* In **Regole porta in ingresso** scegliere **Consenti porte selezionate**, quindi selezionare **RDP (3389)** dall'elenco a discesa. 

   ![Regole porta in ingresso](./media/create-sql-vm-portal/basics-inbound-port-rules.png)


## <a name="2-configure-optional-features"></a>2. Configurare le funzionalità facoltative

### <a name="disks"></a>Dischi

Nella scheda **Dischi** configurare le opzioni dei dischi. 

* In **Tipo di disco del sistema operativo** selezionare nell'elenco a discesa il tipo di disco desiderato per il sistema operativo. Per i sistemi di produzione è consigliato il livello Premium che tuttavia non è disponibile per una macchina virtuale di tipo Basic. Per utilizzare SSD Premium, modificare le dimensioni della macchina virtuale. 
* In **Avanzate** selezionare **Sì** sotto **Usa il servizio Managed Disks**.

   > [!NOTE]
   > Per SQL Server è consigliabile usare Managed Disks. Managed Disks gestisce automaticamente le risorse di archiviazione. Inoltre, quando le macchine virtuali con Managed Disks sono nello stesso set di disponibilità, Azure distribuisce le risorse di archiviazione in modo da garantire la ridondanza appropriata. Per altre informazioni, vedere [Panoramica del servizio Managed Disks](../../../virtual-machines/windows/managed-disks-overview.md). Per informazioni dettagliate sull'uso di Managed Disks in un set di disponibilità, vedere [Usare Managed Disks per le macchine virtuali nel set di disponibilità](../../../virtual-machines/windows/manage-availability.md).

![Impostazioni dei dischi della macchina virtuale SQL](./media/create-sql-vm-portal/azure-sqlvm-disks.png)
  
  
### <a name="networking"></a>Rete

Nella scheda **Rete** configurare le impostazioni di rete. 

* Creare una nuova **rete virtuale** oppure usare una rete virtuale esistente per la macchina virtuale di SQL Server. Designare anche una **subnet**. 

* In **Gruppo di sicurezza di rete della scheda di interfaccia di rete** selezionare un gruppo di sicurezza di base o il gruppo di sicurezza avanzato. Scegliendo l'opzione di base, è possibile selezionare le porte in ingresso per la macchina virtuale di SQL Server (gli stessi valori configurati nella scheda **Generale**). Selezionando l'opzione avanzata, è possibile scegliere un gruppo di sicurezza di rete esistente o crearne uno nuovo. 

* È possibile apportare altre modifiche alle impostazioni di rete o mantenere i valori predefiniti.

![Impostazioni di rete della macchina virtuale SQL](./media/create-sql-vm-portal/azure-sqlvm-networking.png)

#### <a name="monitoring"></a>Monitoraggio

Nella scheda **Monitoraggio** configurare il monitoraggio e l'arresto automatico. 

* Azure abilita l'opzione **Diagnostica di avvio** per impostazione predefinita con lo stesso account di archiviazione designato per la macchina virtuale. Qui è possibile modificare queste impostazioni, oltre ad abilitare **Diagnostica sistema operativo guest**. 
* In questa scheda è possibile abilitare anche **Identità gestita assegnata dal sistema** e l'**arresto automatico**. 

![Impostazioni di gestione della macchina virtuale SQL](./media/create-sql-vm-portal/azure-sqlvm-management.png)


## <a name="3-configure-sql-server-settings"></a>3. Configurare le impostazioni di SQL Server

Nella scheda **Impostazioni di SQL Server** configurare le impostazioni e le ottimizzazioni specifiche per SQL Server. Le impostazioni che è possibile configurare per SQL Server includono le seguenti:

| Impostazione |
| --- |
| [Connettività](#connectivity) |
| [autenticazione](#authentication) |
| [Integrazione di Azure Key Vault](#azure-key-vault-integration) |
| [Configurazione dell'archiviazione](#storage-configuration) |
| [Applicazione automatica delle patch](#automated-patching) |
| [Backup automatico](#automated-backup) |
| [Machine Learning Services](#machine-learning-services) |


### <a name="connectivity"></a>Connettività

In **Connettività SQL**specificare il tipo di accesso da assegnare all'istanza di SQL Server nella VM. Ai fini di questa procedura dettagliata, selezionare **Pubblica (Internet)** per consentire connessioni a SQL Server da macchine virtuali o servizi su Internet. Se si seleziona questa opzione, Azure configura automaticamente il firewall e il gruppo di sicurezza di rete per consentire il traffico sulla porta selezionata.

> [!TIP]
> Per impostazione predefinita, SQL Server è in ascolto su una porta nota, la porta **1433**. Per aumentare la sicurezza, modificare la porta nella finestra di dialogo precedente impostando l'ascolto su una porta non predefinita, ad esempio la 1401. Se si cambia la porta, è necessario usare tale porta per la connessione da qualsiasi strumento client, ad esempio SSMS.

![Sicurezza della macchina virtuale SQL](./media/create-sql-vm-portal/azure-sqlvm-security.png)

Per connettersi a SQL Server tramite Internet, è necessario abilitare anche l'Autenticazione di SQL Server, illustrata nella sezione successiva.

Se si preferisce non abilitare le connessioni al motore di database tramite Internet, scegliere una delle opzioni seguenti:

* **Locale (solo all'interno della macchina virtuale)** per consentire le connessioni a SQL Server solo dalla VM.
* **Privata (solo all'interno della rete virtuale)** per consentire le connessioni a SQL Server da computer o servizi nella stessa rete virtuale.

È in genere possibile migliorare la sicurezza scegliendo la connettività più restrittiva consentita dallo scenario specifico. Tutte le opzioni possono essere comunque protette tramite le regole del gruppo di sicurezza di rete e l'Autenticazione di SQL o Windows. È possibile modificare il gruppo di sicurezza di rete dopo la creazione della VM. Per altre informazioni, vedere [Considerazioni relative alla sicurezza per SQL Server in Macchine virtuali di Azure](security-considerations-best-practices.md).



### <a name="authentication"></a>Authentication

Se è necessaria l'autenticazione di SQL Server, nella scheda **Impostazioni di SQL Server** fare clic su **Abilita** in **Autenticazione SQL**.

![Autenticazione di SQL Server](./media/create-sql-vm-portal/azure-sqlvm-authentication.png)

> [!NOTE]
> Se si prevede di accedere a SQL Server tramite Internet (l'opzione di connettività Pubblica), è necessario abilitare l'autenticazione di SQL in questa area. L'accesso pubblico a SQL Server richiede l'uso dell'autenticazione di SQL.

Se si abilita l'autenticazione di SQL Server, specificare un **Nome di accesso** e una **Password**. Questo ID di accesso viene configurato come account di accesso di Autenticazione di SQL Server e membro del ruolo predefinito del server **sysadmin**. Per altre informazioni sulle modalità di autenticazione, vedere [Scegliere una modalità di autenticazione](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode).

Se non si abilita l'autenticazione di SQL Server, è possibile usare l'account amministratore locale nella macchina virtuale per connettersi all'istanza di SQL Server.


### <a name="azure-key-vault-integration"></a>Integrazione dell'insieme di credenziali delle chiavi di Azure

Per archiviare i segreti di sicurezza in Azure per la crittografia, selezionare **Impostazioni di SQL Server** e scorrere verso il basso fino a **Integrazione di Azure Key Vault**. Selezionare **Abilita** e inserire le informazioni richieste. 

![Integrazione dell'insieme di credenziali delle chiavi di Azure](./media/create-sql-vm-portal/azure-sqlvm-akv.png)

La tabella seguente include l'elenco dei parametri necessari per configurare l'integrazione dell'insieme di credenziali delle chiavi di Azure.

| PARAMETER | DESCRIZIONE | ESEMPIO |
| --- | --- | --- |
| **URL dell'insieme di credenziali delle chiavi** |Percorso dell'insieme di credenziali delle chiavi. |https:\//contosokeyvault.vault.azure.net/ |
| **Nome dell'entità** |Nome dell'entità servizio di Azure Active Directory. È detto anche ID client. |fde2b411-33d5-4e11-af04eb07b669ccf2 |
| **Segreto dell'entità** |Nome dell'entità servizio di Azure Active Directory. È detto anche Segreto client. |9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM= |
| **Nome credenziali** |**Nome credenziali**: L'integrazione dell'insieme di credenziali delle chiavi di Azure crea le credenziali all'interno di SQL Server, consentendo alla VM di accedere all'insieme di credenziali delle chiavi. Scegliere un nome per la credenziale. |mycred1 |

Per altre informazioni, vedere [Configurare l'integrazione dell'insieme di credenziali delle chiavi di Azure per SQL Server in macchine virtuali di Azure (Resource Manager)](azure-key-vault-integration-configure.md).

### <a name="storage-configuration"></a>Configurazione dell'archiviazione

Nella scheda **Impostazioni di SQL Server**, in **Configurazione dell'archiviazione** selezionare **Cambia configurazione** per aprire la pagina di configurazione dell'archiviazione ottimizzata per le prestazioni e specificare i requisiti dell'archiviazione.

![Configurazione dell'archiviazione della macchina virtuale SQL](./media/create-sql-vm-portal/sql-vm-storage-configuration-provisioning.png)

In **Ottimizzazione dell'archiviazione**selezionare una delle opzioni seguenti:

* **Generale** : è l'impostazione predefinita e supporta la maggior parte dei carichi di lavoro.
* **Elaborazione transazionale**: ottimizza l'archiviazione per carichi di lavoro OLTP di database tradizionali.
* **Data warehousing** : ottimizza l'archiviazione per i carichi di lavoro di analisi e creazione di report.

![Configurazione dell'archiviazione della macchina virtuale SQL](./media/create-sql-vm-portal/sql-vm-storage-configuration.png)

È possibile lasciare i valori predefiniti oppure modificare manualmente la topologia di archiviazione in base alle esigenze relative alle operazioni di I/O al secondo. Per altre informazioni, vedere [Configurazione dell'archiviazione](storage-configuration.md). 

### <a name="sql-server-license"></a>Licenza di SQL Server
I clienti Software Assurance possono utilizzare il [Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/) per usare la propria licenza di SQL Server e risparmiare sulle risorse. 

![Licenza per la macchina virtuale SQL](./media/create-sql-vm-portal/azure-sqlvm-license.png)

### <a name="automated-patching"></a>Applicazione automatica delle patch

**Automated patching** è abilitata per impostazione predefinita. Questa opzione consente ad Azure di applicare automaticamente le patch a SQL Server e al sistema operativo. Specificare un giorno della settimana, un'ora e una durata per la finestra di manutenzione. Durante la finestra di manutenzione Azure esegue l'applicazione delle patch. La pianificazione dell'ora, la finestra di manutenzione usa le impostazioni locali della macchina virtuale. Se non si vuole consentire ad Azure di applicare automaticamente le patch per SQL Server e per il sistema operativo, fare clic su **Disabilita**.  

![Applicazione automatica delle patch di SQL alla macchina virtuale](./media/create-sql-vm-portal/azure-sqlvm-automated-patching.png)

Per altre informazioni, vedere [Applicazione automatica delle patch per SQL Server nelle macchine virtuali di Azure (Resource Manager)](automated-patching.md).

### <a name="automated-backup"></a>Backup automatico

Abilitare i backup automatici dei database per tutti i database in **Backup automatico**. L'opzione Backup automatico è disabilitata per impostazione predefinita.

Quando si abilita il backup automatico di SQL è possibile configurare le impostazioni seguenti:

* Periodo di conservazione (giorni) per i backup
* Account di archiviazione da usare per i backup
* Opzione di crittografia e password per i backup
* Backup dei database di sistema
* Pianificazione dei backup

Per crittografare il backup, fare clic su **Abilita**. Specificare quindi la **Password**. Azure crea un certificato per crittografare i backup e usa la password specificata per proteggere il certificato. Per impostazione predefinita, la pianificazione viene impostata automaticamente, ma è possibile creare una pianificazione manuale selezionando **Manuale**. 

![Backup automatizzati della macchina virtuale SQL](./media/create-sql-vm-portal/automated-backup.png)

Per altre informazioni, vedere [Backup automatizzato per SQL Server in Macchine virtuali di Azure](automated-backup-sql-2014.md).


### <a name="machine-learning-services"></a>Machine Learning Services

È possibile abilitare [Machine Learning Services](/sql/advanced-analytics/). Questa opzione consente di usare Machine Learning con Python e R in SQL Server 2017. Selezionare **Abilita** nella finestra **Impostazioni di SQL Server**.


## <a name="4-review--create"></a>4. Rivedi e crea

Nella scheda **Rivedi e crea** esaminare il riepilogo e fare clic su **Crea** per creare SQL Server, il gruppo di risorse e le risorse specificati per questa VM.

È possibile monitorare la distribuzione dal portale di Azure. Il pulsante **Notifiche** nella parte superiore della schermata mostra lo stato di base della distribuzione.

> [!NOTE]
> Per dare un'idea dei tempi di distribuzione, è stata distribuita una macchina virtuale di SQL nell'area Stati Uniti orientali con le impostazioni predefinite. Il completamento della distribuzione di prova ha richiesto circa 12 minuti. È possibile che i tempi di distribuzione specifici siano minori o maggiori in base all'area in cui ci si trova e alle impostazioni selezionate.

## <a name="open-the-vm-with-remote-desktop"></a><a id="remotedesktop"></a> Aprire la VM con Desktop remoto

Seguire questa procedura per connettersi alla macchina virtuale SQL Server con Desktop remoto:

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Dopo essersi connessi alla macchina virtuale di SQL Server, è possibile avviare SQL Server Management Studio e connettersi con l'autenticazione di Windows usando le credenziali di amministratore locale. Se è stata abilitata l'autenticazione di SQL Server, è anche possibile connettersi con l'Autenticazione SQL usando l'account di accesso e la password di SQL configurati durante il provisioning.

L'accesso alla macchina virtuale consente di modificare direttamente le impostazioni di SQL Server in base ai requisiti specifici. Ad esempio, è possibile configurare le impostazioni del firewall o modificare le impostazioni di configurazione di SQL Server.

## <a name="connect-to-sql-server-remotely"></a><a id="connect"></a> Connettersi a SQL Server in modalità remota

In questa procedura dettagliata è stato selezionato l'accesso **Pubblico** per la macchina virtuale ed è stata scelta l'**Autenticazione di SQL Server**. Queste impostazioni hanno configurato automaticamente la macchina virtuale in modo da consentire connessioni a SQL Server da qualsiasi client su Internet, purché abbiano l'account di accesso SQL corretto.

> [!NOTE]
> Se durante il provisioning non è stata selezionata l'opzione Pubblica, è possibile modificare le impostazioni di connettività di SQL tramite il portale dopo il provisioning. Per altre informazioni, vedere la sezione su come [modificare le impostazioni di connettività di SQL](ways-to-connect-to-sql.md#change).

Le sezioni seguenti illustrano come connettersi tramite internet all'istanza di VM di SQL Server.

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

  > [!NOTE]
  > Questo esempio usa la porta comune 1433. Sarà tuttavia necessario modificare questo valore se durante la distribuzione della macchina virtuale di SQL Server è stata specificata una porta diversa (ad esempio, 1401). 


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso di SQL Server in Azure, vedere [SQL Server in macchine virtuali di Azure](sql-server-on-azure-vm-iaas-what-is-overview.md) e le [domande frequenti](frequently-asked-questions-faq.md).
