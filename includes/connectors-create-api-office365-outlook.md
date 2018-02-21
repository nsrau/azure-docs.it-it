#### <a name="prerequisites"></a>prerequisiti
* Un account Azure, che è possibile [creare gratuitamente](https://azure.microsoft.com/free)
* Un account [Office 365](https://office365.com)  

Prima di usare l'account Office 365 nell'app per la logica, autorizzarne la connessione all'account Office 365. È possibile eseguire questa operazione in modo facile dalla propria app per la logica nel portale di Azure.  

Attenersi alla seguente procedura per autorizzare l'app per la logica a connettersi all'account Office 365:

1. Creare un'app per la logica. Nella finestra di progettazione dell'app per la logica selezionare **Mostra API gestite da Microsoft** nell'elenco a discesa, quindi immettere "office 365" nella casella di ricerca. Selezionare un trigger o un'azione:  
    ![Passaggio di creazione della connessione a Office 365](./media/connectors-create-api-office365-outlook/office365-sendemail.png)  
2. Se non sono mai state create connessioni a Office 365 prima d'ora, verrà chiesto di effettuare l'accesso usando le credenziali di Office 365:  
    ![Passaggio di creazione della connessione a Office 365](./media/connectors-create-api-office365-outlook/office365-signin.png)  
3. Fare clic su **Accedi**, quindi immettere il nome utente e la password. Fare clic su **Accedi**:  
    ![Passaggio di creazione della connessione a Office 365](./media/connectors-create-api-office365-outlook/office365-usernamepassword.png)
   
    Queste credenziali vengono usate per autorizzare l'app per la logica a connettersi e ad accedere all'account Office 365. 
4. La connessione è stata creata. A questo punto, procedere con gli altri passaggi nell'app per la logica:   
    ![Passaggio di creazione della connessione a Office 365](./media/connectors-create-api-office365-outlook/office365-sendemailproperties.png)  

