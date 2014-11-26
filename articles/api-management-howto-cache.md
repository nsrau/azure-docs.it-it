<properties pageTitle="How to cache operation results in Azure API Management" metaKeywords="" description="Learn how to improve the latency, bandwidth consumption, and web service load for API Management service calls." metaCanonical="" services="" documentationCenter="API Management" title="How to cache operation results in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie" />

# Come memorizzare nella cache i risultati dell'operazione in Gestione API di Azure

Le operazioni in Gestione API (Anteprima) possono essere configurate per la memorizzazione nella cache della risposta. La memorizzazione nella cache della risposta può ridurre significativamente la latenza delle API, il consumo di larghezza di banda e il carico del servizio Web per i dati che non vengono modificati di frequente.

In questa esercitazione verranno riviste le impostazioni e i criteri di memorizzazione nella cache per una delle operazioni di esempio di API Echo e l'operazione verrà chiamata nel portale per sviluppatori per vedere il funzionamento della memorizzazione nella cache.

## Contenuto dell'argomento

-   [Configurare un'operazione per la memorizzazione nella cache][Configurare un'operazione per la memorizzazione nella cache]
-   [Rivedere i criteri di memorizzazione nella cache][Rivedere i criteri di memorizzazione nella cache]
-   [Chiamare un'operazione e testare la memorizzazione nella cache][Chiamare un'operazione e testare la memorizzazione nella cache]
-   [Passaggi successivi][Passaggi successivi]

## <a name="configure-caching"> </a>Configurare un'operazione per la memorizzazione nella cache

In questo passaggio viene vengono riviste le impostazioni di memorizzazione nella cache dell'operazione **GET su risorsa (memorizzata nella cache)** dell'API Echo di esempio.

> Ogni istanza del servizio Gestione API è preconfigurata con un'API Echo utilizzabile per sperimentare e ottenere altre informazioni su Gestione API. Per altre informazioni, vedere [Introduzione a Gestione API di Azure][Introduzione a Gestione API di Azure].

Per iniziare, fare clic su **Console di gestione** nel portale di Azure per il servizio Gestione API. Verrà visualizzato il portale amministrativo di Gestione API.

![API Management console][API Management console]

