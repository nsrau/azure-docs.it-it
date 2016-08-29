#### Prerequisiti
- Un account Azure, che è possibile [creare gratuitamente](https://azure.microsoft.com/free)
- Un account [Dynamics CRM Online](https://www.microsoft.com/it-IT/dynamics/crm-free-trial-overview.aspx)

Prima di usare l'account Dynamics nell'app per la logica, autorizzare l'app per la logica a connettersi al proprio account CRM Online. È possibile eseguire questa operazione in modo facile dalla propria app per la logica nel portale di Azure.

Attenersi alla seguente procedura per autorizzare l'app per la logica a connettersi all'account CRM Online:

1. Creare un'app per la logica. Nella finestra di progettazione dell'app per la logica selezionare **Mostra API gestite da Microsoft** nell'elenco a discesa, quindi immettere "dynamics" nella casella di ricerca. Selezionare un trigger o un'azione: ![](./media/connectors-create-api-crmonline/dynamics-triggers.png)
2. Se non sono mai state create connessioni a Dynamics prima d'ora, verrà chiesto di effettuare l'accesso utilizzando le credenziali di Dynamics: ![](./media/connectors-create-api-crmonline/dynamics-signin.png)
3. Fare clic su **Accedi**, quindi immettere il nome utente e la password. Fare clic su **Accedi**.

	Queste credenziali vengono usate per autorizzare l'app per la logica a connettersi e ad accedere ai dati presenti nell'account Dynamics.
4. La connessione è stata creata. A questo punto, procedere con gli altri passaggi nell'app per la logica: ![](./media/connectors-create-api-crmonline/dynamics-properties.png)

<!---HONumber=AcomDC_0817_2016-->