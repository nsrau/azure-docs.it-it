<properties
	pageTitle="Esecuzione della migrazione del database locale a SQL Server nella macchina virtuale di Azure"
	description="Leggere le informazioni su come eseguire la migrazione di un database utente locale a SQL Server in una macchina virtuale di Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="carlrabeler"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/16/2015"
	ms.author="carlrab"/>


# Migrazione di un database a SQL Server su una macchina virtuale di Azure

Esistono diversi metodi per eseguire la migrazione di un database utente di SQL Server locale a SQL Server in una macchina virtuale di Azure. In questo articolo verranno illustrati brevemente i vari metodi, verrà consigliato il metodo migliore per i diversi scenari e verrà fornita un'esercitazione come guida per l'utilizzo della procedura guidata per la distribuzione di un database SQL Server a una macchina virtuale di Microsoft Azure.

## Quali sono i metodi di migrazione principali?

I metodi di migrazione principali sono:

- Utilizzo della procedura guidata per la distribuzione di un database di SQL Server a una macchina virtuale di Microsoft Azure
- Esecuzione del backup locale tramite la compressione e copia manuale del file di backup nella macchina virtuale di Azure
- Esecuzione di un backup nell’URL e ripristino nella macchina virtuale di Azure dall'URL
- Scollegamento e successiva copia dei dati e dei file di log sull’archivio BLOB di Azure, quindi collegamento a SQL Server nella macchina virtuale di Azure dall'URL
- Conversione della macchina fisica locale a disco rigido virtuale Hyper-V, caricamento sull'archivio BLOB di Azure e successiva distribuzione come nuova macchina virtuale tramite il disco rigido virtuale caricato
- Spedizione del disco rigido tramite il servizio di Importazione/Esportazione di Windows

