---
title: Come usare i valori denominati nei criteri di Gestione API di Azure
description: Informazioni su come usare i valori denominati nei criteri di Gestione API di Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: apimpm
ms.openlocfilehash: f7c52b7ab8aaad917eb03455800df6d8ba4cbc88
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58082705"
---
# <a name="how-to-use-named-values-in-azure-api-management-policies"></a>Come usare i valori denominati nei criteri di Gestione API di Azure
I criteri di Gestione API sono una potente funzionalità del sistema che consentono al portale di Azure di modificare il comportamento dell'API tramite la configurazione. I criteri sono una raccolta di istruzioni che vengono eseguite in modo sequenziale sulla richiesta o la risposta di un'API. Le istruzioni dei criteri possono essere costruite usando valori di testo letterali, espressioni di criteri e valori denominati. 

Ogni istanza del servizio Gestione API dispone di una raccolta di proprietà di coppie chiave/valore, chiamate valori denominati, globali per l'istanza del servizio. Questi valori denominati possono essere usati per gestire i valori stringa costanti all'interno dell'intera configurazione e di tutti i criteri delle API. Ogni proprietà può avere gli attributi seguenti:

| Attributo | Type | DESCRIZIONE |
| --- | --- | --- |
| Nome visualizzato |stringa |Stringa alfanumerica usata per fare riferimento alla proprietà nei criteri. |
| Valore |stringa |Valore della proprietà. Non può essere vuoto o contenere solo spazi. |
|Segreto|boolean|Determina se il valore è un segreto e se deve essere crittografato.|
| Tag |matrice di valori string |Facoltativi. Quando specificati possono essere usati per filtrare l'elenco delle proprietà. |

![Valori denominati](./media/api-management-howto-properties/named-values.png)

I valori delle proprietà possono contenere stringhe letterali ed [espressioni di criteri](https://msdn.microsoft.com/library/azure/dn910913.aspx). Ad esempio, il valore di `ExpressionProperty` è un'espressione di criteri che restituisce una stringa contenente la data e l'ora correnti. La proprietà `ContosoHeaderValue` è contrassegnata come un segreto, quindi il valore corrispondente non viene visualizzato.

| NOME | Valore | Segreto | Tag |
| --- | --- | --- | --- |
| ContosoHeader |TrackingId |False |Contoso |
| ContosoHeaderValue |•••••••••••••••••••••• |True  |Contoso |
| ExpressionProperty |@(DateTime.Now.ToString()) |False | |

## <a name="to-add-and-edit-a-property"></a>Per aggiungere e modificare una proprietà

![Aggiungi proprietà](./media/api-management-howto-properties/add-property.png)

1. Selezionare **API** in **GESTIONE API**.
2. Selezionare **Valori denominati**.
3. Premere **+Aggiungi**.

   Nome e Valore sono obbligatori. Se il valore della proprietà è un segreto, selezionare la casella di controllo Questo è un segreto. Immettere uno o più tag facoltativi per organizzare i valori denominati e quindi fare clic su Salva.
4. Fare clic su **Create**(Crea).

Dopo aver creato la proprietà, è possibile modificarla facendo clic sulla proprietà. Se si modifica il nome della proprietà, tutti i criteri che fanno riferimento a tale proprietà vengono aggiornati automaticamente con il nuovo nome.

Per informazioni sulla modifica di una proprietà tramite l'API REST, vedere la [pagina relativa alla modifica di una proprietà tramite l'API REST](https://msdn.microsoft.com/library/azure/mt651775.aspx#Patch).

## <a name="to-delete-a-property"></a>Per eliminare una proprietà

Per eliminare una proprietà, fare clic su **Delete** accanto alla proprietà da eliminare.

> [!IMPORTANT]
> Se all'interno di almeno un criterio si fa riferimento alla proprietà, sarà possibile eliminare quest'ultima solo dopo averla rimossa da tutti i criteri che la usano.
> 
> 

Per informazioni sull'eliminazione di una proprietà tramite l'API REST, vedere la [pagina relativa all'eliminazione di una proprietà tramite l'API REST](https://msdn.microsoft.com/library/azure/mt651775.aspx#Delete).

## <a name="to-search-and-filter-named-values"></a>Per cercare e filtrare valori denominati

La scheda **Valori denominati** include funzionalità di ricerca e filtro che consentono di gestire i valori denominati. Per filtrare l'elenco delle proprietà in base al nome di queste, immettere un termine di ricerca nella casella di testo di **ricerca di proprietà** . Per visualizzare tutti i valori denominati, cancellare il contenuto della casella di testo di **ricerca di proprietà** e premere INVIO.

Per filtrare l'elenco delle proprietà in base al valore dei tag, immettere uno o più tag nella casella di testo di **filtro in base ai tag** . Per visualizzare tutti i valori denominati, cancellare il contenuto della casella di testo di **filtro in base ai tag** e premere INVIO.

## <a name="to-use-a-property"></a>Per usare una proprietà

Per usare una proprietà di un criterio, inserire il nome della proprietà all'interno di parentesi graffe doppie, ad esempio `{{ContosoHeader}}`, come illustrato nell'esempio seguente:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

In questo esempio, la proprietà `ContosoHeader` viene usata come nome di un'intestazione in un criterio `set-header` e la proprietà `ContosoHeaderValue` viene usata come valore di tale intestazione. Quando questo criterio viene valutato durante una richiesta o una risposta al gateway di Gestione API, `{{ContosoHeader}}` e `{{ContosoHeaderValue}}` vengono sostituiti dai valori delle rispettive proprietà.

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

Se si osserva la [traccia di Controllo API](api-management-howto-api-inspector.md) relativa a una chiamata che include i due criteri con valori denominati degli esempi precedenti, è possibile vedere i due criteri `set-header` con i valori delle proprietà inseriti, nonché la valutazione delle espressioni dei criteri per la proprietà che contiene l'espressione.

![Traccia di Controllo API][api-management-api-inspector-trace]

Sebbene i valori delle proprietà possano contenere espressioni di criteri, non possono contenere altri valori denominati. Se il testo che contiene un riferimento a una proprietà viene usato come valore di una proprietà, ad esempio `Property value text {{MyProperty}}`, tale riferimento alla proprietà non viene sostituito e viene incluso come parte del valore della proprietà.

## <a name="next-steps"></a>Passaggi successivi
* Ulteriori informazioni sull'uso dei criteri
  * [Criteri in Gestione API](api-management-howto-policies.md)
  * [Riferimento ai criteri](https://msdn.microsoft.com/library/azure/dn894081.aspx)
  * [Espressioni di criteri](https://msdn.microsoft.com/library/azure/dn910913.aspx)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png

