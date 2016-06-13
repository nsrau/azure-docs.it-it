<properties 
	pageTitle="Creare un account DocumentDB con supporto del protocollo per MongoDB | Microsoft Azure" 
	description="Informazioni su come creare un account DocumentDB con supporto del protocollo per MongoDB, ora disponibile in anteprima." 
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

# Procedura di creazione di un account DocumentDB con supporto del protocollo per MongoDB usando il portale di Azure

Per creare un account DocumentDB con supporto del protocollo per MongoDB è necessario:

- Avere un account Azure. È possibile ottenere un [account Azure gratuito](https://azure.microsoft.com/free/) se non se ne ha già uno.

## Creare l'account  

Per creare un account DocumentDB con supporto del protocollo per MongoDB seguire questa procedura.

1. In una nuova finestra accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **NUOVO**, **Dati e archiviazione**, **Visualizza tutto** e quindi cercare "protocollo DocumentDB" nella categoria **Dati e archiviazione**. Fare clic su **DocumentDB - Supporto del protocollo per MongoDB**.

	![Screenshot dei pannelli di ricerca Marketplace e Dati e archiviazione, con l'opzione DocumentDB - Supporto del protocollo per MongoDB, database Mongo evidenziata](./media/documentdb-create-mongodb-account/marketplacegallery2.png)

3. In alternativa, nella categoria **Dati e archiviazione**, in **Archiviazione**, fare clic su **Tutti**, quindi fare clic su **Carica altro** una o più volte per visualizzare **DocumentDB - Supporto del protocollo per MongoDB**. Fare clic su **DocumentDB - Supporto del protocollo per MongoDB**.

	![Screenshot dei pannelli Marketplace e Dati e archiviazione, con l'opzione DocumentDB - Supporto del protocollo per MongoDB, database Mongo evidenziata](./media/documentdb-create-mongodb-account/marketplacegallery1.png)

4. Nel pannello **DocumentDB - supporto del protocollo per MongoDB (anteprima)**, fare clic su **Crea** per avviare il processo di iscrizione all'anteprima.

	![Pannello DocumentDB - Supporto del protocollo per MongoDB nel portale di Azure](./media/documentdb-create-mongodb-account/marketplacegallery3.png)

5. Nel pannello **Account DocumentDB** fare clic su **Iscrizione all'anteprima**. Leggere le informazioni e quindi fare clic su **OK**.

	![Pannello Iscrizione immediata all'anteprima per DocumentDB - Supporto del protocollo per MongoDB nel portale di Azure](./media/documentdb-create-mongodb-account/registerforpreview.png)

6.  Dopo aver accettato le condizioni per l'anteprima verrà di nuovo visualizzato il pannello Crea. Nel pannello **Account DocumentDB** specificare la configurazione per l'account.

	![Screenshot del pannello Nuovo DocumentDB con supporto del protocollo per MongoDB](./media/documentdb-create-mongodb-account/create-documentdb-mongodb-account.png)


	- Nella casella **ID** immettere un nome per identificare l'account. Quando l’**ID** viene convalidato, appare un segno di spunta verde nella casella **ID**. Il valore **ID** diventa il nome host nell'URI. L'**ID** può contenere solo lettere minuscole, numeri e il carattere '-' e deve avere una lunghezza compresa tra 3 e 50 caratteri. Si noti che al nome endpoint scelto viene aggiunto *documents.azure.com* e il risultato finale sarà l'endpoint dell'account.

	- Per **Sottoscrizione** selezionare la sottoscrizione di Azure da usare per l'account. Se l'account dispone solo una sottoscrizione, tale account viene selezionato per impostazione predefinita.

	- In **Gruppo di risorse** selezionare o creare un gruppo di risorse per l'account. Per impostazione predefinita, verrà scelto un gruppo di risorse esistente nella sottoscrizione di Azure. È tuttavia possibile scegliere di creare un nuovo gruppo di risorse a cui aggiungere l'account. Per altre informazioni, vedere [Uso del portale di Azure per gestire le risorse di Azure](resource-group-portal.md).

	- Usare **Località** per specificare la posizione geografica in cui verrà ospitato l'account.
   
	- Selezionare **Aggiungi al dashboard**

7.	Dopo aver configurato le opzioni del nuovo account, fare clic su **Crea**. La creazione dell'account può richiedere alcuni minuti. Per verificare lo stato, è possibile monitorare l'avanzamento nella Schermata iniziale ![Schermata del riquadro Creazione sulla Schermata iniziale - creazione di database in linea](./media/documentdb-create-mongodb-account/create-nosql-db-databases-json-tutorial-3.png)

	o dall'Hub di notifica.

	![Creare database in modo rapido - Schermata dell'hub Notifiche, che mostra l'account DocumentDB creato](./media/documentdb-create-mongodb-account/create-nosql-db-databases-json-tutorial-4.png)

	![Schermata dell'hub Notifiche, che mostra che l'account DocumentDB è stato creato correttamente e distribuito a un gruppo di risorse - Creatore di database online](./media/documentdb-create-mongodb-account/create-nosql-db-databases-json-tutorial-5.png)

8.	Dopo la creazione, l'account è pronto per l'uso con le impostazioni predefinite.

	![Screenshot del pannello dell'account predefinito](./media/documentdb-create-mongodb-account/defaultaccountblades.png)
	

## Passaggi successivi


- Informazioni su come [connettersi](documentdb-connect-mongodb-account.md) a un account DocumentDB con supporto del protocollo per MongoDB.

 

<!---HONumber=AcomDC_0601_2016-->