I passaggi seguenti illustrano come connettersi all'istanza di SQL Server su Internet mediante SQL Server Management Studio (SSMS). Tuttavia, gli stessi passaggi si applicano per rendere accessibile la macchina virtuale di SQL Server per le applicazioni in esecuzione sia in locale che in Azure.

Prima di poter eseguire la connessione all'istanza di SQL Server da un’altra VM o da Internet, è necessario completare le seguenti attività, come descritto nelle seguenti sezioni:

- [Creare un endpoint TCP per la macchina virtuale](#create-a-tcp-endpoint-for-the-virtual-machine)
- [Aprire le porte TCP in Windows Firewall](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
- [Configurare SQL Server per l'ascolto sul protocollo TCP](#configure-sql-server-to-listen-on-the-tcp-protocol)
- [Configurare SQL Server per l'autenticazione in modalità mista](#configure-sql-server-for-mixed-mode-authentication)
- [Creare gli account di accesso di SQL Server](#create-sql-server-authentication-logins)
- [Determinare il nome DNS della macchina virtuale](#determine-the-dns-name-of-the-virtual-machine)
- [Eseguire la connessione al motore di database da un altro computer](#connect-to-the-database-engine-from-another-computer)

Il percorso di connessione è riepilogato nel seguente diagramma:

![Connessione a una macchina virtuale di SQL Server](./media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

### Creare un endpoint TCP per la macchina virtuale

Per poter accedere a SQL Server da internet, nella macchina virtuale deve essere presente un endpoint per l'ascolto delle comunicazioni TCP in entrata. In questo passaggio di configurazione di Azure, il traffico della porta TCP in ingresso viene indirizzato a una porta TCP accessibile alla macchina virtuale.

>[AZURE.NOTE]Se ci si connette all'interno della stesso servizio cloud o rete virtuale, non è necessario creare un endpoint accessibile pubblicamente. In tal caso, è possibile continuare al passaggio successivo. Per altre informazioni, vedere [Scenari di connessione](../articles/virtual-machines/virtual-machines-sql-server-connectivity.md#connection-scenarios).

1. Nel portale di gestione di Azure fare clic su **VIRTUAL MACHINES**.
	
2. Fare clic sulla macchina virtuale appena creata. Verranno visualizzate le informazioni sulla macchina virtuale.
	
3. Nella parte superiore della pagina selezionare **ENDPOINT** e quindi fare clic su **AGGIUNGI** nella parte inferiore della pagina.
	
4. Nella pagina **Aggiungi un endpoint a una macchina virtuale** fare clic su **Aggiungi un endpoint autonomo**, quindi fare clic sulla freccia Avanti per continuare.
	
5. Nella pagina **Specify the details of the endpoint** specificare le informazioni seguenti:

	- Nella casella **NAME** specificare un nome per l'endpoint.
	- Nella casella **PROTOCOL** selezionare **TCP**. È possibile digitare **57500** nella casella **PUBLIC PORT**. Analogamente, è possibile immettere la porta di ascolto predefinita di SQL Server **1433** nella casella **Private Port**. Si noti che molte organizzazioni selezionano numeri di porta diversi per evitare attacchi dannosi al sistema di sicurezza. 

6. Fare clic sul segno di spunta per continuare. La creazione dell'endpoint è completata.

### Aprire le porte TCP in Windows Firewall per l'istanza predefinita del motore di database

1. Connettersi alla macchina virtuale tramite Desktop remoto. Una volta effettuato l'accesso, nella schermata Start digitare **WF.msc** e premere INVIO. 

	![Avviare Windows Firewall](./media/virtual-machines-sql-server-connection-steps/12Open-WF.png)
2. In **Windows Firewall con protezione avanzata**, nel riquadro sinistro fare clic con il pulsante destro del mouse su **Regole connessioni in entrata** e quindi fare clic su **Nuova regola** nel riquadro Azioni.

	![Nuova regola](./media/virtual-machines-sql-server-connection-steps/13New-FW-Rule.png)

3. Nella finestra di dialogo **Creazione guidata nuova regola connessioni in entrata**, nella sezione **Tipo di regola**, selezionare **Porta** e quindi fare clic su **Avanti**.

4. Nella finestra di dialogo **Protocollo e porte** usare il valore predefinito per **TCP**. Selezionare la casella **Porte locali specifiche** e quindi digitare il numero di porta dell'istanza del motore di database (**1433** per l'istanza predefinita o il numero di porta specificato per la porta privata nel passaggio relativo all'endpoint).

	![Porta TCP 1433](./media/virtual-machines-sql-server-connection-steps/14Port-1433.png)

5. Fare clic su **Avanti**.

6. Nella finestra di dialogo **Operazione** selezionare **Consenti la connessione** e quindi fare clic su **Avanti**.

	**Nota sulla sicurezza:** per un ulteriore livello di sicurezza, è possibile selezionare **Consenti solo le connessioni protette**. Selezionare questa opzione per configurare altre opzioni di sicurezza per l'ambiente in uso.

	![Consentire le connessioni](./media/virtual-machines-sql-server-connection-steps/15Allow-Connection.png)

7. Nella finestra di dialogo **Profilo** selezionare **Pubblico**, **Privato** e **Dominio**. Quindi fare clic su **Avanti**.

    **Nota sulla sicurezza:** la selezione di un profilo **Pubblico** consente l'accesso tramite Internet. Se possibile, scegliere un profilo più restrittivo.

	![Profilo Pubblico](./media/virtual-machines-sql-server-connection-steps/16Public-Private-Domain-Profile.png)

8. Nella finestra di dialogo **Nome** digitare un nome e una descrizione per la regola e quindi fare clic su **Fine**.

	![Nome regola](./media/virtual-machines-sql-server-connection-steps/17Rule-Name.png)

Aprire altre porte per altri componenti in base alle esigenze. Per ulteriori informazioni, vedere [Configurare Windows Firewall per consentire l'accesso a SQL Server](http://msdn.microsoft.com/library/cc646023.aspx).


### Configurare SQL Server per l'ascolto sul protocollo TCP

1. Mentre si è connessi alla macchina virtuale, nella pagina iniziale digitare **Gestione configurazione SQL Server** e premere INVIO.
	
	![Apertura di SQL Server Management Studio](./media/virtual-machines-sql-server-connection-steps/9Click-SSCM.png)

2. In Gestione configurazione SQL Server, nel riquadro console espandere **Configurazione di rete SQL Server**.

3. Nel riquadro console fare clic su **Protocolli per MSSQLSERVER** (nome predefinito dell'istanza). Nel riquadro dei dettagli fare clic con il pulsante destro del mouse su TCP: dovrebbe risultare abilitato per le immagini della raccolta per impostazione predefinita. Per le immagini personalizzate, fare clic su **Abilita** (se lo stato corrispondente è Disabilitato).

	![Abilitazione del protocollo TCP](./media/virtual-machines-sql-server-connection-steps/10Enable-TCP.png)

5. Nel riquadro console fare clic su **Servizi di SQL Server**. Nel riquadro dei dettagli fare clic con il pulsante destro del mouse su **SQL Server (_nome istanza_)** (l'istanza predefinita è **SQL Server (MSSQLSERVER)**) e quindi scegliere **Riavvia** per arrestare e riavviare l'istanza di SQL Server.

	![Riavvio del motore di database](./media/virtual-machines-sql-server-connection-steps/11Restart.png)

7. Chiudere Gestione configurazione SQL Server.

Per ulteriori informazioni su come abilitare i protocolli per il motore di database di SQL Server, vedere [Abilitare o disabilitare un protocollo di rete del server](http://msdn.microsoft.com/library/ms191294.aspx).

### Configurare SQL Server per l'autenticazione in modalità mista

Il motore di database di SQL Server non può utilizzare l'Autenticazione di Windows senza ambiente di dominio. Per connettersi al motore di database da un altro computer, configurare SQL Server per l'autenticazione in modalità mista. L'autenticazione in modalità mista consente sia l'autenticazione di SQL Server sia l'autenticazione di Windows.

>[AZURE.NOTE]Se è stata configurata una Rete virtuale di Azure con un ambiente di dominio configurato, potrebbe non essere necessario configurare l'autenticazione in modalità mista.

1. Mentre si è connessi alla macchina virtuale, nella pagina iniziale digitare **SQL Server 2014 Management Studio** e fare clic sull'icona selezionata.

	![Avvio di SQL Server Management Studio](./media/virtual-machines-sql-server-connection-steps/18Start-SSMS.png)

	Alla prima apertura di Management Studio è necessario creare gli utenti dell'ambiente Management Studio. L'operazione potrebbe richiedere alcuni istanti.

2. Verrà visualizzata la finestra di dialogo **Connetti al server** di Management Studio. Nella casella **Nome server** digitare il nome della macchina virtuale da connettere al motore di database con Esplora oggetti. Anziché il nome della macchina virtuale come **Nome server** è inoltre possibile utilizzare **(locale)** o un singolo punto. Selezionare **Autenticazione di Windows** e lasciare **_nome\_macchina\_virtuale_\\amministratore\_locale** nella casella **Nome utente**. Fare clic su **Connect**.

	![Connetti al server](./media/virtual-machines-sql-server-connection-steps/19Connect-to-Server.png)

3. In Esplora oggetti di SQL Server Management Studio fare clic con il pulsante destro del mouse sul nome dell'istanza di SQL Server (nome della macchina virtuale) e quindi scegliere **Proprietà**.

	![Proprietà del server](./media/virtual-machines-sql-server-connection-steps/20Server-Properties.png)

4. Nella pagina **Sicurezza**, in **Autenticazione server** selezionare **Autenticazione di SQL Server e di Windows** e quindi fare clic su **OK**.

	![Selezione della modalità di autenticazione](./media/virtual-machines-sql-server-connection-steps/21Mixed-Mode.png)

5. Nella finestra di dialogo di SQL Server Management Studio fare clic su **OK** per confermare il requisito del riavvio di SQL Server.

6. In Esplora oggetti fare clic con il pulsante destro del mouse sul server e quindi scegliere **Riavvia**. (Se SQL Server Agent è in esecuzione, anch'esso dovrà essere riavviato).

	![Riavvio](./media/virtual-machines-sql-server-connection-steps/22Restart2.png)

7. Nella finestra di dialogo di SQL Server Management Studio fare clic su **Sì** per accettare il riavvio di SQL Server.

### Creare gli account di accesso di SQL Server

Per connettersi al motore di database da un altro computer, configurare almeno un account di accesso con autenticazione di SQL Server.

1. In Esplora oggetti di SQL Server Management Studio espandere la cartella dell'istanza del server in cui si desidera creare il nuovo account di accesso.

2. Fare clic con il pulsante destro del mouse sulla cartella **Sicurezza** scegliere **Nuovo** e quindi **Account di accesso...**.

	![Nuovo account di accesso](./media/virtual-machines-sql-server-connection-steps/23New-Login.png)

3. Nella finestra di dialogo **Account di accesso - Nuovo**, nella pagina **Generale** immettere il nome del nuovo utente nella casella **Nome account di accesso**.

4. Selezionare **Autenticazione di SQL Server**.

5. Nella casella **Password** digitare una password per il nuovo utente. Digitare di nuovo la password nella casella **Conferma password**.

6. Per applicare le opzioni dei criteri password per la complessità e l'imposizione, selezionare **Applica criteri password** (scelta consigliata). Si tratta di un'opzione predefinita quando si seleziona l'autenticazione di SQL Server.

7. Per applicare le opzioni dei criteri password per la scadenza, selezionare **Imponi scadenza password** (scelta consigliata). Per abilitare questa casella di controllo, è necessario che sia selezionata l'opzione Applica criteri password. Si tratta di un'opzione predefinita quando si seleziona l'autenticazione di SQL Server.

8. Per forzare un utente a creare una nuova password dopo il primo utilizzo dell'account di accesso, selezionare **Richiedi modifica della password all'accesso successivo** (scelta consigliata se il nome di accesso verrà utilizzato da un altro utente. Se si tratta del proprio nome di accesso, non selezionare questa opzione). Per abilitare questa casella di controllo, è necessario che sia selezionata l'opzione Imponi scadenza password. Si tratta di un'opzione predefinita quando si seleziona l'autenticazione di SQL Server.

9. Nell'elenco **Database predefinito** selezionare un database predefinito per il nome di accesso. **master** è il valore predefinito per questa opzione. Se il database utente non è ancora stato creato, lasciare questa opzione impostata su **master**.

10. Nell'elenco **Lingua predefinita** lasciare il valore predefinito **default**.
    
	![Proprietà account di accesso](./media/virtual-machines-sql-server-connection-steps/24Test-Login.png)

11. Se si tratta del primo account di accesso che si crea, è possibile assegnarlo all'amministratore di SQL Server. A questo scopo, nella pagina **Ruoli del server** selezionare **sysadmin**.

	**Nota sulla sicurezza:** i membri del ruolo predefinito del server dispongono del controllo completo sul motore di database. È consigliabile limitare attentamente le appartenenze a questo ruolo.

	![sysadmin](./media/virtual-machines-sql-server-connection-steps/25sysadmin.png)

12. Fare clic su OK.

Per ulteriori informazioni sugli account di accesso di SQL Server, vedere [Creazione di un account di accesso](http://msdn.microsoft.com/library/aa337562.aspx).

### Determinare il nome DNS della macchina virtuale

Per connettersi al motore di database di SQL Server da un altro computer, è necessario conoscere il nome DNS (Domain Name System) della macchina virtuale. (Si tratta del nome utilizzato da Internet per identificare la macchina virtuale. È possibile utilizzare l'indirizzo IP, ma questo indirizzo può cambiare se Azure sposta le risorse per la ridondanza o la manutenzione. Il nome DNS rimane stabile in quanto può essere reindirizzato a un nuovo indirizzo IP).

1. Nel portale di gestione di Azure (o dal passaggio precedente) selezionare **VIRTUAL MACHINES**. 

2. Nella pagina **ISTANZE MACCHINA VIRTUALE**, nella colonna **Riepilogo rapido**, trovare e copiare il nome DNS per la macchina virtuale.

	![Nome DNS](./media/virtual-machines-sql-server-connection-steps/sql-vm-dns-name.png)
	

### Eseguire la connessione al motore di database da un altro computer
 
1. In un computer connesso a Internet aprire SQL Server Management Studio.
2. Nella finestra di dialogo **Connetti al server** o **Connetti al motore di database**, nella casella **Server name** immettere il nome DNS della macchina virtuale (determinato nell'attività precedente) e un numero di porta di endpoint pubblica nel formato *NomeDNS,Numeroporta*, ad esempio **tutorialtestVM.cloudapp.net,57500**. Per ottenere il numero di porta, accedere al portale di gestione di Azure e trovare la macchina virtuale. Nel dashboard fare clic su **ENDPOINTS** e usare il valore di **PUBLIC PORT** assegnato a **MSSQL**. ![Public Port](./media/virtual-machines-sql-server-connection-steps/sql-vm-port-number.png)
3. Nella casella **Autenticazione** selezionare **Autenticazione di SQL Server**.
5. Nella casella **Account di accesso** digitare il nome di un account di accesso creato in una delle attività precedenti.
6. Nella casella **Password** digitare la password dell'account di accesso creato in una delle attività precedenti.
7. Fare clic su **Connect**.

	![Connessione tramite SQL Server Management Studio](./media/virtual-machines-sql-server-connection-steps/33Connect-SSMS.png)

<!---HONumber=Oct15_HO3-->