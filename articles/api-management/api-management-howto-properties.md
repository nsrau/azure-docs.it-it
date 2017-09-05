---
title: "Come usare le proprietà nei criteri di Gestione API di Azure"
description: "Informazioni su come usare le proprietà nei criteri di Gestione API di Azure"
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 6f39b00f-cf6e-4cef-9bf2-1f89202c0bc0
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.translationtype: Human Translation
ms.sourcegitcommit: 94e13ac6fec09081484a2f7f5d7bc1871822743f
ms.openlocfilehash: 3b0fe2a300038e13cc488bdb4f50f8be270ea8f4
ms.contentlocale: it-it
ms.lasthandoff: 07/06/2017


---
# <a name="how-to-use-properties-in-azure-api-management-policies"></a>Come usare le proprietà nei criteri di Gestione API di Azure
I criteri di Gestione API sono una potente funzionalità del sistema che consentono all'entità di pubblicazione di modificare il comportamento dell'API tramite la configurazione. I criteri sono una raccolta di istruzioni che vengono eseguite in modo sequenziale sulla richiesta o la risposta di un'API. Per creare istruzioni dei criteri è possibile usare valori di testo, espressioni di criteri e proprietà. 

Ogni istanza del servizio Gestione API dispone di una raccolta di proprietà di coppie chiave/valore globali per l'istanza stessa. Queste proprietà possono essere usate per gestire i valori stringa costanti all'interno dell'intera configurazione e di tutti i criteri delle API. Ogni proprietà ha gli attributi seguenti.

| Attributo | Tipo | Descrizione |
| --- | --- | --- |
| Name |stringa |Nome della proprietà. Può contenere solo lettere, cifre, punti, trattini e caratteri di sottolineatura. |
| Valore |stringa |Valore della proprietà. Non può essere vuoto o contenere solo spazi. |
| Segreto |boolean |Determina se il valore è un segreto e se deve essere crittografato. |
| Tag |matrice di valori string |Facoltativi. Quando specificati possono essere usati per filtrare l'elenco delle proprietà. |

Le proprietà vengono configurate nel portale di pubblicazione nella scheda **Properties** . Nell'esempio seguente, sono configurate tre proprietà.

![Properties][api-management-properties]

