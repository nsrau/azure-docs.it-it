<properties 
	pageTitle="Come configurare notifiche e modelli di posta elettronica in Gestione API di Azure" 
	description="Informazioni su come configurare notifiche e modelli di posta elettronica in Gestione API di Azure." 
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
	ms.date="09/10/2015" 
	ms.author="sdanie"/>

# Come configurare notifiche e modelli di posta elettronica in Gestione API di Azure

Gestione API consente di configurare notifiche per eventi specifici nonché di configurare modelli di posta elettronica da usare per comunicare con gli amministratori e gli sviluppatori di un'istanza di Gestione API. In questo argomento viene illustrato come configurare notifiche per gli eventi disponibili e viene offerta una panoramica sulla configurazione di modelli di posta elettronica usati per tali eventi.

## <a name="publisher-notifications"> </a>Configurare le notifiche dell'editore

Per configurare le notifiche, fare clic su **Gestisci** nel portale di Azure per il servizio Gestione API. Verrà visualizzato il portale di pubblicazione di Gestione API.

![Portale di pubblicazione][api-management-management-console]

>Se non è stato creata un'istanza del servizio Gestione API, vedere [Creare un'istanza del servizio Gestione API][] nell'esercitazione [Introduzione a Gestione API di Azure][].

Fare clic su **Notifiche** dal menu **Gestione API** a sinistra per visualizzare le notifiche disponibili.

![Notifiche dell'editore][api-management-publisher-notifications]

L'elenco di eventi seguente può essere configurato per le notifiche.

-	**Richieste di sottoscrizione (che richiedono approvazione)** - I destinatari e gli utenti di posta elettronica specificati riceveranno notifiche tramite posta elettronica sulle richieste di sottoscrizione per prodotti API che richiedono approvazione.
-	**Nuove sottoscrizioni** - I destinatari e gli utenti di posta elettronica specificati riceveranno notifiche tramite posta elettronica sulle nuove sottoscrizioni a prodotti API.
-	**Richieste relative alla raccolta di applicazioni** - I destinatari e gli utenti di posta elettronica specificati riceveranno notifiche tramite posta elettronica quando nuove applicazioni vengono inviate alla raccolta di applicazioni.
-	**BCC** - I destinatari e gli utenti di posta elettronica specificati riceveranno copie per conoscenza nascosta di tutti i messaggi di posta elettronica inviati agli sviluppatori.
-	**Nuovo problema o commento** - I destinatari e gli utenti di posta elettronica specificati riceveranno notifiche tramite posta elettronica quando un nuovo problema o commento viene inviato al portale per sviluppatori.
-	**Messaggio di chiusura account** - I destinatari e gli utenti di posta elettronica specificati riceveranno notifiche tramite posta elettronica quando un account viene chiuso.
-	**Raggiungimento limite quota sottoscrizione** - I destinatari e gli utenti di posta elettronica specificati riceveranno notifiche tramite posta elettronica quando l'uso della sottoscrizione si avvicina al limite di quota di utilizzo.

Per ogni evento, è possibile specificare i destinatari di posta elettronica usando la casella di testo per l'indirizzo di posta elettronica oppure è possibile selezionare gli utenti da un elenco.

Per specificare gli indirizzi di posta elettronica a cui inviare le notifiche, immettere tali indirizzi nella casella di testo relativa all'indirizzo di posta elettronica. Se ci sono più indirizzi di posta elettronica, separarli usando delle virgole.

![Destinatari delle notifiche][api-management-email-addresses]

Per specificare gli utenti a cui inviare le notifiche, fare clic su **Aggiungi destinatario**, selezionare la casella di controllo corrispondente all'utente desiderato, quindi fare clic su **OK**.

>Si noti che solo gli amministratori vengono visualizzati nell'elenco.

Dopo avere configurato i destinatari delle notifiche, fare clic su **Salva** per rendere effettivi i destinatari delle notifiche aggiornati.

>Se si esce dalla scheda **Notifiche dell'editore**, il portale di gestione API visualizza un avviso in caso di modifiche non salvate.

## <a name="email-templates"> </a>Configurare modelli di posta elettronica

Gestione API fornisce modelli di posta elettronica per i messaggi che vengono inviati durante l'amministrazione e l'uso del servizio. Sono forniti i modelli di posta elettronica indicati di seguito.

-	Invio alla raccolta di applicazioni approvato
-	Lettera di commiato sviluppatore
-	Notifica raggiungimento limite quota sviluppatore
-	Invito utente
-	Nuovo commento aggiunto a problema
-	Nuovo problema ricevuto
-	Nuova sottoscrizione attivata
-	Conferma rinnovo sottoscrizione
-	Richiesta sottoscrizione rifiutata
-	Richiesta sottoscrizione ricevuta

Questi modelli possono essere modificati a piacimento.

Per visualizzare e configurare i modelli di posta elettronica per la propria istanza di Gestione API, fare clic su **Notifiche** dal menu **Gestione API** a sinistra, quindi selezionare la scheda **Modelli di posta elettronica**.

![Modelli di posta elettronica][api-management-email-templates]

Per visualizzare o modificare un modello specifico, selezionarlo dall'elenco a discesa **Modelli**.

![Elenco modelli di posta elettronica][api-management-email-templates-list]

Ogni modello di posta elettronica ha un oggetto in testo normale e una definizione di corpo in formato HTML. Ogni elemento può essere personalizzato a piacimento.

![Editor dei modelli di posta elettronica][api-management-email-template]

L'elenco **Parametri** contiene un elenco di parametri che, quando inserito nell'oggetto o nel corpo, verrà sostituito dal valore designato quando viene inviato il messaggio di posta elettronica. Per inserire un parametro, posizionare il cursore dove si vuole inserire il parametro, quindi fare clic sulla freccia a sinistra del nome del parametro.

Fare clic su **Anteprima** o **Invia test** per verificare l'aspetto del messaggio o per inviare un messaggio di prova.

>Si noti che i parametri non vengono sostituiti con valori effettivi quando si visualizzano anteprime o si inviano messaggi di prova.
>
>Per salvare le modifiche al modello di posta elettronica, fare clic su **Salva** oppure, per annullare le modifiche, fare clic su **Annulla**.



[api-management-management-console]: ./media/api-management-howto-configure-notifications/api-management-management-console.png
[api-management-publisher-notifications]: ./media/api-management-howto-configure-notifications/api-management-publisher-notifications.png
[api-management-email-addresses]: ./media/api-management-howto-configure-notifications/api-management-email-addresses.png


[api-management-email-templates]: ./media/api-management-howto-configure-notifications/api-management-email-templates.png
[api-management-email-templates-list]: ./media/api-management-howto-configure-notifications/api-management-email-templates-list.png
[api-management-email-template]: ./media/api-management-howto-configure-notifications/api-management-email-template.png


[Configure publisher notifications]: #publisher-notifications
[Configure email templates]: #email-templates

[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer

[Introduzione a Gestione API di Azure]: api-management-get-started.md
[Creare un'istanza del servizio Gestione API]: api-management-get-started.md#create-service-instance

<!---HONumber=Sept15_HO2-->