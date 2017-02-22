---
title: Personalizzare il portale per sviluppatori in Gestione API di Azure | Microsoft Docs
description: Informazioni su come personalizzare il portale per sviluppatori in Gestione API di Azure.
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 186128fe-41c0-4efb-9efe-2478ad4d103f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/15/2016
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 13431648e87d085161ad278dc991d49f7872be34
ms.openlocfilehash: 60213f885020a5ba36d6ada0812f755e06b3c48b


---
# <a name="customize-the-developer-portal-in-azure-api-management"></a>Personalizzare il portale per sviluppatori in Gestione API di Azure
Questa guida descrive come modificare l'aspetto del portale per sviluppatori in Gestione API di Azure per mantenere l'uniformità con il proprio marchio.

## <a name="change-page-headers"> </a>Modificare il testo o il logo nelle intestazioni di pagina
Una delle funzionalità principali della personalizzazione del portale è la sostituzione del testo nella parte superiore di tutte le pagine con il nome o il logo dell'azienda.

È possibile modificare il contenuto nel portale per sviluppatori tramite il portale di pubblicazione, accessibile tramite il Portale di Azure. Per accedervi, fare clic su **Publisher portal** (Portale di pubblicazione) nella barra degli strumenti del servizio dell'istanza di Gestione API.

![Portale di pubblicazione][api-management-management-console]

Il portale per sviluppatori è basato su un sistema di gestione dei contenuti o CMS (Content Management System). L'intestazione visualizzata in ogni pagina è un tipo speciale di contenuto noto come widget. Per modificare il contenuto del widget, scegliere **Widget** dal menu **Portale per sviluppatori** a sinistra e quindi selezionare il widget **Intestazione** nell'elenco.

![Widget Intestazione][api-management-widgets-header]

I contenuti dell'intestazione sono modificabili dal campo **Corpo** . Sostituire il testo con "Portale per sviluppatori Fabrikam" e quindi fare clic su **Salva** nella parte inferiore della pagina.

A questo punto, la nuova intestazione dovrebbe essere visualizzata in ogni pagina del portale per sviluppatori.

> Per aprire il **portale per sviluppatori** dal portale di pubblicazione, fare clic sull'opzione corrispondente sulla barra superiore.
> 
> 

## <a name="change-headers-styling"> </a>Modificare lo stile delle intestazioni
I colori, il tipo di carattere e le dimensioni, la spaziatura e altri elementi relativi allo stile di ogni pagina del portale sono definiti da regole di stile. Per modificare gli stili, nel **Portale per sviluppatori** aprire la barra degli strumenti di personalizzazione a sinistra spostando l'icona di personalizzazione e quindi selezionare "stili" sulla barra degli strumenti.

![Pulsante della barra degli strumenti di personalizzazione][api-management-customization-toolbar-button]

È possibile modificare le regole di stile in due modi principali: esaminare l'elenco di tutte le regole di stile ovunque venga visualizzato per impostazione predefinita e modificare uno stile in base alla necessità oppure scegliere **Select an element on the page** (Selezionare un elemento nella pagina) e quindi fare clic in un punto qualsiasi della pagina per visualizzare solo gli stili per l'elemento specifico.

In questa sezione viene modificato solo lo stile delle intestazioni. Fare clic sull'opzione **Select an element on the page** (Selezionare un elemento nella pagina) sulla barra degli strumenti dell'editor dello stile. 

![Barra degli strumenti Personalizzazione][api-management-customization-toolbar]

Man mano che il puntatore del mouse passa sugli elementi, questi vengono evidenziati per indicare gli stili dell'elemento che si possono modificare facendo clic su di essi. Passare il mouse sul testo che indica il nome dell'azienda nell'intestazione ("Portale per sviluppatori Fabrikam" se si sono seguite le istruzioni fornite nella sezione precedente) e quindi fare clic su di esso. Nell'editor dello stile viene visualizzato un set di regole di stile denominate e categorizzate. Ogni regola rappresenta una proprietà di stile dell'elemento selezionato. Ad esempio, per il testo dell'intestazione selezionata sopra, la dimensione del testo è pari a @font-size-h1, mentre il nome del tipo di carattere con le alternative si trova in @headings-font-family.

