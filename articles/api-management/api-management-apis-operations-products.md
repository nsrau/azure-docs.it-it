<properties 
	pageTitle="Come creare API, operazioni e prodotti in Gestione API di Azure" 
	description="Informazioni su come creare API, operazioni e prodotti in Gestione API." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/03/2015" 
	ms.author="sdanie"/>

# Come creare API, operazioni e prodotti in Gestione API di Azure

In Gestione API di Azure le API e le relative operazioni vengono aggiunte ai prodotti, dove possono essere usate dagli sviluppatori che creano applicazioni che usano tali API. Le guide incluse in questa sezione mostrano come creare un'API, aggiungervi le operazioni e quindi associare l'API a un prodotto e pubblicarlo in modo che possa essere usato dagli sviluppatori.

## <a name="create-apis"> </a>Come creare API

Un'API in Gestione API rappresenta un set di operazioni che possono essere richiamate dalle applicazioni client. Le nuove API vengono create nel portale di pubblicazione.

Questa guida illustra come creare e configurare una nuova API in Gestione API.

-   [Come creare API][]

## <a name="add-operations"> </a>Come aggiungere operazioni a un'API

Prima di poter usare un'API in Gestione API, è necessario aggiungervi le operazioni. Questa guida descrive come aggiungere e configurare diversi tipi di operazioni a un'API in Gestione API.

-   [Come aggiungere operazioni a un'API][]

È anche possibile importare un'API e le relative operazioni in un unico passaggio, in formato WADL o Swagger.

-	[Come importare la definizione di un'API con le operazioni][]

## <a name="add-product"> </a>Come creare e pubblicare un prodotto

In Gestione API un prodotto contiene una o più API, oltre a una quota di utilizzo e le condizioni per l'utilizzo. Dopo la pubblicazione di un prodotto, gli sviluppatori possono eseguire la sottoscrizione al prodotto e iniziare a usare le API del prodotto. Questi argomenti forniscono le istruzioni per creare un prodotto, aggiungere un'API e pubblicarla per gli sviluppatori.

-   [Come aggiungere e pubblicare un prodotto][]
-	[Come creare e configurare le impostazioni avanzate del prodotto][]

[Create a product]: #create-product
[Add APIs to a product]: #add-apis
[Add descriptive information to a product]: #add-description
[Publish a product]: #publish-product
[Make a product visible to developers]: #make-visible
[View subscribers to a product]: #view-subscribers
[Next steps]: #next-steps

[api-management-]: ./media/

[Come creare API]: api-management-howto-create-apis.md
[Come aggiungere operazioni a un'API]: api-management-howto-add-operations.md
[Come aggiungere e pubblicare un prodotto]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Come importare la definizione di un'API con le operazioni]: api-management-howto-import-api.md
[Come creare e configurare le impostazioni avanzate del prodotto]: api-management-howto-product-with-rules.md

<!---HONumber=AcomDC_1210_2015-->