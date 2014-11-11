<properties pageTitle="How create and publish a product in Azure API Management" metaKeywords="" description="Learn how to create and publish products in Azure API Management." metaCanonical="" services="" documentationCenter="API Management" title="How create and publish a product in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie" />

# Come creare e pubblicare un prodotto in Gestione API di Azure

In Gestione API di Azure (anteprima) un prodotto contiene una o più API, oltre a una quota di utilizzo e le condizioni per l'utilizzo. Dopo la pubblicazione di un prodotto, gli sviluppatori possono eseguire la sottoscrizione al prodotto e iniziare a usare le API del prodotto. L'argomento include una guida per la creazione di un prodotto, l'aggiunta di un'API e la pubblicazione per gli sviluppatori.

## Contenuto dell'argomento

-   [Creare un prodotto][Creare un prodotto]
-   [Aggiungere API a un prodotto][Aggiungere API a un prodotto]
-   [Aggiungere informazioni descrittive a un prodotto][Aggiungere informazioni descrittive a un prodotto]
-   [Pubblicare un prodotto][Pubblicare un prodotto]
-   [Rendere un prodotto visibile per gli sviluppatori][Rendere un prodotto visibile per gli sviluppatori]
-   [Visualizzare i sottoscrittori di un prodotto][Visualizzare i sottoscrittori di un prodotto]
-   [Passaggi successivi][Passaggi successivi]

## <a name="create-product"> </a>Creare un prodotto

Le operazioni vengono aggiunte e configurate in un'API nella console di gestione. Per accedere alla console di Gestione, fare clic su **Console di gestione** nel portale di Azure per il servizio Gestione API.

