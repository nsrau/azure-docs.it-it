---
title: Come usare i valori denominati nei criteri di Gestione API di Azure
description: Informazioni su come usare i valori denominati nei criteri di Gestione API di Azure. I valori denominati possono contenere stringhe letterali ed espressioni di criteri.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/08/2020
ms.author: apimpm
ms.openlocfilehash: 3f317276ae92e6121d519553b7883677dab89705
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852192"
---
# <a name="how-to-use-named-values-in-azure-api-management-policies"></a>Come usare i valori denominati nei criteri di Gestione API di Azure

I criteri di Gestione API sono una potente funzionalità del sistema che consentono al portale di Azure di modificare il comportamento dell'API tramite la configurazione. I criteri sono una raccolta di istruzioni che vengono eseguite in modo sequenziale sulla richiesta o la risposta di un'API. Le istruzioni dei criteri possono essere costruite usando valori di testo letterali, espressioni di criteri e valori denominati.

Ogni istanza del servizio Gestione API dispone di una raccolta di coppie chiave/valore, chiamate valori denominati, globali per l'istanza del servizio. Non esiste alcun limite imposto per il numero di elementi nella raccolta. I valori denominati possono essere usati per gestire i valori stringa costanti all'interno dell'intera configurazione e di tutti i criteri delle API. Ogni valore denominato può avere gli attributi seguenti:

| Attributo      | Type            | Descrizione                                                                                                                            |
| -------------- | --------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| `Display name` | string          | Usato per fare riferimento al valore denominato nei criteri. Stringa da uno a 256 caratteri. Sono consentiti solo lettere, numeri, punti e trattini. |
| `Value`        | string          | Valore effettivo. Non deve essere vuoto o contenere solo spazi. Lunghezza massima di 4096 caratteri.                                        |
| `Secret`       | boolean         | Determina se il valore è un segreto e se deve essere crittografato.                                                               |
| `Tags`         | matrice di valori string | Usato per filtrare l'elenco di valori denominati. Fino a 32 tag.                                                                                    |

![Valori denominati](./media/api-management-howto-properties/named-values.png)

I valori denominati possono contenere stringhe letterali ed [espressioni di criteri](./api-management-policy-expressions.md). Ad esempio, il valore di `Expression` è un'espressione di criteri che restituisce una stringa contenente la data e l'ora correnti. Il valore denominato `Credential` è contrassegnato come segreto, quindi il relativo valore non viene visualizzato per impostazione predefinita.

| Nome       | valore                      | Segreto | Tag          |
| ---------- | -------------------------- | ------ | ------------- |
| valore      | 42                         | False  | vital-numbers |
| Credenziale | ••••••••••••••••••••••     | True   | security      |
| Expression | @(DateTime.Now.ToString()) | False  |               |

> [!NOTE]
> Anziché i valori denominati archiviati in un servizio Gestione API, è possibile usare i valori archiviati nel servizio [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), come illustrato in questo [esempio](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Look%20up%20Key%20Vault%20secret%20using%20Managed%20Service%20Identity.policy.xml).

## <a name="to-add-and-edit-a-named-value"></a>Per aggiungere e modificare un valore denominato

![Aggiungere un valore denominato](./media/api-management-howto-properties/add-property.png)

1. Selezionare **API** in **GESTIONE API**.
2. Selezionare **Valori denominati**.
3. Premere **+Aggiungi**.

    Nome e Valore sono obbligatori. Se il valore è un segreto, selezionare la casella di controllo _Questo è un segreto_. Immettere uno o più tag facoltativi per organizzare i valori denominati e quindi fare clic su Salva.

4. Fare clic su **Crea**.

Dopo aver creato il valore denominato, è possibile modificarlo facendo clic su di esso. Se si modifica il nome del valore denominato, tutti i criteri che fanno riferimento a tale valore denominato vengono aggiornati automaticamente con il nuovo nome.

