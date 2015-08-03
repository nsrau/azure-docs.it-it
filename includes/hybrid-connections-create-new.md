
1. Nel computer locale accedere al [portale di gestione di Azure](http://manager.windowsazure.com) (il portale precedente).

2. Nella parte inferiore del pannello di navigazione selezionare **+NUOVO** > **Servizi app** > **Servizio BizTalk** > **Creazione personalizzata**

3. Inserire il **Nome servizio BizTalk** e selezionare un'**Edizione**.

	In questa esercitazione viene usato **mobile1**. Sarà necessario fornire un nome univoco per il nuovo servizio BizTalk.

4. Dopo aver creato il servizio BizTalk, selezionare la scheda **Connessioni ibride** e fare clic su **Aggiungi**.

	![Aggiunta della connessione ibrida](./media/hybrid-connections-create-new/3.png)

	Verrà creata una nuova connessione ibrida.

5. Inserire un **Nome** e un **Nome host** per la connessione ibrida e impostare la **Porta** su `1433`.
  
	![Configurazione della connessione ibrida](./media/hybrid-connections-create-new/4.png)

	Il nome host è il nome del server locale. In questo modo la connessione ibrida viene configurata in modo da accedere all'istanza di SQL Server in esecuzione sulla porta 1433. Se si usa un'istanza denominata di SQL Server, usare invece la porta statica definita in precedenza.

6. Dopo aver creato la nuova connessione, il relativo stato sarà **Installazione locale non completata**.

7. Tornare al servizio mobile, fare clic su **Configura**, scorrere verso il basso fino a **Connessioni ibride** e fare clic su **Aggiungi connessione ibrida**, quindi selezionare la connessione ibrida appena creata e fare clic su **OK**.

    In questo modo il servizio mobile è abilitato all'uso della nuova connessione ibrida.

A questo punto è necessario installare Gestione connessione ibrida nel computer locale.

<!---HONumber=July15_HO4-->