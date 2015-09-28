<properties pageTitle="Creazione di una macchina virtuale Oracle Database in Azure" description="Seguire un esempio di creazione di una macchina virtuale Oracle in Microsoft Azure, quindi creare un database Oracle in Azure." services="virtual-machines" authors="bbenz" documentationCenter=""/>
<tags ms.service="virtual-machines" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="infrastructure-services" ms.date="06/22/2015" ms.author="bbenz" />
	
#Creazione di una macchina virtuale Oracle Database in Azure
Nell'esempio seguente viene mostrato come creare una macchina virtuale basata su un'immagine Oracle Database fornita da Microsoft in esecuzione su Windows Server 2012 in Azure. Sono disponibili due passaggi: Innanzitutto, creare la macchina virtuale e quindi creare il Database Oracle all'interno della macchina virtuale. Nell'esempio mostrato viene utilizzata la versione 12c di Oracle Database, ma i passaggi per la versione 11g sono praticamente identici.

##Per creare una macchina virtuale Oracle Database in Azure

1.	Accedere al [Portale di Azure](https://ms.portal.azure.com/).

2.	Fare clic su **Marketplace**, su **Calcolo**, quindi digitare **Oracle** nella casella di ricerca.

3.	Selezionare una delle immagini Oracle Database disponibili tra cui** versione 11g, versione 12c, Standard Edition, Enterprise Edition oppure uno dei raggruppamenti di opzioni comuni o avanzate.** Esaminare le informazioni sull'immagine selezionata (come le dimensioni minime consigliate), quindi fare clic su **Avanti**.

4.	Specificare un **nome host** per la macchina virtuale.

5.	Specificare un **nome utente** per la macchina virtuale. Tenere presente che questo utente può essere utilizzato per l’accesso in remoto alla macchina virtuale e non è il nome utente del database Oracle.

6.	Specificare e confermare una password per la macchina virtuale o fornire una chiave pubblica SSH.

7.	Scegliere un **livello di prezzo**. Per impostazione predefinita vengono visualizzati i livelli di prezzo consigliati. Per visualizzare tutte le opzioni di configurazione, fare clic su **Visualizza tutto** in alto a destra.

8.	Impostare la configurazione facoltativa in base alle esigenze, con queste considerazioni:

	a. Lasciare **Account di archiviazione** invariato per creare un nuovo account di archiviazione con il nome della macchina virtuale.

	b. Lasciare **Set di disponibilità** come “Non configurato”.

	c. Al momento non aggiungere alcun **endpoint**.

9.	Scegliere o creare un gruppo di risorse.

10. Scegliere una **sottoscrizione**.

11. Scegliere una **posizione**.

12. Fare clic su **Crea** e verrà avviato il processo di creazione di una VM. Quando lo stato della VM è **In esecuzione**, procedere con il passaggio successivo.


##Creazione del database utilizzando la macchina virtuale Oracle Database in Azure

1.	Accedere al [Portale di Azure](https://ms.portal.azure.com/).

2.	Fare clic su **Macchine virtuali**.

3.	Fare clic sul nome della macchina virtuale a cui si desidera accedere.

4.	Fare clic su **Connect**.

5.	Rispondere ai prompt visualizzati per connettersi alla macchina virtuale. Quando vengono richiesti il nome e la password dell'amministratore, usare i valori specificati durante la creazione della macchina virtuale.

6.	Creare una variabile di ambiente denominata **ORACLE\_HOSTNAME** con il valore impostato sul nome del computer della VM. È possibile creare una variabile di ambiente utilizzando la procedura seguente:

	a. In Windows, fare clic su **Start**, digitare **Pannello di controllo**, fare clic sull’icona di **Pannello di controllo**, su **Sistema e sicurezza**, su **Sistema**, quindi su **Impostazioni di sistema avanzate**.

	b. Fare clic sulla scheda **Avanzate**, quindi su **Variabili d’ambiente**.

	c. Sotto la sezione **Variabili di sistema**, fare clic su **Nuova** per creare la variabile.

	d. Nella finestra di dialogo **Nuova variabile di sistema**, immettere **ORACLE\_HOSTNAME** per il nome della variabile, quindi il nome del computer della VM come valore. Per determinare il nome del computer, aprire un prompt dei comandi ed eseguire **SET COMPUTERNAME** (l'output del comando conterrà il nome del computer).

	e. Fare clic su **OK** per salvare la nuova variabile di ambiente e chiudere la finestra di dialogo **Nuova variabile di sistema**.

	f. Chiudere le altre finestre di dialogo che sono state aperte da Pannello di controllo.

7.	In Windows, fare clic su **Start**, quindi digitare **Assistente configurazione database**. Fare clic sull’icona di **Assistente configurazione database**.

8.	All'interno della procedura guidata di **Assistente configurazione database**, fornire i valori in base alle esigenze per ciascun passaggio della finestra di dialogo:

	a. **Passaggio 1:** fare clic su **Crea database**, quindi su **Avanti**.

		![](media/virtual-machines-creating-oracle-database-virtual-machine/image5.png)

	b. **Passaggio 2:** immettere un valore per **Nome globale del database**. Immettere e confermare un valore per **Password amministrativa**. Questa password è per l’utente **sistema**del database Oracle. Deselezionare l'opzione **Crea come database contenitore**. Fare clic su **Avanti**.

		![](media/virtual-machines-creating-oracle-database-virtual-machine/image6.png)

	c. **Passaggio 3:** la verifica dei prerequisiti dovrebbe procedere automaticamente, avanzando al **Passaggio 4**.

	d. **Passaggio 4:** esaminare le opzioni di **Crea database - Riepilogo**, quindi fare clic su **Fine**.

		![](media/virtual-machines-creating-oracle-database-virtual-machine/image7.png)
	e. **Passaggio 5:** nella **pagina di avanzamento** verrà segnalato lo stato di creazione del database.

		![](media/virtual-machines-creating-oracle-database-virtual-machine/image8.png)
	f. Dopo aver creato il database, sarà possibile scegliere di utilizzare la finestra di dialogo **Gestione password**. Modificare le impostazioni della password se necessario per le proprie esigenze, quindi chiudere le finestre di dialogo per uscire dalla procedura guidata **Assistente configurazione database**.

##Conferma dell’installazione del database

1.	Restare connessi alla macchina virtuale e avviare un prompt dei comandi SQL Plus. In Windows, fare clic su *Start**, quindi digitare **SQL Plus**. Fare clic sull’icona di **SQL Plus**.

2.	Quando richiesto, accedere con il nome utente di **SISTEMA** e la password specificata durante la creazione del database Oracle.

3.	Nel prompt dei comandi SQL Plus eseguire il comando seguente:

		**select * from GLOBAL\_NAME;**

	Il risultato dovrebbe essere il nome globale del database creato.

	![](media/virtual-machines-creating-oracle-database-virtual-machine/image9.png)

##Autorizzazione dell’accesso in remoto del database
Per autorizzare l’accesso in remoto del database (ad esempio, da un computer client che esegue codice Java), sarà necessario avviare il listener del database, aprire la porta 1521 nel firewall della macchina virtuale e creare un endpoint pubblico per la porta 1521.

### Avvio del listener del database
1.	Accedere alla macchina virtuale.

2.	Aprire un prompt dei comandi.

3.	Nel prompt dei comandi, eseguire il seguente comando:

		**lsnrctl start**

> [AZURE.NOTE]È possibile eseguire **Stato Isnrctl** per verificare lo stato del listener. Quando si desidera interrompere il listener, è possibile eseguire **Termina Isnrctl**.

### Apertura della porta 1521 nel firewall della macchina virtuale

1.	Dopo aver effettuato l’accesso alla macchina virtuale, in Windows, fare clic su **Start**, digitare **Windows Firewall con sicurezza avanzata**, quindi fare clic sull’icona di **Windows Firewall con sicurezza avanzata**. Viene aperta la console di gestione di **Windows Firewall con sicurezza avanzata**.

2.	All’interno della console di gestione del firewall, fare clic su **Regole connessioni in entrata** nel riquadro a sinistra (se non viene visualizzata l’opzione **Regole connessioni in entrata**, espandere il nodo principale nel riquadro a sinistra), quindi fare clic su **Nuova regola** nel riquadro a destra.

3.	Per **Tipo regola**, selezionare **Porta**, quindi fare clic su **Avanti**.

4.	Per **Protocollo e porte**, selezionare **TCP**, **Porte locali specifiche**, immettere **1521** per la porta, quindi fare clic su **Avanti**.

5.	Selezionare **Consenti la connessione** e fare clic su **Avanti**.

6.	Accettare le impostazioni predefinite per i profili per i quali viene applicata la regola e fare clic su **Avanti**.

7.	Specificare un nome per la regola e facoltativamente una descrizione, quindi fare clic su **Fine**.

### Creazione di un endpoint pubblico per la porta 1521

1.	Accedere al [Portale di Azure](https://ms.portal.azure.com/).

2.	Fare clic su **Sfoglia**.

3.  Fare clic su **Macchine virtuali**.

4.	Selezionare la macchina virtuale.

5.	Fare clic su **Impostazioni**.

6.	Fare clic su **Endpoint**.

7.	Fare clic su **Aggiungi**.

8.	Specificare un nome per l'endpoint.

	a. Utilizzare **TCP** per il protocollo.

	b. Utilizzare **1521** per la porta pubblica.

	c. Utilizzare **1521** per la porta privata.

9.	Lasciare le altre opzioni invariate.

10. Fare clic su **OK**.

##Abilitazione dell'accesso remoto a Oracle Database Enterprise Manager
Se si desidera abilitare l'accesso remoto a Oracle Database Enterprise Manager, aprire la porta 5500 nel firewall e creare un endpoint della macchina virtuale per 5500 nel portale di Azure (utilizzando la procedura mostrata in precedenza per l’apertura della porta 1521 e la creazione di un endpoint per 1521). Quindi, per eseguire Oracle Enterprise Manager dal computer remoto, aprire un browser all'URL nel formato `http://<<unique_domain_name>>:5500/em`.

> [AZURE.NOTE]\(È possibile determinare il valore per *\<\<unique\_domain\_name\>* all'interno del [portale di Azure](https://ms.portal.azure.com/) facendo clic su **Macchine virtuali**, quindi selezionando la macchina virtuale che si utilizza per eseguire Oracle Database.)

##Configurazione dei raggruppamenti di opzioni comuni e avanzate
Se si sceglie **Oracle Database with Popular Options** o **Oracle Database with Advanced Options bundle**, il passaggio successivo consiste nel configurare le funzionalità dei componenti aggiuntivi nell'installazione di Oracle. Consultare la documentazione di Oracle per istruzioni sull'impostazione di tali opzioni in Windows, dal momento che le configurazioni possono variare notevolmente in base alle esigenze di ogni singolo componente.

**Oracle Database with Popular Options bundle** include Oracle Database Enterprise Edition e istanze di [Partitioning](http://www.oracle.com/us/products/database/options/partitioning/overview/index.html), [Active Data Guard](http://www.oracle.com/us/products/database/options/active-data-guard/overview/index.html), [Oracle Tuning Pack for Database](http://docs.oracle.com/html/A86647_01/tun_ovw.htm), [Oracle Diagnostics Pack for Database](http://docs.oracle.com/cd/B28359_01/license.111/b28287/options.htm#CIHIHDDJ) e [Oracle Lifecycle Management Pack for Database](http://www.oracle.com/technetwork/oem/lifecycle-mgmt-495331.html) comprese nella licenza.

**Oracle Database with Advanced Options bundle** include istanze comprese nella licenza di tutte le opzioni nel raggruppamento di opzioni comuni, oltre a [Advanced Compression](http://www.oracle.com/us/products/database/options/advanced-compression/overview/index.html), [Advanced Security](http://www.oracle.com/us/products/database/options/advanced-security/overview/index.html), [Label Security](http://www.oracle.com/us/products/database/options/label-security/overview/index.html), [Database Vault](http://www.oracle.com/us/products/database/options/database-vault/overview/index.html), [Advanced Analytics](http://www.oracle.com/us/products/database/options/advanced-analytics/overview/index.html), [OLAP](http://docs.oracle.com/cd/E11882_01/license.112/e47877/options.htm#CIHGDEEF), [Spatial and Graph](http://docs.oracle.com/cd/E11882_01/license.112/e47877/options.htm#CIHGDEEF), [In-Memory Database Cache](http://www.oracle.com/technetwork/products/timesten/overview/timesten-imdb-cache-101293.html), [Data Masking Pack](http://docs.oracle.com/cd/E11882_01/license.112/e47877/options.htm#CHDGEEBB) e Oracle Test Data Management Pack (come parte di Data Masking Pack).

##Risorse aggiuntive
Dopo aver configurato la macchina virtuale e creato il database, vedere gli argomenti seguenti per ulteriori informazioni.

-	[Immagini di macchine virtuali Oracle - Considerazioni varie](virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images.md)

-	[Raccolta di documentazione di Oracle Database 12c](http://www.oracle.com/pls/db1211/homepage)

-	[Connessione a Oracle Database da un'applicazione Java](http://docs.oracle.com/cd/E11882_01/appdev.112/e12137/getconn.htm#TDPJD136)

-	[Immagini di macchine virtuali Oracle per Azure](virtual-machines-oracle-list-oracle-virtual-machine-images.md)

-	[Oracle Database 2 Day DBA 12c Release 1](http://docs.oracle.com/cd/E16655_01/server.121/e17643/toc.htm)

<!---HONumber=Sept15_HO3-->