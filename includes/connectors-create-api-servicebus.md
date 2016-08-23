### Prerequisiti

È necessario avere un account del [bus di servizio](https://azure.microsoft.com/services/service-bus/).

Prima di poter usare l'account del bus di servizio di Azure in un'app per la logica, è necessario autorizzare l'app per la logica a connettersi all'account del bus di servizio. Fortunatamente, è possibile eseguire questa operazione in modo facile dalla propria app per la logica nel portale di Azure.

Ecco i passaggi per autorizzare l'app per la logica a connettersi all'account del bus di servizio:

1. Per creare una connessione al bus di servizio, nella finestra di progettazione dell'app per la logica selezionare **Mostra API gestite da Microsoft** dall'elenco a discesa. Immettere quindi **bus di servizio** nella casella di ricerca. Selezionare il trigger o l'azione da usare. 
![Immagine di connessione al bus di servizio 1](./media/connectors-create-api-servicebus/servicebus-1.png)

2. Se non sono mai state create connessioni al bus di servizio prima d'ora, verrà chiesto di fornire le credenziali del bus di servizio. Queste credenziali vengono usate per autorizzare l'app per la logica a connettersi e ad accedere ai dati dell'account del bus di servizio. Il connettore del bus di servizio richiede la stringa di connessione per lo spazio dei nomi del bus di servizio. Richiede anche le autorizzazioni **Gestisci**. Un buon metodo per sapere se la stringa di connessione è per lo spazio dei nomi o un'entità specifica consiste nel verificare se contiene il parametro `EntityPath`. In tale caso, non si tratta della stringa di connessione corretta per un'app per la logica. 
![Stringa di connessione del bus di servizio](./media/connectors-create-api-servicebus/connectionstring.png)

1. Dopo aver ricevuto la stringa di connessione per lo spazio dei nomi, è possibile usarla per la connessione API nell'app per la logica. 
![Immagine di connessione al bus di servizio 2](./media/connectors-create-api-servicebus/servicebus-2.png)

3. Si noti che la connessione è stata creata ed è ora possibile procedere con gli altri passaggi nell'app per la logica. 
![Immagine di connessione al bus di servizio 3](./media/connectors-create-api-servicebus/servicebus-3.png)

<!---HONumber=AcomDC_0810_2016-->