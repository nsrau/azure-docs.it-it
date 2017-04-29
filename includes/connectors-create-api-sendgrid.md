### <a name="prerequisites"></a>Prerequisiti
* Un account [SendGrid](https://www.SendGrid.com/) 

Prima di poter usare l'account SendGrid in un'app per la logica, è necessario autorizzare l'app per la logica a connettersi all'account SendGrid. Fortunatamente, è possibile eseguire questa operazione in modo facile dalla propria app per la logica nel portale di Azure. 

Ecco i passaggi per autorizzare l'app per la logica a connettersi all'account SendGrid:

1. Per creare una connessione a SendGrid, nella finestra di progettazione dell'app per la logica selezionare **Mostra API gestite da Microsoft** nell'elenco a discesa, quindi immettere *SendGrid* nella casella di ricerca. Selezionare il trigger o l'azione che si desidera usare:   
   ![Passaggio 1 di SendGrid](./media/connectors-create-api-sendgrid/sendgrid-1.png)
2. Se non sono mai state create connessioni a SendGrid prima d'ora, verrà chiesto di fornire le credenziali di SendGrid. Queste credenziali verranno usate per autorizzare l'app per la logica a connettersi e ad accedere ai dati dell'account SendGrid:  
   ![Passaggio 2 SendGrid](./media/connectors-create-api-sendgrid/sendgrid-2.png)
3. Si noti che la connessione è stata creata ed è ora possibile procedere con gli altri passaggi nell'app per la logica:   
   ![Passaggio 3 SendGrid](./media/connectors-create-api-sendgrid/sendgrid-3.png)   

