<properties pageTitle="Customizing the developer portal in Azure API Management" metaKeywords="" description="Customizing the developer portal in Azure API Management." metaCanonical="" services="" documentationCenter="API Management" title="Customizing the developer portal in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

# Personalizzazione del portale per sviluppatori in Gestione API di Azure

Questa guida descrive come modificare l'aspetto del portale per sviluppatori in Gestione API (Anteprima) per mantenere l'uniformità con il proprio marchio.

## Contenuto dell'argomento

-   [Modificare il testo/logo nelle intestazioni di pagina][Modificare il testo/logo nelle intestazioni di pagina]
-   [Modificare lo stile delle intestazioni][Modificare lo stile delle intestazioni]
-   [Modificare i contenuti di una pagina][Modificare i contenuti di una pagina]
-   [Passaggi successivi][Passaggi successivi]

## <a name="change-page-headers"> </a>Modificare il testo/logo nell'intestazione della pagina

Una delle funzionalità principali della personalizzazione del portale è la sostituzione del testo nella parte superiore di tutte le pagine con il nome o il logo dell'azienda.

È possibile modificare il contenuto nel portale per sviluppatori tramite il portale di pubblicazione, a cui si accede tramite il portale di gestione di Azure. Per passare alla console di Gestione API, fare clic su **Console di gestione** nel portale di Azure per il servizio Gestione API.

![Console di gestione][Console di gestione]

Il portale per sviluppatori è basato su un sistema di gestione dei contenuti, o CMS (Content Management System). L'intestazione visualizzata in ogni pagina è un tipo speciale di contenuto noto come widget. Per modificare i contenuti del widget, scegliere **Widget** dal menu **Portale per sviluppatori** a sinistra, quindi selezionare il widget **Intestazione** nell'elenco.

![Widget Intestazione][Widget Intestazione]

I contenuti dell'intestazione sono modificabili dal campo **Corpo**. Sostituire il testo con "Portale per sviluppatori Fabrikam" e fare clic su **Salva** nella parte inferiore della pagina.

A questo punto, la nuova intestazione dovrebbe essere visualizzata in ogni pagina del portale per sviluppatori.

> Per aprire il portale per sviluppatori dalla console di gestione, fare clic su **Portale per sviluppatori** nella barra superiore.

## <a name="change-headers-styling"> </a>Modificare lo stile delle intestazioni

I colori, il tipo di carattere e le dimensioni, la spaziatura e altri elementi relativi allo stile di ogni pagina del portale sono definiti da regole di stile. Per modificare gli stili, nel portale di pubblicazione scegliere **Aspetto** dal menu **Portale per sviluppatori**. Fare clic su **Inizia personalizzazione** per abilitare l'editor dello stile.

Il browser passerà a una pagina nascosta nel portale per sviluppatori contenente esempi di contenuto ed esempi per tutte le regole di stile usate in qualsiasi parte del sito. Per aprire l'editor dello stile, spostare il cursore sulla riga verticale sottile nella parte all'estrema sinistra della pagina. Verrà visualizzata la barra degli strumenti dell'editor

![Barra degli strumenti Personalizzazione][Barra degli strumenti Personalizzazione]

Sono disponibili due modalità principali per la modifica delle regole di stile: **Modifica tutte le regole** consente di visualizzare un elenco di tutte le regole di stile usate nel sito, mentre **Seleziona elemento** consente di selezionare un elemento nella pagina e visualizzare gli stili solo per quell'elemento.

In questa sezione verrà modificato solo lo stile delle intestazioni. Fare clic sull'opzione **Seleziona elemento** sulla barra degli strumenti dell'editor dello stile e quindi fare clic su **Seleziona un elemento da personalizzare**. Man mano che il puntatore passa sugli elementi, questi verranno evidenziati per indicare gli stili dell'elemento che si possono modificare facendo clic su di essi. Passare il mouse sul testo che indica il nome dell'azienda nell'intestazione ("Portale per sviluppatori Fabrikam" se si sono seguite le istruzioni fornite nella sezione precedente) e fare clic su di esso. Nell'editor dello stile verrà visualizzato un set di regole di stile denominate e categorizzate.

