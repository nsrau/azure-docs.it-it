### Prerequisiti

- Un account [SFTP](https://en.wikipedia.org/wiki/SSH_File_Transfer_Protocol)


Prima di poter usare l'account SFTP in un'app per la logica, è necessario autorizzare l'app per la logica a connettersi a tale account. Fortunatamente, è possibile eseguire questa operazione in modo facile dalla propria app per la logica nel portale di Azure.

Ecco i passaggi per autorizzare l'app per la logica a connettersi all'account SFTP:
1. Per creare una connessione a SFTP, nella finestra di progettazione dell'app per la logica, selezionare **Show Microsoft managed APIs** (Mostra API gestite da Microsoft) nell'elenco a discesa, quindi immettere *SFTP* nella casella di ricerca. Selezionare il trigger **SFTP - Quando viene aggiunto o modificato un file**: ![Immagine di connessione online a SFTP 1](./media/connectors-create-api-sftp/sftp-1.png)
2. Se non sono mai state create connessioni a SFTP prima d'ora, verrà chiesto di fornire le relative credenziali. Queste credenziali verranno usate per autorizzare l'app per la logica a connettersi e ad accedere ai dati dell'account SFTP: ![Immagine di connessione online a SFTP 2](./media/connectors-create-api-sftp/sftp-2.png)
3. Si noti che la connessione è stata creata ed è ora possibile procedere con gli altri passaggi nell'app per la logica: ![Immagine di connessione online a SFTP 3](./media/connectors-create-api-sftp/sftp-3.png)

<!---HONumber=AcomDC_0727_2016-->