<properties 
	pageTitle="Come gestire gli account per sviluppatore in Gestione API di Azure" 
	description="Informazioni su come creare o invitare gli sviluppatori in Gestione API di Azure." 
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

# Come gestire gli account per sviluppatore in Gestione API di Azure

In Gestione API (anteprima) gli sviluppatori sono gli utenti delle API esposte con Gestione API. In questa guida viene illustrato come creare e invitare gli sviluppatori a usare le API e i prodotti resi disponibili con l'istanza di Gestione API.

## Contenuto dell'argomento

-   [Creare un nuovo sviluppatore][Creare un nuovo sviluppatore]
-   [Invitare uno sviluppatore][Invitare uno sviluppatore]
-   [Disattivare o riattivare un account sviluppatore][Disattivare o riattivare un account sviluppatore]
-   [Passaggi successivi][Passaggi successivi]

## <a name="create-developer"> </a>Creare un nuovo sviluppatore

Per creare un nuovo sviluppatore, fare clic sulla **Console di gestione** nel portale di Azure per il servizio Gestione API. Verrà visualizzato il portale amministrativo di Gestione API.

> Se non è stato creata un'istanza del servizio Gestione API, vedere [Creare un'istanza del servizio Gestione API][Creare un'istanza del servizio Gestione API] nell'esercitazione [Introduzione a Gestione API di Azure][Introduzione a Gestione API di Azure].

![API Management console][API Management console]

Fare clic su **Sviluppatori** dal menu **Gestione API** sulla sinistra, quindi scegliere **aggiungi utente**.

![Create developer][Create developer]

Immettere **Indirizzo di posta elettronica**, **Password** e **Nome** per il nuovo sviluppatore e fare clic su **Salva**.

![Create developer][1]

Per impostazione predefinita, i nuovi account sviluppatore creati sono **attivi** e associati al gruppo **Sviluppatori**.

![New developer][New developer]

Gli account sviluppatore con stato **attivo** possono essere usati per accedere a tutte le API per cui dispongono di sottoscrizioni. Per associare il nuovo sviluppatore creato ad altri gruppi, vedere [Come associare i gruppi agli sviluppatori][Come associare i gruppi agli sviluppatori].

## <a name="invite-developer"> </a>Invitare uno sviluppatore

Per invitare uno sviluppatore, fare clic su **Sviluppatori** dal menu **Gestione API** sulla sinistra, quindi scegliere **Invita utente**.

![Invite developer][Invite developer]

Immettere il nome e l'indirizzo di posta elettronica e fare clic su **Invita**.

![Invite developer][2]

Viene visualizzato un messaggio di conferma, ma il nuovo sviluppatore invitato non appare nell'elenco finché l'invito non viene accettato.

![Invite confirmation][Invite confirmation]

> Quando uno sviluppatore viene invitato, gli viene inviato un messaggio di posta elettronica generato con un modello e personalizzabile. Per altre informazioni, vedere [Configurare modelli di posta elettronica][Configurare modelli di posta elettronica].

Una volta accettato l'invito, l'account diventa attivo.

## <a name="block-developer"> </a>Disattivare o riattivare un account sviluppatore

Per impostazione predefinita, un nuovo account sviluppatore creato o invitato è **Attivo**. Per disattivare un account sviluppatore, fare clic su **Blocca**. Per riattivare un account sviluppatore bloccato, fare clic su **Attiva**. Un account sviluppatore bloccato non può accedere al portale per sviluppatori né chiamare le API.

![Block developer][New developer]

## <a name="next-steps"> </a>Passaggi successivi

Dopo aver creato un account sviluppatore, è possibile associarlo ai ruoli ed effettuarne la sottoscrizione a prodotti e API. Per altre informazioni, vedere [Come creare e usare i gruppi][Come creare e usare i gruppi].

  [Creare un nuovo sviluppatore]: #create-developer
  [Invitare uno sviluppatore]: #invite-developer
  [Disattivare o riattivare un account sviluppatore]: #block-developer
  [Passaggi successivi]: #next-steps
  [Creare un'istanza del servizio Gestione API]: ../api-management-get-started/#create-service-instance
  [Introduzione a Gestione API di Azure]: ../api-management-get-started
  [API Management console]: ./media/api-management-howto-create-or-invite-developers/api-management-management-console.png
  [Create developer]: ./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png
  [1]: ./media/api-management-howto-create-or-invite-developers/api-management-add-new-user.png
  [New developer]: ./media/api-management-howto-create-or-invite-developers/api-management-new-developer.png
  [Come associare i gruppi agli sviluppatori]: ../api-management-howto-create-groups/#associate-group-developer
  [Invite developer]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer.png
  [2]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-window.png
  [Invite confirmation]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-confirmation.png
  [Configurare modelli di posta elettronica]: ../api-management-howto-configure-notifications/#email-templates
  [Come creare e usare i gruppi]: ../api-management-howto-create-groups

<!--HONumber=46--> 

<!--HONumber=46--> 
 