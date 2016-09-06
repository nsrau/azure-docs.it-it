<properties
	pageTitle="Come creare un account DocumentDB | Microsoft Azure"
	description="Compilare un database NoSQL con Azure DocumentDB. Seguire queste istruzioni per creare un account DocumentDB e iniziare a compilare il database NoSQL estremamente veloce su scala mondiale." 
	keywords="Compilare un database"
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
	ms.date="08/25/2016"
	ms.author="mimig"/> 

# Come creare un account DocumentDB usando il portale di Azure

> [AZURE.SELECTOR]
- [Portale di Azure](documentdb-create-account.md)
- [Interfaccia della riga di comando di Azure e Azure Resource Manager](documentdb-automation-resource-manager-cli.md)

Per compilare un database con Microsoft Azure DocumentDB, è necessario:

- Avere un account Azure. È possibile ottenere un [account Azure gratuito](https://azure.microsoft.com/free) se non se ne ha già uno.
- Creare un account DocumentDB.

È possibile creare un account DocumentDB usando il portale di Azure, i modelli di Azure Resource Manager o l'interfaccia della riga di comando di Azure. Questo articolo illustra come creare un account DocumentDB usando il portale di Azure. Per creare un account con Gestione risorse di Azure o l'interfaccia della riga di comando di Azure, vedere [Automatizzare la creazione di un account database DocumentDB](documentdb-automation-resource-manager-cli.md).

Familiarità con DocumentDB? Guardare [questo](https://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/) video di quattro minuti a cura di Scott Hanselman per vedere come completare le attività più comuni nel portale.

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## Passaggi successivi

Ora che è disponibile un account DocumentDB, il passaggio successivo consiste nel compilare il database DocumentDB. È possibile creare un database utilizzando uno dei seguenti:

- Portale di Azure, come descritto in [Creare un database DocumentDB usando il portale di Azure](documentdb-create-database.md).
- Esempi di C# .NET nel progetto[DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) del repository [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) su GitHub.
- Esercitazioni complete: [.NET](documentdb-get-started.md), [.NET MVC](documentdb-dotnet-application.md), [Java](documentdb-java-application.md), [Node.js](documentdb-nodejs-application.md) o [Python](documentdb-python-application.md).
- [DocumentDB SDK](documentdb-sdk-dotnet.md). DocumentDB dispone di .NET, Java, Python, Node. js e SDK di API JavaScript.


Dopo avere creato il database, è necessario [aggiungere una o più raccolte](documentdb-create-collection.md) al database e quindi [aggiungere documenti](documentdb-view-json-document-explorer.md) alle raccolte.

Quando sono presenti documenti in una raccolta, è possibile usare [DocumentDB SQL](documentdb-sql-query.md) per [eseguire query](documentdb-sql-query.md#executing-queries) nei documenti con [Esplora query](documentdb-query-collections-query-explorer.md) nel portale, l'[API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) o uno degli [SDK](documentdb-sdk-dotnet.md).

Per ulteriori informazioni su DocumentDB, esplorare queste risorse:

-	[Percorso di apprendimento per DocumentDB](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
-	[Modello di risorse e concetti relativi a DocumentDB](documentdb-resources.md)

<!---HONumber=AcomDC_0831_2016-->