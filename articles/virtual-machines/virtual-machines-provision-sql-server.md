<properties 
	pageTitle="Provisioning di una macchina virtuale di SQL Server | Microsoft Azure" 
	description="Esercitazione che illustra come creare e configurare una macchina virtuale SQL Server in Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="rothja" 
	manager="jeffreyg" 
	editor="monicar"
	tags="azure-service-management"
	/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows-sql-server" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/26/2015" 
	ms.author="jroth"/>

# Provisioning di una macchina virtuale di SQL Server in Azure

> [AZURE.SELECTOR]
- [Azure classic portal](virtual-machines-provision-sql-server.md)
- [PowerShell](virtual-machines-sql-server-create-vm-with-powershell.md)

## Panoramica

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modello Gestione risorse.

Nella raccolta di macchine virtuali di Azure sono disponibili numerose immagini che contengono Microsoft SQL Server. È possibile selezionare una delle immagini di macchina virtuale dalla raccolta ed eseguire il provisioning della macchina virtuale nell'ambiente Azure con pochi clic.

In questa esercitazione si apprenderà come:

* [Effettuare la connessione al portale di Azure classico ed eseguire il provisioning di una macchina virtuale dalla raccolta](#Provision)
* [Aprire la macchina virtuale tramite Desktop remoto e completare la configurazione](#RemoteDesktop)
* [Completare la procedura di configurazione per connettersi alla macchina virtuale mediante SQL Server Management Studio in un altro computer](#SSMS)
* [Passaggi successivi](#Optional)

>[AZURE.NOTE]In questo articolo viene descritto come eseguire il provisioning di una VM SQL Server con il portale esistente. Tuttavia, è anche possibile creare e gestire VM di SQL Server nel [nuovo portale](https://manage.windowsazure.com). Esistono alcuni vantaggi per il nuovo portale, ad esempio l'impostazione dell'utilizzo di Archiviazione Premium e altre opzioni, ad esempio l'applicazione di patch automatizzata, il backup automatizzato e configurazioni di AlwaysOn. Il contenuto futuro coprirà istruzioni dettagliate.

##<a id="Provision">Eseguire il provisioning di una macchina virtuale di SQL Server dalla raccolta</a>

1. Accedere al [portale di Azure classico](http://manage.windowsazure.com) con il proprio account. Se non si dispone di un account Azure, provare la [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/).

2. Nel portale di Azure classico, nella parte sinistra della pagina Web fare clic su **+NUOVO**, su **CALCOLO**, su **MACCHINA VIRTUALE** e infine su **DA RACCOLTA**.

3. Nella pagina **Scegli un'immagine** fare clic su **SQL SERVER**, quindi selezionare un'immagine di SQL Server. Fare clic sulla freccia nella parte inferiore destra della pagina.

	![Scegli un'immagine](./media/virtual-machines-provision-sql-server/choose-sql-vm.png)

Per le informazioni più aggiornate sulle immagini di SQL Server supportate in Azure, vedere [Panoramica di SQL Server in Macchine virtuali di Azure](virtual-machines-sql-server-infrastructure-services.md).

>[AZURE.NOTE]Se si dispone di una macchina virtuale creata mediante l'immagine della piattaforma dell'edizione di valutazione di SQL Server, non sarà possibile aggiornarla a un'immagine dell'edizione con pagamento al minuto nella raccolta. È possibile scegliere una delle due opzioni seguenti:
>
> - È possibile creare una nuova macchina virtuale usando l'edizione di SQL Server con pagamento al minuto nella raccolta ed eseguire la migrazione dei file di database alla nuova macchina virtuale seguendo la procedura descritta nell'argomento [Migrazione di un database a SQL Server in una VM in Azure](virtual-machines-migrate-onpremises-database).
> - Oppure, è possibile eseguire l'aggiornamento di un'istanza esistente dell'edizione di valutazione di SQL Server a un'altra edizione di SQL Server nell'ambito del contratto [Mobilità delle licenze tramite Software Assurance in Azure](http://azure.microsoft.com/pricing/license-mobility/) seguendo la procedura illustrata nell'argomento [Eseguire l'aggiornamento a un'edizione diversa di SQL Server](https://msdn.microsoft.com/library/cc707783.aspx). Per informazioni su come acquistare una copia di SQL Server con licenza, vedere [Come acquistare SQL Server](http://www.microsoft.com/sqlserver/get-sql-server/how-to-buy.aspx).

4. Nella prima pagina di **Configurazione macchina virtuale** immettere le informazioni seguenti:
	- Una **DATA DI RILASCIO VERSIONE**. Se sono disponibili più immagini, selezionare quella più recente.
	- Un **NOME MACCHINA VIRTUALE** univoco.
	- Nella casella **NUOVO NOME UTENTE** digitare un nome utente univoco per l'account di amministratore locale della macchina.
	- Nella casella **NEW PASSWORD** immettere una password complessa. 
	- Nella casella **CONFIRM PASSWORD** ridigitare la password.
	- Selezionare un valore appropriato per **SIZE** nell'elenco a discesa. 

	![Configurazione macchina virtuale](./media/virtual-machines-provision-sql-server/4VM-Config.png)

	>[AZURE.NOTE]Le dimensioni della macchina virtuale vengono specificate durante il provisioning:
 	>
	> - Per i carichi di lavoro di produzione, è consigliabile utilizzare Archiviazione Premium con le dimensioni minime consigliate: **DS3** per SQL Server Enterprise e **DS2** per SQL Server Standard. Per altre informazioni, vedere [Procedure consigliate per le prestazioni per SQL Server in Macchine virtuali di Azure](virtual-machines-sql-server-performance-best-practices.md).
	> - Le dimensioni selezionate limitano il numero di dischi dati che è possibile configurare. Per informazioni aggiornate sulle dimensioni di macchine virtuali disponibili e sul numero di dischi dati che è possibile collegare a una macchina virtuale, vedere [Dimensioni delle macchine virtuali e dei servizi cloud per Azure](virtual-machines-size-specs.md).

5. Dopo aver immesso i dettagli di configurazione della macchina virtuale, fare clic sulla freccia avanti nella parte inferiore destra per continuare.

5. Nella seconda pagina di **Configurazione macchina virtuale** configurare le risorse per la rete, l'archiviazione e la disponibilità:
	- Nella casella **Servizio cloud** scegliere **Crea un nuovo servizio cloud**.
	- Nella casella **Nome DNS del servizio cloud** specificare la prima parte del nome DNS desiderato, in modo che completi un nome nel formato **TESTNAME.cloudapp.net**. 
	- Selezionare una **SOTTOSCRIZIONE** se sono presenti più sottoscrizioni. La scelta determina quali **account di archiviazione** saranno disponibili.
	- Nella casella **REGIONE/GRUPPO DI AFFINITÀ/RETE VIRTUALE** selezionare l'area in cui verrà ospitata l'immagine virtuale.
	- Nella casella **Account di archiviazione** generare automaticamente un account o selezionarne uno dall'elenco. Modificare la **SOTTOSCRIZIONE** per visualizzare altri account. 
	- Nella casella **AVAILABILITY SET** selezionare **(none)**.
	- Leggere e accettare le note legali.
	

6. Fare clic sulla freccia Avanti per continuare.


7. Fare clic sul segno di spunta nell'angolo inferiore destro per continuare.

8. Attendere durante la preparazione della macchina virtuale in Azure. Lo stato della macchina virtuale attraverserà le fasi seguenti:

	- **Starting (Provisioning)**
	- **Stopped**
	- **Starting (Provisioning)**
	- **Running (Provisioning)**
	- **Running**
	

##<a id="RemoteDesktop">Aprire la VM tramite Desktop remoto per completare l’installazione</a>

1. Al termine del provisioning, fare clic sul nome della macchina virtuale per passare alla pagina DASHBOARD. Nella parte inferiore della pagina fare clic su **Connect**.

2. Fare clic sul pulsante **Apri**.

	![Fare clic sul pulsante Apri](./media/virtual-machines-provision-sql-server/click-open-to-connect.png)

3. Nella finestra di dialogo **Protezione di Windows**, fare clic su **Utilizza un altro account**.

	![Fare clic su Utilizza un altro account](./media/virtual-machines-provision-sql-server/credentials.png)

4. Utilizzare il nome della macchina come nome di dominio, seguito dal nome dell'amministratore nel seguente formato: `machinename\username`. Digitare la password e connettersi alla macchina.

4. In occasione del primo accesso, verranno completati diversi processi, come la configurazione del desktop, l'aggiornamento di Windows e il completamento delle attività di configurazione iniziali di Windows (sysprep). Al termine delle attività di configurazione iniziali di Windows, verranno completate le attività di configurazione di SQL Server. Queste attività potrebbero causare un ritardo di pochi minuti mentre vengono completate. `SELECT @@SERVERNAME` potrebbe non restituire il nome corretto fino al completamento dell'installazione di SQL Server e SQL Server Management Studio potrebbe non essere visibile nella pagina iniziale.

Dopo avere eseguito la connessione alla macchina virtuale con Desktop remoto Windows, la macchina virtuale funziona come qualsiasi altro computer. Eseguire normalmente la connessione all'istanza predefinita di SQL Server con SQL Server Management Studio (in esecuzione nella macchina virtuale).

##<a id="SSMS">Connettersi all'istanza di VM di SQL Server da SQL Server Management Studio da un altro computer</a>

[AZURE.INCLUDE [Connettersi a SQL Server in una macchina virtuale](../../includes/virtual-machines-sql-server-connection-steps.md)]

## <a id="cdea">Connettersi al motore di database dall'applicazione</a>

Se è possibile connettersi a un'istanza di SQL Server in esecuzione in una macchina virtuale di Azure tramite Management Studio, dovrebbe essere possibile connettersi anche con una stringa di connessione simile a quella riportata di seguito.

	connectionString = "Server=tutorialtestVM.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Per altre informazioni, vedere l'argomento relativo alla [risoluzione dei problemi di connessione al motore di database di SQL Server](http://social.technet.microsoft.com/wiki/contents/articles/how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx).

##<a id="Optional">Passaggi successivi</a>

Si è appreso come creare e configurare un'istanza di SQL Server in una macchina virtuale di Azure usando l'immagine della piattaforma. In molti casi, il passaggio successivo consiste nella migrazione dei database in questa nuova macchina virtuale di SQL Server. Per linee guida sulla migrazione dei database, vedere [Migrazione di un database a SQL Server in una VM di Azure](virtual-machines-migrate-onpremises-database.md).

L'elenco seguente fornisce ulteriori risorse per SQL Server in macchine virtuali di Azure.

### Risorse consigliate per SQL Server in Macchine virtuali di Azure:
- [Panoramica di SQL Server in Macchine virtuali di Azure](virtual-machines-sql-server-infrastructure-services.md)

- [Connettersi a una macchina virtuale di SQL Server in Azure](virtual-machines-sql-server-connectivity.md)

- [Procedure consigliate per le prestazioni per SQL Server in Macchine virtuali di Azure](virtual-machines-sql-server-performance-best-practices.md)

- [Considerazioni relative alla sicurezza per SQL Server in Macchine virtuali di Azure](virtual-machines-sql-server-security-considerations.md)

### Disponibilità elevata e ripristino di emergenza
- [Disponibilità elevata e ripristino di emergenza di SQL Server in Macchine virtuali di Azure](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md)

- [Backup e ripristino per SQL Server in Macchine virtuali di Azure](virtual-machines-sql-server-backup-and-restore.md)

### SQL Server i carichi di lavoro in Azure:
- [SQL Server Business Intelligence in Macchine virtuali di Azure](virtual-machines-sql-server-business-intelligence.md)

### White paper:
- [Informazioni sul database SQL di Azure e SQL Server nelle macchine virtuali di Azure](sql-database/data-management-azure-sql-database-and-sql-server-iaas.md)

- [Modelli di applicazione e strategie di sviluppo per SQL Server in Macchine virtuali di Azure](virtual-machines-sql-server-application-patterns-and-development-strategies.md)

<!---HONumber=AcomDC_1203_2015-->