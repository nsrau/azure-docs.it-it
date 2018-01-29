### <a name="open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine"></a>Aprire le porte TCP in Windows Firewall per l'istanza predefinita del motore di database
1. Connettersi alla macchina virtuale tramite Desktop remoto. Per istruzioni dettagliate sulla connessione alla macchina virtuale, vedere [Aprire la macchina virtuale tramite Desktop remoto](../articles/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md#remotedesktop).
2. Una volta effettuato l'accesso, nella schermata Start digitare **WF.msc**e premere INVIO.
   
    ![Avviare Windows Firewall](./media/virtual-machines-sql-server-connection-steps/12Open-WF.png)
3. In **Windows Firewall con protezione avanzata**, nel riquadro sinistro fare clic con il pulsante destro del mouse su **Regole in ingresso** e quindi fare clic su **Nuova regola** nel riquadro Azioni.
   
    ![Nuova regola](./media/virtual-machines-sql-server-connection-steps/13New-FW-Rule.png)
4. Nella finestra di dialogo **Creazione guidata nuova regola connessioni in entrata** nella sezione **Tipo di regola** selezionare **Porta** e quindi fare clic su **Avanti**.
5. Nella finestra di dialogo **Protocollo e porte** usare il valore predefinito per **TCP**. Selezionare la casella **Porte locali specifiche** e quindi digitare il numero di porta dell'istanza del motore di database (**1433** per l'istanza predefinita o il numero di porta specificato per la porta privata nel passaggio di creazione dell'endpoint).
   
    ![Porta TCP 1433](./media/virtual-machines-sql-server-connection-steps/14Port-1433.png)
6. Fare clic su **Avanti**.
7. Nella finestra di dialogo **Azione** selezionare **Consenti la connessione** e quindi fare clic su **Avanti**.
   
    **Nota sulla sicurezza**: per un maggiore livello di sicurezza è possibile selezionare **Consenti solo connessioni protette**. Selezionare questa opzione per configurare altre opzioni di sicurezza per l'ambiente in uso.
   
    ![Consentire le connessioni](./media/virtual-machines-sql-server-connection-steps/15Allow-Connection.png)
8. Nella finestra di dialogo **Profilo** selezionare **Pubblico**, **Privato** e **Dominio**. Quindi fare clic su **Next**.
   
    **Nota sulla sicurezza:** la selezione di un profilo **Pubblico** consente l'accesso tramite Internet. Se possibile, scegliere un profilo più restrittivo.
   
    ![Profilo Pubblico](./media/virtual-machines-sql-server-connection-steps/16Public-Private-Domain-Profile.png)
9. Nella finestra di dialogo **Nome** digitare un nome e una descrizione per la regola e quindi fare clic su **Fine**.
   
    ![Nome regola](./media/virtual-machines-sql-server-connection-steps/17Rule-Name.png)

Aprire altre porte per altri componenti in base alle esigenze. Per ulteriori informazioni, vedere [Configurare Windows Firewall per consentire l'accesso a SQL Server](http://msdn.microsoft.com/library/cc646023.aspx).

### <a name="configure-sql-server-to-listen-on-the-tcp-protocol"></a>Configurare SQL Server per l'ascolto sul protocollo TCP

[!INCLUDE [Enable TCP](virtual-machines-sql-server-connection-tcp-protocol.md)]

### <a name="configure-sql-server-for-mixed-mode-authentication"></a>Configurare SQL Server per l'autenticazione in modalità mista
Il motore di database di SQL Server non può utilizzare l'Autenticazione di Windows senza ambiente di dominio. Per connettersi al motore di database da un altro computer, configurare SQL Server per l'autenticazione in modalità mista. L'autenticazione in modalità mista consente sia l'autenticazione di SQL Server sia l'autenticazione di Windows.

> [!NOTE]
> Se è stata configurata una Rete virtuale di Azure con un ambiente di dominio configurato, potrebbe non essere necessario configurare l'autenticazione in modalità mista.
> 
> 

1. Mentre si è connessi alla macchina virtuale, nella pagina iniziale digitare **SQL Server Management Studio** e fare clic sull'icona selezionata.
   
    Alla prima apertura di Management Studio è necessario creare gli utenti dell'ambiente Management Studio. L'operazione potrebbe richiedere alcuni istanti.
2. Verrà visualizzata la finestra di dialogo **Connetti al server** di Management Studio. Nella casella **Nome server** digitare il nome della macchina virtuale per la connessione al motore di database con Esplora oggetti; al posto del nome della macchina virtuale, per **Nome server** è anche possibile usare **(locale)** o un singolo punto. Selezionare **Autenticazione di Windows** e lasciare ***nome_macchina_virtuale*\amministratore_locale** nella casella **Nome utente**. Fare clic su **Connetti**.
   
    ![Connetti al server](./media/virtual-machines-sql-server-connection-steps/19Connect-to-Server.png)
3. In Esplora oggetti di SQL Server Management Studio fare clic con il pulsante destro del mouse sul nome dell'istanza di SQL Server (nome della macchina virtuale) e quindi scegliere **Proprietà**.
   
    ![Proprietà del server](./media/virtual-machines-sql-server-connection-steps/20Server-Properties.png)
4. Nella pagina **Sicurezza**, in **Autenticazione server** selezionare **Autenticazione di SQL Server e di Windows** e quindi fare clic su **OK**.
   
    ![Selezione della modalità di autenticazione](./media/virtual-machines-sql-server-connection-steps/21Mixed-Mode.png)
5. Nella finestra di dialogo di SQL Server Management Studio fare clic su **OK** per confermare il requisito del riavvio di SQL Server.
6. In Esplora oggetti fare clic con il pulsante destro del mouse sul server e quindi scegliere **Riavvia**. (Se SQL Server Agent è in esecuzione, anch'esso dovrà essere riavviato).
   
    ![Riavvio](./media/virtual-machines-sql-server-connection-steps/22Restart2.png)
7. Nella finestra di dialogo di SQL Server Management Studio fare clic su **Sì** per accettare il riavvio di SQL Server.

### <a name="create-sql-server-authentication-logins"></a>Creare gli account di accesso di SQL Server
Per connettersi al motore di database da un altro computer, configurare almeno un account di accesso con autenticazione di SQL Server.

1. In Esplora oggetti di SQL Server Management Studio espandere la cartella dell'istanza del server in cui si desidera creare il nuovo account di accesso.
2. Fare clic con il pulsante destro del mouse sulla cartella **Sicurezza**, scegliere **Nuovo** e quindi **Account di accesso**.
   
    ![Nuovo account di accesso](./media/virtual-machines-sql-server-connection-steps/23New-Login.png)
3. Nella finestra di dialogo **Account di accesso - Nuovo**, nella pagina **Generale** immettere il nome del nuovo utente nella casella **Nome account di accesso**.
4. Selezionare **Autenticazione di SQL Server**.
5. Nella casella **Password** digitare una password per il nuovo utente. Digitare di nuovo la password nella casella **Conferma password** .
6. Selezionare le opzioni di applicazione della password richieste, ovvero **Applica criteri password**, **Imponi scadenza password** e **Richiedi modifica della password all'accesso successivo**. Se si usa questo account di accesso per se stessi, non è necessario richiedere una modifica della password all'accesso successivo.
7. Nell'elenco **Database predefinito** selezionare un database predefinito per il nome di accesso. **master** è il valore predefinito per questa opzione. Se il database utente non è ancora stato creato, lasciare questa opzione impostata su **master**.
   
    ![Proprietà account di accesso](./media/virtual-machines-sql-server-connection-steps/24Test-Login.png)
8. Se si tratta del primo account di accesso che si crea, è possibile assegnarlo all'amministratore di SQL Server. A questo scopo, nella pagina **Ruoli server** selezionare **sysadmin**.
   
   > [!NOTE]
   > i membri del ruolo predefinito del server dispongono del controllo completo sul motore di database. È consigliabile limitare attentamente le appartenenze a questo ruolo.
   > 
   > 
   
   ![sysadmin](./media/virtual-machines-sql-server-connection-steps/25sysadmin.png)
9. Fare clic su OK.

Per ulteriori informazioni sugli account di accesso di SQL Server, vedere [Creazione di un account di accesso](http://msdn.microsoft.com/library/aa337562.aspx).