## <a name="to-delete-a-named-value"></a>Per eliminare un valore denominato

Per eliminare un valore denominato, fare clic su **Elimina** accanto al valore denominato da eliminare.

> [!IMPORTANT]
> Se all'interno di almeno un criterio si fa riferimento al valore denominato, sarà possibile eliminarlo solo dopo aver rimosso il valore denominato da tutti i criteri che lo usano.

## <a name="to-search-and-filter-named-values"></a>Per cercare e filtrare valori denominati

La scheda **Valori denominati** include funzionalità di ricerca e filtro che consentono di gestire i valori denominati. Per filtrare l'elenco dei valori denominati in base al nome, immettere un termine di ricerca nella casella di testo di **ricerca di proprietà**. Per visualizzare tutti i valori denominati, cancellare il contenuto della casella di testo di **ricerca di proprietà** e premere INVIO.

Per filtrare l'elenco in base al tag, immettere uno o più tag nella casella di testo di **filtro in base ai tag**. Per visualizzare tutti i valori denominati, cancellare il contenuto della casella di testo di **filtro in base ai tag** e premere INVIO.

## <a name="to-use-a-named-value"></a>Per usare un valore denominato

Per usare un valore denominato in un criterio, inserirne il nome all'interno di parentesi graffe doppie, ad esempio `{{ContosoHeader}}`, come illustrato nell'esempio seguente:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

In questo esempio, la proprietà `ContosoHeader` viene usata come nome di un'intestazione in un criterio `set-header` e la proprietà `ContosoHeaderValue` viene usata come valore di tale intestazione. Quando questo criterio viene valutato durante una richiesta o una risposta al gateway di Gestione API, `{{ContosoHeader}}` e `{{ContosoHeaderValue}}` vengono sostituiti dai rispettivi valori.

I valori denominati possono essere usati come valori completi di attributo o di elemento, come illustrato nell'esempio precedente, ma possono anche essere inseriti all'interno di un'espressione di testo o combinati con una parte di un'espressione di testo, come illustrato nell'esempio seguente: `<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

I valori denominati possono anche contenere espressioni di criteri. Nell'esempio seguente viene usata la proprietà `ExpressionProperty` .

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

Quando questo criterio viene valutato, `{{ExpressionProperty}}` viene sostituito dal valore corrispondente: `@(DateTime.Now.ToString())`. Poiché il valore è un'espressione di criteri, l'espressione viene valutata e il criterio passa alla fase di esecuzione.

È possibile eseguirne i test nel portale per sviluppatori chiamando un'operazione il cui ambito contiene criteri con valori denominati. Nell'esempio seguente viene chiamata un'operazione che contiene i due criteri di esempio precedenti `set-header` con valori denominati. La risposta contiene due intestazioni personalizzate che vengono configurate tramite criteri con valori denominati.

![Portale per sviluppatori][api-management-send-results]

Se si osserva la [traccia di Controllo API](api-management-howto-api-inspector.md) relativa a una chiamata che include i due criteri con valori denominati degli esempi precedenti, è possibile vedere i due criteri `set-header` con i valori denominati inseriti, nonché la valutazione delle espressioni dei criteri per il valore denominato che contiene l'espressione.

![Traccia di Controllo API][api-management-api-inspector-trace]

Sebbene i valori denominati possano contenere espressioni di criteri, non possono contenere altri valori denominati. Se il testo contenente un riferimento a un valore denominato viene usato per un valore, ad esempio `Text: {{MyProperty}}`, tale riferimento non verrà risolto e sostituito.

## <a name="next-steps"></a>Passaggi successivi

-   Ulteriori informazioni sull'uso dei criteri
    -   [Criteri di Gestione API](api-management-howto-policies.md)
    -   [Riferimento ai criteri](./api-management-policies.md)
    -   [Espressioni di criteri](./api-management-policy-expressions.md)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png