> Se si ha familiarità con [Bootstrap][bootstrap], queste regole sono in effetti [variabili LESS][LESS variables] nel tema bootstrap usato dal portale per sviluppatori.
> 
> 

A questo punto, verrà modificato il colore del testo dell'intestazione. Selezionare la voce nel campo **@headings-color** e digitare **#000000**. Questo è il codice esadecimale per il colore nero. Durante l'operazione, viene visualizzato un indicatore di colore quadrato all'estremità della casella di testo. Se si fa clic su questo indicatore, viene visualizzato uno strumento di selezione dei colori che consente di scegliere un colore.

![Selezione colori][api-management-customization-toolbar-color-picker]

L'anteprima delle modifiche viene visualizzata in tempo reale durante l'inserimento delle modifiche, che risultano tuttavia visibili solo agli amministratori. Per rendere queste modifiche visibili a tutti, fare clic sul pulsante **Pubblica** nell'editor dello stile e confermare le modifiche.

![Menu Pubblica][api-management-customization-toolbar-publish-form]

> Per modificare le regole di stile applicate a qualsiasi altro elemento della pagina, seguire la stessa procedura usata per l'intestazione. A tale scopo, fare clic su **Seleziona elemento** nell'editor dello stile, selezionare l'elemento desiderato e iniziare a modificare i valori delle regole di stile sullo schermo.
> 
> 

## <a name="edit-page-contents"> </a>Modificare i contenuti di una pagina
Il portale per sviluppatori è costituito da pagine generate automaticamente come API, Prodotti, Applicazioni, Problemi e contenuto scritto manualmente. Poiché è basato su un sistema di gestione dei contenuti, è possibile creare il contenuto in base alle esigenze.

Per visualizzare l'elenco di tutte le pagine di contenuto esistenti, nel portale di pubblicazione scegliere **Contenuto** dal menu **Portale per sviluppatori**.

![Gestione del contenuto][api-management-customization-manage-content]

Fare clic nella pagina di **benvenuto** per modificare gli elementi visualizzati nella home page del portale per sviluppatori. Apportare le modifiche desiderate, visualizzarle in anteprima, se necessario, e quindi fare clic su **Pubblica** per renderle visibili a tutti.

> Per la home page viene usato un layout speciale che consente di visualizzare un banner nella parte superiore. Questo banner non è modificabile dalla sezione **Contenuto** . Per modificare il banner, scegliere **Widget** dal menu **Portale per sviluppatori**, selezionare **Home page** dall'elenco a discesa **Current Layer** (Livello corrente) e quindi aprire l'elemento **Banner** nella sezione **Featured section** (In primo piano). I contenuti di questo banner sono modificabili come quelli di ogni altra pagina.
> 
> 

## <a name="next-steps"> </a>Passaggi successivi
* Informazioni su come personalizzare il contenuto delle pagine del portale per sviluppatori usando i [modelli del portale per sviluppatori](api-management-developer-portal-templates.md).

[Change the text/logo in the page headers]: #change-page-headers
[Change the styling of the headers]: #change-headers-styling
[Edit the contents of a page]: #edit-page-contents
[Next steps]: #next-steps

[Azure Classic Portal]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-customize-portal/api-management-management-console.png
[api-management-widgets-header]: ./media/api-management-customize-portal/api-management-widgets-header.png
[api-management-customization-toolbar-button]: ./media/api-management-customize-portal/api-management-customization-toolbar-button.png
[api-management-customization-toolbar]: ./media/api-management-customize-portal/api-management-customization-toolbar.png
[api-management-customization-toolbar-color-picker]: ./media/api-management-customize-portal/api-management-customization-toolbar-color-picker.png
[api-management-customization-toolbar-publish-form]: ./media/api-management-customize-portal/api-management-customization-toolbar-publish-form.png
[api-management-customization-manage-content]: ./media/api-management-customize-portal/api-management-customization-manage-content.png


[bootstrap]: http://getbootstrap.com/
[LESS variables]: http://getbootstrap.com/css/



<!--HONumber=Feb17_HO1-->


