---
title: Come usare i valori denominati nei criteri di gestione API di Azure
description: Informazioni su come usare i valori denominati nei criteri di gestione API di Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/05/2019
ms.author: apimpm
ms.openlocfilehash: d11239aa49a53a90a38f2b5336d36cea6c97e9df
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824166"
---
# <a name="how-to-use-named-values-in-azure-api-management-policies"></a>Come usare i valori denominati nei criteri di gestione API di Azure

I criteri di Gestione API sono una potente funzionalità del sistema che consentono al portale di Azure di modificare il comportamento dell'API tramite la configurazione. I criteri sono una raccolta di istruzioni che vengono eseguite in modo sequenziale sulla richiesta o la risposta di un'API. Le istruzioni dei criteri possono essere costruite usando valori di testo letterali, espressioni di criteri e valori denominati.

Ogni istanza del servizio gestione API ha una raccolta Properties di coppie chiave/valore, denominate valori denominati, globali per l'istanza del servizio. Non esiste alcun limite imposto per il numero di elementi nella raccolta. I valori denominati possono essere usati per gestire i valori stringa costanti in tutti i criteri e la configurazione dell'API. Ogni valore denominato può avere gli attributi seguenti:

| Attributo      | Tipo            | Descrizione                                                                                                                         |
| -------------- | --------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| `Display name` | stringa          | Utilizzato per fare riferimento al valore denominato nei criteri. Stringa da uno a 256 caratteri. Sono consentiti solo lettere, numeri, punti e trattini. |
| `Value`        | stringa          | Valore effettivo. Non devono essere vuoti o contenere solo spazi. Lunghezza massima di 4096 caratteri.                                     |
| `Secret`       | boolean         | Determina se il valore è un segreto e se deve essere crittografato.                                                            |
| `Tags`         | matrice di valori string | Utilizzato per filtrare l'elenco di valori denominati. Fino a 32 tag.                                                                                    |

![Valori denominati](./media/api-management-howto-properties/named-values.png)

I valori denominati possono contenere stringhe letterali ed [espressioni di criteri](/azure/api-management/api-management-policy-expressions). Ad esempio, il valore di `Expression` è un'espressione di criteri che restituisce una stringa contenente la data e l'ora correnti. Il valore denominato `Credential` è contrassegnato come segreto, quindi il relativo valore non viene visualizzato per impostazione predefinita.

| Name       | Valore                      | Segreto | Tag          |
| ---------- | -------------------------- | ------ | ------------- |
| Valore      | 42                         | False  | numeri vitali |
| Credenziali | ••••••••••••••••••••••     | True   | security      |
| Expression | @(DateTime.Now.ToString()) | False  |               |

## <a name="to-add-and-edit-a-named-value"></a>Per aggiungere e modificare un valore denominato

![Aggiungere un valore denominato](./media/api-management-howto-properties/add-property.png)

1. Selezionare **API** in **GESTIONE API**.
2. Selezionare **Valori denominati**.
3. Premere **+Aggiungi**.

    Nome e Valore sono obbligatori. Se il valore è un segreto, selezionare la casella *di controllo questo è un segreto* . Immettere uno o più tag facoltativi per organizzare i valori denominati e quindi fare clic su Salva.

4. Fare clic su **Create**.

Una volta creato il valore denominato, è possibile modificarlo facendo clic su di esso. Se si modifica il nome del valore denominato, tutti i criteri che fanno riferimento a quel valore denominato vengono aggiornati automaticamente in modo da usare il nuovo nome.

Per informazioni sulla modifica di un valore denominato usando l'API REST, vedere [modificare un valore denominato usando l'API REST](/rest/api/apimanagement/2019-01-01/property?patch).

## <a name="to-delete-a-named-value"></a>Per eliminare un valore denominato

Per eliminare un valore denominato, fare clic su **Elimina** accanto al valore denominato da eliminare.