> [AZURE.NOTE]Se si dispone di una distribuzione AlwaysOn locale, è inoltre possibile utilizzare la [procedura guidata Aggiungi replica di Azure](https://msdn.microsoft.com/library/dn463980.aspx) per creare una replica in Azure e successivamente il failover come metodo di migrazione.

## Scelta del metodo di migrazione

Per prestazioni di trasferimento dei dati ottimali, il metodo migliore è in genere la migrazione dei file del database alla macchina virtuale di Azure eseguita tramite un file di backup compresso. Si tratta del metodo utilizzato dalla [procedura guidata per la distribuzione di un database di SQL Server a una macchina virtuale di Microsoft Azure](#azure-vm-deployment-wizard-tutorial). Questa procedura guidata è il metodo consigliato per la migrazione di un database utente locale in esecuzione su SQL Server 2005 o versione successiva a SQL Server 2014 o versione successiva quando il file di backup compresso del database è inferiore a 1 TB.

Se non è possibile utilizzare la procedura guidata perché il file di backup del database è troppo grande o l'istanza di SQL Server di destinazione non è SQL Server 2014 o versione successiva, il processo di migrazione sarà un processo manuale che in genere inizierà con un backup del database seguito da una copia del backup del database in Azure e terminerà con un ripristino del database. È inoltre possibile copiare gli stessi file del database in Azure. Esistono diversi metodi che consentono di eseguire questo processo di migrazione manuale di un database in una macchina virtuale di Azure.

> [AZURE.NOTE]Durante l'aggiornamento a SQL Server 2014 o SQL Server 2016 da versioni precedenti di SQL Server, è necessario considerare se sono necessarie modifiche. È consigliabile prendere in considerazione tutte le dipendenze sulle funzionalità non supportate dalla nuova versione di SQL Server come parte del progetto di migrazione. Per ulteriori informazioni sulle edizioni supportate e sugli scenari, vedere [Aggiornamento a SQL Server](https://msdn.microsoft.com/library/bb677622.aspx).

Nella tabella seguente sono elencati tutti i principali metodi di migrazione e viene illustrato quando l'utilizzo di ciascun metodo è più appropriato.

| Metodo | Versione del database di origine | Versione del database di destinazione | Vincolo di dimensioni del backup del database di origine | Note |
|---|---|---|---|---|
| [Utilizzo della procedura guidata per la distribuzione di un database di SQL Server a una macchina virtuale di Microsoft Azure](#azure-vm-deployment-wizard-tutorial) | SQL Server 2005 o versione successiva | SQL Server 2014 o versione successiva | Più di 1 TB | Metodo più rapido e semplice, da utilizzare ogni volta che è possibile eseguire la migrazione a un'istanza di SQL Server nuova o esistente in una macchina virtuale di Azure |
| [Esecuzione del backup locale tramite la compressione e copia manuale del file di backup nella macchina virtuale di Azure](#backup-to-file-and-copy-to-vm-and-restore) | SQL Server 2005 o versione successiva | SQL Server 2005 o versione successiva | [Limite di archiviazione della macchina virtuale di Azure](https://azure.microsoft.com/it-it/documentation/articles/azure-subscription-service-limits/) | Da utilizzare solo quando non è possibile usare la procedura guidata, ad esempio quando la versione del database di destinazione è precedente a SQL Server 2012 SP1 CU2 o le dimensioni del backup del database sono superiori a 1 TB (12,8 TB con SQL Server 2016) |
| [Esecuzione di un backup nell’URL e ripristino nella macchina virtuale di Azure dall'URL](#backup-to-url-and-restore) | SQL Server 2012 SP1 CU2 o versione successiva | SQL Server 2012 SP1 CU2 o versione successiva | > 1 TB (per SQL Server 2016, < 12,8 TB) | In genere l’utilizzo del [backup nell'URL](https://msdn.microsoft.com/library/dn435916.aspx) è equivalente all'utilizzo della procedura guidata in termini di prestazioni ma non in termini di semplicità |
| [Scollegamento e successiva copia dei dati e dei file di log sull’archivio BLOB di Azure, quindi collegamento a SQL Server nella macchina virtuale di Azure dall'URL](#detach-and-copy-to-url-and-attach-from-url) | SQL Server 2005 o versione successiva | SQL Server 2014 o versione successiva | [Limite di archiviazione della macchina virtuale di Azure](https://azure.microsoft.com/it-it/documentation/articles/azure-subscription-service-limits/) | Da utilizzare quando il collegamento dei file del database a SQL Server in una macchina virtuale di Azure viene eseguito [archiviando tali file tramite il servizio di archiviazione BLOB di Azure](https://msdn.microsoft.com/library/dn385720.aspx), in particolare con database di grandi dimensioni |
| [Conversione della macchina locale a dischi rigidi virtuali Hyper-V, caricamento sull'archivio BLOB di Azure e successiva distribuzione di una nuova macchina virtuale tramite il disco rigido virtuale caricato](#convert-to-vm-and-upload-to-url-and-deploy-as-new-vm) | SQL Server 2005 o versione successiva | SQL Server 2005 o versione successiva | [Limite di archiviazione della macchina virtuale di Azure](https://azure.microsoft.com/it-it/documentation/articles/azure-subscription-service-limits/) | Da utilizzare per la [connessione della propria licenza di SQL Server](../data-management-azure-sql-database-and-sql-server-iaas/), per la migrazione di un database che verrà eseguito su una versione precedente di SQL Server o per la migrazione dei database di sistema e utente insieme come parte della migrazione di database dipendenti da altri database utente e/o database di sistema. |
| [Spedizione del disco rigido tramite il servizio di Importazione/Esportazione di Windows](#ship-hard-drive) | SQL Server 2005 o versione successiva | SQL Server 2005 o versione successiva | [Limite di archiviazione della macchina virtuale di Azure](https://azure.microsoft.com/it-it/documentation/articles/azure-subscription-service-limits/) | Utilizzare il [servizio di Importazione/Esportazione di Windows](../storage-import-export-service/) quando il metodo della copia manuale è troppo lento, ad esempio con database di grandi dimensioni |

## Esercitazione relativa alla procedura guidata per la distribuzione della macchina virtuale di Azure

Utilizzare la procedura guidata per la distribuzione di un database di SQL Server a una macchina virtuale di Microsoft Azure in Microsoft SQL Server Management Studio per eseguire la migrazione di un database utente di SQL Server 2005, SQL Server 2008, SQL Server 2008 R2, SQL Server 2012, SQL Server 2014 o SQL Server 2016 locale (fino a 1 TB) a SQL Server 2014 o SQL Server 2016 in una macchina virtuale di Azure. Utilizzare questa procedura guidata per eseguire la migrazione di un database utente a una macchina virtuale di Azure esistente o a una macchina virtuale di Azure con SQL Server creata dalla procedura guidata durante il processo di migrazione. Quando si esegue la migrazione di un database a una versione successiva di SQL Server, il database viene aggiornato automaticamente durante il processo.

### Ottenimento dell’ultima versione della procedura guidata per la distribuzione di un database di SQL Server a una macchina virtuale di Microsoft Azure

Utilizzare la versione più recente di Microsoft SQL Server Management Studio per SQL Server per assicurarsi di disporre della versione più recente della procedura guidata per la distribuzione di un database di SQL Server a una macchina virtuale di Microsoft Azure. La versione più recente di questa procedura guidata include gli aggiornamenti più recenti per il portale di Azure e supporta le immagini della macchina virtuale di Azure più recenti nella raccolta (versioni precedenti della procedura guidata potrebbero non funzionare). Per ottenere la versione più recente di Microsoft SQL Server Management Studio per SQL Server, [scaricarla](http://go.microsoft.com/fwlink/?LinkId=616025) e installarla su un computer client con la connettività al database di cui si intende eseguire la migrazione e a Internet.

### Configurazione della macchina virtuale di Azure esistente e dell'istanza di SQL Server (se applicabile)

Se viene eseguita la migrazione a una macchina virtuale di Azure esistente, è necessario attenersi ai passaggi di configurazione seguenti:

- Configurare la macchina virtuale di Azure e l'istanza di SQL Server per abilitare la connettività da un altro computer seguendo i passaggi della sezione Connessione all'istanza della macchina virtuale di SQL Server da SSMS su un altro computer in [Provisioning di una macchina virtuale di SQL Server in Azure](../virtual-machines-provision-sql-server/#SSMS). Se si esegue la migrazione tramite la procedura guidata, sono supportate solo le immagini di SQL Server 2014 e SQL Server 2016 nella raccolta.
- Configurare un endpoint aperto per il servizio adattatore cloud di SQL Server sul gateway di Microsoft Azure con porta privata 11435. Questa porta viene creata come parte del provisioning di SQL Server 2014 o SQL Server 2016 su una macchina virtuale di Microsoft Azure. L’adattatore cloud crea inoltre una regola di Windows Firewall per consentire le connessioni TCP in entrata alla porta 11435 predefinita. Questo endpoint consente alla procedura guidata di utilizzare il servizio adattatore cloud per copiare i file di backup dall'istanza locale alla macchina virtuale di Azure. Per ulteriori informazioni, vedere [Adattatore cloud per SQL Server](https://msdn.microsoft.com/library/dn169301.aspx).

	![Creazione dell’endpoint adattatore cloud](./media/virtual-machines-migrate-onpremises-database/cloud-adapter-endpoint.png)

### Esecuzione della procedura guidata per la distribuzione di un database di SQL Server a una macchina virtuale di Microsoft Azure

1. Aprire Microsoft SQL Server Management Studio per Microsoft SQL Server 2016 ed eseguire la connessione all'istanza di SQL Server contenente il database utente di cui si desidera effettuare la migrazione a una macchina virtuale di Azure.
2. Fare clic con il pulsante destro del mouse sul database di cui si esegue la migrazione, scegliere Attività, quindi fare clic su Distribuisci a una macchina virtuale di Microsoft Azure.

	![Avvio della procedura guidata](./media/virtual-machines-migrate-onpremises-database/start-wizard.png)

3. Nella pagina Introduzione, fare clic su Avanti.
4. Nella pagina Impostazioni di origine, eseguire la connessione all'istanza di SQL Server contenente il database di cui si desidera eseguire la migrazione a una macchina virtuale di Azure.
5. Specificare una posizione temporanea per i file di backup. Se si esegue la connessione a un server remoto, è necessario specificare un'unità di rete.

	![Impostazioni di origine](./media/virtual-machines-migrate-onpremises-database/source-settings.png)

6. Fare clic su Avanti.
7. Nella pagina di accesso di Microsoft Azure, fare clic su Accedi e accedere al proprio account di Azure.
8. Selezionare la sottoscrizione che si desidera utilizzare e fare clic su Avanti.

	![Accesso ad Azure](./media/virtual-machines-migrate-onpremises-database/azure-signin.png)

9. Nella pagina Impostazioni di distribuzione, è possibile specificare un nome per il servizio cloud e per la macchina virtuale nuovo o esistente:

 - Specificare un nome per il servizio cloud e per la macchina virtuale nuovo per creare un nuovo servizio cloud con una nuova macchina virtuale di Azure utilizzando un'immagine della raccolta di SQL Server 2014 o SQL Server 2016.

     - Se si specifica un nome per il servizio cloud nuovo, specificare l'account di archiviazione che verrà utilizzato.

     - Se si specifica un nome per il servizio cloud esistente, l'account di archiviazione verrà recuperato e immesso automaticamente.

 - Specificare un nome per il servizio cloud esistente e un nome per la macchina virtuale nuovo per creare una nuova macchina virtuale di Azure in un servizio cloud esistente. Specificare solo un'immagine della raccolta di SQL Server 2014 o SQL Server 2016.
 - Specificare un nome per il servizio cloud e per la macchina virtuale esistente per utilizzare una macchina virtuale di Azure esistente. Deve essere un'immagine creata utilizzando un'immagine della raccolta di SQL Server 2014 o SQL Server 2016.

		![Deploymnent Settings](./media/virtual-machines-migrate-onpremises-database/deployment-settings.png)

10. Fare clic su Impostazioni
  - Se è stato specificato un nome per il servizio cloud e per la macchina virtuale esistente, verrà richiesto di fornire il nome utente e la password.

		![Azure machine settings](./media/virtual-machines-migrate-onpremises-database/azure-machine-settings.png)

	- Se è stato specificato un nome per la macchina virtuale nuovo, verrà richiesto di selezionare un'immagine dall'elenco delle immagini della raccolta e di fornire le informazioni seguenti:
	  - Immagine: selezionare solo SQL Server 2014 o SQL Server 2016
		- Nome utente
		- Nuova password
		- Conferma password
		- Percorso
		- Dimensioni
 	- Inoltre, fare clic per accettare il certificato generato automaticamente per la nuova macchina virtuale di Microsoft Azure, quindi fare clic su OK.

	![Impostazioni della nuova macchina di Azure](./media/virtual-machines-migrate-onpremises-database/azure-new-machine-settings.png)

11. Specificare il nome del database di destinazione se diverso dal nome del database di origine. Se il database di destinazione esiste già, il sistema incrementerà automaticamente il nome del database anziché sovrascrivere il database esistente.
12. Fare clic su Avanti, quindi su Fine.

	![Risultati](./media/virtual-machines-migrate-onpremises-database/results.png)

13. Al termine della procedura guidata, eseguire la connessione alla macchina virtuale e verificare che la migrazione del database sia stata effettuata.
14. Se è stata creata una nuova macchina virtuale, configurare la macchina virtuale di Azure e l'istanza di SQL Server seguendo i passaggi della sezione Connessione all'istanza della macchina virtuale di SQL Server da SSMS su un altro computer in [Provisioning di una macchina virtuale di SQL Server in Azure](../virtual-machines-provision-sql-server/#SSMS).

## Backup sul file, copia sulla macchina virtuale e ripristino

Utilizzare questo metodo quando non è possibile usare la procedura guidata per la distribuzione di un database di SQL Server a una macchina virtuale di Microsoft Azure perché si esegue la migrazione a una versione di SQL Server precedente a SQL Server 2014 o il file di backup è superiore a 1 TB. Se il file di backup è superiore a 1 TB, è necessario eseguirne lo striping perché le dimensioni massime del disco di una macchina virtuale sono pari a 1 TB. Per eseguire la migrazione di un database utente tramite il metodo manuale, attenersi ai passaggi generali seguenti:

1.	Eseguire un backup completo del database su una posizione locale.
2.	Creare o caricare una macchina virtuale con la versione di SQL Server desiderata.
3.	Eseguire il provisioning della macchina virtuale utilizzando i passaggi descritti in [Provisioning di una macchina virtuale di SQL Server in Azure](../virtual-machines-provision-sql-server/#SSMS).
4.	Copiare i file di backup sulla macchina virtuale utilizzando il desktop remoto, Esplora risorse o il comando di copia da un prompt dei comandi.

## Backup nell’URL e ripristino

Utilizzare il metodo di [backup nell'URL](https://msdn.microsoft.com/library/dn435916.aspx) quando non è possibile usare la procedura guidata per la distribuzione di un database di SQL Server a una macchina virtuale di Microsoft Azure perché il file di backup è superiore a 1 TB e si esegue la migrazione da e a SQL Server 2016. Per i database inferiori a 1 TB o che eseguono una versione di SQL Server precedente a SQL Server 2016, è consigliabile utilizzare la procedura guidata. Con SQL Server 2016, i set di backup con striping sono supportati, sono consigliati per le prestazioni e necessari per superare i limiti di dimensione per BLOB. Per i database di dimensioni molto grandi, è consigliato l'utilizzo del [servizio di Importazione/Esportazione di Windows](../storage-import-export-service/).

## Scollegamento, copia nell’URL e collegamento dall’URL

Utilizzare questo metodo quando si prevede di [archiviare i file tramite il servizio di archiviazione BLOB di Azure](https://msdn.microsoft.com/library/dn385720.aspx) e di collegarli a SQL Server in esecuzione su una macchina virtuale di Azure, in particolare con database di grandi dimensioni. Per eseguire la migrazione di un database utente tramite il metodo manuale, attenersi ai passaggi generali seguenti:

1.	Scollegare i file del database dall'istanza del database locale.
2.	Copiare i file del database scollegati nell’archivio BLOB di Azure utilizzando l’[utilità della riga di comando AZCopy](../storage-use-azcopy/).
3.	Collegare i file del database dall'URL di Azure all'istanza di SQL Server nella macchina virtuale di Azure.

## Conversione alla macchina virtuale, caricamento nell’URL e distribuzione come nuova macchina virtuale

Utilizzare questo metodo per eseguire la migrazione di tutti i database di sistema e utente in un'istanza di SQL Server locale alla macchina virtuale Azure. Per eseguire la migrazione di un'intera istanza di SQL Server utilizzando il metodo manuale, attenersi ai passaggi generali seguenti:

1.	Convertire le macchine fisiche o virtuali in dischi rigidi virtuali Hyper-V utilizzando[Microsoft Virtual Machine Converter](http://technet.microsoft.com/library/dn873998.aspx).
2.	Caricare i file dei dischi rigidi virtuali su Archiviazione di Azure utilizzando il [cmdlet Add-AzureVHD](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx).
3.	Distribuire una nuova macchina virtuale utilizzando il disco rigido virtuale caricato.

> [AZURE.NOTE]Per eseguire la migrazione di un'intera applicazione, è consigliabile utilizzare [Azure Site Recovery](../services/site-recovery/).

## Spedizione del disco rigido

Utilizzare il [metodo del servizio di Importazione/Esportazione di Windows](../storage-import-export-service/) per trasferire grandi quantità di dati di file sull'archivio BLOB di Azure in situazioni in cui il caricamento in rete è eccessivamente costoso o non è possibile. Con questo servizio, è possibile inviare uno o più dischi rigidi contenenti tali dati a un datacenter di Azure, dove i dati verranno caricati sull'account di archiviazione.

<!---HONumber=July15_HO4-->