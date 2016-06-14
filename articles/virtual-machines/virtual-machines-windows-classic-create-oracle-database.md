<properties
	pageTitle="Creare una macchina virtuale Oracle Database con il portale di Azure classico | Microsoft Azure"
	description="Informazioni su come creare una macchina virtuale con un database Oracle all'interno usando il modello di distribuzione classica e il portale di Azure."
	services="virtual-machines-windows"
	authors="rickstercdn"
	manager="timlt"
	documentationCenter=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="Windows"
	ms.workload="infrastructure-services"
	ms.date="05/17/2016"
	ms.author="rclaus" />

#Creare una macchina virtuale Oracle Database in Azure

[AZURE.INCLUDE [virtual-machines-common-oracle-support](../../includes/virtual-machines-common-oracle-support.md)]

L'esempio seguente illustra come creare un Database Oracle in una macchina virtuale Windows Server in cui sia già creato e installato il Database Oracle in versione 12C. Sono disponibili due passaggi: Innanzitutto, collegare la macchina virtuale e quindi creare il Database Oracle all'interno della VM. Nell'esempio mostrato viene utilizzata la versione 12c di Oracle Database, ma i passaggi per la versione 11g sono praticamente identici.


##Creazione del database utilizzando la macchina virtuale Oracle Database in Azure