> [!IMPORTANT]
> Se un criterio fa riferimento a un valore denominato, non sarà possibile eliminarlo correttamente fino a quando non si rimuove il valore denominato da tutti i criteri che lo usano.

Per informazioni sull'eliminazione di un valore denominato usando l'API REST, vedere [eliminare un valore denominato usando l'API REST](/rest/api/apimanagement/2019-01-01/property/delete).

## <a name="to-search-and-filter-named-values"></a>Per ricercare e filtrare i valori denominati

La scheda **Valori denominati** include funzionalità di ricerca e filtro che consentono di gestire i valori denominati. Per filtrare l'elenco di valori denominati in base al nome, immettere un termine di ricerca nella casella di testo **proprietà di ricerca** . Per visualizzare tutti i valori denominati, cancellare il contenuto della casella di testo di **ricerca di proprietà** e premere INVIO.

Per filtrare l'elenco in base al tag, immettere uno o più tag nella casella **di testo Filtra per tag** . Per visualizzare tutti i valori denominati, cancellare il contenuto della casella di testo di **filtro in base ai tag** e premere INVIO.

## <a name="to-use-a-named-value"></a>Per usare un valore denominato

Per usare un valore denominato in un criterio, inserire il nome all'interno di una coppia doppia di parentesi graffe, ad esempio `{{ContosoHeader}}`, come illustrato nell'esempio seguente:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

In questo esempio, la proprietà `ContosoHeader` viene usata come nome di un'intestazione in un criterio `set-header` e la proprietà `ContosoHeaderValue` viene usata come valore di tale intestazione. Quando questo criterio viene valutato durante una richiesta o una risposta al gateway di gestione API, `{{ContosoHeader}}` e `{{ContosoHeaderValue}}` vengono sostituiti con i rispettivi valori.

I valori denominati possono essere usati come valori completi di attributo o di elemento, come illustrato nell'esempio precedente, ma possono anche essere inseriti all'interno di un'espressione di testo o combinati con una parte di un'espressione di testo, come illustrato nell'esempio seguente: `<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

I valori denominati possono anche contenere espressioni di criteri. Nell'esempio seguente viene usata la proprietà `ExpressionProperty` .

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

Quando questo criterio viene valutato, `{{ExpressionProperty}}` viene sostituito dal valore corrispondente: `@(DateTime.Now.ToString())`. Poiché il valore è un'espressione di criteri, l'espressione viene valutata e il criterio passa alla fase di esecuzione.

È possibile eseguirne i test nel portale per sviluppatori chiamando un'operazione il cui ambito contiene criteri con valori denominati. Nell'esempio seguente viene chiamata un'operazione che contiene i due criteri di esempio precedenti `set-header` con valori denominati. La risposta contiene due intestazioni personalizzate che vengono configurate tramite criteri con valori denominati.

![portale per sviluppatori][api-management-send-results]

Se si esamina la [traccia di controllo API](api-management-howto-api-inspector.md) per una chiamata che include i due criteri di esempio precedenti con i valori denominati, è possibile visualizzare i due criteri di `set-header` con i valori denominati inseriti e la valutazione dell'espressione di criteri per il valore denominato che contiene l'espressione di criteri.

![Traccia di Controllo API][api-management-api-inspector-trace]

Mentre i valori denominati possono contenere espressioni di criteri, non possono contenere altri valori denominati. Se il testo contenente un riferimento a un valore denominato viene usato per un valore, ad esempio `Text: {{MyProperty}}`, il riferimento non verrà risolto e sostituito.

## <a name="next-steps"></a>Passaggi successivi

-   Ulteriori informazioni sull'uso dei criteri
    -   [Criteri in Gestione API](api-management-howto-policies.md)
    -   [Riferimenti per i criteri](/azure/api-management/api-management-policies)
    -   [Espressioni di criteri](/azure/api-management/api-management-policy-expressions)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png
