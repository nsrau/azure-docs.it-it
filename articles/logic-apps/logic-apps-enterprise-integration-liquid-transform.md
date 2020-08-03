---
title: Convertire JSON e XML con i modelli Liquid
description: Trasformare JSON e XML usando i modelli Liquid come mappe in app per la logica di Azure
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, daviburg, logicappspm
ms.topic: article
ms.date: 07/31/2020
ms.openlocfilehash: 5aa6b3717925146607f3785ad5ea5fb940e8c236
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87503394"
---
# <a name="transform-json-and-xml-using-liquid-templates-as-maps-in-azure-logic-apps"></a>Trasformare JSON e XML usando i modelli Liquid come mappe in app per la logica di Azure

Quando si vogliono eseguire trasformazioni JSON di base nelle app per la logica, è possibile usare [operazioni dati](../logic-apps/logic-apps-perform-data-operations.md) native, ad esempio **compose** o **Parse JSON**. Per le trasformazioni JSON avanzate e complesse che includono elementi quali iterazioni, flussi di controllo e variabili, creare e usare modelli che descrivono queste trasformazioni usando il linguaggio del modello open source [Liquid](https://shopify.github.io/liquid/) . È anche possibile [eseguire altre trasformazioni](#other-transformations), ad esempio da JSON a testo, da XML a JSON e da XML a testo.

Prima di poter eseguire una trasformazione Liquid nell'app per la logica, è prima necessario creare un modello Liquid che definisce il mapping desiderato. [Il modello viene quindi caricato come mappa](../logic-apps/logic-apps-enterprise-integration-maps.md) nell'account di [integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md). Quando si aggiunge l'azione **Transform JSON to JSON-Liquid** all'app per la logica, è possibile selezionare il modello Liquid come mappa per l'azione da usare.

Questo articolo illustra come completare queste attività:

* Creare un modello Liquid.
* Aggiungere il modello all'account di integrazione.
* Aggiungere l'azione di trasformazione Liquid all'app per la logica.
* Selezionare il modello come mappa che si vuole usare.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Un [account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* Informazioni di base sul [linguaggio del modello Liquid](https://shopify.github.io/liquid/)

  > [!NOTE]
  > L'azione **trasforma JSON in JSON-Liquid** segue l' [implementazione di DotLiquid per Liquid](https://github.com/dotliquid/dotliquid), che differisce da casi specifici dell' [implementazione di Shopify per Liquid](https://shopify.github.io/liquid). Per altre informazioni, vedere [considerazioni sui modelli Liquid](#template-considerations).

## <a name="create-the-template"></a>Creare il modello

1. Creare il modello Liquid usato come mappa per la trasformazione JSON. È possibile utilizzare qualsiasi strumento di modifica desiderato.

   Per questo esempio, creare il modello Liquid di esempio come descritto in questa sezione:

   ```json
   {%- assign deviceList = content.devices | Split: ', ' -%}

   {
      "fullName": "{{content.firstName | Append: ' ' | Append: content.lastName}}",
      "firstNameUpperCase": "{{content.firstName | Upcase}}",
      "phoneAreaCode": "{{content.phone | Slice: 1, 3}}",
      "devices" : [
         {%- for device in deviceList -%}
            {%- if forloop.Last == true -%}
            "{{device}}"
            {%- else -%}
            "{{device}}",
            {%- endif -%}
         {%- endfor -%}
      ]
   }
   ```

1. Salvare il modello usando l' `.liquid` estensione. In questo esempio viene utilizzato `SimpleJsonToJsonTemplate.liquid`.

## <a name="upload-the-template"></a>Caricare il modello

1. Accedere al [portale di Azure](https://portal.azure.com) con le credenziali dell'account Azure.

1. Nella casella di ricerca portale di Azure immettere `integration accounts` e selezionare account di **integrazione**.

   ![Trova "account di integrazione"](./media/logic-apps-enterprise-integration-liquid-transform/find-integration-accounts.png)

1. Trovare e selezionare l'account di integrazione.

   ![Selezionare l'account di integrazione](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

1. Nel riquadro **Panoramica** , in **componenti**, selezionare **Maps**.

    ![Selezionare il riquadro "mappe"](./media/logic-apps-enterprise-integration-liquid-transform/select-maps-tile.png)

1. Nel riquadro **mappe** selezionare **Aggiungi** e specificare i dettagli per la mappa:

   | Proprietà | Valore | Descrizione |
   |----------|-------|-------------|
   | **Nome** | `JsonToJsonTemplate` | Nome per la mappa, ovvero "JsonToJsonTemplate" in questo esempio |
   | **Tipo di mapping** | **liquid** | Tipo di mappa. Per la trasformazione da JSON a JSON, è necessario selezionare **Liquid**. |
   | **Mappa** | `SimpleJsonToJsonTemplate.liquid` | File di mappa o di modello Liquid esistente da usare per la trasformazione, ovvero "SimpleJsonToJsonTemplate.liquid" in questo esempio. Per trovare questo file, è possibile usare la selezione file. Per i limiti delle dimensioni della mappa, vedere [limiti e configurazione](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits). |
   |||

   ![Aggiungi modello Liquid](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)

## <a name="add-the-liquid-transformation-action"></a>Aggiungere l'azione di trasformazione Liquid

1. Nel portale di Azure seguire questa procedura per [creare un'app per la logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Nella finestra di progettazione di app per la logica aggiungere il [trigger Request](../connectors/connectors-native-reqres.md#add-request) all'app per la logica.

1. Nel trigger scegliere **Nuovo passaggio**. Nella casella di ricerca immettere `liquid` come filtro e selezionare questa azione: **trasforma JSON in JSON-Liquid**

   ![Trovare e selezionare l'azione Liquid](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

1. Aprire l'elenco **mappa** e selezionare il modello Liquid, ovvero "JsonToJsonTemplate" in questo esempio.

   ![Selezionare la mappa](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Se l'elenco Maps è vuoto, probabilmente l'app per la logica non è collegata all'account di integrazione. 
   Per collegare l'app per la logica all'account di integrazione che include il modello o la mappa Liquid, seguire questa procedura:

   1. Nel menu dell'app per la logica selezionare **Impostazioni del flusso di lavoro**.

   1. Nell'elenco **selezionare un account di integrazione** selezionare l'account di integrazione e selezionare **Salva**.

      ![Collegare l'app per la logica all'account di integrazione](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

1. A questo punto aggiungere la proprietà **Content** a questa azione. Aprire l'elenco **Aggiungi nuovo parametro** e selezionare **contenuto**.

   ![Aggiungere la proprietà "Content" all'azione](./media/logic-apps-enterprise-integration-liquid-transform/add-content-property-to-action.png)

1. Per impostare il valore della proprietà **contenuto** , fare clic all'interno della casella **contenuto** in modo che venga visualizzato l'elenco contenuto dinamico. Selezionare il token del **corpo** che rappresenta l'output del contenuto del corpo del trigger.

   ![Selezionare il token "Body" per il valore della proprietà "Content"](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)

   Al termine, l'azione si presenterà come in questo esempio:

   ![Azione "trasforma JSON in JSON" completata](./media/logic-apps-enterprise-integration-liquid-transform/finished-transform-action.png)

## <a name="test-your-logic-app"></a>Testare l'app per la logica

Usando il [posting](https://www.getpostman.com/postman) o uno strumento simile, pubblicare l'input JSON nell'app per la logica. L'output JSON trasformato dall'app per la logica è simile al seguente:

![Output di esempio](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

<a name="template-considerations"></a>

## <a name="liquid-template-considerations"></a>Considerazioni sui modelli Liquid

* I modelli Liquid seguono i [limiti delle dimensioni dei file per le mappe](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits) in app per la logica di Azure.

* L'azione **trasforma JSON in JSON-Liquid** segue l' [implementazione di DotLiquid per Liquid](https://github.com/dotliquid/dotliquid). Questa implementazione è una porta per la .NET Framework dall' [implementazione di Shopify per Liquid](https://shopify.github.io/liquid/) e differisce da [casi specifici](https://github.com/dotliquid/dotliquid/issues).

  Di seguito sono riportate le differenze note:

  * L'azione **trasforma JSON in JSON-Liquid restituisce in** modo nativo una stringa che può includere JSON, XML, HTML e così via. L'azione Liquid indica solo che l'output di testo previsto dall'oggetto del modello Liquid è una stringa JSON. L'azione indica all'app per la logica di analizzare l'input come oggetto JSON e applica un wrapper in modo che Liquid possa interpretare la struttura JSON. Dopo la trasformazione, l'azione indica all'app per la logica di analizzare l'output di testo da Liquid a JSON.

    DotLiquid non riconosce in modo nativo JSON, quindi assicurarsi di eseguire l'escape del carattere barra rovesciata ( `\` ) e di eventuali altri caratteri JSON riservati.

  * Se il modello USA [filtri Liquid](https://shopify.github.io/liquid/basics/introduction/#filters), assicurarsi di seguire le [convenzioni di denominazione DotLiquid e C#](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers#filter-and-output-casing), che usano la *combinazione di maiuscole e minuscole*. Per tutte le trasformazioni liquide, assicurarsi che i nomi di filtro nel modello usino anche la combinazione di maiuscole e minuscole. In caso contrario, i filtri non funzioneranno.

    Ad esempio, quando si usa il `replace` filtro, usare `Replace` , Not `replace` . La stessa regola si applica se si provano esempi in [DotLiquid online](http://dotliquidmarkup.org/try-online). Per ulteriori informazioni, vedere [Shopify Liquid filters](https://shopify.dev/docs/themes/liquid/reference/filters) e [DotLiquid Liquid filters](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Developers#create-your-own-filters). La specifica Shopify include esempi per ogni filtro. per il confronto, è quindi possibile provare questi esempi in [DotLiquid-try online](https://dotliquidmarkup.org/try-online).

  * Il `json` filtro dei filtri di estensione Shopify non è attualmente [implementato in DotLiquid](https://github.com/dotliquid/dotliquid/issues/384). In genere, è possibile usare questo filtro per preparare l'output di testo per l'analisi delle stringhe JSON, ma è invece necessario usare il `Replace` filtro.

  * Il `Replace` filtro standard nell' [implementazione di DotLiquid](https://github.com/dotliquid/dotliquid/blob/b6a7d992bf47e7d7dcec36fb402f2e0d70819388/src/DotLiquid/StandardFilters.cs#L425) usa la [corrispondenza di espressioni regolari (Regex)](/dotnet/standard/base-types/regular-expression-language-quick-reference), mentre l' [implementazione di Shopify](https://shopify.github.io/liquid/filters/replace/) usa la [corrispondenza di stringa semplice](https://github.com/Shopify/liquid/issues/202). Entrambe le implementazioni sembrano funzionare nello stesso modo fino a quando non si usa un carattere riservato RegEx o un carattere di escape nel parametro match.

    Ad esempio, per eseguire l'escape del carattere di escape della barra rovesciata ( `\` ), usare `| Replace: '\\', '\\'` e Not `| Replace: '\', '\\'` . In questi esempi viene illustrato il `Replace` comportamento del filtro in modo diverso quando si tenta di eseguire l'escape del carattere barra rovesciata. Questa versione funziona correttamente:

    `{ "SampleText": "{{ 'The quick brown fox "jumped" over the sleeping dog\\' | Replace: '\\', '\\' | Replace: '"', '\"'}}"}`

    Con questo risultato:

    `{ "SampleText": "The quick brown fox \"jumped\" over the sleeping dog\\\\"}`

    Questa versione ha esito negativo:

    `{ "SampleText": "{{ 'The quick brown fox "jumped" over the sleeping dog\\' | Replace: '\', '\\' | Replace: '"', '\"'}}"}`

    Con questo errore:

    `{ "SampleText": "Liquid error: parsing "\" - Illegal \ at end of pattern."}`

    Per ulteriori informazioni, vedere [Sostituisci filtro standard utilizza criteri di ricerca Regex...](https://github.com/dotliquid/dotliquid/issues/385).

  * Il `Sort` filtro nell' [implementazione di DotLiquid](https://github.com/dotliquid/dotliquid/blob/b6a7d992bf47e7d7dcec36fb402f2e0d70819388/src/DotLiquid/StandardFilters.cs#L326) Ordina gli elementi in una matrice o una raccolta per proprietà ma con le differenze seguenti:<p>

    * Segue [il comportamento sort_natural di Shopify](https://shopify.github.io/liquid/filters/sort_natural/), non [il comportamento di ordinamento di Shopify](https://shopify.github.io/liquid/filters/sort/).

    * Ordina solo nell'ordine alfanumerico di stringa. Per ulteriori informazioni, vedere [ordinamento numerico](https://github.com/Shopify/liquid/issues/980).

    * Usa l'ordine senza *distinzione* tra maiuscole e minuscole, senza distinzione tra maiuscole e minuscole. Per ulteriori informazioni, vedere [filtro di ordinamento non segue il comportamento della combinazione di maiuscole e minuscole dalla specifica di Shopify]( https://github.com/dotliquid/dotliquid/issues/393).

<a name="other-transformations"></a>

## <a name="other-transformations-using-liquid"></a>Altre trasformazioni con Liquid

Liquid non è limitato solo alle trasformazioni JSON. È anche possibile usare Liquid per eseguire altre trasformazioni, ad esempio:

* [Da JSON a testo](#json-text)
* [Da XML a JSON](#xml-json)
* [Da XML a testo](#xml-text)

<a name="json-text"></a>

### <a name="transform-json-to-text"></a>Trasforma JSON in testo

Ecco il modello Liquid usato per questo esempio:

```json
{{content.firstName | Append: ' ' | Append: content.lastName}}
```

Ecco gli input e gli output di esempio:

![Output di esempio di trasformazione JSON in testo](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

<a name="xml-json"></a>

### <a name="transform-xml-to-json"></a>Trasforma XML in JSON

Ecco il modello Liquid usato per questo esempio:

``` json
[{% JSONArrayFor item in content -%}
      {{item}}
  {% endJSONArrayFor -%}]
```

Il `JSONArrayFor` ciclo è un meccanismo di ciclo personalizzato per l'input XML, in modo che sia possibile creare payload JSON che evitano una virgola finale. Inoltre, la `where` condizione per questo meccanismo di ciclo personalizzato usa il nome dell'elemento XML per il confronto, anziché il valore dell'elemento come altri filtri Liquid. Per altre informazioni, vedere [Deep Dive on set-Body Policy-Collections of Things](https://azure.microsoft.com/blog/deep-dive-on-set-body-policy).

Ecco gli input e gli output di esempio:

![Esempio di output di trasformazione XML in JSON](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

<a name="xml-text"></a>

### <a name="transform-xml-to-text"></a>Trasforma XML in testo

Ecco il modello Liquid usato per questo esempio:

``` json
{{content.firstName | Append: ' ' | Append: content.lastName}}
```

Ecco gli input e gli output di esempio:

![Esempio di output di trasformazione XML in testo](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>Passaggi successivi

* [Linguaggi ed esempi di Shopify Liquid](https://shopify.github.io/liquid/basics/introduction/)
* [DotLiquid](http://dotliquidmarkup.org/)
* [DotLiquid-prova online](https://dotliquidmarkup.org/try-online)
* [GitHub DotLiquid](https://github.com/dotliquid/dotliquid)
* [Problemi di GitHub DotLiquid](https://github.com/dotliquid/dotliquid/issues/)
* Altre informazioni sulle [mappe](../logic-apps/logic-apps-enterprise-integration-maps.md)
