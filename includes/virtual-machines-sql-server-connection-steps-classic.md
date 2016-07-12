### Determinare il nome DNS della macchina virtuale

Per connettersi al motore di database di SQL Server da un altro computer, è necessario conoscere il nome DNS (Domain Name System) della macchina virtuale. (Si tratta del nome utilizzato da Internet per identificare la macchina virtuale. È possibile utilizzare l'indirizzo IP, ma questo indirizzo può cambiare se Azure sposta le risorse per la ridondanza o la manutenzione. Il nome DNS rimane stabile in quanto può essere reindirizzato a un nuovo indirizzo IP).

1. Nel portale di Azure (o dal passaggio precedente) selezionare **Macchine virtuali (versione classica)**.

2. Selezionare la macchina virtuale di SQL.

2. Nel pannello **Macchina virtuale**, copiare il **nome DNS** per la macchina virtuale.

	![Nome DNS](./media/virtual-machines-sql-server-connection-steps/sql-vm-dns-name.png)


### Eseguire la connessione al motore di database da un altro computer

1. In un computer connesso a Internet aprire SQL Server Management Studio.

2. Nella finestra di dialogo **Connetti al server** o **Connetti al motore di database**, nella casella **Nome server** immettere il nome DNS della macchina virtuale (determinato nell'attività precedente) e un numero di porta di endpoint pubblica nel formato *NomeDNS,Numero porta*, ad esempio **mysqlvm.cloudapp.net,57500**.

	![Connessione tramite SQL Server Management Studio](./media/virtual-machines-sql-server-connection-steps/33Connect-SSMS.png)

	Se non si ricorda il numero di porta di endpoint pubblica creata in precedenza, è possibile trovarlo nell'area **Endpoint** del pannello **Macchina virtuale**.

	![Public Port](./media/virtual-machines-sql-server-connection-steps/sql-vm-port-number.png)

3. Nella casella **Autenticazione** selezionare **Autenticazione di SQL Server**.

5. Nella casella **Account di accesso** digitare il nome di un account di accesso creato in una delle attività precedenti.

6. Nella casella **Password** digitare la password dell'account di accesso creato in una delle attività precedenti.

7. Fare clic su **Connect**.

<!---HONumber=AcomDC_0629_2016-->