1.	Accedere al [Portale di Azure](https://ms.portal.azure.com/).

2.	Fare clic su **Macchine virtuali**.

3.	Fare clic sul nome della macchina virtuale a cui si desidera accedere.

4.	Fare clic su **Connect**.

5.	Rispondere ai prompt visualizzati per connettersi alla macchina virtuale. Quando vengono richiesti il nome e la password dell'amministratore, usare i valori specificati durante la creazione della macchina virtuale.

6.	Creare una variabile di ambiente denominata **ORACLE\_HOSTNAME** con il valore impostato sul nome computer della macchina virtuale. È possibile creare una variabile di ambiente utilizzando la procedura seguente:

	a. In Windows, fare clic su **Start**, digitare **Pannello di controllo**, fare clic sull’icona di **Pannello di controllo**, su **Sistema e sicurezza**, su **Sistema**, quindi su **Impostazioni di sistema avanzate**.

	b. Fare clic sulla scheda **Avanzate**, quindi su **Variabili d’ambiente**.

	c. Nella sezione **Variabili di sistema** fare clic su **Nuova** per creare la variabile.

	d. Nella finestra di dialogo **Nuova variabile di sistema** immettere **ORACLE\_HOSTNAME** come nome della variabile e quindi il nome computer della macchina virtuale come valore. Per determinare il nome computer, aprire il prompt dei comandi ed eseguire **SET COMPUTERNAME** (l'output del comando conterrà il nome computer).

	e. Fare clic su **OK** per salvare la nuova variabile di ambiente e chiudere la finestra di dialogo **Nuova variabile di sistema**.

	f. Chiudere le altre finestre di dialogo che sono state aperte da Pannello di controllo.

7.	In Windows fare clic su **Start** e quindi digitare **Assistente alla configurazione del database**. Fare clic sull'icona dell'**Assistente alla configurazione del database**.

8.	All'interno della procedura guidata **Database Configuration Assistant** specificare i valori in base alle esigenze per ogni passaggio nelle diverse finestre di dialogo:

	a. **Passaggio 1:** fare clic su **Create Database** e quindi su **Next**.

		![](media/virtual-machines-windows-classic-create-oracle-database/image5.png)

	b. **Passaggio 2:** immettere un valore per **Global Database Name**. Immettere e confermare un valore per **Administrative Password**. Questa password è per l’utente **sistema**del database Oracle. Deselezionare l'opzione **Create As Container Database**. Fare clic su **Avanti**.

		![](media/virtual-machines-windows-classic-create-oracle-database/image6.png)

	c. **Passaggio 3:** la verifica dei prerequisiti dovrebbe procedere automaticamente, avanzando al **Passaggio 4**.

	d. **Passaggio 4:** esaminare le opzioni in **Create Database – Summary** e quindi fare clic su **Finish**.

		![](media/virtual-machines-windows-classic-create-oracle-database/image7.png)
	e. **Passaggio 5:** nella **pagina di avanzamento** verrà segnalato lo stato di creazione del database.

		![](media/virtual-machines-windows-classic-create-oracle-database/image8.png)
	f. Dopo aver creato il database, sarà possibile scegliere di utilizzare la finestra di dialogo **Gestione password**. Modificare le impostazioni della password, se necessario per le proprie esigenze, e quindi chiudere le finestre di dialogo per uscire dalla procedura guidata **Database Configuration Assistant**.

##Conferma dell’installazione del database

1.	Restare connessi alla macchina virtuale e avviare un prompt dei comandi SQL Plus. In Windows, fare clic su *Start**, quindi digitare **SQL Plus**. Fare clic sull'icona di **SQL Plus**.

2.	Quando richiesto, accedere con il nome utente di **SISTEMA** e la password specificata durante la creazione del database Oracle.

3.	Nel prompt dei comandi SQL Plus eseguire il comando seguente:

		**select * from GLOBAL\_NAME;**

	Il risultato dovrebbe essere il nome globale del database creato.

	![](media/virtual-machines-windows-classic-create-oracle-database/image9.png)

##Autorizzazione dell’accesso in remoto del database
Per autorizzare l’accesso in remoto del database (ad esempio, da un computer client che esegue codice Java), sarà necessario avviare il listener del database, aprire la porta 1521 nel firewall della macchina virtuale e creare un endpoint pubblico per la porta 1521.

### Avvio del listener del database
1.	Accedere alla macchina virtuale.

2.	Aprire un prompt dei comandi.

3.	Nel prompt dei comandi, eseguire il seguente comando:

		**lsnrctl start**

> [AZURE.NOTE] È possibile eseguire **Stato Isnrctl** per verificare lo stato del listener. Quando si desidera interrompere il listener, è possibile eseguire **Termina Isnrctl**.

### Apertura della porta 1521 nel firewall della macchina virtuale

1.	Dopo aver effettuato l’accesso alla macchina virtuale, in Windows, fare clic su **Start**, digitare **Windows Firewall con sicurezza avanzata**, quindi fare clic sull’icona di **Windows Firewall con sicurezza avanzata**. Viene aperta la console di gestione di **Windows Firewall con sicurezza avanzata**.

2.	All’interno della console di gestione del firewall, fare clic su **Regole connessioni in entrata** nel riquadro a sinistra (se non viene visualizzata l’opzione **Regole connessioni in entrata**, espandere il nodo principale nel riquadro a sinistra), quindi fare clic su **Nuova regola** nel riquadro a destra.

3.	Per **Tipo di regola** selezionare **Porta** e quindi fare clic su **Avanti**.

4.	Per **Protocollo e porte** selezionare **TCP**, quindi **Porte locali specifiche**, immettere **1521** per la porta e infine fare clic su **Avanti**.

5.	Selezionare **Consenti la connessione** e fare clic su **Avanti**.

6.	Accettare le impostazioni predefinite per i profili per i quali viene applicata la regola e quindi fare clic su **Avanti**.

7.	Specificare un nome per la regola e facoltativamente una descrizione e quindi fare clic su **Fine**.

### Creazione di un endpoint pubblico per la porta 1521

1.	Accedere al [Portale di Azure](https://ms.portal.azure.com/).

2.	Fare clic su **Sfoglia**.

3.  Fare clic su **Macchine virtuali**.

4.	Selezionare la macchina virtuale.

5.	Fare clic su **Impostazioni**.

6.	Fare clic su **Endpoint**.

7.	Fare clic su **Aggiungi**.

8.	Specificare un nome per l'endpoint.

	a. Usare **TCP** per il protocollo.

	b. Usare **1521** per la porta pubblica.

	c. Usare **1521** per la porta privata.

9.	Lasciare le altre opzioni invariate.

10. Fare clic su **OK**.

##Abilitazione dell'accesso remoto a Oracle Database Enterprise Manager
Se si desidera abilitare l'accesso remoto a Oracle Database Enterprise Manager, aprire la porta 5500 nel firewall e creare un endpoint della macchina virtuale per 5500 nel portale di Azure classico (utilizzando la procedura mostrata in precedenza per l’apertura della porta 1521 e la creazione di un endpoint per 1521). Quindi, per eseguire Oracle Enterprise Manager dal computer remoto, aprire un browser per accedere all'URL nel formato `http://<<unique_domain_name>>:5500/em`.

> [AZURE.NOTE] È possibile determinare il valore per *<<unique\_domain\_name>>* all'interno del [portale di Azure classico](https://ms.portal.azure.com/) facendo clic su **Macchine virtuali** e selezionando la macchina virtuale usata per eseguire Oracle Database.

##Configurazione dei raggruppamenti di opzioni comuni e avanzate
Se si sceglie **Oracle Database with Popular Options** o **Oracle Database with Advanced Options bundle**, il passaggio successivo consiste nel configurare le funzionalità dei componenti aggiuntivi nell'installazione di Oracle. Consultare la documentazione di Oracle per istruzioni sull'impostazione di tali opzioni in Windows, dal momento che le configurazioni possono variare notevolmente in base alle esigenze di ogni singolo componente.

Il bundle **Oracle Database with Popular Options** include Oracle Database Enterprise Edition e istanze di [Partitioning](http://www.oracle.com/us/products/database/options/partitioning/overview/index.html), [Active Data Guard](http://www.oracle.com/us/products/database/options/active-data-guard/overview/index.html), [Oracle Tuning Pack for Database](http://docs.oracle.com/html/A86647_01/tun_ovw.htm), [Oracle Diagnostics Pack for Database](http://docs.oracle.com/cd/B28359_01/license.111/b28287/options.htm#CIHIHDDJ) e [Oracle Lifecycle Management Pack for Database](http://www.oracle.com/technetwork/oem/lifecycle-mgmt-495331.html) comprese nella licenza.

Il bundle **Oracle Database with Advanced Options** include istanze comprese nella licenza di tutte le opzioni del bundle Popular Options, nonché [Advanced Compression](http://www.oracle.com/us/products/database/options/advanced-compression/overview/index.html), [Advanced Security](http://www.oracle.com/us/products/database/options/advanced-security/overview/index.html), [Label Security](http://www.oracle.com/us/products/database/options/label-security/overview/index.html), [Database Vault](http://www.oracle.com/us/products/database/options/database-vault/overview/index.html), [Advanced Analytics](http://www.oracle.com/us/products/database/options/advanced-analytics/overview/index.html), [OLAP](http://docs.oracle.com/cd/E11882_01/license.112/e47877/options.htm#CIHGDEEF), [Spatial and Graph](http://docs.oracle.com/cd/E11882_01/license.112/e47877/options.htm#CIHGDEEF), [In-Memory Database Cache](http://www.oracle.com/technetwork/products/timesten/overview/timesten-imdb-cache-101293.html), [Data Masking Pack](http://docs.oracle.com/cd/E11882_01/license.112/e47877/options.htm#CHDGEEBB) e Oracle Test Data Management Pack (come parte di Data Masking Pack).

##Risorse aggiuntive
Dopo aver configurato la macchina virtuale e creato il database, vedere gli argomenti seguenti per altre informazioni.

-	[Immagini di macchine virtuali Oracle - Considerazioni varie](virtual-machines-windows-classic-oracle-considerations.md)

-	[Raccolta di documentazione di Oracle Database 12c](http://www.oracle.com/pls/db1211/homepage)

-	[Connessione a Oracle Database da un'applicazione Java](http://docs.oracle.com/cd/E11882_01/appdev.112/e12137/getconn.htm#TDPJD136)

-	[Immagini di macchine virtuali Oracle per Azure](virtual-machines-linux-classic-oracle-images.md)

-	[Oracle Database 2 Day DBA 12c Release 1](http://docs.oracle.com/cd/E16655_01/server.121/e17643/toc.htm)

<!---HONumber=AcomDC_0601_2016-->