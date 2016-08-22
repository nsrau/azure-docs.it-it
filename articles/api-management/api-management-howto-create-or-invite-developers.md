<properties 
	pageTitle="Come gestire gli account utente in Gestione API di Azure | Microsoft Azure" 
	description="Informazioni su come creare o invitare gli utenti in Gestione API di Azure." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/09/2016" 
	ms.author="sdanie"/>  

# Come gestire gli account utente in Gestione API di Azure

In Gestione API gli sviluppatori sono gli utenti delle API esposte con Gestione API. In questa guida viene illustrato come creare e invitare gli sviluppatori a usare le API e i prodotti resi disponibili con l'istanza di Gestione API. Per informazioni sulla gestione degli account utente a livello di codice, vedere la documentazione [User entity (Entità utente)](https://msdn.microsoft.com/library/azure/dn776330.aspx) nel riferimento [API Management REST (REST di gestione API)](https://msdn.microsoft.com/library/azure/dn776326.aspx).

## <a name="create-developer"> </a>Creare un nuovo sviluppatore

Per creare un nuovo sviluppatore, fare clic su **Gestisci** nel portale di Azure classico per il servizio Gestione API. Verrà visualizzato il portale di pubblicazione di Gestione API. Se non è stato creata un'istanza del servizio Gestione API, vedere [Creare un'istanza del servizio Gestione API][] nell'esercitazione [Introduzione a Gestione API di Azure][].

![Portale di pubblicazione][api-management-management-console]

Fare clic su **Utenti** dal menu **Gestione API** sulla sinistra, quindi scegliere **aggiungi utente**.

![Create developer][api-management-create-developer]

Immettere **Indirizzo di posta elettronica**, **Password** e **Nome** per il nuovo sviluppatore e fare clic su **Salva**.

![Create developer][api-management-add-new-user]

Per impostazione predefinita, i nuovi account sviluppatore creati sono **attivi** e associati al gruppo **Sviluppatori**.

![New developer][api-management-new-developer]

Gli account sviluppatore con stato **attivo** possono essere usati per accedere a tutte le API per cui dispongono di sottoscrizioni. Per associare il nuovo sviluppatore creato ad altri gruppi, vedere [Come associare i gruppi agli sviluppatori][].

## <a name="invite-developer"> </a>Invitare uno sviluppatore

Per invitare uno sviluppatore, fare clic su **Utenti** dal menu **Gestione API** sulla sinistra, quindi scegliere **Invita utente**.

![Invite developer][api-management-invite-developer]

Immettere il nome e l'indirizzo di posta elettronica e fare clic su **Invita**.

![Invite developer][api-management-invite-developer-window]

Viene visualizzato un messaggio di conferma, ma il nuovo sviluppatore invitato non appare nell'elenco finché l'invito non viene accettato.

![Invite confirmation][api-management-invite-developer-confirmation]

Quando uno sviluppatore viene invitato, gli viene inviato un messaggio di posta elettronica generato con un modello e personalizzabile. Per altre informazioni, vedere [Configurare modelli di posta elettronica][].

Dopo l'accettazione dell'invito, l'account diventa attivo.

## <a name="block-developer"> </a> Disattivare o riattivare un account sviluppatore

Per impostazione predefinita, un nuovo account sviluppatore creato o invitato è **Attivo**. Per disattivare un account sviluppatore, fare clic su **Blocca**. Per riattivare un account sviluppatore bloccato, fare clic su **Attiva**. Un account sviluppatore bloccato non può accedere al portale per sviluppatori né chiamare le API. Per eliminare un account utente, fare clic su **Elimina**.

![Block developer][api-management-new-developer]

## Reimpostare la password di un utente

Per reimpostare la password per un account utente, fare clic sul nome dell'account.

![Reimpostazione delle password][api-management-view-developer]

Fare clic su **Reimposta password** per inviare all'utente un collegamento per la reimpostazione della password.

![Reimpostazione delle password][api-management-reset-password]

Per lavorare con gli account utente a livello di codice, vedere la documentazione [User entity (Entità utente)](https://msdn.microsoft.com/library/azure/dn776330.aspx) nel riferimento [API Management REST (REST di gestione API)](https://msdn.microsoft.com/library/azure/dn776326.aspx). Per reimpostare la password di un account utente su un valore specifico, è possibile usare la procedura [Update a user (Aggiornamento di un utente)](https://msdn.microsoft.com/library/azure/dn776330.aspx#UpdateUser) e specificare la password desiderata.

## Verifica in sospeso

![Verifica in sospeso][api-management-pending-verification]

## <a name="next-steps"> </a>Passaggi successivi

Dopo aver creato un account sviluppatore, è possibile associarlo ai ruoli ed effettuarne la sottoscrizione a prodotti e API. Per altre informazioni, vedere [Come creare e usare i gruppi][].


[api-management-management-console]: ./media/api-management-howto-create-or-invite-developers/api-management-management-console.png
[api-management-add-new-user]: ./media/api-management-howto-create-or-invite-developers/api-management-add-new-user.png
[api-management-create-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png
[api-management-invite-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer.png
[api-management-new-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-new-developer.png
[api-management-invite-developer-window]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-window.png
[api-management-invite-developer-confirmation]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-confirmation.png
[api-management-pending-verification]: ./media/api-management-howto-create-or-invite-developers/api-management-pending-verification.png
[api-management-view-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-view-developer.png
[api-management-reset-password]: ./media/api-management-howto-create-or-invite-developers/api-management-reset-password.png
: ./media/api-management-howto-create-or-invite-developers/.png



[Create a new developer]: #create-developer
[Invite a developer]: #invite-developer
[Deactivate or reactivate a developer account]: #block-developer
[Next steps]: #next-steps
[Come creare e usare i gruppi]: api-management-howto-create-groups.md
[Come associare i gruppi agli sviluppatori]: api-management-howto-create-groups.md#associate-group-developer

[Introduzione a Gestione API di Azure]: api-management-get-started.md
[Creare un'istanza del servizio Gestione API]: api-management-get-started.md#create-service-instance
[Configurare modelli di posta elettronica]: api-management-howto-configure-notifications.md#email-templates

<!---HONumber=AcomDC_0810_2016-->