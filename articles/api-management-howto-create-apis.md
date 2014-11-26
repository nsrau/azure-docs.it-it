<properties pageTitle="How to create APIs in Azure API Management" metaKeywords="" description="Learn how to create and configure APIs in Azure API Management." metaCanonical="" services="" documentationCenter="API Management" title="How to create APIs in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie" />

# Come creare API in Gestione API di Azure

Un'API in Gestione API (Anteprima) rappresenta un set di operazioni che possono essere richiamate dalle applicazioni client. Le nuove API vengono create nella console di gestione e quindi vengono aggiunte le operazioni desiderate. Una volta aggiunte le operazioni, l'API viene aggiunta a un prodotto e può essere pubblicata. Dopo la pubblicazione l'API può essere sottoscritta e usata dagli sviluppatori.

Questa guida mostra il primo passaggio del processo, ovvero come creare e configurare una nuova API in Gestione API. Per altre informazioni sull'aggiunta di operazioni e sulla pubblicazione di un prodotto, vedere [Come aggiungere operazioni a un'API][Come aggiungere operazioni a un'API] e [Come creare e pubblicare un prodotto][Come creare e pubblicare un prodotto].

## Contenuto dell'argomento

-   [Creare una nuova API][Creare una nuova API]
-   [Configurare le impostazioni API][Configurare le impostazioni API]
-   [Passaggi successivi][Passaggi successivi]

## <a name="create-new-api"> </a>Creare una nuova API

Per creare e configurare le API, fare clic sulla **Console di gestione** nel portale di Azure per l'istanza del servizio Gestione API. Verrà visualizzato il portale amministrativo di Gestione API.

> Se non è stato creata un'istanza del servizio Gestione API, vedere [Creare un'istanza del servizio Gestione API][Creare un'istanza del servizio Gestione API] nell'esercitazione [Introduzione a Gestione API di Azure][Introduzione a Gestione API di Azure].

![Console di gestione][Console di gestione]

Fare clic su **API** dal menu **Gestione API** sulla sinistra, quindi scegliere **Aggiungi API**.

![Create API][Create API]

Usare la finestra **Aggiungi nuova API** per configurare la nuova API.

![Add new API][Add new API]

Per configurare la nuova API, vengono usati i tre campi seguenti.

-   La casella **Titolo API Web** consente di specificare un nome univoco e descrittivo per l'API, che viene visualizzato nel portale di gestione e nel portale per sviluppatori.
-   Il valore specificato nella casella **URL del servizio Web** fa riferimento al servizio HTTP che implementa l'API e corrisponde all'indirizzo a cui Gestione API inoltra le richieste.
-   Il valore della casella **Suffisso dell'URL dell'API Web** viene aggiunto all'URL di base del servizio Gestione API. L'URL di base è comune a tutte le API ospitate da un'istanza del servizio Gestione API. Gestione API distingue le API in base al suffisso, quindi è necessario che questo sia univoco per ciascuna API di un autore specifico.

Dopo avere configurato i tre valori, fare clic su **Salva**. Una volta creata la nuova API, la pagina di riepilogo dell'API viene visualizzata nel portale di gestione.

![API summary][API summary]

## <a name="configure-api-settings"> </a>Configurare le impostazioni API

È possibile usare la scheda **Impostazioni** per verificare e modificare la configurazione di un'API. **Titolo API Web**, **URL servizio Web** e **Suffisso dell'URL dell'API Web** vengono impostati inizialmente alla creazione dell'API e possono essere modificati in questa scheda. **Descrizione** fornisce una descrizione facoltativa e **Con credenziali** consente di configurare l'autenticazione HTTP di base.

![Impostazioni API][Impostazioni API]

Per configurare l'autenticazione HTTP di base per il servizio Web che implementa l'API, selezionare **Di base** dall'elenco a discesa **Con credenziali** e immettere le credenziali desiderate.

![Impostazioni dell'autenticazione di base][Impostazioni dell'autenticazione di base]

Fare clic su **Salva** per salvare le modifiche apportate alle impostazioni API.

## <a name="next-steps"> </a>Passaggi successivi

Dopo aver creato un'API e configurato le impostazioni, i passaggi successivi consentono di aggiungere le operazioni all'API, aggiungere l'API a un prodotto e pubblicarla in modo che sia disponibile per gli sviluppatori. Per altre informazioni, vedere le seguenti due guide.

-   [Come aggiungere operazioni a un'API][Come aggiungere operazioni a un'API]
-   [Come creare e pubblicare un prodotto][Come creare e pubblicare un prodotto]

  [Come aggiungere operazioni a un'API]: ../api-management-howto-add-operations
  [Come creare e pubblicare un prodotto]: ../api-management-howto-add-products
  [Creare una nuova API]: #create-new-api
  [Configurare le impostazioni API]: #configure-api-settings
  [Passaggi successivi]: #next-steps
  [Creare un'istanza del servizio Gestione API]: ../api-management-get-started/#create-service-instance
  [Introduzione a Gestione API di Azure]: ../api-management-get-started
  [Console di gestione]: ./media/api-management-howto-create-apis/api-management-management-console.png
  [Create API]: ./media/api-management-howto-create-apis/api-management-create-api.png
  [Add new API]: ./media/api-management-howto-create-apis/api-management-add-new-api.png
  [API summary]: ./media/api-management-howto-create-apis/api-management-api-summary.png
  [Impostazioni API]: ./media/api-management-howto-create-apis/api-management-api-settings.png
  [Impostazioni dell'autenticazione di base]: ./media/api-management-howto-create-apis/api-management-api-settings-credentials.png
