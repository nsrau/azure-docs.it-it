<properties 
	pageTitle="Come personalizzare l'aspetto del portale per sviluppatori in Gestione API di Azure" 
	description="Informazioni su come personalizzare l'aspetto del portale per sviluppatori in Gestione API di Azure." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/18/2014" 
	ms.author="sdanie"/>

# Come personalizzare l'aspetto del portale per sviluppatori in Gestione API di Azure

I colori, il tipo di carattere, le dimensioni, la spaziatura e altri elementi relativi all'aspetto del portale per sviluppatori sono definiti da regole di stile. Per ogni elemento strutturale di una pagina sono disponibili set di regole, ad esempio per l'intestazione, il menu, il corpo del contenuto, il titolo della pagina e così via. Questa procedura illustra come modificare le regole di stile.

Per modificare le regole di stile, nel portale di pubblicazione scegliere **Aspetto** dal menu **Portale per sviluppatori**. Fare clic su **Inizia personalizzazione** per abilitare l'editor dello stile.

Il browser passerà a una pagina nascosta nel portale per sviluppatori contenente esempi di contenuto ed esempi per tutte le regole di stile usate in qualsiasi parte del sito. Per aprire l'editor dello stile, spostare il cursore sulla riga verticale sottile nella parte all'estrema sinistra della pagina. Verrà visualizzata la barra degli strumenti dell'editor

![Barra degli strumenti Personalizzazione][Barra degli strumenti Personalizzazione]

Sono disponibili due modalità principali per la modifica delle regole di stile: **Modifica tutte le regole** consente di visualizzare un elenco di tutte le regole di stile usate nel sito, mentre **Seleziona elemento** consente di selezionare un elemento nella pagina e visualizzare gli stili solo per quell'elemento.

In questa sezione verrà modificato lo stile di un solo elemento specifico, ad esempio le intestazioni di pagina. Fare clic sull'opzione **Seleziona elemento** sulla barra degli strumenti dell'editor dello stile e quindi fare clic su **Seleziona un elemento da personalizzare**. Man mano che il puntatore passa sugli elementi, questi verranno evidenziati per indicare gli stili dell'elemento che si possono modificare facendo clic su di essi.

Passare il puntatore del mouse sul testo che rappresenta il titolo della pagina nell'intestazione e fare clic su di esso. Nell'editor dello stile verrà visualizzato un set di regole di stile denominate e categorizzate.

Ogni regola rappresenta una proprietà di stile dell'elemento selezionato. Ad esempio, per il testo dell'intestazione selezionata sopra, la dimensione del testo è pari a @font-size-h1 mentre il nome del tipo di carattere con le alternative si trova in @headings-font-family.

> Se si ha familiarità con [bootstrap][<http://getbootstrap.com/>], queste regole sono in effetti [variabili LESS][<http://getbootstrap.com/css/>] all'interno del tema bootstrap usato dal portale per sviluppatori.

A questo punto, verrà modificato il colore del testo dell'intestazione. Selezionare la voce nel campo **@headings-color** e digitare #000000. Questo è il codice esadecimale per il colore nero. Durante l'operazione, verrà visualizzato un indicatore di colore quadrato all'estremità della casella di testo. Se si fa clic su questo indicatore, verrà visualizzato uno strumento di selezione dei colori che consente di scegliere un colore.

![Selezione colori][Selezione colori]

Dopo avere apportato le modifiche desiderate agli stili dell'elemento selezionato, fare clic su **Anteprima modifiche** per visualizzare i risultati sullo schermo. Al momento, le modifiche sono visibili solo agli amministratori. Per rendere queste modifiche visibili a tutti, fare clic sul pulsante **Pubblica** nell'editor dello stile e confermare le modifiche.

![Modulo di pubblicazione][Modulo di pubblicazione]

> Per modificare le regole di stile per qualsiasi altro elemento nella pagina, attenersi alla stessa procedura usata per l'intestazione, ovvero fare clic su **Seleziona elemento** nell'edito dello stile, selezionare l'elemento desiderato e iniziare a modificare i valori delle regole di stile visualizzate sullo schermo.

  [Barra degli strumenti Personalizzazione]: ./media/api-management-howto-customize-look-and-feel/api-management-customization-toolbar.png
  [Selezione colori]: ./media/api-management-howto-customize-look-and-feel/api-management-customization-toolbar-color-picker.png
  [Modulo di pubblicazione]: ./media/api-management-howto-customize-look-and-feel/api-management-customization-toolbar-publish-form.png

<!--HONumber=46--> 

<!--HONumber=46--> 
