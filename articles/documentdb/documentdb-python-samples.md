<properties 
	pageTitle="Esempi di Python NoSQL per DocumentDB | Microsoft Azure" 
	description="Trovare esempi di Python NoSQL su GitHub per attività comuni in DocumentDB, incluse operazioni CRUD per documenti JSON in database NoSQL." 
	keywords="esempi di Python"
	services="documentdb" 
	authors="aliuy" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter="python"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/18/2016" 
	ms.author="andrl"/>


# Esempi di Python di DocumentDB

> [AZURE.SELECTOR]
- [Esempi di .NET](documentdb-dotnet-samples.md)
- [Esempi di Node.js](documentdb-nodejs-samples.md)
- [Esempi di Python](documentdb-python-samples.md)
- [Raccolta di esempi di codice di Azure](https://azure.microsoft.com/documentation/samples/?service=documentdb)

Il repository [azure-documentdb-python](https://github.com/Azure/azure-documentdb-python/tree/master/samples) di GitHub include soluzioni di esempio che eseguono operazioni CRUD e altre operazioni comuni su risorse di DocumentDB. Questo articolo include:

- Collegamenti alle attività in ogni file di progetto Python di esempio. 
- Collegamenti al contenuto di riferimento sulle API correlato.

**Prerequisiti**

1. Per usare questi esempi Python, è necessario un account Azure:
    - È possibile [aprire un account Azure gratuitamente](https://azure.microsoft.com/pricing/free-trial/): si riceveranno dei crediti da usare per provare i servizi di Azure a pagamento e, anche dopo avere esaurito i crediti, è possibile mantenere l'account per usare i servizi di Azure gratuiti, ad esempio Siti Web. La carta di credito non verrà mai addebitata, a meno l'utente non modifichi le impostazioni e che richieda esplicitamente di essere addebitato.
   - È possibile [attivare i benefici della sottoscrizione Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/): con la sottoscrizione Visual Studio ogni mese si accumulano crediti che è possibile usare per i servizi di Azure a pagamento.
2. È anche necessario [Python SDK](documentdb-sdk-python.md). 

    > [AZURE.NOTE] Ogni esempio è indipendente e le operazioni di installazione e pulizia sono eseguite automaticamente. Di conseguenza, gli esempi effettuano più chiamate a [document\_client.CreateCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html). A ogni chiamata, viene addebitata alla sottoscrizione 1 ora di utilizzo per ogni livello di prestazioni della raccolta in fase di creazione.

## Esempi di database

Il file [Program.py](https://github.com/Azure/azure-documentdb-python/tree/master/samples/DatabaseManagement/Program.py) del progetto [DatabaseManagement](https://github.com/Azure/azure-documentdb-python/tree/master/samples/DatabaseManagement) illustra come eseguire le attività seguenti.

Attività | Informazioni di riferimento sulle API
--- | ---
[Creare un database](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L65-L76) | [document\_client. CreateDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)
[Eseguire query in un account per un database](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L49-L62) | [document\_client.QueryDatabases](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)
[Leggere un database in base all'ID](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L79-L96) | [document\_client.ReadDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)
[Elencare database per un account](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L99-L110) | [document\_client.ReadDatabases](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)
[Eliminare un database](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L113-L126) | [document\_client.DeleteDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)

## Esempi di raccolta 

Il file [Program.py](https://github.com/Azure/azure-documentdb-python/tree/master/samples/CollectionManagement/Program.py) del progetto [CollectionManagement](https://github.com/Azure/azure-documentdb-python/tree/master/samples/CollectionManagement) illustra come eseguire le attività seguenti.

Attività | Informazioni di riferimento sulle API
--- | ---
[Creare una raccolta](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L84-L135) | [document\_client.CreateCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[Leggere un elenco di raccolte in un database](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L198-L225) | [document\_client.ListCollections](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[Ottenere una raccolta in base all'ID](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L178-L195) | [document\_client.ReadCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[Ottenere il livello di prestazioni di una raccolta](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L139-L161) | [DocumentQueryable.QueryOffers](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[Cambiare il livello di prestazioni di una raccolta](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L163-L175) | [document\_client.ReplaceOffer](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[Eliminare una raccolta](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L212-L225) | [document\_client.DeleteCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)

<!---HONumber=AcomDC_0420_2016-->