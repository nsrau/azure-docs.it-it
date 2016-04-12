### Creare un endpoint TCP per la macchina virtuale

Per poter accedere a SQL Server da internet, nella macchina virtuale deve essere presente un endpoint per l'ascolto delle comunicazioni TCP in entrata. In questo passaggio di configurazione di Azure, il traffico della porta TCP in ingresso viene indirizzato a una porta TCP accessibile alla macchina virtuale.

>[AZURE.NOTE] Se ci si connette all'interno della stesso servizio cloud o rete virtuale, non è necessario creare un endpoint accessibile pubblicamente. In tal caso, è possibile continuare al passaggio successivo. Per altre informazioni, vedere [Scenari di connessione](../articles/virtual-machines/virtual-machines-windows-classic-sql-connect.md#connection-scenarios).

1. Nel portale di gestione di Azure fare clic su **VIRTUAL MACHINES**.
	
2. Fare clic sulla macchina virtuale appena creata. Verranno visualizzate le informazioni sulla macchina virtuale.
	
3. Nella parte superiore della pagina selezionare **ENDPOINT** e quindi fare clic su **AGGIUNGI** nella parte inferiore della pagina.
	
4. Nella pagina **Aggiungi un endpoint a una macchina virtuale** fare clic su **Aggiungi un endpoint autonomo**, quindi fare clic sulla freccia Avanti per continuare.
	
5. Nella pagina **Specify the details of the endpoint** specificare le informazioni seguenti:

	- Nella casella **NAME** specificare un nome per l'endpoint.
	- Nella casella **PROTOCOL** selezionare **TCP**. È possibile digitare **57500** nella casella **PUBLIC PORT**. Analogamente, è possibile immettere la porta di ascolto predefinita di SQL Server **1433** nella casella **Private Port**. Si noti che molte organizzazioni selezionano numeri di porta diversi per evitare attacchi dannosi al sistema di sicurezza. 

6. Fare clic sul segno di spunta per continuare. La creazione dell'endpoint è completata.

<!---HONumber=AcomDC_0323_2016-->