> Se non è stato creata un'istanza del servizio Gestione API, vedere [Creare un'istanza del servizio Gestione API][Creare un'istanza del servizio Gestione API] nell'esercitazione [Introduzione a Gestione API di Azure][Introduzione a Gestione API di Azure].

![API Management console][API Management console]

Fare clic su **Prodotti** nel menu sulla sinistra per visualizzare la pagina **Prodotti** e fare clic su **Aggiungi prodotto**.

![Prodotti][Prodotti]

![New product][New product]

Immettere un nome descrittivo per il prodotto nel campo **Nome** e una descrizione del prodotto nel campo **Descrizione**.

Selezionare **Richiedi approvazione della sottoscrizione** se si preferisce che i tentativi di sottoscrizione al prodotto vengano esaminati e quindi accettati o rifiutati da un amministratore. Se la casella è deselezionata, i tentativi di sottoscrizione verranno approvati automaticamente. Per altre informazioni sulle sottoscrizioni, vedere [Visualizzare i sottoscrittori di un prodotto][Visualizzare i sottoscrittori di un prodotto].

![Prodotti][1]

> Per impostazione predefinita, i nuovi prodotti non sono pubblicati e sono visibili solo agli utenti nel gruppo **Amministratori**.

Per configurare un prodotto, fare clic sul nome del prodotto nella scheda **Prodotti**.

## <a name="add-apis"> </a>Aggiungere API a un prodotto

La pagina **Prodotti** contiene quattro collegamenti per la configurazione: **Riepilogo**, **Impostazioni**, **Visibilità** e **Sviluppatori**. La scheda **Riepilogo** consente di aggiungere le API e di pubblicare o annullare la pubblicazione di un prodotto.

![Riepilogo][Riepilogo]

Prima di pubblicare il prodotto, è necessario aggiungere una o più API. A tale scopo, fare clic su **Aggiungi API al prodotto**.

![Add APIs][Add APIs]

Selezionare le API desiderate e fare clic su **Salva**.

## <a name="add-description"> </a>Aggiungere informazioni descrittive a un prodotto

La scheda **Impostazioni** consente di specificare informazioni dettagliate sul prodotto, ad esempio lo scopo, le API a cui fornisce l'accesso e altre informazioni utili. Il contenuto è indirizzato agli sviluppatori che chiameranno l'API e può essere scritto come testo normale o commenti HTML.

![Product settings][Product settings]

Selezionare **Richiedi approvazione della sottoscrizione** se si preferisce approvare manualmente tutte le richieste di sottoscrizione al prodotto. Per impostazione predefinita, tutte le sottoscrizioni al prodotto vengono concesse automaticamente.

Immettere eventualmente nel campo **Condizioni per l'utilizzo** la descrizione delle condizioni per l'utilizzo del prodotto che i sottoscrittori devono accettare per usare il prodotto.

## <a name="publish-product"> </a>Pubblicare un prodotto

Per poter chiamare le API in un prodotto, il prodotto deve essere pubblicato. Nella scheda **Riepilogo** del prodotto fare clic su **Pubblica**, quindi su **Sì, pubblica** per confermare. Per impostare come privato un prodotto pubblicato in precedenza, fare clic **Annulla pubblicazione**.

![Publish product][Publish product]

## <a name="make-visible"> </a>Rendere un prodotto visibile per gli sviluppatori

La scheda **Visibilità** consente di scegliere i ruoli che possono visualizzare il prodotto nel portale per sviluppatori e sottoscrivere il prodotto.

![Product visibility][Product visibility]

Per abilitare o disabilitare la visibilità di un prodotto per gli sviluppatori di un gruppo, selezionare o deselezionare la casella di controllo accanto al gruppo e quindi fare clic su **Salva**.

> Per altre informazioni, vedere [Come creare e usare i gruppi per gestire gli account sviluppatore in Gestione API di Azure][Come creare e usare i gruppi per gestire gli account sviluppatore in Gestione API di Azure].

## <a name="view-subscribers"> </a>Visualizzare i sottoscrittori di un prodotto

Nella scheda **Sviluppatori** sono elencati gli sviluppatori che hanno sottoscritto il prodotto. Per visualizzare i dettagli e le impostazioni per ogni sviluppatore, fare clic sul nome dello sviluppatore. In questo esempio nessuno sviluppatore ha ancora sottoscritto il prodotto.

![Sviluppatori][Sviluppatori]

## <a name="next-steps"> </a>Passaggi successivi

Una volta che le API desiderate sono state aggiunte e il prodotto pubblicato, gli sviluppatori possono sottoscrivere il prodotto e iniziare a chiamare le API. Per una dimostrazione di questi elementi e della configurazione avanzata del prodotto, vedere l'esercitazione [Come creare e configurare le impostazioni avanzate del prodotto in Gestione API di Azure][Come creare e configurare le impostazioni avanzate del prodotto in Gestione API di Azure].

  [Creare un prodotto]: #create-product
  [Aggiungere API a un prodotto]: #add-apis
  [Aggiungere informazioni descrittive a un prodotto]: #add-description
  [Pubblicare un prodotto]: #publish-product
  [Rendere un prodotto visibile per gli sviluppatori]: #make-visible
  [Visualizzare i sottoscrittori di un prodotto]: #view-subscribers
  [Passaggi successivi]: #next-steps
  [Creare un'istanza del servizio Gestione API]: ../api-management-get-started/#create-service-instance
  [Introduzione a Gestione API di Azure]: ../api-management-get-started
  [API Management console]: ./media/api-management-howto-add-products/api-management-management-console.png
  [Prodotti]: ./media/api-management-howto-add-products/api-management-products.png
  [New product]: ./media/api-management-howto-add-products/api-management-add-new-product.png
  [1]: ./media/api-management-howto-add-products/api-management-products-page.png
  [Riepilogo]: ./media/api-management-howto-add-products/api-management-new-product-summary.png
  [Add APIs]: ./media/api-management-howto-add-products/api-management-add-apis-to-product.png
  [Product settings]: ./media/api-management-howto-add-products/api-management-product-settings.png
  [Publish product]: ./media/api-management-howto-add-products/api-management-publish-product.png
  [Product visibility]: ./media/api-management-howto-add-products/api-management-product-visibility.png
  [Come creare e usare i gruppi per gestire gli account sviluppatore in Gestione API di Azure]: ../api-management-howto-create-groups
  [Sviluppatori]: ./media/api-management-howto-add-products/api-management-developer-list.png
  [Come creare e configurare le impostazioni avanzate del prodotto in Gestione API di Azure]: ../api-management-howto-product-with-rules