> Se non è stato creata un'istanza del servizio Gestione API, vedere [Creare un'istanza del servizio Gestione API][Creare un'istanza del servizio Gestione API] nell'esercitazione [Introduzione a Gestione API di Azure][Introduzione a Gestione API di Azure].

Fare clic su **API** nel menu **Gestione API** sulla sinistra, quindi scegliere **API Echo**.

![API Echo][API Echo]

Selezionare la scheda **Operazioni**, quindi fare clic sull'operazione **GET su risorsa (memorizzata nella cache)** nell'elenco **Operazioni**.

![Operazioni dell'API Echo][Operazioni dell'API Echo]

Selezionare la scheda **Memorizzazione nella cache** per visualizzare le impostazioni di memorizzazione nella cache per l'operazione.

![Scheda Memorizzazione nella cache][Scheda Memorizzazione nella cache]

Per abilitare la memorizzazione nella cache per un'operazione, selezionare la casella di controllo **Abilita**. In questo esempio, la memorizzazione nella cache è abilitata.

La risposta di ogni operazione è associata a una chiave in base ai valori dei campi **Varia in base ai parametri delle stringhe di query** e **Varia in base alle intestazioni**. Per memorizzare nella cache più risposte in base ai parametri delle stringhe di query o alle intestazioni, è possibile configurarle in questi due campi.

La **durata** specifica l'intervallo di scadenza delle risposte memorizzate nella cache. In questo esempio l'intervallo è di **3600** secondi, equivalente a un'ora.

Nella configurazione di esempio della memorizzazione nella cache, la prima richiesta all'operazione **GET su risorsa (memorizzata nella cache)** restituisce una risposta dal servizio back-end. Questa risposta viene memorizzata nella cache, associata a una chiave mediante le intestazioni e i parametri delle stringhe di query specificati. Le chiamate successive all'operazione, con i parametri corrispondenti, riceveranno la risposta memorizzata nella cache finché non scade l'intervallo di durata della cache.

## <a name="caching-policies"> </a>Rivedere i criteri di memorizzazione nella cache

Quando le impostazioni di memorizzazione nella cache vengono configurate per un'operazione nella scheda **Memorizzazione nella cache**, vengono aggiunti i criteri di memorizzazione nella cache per l'operazione. Questi criteri possono essere visualizzati e modificati nell'editor dei criteri.

Fare clic su **Criteri** nel menu **Gestione API** sulla sinistra e selezionare **API Echo/GET su risorsa (memorizzata nella cache)** dall'elenco a discesa **Operazione**.

![Operazione nell'ambito dei criteri][Operazione nell'ambito dei criteri]

Visualizza i criteri per l'operazione nell'editor dei criteri.

![Editor dei criteri di Gestione API][Editor dei criteri di Gestione API]

La definizione dei criteri per questa operazione include i criteri che definiscono la configurazione della memorizzazione nella cache rivisti usando la scheda **Memorizzazione nella cache** nel passaggio precedente.

    <policies>
        <inbound>
            <base />
            <cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
                <vary-by-header>Accept</vary-by-header>
                <vary-by-header>Accept-Charset</vary-by-header>
            </cache-lookup>
            <rewrite-uri template="/resource" />
        </inbound>
        <outbound>
            <base />
            <cache-store caching-mode="cache-on" duration="3600" />
        </outbound>
    </policies>

> Le modifiche apportate ai criteri di memorizzazione nella cache nell'editor dei criteri si riflettono nella scheda **Memorizzazione nella cache** di un'operazione e viceversa.

## <a name="test-operation"> </a>Chiamare un'operazione e testare la memorizzazione nella cache

Per vedere il funzionamento della memorizzazione nella cache, l'operazione viene chiamata dal portale per sviluppatori. Fare clic su **Portale per sviluppatori** nel menu in alto a destra.

![Portale per sviluppatori][Portale per sviluppatori]

Fare clic su **API** nel menu superiore e scegliere **API Echo**.

![API Echo][1]

> Se è stata configurata una sola API o se ne è visibile solo una per l'account, facendo clic sulle API vengono visualizzate le operazioni per l'API.

Selezionare l'operazione **GET su risorsa (memorizzata nella cache)** e fare clic su **Apri console**.

![Open console][Open console]

La console consente di richiamare le operazioni direttamente dal portale per sviluppatori.

![Console][Console]

Mantenere i valori predefiniti per **param1** e **param2**.

Selezionare la chiave desiderata dall'elenco a discesa **subscription-key**. Se l'account dispone di una sola sottoscrizione, sarà già selezionata automaticamente.

Immettere **sampleheader:value1** nella casella di testo **Intestazioni della richiesta**.

Fare clic su **GET HTTP** e annotare le intestazioni di risposta.

Immettere **sampleheader:value2** nella casella di testo **Intestazioni della richiesta** e fare clic su **GET HTTP**.

Il valore di **sampleheader** nella risposta è ancora **value1**. Provare altri valori diversi e notare che viene restituita la risposta memorizzata nella cache della prima chiamata.

Immettere **25** nel campo **param2** e fare clic su **GET HTTP**.

Il valore di **sampleheader** nella risposta ora è **value2**. I risultati dell'operazione vengono associati a una chiave in base alla stringa di query, quindi non viene restituita la risposta memorizzata nella cache precedente.

## <a name="next-steps"> </a>Passaggi successivi

-   Vedere gli altri argomenti nell'esercitazione [Introduzione alla configurazione API avanzata][Introduzione alla configurazione API avanzata].
-   Per altre informazioni sui criteri di memorizzazione nella cache, vedere [Criteri di memorizzazione nella cache][Criteri di memorizzazione nella cache] nell'argomento di [riferimento ai criteri di Gestione API][riferimento ai criteri di Gestione API].

  [Configurare un'operazione per la memorizzazione nella cache]: #configure-caching
  [Rivedere i criteri di memorizzazione nella cache]: #caching-policies
  [Chiamare un'operazione e testare la memorizzazione nella cache]: #test-operation
  [Passaggi successivi]: #next-steps
  [Introduzione a Gestione API di Azure]: ../api-management-get-started
  [API Management console]: ./media/api-management-howto-cache/api-management-management-console.png
  [Creare un'istanza del servizio Gestione API]: ../api-management-get-started/#create-service-instance
  [API Echo]: ./media/api-management-howto-cache/api-management-echo-api.png
  [Operazioni dell'API Echo]: ./media/api-management-howto-cache/api-management-echo-api-operations.png
  [Scheda Memorizzazione nella cache]: ./media/api-management-howto-cache/api-management-caching-tab.png
  [Operazione nell'ambito dei criteri]: ./media/api-management-howto-cache/api-management-operation-dropdown.png
  [Editor dei criteri di Gestione API]: ./media/api-management-howto-cache/api-management-policy-editor.png
  [Portale per sviluppatori]: ./media/api-management-howto-cache/api-management-developer-portal-menu.png
  [1]: ./media/api-management-howto-cache/api-management-apis-echo-api.png
  [Open console]: ./media/api-management-howto-cache/api-management-open-console.png
  [Console]: ./media/api-management-howto-cache/api-management-console.png
  [Introduzione alla configurazione API avanzata]: ../api-management-get-started-advanced
  [Criteri di memorizzazione nella cache]: ../api-management-policy-reference/#caching-policies
  [riferimento ai criteri di Gestione API]: ../api-management-policy-reference
