---
title: 'Esercitazione: Personalizzazione degli articoli - Servizio decisionale personalizzato'
titlesuffix: Azure Cognitive Services
description: Esercitazione per la personalizzazione degli articoli per i processi decisionali contestuali.
services: cognitive-services
author: slivkins
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-decision-service
ms.topic: tutorial
ms.date: 05/08/2018
ms.author: slivkins
ms.openlocfilehash: b142fe2051c017d0c0ec3c4cac6aaedd563f6cd7
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2018
ms.locfileid: "46366336"
---
# <a name="tutorial-article-personalization-for-contextual-decision-making"></a>Esercitazione: Personalizzazione degli articoli per i processi decisionali contestuali

Questa esercitazione si concentra sulla personalizzazione della selezione di articoli nella pagina principale di un sito Web. Il Servizio decisionale personalizzato influisce, ad esempio, su *più* elenchi di articoli nella pagina iniziale. Si supponga che la pagina sia un sito Web di notizie che tratta solo di politica e sport. Mostrerebbe tre elenchi di articoli classificati: politica, sport e recenti.

## <a name="applications-and-action-sets"></a>Applicazioni e set di azioni

Di seguito si descrive come adattare lo scenario al framework. Immaginare tre applicazioni, una per ogni elenco che viene ottimizzato: app-politics, app-sports e app-recent. Per specificare gli articoli candidati per ogni applicazione esistono due set di azioni: uno per la politica e uno per lo sport. Il set di azioni per app-recent è automaticamente l'unione degli altri due set.

> [!TIP]
> I set di azioni possono essere condivisi tra le applicazioni nel Servizio decisionale personalizzato.

## <a name="prepare-action-set-feeds"></a>Preparare i feed del set di azioni

Il Servizio decisionale personalizzato usa i set di azioni mediante feed RSS o Atom forniti dal cliente. Si forniscono due feed: uno per la politica e uno per lo sport. Si supponga che siano resi disponibili da `http://www.domain.com/feeds/<feed-name>`.

Ogni feed fornisce un elenco di articoli. In RSS ciascuno è specificato da un elemento `<item>`, come indicato di seguito:

```xml
<rss version="2.0"><channel>
   <item>
      <title><![CDATA[article title]]></title>
      <link>"article url"</link>
      <pubDate>publication date</pubDate>
    </item>
</channel></rss>
```

