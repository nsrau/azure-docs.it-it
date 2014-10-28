<properties linkid="manage-windows-commontask-install-sql-server" urlDisplayName="Install SQL Server" pageTitle="Provision a SQL Server virtual machine in Azure " metaKeywords="Azure tutorial creating SQL Server, SQL Server vm, configuring SQL Server" description="A tutorial that teaches you how to create and configure a SQL Server virtual machine on Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="Provisioning a SQL Server Virtual Machine on Azure" authors="selcint" solutions="" manager="jeffreyg" editor="tyson" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="selcint"></tags>

# Provisioning di una macchina virtuale di SQL Server in Azure

Nella raccolta di macchine virtuali di Azure sono disponibili numerose immagini che contengono Microsoft SQL Server. È possibile selezionare una delle immagini di macchina virtuale dalla raccolta ed eseguire il provisioning della macchina virtuale nell'ambiente Azure con pochi clic.

In questa esercitazione si apprenderà come:

-   [Effettuare la connessione al portale di gestione di Azure ed eseguire il provisioning di una macchina virtuale dalla raccolta](#Provision)
-   [Aprire la macchina virtuale tramite Desktop remoto e completare la configurazione](#RemoteDesktop)
-   [Completare la procedura di configurazione per connettersi alla macchina virtuale mediante SQL Server Management Studio in un altro computer](#SSMS)
-   [Passaggi successivi](#Optional)

## <span id="Provision"></span>Effettuare la connessione al portale di gestione di Azure ed eseguire il provisioning di una macchina virtuale dalla raccolta</a>

1.  Accedere al [portale di gestione di Azure](http://manage.windowsazure.com) con il proprio account. Se non si dispone di un account Azure, provare la [versione di valutazione gratuita di Azure](http://www.windowsazure.com/it-it/pricing/free-trial/)

2.  Nel portale di gestione di Azure, nella parte sinistra della pagina Web fare clic su **+NEW**, su **COMPUTE**, su **VIRTUAL MACHINE** e infine su **FROM GALLERY**.

3.  Nella pagina **Create a Virtual Machine** selezionare un'immagine della macchina virtuale contenente SQL Server e quindi fare clic sulla freccia Next nella parte inferiore destra della pagina. Per le informazioni più aggiornate sulle immagini di SQL Server supportate in Azure, vedere l'argomento [Introduzione a SQL Server in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/p/?LinkId=294720) nel set di documentazione [SQL Server in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/p/?LinkId=294719).

    > [WACOM.NOTE] Se si ha una macchina virtuale creata usando l'immagine della piattaforma dell'edizione di valutazione di SQL Server, non sarà possibile aggiornarla a un'immagine dell'edizione con pagamento al minuto nella raccolta. È possibile scegliere una delle due opzioni seguenti:

    > -   È possibile creare una nuova macchina virtuale usando l'edizione di SQL Server con pagamento al minuto nella raccolta ed eseguire la migrazione dei file di database alla nuova macchina virtuale seguendo la procedura descritta nell'argomento [Come eseguire la migrazione dello schema e dei file di database di SQL Server tra macchine virtuali in Azure utilizzando dischi dati](http://go.microsoft.com/fwlink/p/?LinkId=294738). **Oppure**

    > -   È possibile eseguire l'aggiornamento di un'istanza esistente dell'edizione di valutazione di SQL Server a un'altra edizione di SQL Server nell'ambito del contratto [Mobilità delle licenze tramite Software Assurance in Azure](http://www.windowsazure.com/it-it/pricing/license-mobility/) seguendo la procedura illustrata nell'argomento [Eseguire l'aggiornamento a un'edizione diversa di SQL Server 2014](http://go.microsoft.com/fwlink/?LinkId=396915). Per informazioni su come acquistare una copia di SQL Server con licenza, vedere [Come acquistare SQL Server](http://www.microsoft.com/it-it/sqlserver/get-sql-server/how-to-buy.aspx).

4.  Nella pagina **Virtual Machine Configuration** specificare le informazioni seguenti:

    -   Specificare un nome in **VIRTUAL MACHINE NAME**.
    -   Nella casella **NEW USER NAME** digitare un nome utente univoco per l'account di amministratore locale della macchina virtuale.
    -   Nella casella **NEW PASSWORD** immettere una password complessa. Per ulteriori informazioni, vedere [Password complesse](http://msdn.microsoft.com/library/ms161962.aspx).
    -   Nella casella **CONFIRM PASSWORD** ridigitare la password.
    -   Selezionare un valore appropriato per **SIZE** nell'elenco a discesa.

    > [WACOM.NOTE] La dimensione della macchina virtuale viene specificata durante il provisioning:
    > A2 è la dimensione minima consigliata per i carichi di lavoro di produzione.
    > Se si usa SQL Server Enterprise Edition, la dimensione minima consigliata per una macchina virtuale è A3.
    > Selezionare A3 o superiore per l'uso di SQL Server Enterprise Edition.
    > Selezionare A6 per l'uso dell'immagine di SQL Server 2012 Enterprise per data warehouse.
    > Selezionare A7 per l'uso dell'immagine di SQL Server 2014 per data warehouse.
    > La dimensione selezionata limita il numero di dischi dati che è possibile configurare. Per informazioni aggiornate sulle dimensioni di macchine virtuali disponibili e sul numero di dischi dati che è possibile collegare a una macchina virtuale, vedere [Dimensioni delle macchine virtuali e dei servizi cloud per Azure]( http://msdn.microsoft.com/it-it/library/azure/dn197896.aspx).

    Fare clic sulla freccia Next nella parte inferiore destra per continuare.

    ![Configurazione macchina virtuale](./media/virtual-machines-provision-sql-server/4VM-Config.png)

5.  Nella pagina **Virtual machine mode** specificare le informazioni seguenti:

    -   Selezionare **Standalone Virtual Machine**.
    -   Nella casella **DNS NAME** immettere la prima parte di un nome DNS a scelta, affinché completi un nome nel formato **TESTNAME.cloudapp.net**
    -   Nella casella **REGION/AFFINITY GROUP/VIRTUAL NETWORK** selezionare la regione in cui verrà ospitata questa immagine virtuale.

    Fare clic sulla freccia Next per continuare.

    ![Modalità macchina virtuale][Modalità macchina virtuale]

6.  Nella pagina **Virtual machine options**:

    -   Nella casella **AVAILABILITY SET** selezionare **(none)**.
    -   Leggere e accettare le note legali.

    ![Opzioni della macchina virtuale][Opzioni della macchina virtuale]

7.  Fare clic sul segno di spunta nell'angolo inferiore destro per continuare.

8.  Attendere durante la preparazione della macchina virtuale in Azure. Lo stato della macchina virtuale attraverserà le fasi seguenti:

    -   Starting (Provisioning)
    -   Stopped
    -   Starting (Provisioning)
    -   Running (Provisioning)
    -   Running

## <span id="RemoteDesktop"></span>Aprire la macchina virtuale tramite Desktop remoto e completare la configurazione</a>

1.  Al termine del provisioning, fare clic sul nome della macchina virtuale per passare alla pagina DASHBOARD. Nella parte inferiore della pagina fare clic su **Connect**.

    ![Selezione della pagina Dashboard][Selezione della pagina Dashboard]

2.  Scegliere di aprire il file rpd usando il programma Desktop remoto Windows (`%windir%\system32\mstsc.exe`).

3.  Nella finestra di dialogo **Protezione di Windows** immettere la password per l'account Administrator locale specificata in uno dei passaggi precedenti. (Potrebbe essere richiesta la verifica delle credenziali della macchina virtuale).

4.  In occasione del primo accesso alla macchina virtuale, potrebbe essere necessario completare diversi processi, come la configurazione del desktop, l'aggiornamento di Windows e il completamento delle attività di configurazione iniziali di Windows (sysprep). Al termine delle attività di configurazione iniziali di Windows, verranno completate le attività di configurazione di SQL Server. Il completamento di queste attività potrebbe richiedere qualche minuto. `SELECT @@SERVERNAME` potrebbe non restituire il nome corretto fino al termine dell'installazione di SQL Server.

Dopo avere eseguito la connessione alla macchina virtuale con Desktop remoto Windows, la macchina virtuale funziona come qualsiasi altro computer. Eseguire normalmente la connessione all'istanza predefinita di SQL Server con SQL Server Management Studio (in esecuzione nella macchina virtuale).

## <span id="SSMS"></span>Completare la procedura di configurazione per connettersi alla macchina virtuale mediante SQL Server Management Studio in un altro computer</a>

Prima di poter eseguire la connessione all'istanza di SQL Server da Internet, è necessario completare le attività riportate di seguito, come descritto nelle sezioni seguenti:

-   [Creare un endpoint TCP per la macchina virtuale](#Endpoint)
-   [Aprire le porte TCP in Windows Firewall](#FW)
-   [Configurare SQL Server per l'ascolto sul protocollo TCP](#TCP)
-   [Configurare SQL Server per l'autenticazione in modalità mista](#Mixed)
-   [Creare gli account di accesso di SQL Server](#Logins)
-   [Determinare il nome DNS della macchina virtuale](#DNS)
-   [Eseguire la connessione al motore di database da un altro computer](#cde)
-   [Connessione al motore di database dall'applicazione] (\#cdea)

Il percorso di connessione è riepilogato nel diagramma seguente:

![Connessione a una macchina virtuale di SQL Server][Connessione a una macchina virtuale di SQL Server]

### <span id="Endpoint"></span>Creare un endpoint TCP per la macchina virtuale</a>

Nella macchina virtuale deve essere presente un endpoint per l'ascolto delle comunicazioni TCP in entrata. In questo passaggio di configurazione di Azure, il traffico della porta TCP in ingresso viene indirizzato a una porta TCP accessibile alla macchina virtuale.

1.  Nel portale di gestione di Azure fare clic su **VIRTUAL MACHINES**.

2.  Fare clic sulla macchina virtuale appena creata. Verranno visualizzate le informazioni sulla macchina virtuale.

3.  Nella parte superiore della pagina selezionare **ENDPOINTS** e quindi fare clic su **ADD ENDPOINT** nella parte inferiore della pagina.

    ![Fare clic su ADD ENDPOINT][Fare clic su ADD ENDPOINT]

4.  Nella pagina **Add Endpoint to Virtual Machine** fare clic su **Add Endpoint** e quindi sulla freccia Next per continuare.

    ![Fare clic su Add endpoint][Fare clic su Add endpoint]

5.  Nella pagina **Specify the details of the endpoint** specificare le informazioni seguenti:

    -   Nella casella **NAME** specificare un nome per l'endpoint.
    -   Nella casella **PROTOCOL** selezionare **TCP**. È possibile immettere la porta di ascolto predefinita di SQL Server **1433** nella casella **Private Port**. Allo stesso modo, è possibile specificare **57500** nella casella **PUBLIC PORT**. Si noti che molte organizzazioni selezionano numeri di porta diversi per evitare attacchi dannosi al sistema di sicurezza.

    ![Schermata Endpoint][Schermata Endpoint]

6.  Fare clic sul segno di spunta per continuare. La creazione dell'endpoint è completata.

    ![Macchina virtuale con endpoint][Macchina virtuale con endpoint]

### <span id="FW"></span>Aprire le porte TCP in Windows Firewall per l'istanza predefinita del motore di database</a>

1.  Connettersi alla macchina virtuale tramite Desktop remoto. Dopo avere eseguito l'accesso, nel menu Start fare clic su **Esegui**, digitare **WF.msc** e quindi fare clic su **OK**.

    ![Avviare Windows Firewall][Avviare Windows Firewall]

2.  In **Windows Firewall con protezione avanzata**, nel riquadro sinistro fare clic con il pulsante destro del mouse su **Regole connessioni in entrata** e quindi fare clic su **Nuova regola** nel riquadro Azioni.

    ![Nuova regola][Nuova regola]

3.  Nella finestra di dialogo **Tipo di regola** selezionare **Porta** e quindi fare clic su **Avanti**.

4.  Nella finestra di dialogo **Protocollo e porte** selezionare **TCP**. Selezionare **Porte locali specifiche** e quindi digitare il numero di porta dell'istanza del motore di database (**1433** per l'istanza predefinita o il numero di porta specificato per la porta privata nel passaggio di creazione dell'endpoint).

    ![Porta TCP 1433][Porta TCP 1433]

5.  Fare clic su **Avanti**.

6.  Nella finestra di dialogo **Operazione** selezionare **Consenti la connessione** e quindi fare clic su **Avanti**.

    **Nota di sicurezza:** per un ulteriore livello di sicurezza, è possibile selezionare **Consenti solo le connessioni protette**. Selezionare questa opzione se si desidera configurare ulteriori opzioni di sicurezza per l'ambiente in uso.

    ![Consenti la connessione][Consenti la connessione]

7.  Nella finestra di dialogo **Profilo** selezionare **Pubblico** e quindi fare clic su **Avanti**.

    **Nota di sicurezza:** la selezione di un profilo **Pubblico** consente l'accesso tramite Internet. Se possibile, scegliere un profilo più restrittivo.

    ![Profilo Pubblico][Profilo Pubblico]

8.  Nella finestra di dialogo **Nome** digitare un nome e una descrizione per la regola e quindi fare clic su **Fine**.

    ![Nome regola][Nome regola]

Aprire altre porte per altri componenti in base alle esigenze. Per ulteriori informazioni, vedere [Configurare Windows Firewall per consentire l'accesso a SQL Server](http://msdn.microsoft.com/it-it/library/cc646023.aspx).

### <span id="TCP"></span>Configurare SQL Server per l'ascolto sul protocollo TCP</a>

1.  Quando si è connessi alla macchina virtuale tramite Desktop remoto, nel menu Start fare clic su **Tutti i programmi**, **Microsoft SQL Server** ** versione**,** Strumenti di configurazione **e infine su** Gestione configurazione SQL Server.

    ![Apertura di Gestione configurazione SQL Server][Apertura di Gestione configurazione SQL Server]

2.  In Gestione configurazione SQL Server, nel riquadro console espandere **Configurazione di rete SQL Server**.

3.  Nel riquadro console fare clic su **Protocolli per *nome istanza***. (L'istanza predefinita è **Protocolli per MSSQLSERVER**).

4.  Nel riquadro dei dettagli fare clic con il pulsante destro del mouse su TCP: dovrebbe risultare abilitato per le immagini della raccolta per impostazione predefinita. Per le immagini personalizzate, fare clic su **Abilita** (se lo stato corrispondente è Disabilitato).

    ![Abilitazione del protocollo TCP][Abilitazione del protocollo TCP]

5.  Nel riquadro console fare clic su **Servizi di SQL Server**. Il riavvio del motore di database può essere rimandato fino al termine del passaggio successivo.

6.  Nel riquadro dei dettagli fare clic con il pulsante destro del mouse su **SQL Server (*nome istanza*)** (l'istanza predefinita è **SQL Server (MSSQLSERVER)**) e quindi scegliere **Riavvia** per arrestare e riavviare l'istanza di SQL Server.

    ![Riavvio del motore di database][Riavvio del motore di database]

7.  Chiudere Gestione configurazione SQL Server.

Per ulteriori informazioni su come abilitare i protocolli per il motore di database di SQL Server, vedere [Abilitare o disabilitare un protocollo di rete del server](http://msdn.microsoft.com/it-it/library/ms191294.aspx).

### <span id="Mixed"></span>Configurare SQL Server per l'autenticazione in modalità mista</a>

Il motore di database di SQL Server non può utilizzare l'Autenticazione di Windows senza ambiente di dominio. Per connettersi al motore di database da un altro computer, configurare SQL Server per l'autenticazione in modalità mista. L'autenticazione in modalità mista consente sia l'autenticazione di SQL Server sia l'autenticazione di Windows. Se è stata configurata una Rete virtuale di Azure, potrebbe non essere necessario configurare l'autenticazione in modalità mista. Per ulteriori informazioni, vedere l'argomento [Considerazioni relative alla connettività per SQL Server in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/?LinkId=294723) nel set di documentazione [SQL Server in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/?LinkId=294719).

1.  Quando si è connessi alla macchina virtuale tramite Desktop remoto, nel menu Start fare clic su **Tutti i programmi**, **Microsoft SQL Server *versione*** e quindi su **SQL Server Management Studio**.

    ![Avvio di SQL Server Management Studio][Avvio di SQL Server Management Studio]

    Alla prima apertura di Management Studio è necessario creare gli utenti dell'ambiente Management Studio. L'operazione potrebbe richiedere alcuni istanti.

2.  All'apertura verrà visualizzata la finestra di dialogo **Connetti al server** di Management Studio. Nella casella **Nome server** digitare il nome della macchina virtuale da connettere al motore di database con Esplora oggetti. Anziché il nome della macchina virtuale come **Nome server** è inoltre possibile utilizzare **(locale)** o un singolo punto. Selezionare **Autenticazione di Windows** e lasciare ***nome\_macchina\_virtuale*\\amministratore\_locale** nella casella **Nome utente**. Fare clic su **Connect**.

    ![Connetti al server][Connetti al server]

3.  In Esplora oggetti di SQL Server Management Studio fare clic con il pulsante destro del mouse sul nome dell'istanza di SQL Server (nome della macchina virtuale) e quindi scegliere **Proprietà**.

    ![Proprietà del server][Proprietà del server]

4.  Nella pagina **Sicurezza**, in **Autenticazione server** selezionare **Autenticazione di SQL Server e di Windows** e quindi fare clic su **OK**.

    ![Selezione della modalità di autenticazione][Selezione della modalità di autenticazione]

5.  Nella finestra di dialogo di SQL Server Management Studio fare clic su **OK** per confermare il requisito del riavvio di SQL Server.

6.  In Esplora oggetti fare clic con il pulsante destro del mouse sul server e quindi scegliere **Riavvia**. (Se SQL Server Agent è in esecuzione, anch'esso dovrà essere riavviato).

    ![Riavvio][Riavvio]

7.  Nella finestra di dialogo di SQL Server Management Studio fare clic su **Sì** per accettare il riavvio di SQL Server.

### <span id="Logins"></span>Creare gli account di accesso di SQL Server</a>

Per connettersi al motore di database da un altro computer, configurare almeno un account di accesso con autenticazione di SQL Server.

1.  In Esplora oggetti di SQL Server Management Studio espandere la cartella dell'istanza del server in cui si desidera creare il nuovo account di accesso.

2.  Fare clic con il pulsante destro del mouse sulla cartella **Sicurezza** scegliere **Nuovo** e quindi **Account di accesso...**.

    ![Nuovo account di accesso][Nuovo account di accesso]

3.  Nella finestra di dialogo **Account di accesso - Nuovo**, nella pagina **Generale** immettere il nome del nuovo utente nella casella **Nome account di accesso**.

4.  Selezionare **Autenticazione di SQL Server**.

5.  Nella casella **Password** digitare una password per il nuovo utente. Digitare di nuovo la password nella casella **Conferma password**.

6.  Per applicare le opzioni dei criteri password per la complessità e l'imposizione, selezionare **Applica criteri password** (scelta consigliata). Si tratta di un'opzione predefinita quando si seleziona l'autenticazione di SQL Server.

7.  Per applicare le opzioni dei criteri password per la scadenza, selezionare **Imponi scadenza password** (scelta consigliata). Per abilitare questa casella di controllo, è necessario che sia selezionata l'opzione Applica criteri password. Si tratta di un'opzione predefinita quando si seleziona l'autenticazione di SQL Server.

8.  Per forzare un utente a creare una nuova password dopo il primo utilizzo dell'account di accesso, selezionare **Richiedi modifica della password all'accesso successivo** (scelta consigliata se il nome di accesso verrà utilizzato da un altro utente. Se si tratta del proprio nome di accesso, non selezionare questa opzione). Per abilitare questa casella di controllo, è necessario che sia selezionata l'opzione Imponi scadenza password. Si tratta di un'opzione predefinita quando si seleziona l'autenticazione di SQL Server.

9.  Nell'elenco **Database predefinito** selezionare un database predefinito per il nome di accesso. **master** è il valore predefinito per questa opzione. Se il database utente non è ancora stato creato, lasciare questa opzione impostata su **master**.

10. Nell'elenco **Lingua predefinita** lasciare il valore predefinito **default**.

    ![Proprietà account di accesso][Proprietà account di accesso]

11. Se si tratta del primo account di accesso che si crea, è possibile assegnarlo all'amministratore di SQL Server. A questo scopo, nella pagina **Ruoli del server** selezionare **sysadmin**.

    **Nota di sicurezza:** i membri del ruolo predefinito del server dispongono del controllo completo sul motore di database. È consigliabile limitare attentamente le appartenenze a questo ruolo.

    ![sysadmin][sysadmin]

12. Fare clic su OK.

Per ulteriori informazioni sugli account di accesso di SQL Server, vedere [Creazione di un account di accesso](http://msdn.microsoft.com/it-it/library/aa337562.aspx).

### <span id="DNS"></span>Determinare il nome DNS della macchina virtuale</a>

Per connettersi al motore di database di AQL Server da un altro computer, è necessario conoscere il nome DNS (Domain Name System) della macchina virtuale. (Si tratta del nome utilizzato da Internet per identificare la macchina virtuale. È possibile utilizzare l'indirizzo IP, ma questo indirizzo può cambiare se Azure sposta le risorse per la ridondanza o la manutenzione. Il nome DNS rimane stabile in quanto può essere reindirizzato a un nuovo indirizzo IP).

1.  Nel portale di gestione di Azure (o dal passaggio precedente) selezionare **VIRTUAL MACHINES**.

2.  Nella pagina **VIRTUAL MACHINE INSTANCES**, nella colonna **DNS NAME** trovare e copiare il nome DNS per la macchina virtuale preceduto da **<http://>**. (Nell'interfaccia utente potrebbe non essere visualizzato l'intero nome, ma è possibile fare clic su di esso con il pulsante destro del mouse e scegliere Copia).

    ![Nome DNS][Nome DNS]

### <span id="cde"></span>Eseguire la connessione al motore di database da un altro computer</a>

1.  In un computer connesso a Internet aprire SQL Server Management Studio.

2.  Nella casella **Connetti al server** o **Connetti al motore di database**, nella casella **Nome server** immettere il nome DNS della macchina virtuale (determinato nell'attività precedente) e un numero di porta di endpoint pubblica nel formato *NomeDNS,Numero porta*, ad esempio **tutorialtestVM.cloudapp.net,57500**.

3.  Nella casella **Autenticazione** selezionare **Autenticazione di SQL Server**.

4.  Nella casella **Account di accesso** digitare il nome di un account di accesso creato in una delle attività precedenti.

5.  Nella casella **Password** digitare la password dell'account di accesso creato in una delle attività precedenti.

6.  Fare clic su **Connect**.

    ![Connessione tramite SQL Server Management Studio][Connessione tramite SQL Server Management Studio]

### <span id="cdea"></span> Connessione al motore di database dall'applicazione</a>

Se è possibile connettersi a un'istanza di SQL Server in esecuzione in una macchina virtuale di Azure tramite Management Studio, dovrebbe essere possibile connettersi anche con una stringa di connessione simile a quella riportata di seguito.

    connectionString="Server=<DNS_Name>;Integrated Security=false;User ID=<login_name>;Password=<your_password>;"providerName="System.Data.SqlClient"

Per ulteriori informazioni, vedere l'argomento relativo alla [risoluzione dei problemi di connessione al motore di database di SQL Server](http://social.technet.microsoft.com/wiki/contents/articles/how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx)].

## <span id="Optional"></span>Passaggi successivi</a>

Si è appreso come creare e configurare un'istanza di SQL Server in una macchina virtuale di Azure utilizzando l'immagine della piattaforma. Per l'utilizzo di SQL Server in Macchine virtuali di Azure, è consigliabile seguire le linee guida dettagliate fornite nel set di documentazione [SQL Server in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/p/?LinkId=294719) in MSDN Library. In questo set di documentazione è inclusa una serie di articoli ed esercitazioni che offrono linee guida dettagliate. Nella serie sono incluse le sezioni seguenti:

[SQL Server in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/p/?LinkId=294719)

[Introduzione a SQL Server in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/p/?LinkId=294720)

[Preparazione della migrazione a SQL Server in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/p/?LinkId=294721)

-   Come eseguire la migrazione dello schema e dei file di database di SQL tra macchine virtuali in Azure utilizzando dischi dati

[Distribuzione di SQL Server in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/p/?LinkId=294722)

-   Come copiare i file di dati e dell'installazione di SQL Server in un disco dati dall'ambiente locale ad Azure utilizzando CSUpload
-   Come creare una macchina virtuale di base locale utilizzando Hyper-V
-   Come creare una macchina virtuale SQL Server in Azure utilizzando il disco di SQL Server locale
-   Come creare una macchina virtuale SQL Server in Azure utilizzando la macchina virtuale SQL Server locale esistente
-   Modalità di utilizzo di PowerShell per la configurazione di una macchina virtuale di SQL Server in Azure
-   Come utilizzare un disco dati collegato per archiviare i file di database

[Considerazioni relative alla connettività per SQL Server in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/p/?LinkId=294723)

-   Esercitazione: Configurare e connettersi a più macchine virtuali di SQL Server nello stesso servizio cloud di Azure
-   Esercitazione: Configurare e connettersi a una macchina virtuale di SQL Server in un altro servizio cloud di Azure
-   Esercitazione: Connessione dell'applicazione ASP.NET a SQL Server in Azure tramite una rete virtuale

[Considerazioni relative alle prestazioni per SQL Server in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/?LinkId=294724)

[Considerazioni relative alla sicurezza per SQL Server in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/p/?LinkId=294725)

[Risoluzione dei problemi e monitoraggio per SQL Server in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/p/?LinkId=294726)

[Disponibilità elevata e ripristino di emergenza di SQL Server in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/p/?LinkId=294727)

-   Esercitazione: Gruppi di disponibilità AlwaysOn in Azure (GUI)
-   Esercitazione: Gruppi di disponibilità AlwaysOn in Azure (PowerShell)
-   Esercitazione: Configurazione del listener per i gruppi di disponibilità AlwaysOn
-   Esercitazione: Procedura guidata Aggiungi replica di Azure
-   Esercitazione: Mirroring del database per il ripristino di emergenza in Azure
-   Esercitazione: Mirroring del database per il ripristino di emergenza nell'ambiente IT ibrido
-   Esercitazione: Mirroring del database per la disponibilità elevata in Azure
-   Esercitazione: Log shipping per il ripristino di emergenza nell'ambiente IT ibrido
-   Risoluzione dei problemi relativi al listener del gruppo di disponibilità in Azure

[Backup e ripristino per SQL Server in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/p/?LinkId=294728)

[SQL Server Business Intelligence in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/p/?LinkId=294729)

-   Utilizzare PowerShell per creare una VM di Azure con SQL Server BI e SharePoint 2010
-   Utilizzare PowerShell per creare una VM di Azure con SQL Server BI e SharePoint 2013
-   Utilizzare PowerShell per creare una macchina virtuale di Azure con un server di report in modalità nativa

[SQL Server Data warehouse nelle macchine virtuali di Azure](http://msdn.microsoft.com/library/windowsazure/dn387396.aspx)

[Articoli tecnici relativi a SQL Server in Macchine virtuali di Azure](http://msdn.microsoft.com/library/azure/dn248435.aspx)

-   [White paper: Modelli di applicazione e strategie di sviluppo per SQL Server in Macchine virtuali di Azure](http://msdn.microsoft.com/library/azure/dn574746.aspx)

-   [White paper: Distribuire SQL Server Business Intelligence in Macchine virtuali di Azure](http://msdn.microsoft.com/library/windowsazure/dn321998.aspx)

-   [White paper: Linee guida sulle prestazioni per SQL Server nelle macchine virtuali di Azure](http://msdn.microsoft.com/library/windowsazure/dn248436.aspx)

-   [White paper: Controllo visualizzatore di report del servizio di creazione report e server di report basati sulle macchine virtuali di Microsoft Azure](http://msdn.microsoft.com/library/azure/dn753698.aspx)


  [Configurazione macchina virtuale]: ./media/virtual-machines-provision-sql-server/4VM-Config.png
  [Modalità macchina virtuale]: ./media/virtual-machines-provision-sql-server/5VM-Mode.png
  [Opzioni della macchina virtuale]: ./media/virtual-machines-provision-sql-server/6VM-Options.png
  [Selezione della pagina Dashboard]: ./media/virtual-machines-provision-sql-server/5VM-Connect.png
  [Connessione a una macchina virtuale di SQL Server]: ./media/virtual-machines-provision-sql-server/SQLVMConnectionsOnAzure.GIF
  [Fare clic su ADD ENDPOINT]: ./media/virtual-machines-provision-sql-server/28Add-Endpoint.png
  [Fare clic su Add endpoint]: ./media/virtual-machines-provision-sql-server/29Add-Endpoint-to-VM.png
  [Schermata Endpoint]: ./media/virtual-machines-provision-sql-server/30Endpoint-Details.png
  [Macchina virtuale con endpoint]: ./media/virtual-machines-provision-sql-server/31VM-Connect.png
  [Avviare Windows Firewall]: ./media/virtual-machines-provision-sql-server/12Open-WF.png
  [Nuova regola]: ./media/virtual-machines-provision-sql-server/13New-FW-Rule.png
  [Porta TCP 1433]: ./media/virtual-machines-provision-sql-server/14Port-1433.png
  [Consenti la connessione]: ./media/virtual-machines-provision-sql-server/15Allow-Connection.png
  [Profilo Pubblico]: ./media/virtual-machines-provision-sql-server/16Public-Profile.png
  [Nome regola]: ./media/virtual-machines-provision-sql-server/17Rule-Name.png
  [Apertura di Gestione configurazione SQL Server]: ./media/virtual-machines-provision-sql-server/9Click-SSCM.png
  [Abilitazione del protocollo TCP]: ./media/virtual-machines-provision-sql-server/10Enable-TCP.png
  [Riavvio del motore di database]: ./media/virtual-machines-provision-sql-server/11Restart.png
  [Avvio di SQL Server Management Studio]: ./media/virtual-machines-provision-sql-server/18Start-SSMS.png
  [Connetti al server]: ./media/virtual-machines-provision-sql-server/19Connect-to-Server.png
  [Proprietà del server]: ./media/virtual-machines-provision-sql-server/20Server-Properties.png
  [Selezione della modalità di autenticazione]: ./media/virtual-machines-provision-sql-server/21Mixed-Mode.png
  [Riavvio]: ./media/virtual-machines-provision-sql-server/22Restart2.png
  [Nuovo account di accesso]: ./media/virtual-machines-provision-sql-server/23New-Login.png
  [Proprietà account di accesso]: ./media/virtual-machines-provision-sql-server/24Test-Login.png
  [sysadmin]: ./media/virtual-machines-provision-sql-server/25sysadmin.png
  [Creazione di un account di accesso]: http://msdn.microsoft.com/it-it/library/aa337562.aspx
  [Nome DNS]: ./media/virtual-machines-provision-sql-server/32DNS-Name.png
  [Connessione tramite SQL Server Management Studio]: ./media/virtual-machines-provision-sql-server/33Connect-SSMS.png
 