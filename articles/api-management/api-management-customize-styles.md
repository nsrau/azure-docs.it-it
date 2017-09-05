---
title: Personalizzare gli stili nel portale per sviluppatori in Gestione API di Azure | Microsoft Docs
description: Informazioni su come modificare gli stili usati in una pagina del portale per sviluppatori in Gestione API di Azure.
services: api-management
documentationcenter: 
author: vladvino
manager: vlvinogr
editor: 
ms.assetid: 186128fe-41c0-4efb-9efe-2478ad4d103f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/09/2017
ms.author: antonba
ms.translationtype: Human Translation
ms.sourcegitcommit: 336d4f80f0357796fb29eb9314c11edfce831a69
ms.openlocfilehash: bd08eb476a4bd7298c5650977b88ba0b24deddec
ms.contentlocale: it-it
ms.lasthandoff: 02/23/2017

---
# <a name="customize-the-styling-of-the-developer-portal-in-azure-api-management"></a>Personalizzare gli stili nel portale per sviluppatori in Gestione API di Azure
Per personalizzare il portale per sviluppatori in Gestione API di Azure ci sono tre modalità principali:

* [Modificare i contenuti di pagine statiche e gli elementi di layout di pagina][modify-content-layout]
* [Aggiornare gli stili usati per gli elementi della pagina nel portale per sviluppatori][customize-styles] (illustrato in questa guida)
* [Modificare i modelli usati per le pagine generate dal portale][portal-templates] (ad esempio documenti API, prodotti, autenticazione utente e così via)

## <a name="change-headers-styling"> </a> Modificare lo stile degli elementi di una pagina

I colori, il tipo di carattere e le dimensioni, la spaziatura e altri elementi relativi allo stile di ogni pagina del portale sono definiti da regole di stile. 

La modifica delle regole di stile viene eseguita dal **portale per sviluppatori** quando vi si accede come amministratore. Per accedervi, aprire prima il Portale di Azure e fare clic su **Portale di pubblicazione** nella barra degli strumenti del servizio dell'istanza di Gestione API.

![Portale di pubblicazione][api-management-management-console]

Quindi fare clic su **Portale per sviluppatori** in alto a destra. 

![Collegamento al Portale per sviluppatori nel portale di pubblicazione][api-management-pp-dp-link]

Per aprire la barra degli strumenti per la personalizzazione spostare il mouse sull'icona della personalizzazione (o selezionarla) e quindi fare clic su "stili" dalla barra degli strumenti.

![Pulsante della barra degli strumenti di personalizzazione][api-management-customization-toolbar-button]

È possibile modificare le regole di stile in due modi principali: esaminare l'elenco di tutte le regole di stile ovunque venga visualizzato per impostazione predefinita e modificare uno stile in base alla necessità oppure scegliere **Select an element on the page** (Selezionare un elemento nella pagina) e quindi fare clic in un punto qualsiasi della pagina per visualizzare solo gli stili per l'elemento specifico.

![Barra degli strumenti Personalizzazione][api-management-customization-toolbar]

Fare clic sull'opzione **Select an element on the page** (Selezionare un elemento nella pagina) per questo esempio.  Man mano che il puntatore del mouse passa sugli elementi, questi vengono evidenziati per indicare gli stili dell'elemento che si possono modificare facendo clic su di essi. Spostare il mouse sul testo nell'intestazione (in genere qui è presente il nome della società) e quindi fare clic. Nell'editor dello stile viene visualizzato un set di regole di stile denominate e categorizzate. Ogni regola rappresenta una proprietà di stile dell'elemento selezionato. Ad esempio, per il testo dell'intestazione selezionata sopra, la dimensione del testo è pari a @font-size-h1, mentre il nome del tipo di carattere con le alternative si trova in @headings-font-family.

> Se si ha familiarità con [Bootstrap][bootstrap], queste regole sono in effetti [variabili LESS][LESS variables] nel tema bootstrap usato dal portale per sviluppatori.
> 
> 

A questo punto, verrà modificato il colore del testo dell'intestazione. Selezionare la voce nel campo **@headings-color** e digitare **#000000**. Questo è il codice esadecimale per il colore nero. Durante l'operazione, viene visualizzato un indicatore di colore quadrato all'estremità della casella di testo. Se si fa clic su questo indicatore, viene visualizzato uno strumento di selezione dei colori che consente di scegliere un colore.

![Selezione colori][api-management-customization-toolbar-color-picker]

L'anteprima delle modifiche viene visualizzata in tempo reale durante l'inserimento delle modifiche, che risultano tuttavia visibili solo agli amministratori. Per rendere queste modifiche visibili a tutti, fare clic sul pulsante **Pubblica** nell'editor dello stile e confermare le modifiche.

![Menu Pubblica][api-management-customization-toolbar-publish-form]

> Per modificare le regole di stile applicate a qualsiasi altro elemento della pagina, seguire la stessa procedura usata per l'intestazione. Fare clic su **Select an element on the page** (Selezionare un elemento nella pagina) nell'editor di stile, selezionare l'elemento desiderato e iniziare a modificare i valori delle regole di stile mostrati sullo schermo.
> 
> 


## <a name="next-steps"> </a>Passaggi successivi
* Informazioni su come personalizzare il contenuto delle pagine del portale per sviluppatori usando i [modelli del portale per sviluppatori](api-management-developer-portal-templates.md).

[Change the styling of the headers]: #change-headers-styling
[Next steps]: #next-steps

[Azure Classic Portal]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-customize-styles/api-management-management-console.png
[api-management-pp-dp-link]: ./media/api-management-customize-styles/api-management-pp-dp-link.png
[api-management-customization-toolbar-button]: ./media/api-management-customize-styles/api-management-customization-toolbar-button.png
[api-management-customization-toolbar]: ./media/api-management-customize-styles/api-management-customization-toolbar.png
[api-management-customization-toolbar-color-picker]: ./media/api-management-customize-styles/api-management-customization-toolbar-color-picker.png
[api-management-customization-toolbar-publish-form]: ./media/api-management-customize-styles/api-management-customization-toolbar-publish-form.png

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[bootstrap]: http://getbootstrap.com/
[LESS variables]: http://getbootstrap.com/css/

