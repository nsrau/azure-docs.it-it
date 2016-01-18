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

<!---HONumber=AcomDC_0107_2016-->