Ogni regola rappresenta una proprietà di stile dell'elemento selezionato. Ad esempio, per il testo dell'intestazione selezionata sopra, la dimensione del testo è pari a @font-size-h1 mentre il nome del tipo di carattere con le alternative si trova in @headings-font-family.

> Se si ha familiarità con [Bootstrap][Bootstrap], queste regole sono in effetti [variabili LESS][variabili LESS] all'interno del tema bootstrap usato dal portale per sviluppatori.

A questo punto, verrà modificato il colore del testo dell'intestazione. Selezionare la voce nel campo **@headings-color** e digitare \#000000. Questo è il codice esadecimale per il colore nero. Durante l'operazione, verrà visualizzato un indicatore di colore quadrato all'estremità della casella di testo. Se si fa clic su questo indicatore, verrà visualizzato uno strumento di selezione dei colori che consente di scegliere un colore.

![Selezione colori][Selezione colori]

Dopo avere apportato le modifiche desiderate agli stili dell'elemento selezionato, fare clic su **Anteprima modifiche** per visualizzare i risultati sullo schermo. Al momento, le modifiche sono visibili solo agli amministratori. Per rendere queste modifiche visibili a tutti, fare clic sul pulsante **Pubblica** nell'editor dello stile e confermare le modifiche.

![Menu Pubblica][Menu Pubblica]

> Per modificare le regole di stile per qualsiasi altro elemento nella pagina, attenersi alla stessa procedura usata per l'intestazione, ovvero fare clic su **Seleziona elemento** nell'edito dello stile, selezionare l'elemento desiderato e iniziare a modificare i valori delle regole di stile visualizzate sullo schermo.

## <a name="edit-page-contents"> </a>Modificare i contenuti di una pagina

Il portale per sviluppatori è costituito da pagine generate automaticamente come API, Prodotti, Applicazioni, Problemi e contenuto scritto manualmente. Poiché è basato su un sistema di gestione dei contenuti, è possibile creare i contenuti in base alle esigenze.

Per visualizzare l'elenco di tutte le pagine di contenuto esistenti, nella console di gestione scegliere **Contenuto** dal menu **Portale per sviluppatori**.

![Gestione del contenuto][Gestione del contenuto]

Fare clic nella pagina di benvenuto per modificare gli elementi visualizzati nella home page del portale per sviluppatori. Apportare le modifiche desiderate, visualizzarle in anteprima, se necessario, e quindi fare clic su **Pubblica** per renderle visibili a tutti.

> Per la home page viene usato un layout speciale che consente di visualizzare un banner nella parte superiore. Questo banner non è modificabile dalla sezione Contenuto. Per modificare il banner, scegliere **Widget** dal menu **Portale per sviluppatori**, quindi selezionare **Home page** dal menu a discesa **Livello corrente** e quindi aprire l'elemento **Banner** nella sezione In primo piano. I contenuti di questo banner sono modificabili come quelli di ogni altra pagina.

## <a name="next-steps"> </a>Passaggi successivi

-   Vedere gli altri argomenti nell'esercitazione [Introduzione alla configurazione API avanzata][Introduzione alla configurazione API avanzata].

  [Modificare il testo/logo nelle intestazioni di pagina]: #change-page-headers
  [Modificare lo stile delle intestazioni]: #change-headers-styling
  [Modificare i contenuti di una pagina]: #edit-page-contents
  [Passaggi successivi]: #next-steps
  [Console di gestione]: ./media/api-management-customize-portal/api-management-management-console.png
  [Widget Intestazione]: ./media/api-management-customize-portal/api-management-widgets-header.png
  [Barra degli strumenti Personalizzazione]: ./media/api-management-customize-portal/api-management-customization-toolbar.png
  [Bootstrap]: http://getbootstrap.com/
  [variabili LESS]: http://getbootstrap.com/css/
  [Selezione colori]: ./media/api-management-customize-portal/api-management-customization-toolbar-color-picker.png
  [Menu Pubblica]: ./media/api-management-customize-portal/api-management-customization-toolbar-publish-form.png
  [Gestione del contenuto]: ./media/api-management-customize-portal/api-management-customization-manage-content.png
  [Introduzione alla configurazione API avanzata]: ../api-management-get-started-advanced
