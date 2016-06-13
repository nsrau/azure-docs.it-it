<properties 
	pageTitle="Usare MongoChef con un account DocumentDB con supporto del protocollo per MongoDB | Microsoft Azure" 
	description="Informazioni su come usare MongoChef con un account DocumentDB con supporto del protocollo per MongoDB, ora disponibile in anteprima." 
	keywords="mongochef"
	services="documentdb" 
	authors="stephbaron" 
	manager="jhubbard" 
	editor="" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/31/2016" 
	ms.author="stbaro"/>

# Usare MongoChef con un account DocumentDB con supporto del protocollo per MongoDB

Per connettersi a un account DocumentDB con il supporto del protocollo per MongoDB usando MongoChef, seguire questa procedura:

- Scaricare e installare [MongoChef](http://3t.io/mongochef)
- Creare l'account DocumentDB con supporto del protocollo per le informazioni sulla [stringa di connessione](documentdb-connect-mongodb-account.md) di MongoDB

## Creare la connessione in MongoChef  

Per aggiungere l'account DocumentDB con supporto del protocollo per MongoDB a gestione connessione di MongoChef, seguire questa procedura.

1. Fare clic su **Connect** per aprire gestione connessione, quindi fare clic su **New Connection**.

	![Screenshot di gestione connessione di MongoChef](./media/documentdb-mongodb-mongochef/ConnectionManager.png)
	
2. Nella finestra **New Connection** immettere nella scheda **Server** il nome di dominio completo dell'account DocumentDB con supporto del protocollo per MongoDB e la porta.
	
	![Screenshot della scheda relativa al server di gestione connessione di MongoChef](./media/documentdb-mongodb-mongochef/ConnectionManagerServerTab.png)

3. Nella finestra **New Connection** scegliere nella scheda **Authentication** la modalità di autenticazione **Standard (MONGODB-CR or SCARM-SHA-1)** e immettere le informazioni relative a nome utente e password. Accettare il database di autenticazione predefinito (admin) o specificare un valore personalizzato.

	![Screenshot della scheda relativa all'autenticazione di gestione connessione di MongoChef](./media/documentdb-mongodb-mongochef/ConnectionManagerAuthenticationTab.png)

4. Nella finestra **New Connection** selezionare nella scheda **SSL** la casella di controllo **Use SSL protocol to connect**, quindi selezionare il pulsante di opzione **Accept self-signed SSL certificates**.

	![Screenshot della scheda relativa a SSL di gestione connessione di MongoChef](./media/documentdb-mongodb-mongochef/ConnectionManagerSSLTab.png)

5. Fare clic sul pulsante **Test Connection** per convalidare le informazioni di connessione, quindi fare clic su **OK** per tornare alla finestra New Connection e infine su **Save**.

	![Screenshot della scheda relativa al test della connessione di MongoChef](./media/documentdb-mongodb-mongochef/TestConnectionResults.png)

## Usare MongoChef per creare un database, una raccolta e documenti  

Per creare un database, una raccolta e documenti usando MongoChef, seguire questa procedura.

1. In **Connection Manager** evidenziare la connessione e fare clic su **Connect**.

	![Screenshot di gestione connessione di MongoChef](./media/documentdb-mongodb-mongochef/ConnectToAccount.png)

2. Fare clic con il pulsante destro del mouse sull'host, quindi scegliere **Add Database**. Specificare un nome di database e fare clic su **OK**.
	
	![Screenshot dell'opzione di aggiunta database di MongoChef](./media/documentdb-mongodb-mongochef/AddDatabase1.png)

3. Fare clic con il pulsante destro del mouse sul database, quindi scegliere **Add Collection**. Specificare un nome di raccolta, quindi fare clic su **Create**.

	![Screenshot dell'opzione di aggiunta raccolta di MongoChef](./media/documentdb-mongodb-mongochef/AddCollection.png)

4. Selezionare la voce di menu **Collection**, quindi fare clic su **Add Document**.

	![Screenshot della voce di menu di aggiunta documento di MongoChef](./media/documentdb-mongodb-mongochef/AddDocument1.png)

5. Nella finestra di dialogo Add Document incollare quanto segue, quindi fare clic su **Add Document**.

		{
    	"_id": "AndersenFamily",
    	"lastName": "Andersen",
    	"parents": [
       		{ "firstName": "Thomas" },
       		{ "firstName": "Mary Kay"}
    	],
    	"children": [
       	{
           	"firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
           	"pets": [{ "givenName": "Fluffy" }]
       	}
    	],
    	"address": { "state": "WA", "county": "King", "city": "seattle" },
    	"isRegistered": true
		}

	
6. Aggiungere un altro documento, questa volta con il contenuto seguente.

		{
	    "_id": "WakefieldFamily",
	    "parents": [
    	    { "familyName": "Wakefield", "givenName": "Robin" },
        	{ "familyName": "Miller", "givenName": "Ben" }
    	],
    	"children": [
	        {
            	"familyName": "Merriam", 
             	"givenName": "Jesse", 
            	"gender": "female", "grade": 1,
            	"pets": [
	                { "givenName": "Goofy" },
                	{ "givenName": "Shadow" }
            	]
        	},
        	{ 
	            "familyName": "Miller", 
             	"givenName": "Lisa", 
             	"gender": "female", 
             	"grade": 8 }
    	],
    	"address": { "state": "NY", "county": "Manhattan", "city": "NY" },
    	"isRegistered": false
		}

7. Eseguire una query di esempio. Cercare ad esempio famiglie con cognome 'Andersen' e restituire i campi relativi a genitori e stato.

	![Screenshot dei risultati della query di MongoChef](./media/documentdb-mongodb-mongochef/QueryDocument1.png)
	

## Passaggi successivi

- Esplorare DocumentDB con supporto del protocollo per trovare [esempi](documentdb-mongodb-samples.md) di MongoDB.
- Vedere le informazioni su DocumentDB con supporto del protocollo per le [linee guida sullo sviluppo in anteprima](documentdb-mongodb-guidelines.md) per MongoDB.

 

<!---HONumber=AcomDC_0601_2016-->