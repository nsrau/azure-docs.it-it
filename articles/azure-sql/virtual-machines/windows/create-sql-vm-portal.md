---
title: Effettuare il provisioning di una macchina virtuale Windows con il portale di Azure
description: Questa guida illustra le opzioni disponibili per l'uso del portale di Azure per eseguire il provisioning di SQL Server in una macchina virtuale Windows.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
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
ms.openlocfilehash: e315f49cb0b78e13c4b6132f844397d1261ff0f9
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88652017"
---
# <a name="how-to-use-the-azure-portal-to-provision-a-windows-virtual-machine-with-sql-server"></a>Come usare la portale di Azure per eseguire il provisioning di una macchina virtuale Windows con SQL Server

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Questa guida illustra le opzioni disponibili per l'uso del portale di Azure per eseguire il provisioning di SQL Server in una macchina virtuale (VM) Windows. Questo articolo illustra altre opzioni di configurazione rispetto alla [Guida introduttiva alla macchina virtuale SQL Server](sql-vm-create-portal-quickstart.md), che è incentrata su una singola configurazione. 

Usare questa guida per creare la propria VM di SQL Server. In alternativa, è possibile usarla come riferimento per le opzioni disponibili nel portale di Azure.

> [!TIP]
> In caso di domande sulle macchine virtuali SQL Server, vedere le [domande frequenti](frequently-asked-questions-faq.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="sql-server-virtual-machine-gallery-images"></a><a id="select"></a>Immagini della raccolta di macchine virtuali di SQL Server

Quando si crea una macchina virtuale di SQL Server, è possibile selezionare una delle numerose immagini configurate in precedenza dalla raccolta delle macchine virtuali. La procedura seguente spiega come selezionare una delle immagini di SQL Server 2017.

1. Selezionare **Azure SQL** nel menu a sinistra nel portale di Azure. Se **Azure SQL** non è presente nell'elenco, selezionare **Tutti i servizi** e quindi digitare *Azure SQL* nella casella di ricerca. 

   È anche possibile selezionare la stella accanto a **SQL di Azure** per salvarla come preferita e aggiungerla come elemento nel menu di spostamento a sinistra. 

1. Selezionare **+ Aggiungi** per aprire la pagina **Selezionare l'opzione di distribuzione SQL**. È possibile visualizzare informazioni aggiuntive selezionando **Mostra dettagli**. 
1. Digitare *2017* nella casella di ricerca immagine SQL Server nel riquadro **macchine virtuali SQL** e quindi selezionare **SQL Server licenza gratuita: SQL Server Developer 2017 su Windows Server 2016** dall'elenco a discesa. 

   ![Selezionare l'immagine della macchina virtuale SQL](./media/create-sql-vm-portal/select-sql-vm-image-portal.png)

   > [!TIP]
   > L'edizione Developer viene usata in questo articolo perché è un'edizione gratuita completa di SQL Server per i test di sviluppo. Si paga solo il costo dell'esecuzione della macchina virtuale. Per questa procedura dettagliata è tuttavia possibile scegliere e usare qualsiasi immagine. Per una descrizione delle immagini disponibili, vedere la [panoramica delle macchine virtuali di Microsoft SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo).

   > [!TIP]
   > I costi delle licenze per SQL Server sono incorporati nel prezzo al secondo della macchina virtuale creata e variano a seconda dell'edizione e dei core. Tuttavia, SQL Server Developer edizione è gratuita per lo sviluppo e il test, non per la produzione. Inoltre, SQL Express è gratuito per carichi di lavoro leggeri (inferiori a 1 GB di memoria, meno di 10 GB di spazio di archiviazione). Si può anche usare l'opzione Bring Your Own License (BYOL) e procedere con il pagamento della sola VM. Tali nomi di immagine hanno il prefisso {BYOL}. 
   >
   > Per altre informazioni su queste opzioni, vedere [Pricing guidance for SQL Server Azure VMs](pricing-guidance.md) (Guida ai prezzi per le VM di SQL Server in Azure).


1. Selezionare **Crea**.


## <a name="1-configure-basic-settings"></a>1. Configurare le impostazioni di base

Nella scheda **Nozioni di base** specificare le informazioni seguenti:

* In **Dettagli progetto** verificare che sia selezionata la sottoscrizione corretta. 
* Nella sezione **Gruppo di risorse** selezionare un gruppo di risorse esistente dall'elenco oppure scegliere **Crea nuovo** per creare un nuovo gruppo di risorse. Un gruppo di risorse è una raccolta di risorse correlate in Azure, ovvero macchine virtuali, account di archiviazione, reti virtuali e così via. 

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

* In **account amministratore**specificare un nome utente e una password. La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](../../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

   ![Account amministratore](./media/create-sql-vm-portal/basics-administrator-account.png)

* In **Regole porta in ingresso** scegliere **Consenti porte selezionate**, quindi selezionare **RDP (3389)** dall'elenco a discesa. 

   ![Regole porta in ingresso](./media/create-sql-vm-portal/basics-inbound-port-rules.png)


## <a name="2-configure-optional-features"></a>2. Configurare le funzionalità facoltative

### <a name="disks"></a>Dischi

Nella scheda **Dischi** configurare le opzioni dei dischi. 

* In **Tipo di disco del sistema operativo** selezionare nell'elenco a discesa il tipo di disco desiderato per il sistema operativo. Per i sistemi di produzione è consigliato il livello Premium che tuttavia non è disponibile per una macchina virtuale di tipo Basic. Per usare un SSD Premium, modificare le dimensioni della macchina virtuale. 
* In **Avanzate** selezionare **Sì** sotto **Usa il servizio Managed Disks**.

   > [!NOTE]
   > Per SQL Server è consigliabile usare Managed Disks. Managed Disks gestisce automaticamente le risorse di archiviazione. Inoltre, quando le macchine virtuali con Managed Disks sono nello stesso set di disponibilità, Azure distribuisce le risorse di archiviazione in modo da garantire la ridondanza appropriata. Per altre informazioni, vedere [Panoramica del servizio Managed Disks](../../../virtual-machines/managed-disks-overview.md). Per informazioni dettagliate sull'uso di Managed Disks in un set di disponibilità, vedere [Usare Managed Disks per le macchine virtuali nel set di disponibilità](../../../virtual-machines/windows/manage-availability.md).

![Impostazioni dei dischi della macchina virtuale SQL](./media/create-sql-vm-portal/azure-sqlvm-disks.png)
  
  
### <a name="networking"></a>Rete

Nella scheda **Rete** configurare le impostazioni di rete. 

* Creare una nuova **rete virtuale** o usare una rete virtuale esistente per la VM SQL Server. Designare anche una **subnet**. 

* In **gruppo di sicurezza di rete NIC**selezionare un gruppo di sicurezza di base o il gruppo di sicurezza avanzato. Scegliendo l'opzione di base è possibile selezionare le porte in ingresso per la macchina virtuale SQL Server che corrispondono ai valori configurati nella scheda di **base** . selezionando l'opzione avanzate è possibile scegliere un gruppo di sicurezza di rete esistente o crearne uno nuovo. 

* È possibile apportare altre modifiche alle impostazioni di rete o mantenere i valori predefiniti.

![Impostazioni di rete della macchina virtuale SQL](./media/create-sql-vm-portal/azure-sqlvm-networking.png)

#### <a name="monitoring"></a>Monitoraggio

Nella scheda **monitoraggio** configurare il monitoraggio e l'arresto automatico. 

* Azure abilita l'opzione **Diagnostica di avvio** per impostazione predefinita con lo stesso account di archiviazione designato per la macchina virtuale. In questa scheda è possibile modificare queste impostazioni e abilitare la **diagnostica Guest del sistema operativo**. 
* È anche possibile abilitare l' **identità gestita assegnata dal sistema** e l' **arresto automatico** in questa scheda. 

![Impostazioni di gestione della macchina virtuale SQL](./media/create-sql-vm-portal/azure-sqlvm-management.png)


## <a name="3-configure-sql-server-settings"></a>3. Configurare le impostazioni di SQL Server

Nella scheda **Impostazioni di SQL Server** configurare le impostazioni e le ottimizzazioni specifiche per SQL Server. Per SQL Server, è possibile configurare le impostazioni seguenti:

- [Connettività](#connectivity)
- [autenticazione](#authentication)
- [Integrazione di Azure Key Vault](#azure-key-vault-integration)
- [Configurazione dell'archiviazione](#storage-configuration)
- [Applicazione automatica delle patch](#automated-patching)
- [Backup automatico](#automated-backup)
- [Machine Learning Services](#machine-learning-services)


### <a name="connectivity"></a>Connettività

In **Connettività SQL**specificare il tipo di accesso da assegnare all'istanza di SQL Server nella VM. Ai fini di questa procedura dettagliata, selezionare **Pubblica (Internet)** per consentire connessioni a SQL Server da macchine virtuali o servizi su Internet. Se si seleziona questa opzione, Azure configura automaticamente il firewall e il gruppo di sicurezza di rete per consentire il traffico sulla porta selezionata.

> [!TIP]
> Per impostazione predefinita, SQL Server è in ascolto su una porta nota, la porta **1433**. Per aumentare la sicurezza, modificare la porta nella finestra di dialogo precedente impostando l'ascolto su una porta non predefinita, ad esempio la 1401. Se si modifica la porta, è necessario connettersi utilizzando la porta da qualsiasi strumento client, ad esempio SQL Server Management Studio (SSMS).

![Sicurezza della macchina virtuale SQL](./media/create-sql-vm-portal/azure-sqlvm-security.png)

Per connettersi a SQL Server tramite Internet, è necessario abilitare anche l'Autenticazione di SQL Server, illustrata nella sezione successiva.

Se si preferisce non abilitare le connessioni al motore di database tramite Internet, scegliere una delle opzioni seguenti:

* **Locale (solo all'interno della macchina virtuale)** per consentire le connessioni a SQL Server solo dalla VM.
* **Privata (solo all'interno della rete virtuale)** per consentire le connessioni a SQL Server da computer o servizi nella stessa rete virtuale.

È in genere possibile migliorare la sicurezza scegliendo la connettività più restrittiva consentita dallo scenario specifico. Tuttavia, tutte le opzioni sono entità a protezione diretta tramite le regole del gruppo di sicurezza di rete (NSG) e l'autenticazione di SQL/Windows. È possibile modificare il NSG dopo la creazione della macchina virtuale. Per altre informazioni, vedere [Considerazioni relative alla sicurezza per SQL Server in Macchine virtuali di Azure](security-considerations-best-practices.md).

### <a name="authentication"></a>Authentication

Se è necessario SQL Server autenticazione, selezionare **Abilita** in **autenticazione SQL** nella scheda **Impostazioni SQL Server** .

![Autenticazione di SQL Server](./media/create-sql-vm-portal/azure-sqlvm-authentication.png)

> [!NOTE]
> Se si prevede di accedere a SQL Server tramite Internet (opzione di connettività pubblica), è necessario abilitare l'autenticazione SQL qui. L'accesso pubblico al SQL Server richiede l'autenticazione SQL.

Se si abilita l'autenticazione di SQL Server, specificare un **Nome di accesso** e una **Password**. Questo nome di account di accesso è configurato come account di accesso con autenticazione SQL Server e membro del ruolo predefinito del server **sysadmin** . Per altre informazioni sulle modalità di autenticazione, vedere [Scegliere una modalità di autenticazione](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode).

Se si preferisce non abilitare l'autenticazione SQL Server, è possibile usare l'account amministratore locale nella macchina virtuale per connettersi all'istanza di SQL Server.

### <a name="azure-key-vault-integration"></a>Integrazione dell'insieme di credenziali delle chiavi di Azure

Per archiviare i segreti di sicurezza in Azure per la crittografia, selezionare **Impostazioni di SQL Server** e scorrere verso il basso fino a **Integrazione di Azure Key Vault**. Selezionare **Abilita** e inserire le informazioni richieste. 

![Integrazione dell'insieme di credenziali delle chiavi di Azure](./media/create-sql-vm-portal/azure-sqlvm-akv.png)

Nella tabella seguente sono elencati i parametri necessari per configurare l'integrazione Azure Key Vault (AKV).

| PARAMETER | DESCRIZIONE | ESEMPIO |
| --- | --- | --- |
| **URL dell'insieme di credenziali delle chiavi** |Percorso dell'insieme di credenziali delle chiavi. |`https://contosokeyvault.vault.azure.net/` |
| **Nome dell'entità** |Nome dell'entità servizio di Azure Active Directory. È detto anche ID client. |`fde2b411-33d5-4e11-af04eb07b669ccf2` |
| **Segreto dell'entità** |Nome dell'entità servizio di Azure Active Directory. È detto anche Segreto client. |`9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=` |
| **Nome credenziali** |**Nome credenziale**: l'integrazione con AKV crea una credenziale all'interno SQL Server e consente alla macchina virtuale di accedere all'insieme di credenziali delle chiavi. Scegliere un nome per la credenziale. |`mycred1` |

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

Se sei un cliente di Software Assurance, puoi usare il [vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/) per ottenere la tua licenza SQL Server e risparmiare sulle risorse. 

![Licenza per la macchina virtuale SQL](./media/create-sql-vm-portal/azure-sqlvm-license.png)

### <a name="automated-patching"></a>Applicazione automatica delle patch

**Automated patching** è abilitata per impostazione predefinita. Questa opzione consente ad Azure di applicare automaticamente le patch a SQL Server e al sistema operativo. Specificare un giorno della settimana, un'ora e una durata per la finestra di manutenzione. Durante la finestra di manutenzione Azure esegue l'applicazione delle patch. La pianificazione della finestra di manutenzione usa le impostazioni locali della macchina virtuale. Se non si vuole che Azure esegua automaticamente la patch SQL Server e il sistema operativo, selezionare **Disabilita**.  

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

Per crittografare il backup, selezionare **Abilita**. Specificare quindi la **Password**. Azure crea un certificato per crittografare i backup e usa la password specificata per proteggere il certificato. Per impostazione predefinita, la pianificazione viene impostata automaticamente, ma è possibile creare una pianificazione manuale selezionando **Manuale**. 

![Backup automatizzati della macchina virtuale SQL](./media/create-sql-vm-portal/automated-backup.png)

Per altre informazioni, vedere [Backup automatizzato per SQL Server in Macchine virtuali di Azure](automated-backup-sql-2014.md).


### <a name="machine-learning-services"></a>Machine Learning Services

È possibile abilitare [Machine Learning Services](/sql/advanced-analytics/). Questa opzione consente di usare Machine Learning con Python e R in SQL Server 2017. Selezionare **Abilita** nella finestra **Impostazioni di SQL Server**.


## <a name="4-review--create"></a>4. Rivedi e crea

Nella scheda **Verifica + crea** :
1. Esaminare il riepilogo.
1. Selezionare **Crea** per creare il SQL Server, il gruppo di risorse e le risorse specificati per questa macchina virtuale.

È possibile monitorare la distribuzione dal portale di Azure. Il pulsante **Notifiche** nella parte superiore della schermata mostra lo stato di base della distribuzione.

> [!NOTE]
> Esempio di tempo per la distribuzione di una macchina virtuale SQL Server da parte di Azure: una macchina virtuale di SQL Server di test con provisioning nell'area Stati Uniti orientali con impostazioni predefinite richiede circa 12 minuti per il completamento. È possibile che si verifichino tempi di distribuzione più rapidi o più lenti in base all'area geografica e alle impostazioni selezionate.

## <a name="open-the-vm-with-remote-desktop"></a><a id="remotedesktop"></a> Aprire la VM con Desktop remoto

Usare la procedura seguente per connettersi alla macchina virtuale SQL Server con Remote Desktop Protocol (RDP):

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
