---
title: Come creare e pubblicare un prodotto in Gestione API di Azure
description: Informazioni su come creare e pubblicare prodotti in Gestione API di Azure.
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 31de55cb-9384-490b-a2f2-6dfcf83da764
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 2cf905967f26de97613ff7d5fc495c9223e11390
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2017
---
# <a name="how-to-create-and-publish-a-product-in-azure-api-management"></a>Come creare e pubblicare un prodotto in Gestione API di Azure
In Gestione API di Azure un prodotto contiene una o più API, oltre a una quota di utilizzo e alle condizioni per l'utilizzo. Dopo la pubblicazione di un prodotto, gli sviluppatori possono eseguire la sottoscrizione al prodotto e iniziare a usare le API del prodotto. L'argomento include una guida per la creazione di un prodotto, l'aggiunta di un'API e la pubblicazione per gli sviluppatori.

## <a name="create-product"> </a>Creare un prodotto
Le operazioni vengono aggiunte e configurate in un'API nel portale di pubblicazione. Per accedere al portale di pubblicazione, fare clic su **Portale di pubblicazione** nel portale di Azure per il servizio Gestione API.

![Portale di pubblicazione][api-management-management-console]

> Se non è stata creata un'istanza del servizio Gestione API, vedere [Creare un'istanza di Gestione API][Create an API Management service instance] nell'esercitazione [Introduzione a Gestione API di Azure][Get started with Azure API Management].
> 
> 

Fare clic su **Prodotti** nel menu a sinistra per visualizzare la pagina **Prodotti** e fare clic su **Aggiungi prodotto**.

![Prodotti][api-management-products]

![Nuovo prodotto][api-management-add-new-product]

Immettere un nome descrittivo per il prodotto nel campo **Nome** e una descrizione del prodotto nel campo **Descrizione**.

Lo stato dei prodotti in Gestione API può essere **Aperto** o **Protetto**. Per usare i prodotti protetti, è prima di tutto necessaria una sottoscrizione, mentre i prodotti aperti possono essere usati senza sottoscrizione. Per creare un prodotto protetto che richiede una sottoscrizione, selezionare **Richiedi approvazione della sottoscrizione** . Questa è l'impostazione predefinita.

Selezionare **Richiedi approvazione della sottoscrizione** se si preferisce che i tentativi di sottoscrizione al prodotto vengano esaminati e quindi accettati o rifiutati da un amministratore. Se la casella è deselezionata, i tentativi di sottoscrizione verranno approvati automaticamente. Per altre informazioni sulle sottoscrizioni, vedere [Visualizzare i sottoscrittori di un prodotto][View subscribers to a product].

Per consentire agli account per sviluppatore di sottoscrivere più volte il prodotto, selezionare la casella di controllo **Consenti più sottoscrizioni** . Se questa casella non è selezionata, ogni account per sviluppatore può sottoscrivere il prodotto una sola volta.

![Più sottoscrizioni senza limitazioni][api-management-unlimited-multiple-subscriptions]

Per limitare il numero di più sottoscrizioni simultanee, selezionare la casella di controllo **Limita numero di sottoscrizioni simultanee a** e immettere il limite per le sottoscrizioni. Nell'esempio seguente le sottoscrizioni simultanee sono limitate a quattro per ogni account per sviluppatore.

![Quattro sottoscrizioni][api-management-four-multiple-subscriptions]

Dopo aver configurato tutte le opzioni del nuovo prodotto, fare clic su **Salva** per creare il nuovo prodotto.

![Prodotti][api-management-products-page]

> Per impostazione predefinita, i nuovi prodotti non sono pubblicati e sono visibili solo agli utenti nel gruppo **Amministratori**.
> 
> 

Per configurare un prodotto, fare clic sul nome del prodotto nella scheda **Prodotti** .

## <a name="add-apis"> </a>Aggiungere API a un prodotto
La pagina **Prodotti** contiene quattro collegamenti per la configurazione: **Riepilogo**, **Impostazioni**, **Visibilità** e **Sottoscrittori**. La scheda **Riepilogo** consente di aggiungere le API e di pubblicare o annullare la pubblicazione di un prodotto.

![Riepilogo][api-management-new-product-summary]

Prima di pubblicare il prodotto, è necessario aggiungere una o più API. A tale scopo, fare clic su **Aggiungi API al prodotto**.

![Aggiunta di API][api-management-add-apis-to-product]

Selezionare le API desiderate e fare clic su **Salva**.

## <a name="add-description"></a>Aggiungere informazioni descrittive a un prodotto
La scheda **Impostazioni** consente di specificare informazioni dettagliate sul prodotto, ad esempio lo scopo, le API a cui fornisce l'accesso e altre informazioni utili. Il contenuto è indirizzato agli sviluppatori che chiameranno l'API e può essere scritto come testo normale o commenti HTML.

![Impostazioni prodotto][api-management-product-settings]