I valori delle proprietà possono contenere stringhe letterali ed [espressioni di criteri](https://msdn.microsoft.com/library/azure/dn910913.aspx). Nella tabella seguente sono mostrate le tre proprietà di esempio precedenti e i relativi attributi. Il valore di `ExpressionProperty` è un'espressione di criteri che restituisce una stringa contenente la data e l'ora correnti. La proprietà `ContosoHeaderValue` è contrassegnata come un segreto, quindi il valore corrispondente non viene visualizzato.

| Name | Valore | Segreto | Tag |
| --- | --- | --- | --- |
| ContosoHeader |TrackingId |False |Contoso |
| ContosoHeaderValue |•••••••••••••••••••••• |True |Contoso |
| ExpressionProperty |@(DateTime.Now.ToString()) |False | |

## <a name="to-use-a-property"></a>Per usare una proprietà
Per usare una proprietà di un criterio, inserire il nome della proprietà all'interno di parentesi graffe doppie, ad esempio `{{ContosoHeader}}`, come illustrato nell'esempio seguente.

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

In questo esempio, la proprietà `ContosoHeader` viene usata come nome di un'intestazione in un criterio `set-header` e la proprietà `ContosoHeaderValue` viene usata come valore di tale intestazione. Quando questo criterio viene valutato durante una richiesta o una risposta al gateway di Gestione API, `{{ContosoHeader}}` e `{{ContosoHeaderValue}}` vengono sostituiti dai valori delle rispettive proprietà.

Le proprietà possono essere usate come valori completi di attributo o di elemento, come illustrato nell'esempio precedente, ma possono anche essere inserite all'interno di un'espressione di testo o combinate con una parte di un'espressione di testo, come illustrato nell'esempio seguente: `<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Le proprietà possono anche contenere espressioni di criteri. Nell'esempio seguente viene usata la proprietà `ExpressionProperty` .

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

Quando questo criterio viene valutato, `{{ExpressionProperty}}` viene sostituito dal valore corrispondente: `@(DateTime.Now.ToString())`. Poiché il valore è un'espressione di criteri, l'espressione viene valutata e il criterio passa alla fase di esecuzione.

È possibile eseguire un test nel portale per sviluppatori chiamando un'operazione il cui ambito contiene un criterio con proprietà. Nell'esempio seguente viene chiamata un'operazione che contiene i due criteri di esempio precedenti `set-header` con proprietà. Si noti che la risposta contiene due intestazioni personalizzate configurate tramite criteri con proprietà.

![Portale per sviluppatori][api-management-send-results]

Se si osserva la [traccia di Controllo API](api-management-howto-api-inspector.md) relativa a una chiamata che include i due criteri con proprietà degli esempi precedenti, è possibile vedere i due criteri `set-header` con i valori delle proprietà inseriti, nonché la valutazione delle espressioni dei criteri per la proprietà che contiene l'espressione.

![Traccia di Controllo API][api-management-api-inspector-trace]

Si noti che mentre i valori delle proprietà possono contenere espressioni di criteri, i valori delle proprietà non possono contenere altre proprietà. Se il testo che contiene un riferimento a una proprietà viene usato come valore di una proprietà, ad esempio `Property value text {{MyProperty}}`, tale riferimento alla proprietà non viene sostituito e viene incluso come parte del valore della proprietà.

## <a name="to-create-a-property"></a>Per creare una proprietà
Per creare una proprietà, fare clic su **Aggiungi proprietà** nella scheda **Proprietà**.

![Aggiungi proprietà][api-management-properties-add-property-menu]

**Nome** e **Valore** sono obbligatori. Se il valore della proprietà è un segreto, selezionare la casella di controllo **This is a secret** . Immettere uno o più tag facoltativi per organizzare le proprietà, quindi fare clic su **Save**.

![Aggiungi proprietà][api-management-properties-add-property]

Quando si salva una nuova proprietà, la casella di testo di **ricerca di proprietà** viene popolata con il nome della nuova proprietà e la nuova proprietà viene visualizzata. Per visualizzare tutte le proprietà, cancellare il contenuto della casella di testo di **ricerca di proprietà** e premere INVIO.

![Properties][api-management-properties-property-saved]

Per informazioni sulla creazione di una proprietà tramite l'API REST, vedere la [pagina relativa alla creazione di una proprietà tramite l'API REST](https://msdn.microsoft.com/library/azure/mt651775.aspx#Put).

## <a name="to-edit-a-property"></a>Per modificare una proprietà
Per modificare una proprietà, fare clic su **Edit** accanto alla proprietà da modificare.

![Modifica proprietà][api-management-properties-edit]

Apportare le modifiche desiderate e fare clic su **Save**. Se si modifica il nome della proprietà, tutti i criteri che fanno riferimento a tale proprietà vengono aggiornati automaticamente con il nuovo nome.

![Modifica proprietà][api-management-properties-edit-property]

Per informazioni sulla modifica di una proprietà tramite l'API REST, vedere la [pagina relativa alla modifica di una proprietà tramite l'API REST](https://msdn.microsoft.com/library/azure/mt651775.aspx#Patch).

## <a name="to-delete-a-property"></a>Per eliminare una proprietà
Per eliminare una proprietà, fare clic su **Delete** accanto alla proprietà da eliminare.

![Elimina proprietà][api-management-properties-delete]

Fare clic su **Sì, elimina** per confermare.

![Conferma dell'eliminazione][api-management-delete-confirm]

> [!IMPORTANT]
> Se all'interno di almeno un criterio si fa riferimento alla proprietà, sarà possibile eliminare quest'ultima solo dopo averla rimossa da tutti i criteri che la usano.
> 
> 

Per informazioni sull'eliminazione di una proprietà tramite l'API REST, vedere la [pagina relativa all'eliminazione di una proprietà tramite l'API REST](https://msdn.microsoft.com/library/azure/mt651775.aspx#Delete).

## <a name="to-search-and-filter-properties"></a>Per cercare e filtrare proprietà
La scheda **Properties** include funzionalità di ricerca e filtro che consentono di gestire le proprietà. Per filtrare l'elenco delle proprietà in base al nome di queste, immettere un termine di ricerca nella casella di testo di **ricerca di proprietà** . Per visualizzare tutte le proprietà, cancellare il contenuto della casella di testo di **ricerca di proprietà** e premere INVIO.

![Search][api-management-properties-search]

Per filtrare l'elenco delle proprietà in base al valore dei tag, immettere uno o più tag nella casella di testo di **filtro in base ai tag** . Per visualizzare tutte le proprietà, cancellare il contenuto della casella di testo di **filtro in base ai tag** e premere INVIO.

![Filtro][api-management-properties-filter]

## <a name="next-steps"></a>Passaggi successivi
* Ulteriori informazioni sull'uso dei criteri
  * [Criteri in Gestione API](api-management-howto-policies.md)
  * [Riferimento ai criteri](https://msdn.microsoft.com/library/azure/dn894081.aspx)
  * [Espressioni di criteri](https://msdn.microsoft.com/library/azure/dn910913.aspx)

## <a name="watch-a-video-overview"></a>Guardare un video introduttivo
> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Use-Properties-in-Policies/player]
> 
> 

[api-management-properties]: ./media/api-management-howto-properties/api-management-properties.png
[api-management-properties-add-property]: ./media/api-management-howto-properties/api-management-properties-add-property.png
[api-management-properties-edit-property]: ./media/api-management-howto-properties/api-management-properties-edit-property.png
[api-management-properties-add-property-menu]: ./media/api-management-howto-properties/api-management-properties-add-property-menu.png
[api-management-properties-property-saved]: ./media/api-management-howto-properties/api-management-properties-property-saved.png
[api-management-properties-delete]: ./media/api-management-howto-properties/api-management-properties-delete.png
[api-management-properties-edit]: ./media/api-management-howto-properties/api-management-properties-edit.png
[api-management-delete-confirm]: ./media/api-management-howto-properties/api-management-delete-confirm.png
[api-management-properties-search]: ./media/api-management-howto-properties/api-management-properties-search.png
[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png


