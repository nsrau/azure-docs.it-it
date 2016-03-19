<properties
	pageTitle="Effettuare il provisioning di una macchina virtuale di SQL Server in Gestione risorse di Azure (GUI) | Microsoft Azure"
	description="Creare una macchina virtuale di SQL Server in modalità Gestione risorse di Azure Questa esercitazione usa principalmente l'interfaccia utente e gli strumenti invece dello scripting."
	services="virtual-machines"
	documentationCenter="na"
	authors="MikeRayMSFT"
    editor=""
	manager="jeffreyg"
	tags="azure-service-management" />


<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="12/22/2015"
	ms.author="mikeray" />

# Effettuare il provisioning di una macchina virtuale di SQL Server in Gestione risorse di Azure

> [AZURE.SELECTOR]
- [Classic portal](virtual-machines-provision-sql-server.md)
- [PowerShell](virtual-machines-sql-server-create-vm-with-powershell.md)
- [Azure Resource Manager portal](virtual-machines-sql-server-provision-resource-manager.md)

<br/>

>[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modello di distribuzione classica.

Questa esercitazione end-to-end illustra come effettuare il provisioning di una macchina virtuale di Azure nel portale usando il modello di Gestione risorse di Azure e configurare SQL Server da un modello nella raccolta di Azure.

La raccolta di macchine virtuali (VM) di Azure include numerose immagini che contengono Microsoft SQL Server. È possibile selezionare una delle immagini di VM disponibili nella raccolta ed effettuare il provisioning della VM nell'ambiente Azure con pochi clic.

In questa esercitazione si apprenderà come:

- [Connettersi al portale di Azure ed effettuare il provisioning di un'immagine della VM di SQL dalla raccolta con il modello di distribuzione di Gestione risorse](#Provision)

- [Configurare le impostazioni della macchina virtuale e di SQL Server](#ConfigureVM)

- [Aprire la macchina virtuale tramite Desktop remoto](#Open)

- [Connettersi all'istanza di SQL Server con SQL Server Management Studio in un altro computer](#Connect)

- [Passaggi successivi](#Next)

In questa esercitazione si presuppone che sia già disponibile un account Azure. Se non si dispone di un account Azure, provare la [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a id="Provision">Effettuare il provisioning di un'immagine della VM di SQL dalla raccolta con il modello di distribuzione di Gestione risorse

1. Accedere al [portale di Azure](https://portal.azure.com) con il proprio account.
1. Nel portale di Azure fare clic su **+Nuovo**. Nel portale si aprirà il pannello **Nuovo**. I modelli di VM di SQL Server sono nel gruppo **Calcolo** del Marketplace.

1. Nel pannello **Nuovo** fare clic su **Calcolo**.
1. Per visualizzare tutti i tipi di risorse nel pannello **Calcolo**, fare clic su **Visualizza tutto**. <br/> ![Pannello Calcolo di Azure](./media/virtual-machines-sql-server-provision-resource-manager/azure-compute-blade.png) <br/>
1. In **Server database** fare clic su **SQL Server** per visualizzare tutti i modelli disponibili per SQL Server. Potrebbe essere necessario scorrere verso il basso per trovare **Server database**.
1. 	Ogni modello identifica una versione di SQL Server e un sistema operativo. Selezionare una di queste immagini dall'elenco per visualizzare un pannello contenente i dettagli.
1.	Il pannello dei dettagli include una descrizione dell'immagine di questa macchina virtuale e consente di selezionare un modello di distribuzione. In **Selezionare un modello di distribuzione** selezionare **Gestione risorse** e fare clic su **Crea**. <br/> ![Pannello Calcolo di Azure](./media/virtual-machines-sql-server-provision-resource-manager/azure-compute-sql-deployment-model.png) <br/>

## <a id="ConfigureVM"> Configurare la macchina virtuale
Nel portale di Azure sono disponibili cinque pannelli per configurare una macchina virtuale di SQL Server.

1.	Configurare le impostazioni di base
1.	Scegliere le dimensioni della macchina virtuale
1.	Configurare le impostazioni della macchina virtuale
1.	Configurare SQL Server
1.	Esaminare il riepilogo

## 1\. Configurare le impostazioni di base
Nel pannello **Crea macchina virtuale** in **Nozioni di base** specificare le informazioni seguenti:

* Nella casella **Nome** digitare un nome univoco della macchina virtuale.
* Nella casella **Nome utente** digitare un nome utente univoco per l'account amministratore locale della macchina virtuale. Questo account sarà anche membro del ruolo predefinito del server sysadmin di SQL Server.
* Nella casella **Password** digitare una password complessa.
* Se sono disponibili più sottoscrizioni, verificare che la sottoscrizione sia corretta per la VM che si sta creando.
* Nella casella **Gruppo di risorse** digitare un nome per il gruppo di risorse. In alternativa,scegliere **Seleziona esistente** per usare un gruppo di risorse esistente. Un gruppo di risorse è una raccolta di servizi correlati in Azure. Per informazioni sui gruppi di risorse, vedere [Panoramica di Gestione risorse di Microsoft Azure](../resource-group-overview.md). Verificare che il valore in **Percorso** sia corretto per le proprie esigenze.
* Fare clic su **OK** per salvare le impostazioni. <br/>

>![Nozioni di base di Gestione risorse di Azure per SQL](./media/virtual-machines-sql-server-provision-resource-manager/azure-sql-arm-basic.png) <br/>

## 2\. Scegliere le dimensioni della macchina virtuale
Nel pannello **Crea macchina virtuale** scegliere una dimensione di macchina virtuale in **Dimensioni**. Il portale di Azure visualizzerà le dimensioni consigliate. Per altre informazioni sulle dimensioni di macchine virtuali, vedere [Dimensioni delle macchine virtuali](virtual-machines-size-specs.md). Le dimensioni sono basate sul modello selezionato. Le dimensioni stimano il costo mensile per l'esecuzione della VM. Selezionare le dimensioni della macchina virtuale per il server. Per considerazioni sulle dimensioni delle VM di SQL Server, vedere [Procedure consigliate per le prestazioni per SQL Server in Macchine virtuali di Azure](virtual-machines-sql-server-performance-best-practices.md).

## 3\. Configurare le impostazioni della macchina virtuale
Nel pannello **Crea macchina virtuale** configurare l'archiviazione di Azure, la rete e il monitoraggio per la macchina virtuale in **Impostazioni**.

- In **Archiviazione** specificare un tipo di disco. Archiviazione Premium è l'impostazione consigliata per i carichi di lavoro di produzione.

>[AZURE.NOTE] L'opzione Archiviazione Premium è abilitata per impostazione predefinita. Reimposta automaticamente la macchina virtuale alle dimensioni che supportano Archiviazione Premium. Se si disabilita Archiviazione Premium, verranno usate le dimensioni della macchina virtuale selezionate in precedenza.

- In **Account di archiviazione** è possibile accettare il nome dell'account di archiviazione fornito automaticamente o fare clic su **Account di archiviazione** per scegliere un account esistente e configurare il tipo di account di archiviazione. Per impostazione predefinita, Azure crea un nuovo account di archiviazione con archiviazione con ridondanza locale.

- In **Rete** è possibile accettare i valori popolati automaticamente per le funzionalità o fare clic su ogni funzionalità per configurare **Rete virtuale**, **Subnet**, **Indirizzo IP pubblico** e **Gruppo di sicurezza di rete**. Per impostazione predefinita, Azure configura automaticamente questi valori.

- Azure abilita l'opzione **Monitoraggio** con lo stesso account di archiviazione designato per la VM, per impostazione predefinita. Queste impostazioni possono essere modificate qui, se necessario.

- In **Set di disponibilità** specificare un set di disponibilità. Ai fini di questa esercitazione, è possibile selezionare **nessuno**. Se si prevede di impostare i gruppi di disponibilità SQL AlwaysOn, configurare la disponibilità per evitare di ricreare la macchina virtuale. Per altre informazioni, vedere [Gestione della disponibilità delle macchine virtuali](virtual-machines-manage-availability.md).

## 4\. Configurare SQL Server
Nel pannello **Crea macchina virtuale** configurare impostazioni e le ottimizzazioni specifiche per SQL Server in **Configura SQL Server**. Le impostazioni che è possibile configurare per SQL Server includono:

- Connettività
- Autenticazione
- Ottimizzazione dell'archiviazione
- Applicazione di patch
- Backup
- Integrazione dell'insieme di credenziali delle chiavi

### Connettività
In **Connettività SQL** specificare **Pubblico (Internet)** per consentire le connessioni a SQL Server dal computer o i servizi in Internet. Selezionando questa opzione, Azure configura automaticamente il firewall e il gruppo di sicurezza di rete per consentire il traffico sulla porta 1433. <br/>![Connettività di Gestione risorse di Azure per SQL](./media/virtual-machines-sql-server-provision-resource-manager/azure-sql-arm-connectivity-alt.png) <br/>

Per connettersi a SQL Server tramite Internet, è necessario abilitare anche Autenticazione di SQL Server.

>[AZURE.NOTE]Per motivi di sicurezza, limitare la porta di origine usando Gruppo di sicurezza di rete. Per altre informazioni, vedere [Che cos'è un gruppo di sicurezza di rete](virtual-networks-nsg.md).

Se si preferisce non abilitare automaticamente le connessioni al motore di database tramite internet, scegliere una delle opzioni seguenti:- **Locale (solo all'interno della VM)** per consentire le connessioni a SQL Server solo dall'interno della macchina virtuale. - **Privato (nella rete virtuale)** per consentire le connessioni a SQL Server dal computer o dai servizi nella stessa rete virtuale.


**Porta**: il valore predefinito 1433, ma è possibile specificare un numero di porta diverso. Per altre informazioni, vedere [Connettersi a una macchina virtuale di SQL Server (Gestione risorse) | Microsoft Azure](virtual-machines-sql-server-connectivity-resource-manager.md).



### Autenticazione
Se si richiede l'autenticazione di SQL Server, fare clic su **Abilita** in **Autenticazione SQL**.

<br/>![Autenticazione di Gestione risorse di Azure per SQL](./media/virtual-machines-sql-server-provision-resource-manager/azure-sql-arm-authentication.png) <br/>


Se si abilita l'autenticazione di SQL Server specificare un **Nome di accesso** e una **Password**. Questo nome utente sarà un account di accesso con autenticazione di SQL Server e membro del ruolo predefinito del server sysadmin. Per altre informazioni sulle modalità di autenticazione, vedere [Scegliere una modalità di autenticazione](http://msdn.microsoft.com/library/ms144284.aspx). Per impostazione predefinita, SQL Server non abilita l'autenticazione di SQL Server. In questo scenario gli amministratori locali nella macchina virtuale possono connettersi all'istanza di SQL Server.

>[AZURE.NOTE] Se si prevede di accedere a SQL Server tramite internet, ovvero l'opzione di connettività Pubblico, è necessario abilitare l'autenticazione di SQL in questa area. L'accesso pubblico a SQL Server richiede l'uso dell'autenticazione di SQL.

### Ottimizzazione dell'archiviazione
Fare clic su **Configurazione dell'archiviazione** per specificare i requisiti di archiviazione. È possibile specificare requisiti come operazioni di I/O al secondo, velocità effettiva in Mbps e dimensioni di archiviazione totali. Configurare questi elementi usando i dispositivi di scorrimento. Il portale calcola automaticamente il numero di dischi in base a questi requisiti.

Per impostazione predefinita, Azure ottimizza l'archiviazione per 5000 operazioni di I/O al secondo, 200 Mbps e 1 TB di spazio di archiviazione. È possibile modificare queste impostazioni di archiviazione in base al carico di lavoro. In **Ottimizzazione dell'archiviazione** selezionare una delle impostazioni seguenti

- **Generale**: è l'impostazione predefinita e supporta la maggior parte dei carichi di lavoro.
- **Elaborazione transazionale**: ottimizza l'archiviazione per carichi di lavoro OLTP di database transazionale.
- **Data warehousing**: ottimizza l'archiviazione per i carichi di lavoro di analisi e creazione di report.

L'immagine seguente mostra il pannello Configurazione dell'archiviazione. <br/>![Archiviazione di Gestione risorse di Azure per SQL](./media/virtual-machines-sql-server-provision-resource-manager/azure-sql-arm-storage.png) <br/>

>[AZURE.NOTE] I limiti per la configurazione dell'archiviazione dipendono dalle dimensioni della macchina virtuale. Per altre informazioni, vedere [Dimensioni delle macchine virtuali](virtual-machines-size-specs.md).

### Applicazione di patch
**Applicazione automatica delle patch di SQL** è abilitata per impostazione predefinita. Questa opzione consente ad Azure di applicare automaticamente le patch a SQL Server e al sistema operativo. Specificare un giorno della settimana, un'ora e una durata per la finestra di manutenzione. Azure eseguirà l'applicazione di patch nella finestra di manutenzione. La pianificazione dell'ora, la finestra di manutenzione usa le impostazioni locali della macchina virtuale. Se non si vuole consentire ad Azure di applicare automaticamente le patch a SQL Server e al sistema operativo, fare clic su **Disabilita**.

<br/>![Applicazione di patch di Gestione risorse di Azure per SQL](./media/virtual-machines-sql-server-provision-resource-manager/azure-sql-arm-patching.png) <br/>

Per altre informazioni, vedere [Applicazione automatizzata di patch per SQL Server in Macchine virtuali di Azure](virtual-machines-sql-server-automated-patching.md).

### Backup
Abilitare i backup automatici del database per tutti i database in **Backup automatico di SQL**. Quando si abilita il backup automatico di SQL è possibile configurare quanto segue:

- Periodo di conservazione dei backup in giorni
- L'account di archiviazione da usare per i backup
- Se applicare o meno la crittografia al backup. Per crittografare il backup, fare clic su **Abilita**. Se i backup automatizzati sono crittografati, specificare una password. Azure crea un certificato per crittografare i backup e usa la password specificata per proteggere il certificato.

<br/>![Backup di Gestione risorse di Azure per SQL](./media/virtual-machines-sql-server-provision-resource-manager/azure-sql-arm-autobackup.png) <br/>

 Per altre informazioni, vedere [Backup automatizzato per SQL Server in Macchine virtuali di Azure](virtual-machines-sql-server-automated-backup.md).

### Integrazione dell'insieme di credenziali delle chiavi
Per archiviare i segreti di sicurezza in Azure per la crittografia, fare clic su **Integrazione Insieme di credenziali delle chiavi di Azure** e quindi su **Abilita**.

<br/>![Integrazione dell'insieme di credenziali delle chiavi di Gestione risorse di Azure per SQL](./media/virtual-machines-sql-server-provision-resource-manager/azure-sql-arm-akv.png) <br/>

La tabella seguente include l'elenco dei parametri necessari per configurare l'integrazione dell'insieme di credenziali delle chiavi di Azure.

|PARAMETRO|DESCRIZIONE|ESEMPIO|
|----------|----------|-------|
|**URL dell'insieme di credenziali delle chiavi** | Percorso dell'insieme di credenziali delle chiavi.|https://contosokeyvault.vault.azure.net/ |
|**Nome dell'entità dell'insieme di credenziali delle chiavi di Azure** |Nome dell'entità servizio di Azure Active Directory. È detto anche ID client. |fde2b411-33d5-4e11-af04eb07b669ccf2|
| **Segreto dell'entità dell'insieme di credenziali delle chiavi di Azure**|L'integrazione dell'insieme di credenziali delle chiavi di Azure crea le credenziali all'interno di SQL Server, consentendo alla VM di accedere all'insieme di credenziali delle chiavi. Scegliere un nome per la credenziale. | 9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=|
|**Nome credenziali**|Scegliere un nome per identificare queste credenziali.| mycred1|

Per altre informazioni, vedere [Configurare l'integrazione dell'insieme di credenziali delle chiavi di Azure per SQL Server in macchine virtuali di Azure](virtual-machines-sql-server-azure-key-vault-integration.md).

## 5\. Esaminare il riepilogo
Esaminare il riepilogo e fare clic su **OK** per creare SQL Server, il gruppo di risorse e le risorse specificate per questa VM. È possibile monitorare la distribuzione dal portale di Azure. Il pulsante **Notifiche** nella parte superiore della schermata mostra lo stato di base della distribuzione.

##<a id="Open"> Aprire la macchina virtuale tramite Desktop remoto e completare la configurazione
Seguire questa procedura per usare Desktop remoto per aprire la macchina virtuale:

1.	Dopo la compilazione della VM di Azure, nel dashboard di Azure verrà visualizzata un'icona per la macchina virtuale. Fare clic sull'icona per visualizzare le informazioni sulla VM.
1.	Nella parte superiore del pannello della VM fare clic su **Connetti**. Il browser scaricherà un file RDP per la VM. Aprire del file RDP.
1.	Connessione Desktop remoto informerà l'utente che non è possibile identificare l'autore della connessione remota e chiederà se si vuole connettersi comunque. Fare clic su **Connetti**.
1.	Nella finestra di dialogo **Sicurezza di Windows**, fare clic su **Usa un altro account**. Per **Nome utente** digitare <machine name><nome utente> specificato durante la configurazione della VM.

Dopo essersi connessi alla macchina virtuale di SQL Server, è possibile avviare SQL Server Management Studio e connettersi con l'autenticazione di Windows usando le credenziali di amministratore locale. In questo modo è possibile modificare le impostazioni del firewall o le impostazioni di configurazione di SQL Server post-provisioning, se necessario.

##<a id="Connect">Connettersi a SQL Server tramite Internet

Per connettersi al motore di database di SQL Server da Internet, sono necessari alcuni passaggi, ad esempio configurazione del firewall, abilitazione dell'autenticazione di SQL Server e configurazione del gruppo di sicurezza di rete. È necessario avere una regola del gruppo di sicurezza di rete per consentire il traffico TCP sulla porta 1433.

Se si usa il portale per effettuare il provisioning di un'immagine di macchina virtuale di SQL Server con Gestione risorse, questi passaggi vengono eseguiti automaticamente quando si seleziona **Pubblico** per l'opzione di connettività SQL e si abilita l'autenticazione di SQL Server. Per accedere all'istanza di SQL Server da Internet, è tuttavia necessario completare alcuni passaggi rimanenti.

>[AZURE.NOTE] Se non si è selezionato Pubblico durante il provisioning, sono necessari altri passaggi per accedere all'istanza di SQL Server tramite Internet. Per altre informazioni, vedere [Connettersi a una macchina virtuale di SQL Server (Gestione risorse) | Microsoft Azure](virtual-machines-sql-server-connectivity-resource-manager.md).

I passaggi seguenti non sono necessari se si deve solo accedere alla macchina virtuale in locale o dall'interno della stessa rete virtuale.

> [AZURE.INCLUDE [Connettersi a SQL Server da una VM in Gestione risorse](../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

##<a id="Next">Passaggi successivi
Per altre informazioni sull'uso di SQL Server in Azure, vedere [SQL Server in Macchine virtuali di Azure](../articles/virtual-machines/virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=AcomDC_0128_2016-->