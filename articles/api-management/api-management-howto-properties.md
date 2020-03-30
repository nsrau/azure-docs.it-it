---
title: Come usare valori denominati nei criteri di Gestione API di AzureHow to use named values in Azure API Management policies
description: Informazioni su come usare valori denominati nei criteri di gestione delle API di Azure.Learn how to use named values in Azure API Management policies.
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
ms.openlocfilehash: 7c25455e28e57ff40664a69718a2e406b52b7632
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834304"
---
# <a name="how-to-use-named-values-in-azure-api-management-policies"></a>Come usare valori denominati nei criteri di Gestione API di AzureHow to use named values in Azure API Management policies

I criteri di Gestione API sono una potente funzionalità del sistema che consentono al portale di Azure di modificare il comportamento dell'API tramite la configurazione. I criteri sono una raccolta di istruzioni che vengono eseguite in modo sequenziale sulla richiesta o la risposta di un'API. Le istruzioni dei criteri possono essere costruite usando valori di testo letterali, espressioni di criteri e valori denominati.

Ogni istanza del servizio Gestione API dispone di una raccolta di coppie chiave/valore, denominate valori denominati, globali per l'istanza del servizio. Non esiste alcun limite imposto al numero di elementi nella raccolta. I valori denominati possono essere utilizzati per gestire valori stringa costanti in tutti i criteri e la configurazione delle API. Ogni valore denominato può avere i seguenti attributi:

| Attributo      | Type            | Descrizione                                                                                                                            |
| -------------- | --------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| `Display name` | string          | Utilizzato per fare riferimento al valore denominato nei criteri. Una stringa da uno a 256 caratteri. Sono consentiti solo lettere, numeri, punti e trattini. |
| `Value`        | string          | Valore effettivo. Non deve essere vuoto o costituito solo da spazi vuoti. Lunghezza massima di 4096 caratteri.                                        |
| `Secret`       | boolean         | Determina se il valore è un segreto e se deve essere crittografato.                                                               |
| `Tags`         | matrice di valori string | Utilizzato per filtrare l'elenco di valori denominato. Fino a 32 tag.                                                                                    |

![Valori denominati](./media/api-management-howto-properties/named-values.png)

I valori denominati possono contenere stringhe letterali ed [espressioni di criteri.](/azure/api-management/api-management-policy-expressions) Ad esempio, il valore di `Expression` è un'espressione di criteri che restituisce una stringa contenente la data e l'ora correnti. Il valore `Credential` denominato è contrassegnato come segreto, pertanto il relativo valore non viene visualizzato per impostazione predefinita.

| Nome       | valore                      | Segreto | Tag          |
| ---------- | -------------------------- | ------ | ------------- |
| valore      | 42                         | False  | numeri vitali |
| Credenziale | ••••••••••••••••••••••     | True   | security      |
| Expression | @(DateTime.Now.ToString()) | False  |               |

> [!NOTE]
> Anziché i valori denominati archiviati in un servizio Gestione API, è possibile usare i valori archiviati nel servizio [Dell'insieme](https://azure.microsoft.com/services/key-vault/) di credenziali delle chiavi di Azure, come illustrato in questo [esempio.](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Look%20up%20Key%20Vault%20secret%20using%20Managed%20Service%20Identity.policy.xml)

## <a name="to-add-and-edit-a-named-value"></a>Per aggiungere e modificare un valore denominato

![Aggiungere un valore denominato](./media/api-management-howto-properties/add-property.png)

1. Selezionare **le API** da **gestione API**.
2. Selezionare **Valori denominati**.
3. Premere **il tasto Aggiungi**.

    Nome e Valore sono obbligatori. Se value è un segreto, selezionare la casella di controllo _This is a secret._ Immettere uno o più tag facoltativi per organizzare i valori denominati e quindi fare clic su Salva.

4. Fare clic su **Crea**.

Una volta creato il valore denominato, è possibile modificarlo facendo clic su di esso. Se si modifica il nome del valore denominato, tutti i criteri che fanno riferimento al valore denominato vengono aggiornati automaticamente per utilizzare il nuovo nome.

Per informazioni sulla modifica di un valore denominato tramite l'API REST, vedere [Modificare un valore denominato tramite l'API REST.](/rest/api/apimanagement/2019-01-01/property?patch)

## <a name="to-delete-a-named-value"></a>Per eliminare un valore denominato

Per eliminare un valore denominato, fare clic su **Elimina** accanto al valore denominato da eliminare.

> [!IMPORTANT]
> Se il valore denominato fa riferimento a qualsiasi criterio, non sarà possibile eliminarlo correttamente finché non si rimuove il valore denominato da tutti i criteri che lo utilizzano.

Per informazioni sull'eliminazione di un valore denominato tramite l'API REST, vedere [Eliminare un valore denominato tramite l'API REST.](/rest/api/apimanagement/2019-01-01/property/delete)

## <a name="to-search-and-filter-named-values"></a>Per cercare e filtrare i valori denominati

La scheda **Valori denominati** include funzionalità di ricerca e filtro che consentono di gestire i valori denominati. Per filtrare l'elenco dei valori denominati in base al nome, immettere un termine di ricerca nella casella di testo **Proprietà di ricerca.** Per visualizzare tutti i valori denominati, cancellare il contenuto della casella di testo di **ricerca di proprietà** e premere INVIO.

Per filtrare l'elenco in base al tag, immettere uno o più tag nella casella di testo **Filtra per tag.** Per visualizzare tutti i valori denominati, cancellare il contenuto della casella di testo di **filtro in base ai tag** e premere INVIO.

## <a name="to-use-a-named-value"></a>Per utilizzare un valore denominato

Per utilizzare un valore denominato in un criterio, inserire il `{{ContosoHeader}}`nome all'interno di una coppia doppia di parentesi graffe, ad esempio , come illustrato nell'esempio seguente:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

In questo esempio, la proprietà `ContosoHeader` viene usata come nome di un'intestazione in un criterio `set-header` e la proprietà `ContosoHeaderValue` viene usata come valore di tale intestazione. Quando questo criterio viene valutato durante una richiesta `{{ContosoHeader}}` o `{{ContosoHeaderValue}}` una risposta al gateway di Gestione API e vengono sostituiti con i rispettivi valori.

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

Se si esamina la traccia di [Controllo API](api-management-howto-api-inspector.md) per una chiamata che include i `set-header` due criteri di esempio precedenti con valori denominati, è possibile visualizzare i due criteri con i valori denominati inseriti, nonché la valutazione dell'espressione di criteri per il valore denominato che contiene l'espressione di criteri.

![Traccia di Controllo API][api-management-api-inspector-trace]

Sebbene i valori denominati possano contenere espressioni di criteri, non possono contenere altri valori denominati. Se per un valore viene utilizzato il testo `Text: {{MyProperty}}`contenente un riferimento a un valore denominato, ad esempio , tale riferimento non verrà risolto e sostituito.

## <a name="next-steps"></a>Passaggi successivi

-   Ulteriori informazioni sull'uso dei criteri
    -   [Criteri di Gestione API](api-management-howto-policies.md)
    -   [Informazioni di riferimento sui criteri](/azure/api-management/api-management-policies)
    -   [Espressioni di criteri](/azure/api-management/api-management-policy-expressions)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png
