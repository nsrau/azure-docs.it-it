---
title: Importare un'API in Gestione API di Azure | Documentazione Microsoft
description: Informazioni su come importare un'API e le relative operazioni in Gestione API di Azure.
services: api-management
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: 40398b0a-ac2c-43f0-89e1-07e4abbf502f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: cc56c9a91979ec2ec06b2d63a22b2ded68c0dce1
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2017
---
# <a name="how-to-import-the-definition-of-an-api-with-operations-in-azure-api-management"></a>Come importare la definizione di un'API con le operazioni in Gestione API di Azure
In Gestione API è possibile creare nuove API e aggiungere manualmente le operazioni oppure è possibile importare l'API insieme alle operazioni in un unico passaggio.

Le API e le relative operazioni possono essere importate usando i seguenti formati.

* WADL
* Swagger

In questa guida viene illustrato come creare una nuova API e importarne le operazioni in un unico passaggio. Per informazioni su come creare un'API e aggiungere le operazioni, vedere [Come creare le API][How to create APIs] e [Come aggiungere operazioni a un'API][How to add operations to an API].

## <a name="import-api"></a>Importare un'API
Le API vengono create e configurate nel portale di pubblicazione. Per accedere al portale di pubblicazione, fare clic su **Portale di pubblicazione** nel portale di Azure per il servizio Gestione API. Se non è stata creata un'istanza del servizio Gestione API, vedere [Creare un'istanza di Gestione API][Create an API Management service instance] nell'esercitazione [Introduzione a Gestione API di Azure][Get started with Azure API Management].

![Portale di pubblicazione][api-management-management-console]

Fare clic su **APi** dal menu **Gestione API** sulla sinistra, quindi scegliere **Importa API**.

![Importa API][api-management-import-apis]

La finestra **Importa API** contiene tre schede che corrispondono alle tre modalità con cui è possibile fornire la specifica API.

* **Da Appunti** consente di incollare la specifica API nella casella di testo indicata.
* **Da file** consente di sfogliare e selezionare il file che contiene la specifica API.
* **Da URL** consente di fornire l'URL alla specifica per l'API.

![Formato di importazione API][api-management-import-api-clipboard]

Dopo aver fornito la specifica API, usare i pulsanti di opzione a destra per indicare il formato della specifica. Sono supportati i seguenti formati.

* WADL
* Swagger

Quindi, immettere un **Suffisso dell'URL dell'API Web**. Il suffisso viene aggiunto all'URL di base del servizio Gestione API. L'URL di base è comune a tutte le API ospitate in un'istanza di un servizio Gestione API. Gestione API distingue le API in base al suffisso, quindi è necessario che questo sia univoco per ciascuna API in ciascuna istanza del servizio Gestione API.

Dopo aver immesso tutti i valori, fare clic su **Salva** per creare l'API e le operazioni associate. 

> [!NOTE]
> Per un'esercitazione relativa all'importazione di un'API di calcolatrice di base in formato Swagger, vedere [Gestire la prima API in Gestione API di Azure](api-management-get-started.md).
> 
> 

## <a name="export-api"></a> Esportare un'API
Oltre a importare nuove API, è possibile esportare le definizioni delle API dal portale di pubblicazione. Per farlo, fare clic su **Esporta API** dalla scheda **Riepilogo** dell'**API**.

![Esporta API][api-management-export-api]

Le API possono essere esportate con WADL o Swagger. Selezionare il formato desiderato, fare clic su **Salva**e scegliere il percorso in cui salvare il file.

![Formato di esportazione API][api-management-export-api-format]

## <a name="next-steps"></a>Passaggi successivi
Dopo aver creato un'API ed importato le operazioni, è possibile rivedere e configurare tutte le impostazioni aggiuntive, aggiungere l'API a un prodotto e pubblicarla in modo che sia disponibile per gli sviluppatori. Per altre informazioni, vedere le seguenti guide.

* [Come configurare le impostazioni API][How to configure API settings]
* [Come creare e pubblicare un prodotto][How to create and publish a product]

[api-management-management-console]: ./media/api-management-howto-import-api/api-management-management-console.png
[api-management-import-apis]: ./media/api-management-howto-import-api/api-management-api-import-apis.png
[api-management-import-api-clipboard]: ./media/api-management-howto-import-api/api-management-import-api-wizard.png
[api-management-export-api]: ./media/api-management-howto-import-api/api-management-export-api.png
[api-management-export-api-format]: ./media/api-management-howto-import-api/api-management-export-api-format.png

[Import an API]: #import-api
[Export an API]: #export-api
[Configure API settings]: #configure-api-settings
[Next steps]: #next-steps

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[How to create APIs]: api-management-howto-create-apis.md
[How to configure API settings]: api-management-howto-create-apis.md#configure-api-settings