L'ordine degli articoli è importante. Specifica la classificazione predefinita, ovvero l'ordinamento più probabile degli articoli. La classificazione predefinita è usata quindi per il confronto delle prestazioni nel [dashboard](#performance-dashboard).

Per altre informazioni sul formato di feed, vedere le [informazioni di riferimento per l'API](custom-decision-service-api-reference.md#action-set-api-customer-provided).

## <a name="register-a-new-app"></a>Registrare una nuova app

1. Accedere con il proprio [account Microsoft](https://account.microsoft.com/account). Sulla barra multifunzione fare clic su **My Portal** (Il mio portale).

2. Per registrare una nuova applicazione fare clic sul pulsante **Nuova app**.

    ![Portale del Servizio decisionale personalizzato](./media/custom-decision-service-tutorial/portal.png)

3. Immettere un nome univoco per l'applicazione nella casella di testo **ID app**. Se il nome è già utilizzato da un altro cliente, viene chiesto all'utente di scegliere un ID app diverso. Selezionare la casella di controllo **Avanzate** e immettere la [stringa di connessione](../../storage/common/storage-configure-connection-string.md) per l'account di archiviazione di Azure. In genere si usa lo stesso account di archiviazione per tutte le applicazioni.

    ![Finestra di dialogo Nuova app](./media/custom-decision-service-tutorial/new-app-dialog.png)

    Dopo aver registrato tutte e tre le applicazioni dello scenario precedente, esse compaiono nell'elenco:

    ![Elenco di app](./media/custom-decision-service-tutorial/apps.png)

    È possibile tornare a questo elenco facendo clic sul pulsante **App**.

4. Nella finestra di dialogo **Nuova app** specificare un feed di azioni. I feed di azioni possono essere specificati anche facendo clic sul pulsante **Feed** e quindi selezionando il pulsante **Nuovo feed**. Immettere un **nome** per il feed, l'**URL** da cui è fornito e il **periodo di aggiornamento**. Il periodo di aggiornamento specifica la frequenza con cui il Servizio decisionale personalizzato deve aggiornare il feed.

    ![Finestra di dialogo Nuovo feed](./media/custom-decision-service-tutorial/new-feed-dialog.png)

    I feed di azioni sono utilizzabili da qualsiasi app, indipendentemente da dove sono specificati. Dopo aver specificato entrambi i feed di azioni in uno scenario, essi compaiono nell'elenco:

    ![Elenco di feed](./media/custom-decision-service-tutorial/feeds.png)

    È possibile tornare a questo elenco facendo clic sul pulsante **Feed**.

## <a name="use-the-apis"></a>Usare le API

Il Servizio decisionale personalizzato classifica gli articoli tramite l'API di classificazione. Per usare questa API, inserire il codice seguente nell'intestazione HTML della pagina iniziale:

```html
<!-- Define the "callback function" to render UI -->
<script> function callback(data) { … } </script>

<!--  call Ranking API after callback() is defined, separately for each app -->
<script src="https://ds.microsoft.com/api/v2/app-politics/rank/feed-politics" async></script>
<script src="https://ds.microsoft.com/api/v2/app-sports/rank/feed-sports" async></script>
<script src="https://ds.microsoft.com/api/v2/app-recent/rank/feed-politics/feed-sports" async></script>
<!-- NB: action feeds for 'app-recent' are listed one after another. -->
```

La risposta HTTP dell'API di classificazione è una stringa in formato JSONP. Per app-politics, ad esempio, la stringa è simile a:

```json
callback({
   "ranking":[{"id":"url1"}, {"id":"url2"}, {"id":"url3"}],
   "eventId":"<opaque event string>",
   "appId":"app-politics",
   "actionSets":[{"id":"feed-politics","lastRefresh":"date"}] });
```

Il browser esegue quindi questa stringa come una chiamata alla funzione `callback()`. L'argomento `data` nella funzione `callback()` contiene l'ID app e la classificazione degli URL di cui eseguire il rendering. In particolare `callback()` deve usare `data.appId` per distinguere tra le tre applicazioni. `eventId` è utilizzato internamente dal Servizio decisionale personalizzato per associare la classificazione fornita al clic corrispondente, se esiste.

> [!TIP]
> `callback()` può controllare ogni feed di azioni per verificarne l'aggiornamento usando il campo `lastRefresh`. Se un feed specificato non è sufficientemente aggiornato, `callback()` può ignorare la classificazione fornita, chiamare direttamente il feed e usare la classificazione predefinita fornita dal feed.

Per altre informazioni sulle specifiche e opzioni aggiuntive fornite dall'API di classificazione, vedere le [informazioni di riferimento per l'API](custom-decision-service-api-reference.md).

Gli articoli preferiti dall'utente vengono restituiti chiamando l'API premio. Quando viene ricevuta la scelta di un articolo preferito, deve essere chiamato il codice seguente sulla pagina iniziale:

```javascript
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/<eventId>',
    contentType: "application/json" })
```

L'uso di `appId` e `eventId` nel codice di gestione dei clic richiede attenzione. Ad esempio, è possibile implementare la funzione `callback()` nel modo seguente:

```javascript
function callback(data) {
    $.map(data.ranking, function (element) {
        //custom rendering function given content info
        render({appId:data.appId,
                article:element,
                onClick: element.id != data.rewardAction ? null :
                   function() {
                       $.ajax({
                       type: "POST",
                       url: '//ds.microsoft.com/api/v2/' + data.appId + '/reward/' + data.eventId,
                       contentType: "application/json" })}
                });
}}
```

In questo esempio si implementa la funzione `render()` per eseguire il rendering di un determinato articolo per una determinata applicazione. Questa funzione fornisce come input l'ID app e l'articolo, nel formato dall'API di classificazione. Il parametro `onClick` è la funzione che deve essere chiamata da `render()` per gestire un clic. Controlla se il clic si trova sullo slot superiore. Quindi chiama l'API premio con l'ID app e l'ID evento appropriati.

## <a name="next-steps"></a>Passaggi successivi

* Consultare le [informazioni di riferimento sull'API](custom-decision-service-api-reference.md) per altre informazioni sulla funzionalità fornita.