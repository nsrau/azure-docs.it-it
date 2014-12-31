<properties urlDisplayName="Install SQL Server" pageTitle="Eseguire il provisioning di una macchina virtuale di SQL Server in Azure " metaKeywords="Azure tutorial creating SQL Server, SQL Server vm, configuring SQL Server" description="A tutorial that teaches you how to create and configure a SQL Server virtual machine on Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="Provisioning a SQL Server Virtual Machine on Azure" authors="selcint" solutions="" manager="jhubbard" editor="tyson" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/30/2014" ms.author="" />





# Provisioning di una macchina virtuale di SQL Server in Azure #

Nella raccolta di macchine virtuali di Azure sono disponibili numerose immagini che contengono Microsoft SQL Server. È possibile selezionare una delle immagini di macchina virtuale dalla raccolta ed eseguire il provisioning della macchina virtuale nell'ambiente Azure con pochi clic.

In questa esercitazione si apprenderà come:

* [Effettuare la connessione al portale di gestione di Azure ed eseguire il provisioning di una macchina virtuale dalla raccolta](#Provision)
* [Aprire la macchina virtuale tramite Desktop remoto e completare la configurazione](#RemoteDesktop)
* [Completare la procedura di configurazione per connettersi alla macchina virtuale mediante SQL Server Management Studio in un altro computer](#SSMS)
* [Passaggi successivi](#Optional)

##<a id="Provision">Effettuare la connessione al portale di gestione di Azure ed eseguire il provisioning di una macchina virtuale dalla raccolta</a>

1. Accedere al [portale di gestione di Azure](http://manage.windowsazure.com) con il proprio account. Se non si dispone di un account Azure, provare la [versione di valutazione gratuita di Azure](http://www.windowsazure.com/it-it/pricing/free-trial/).

2. Nel portale di gestione di Azure, nella parte inferiore sinistra della pagina Web fare clic su **+NUOVO**, su **CALCOLO**, su **MACCHINA VIRTUALE** e infine su **DA RACCOLTA**.

3. Nella pagina **Crea macchina virtuale** selezionare un'immagine della macchina virtuale contenente SQL Server e quindi fare clic sulla freccia Avanti nella parte inferiore destra della pagina. Per le informazioni più aggiornate sulle immagini di SQL Server supportate in Azure, vedere l'argomento [Introduzione a SQL Server in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/p/?LinkId=294720) nel set di documentazione [SQL Server in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/p/?LinkId=294719). 

    >[WACOM.NOTE] Se si ha una macchina virtuale creata usando l'immagine della piattaforma dell'edizione di valutazione di SQL Server, non sarà possibile aggiornarla a un'immagine dell'edizione con pagamento al minuto nella raccolta. È possibile scegliere una delle due opzioni seguenti:
    
    È possibile creare una nuova macchina virtuale usando l'edizione di SQL Server con pagamento al minuto nella raccolta ed eseguire la migrazione dei file di database alla nuova macchina virtuale seguendo la procedura descritta nell'argomento [Come eseguire la migrazione dello schema e dei file di database di SQL Server tra macchine virtuali in Azure usando dischi dati](http://go.microsoft.com/fwlink/p/?LinkId=294738). **Oppure**

    È possibile eseguire l'aggiornamento di un'istanza esistente dell'edizione di valutazione di SQL Server a un'altra edizione di SQL Server nell'ambito del contratto [Mobilità delle licenze tramite Software Assurance in Azure](http://www.windowsazure.com/it-it/pricing/license-mobility/) seguendo la procedura illustrata nell'argomento [Eseguire l'aggiornamento a un'edizione diversa di SQL Server 2014](http://go.microsoft.com/fwlink/?LinkId=396915). Per informazioni su come acquistare una copia di SQL Server con licenza, vedere [Come acquistare SQL Server](http://www.microsoft.com/it-it/sqlserver/get-sql-server/how-to-buy.aspx).
   

4. Nella prima pagina di **Configurazione macchina virtuale** immettere le informazioni seguenti:
	- Specificare un nome in **NOME MACCHINA VIRTUALE**.
	- Nella casella **NUOVO NOME UTENTE** digitare un nome utente univoco per l'account di amministratore locale della macchina virtuale.
	- Nella casella **NUOVA PASSWORD** immettere una password complessa. Per altre informazioni, vedere [Password complesse](http://msdn.microsoft.com/library/ms161962.aspx).
	- Nella casella **CONFERMA PASSWORD** ridigitare la password.
	- Selezionare un valore appropriato per **DIMENSIONI** nell'elenco a discesa. 

	>[WACOM.NOTE] Le dimensioni della macchina virtuale vengono specificate durante il provisioning:
 	> A2 corrisponde alle dimensioni minime consigliate per i carichi di lavoro di produzione. 
    > Le dimensioni minime consigliate per una macchina virtuale corrispondono ad A3 quando si usa SQL Server Enterprise Edition.
    > Selezionare A3 o un valore superiore per l'uso di SQL Server Enterprise Edition.
   	> Selezionare A4 per l'uso di immagini di SQL Server 2012 o 2014 Enterprise ottimizzato per carichi di lavoro transazionali.  
   	> Selezionare A7 per l'uso di immagini di SQL Server 2012 o 2014 Enterprise ottimizzato per carichi di lavoro di data warehouse. 
   	> Le dimensioni selezionate limitano il numero di dischi dati che è possibile configurare. Per informazioni aggiornate sulle dimensioni di macchine virtuali disponibili e sul numero di dischi dati che è possibile collegare a una macchina virtuale, vedere [Dimensioni delle macchine virtuali e dei servizi cloud per Azure](http://msdn.microsoft.com/it-it/library/azure/dn197896.aspx).

	Fare clic sulla freccia Next nella parte inferiore destra per continuare.

	![VM Configuration](./media/virtual-machines-provision-sql-server/4VM-Config.png)


5. Nella seconda pagina di **Configurazione macchina virtuale** configurare le risorse per la rete, l'archiviazione e la disponibilità:
	- Nella casella **Servizio cloud** scegliere **Crea un nuovo servizio cloud**.
	- Nella casella **Nome DNS del servizio cloud** specificare la prima parte del nome DNS desiderato, in modo che completi un nome nel formato **TESTNAME.cloudapp.net**. 
	- Nella casella **AREA/GRUPPO DI AFFINITÀ/RETE VIRTUALE** selezionare l'area in cui verrà ospitata l'immagine virtuale.
	- In **Account di archiviazione** selezionare un account di archiviazione esistente o sceglierne uno generato automaticamente.
	- Nella casella **SET DI DISPONIBILITÀ** selezionare **(nessuno)**.
	- Leggere e accettare le note legali.
	

6. Fare clic sulla freccia Avanti per continuare.


7. Fare clic sul segno di spunta nell'angolo inferiore destro per continuare.

8. Attendere durante la preparazione della macchina virtuale in Azure. Lo stato della macchina virtuale attraverserà le fasi seguenti:

	- Starting (Provisioning)
	- Stopped
	- Starting (Provisioning)
	- Running (Provisioning)
	- Running
	

##<a id="RemoteDesktop">Aprire la macchina virtuale tramite Desktop remoto e completare la configurazione</a>

1. Al termine del provisioning, fare clic sul nome della macchina virtuale per passare alla pagina DASHBOARD. Nella parte inferiore della pagina fare clic su **Connetti**.

	
2. Scegliere di aprire il file rpd usando il programma Desktop remoto Windows (`%windir%\system32\mstsc.exe`).

	
3. Nella finestra di dialogo **Sicurezza di Windows** immettere la password per l'account Administrator locale specificata in uno dei passaggi precedenti. (Potrebbe essere richiesta la verifica delle credenziali della macchina virtuale).

4. In occasione del primo accesso alla macchina virtuale, potrebbe essere necessario completare diversi processi, come la configurazione del desktop, l'aggiornamento di Windows e il completamento delle attività di configurazione iniziali di Windows (sysprep). Al termine delle attività di configurazione iniziali di Windows, verranno completate le attività di configurazione di SQL Server. Il completamento di queste attività potrebbe richiedere qualche minuto. `SELECT @@SERVERNAME` potrebbe non restituire il nome corretto fino al termine dell'installazione di SQL Server.

Dopo avere eseguito la connessione alla macchina virtuale con Desktop remoto Windows, la macchina virtuale funziona come qualsiasi altro computer. Eseguire normalmente la connessione all'istanza predefinita di SQL Server con SQL Server Management Studio (in esecuzione nella macchina virtuale). 

##<a id="SSMS">Completare la procedura di configurazione per connettersi alla macchina virtuale mediante SQL Server Management Studio in un altro computer</a>

Prima di poter eseguire la connessione all'istanza di SQL Server da Internet, è necessario completare le attività riportate di seguito, come descritto nelle sezioni seguenti:

- [Creare un endpoint TCP per la macchina virtuale](#Endpoint)
- [Aprire le porte TCP in Windows Firewall](#FW)
- [Configurare SQL Server per l'ascolto sul protocollo TCP](#TCP)
- [Configurare SQL Server per l'autenticazione in modalità mista](#Mixed)
- [Creare gli account di accesso di SQL Server](#Logins)
- [Determinare il nome DNS della macchina virtuale](#DNS)
- [Eseguire la connessione al motore di database da un altro computer](#cde)
- [Connessione al motore di database dall'applicazione](#cdea)

Il percorso di connessione è riepilogato nel diagramma seguente:

![Connecting to a SQL Server virtual machine][Image8b]

###<a id="Endpoint">Creare un endpoint TCP per la macchina virtuale</a>

Nella macchina virtuale deve essere presente un endpoint per l'ascolto delle comunicazioni TCP in entrata. In questo passaggio di configurazione di Azure, il traffico della porta TCP in ingresso viene indirizzato a una porta TCP accessibile alla macchina virtuale.

1. Nel portale di gestione di Azure fare clic su **MACCHINE VIRTUALI**.

	
2. Fare clic sulla macchina virtuale appena creata. Verranno visualizzate le informazioni sulla macchina virtuale.
	

3. Nella parte superiore della pagina selezionare **ENDPOINT** e quindi fare clic su **AGGIUNGI** nella parte inferiore della pagina.
	

4. Nella pagina **Aggiungi un endpoint a una macchina virtuale** fare clic su **Aggiungi un endpoint autonomo**, quindi fare clic sulla freccia Avanti per continuare.

	
5. Nella pagina **Specificare i dettagli dell'endpoint** specificare le informazioni seguenti:

	- Nella casella **NOME** specificare un nome per l'endpoint.
	- Nella casella **PROTOCOLLO** selezionare **TCP**. Nella casella **PORTA PUBBLICA** digitare **57500**. Analogamente, nella casella **Porta privata** digitare la porta di ascolto predefinita di SQL Server **1433**. Si noti che molte organizzazioni selezionano numeri di porta diversi per evitare attacchi dannosi al sistema di sicurezza. 


6. Fare clic sul segno di spunta per continuare. La creazione dell'endpoint è completata.
	

###<a id="FW">Aprire le porte TCP in Windows Firewall per l'istanza predefinita del motore di database</a>

1. Connettersi alla macchina virtuale tramite Desktop remoto. Dopo avere eseguito l'accesso, nel menu Start fare clic su **Esegui**, digitare **WF.msc** e quindi fare clic su **OK**.

	![Start the Firewall Program][Image12]
2. In **Windows Firewall con protezione avanzata**, nel riquadro sinistro fare clic con il pulsante destro del mouse su **Regole connessioni in entrata** e quindi fare clic su **Nuova regola** nel riquadro Azioni.

	![New Rule][Image13]

3. Nella finestra di dialogo **Tipo di regola** selezionare **Porta** e quindi fare clic su **Avanti**.

4. Nella finestra di dialogo **Protocollo e porte** selezionare **TCP**. Selezionare **Porte locali specifiche** e quindi digitare il numero di porta dell'istanza del motore di database (**1433** per l'istanza predefinita o il numero di porta specificato per la porta privata nel passaggio di creazione dell'endpoint). 

	![TCP Port 1433][Image14]

5. Fare clic su **Avanti**.

6. Nella finestra di dialogo **Azione** selezionare **Consenti la connessione** e quindi fare clic su **Avanti**.

	**Nota di sicurezza:** per un ulteriore livello di sicurezza, è possibile selezionare **Consenti solo le connessioni protette**. Selezionare questa opzione se si desidera configurare ulteriori opzioni di sicurezza per l'ambiente in uso.

	![Allow Connections][Image15]

7. Nella finestra di dialogo **Profilo** selezionare **Pubblico** e quindi fare clic su **Avanti**. 

    **Nota di sicurezza:**  la selezione di un profilo **Pubblico** consente l'accesso tramite Internet. Se possibile, scegliere un profilo più restrittivo.

	![Public Profile][Image16]

8. Nella finestra di dialogo **Nome** digitare un nome e una descrizione per la regola e quindi fare clic su **Fine**.

	![Rule Name][Image17]

Aprire altre porte per altri componenti in base alle esigenze. Per altre informazioni, vedere [Configurare Windows Firewall per consentire l'accesso a SQL Server](http://msdn.microsoft.com/it-it/library/cc646023.aspx).


###<a id="TCP">Configurare SQL Server per l'ascolto sul protocollo TCP</a>

1. Quando si è connessi alla macchina virtuale tramite Desktop remoto, nel menu Start fare clic su **Tutti i programmi**, **Microsoft SQL Server** *versione*, **Strumenti di configurazione** e infine su **Gestione configurazione SQL Server**.
	
	![Open SSCM][Image9]

2. In Gestione configurazione SQL Server, nel riquadro console espandere **Configurazione di rete SQL Server**.

3. Nel riquadro console fare clic su **Protocolli per _nome istanza_**. L'istanza predefinita è **Protocolli per MSSQLSERVER**.

4. Nel riquadro dei dettagli fare clic con il pulsante destro del mouse su TCP: dovrebbe risultare abilitato per le immagini della raccolta per impostazione predefinita. Per le immagini personalizzate, fare clic su **Abilita** (se lo stato corrispondente è Disabilitato).

	![Enable TCP][Image10]

5. Nel riquadro console fare clic su **Servizi di SQL Server**. Il riavvio del motore di database può essere rimandato fino al termine del passaggio successivo.

6. Nel riquadro dei dettagli fare clic con il pulsante destro del mouse su **SQL Server (_nome istanza_)** (l'istanza predefinita è **SQL Server (MSSQLSERVER)**), quindi fare clic su **Riavvia** per arrestare e riavviare l'istanza di SQL Server. 

	![Restart Database Engine][Image11]

7. Chiudere Gestione configurazione SQL Server.

Per altre informazioni su come abilitare i protocolli per il motore di database di SQL Server, vedere [Abilitare o disabilitare un protocollo di rete del server](http://msdn.microsoft.com/it-it/library/ms191294.aspx).

###<a id="Mixed">Configurare SQL Server per l'autenticazione in modalità mista</a>

Il motore di database di SQL Server non può usare l'Autenticazione di Windows senza ambiente di dominio. Per connettersi al motore di database da un altro computer, configurare SQL Server per l'autenticazione in modalità mista. L'autenticazione in modalità mista consente sia l'autenticazione di SQL Server sia l'autenticazione di Windows. Se è stata configurata una Rete virtuale di Azure, potrebbe non essere necessario configurare l'autenticazione in modalità mista. Per altre informazioni, vedere l'argomento [Considerazioni relative alla connettività per SQL Server in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/?LinkId=294723) nel set di documentazione [SQL Server in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/?LinkId=294719).

1. Quando si è connessi alla macchina virtuale tramite Desktop remoto, nel menu Start fare clic su **Tutti i programmi**, **Microsoft SQL Server _versione_** e quindi su **SQL Server Management Studio**. 

	![Start SSMS][Image18]

	Alla prima apertura di Management Studio è necessario creare gli utenti dell'ambiente Management Studio. L'operazione potrebbe richiedere alcuni istanti.

2. All'apertura verrà visualizzata la finestra di dialogo **Connetti al server** di Management Studio. Nella casella **Nome server** digitare il nome della macchina virtuale da connettere al motore di database con Esplora oggetti. Anziché il nome della macchina virtuale come **Nome server** è anche possibile usare **(locale)** o un singolo punto. Selezionare **Autenticazione di Windows** e lasciare **_nome_macchina_virtuale_\amministratore_locale** nella casella **Nome utente**. Fare clic su **Connetti**.

	![Connect to Server][Image19]

3. In Esplora oggetti di SQL Server Management Studio fare clic con il pulsante destro del mouse sul nome dell'istanza di SQL Server (nome della macchina virtuale) e quindi scegliere **Proprietà**.

	![Server Properties][Image20]

4. Nella pagina **Sicurezza**, in **Autenticazione server** selezionare **Autenticazione di SQL Server e di Windows** e quindi fare clic su **OK**.

	![Select Authentication Mode][Image21]

5. Nella finestra di dialogo di SQL Server Management Studio fare clic su **OK** per confermare il requisito del riavvio di SQL Server.

6. In Esplora oggetti fare clic con il pulsante destro del mouse sul server e quindi scegliere **Riavvia**. (Se SQL Server Agent è in esecuzione, anch'esso dovrà essere riavviato).

	![Restart][Image22]

7. Nella finestra di dialogo di SQL Server Management Studio fare clic su **Sì** per accettare il riavvio di SQL Server.

###<a id="Logins">Creare gli account di accesso di SQL Server</a>

Per connettersi al motore di database da un altro computer, configurare almeno un account di accesso con autenticazione di SQL Server.

1. In Esplora oggetti di SQL Server Management Studio espandere la cartella dell'istanza del server in cui si desidera creare il nuovo account di accesso.

2. Fare clic con il pulsante destro del mouse sulla cartella **Sicurezza**, scegliere **Nuovo**, quindi selezionare **Account di accesso**.

	![New Login][Image23]

3. Nella finestra di dialogo **Account di accesso - Nuovo**, nella pagina **Generale** immettere il nome del nuovo utente nella casella **Nome account di accesso**.

4. Selezionare **Autenticazione di SQL Server**.

5. Nella casella **Password** digitare una password per il nuovo utente. Digitare di nuovo la password nella casella **Conferma password**.

6. Per applicare le opzioni dei criteri password per la complessità e l'imposizione, selezionare **Applica criteri password** (scelta consigliata). Si tratta di un'opzione predefinita quando si seleziona l'autenticazione di SQL Server.

7. Per applicare le opzioni dei criteri password per la scadenza, selezionare **Imponi scadenza password** (scelta consigliata). Per abilitare questa casella di controllo, è necessario che sia selezionata l'opzione Applica criteri password. Si tratta di un'opzione predefinita quando si seleziona l'autenticazione di SQL Server.

8. Per forzare un utente a creare una nuova password dopo il primo uso dell'account di accesso, selezionare **Richiedi modifica della password all'accesso successivo** (scelta consigliata se il nome di accesso verrà usato da un altro utente. Se si tratta del proprio nome di accesso, non selezionare questa opzione). Per abilitare questa casella di controllo, è necessario che sia selezionata l'opzione Imponi scadenza password. Si tratta di un'opzione predefinita quando si seleziona l'autenticazione di SQL Server. 

9. Nell'elenco **Database predefinito** selezionare un database predefinito per il nome di accesso. **master** è il valore predefinito per questa opzione. Se il database utente non è ancora stato creato, lasciare questa opzione impostata su **master**.

10. Nell'elenco **Lingua predefinita** lasciare il valore predefinito **default**.
    
	![Login Properties][Image24]

11. Se si tratta del primo account di accesso che si crea, è possibile assegnarlo all'amministratore di SQL Server. A questo scopo, nella pagina **Ruoli del server** selezionare **sysadmin**. 

	**Nota di sicurezza:** i membri del ruolo predefinito del server dispongono del controllo completo sul motore di database. È consigliabile limitare attentamente le appartenenze a questo ruolo.

	![sysadmin][Image25]

12. Fare clic su OK.

Per altre informazioni sugli account di accesso di SQL Server, vedere [Creazione di un account di accesso](http://msdn.microsoft.com/it-it/library/aa337562.aspx).



###<a id="DNS">Determinare il nome DNS della macchina virtuale</a>

Per connettersi al motore di database di AQL Server da un altro computer, è necessario conoscere il nome DNS (Domain Name System) della macchina virtuale. (Si tratta del nome usato da Internet per identificare la macchina virtuale. È possibile usare l'indirizzo IP, ma questo indirizzo può cambiare se Azure sposta le risorse per la ridondanza o la manutenzione. Il nome DNS rimane stabile in quanto può essere reindirizzato a un nuovo indirizzo IP).  

1. Nel portale di gestione di Azure (o dal passaggio precedente) selezionare **MACCHINE VIRTUALI**. 

2. Nella pagina **ISTANZE MACCHINA VIRTUALE**, nella colonna **NOME DNS** trovare e copiare il nome DNS per la macchina virtuale preceduto da **http://**. (Nell'interfaccia utente potrebbe non essere visualizzato l'intero nome, ma è possibile fare clic su di esso con il pulsante destro del mouse e scegliere Copia).
	

### <a id="cde">Eseguire la connessione al motore di database da un altro computer</a>
 
1. In un computer connesso a Internet aprire SQL Server Management Studio.

2. Nella finestra di dialogo **Connetti al server** o **Connetti al motore di database**, nella casella **Nome server** immettere il nome DNS della macchina virtuale (determinato nell'attività precedente) e un numero di porta di endpoint pubblica nel formato *NomeDNS,Numeroporta*, ad esempio **tutorialtestVM.cloudapp.net,57500**.

3. Nella casella **Autenticazione** selezionare **Autenticazione di SQL Server**.

4. Nella casella **Account di accesso** digitare il nome di un account di accesso creato in una delle attività precedenti.

5. Nella casella **Password** digitare la password dell'account di accesso creato in una delle attività precedenti.

6. Fare clic su **Connetti**.

	![Connect using SSMS][Image33]

### <a id="cdea"> Connessione al motore di database dall'applicazione</a>

Se è possibile connettersi a un'istanza di SQL Server in esecuzione in una macchina virtuale di Azure tramite Management Studio, dovrebbe essere possibile connettersi anche con una stringa di connessione simile a quella riportata di seguito.

	connectionString="Server=<DNS_Name>;Integrated Security=false;User ID=<login_name>;Password=<your_password>;"providerName="System.Data.SqlClient"

Per altre informazioni, vedere l'argomento relativo alla [risoluzione dei problemi di connessione al motore di database di SQL Server](http://social.technet.microsoft.com/wiki/contents/articles/how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx).

##<a id="Optional">Passaggi successivi</a>
Si è appreso come creare e configurare un'istanza di SQL Server in una macchina virtuale di Azure usando l'immagine della piattaforma. Per l'uso di SQL Server in Macchine virtuali di Azure, è consigliabile seguire le linee guida dettagliate fornite nel set di documentazione [SQL Server in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/p/?LinkId=294719) in MSDN Library. In questo set di documentazione è inclusa una serie di articoli ed esercitazioni che offrono linee guida dettagliate. Nella serie sono incluse le sezioni seguenti:

[SQL Server in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/p/?LinkId=294719)

[Introduzione a SQL Server in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/p/?LinkId=294720)

[Preparazione della migrazione a SQL Server in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/p/?LinkId=294721)

- Come eseguire la migrazione dello schema e dei file di database di SQL tra macchine virtuali in Azure usando dischi dati

[Distribuzione di SQL Server in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/p/?LinkId=294722)

- Come copiare i file di dati e dell'installazione di SQL Server in un disco dati dall'ambiente locale ad Azure usando CSUpload
- Come creare una macchina virtuale di base locale usando Hyper-V
- Come creare una macchina virtuale SQL Server in Azure usando il disco di SQL Server locale
- Come creare una macchina virtuale SQL Server in Azure usando la macchina virtuale SQL Server locale esistente 
- Modalità di uso di PowerShell per la configurazione di una macchina virtuale di SQL Server in Azure 
- Come usare un disco dati collegato per archiviare i file di database

[Considerazioni relative alla connettività per SQL Server in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/p/?LinkId=294723)

- Esercitazione: Configurare e connettersi a più macchine virtuali di SQL Server nello stesso servizio cloud di Azure 
- Esercitazione: Configurare e connettersi a una macchina virtuale di SQL Server in un altro servizio cloud di Azure 
- Esercitazione: Connessione dell'applicazione ASP.NET a SQL Server in Azure tramite una rete virtuale 

[Considerazioni relative alle prestazioni per SQL Server in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/?LinkId=294724)

[Considerazioni relative alla sicurezza per SQL Server in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/p/?LinkId=294725)

[Risoluzione dei problemi e monitoraggio per SQL Server in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/p/?LinkId=294726)

[Disponibilità elevata e ripristino di emergenza di SQL Server in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/p/?LinkId=294727)

- Esercitazione: Gruppi di disponibilità AlwaysOn in Azure (GUI)
- Esercitazione: Gruppi di disponibilità AlwaysOn in Azure (PowerShell)
- Esercitazione: Configurazione del listener per i gruppi di disponibilità AlwaysOn
- Esercitazione: Procedura guidata Aggiungi replica di Azure
- Esercitazione: Mirroring del database per il ripristino di emergenza in Azure
- Esercitazione: Mirroring del database per il ripristino di emergenza nell'ambiente IT ibrido 
- Esercitazione: Mirroring del database per la disponibilità elevata in Azure
- Esercitazione: Log shipping per il ripristino di emergenza nell'ambiente IT ibrido 
- Risoluzione dei problemi relativi al listener del gruppo di disponibilità in Azure

[Backup e ripristino per SQL Server in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/p/?LinkId=294728)

[SQL Server Business Intelligence in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/p/?LinkId=294729)

- Usare PowerShell per creare una VM di Azure con SQL Server BI e SharePoint 2010
- Usare PowerShell per creare una VM di Azure con SQL Server BI e SharePoint 2013
- Usare PowerShell per creare una macchina virtuale di Azure con un server di report in modalità nativa

[SQL Server Data warehouse nelle macchine virtuali di Azure](http://msdn.microsoft.com/library/windowsazure/dn387396.aspx)

[Articoli tecnici per SQL Server in Macchine virtuali di Azure](http://msdn.microsoft.com/library/azure/dn248435.aspx)

- [White paper: Informazioni sul database SQL di Azure e su SQL Server in Macchine virtuali di Azure](http://azure.microsoft.com/it-it/documentation/articles/data-management-azure-sql-database-and-sql-server-iaas/)

- [White paper: Modelli di applicazione e strategie di sviluppo per SQL Server in Macchine virtuali di Azure](http://msdn.microsoft.com/library/azure/dn574746.aspx)

- [White paper: Distribuire SQL Server Business Intelligence nell macchine virtuali di Azure](http://msdn.microsoft.com/library/windowsazure/dn321998.aspx)

- [White paper: Linee guida sulle prestazioni per SQL Server nelle macchine virtuali di Azure](http://msdn.microsoft.com/library/windowsazure/dn248436.aspx)

- [White paper: Controllo visualizzatore di report del servizio di creazione report e server di report basati sulle macchine virtuali di Microsoft Azure](http://msdn.microsoft.com/library/azure/dn753698.aspx)

[Image5]: ./media/virtual-machines-provision-sql-server/5VM-Mode.png
[Image5b]: ./media/virtual-machines-provision-sql-server/5VM-Connect.png
[Image6]: ./media/virtual-machines-provision-sql-server/6VM-Options.png
[Image8b]: ./media/virtual-machines-provision-sql-server/SQLServerinVMConnectionMap.png
[Image9]: ./media/virtual-machines-provision-sql-server/9Click-SSCM.png
[Image10]: ./media/virtual-machines-provision-sql-server/10Enable-TCP.png
[Image11]: ./media/virtual-machines-provision-sql-server/11Restart.png
[Image12]: ./media/virtual-machines-provision-sql-server/12Open-WF.png
[Image13]: ./media/virtual-machines-provision-sql-server/13New-FW-Rule.png
[Image14]: ./media/virtual-machines-provision-sql-server/14Port-1433.png
[Image15]: ./media/virtual-machines-provision-sql-server/15Allow-Connection.png
[Image16]: ./media/virtual-machines-provision-sql-server/16Public-Profile.png
[Image17]: ./media/virtual-machines-provision-sql-server/17Rule-Name.png
[Image18]: ./media/virtual-machines-provision-sql-server/18Start-SSMS.png
[Image19]: ./media/virtual-machines-provision-sql-server/19Connect-to-Server.png
[Image20]: ./media/virtual-machines-provision-sql-server/20Server-Properties.png
[Image21]: ./media/virtual-machines-provision-sql-server/21Mixed-Mode.png
[Image22]: ./media/virtual-machines-provision-sql-server/22Restart2.png
[Image23]: ./media/virtual-machines-provision-sql-server/23New-Login.png
[Image24]: ./media/virtual-machines-provision-sql-server/24Test-Login.png
[Image25]: ./media/virtual-machines-provision-sql-server/25sysadmin.png
[Image28]: ./media/virtual-machines-provision-sql-server/28Add-Endpoint.png
[Image29]: ./media/virtual-machines-provision-sql-server/29Add-Endpoint-to-VM.png
[Image30]: ./media/virtual-machines-provision-sql-server/30Endpoint-Details.png
[Image31]: ./media/virtual-machines-provision-sql-server/31VM-Connect.png
[Image32]: ./media/virtual-machines-provision-sql-server/32DNS-Name.png
[Image33]: ./media/virtual-machines-provision-sql-server/33Connect-SSMS.png


<!--HONumber=35_1-->