Selezionare **Richiedi sottoscrizione** per creare un prodotto protetto che richiede l'uso di una sottoscrizione oppure deselezionarla per creare un prodotto aperto che può essere chiamato senza sottoscrizione.

Selezionare **Richiedi approvazione della sottoscrizione** se si preferisce approvare manualmente tutte le richieste di sottoscrizione al prodotto. Per impostazione predefinita, tutte le sottoscrizioni al prodotto vengono concesse automaticamente.

Per consentire agli account per sviluppatore di sottoscrivere più volte il prodotto, selezionare la casella di controllo **Consenti più sottoscrizioni** e, se necessario, specificare un limite. Se questa casella non è selezionata, ogni account per sviluppatore può sottoscrivere il prodotto una sola volta.

Immettere eventualmente nel campo **Condizioni per l'utilizzo** la descrizione delle condizioni per l'utilizzo del prodotto che i sottoscrittori devono accettare per usare il prodotto.

## <a name="publish-product"></a>Pubblicare un prodotto
Per poter chiamare le API in un prodotto, il prodotto deve essere pubblicato. Nella scheda **Riepilogo** del prodotto fare clic su **Pubblica**, quindi su **Sì, pubblica** per confermare. Per impostare come privato un prodotto pubblicato in precedenza, fare clic **Annulla pubblicazione**.

![Pubblicazione prodotto][api-management-publish-product]

## <a name="make-visible"></a>Rendere un prodotto visibile per gli sviluppatori
La scheda **Visibilità** consente di scegliere i ruoli che possono visualizzare il prodotto nel portale per sviluppatori e sottoscrivere il prodotto.

![Visibilità prodotto][api-management-product-visiblity]

Per abilitare o disabilitare la visibilità di un prodotto per gli sviluppatori di un gruppo, selezionare o deselezionare la casella di controllo accanto al gruppo e quindi fare clic su **Salva**.

> Per altre informazioni, vedere [Come creare e usare gruppi per gestire account di sviluppatori in Gestione API di Azure][How to create and use groups to manage developer accounts in Azure API Management].
> 
> 

## <a name="view-subscribers"> </a>Visualizzare i sottoscrittori di un prodotto
Nella scheda **Sottoscrittori** sono elencati gli sviluppatori che hanno sottoscritto il prodotto. Per visualizzare i dettagli e le impostazioni per ogni sviluppatore, fare clic sul nome dello sviluppatore. In questo esempio nessuno sviluppatore ha ancora sottoscritto il prodotto.

![Sviluppatori:][api-management-developer-list]

## <a name="next-steps"></a>Passaggi successivi
Una volta che le API desiderate sono state aggiunte e il prodotto pubblicato, gli sviluppatori possono sottoscrivere il prodotto e iniziare a chiamare le API. Per una dimostrazione di questi elementi e della configurazione avanzata del prodotto, vedere l'esercitazione [Come creare e configurare le impostazioni avanzate del prodotto in Gestione API di Azure][How create and configure advanced product settings in Azure API Management].

Per altre informazioni sull'uso dei prodotti, vedere il video seguente.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Using-Products/player]
> 
> 

[Create a product]: #create-product
[Add APIs to a product]: #add-apis
[Add descriptive information to a product]: #add-description
[Publish a product]: #publish-product
[Make a product visible to developers]: #make-visible
[View subscribers to a product]: #view-subscribers
[Next steps]: #next-steps

[api-management-management-console]: ./media/api-management-howto-add-products/api-management-management-console.png
[api-management-add-product]: ./media/api-management-howto-add-products/api-management-add-product.png
[api-management-add-new-product]: ./media/api-management-howto-add-products/api-management-add-new-product.png
[api-management-unlimited-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-unlimited-multiple-subscriptions.png
[api-management-four-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-four-multiple-subscriptions.png
[api-management-products-page]: ./media/api-management-howto-add-products/api-management-products-page.png
[api-management-new-product-summary]: ./media/api-management-howto-add-products/api-management-new-product-summary.png
[api-management-add-apis-to-product]: ./media/api-management-howto-add-products/api-management-add-apis-to-product.png
[api-management-product-settings]: ./media/api-management-howto-add-products/api-management-product-settings.png
[api-management-publish-product]: ./media/api-management-howto-add-products/api-management-publish-product.png
[api-management-product-visiblity]: ./media/api-management-howto-add-products/api-management-product-visibility.png
[api-management-developer-list]: ./media/api-management-howto-add-products/api-management-developer-list.png



[api-management-products]: ./media/api-management-howto-add-products/api-management-products.png
[api-management-]: ./media/api-management-howto-add-products/
[api-management-]: ./media/api-management-howto-add-products/


[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Next steps]: #next-steps
[How to create and use groups to manage developer accounts in Azure API Management]: api-management-howto-create-groups.md
[How create and configure advanced product settings in Azure API Management]: api-management-howto-product-with-rules.md 
