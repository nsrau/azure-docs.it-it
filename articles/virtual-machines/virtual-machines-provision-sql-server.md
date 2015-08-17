<properties 
	pageTitle="Provisioning di una macchina virtuale di SQL Server in Azure" 
	description="Esercitazione che illustra come creare e configurare una macchina virtuale SQL Server in Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="rothja" 
	manager="jeffreyg" 
	editor="monicar"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows-sql-server" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/28/2015" 
	ms.author="jroth"/>

# Provisioning di una macchina virtuale di SQL Server in Azure #

Nella raccolta di macchine virtuali di Azure sono disponibili numerose immagini che contengono Microsoft SQL Server. È possibile selezionare una delle immagini di macchina virtuale dalla raccolta ed eseguire il provisioning della macchina virtuale nell'ambiente Azure con pochi clic.

In questa esercitazione si apprenderà come:

* [Effettuare la connessione al portale di gestione di Azure ed eseguire il provisioning di una macchina virtuale dalla raccolta](#Provision)
* [Aprire la macchina virtuale tramite Desktop remoto e completare la configurazione](#RemoteDesktop)
* [Completare la procedura di configurazione per connettersi alla macchina virtuale mediante SQL Server Management Studio in un altro computer](#SSMS)
* [Passaggi successivi](#Optional)

##<a id="Provision">Eseguire il provisioning di una macchina virtuale di SQL Server dalla raccolta</a>

1. Accedere al [portale di gestione di Azure](http://manage.windowsazure.com) con il proprio account. Se non si dispone di un account Azure, provare la [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/).

2. Nel portale di gestione di Azure, nella parte sinistra della pagina Web fare clic su **+NEW**, su **COMPUTE**, su **VIRTUAL MACHINE** e infine su **FROM GALLERY**.

3. Nella pagina **Scegli un'immagine** fare clic su **SQL SERVER**, quindi selezionare un'immagine di SQL Server. Fare clic sulla freccia nella parte inferiore destra della pagina. ![Scegli un'immagine][Image34]


Per le informazioni più aggiornate sulle immagini di SQL Server supportate in Azure, vedere l'argomento [Introduzione a SQL Server in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/p/?LinkId=294720) nel set di documentazione [SQL Server in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/p/?LinkId=294719).

   
>[AZURE.NOTE]Se si dispone di una macchina virtuale creata mediante l'immagine della piattaforma dell'edizione di valutazione di SQL Server, non sarà possibile aggiornarla a un'immagine dell'edizione con pagamento al minuto nella raccolta. È possibile scegliere una delle seguenti due opzioni: è possibile creare una nuova macchina virtuale utilizzando l'edizione SQL Server con pagamento al minuto dalla raccolta ed effettuare la migrazione dei file del database a questa nuova macchina virtuale attenendosi ai passaggi descritti in [Come eseguire la migrazione dello schema e dei file di database di SQL tra macchine virtuali in Azure usando dischi dati](http://go.microsoft.com/fwlink/p/?LinkId=294738), **o**, è possibile aggiornare un'istanza esistente dell'edizione di valutazione di SQL Server su un'edizione differente di SQL Server nel contratto [License Mobility tramite Software Assurance su Azure](http://azure.microsoft.com/pricing/license-mobility/) tramite i seguenti passaggi in [Aggiornamento a un'edizione differente di SQL Server 2014](http://go.microsoft.com/fwlink/?LinkId=396915). Per informazioni su come acquistare una copia di SQL Server con licenza, vedere [Come acquistare SQL Server](http://www.microsoft.com/sqlserver/get-sql-server/how-to-buy.aspx).


4. Nella prima pagina di **Configurazione macchina virtuale** immettere le informazioni seguenti:
	- Una **DATA DI RILASCIO VERSIONE**. Se sono disponibili più immagini, selezionare quella più recente.
	- Un **NOME MACCHINA VIRTUALE** univoco.
	- Nella casella **NUOVO NOME UTENTE** digitare un nome utente univoco per l'account di amministratore locale della macchina.
	- Nella casella **NEW PASSWORD** immettere una password complessa. 
	- Nella casella **CONFIRM PASSWORD** ridigitare la password.
	- Selezionare un valore appropriato per **SIZE** nell'elenco a discesa. 

	>[AZURE.NOTE]Le dimensioni della macchina virtuale vengono specificate durante il provisioning: A2 corrisponde alle dimensioni minime consigliate per i carichi di lavoro di produzione. Le dimensioni minime consigliate per una macchina virtuale corrispondono ad A3 quando si usa SQL Server Enterprise Edition. Selezionare A3 o un valore superiore per l'uso di SQL Server Enterprise Edition. Selezionare A4 per l'uso di immagini di SQL Server 2012 o 2014 Enterprise ottimizzato per carichi di lavoro transazionali. Selezionare A7 per l'uso di immagini di SQL Server 2012 o 2014 Enterprise ottimizzato per carichi di lavoro di data warehouse. Le dimensioni selezionate limitano il numero di dischi dati che è possibile configurare. Per informazioni aggiornate sulle dimensioni di macchine virtuali disponibili e sul numero di dischi dati che è possibile collegare a una macchina virtuale, vedere [Dimensioni delle macchine virtuali e dei servizi cloud per Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx).

	Fare clic sulla freccia Avanti nella parte inferiore destra per continuare.

	![Configurazione macchina virtuale][Image4]


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

	- Starting (Provisioning)
	- Stopped
	- Starting (Provisioning)
	- Running (Provisioning)
	- Running
	

##<a id="RemoteDesktop">Aprire la VM tramite Desktop remoto per completare l’installazione</a>

1. Al termine del provisioning, fare clic sul nome della macchina virtuale per passare alla pagina DASHBOARD. Nella parte inferiore della pagina fare clic su **Connect**.
2. Fare clic sul pulsante **Apri**. ![Fare clic sul pulsante Apri][Image37]

3. Nella finestra di dialogo **Protezione di Windows**, fare clic su **Utilizza un altro account**. ![Fare clic su Utilizza un altro account][Image38]
4. Utilizzare il nome della macchina come nome di dominio, seguito dal nome dell'amministratore nel seguente formato: `machinename\username`. Digitare la password e connettersi alla macchina.

4. In occasione del primo accesso, verranno completati diversi processi, come la configurazione del desktop, l'aggiornamento di Windows e il completamento delle attività di configurazione iniziali di Windows (sysprep). Al termine delle attività di configurazione iniziali di Windows, verranno completate le attività di configurazione di SQL Server. Queste attività potrebbero causare un ritardo di pochi minuti mentre vengono completate. `SELECT @@SERVERNAME` potrebbe non restituire il nome corretto fino al completamento dell'installazione di SQL Server e SQL Server Management Studio potrebbe non essere visibile nella pagina iniziale.

Dopo avere eseguito la connessione alla macchina virtuale con Desktop remoto Windows, la macchina virtuale funziona come qualsiasi altro computer. Eseguire normalmente la connessione all'istanza predefinita di SQL Server con SQL Server Management Studio (in esecuzione nella macchina virtuale).

##<a id="SSMS">Connettersi all'istanza di VM e SQL Server da SQL Server Management Studio da un altro computer</a>

I seguenti passaggi illustrano come connettersi all'istanza di SQL Server su internet utilizzando SQL Server Management Studio (SSMS). Tuttavia, gli stessi passaggi si applicano per rendere accessibile la macchina virtuale di SQL Server per le applicazioni in esecuzione sia in locale che in Azure.

Prima di poter eseguire la connessione all'istanza di SQL Server da un’altra VM o da Internet, è necessario completare le seguenti attività, come descritto nelle seguenti sezioni:

- [Creare un endpoint TCP per la macchina virtuale](#Endpoint)
- [Aprire le porte TCP in Windows Firewall](#FW)
- [Configurare SQL Server per l'ascolto sul protocollo TCP](#TCP)
- [Configurare SQL Server per l'autenticazione in modalità mista](#Mixed)
- [Creare gli account di accesso di SQL Server](#Logins)
- [Determinare il nome DNS della macchina virtuale](#DNS)
- [Eseguire la connessione al motore di database da un altro computer](#cde)
- [Connessione al motore di database dall'applicazione](#cdea)

Il percorso di connessione è riepilogato nel seguente diagramma:

![Connessione a una macchina virtuale di SQL Server][Image8b]

###<a id="Endpoint">Creare un endpoint TCP per la macchina virtuale</a>

Per poter accedere a SQL Server da internet, nella macchina virtuale deve essere presente un endpoint per l'ascolto delle comunicazioni TCP in entrata. In questo passaggio di configurazione di Azure, il traffico della porta TCP in ingresso viene indirizzato a una porta TCP accessibile alla macchina virtuale.

>[AZURE.NOTE]Se ci si connette all'interno della stesso servizio cloud o rete virtuale, non è necessario creare un endpoint accessibile pubblicamente. In tal caso, è possibile continuare al passaggio successivo. Per maggiori informazioni, vedere [Considerazioni relative alla connettività per SQL Server in Macchine virtuali di Azure](https://msdn.microsoft.com/library/azure/dn133152.aspx).

1. Nel portale di gestione di Azure fare clic su **VIRTUAL MACHINES**.
	
2. Fare clic sulla macchina virtuale appena creata. Verranno visualizzate le informazioni sulla macchina virtuale.
	
3. Nella parte superiore della pagina selezionare **ENDPOINT** e quindi fare clic su **AGGIUNGI** nella parte inferiore della pagina.
	
4. Nella pagina **Aggiungi un endpoint a una macchina virtuale** fare clic su **Aggiungi un endpoint autonomo**, quindi fare clic sulla freccia Avanti per continuare.
	
5. Nella pagina **Specify the details of the endpoint** specificare le informazioni seguenti:

	- Nella casella **NAME** specificare un nome per l'endpoint.
	- Nella casella **PROTOCOL** selezionare **TCP**. È possibile digitare **57500** nella casella **PUBLIC PORT**. Analogamente, è possibile immettere la porta di ascolto predefinita di SQL Server **1433** nella casella **Private Port**. Si noti che molte organizzazioni selezionano numeri di porta diversi per evitare attacchi dannosi al sistema di sicurezza. 

6. Fare clic sul segno di spunta per continuare. La creazione dell'endpoint è completata.

###<a id="FW">Aprire le porte TCP in Windows Firewall per l'istanza predefinita del motore di database</a>

1. Connettersi alla macchina virtuale tramite Desktop remoto. Una volta effettuato l'accesso, nella schermata Start digitare **WF.msc** e premere INVIO. 

	![Avviare Windows Firewall][Image12]
2. In **Windows Firewall con protezione avanzata**, nel riquadro sinistro fare clic con il pulsante destro del mouse su **Regole connessioni in entrata** e quindi fare clic su **Nuova regola** nel riquadro Azioni.

	![Nuova regola][Image13]

3. Nella finestra di dialogo **Creazione guidata nuova regola connessioni in entrata**, nella sezione **Tipo di regola**, selezionare **Porta** e quindi fare clic su **Avanti**.

4. Nella finestra di dialogo **Protocollo e porte** usare il valore predefinito per **TCP**. Selezionare la casella **Porte locali specifiche** e quindi digitare il numero di porta dell'istanza del motore di database (**1433** per l'istanza predefinita o il numero di porta specificato per la porta privata nel passaggio di creazione dell'endpoint).

	![Porta TCP 1433][Image14]

5. Fare clic su **Avanti**.

6. Nella finestra di dialogo **Operazione** selezionare **Consenti la connessione** e quindi fare clic su **Avanti**.

	**Nota sulla sicurezza:** per un ulteriore livello di sicurezza, è possibile selezionare **Consenti solo le connessioni protette**. Selezionare questa opzione per configurare altre opzioni di sicurezza per l'ambiente in uso.

	![Consentire le connessioni][Image15]

7. Nella finestra di dialogo **Profilo** selezionare **Pubblico**, **Privato** e **Dominio**. Quindi fare clic su **Avanti**.

    **Nota sulla sicurezza:** la selezione di un profilo **Pubblico** consente l'accesso tramite Internet. Se possibile, scegliere un profilo più restrittivo.

	![Profilo Pubblico][Image16]

8. Nella finestra di dialogo **Nome** digitare un nome e una descrizione per la regola e quindi fare clic su **Fine**.

	![Nome regola][Image17]

Aprire altre porte per altri componenti in base alle esigenze. Per ulteriori informazioni, vedere [Configurare Windows Firewall per consentire l'accesso a SQL Server](http://msdn.microsoft.com/library/cc646023.aspx).


###<a id="TCP">Configurare SQL Server per l'ascolto sul protocollo TCP</a>

1. Mentre si è connessi alla macchina virtuale, nella pagina iniziale digitare **Gestione configurazione SQL Server** e premere INVIO.
	
	![Apertura di Gestione configurazione SQL Server][Image9]

2. In Gestione configurazione SQL Server, nel riquadro console espandere **Configurazione di rete SQL Server**.

3. Nel riquadro console fare clic su **Protocolli per MSSQLSERVER** (nome predefinito dell'istanza). Nel riquadro dei dettagli fare clic con il pulsante destro del mouse su TCP: dovrebbe risultare abilitato per le immagini della raccolta per impostazione predefinita. Per le immagini personalizzate, fare clic su **Abilita** (se lo stato corrispondente è Disabilitato).

	![Abilitazione del protocollo TCP][Image10]

5. Nel riquadro console fare clic su **Servizi di SQL Server**. Nel riquadro dei dettagli fare clic con il pulsante destro del mouse su **SQL Server (_nome istanza_)** (l'istanza predefinita è **SQL Server (MSSQLSERVER)**) e quindi scegliere **Riavvia** per arrestare e riavviare l'istanza di SQL Server.

	![Riavvio del motore di database][Image11]

7. Chiudere Gestione configurazione SQL Server.

Per ulteriori informazioni su come abilitare i protocolli per il motore di database di SQL Server, vedere [Abilitare o disabilitare un protocollo di rete del server](http://msdn.microsoft.com/library/ms191294.aspx).

###<a id="Mixed">Configurare SQL Server per l'autenticazione in modalità mista</a>

Il motore di database di SQL Server non può usare l'Autenticazione di Windows senza ambiente di dominio. Per connettersi al motore di database da un altro computer, configurare SQL Server per l'autenticazione in modalità mista. L'autenticazione in modalità mista consente sia l'autenticazione di SQL Server sia l'autenticazione di Windows.

>[AZURE.NOTE]Se è stata configurata una Rete virtuale di Azure con un ambiente di dominio configurato, potrebbe non essere necessario configurare l'autenticazione in modalità mista.

1. Mentre si è connessi alla macchina virtuale, nella pagina iniziale digitare **SQL Server 2014 Management Studio** e fare clic sull'icona selezionata.

	![Avvio di SQL Server Management Studio][Image18]

	Alla prima apertura di Management Studio è necessario creare gli utenti dell'ambiente Management Studio. L'operazione potrebbe richiedere alcuni istanti.

2. Verrà visualizzata la finestra di dialogo **Connetti al server** di Management Studio. Nella casella **Nome server** digitare il nome della macchina virtuale da connettere al motore di database con Esplora oggetti. Anziché il nome della macchina virtuale come **Nome server** è inoltre possibile utilizzare **(locale)** o un singolo punto. Selezionare **Autenticazione di Windows** e lasciare **_nome\_macchina\_virtuale_\\amministratore\_locale** nella casella **Nome utente**. Fare clic su **Connect**.

	![Connetti al server][Image19]

3. In Esplora oggetti di SQL Server Management Studio fare clic con il pulsante destro del mouse sul nome dell'istanza di SQL Server (nome della macchina virtuale) e quindi scegliere **Proprietà**.

	![Proprietà del server][Image20]

4. Nella pagina **Sicurezza**, in **Autenticazione server** selezionare **Autenticazione di SQL Server e di Windows** e quindi fare clic su **OK**.

	![Selezione della modalità di autenticazione][Image21]

5. Nella finestra di dialogo di SQL Server Management Studio fare clic su **OK** per confermare il requisito del riavvio di SQL Server.

6. In Esplora oggetti fare clic con il pulsante destro del mouse sul server e quindi scegliere **Riavvia**. (Se SQL Server Agent è in esecuzione, anch'esso dovrà essere riavviato).

	![Riavvio][Image22]

7. Nella finestra di dialogo di SQL Server Management Studio fare clic su **Sì** per accettare il riavvio di SQL Server.

###<a id="Logins">Creare gli account di accesso di SQL Server</a>

Per connettersi al motore di database da un altro computer, configurare almeno un account di accesso con autenticazione di SQL Server.

1. In Esplora oggetti di SQL Server Management Studio espandere la cartella dell'istanza del server in cui si desidera creare il nuovo account di accesso.

2. Fare clic con il pulsante destro del mouse sulla cartella **Sicurezza** scegliere **Nuovo** e quindi **Account di accesso...**.

	![Nuovo account di accesso][Image23]

3. Nella finestra di dialogo **Account di accesso - Nuovo**, nella pagina **Generale** immettere il nome del nuovo utente nella casella **Nome account di accesso**.

4. Selezionare **Autenticazione di SQL Server**.

5. Nella casella **Password** digitare una password per il nuovo utente. Digitare di nuovo la password nella casella **Conferma password**.

6. Per applicare le opzioni dei criteri password per la complessità e l'imposizione, selezionare **Applica criteri password** (scelta consigliata). Si tratta di un'opzione predefinita quando si seleziona l'autenticazione di SQL Server.

7. Per applicare le opzioni dei criteri password per la scadenza, selezionare **Imponi scadenza password** (scelta consigliata). Per abilitare questa casella di controllo, è necessario che sia selezionata l'opzione Applica criteri password. Si tratta di un'opzione predefinita quando si seleziona l'autenticazione di SQL Server.

8. Per forzare un utente a creare una nuova password dopo il primo utilizzo dell'account di accesso, selezionare **Richiedi modifica della password all'accesso successivo** (scelta consigliata se il nome di accesso verrà utilizzato da un altro utente. Se si tratta del proprio nome di accesso, non selezionare questa opzione). Per abilitare questa casella di controllo, è necessario che sia selezionata l'opzione Imponi scadenza password. Si tratta di un'opzione predefinita quando si seleziona l'autenticazione di SQL Server.

9. Nell'elenco **Database predefinito** selezionare un database predefinito per il nome di accesso. **master** è il valore predefinito per questa opzione. Se il database utente non è ancora stato creato, lasciare questa opzione impostata su **master**.

10. Nell'elenco **Lingua predefinita** lasciare il valore predefinito **default**.
    
	![Proprietà account di accesso][Image24]

11. Se si tratta del primo account di accesso che si crea, è possibile assegnarlo all'amministratore di SQL Server. A questo scopo, nella pagina **Ruoli del server** selezionare **sysadmin**.

	**Nota sulla sicurezza:** i membri del ruolo predefinito del server dispongono del controllo completo sul motore di database. È consigliabile limitare attentamente le appartenenze a questo ruolo.

	![sysadmin][Image25]

12. Fare clic su OK.

Per ulteriori informazioni sugli account di accesso di SQL Server, vedere [Creazione di un account di accesso](http://msdn.microsoft.com/library/aa337562.aspx).



###<a id="DNS">Determinare il nome DNS della macchina virtuale</a>

Per connettersi al motore di database di SQL Server da un altro computer, è necessario conoscere il nome DNS (Domain Name System) della macchina virtuale. (Si tratta del nome utilizzato da Internet per identificare la macchina virtuale. È possibile utilizzare l'indirizzo IP, ma questo indirizzo può cambiare se Azure sposta le risorse per la ridondanza o la manutenzione. Il nome DNS rimane stabile in quanto può essere reindirizzato a un nuovo indirizzo IP).

1. Nel portale di gestione di Azure (o dal passaggio precedente) selezionare **VIRTUAL MACHINES**. 

2. Nella pagina **ISTANZE MACCHINA VIRTUALE**, nella colonna **Riepilogo rapido**, trovare e copiare il nome DNS per la macchina virtuale.

![Nome DNS][Image35]
	

### <a id="cde">Eseguire la connessione al motore di database da un altro computer</a>
 
1. In un computer connesso a Internet aprire SQL Server Management Studio.
2. Nella finestra di dialogo **Connetti al server** o **Connetti al motore di database**, nella casella **Server name** immettere il nome DNS della macchina virtuale (determinato nell'attività precedente) e un numero di porta di endpoint pubblica nel formato *NomeDNS,Numeroporta*, ad esempio **tutorialtestVM.cloudapp.net,57500**. Per ottenere il numero di porta, accedere al portale di gestione di Azure e trovare la macchina virtuale. Nel dashboard fare clic su **ENDPOINTS** e usare il valore di **PUBLIC PORT** assegnato a **MSSQL**. ![Public Port][Image36]
3. Nella casella **Autenticazione** selezionare **Autenticazione di SQL Server**.
5. Nella casella **Account di accesso** digitare il nome di un account di accesso creato in una delle attività precedenti.
6. Nella casella **Password** digitare la password dell'account di accesso creato in una delle attività precedenti.
7. Fare clic su **Connect**.

	![Connessione tramite SQL Server Management Studio][Image33]

## <a id="cdea"> Connessione al motore di database dall'applicazione</a>

Se è possibile connettersi a un'istanza di SQL Server in esecuzione in una macchina virtuale di Azure tramite Management Studio, dovrebbe essere possibile connettersi anche con una stringa di connessione simile a quella riportata di seguito.

	connectionString = "Server=tutorialtestVM.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Per altre informazioni, vedere l'argomento relativo alla [risoluzione dei problemi di connessione al motore di database di SQL Server](http://social.technet.microsoft.com/wiki/contents/articles/how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx).

##<a id="Optional">Passaggi successivi</a>
Si è appreso come creare e configurare un'istanza di SQL Server in una macchina virtuale di Azure usando l'immagine della piattaforma. In molti casi, il passaggio successivo consiste nella migrazione dei database in questa nuova macchina virtuale di SQL Server. Per linee guida sulla migrazione di database, vedere [Migrazione di un database a SQL Server in una macchina virtuale di Azure](virtual-machines-migrate-onpremises-database.md).

Oltre a queste risorse, è consigliabile seguire le linee guida dettagliate fornite nella documentazione [SQL Server in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/p/?LinkId=294719) nella libreria. In questo set di documentazione è inclusa una serie di articoli ed esercitazioni che offrono linee guida dettagliate. Nella serie sono incluse le seguenti sezioni:

[SQL Server in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/p/?LinkId=294719)

[Introduzione a SQL Server in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/p/?LinkId=294720)

[Preparazione della migrazione a SQL Server in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/p/?LinkId=294721)

[Considerazioni relative alla connettività per SQL Server in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/p/?LinkId=294723)

[Considerazioni relative alle prestazioni per SQL Server in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/?LinkId=294724)

[Considerazioni relative alla sicurezza per SQL Server in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/p/?LinkId=294725)

[Risoluzione dei problemi e monitoraggio per SQL Server in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/p/?LinkId=294726)

[Disponibilità elevata e ripristino di emergenza di SQL Server in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/p/?LinkId=294727)

- Esercitazione: gruppi di disponibilità AlwaysOn in Azure (GUI)
- Esercitazione: gruppi di disponibilità AlwaysOn in Azure (PowerShell)
- Esercitazione: configurazione del listener per i gruppi di disponibilità AlwaysOn
- Esercitazione: procedura guidata Aggiungi replica di Azure
- Risoluzione dei problemi relativi al listener del gruppo di disponibilità in Azure

[Backup e ripristino per SQL Server in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/p/?LinkId=294728)

[SQL Server Business Intelligence in Macchine virtuali di Azure](http://go.microsoft.com/fwlink/p/?LinkId=294729)

[SQL Server Data warehouse e carichi di lavoro transazionali in macchine virtuali di Azure](http://msdn.microsoft.com/library/windowsazure/dn387396.aspx)

[Articoli tecnici relativi a SQL Server in Macchine virtuali di Azure](http://msdn.microsoft.com/library/azure/dn248435.aspx)

- [White paper: informazioni sul database SQL di Azure e SQL Server in Macchine virtuali di Azure](sql-database/data-management-azure-sql-database-and-sql-server-iaas.md)

- [White paper: modelli di applicazione e strategie di sviluppo per SQL Server in Macchine virtuali di Azure](http://msdn.microsoft.com/library/azure/dn574746.aspx)

[Image4]: ./media/virtual-machines-provision-sql-server/4VM-Config.png
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
[Image16]: ./media/virtual-machines-provision-sql-server/16Public-Private-Domain-Profile.png
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
[Image34]: ./media/virtual-machines-provision-sql-server/choose-sql-vm.png
[Image35]: ./media/virtual-machines-provision-sql-server/sql-vm-dns-name.png
[Image36]: ./media/virtual-machines-provision-sql-server/sql-vm-port-number.png
[Image37]: ./media/virtual-machines-provision-sql-server/click-open-to-connect.png
[Image38]: ./media/virtual-machines-provision-sql-server/credentials.png
 

<!---HONumber=August15_HO6-->