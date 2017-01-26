---
title: Come personalizzare il portale per sviluppatori di Gestione API di Azure con i modelli | Microsoft Docs
description: Informazioni su come personalizzare il portale per sviluppatori di Gestione API di Azure usando i modelli.
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: a195675b-f7d0-4fc9-90bf-860e6f17ccf7
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apipm
translationtype: Human Translation
ms.sourcegitcommit: 77fd7b5b339a8ede8a297bec96f91f0a243cc18d
ms.openlocfilehash: 9ddd871a845af1169337480804b216a9a4fc9a06


---
# <a name="how-to-customize-the-azure-api-management-developer-portal-using-templates"></a>Come personalizzare il portale per sviluppatori di Gestione API di Azure con i modelli
Gestione API di Azure offre diverse funzionalità di personalizzazione per consentire agli amministratori di [personalizzare l'aspetto del portale per sviluppatori](api-management-customize-portal.md)e il contenuto delle relative pagine usando un set di modelli che permettono di configurare il contenuto delle pagine stesse. La sintassi [DotLiquid](http://dotliquidmarkup.org/) , insieme a un set fornito di risorse stringa localizzate, icone e controlli di pagina, offre grande flessibilità nella configurazione del contenuto delle pagine con questi modelli.

## <a name="developer-portal-templates-overview"></a>Panoramica sui modelli del portale per sviluppatori
I modelli del portale per sviluppatori vengono gestiti nel portale dagli amministratori dell'istanza del servizio Gestione API. Per gestire i modelli di sviluppo, passare all'istanza del servizio Gestione API nel portale di Azure classico e fare clic su **Portale per sviluppatori** nella barra degli strumenti.

![Portale per sviluppatori][api-management-browse]

Se si è già nel portale di pubblicazione, è possibile accedere al **portale per sviluppatori**facendo clic sul relativo collegamento.

![Menu del portale per sviluppatori][api-management-developer-portal-menu]

Per accedere ai modelli del portale per sviluppatori, fare clic sull'icona di personalizzazione a sinistra per visualizzare il menu di personalizzazione e quindi fare clic su **Templates**.

![Modelli del portale per sviluppatori][api-management-customize-menu]

L'elenco dei modelli include varie categorie di modelli che coprono le diverse pagine del portale per sviluppatori. Ogni modello è diverso, ma i passaggi per modificarli e pubblicare le modifiche sono uguali per tutti. Per modificare un modello, fare clic sul nome del modello.

![Modelli del portale per sviluppatori][api-management-templates-menu]

Facendo clic su un modello si passa alla pagina del portale per sviluppatori personalizzabile con il modello selezionato. In questo esempio viene visualizzato il modello **Product list** . Il modello **Product list** controlla l'area dello schermo indicata dal rettangolo rosso. 

![Modello Product list][api-management-developer-portal-templates-overview]

Alcuni modelli, come i modelli **User Profile** , permettono di personalizzare parti diverse della stessa pagina. 

![Modelli User Profile][api-management-user-profile-templates]

L'editor di ogni modello del portale per sviluppatori include due sezioni visualizzate nella parte inferiore della pagina. A sinistra è visualizzato il riquadro di modifica per il modello, mentre a destra è visualizzato il relativo modello di dati. 

Il riquadro di modifica del modello contiene il markup che controlla l'aspetto e il comportamento della pagina corrispondente nel portale per sviluppatori. Il markup del modello usa la sintassi [DotLiquid](http://dotliquidmarkup.org/) . Uno degli editor più diffusi per DotLiquid è [DotLiquid for Designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers). Tutte le modifiche apportate al modello in fase di modifica vengono visualizzate in tempo reale nel browser, ma non sono visibili ai clienti finché non si sceglie di [salvare](#to-save-a-template) e [pubblicare](#to-publish-a-template) il modello.

![Markup del modello][api-management-template]

Il riquadro **Template data** fornisce una guida al modello di dati per le entità disponibili per l'uso in un determinato modello. La guida consiste nella possibilità di visualizzare i dati attivi attualmente visualizzati nel portale per sviluppatori. Per espandere i riquadri del modello è possibile fare clic sul rettangolo nell'angolo superiore destro del riquadro **Template data** .

![Modello Template data][api-management-template-data]

Nell'esempio precedente sono disponibili due prodotti visualizzati nel portale per gli sviluppatori che sono stati recuperati dai dati visualizzati nel riquadro **Template data** , come illustrato nell'esempio seguente.

```json
{
    "Paging": {
        "Page": 1,
        "PageSize": 10,
        "TotalItemCount": 2,
        "ShowAll": false,
        "PageCount": 1
    },
    "Filtering": {
        "Pattern": null,
        "Placeholder": "Search products"
    },
    "Products": [
        {
            "Id": "56ec64c380ed850042060001",
            "Title": "Starter",
            "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",
            "Terms": "",
            "ProductState": 1,
            "AllowMultipleSubscriptions": false,
            "MultipleSubscriptionsCount": 1
        },
        {
            "Id": "56ec64c380ed850042060002",
            "Title": "Unlimited",
            "Description": "Subscribers have completely unlimited access to the API. Administrator approval is required.",
            "Terms": null,
            "ProductState": 1,
            "AllowMultipleSubscriptions": false,
            "MultipleSubscriptionsCount": 1
        }
    ]
}
```

Il markup del modello **Product list** elabora i dati per fornire l'output previsto scorrendo la raccolta di prodotti per visualizzare le relative informazioni e un collegamento a ogni singolo prodotto. Si notino gli elementi `<search-control>` e `<page-control>` nel markup. Questi controllano la visualizzazione dei controlli di ricerca e di paging nella pagina. `ProductsStrings|PageTitleProducts` è un riferimento a una stringa localizzata che contiene il testo dell'intestazione `h2` della pagina. Per un elenco delle risorse stringa, i controlli di pagina e le icone disponibili per l'uso nei modelli del portale per sviluppatori, vedere il [riferimento ai modelli del portale per sviluppatori di Gestione API](api-management-developer-portal-templates-reference.md).

```html
<search-control></search-control>
<div class="row">
    <div class="col-md-9">
        <h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>
    </div>
</div>
<div class="row">
    <div class="col-md-12">
    {% if products.size > 0 %}
    <ul class="list-unstyled">
    {% for product in products %}
        <li>
            <h3><a href="/products/{{product.id}}">{{product.title}}</a></h3>
            {{product.description}}
        </li>    
    {% endfor %}
    </ul>
    <paging-control></paging-control>
    {% else %}
    {% localized "CommonResources|NoItemsToDisplay" %}
    {% endif %}
    </div>
</div>
```

## <a name="to-save-a-template"></a>Per salvare un modello
Per salvare un modello, fare clic sull'icona di salvataggio nell'editor del modello.

![Salvare il modello][api-management-save-template]

Le modifiche salvate non sono attive nel portale per sviluppatori fino a quando non vengono pubblicate.

## <a name="to-publish-a-template"></a>Per pubblicare un modello
I modelli salvati possono essere pubblicati singolarmente o tutti insieme. Per pubblicare un singolo modello, fare clic sull'icona di pubblicazione nell'editor del modello.

![Pubblicare il modello][api-management-publish-template]

Fare clic su **Yes** per confermare e rendere il modello attivo nel portale per sviluppatori.

![Confermare la pubblicazione][api-management-publish-template-confirm]

Per pubblicare tutte le versioni dei modelli non ancora pubblicate, fare clic su **Publish** nell'elenco dei modelli. I modelli non pubblicati sono contraddistinti da un asterisco dopo il nome del modello. In questo esempio vengono pubblicati i modelli **Product list** (Elenco prodotti) e **Product** (Prodotto).

![Pubblicare i modelli][api-management-publish-templates]

Fare clic su **Publish customizations** per confermare.

![Confermare la pubblicazione][api-management-publish-customizations]

I modelli appena pubblicati diventano immediatamente disponibili nel portale per sviluppatori.

## <a name="to-revert-a-template-to-the-previous-version"></a>Per annullare la modifiche alla versione precedente di un modello
Per annullare la modifiche alla versione pubblicata precedente di un modello, fare clic sull'icona di annullamento nell'editor del modello.

![Annullare le modifiche al modello][api-management-revert-template]

Fare clic su **Yes** per confermare.

![Confirm][api-management-revert-template-confirm]

Al termine dell'operazione di annullamento, la versione pubblicata precedente del modello sarà disponibile nel portale per sviluppatori.

## <a name="to-restore-a-template-to-the-default-version"></a>Per ripristinare la versione predefinita di un modello
Il ripristino della versione predefinita di un modello è un processo in due passaggi. Occorre prima di tutto ripristinare i modelli e quindi pubblicare le versioni ripristinate.

Per ripristinare la versione predefinita di un singolo modello, fare clic sull'icona di ripristino nell'editor del modello.

![Annullare le modifiche al modello][api-management-reset-template]

Fare clic su **Yes** per confermare.

![Confirm][api-management-reset-template-confirm]

Per ripristinare le versioni predefinite di tutti i modelli, fare clic su **Restore default templates** nell'elenco dei modelli.

![Ripristinare i modelli][api-management-restore-templates]

I modelli ripristinati devono poi essere pubblicati singolarmente o tutti insieme seguendo i passaggi descritti nella sezione [Per pubblicare un modello](#to-publish-a-template).

## <a name="next-steps"></a>Passaggi successivi
Per informazioni di riferimento sui modelli del portale per sviluppatori, le risorse stringa, le icone e i controlli di pagina, vedere il [riferimento ai modelli del portale per sviluppatori di Gestione API](api-management-developer-portal-templates-reference.md).


[api-management-customize-menu]: ./media/api-management-developer-portal-templates/api-management-customize-menu.png
[api-management-templates-menu]: ./media/api-management-developer-portal-templates/api-management-templates-menu.png
[api-management-developer-portal-templates-overview]: ./media/api-management-developer-portal-templates/api-management-developer-portal-templates-overview.png
[api-management-template]: ./media/api-management-developer-portal-templates/api-management-template.png
[api-management-template-data]: ./media/api-management-developer-portal-templates/api-management-template-data.png
[api-management-developer-portal-menu]: ./media/api-management-developer-portal-templates/api-management-developer-portal-menu.png
[api-management-browse]: ./media/api-management-developer-portal-templates/api-management-browse.png
[api-management-user-profile-templates]: ./media/api-management-developer-portal-templates/api-management-user-profile-templates.png
[api-management-save-template]: ./media/api-management-developer-portal-templates/api-management-save-template.png
[api-management-publish-template]: ./media/api-management-developer-portal-templates/api-management-publish-template.png
[api-management-publish-template-confirm]: ./media/api-management-developer-portal-templates/api-management-publish-template-confirm.png
[api-management-publish-templates]: ./media/api-management-developer-portal-templates/api-management-publish-templates.png
[api-management-publish-customizations]: ./media/api-management-developer-portal-templates/api-management-publish-customizations.png
[api-management-revert-template]: ./media/api-management-developer-portal-templates/api-management-revert-template.png
[api-management-revert-template-confirm]: ./media/api-management-developer-portal-templates/api-management-revert-template-confirm.png
[api-management-reset-template]: ./media/api-management-developer-portal-templates/api-management-reset-template.png
[api-management-reset-template-confirm]: ./media/api-management-developer-portal-templates/api-management-reset-template-confirm.png
[api-management-restore-templates]: ./media/api-management-developer-portal-templates/api-management-restore-templates.png










<!--HONumber=Jan17_HO2-->


