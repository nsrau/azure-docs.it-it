<properties 
	pageTitle="Creare un account database NoSQL- Versione di valutazione gratuita | Microsoft Azure" 
	description="Informazioni su come creare account di database tramite il portale del servizio in linea per Azure DocumentDB, un database di documenti NoSQL gestito per JSON. Ottenere una versione di valutazione."
	keywords="versione di valutazione gratuita, servizio di creazione database online, creare database, documentdb, azure, Microsoft azure"
	services="documentdb" 
	documentationCenter="" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="12/17/2015" 
	ms.author="mimig"/>

# Creare un account database DocumentDB usando il portale di Azure

> [AZURE.SELECTOR]
- [Azure Portal](documentdb-create-account.md)
- [Azure CLI and ARM](documentdb-automation-resource-manager-cli.md)

Per usare Microsoft Azure DocumentDB, è necessario creare un account database DocumentDB con il portale di Azure, i modelli di Gestione risorse di Azure o l'interfaccia della riga di comando di Azure. Questo articolo illustra come creare un account database usando il portale di Azure. Per creare un account con Gestione risorse di Azure o l'interfaccia della riga di comando di Azure, vedere [Automatizzare la creazione di un account database DocumentDB](documentdb-automation-resource-manager-cli.md).

Familiarità con DocumentDB? Guardare [questo](https://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/) video di quattro minuti a cura di Scott Hanselman per vedere come completare le attività più comuni nel portale.

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## Passaggi successivi

Ora che avete un account di DocumentDB, il passaggio successivo consiste nel creare un database di DocumentDB. È possibile creare un database utilizzando uno dei seguenti:

- Esempi di C# .NET nel progetto[DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) del repository [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) su GitHub.
- Portale di Azure, come descritto in [Creare un database DocumentDB usando il portale di Azure](documentdb-create-database.md).
- Esercitazioni complete: [.NET](documentdb-get-started.md), [.NET MVC](documentdb-dotnet-application.md), [Java](documentdb-java-application.md), [Node.js](documentdb-nodejs-application.md) o [Python](documentdb-python-application.md).
- [DocumentDB SDK](documentdb-sdk-dotnet.md). DocumentDB dispone di .NET, Java, Python, Node. js e SDK di API JavaScript. 


Dopo avere creato il database, è necessario [aggiungere una o più raccolte](documentdb-create-collection.md) al database e quindi [aggiungere documenti](documentdb-view-json-document-explorer.md) alle raccolte.

Quando sono presenti documenti in una raccolta, è possibile usare [DocumentDB SQL](documentdb-sql-query.md) per [eseguire query](documentdb-sql-query.md#executing-queries) nei documenti tramite [Esplora query](documentdb-query-collections-query-explorer.md) nel portale, l'[API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) o uno degli [SDK](documentdb-sdk-dotnet.md).

Per ulteriori informazioni su DocumentDB, esplorare queste risorse:

-	[Percorso di apprendimento per DocumentDB](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
-	[Modello di risorse e concetti relativi a DocumentDB](documentdb-resources.md)

 

<!---HONumber=AcomDC_0128_2016-->