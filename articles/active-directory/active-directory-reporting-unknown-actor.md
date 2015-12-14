<properties
   pageTitle="’Attore Sconosciuto’ per il report di Azure Active Directory | Microsoft Azure"
   description="Descrizione dell'evento 'Attore Sconosciuto' nei report di Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="kenhoff"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="11/30/2015"
   ms.author="kenhoff"/>

# Evento ’Attore Sconosciuto’ per il report di Azure Active Directory

## Articoli di documentazione della creazione di report

 - [API di creazione report](active-directory-reporting-api-getting-started.md)
 - [Eventi di controllo](active-directory-reporting-audit-events.md)
 - [Conservazione](active-directory-reporting-retention.md)
 - [Anteprime](active-directory-reporting-previews.md)
 - [Search](active-directory-reporting-search.md)
 - [Recupero delle informazioni](active-directory-reporting-backfill.md)
 - [Latenze](active-directory-reporting-latencies.md)
 - [Evento "Attore sconosciuto"](active-directory-reporting-unknown-actor.md)

In rari casi, è possibile visualizzare valori insoliti nei campi "Attore" o "Utente" nei report di Azure AD. Questo comportamento è previsto ed è causato da uno dei due eventi:

## Un'entità servizio è in uso nella directory, senza un contesto utente

In questo caso, un'entità servizio (applicazione) esegue degli aggiornamenti di directory senza aver avuto effettivamente accesso come utente. In questo modo l'ID dell'entità servizio viene visualizzata come Attore, anziché come un UPN. Ad esempio:

![](./media/active-directory-reporting-unknown-actor/spd-actor.png)

Si tratta di un bug noto e si sta lavorando per risolvere il problema.

## Un utente è stato eliminato dalla directory prima dell'elaborazione dell'evento

In questo caso, un utente è stato eliminato dalla directory prima dell’elaborazione dell'evento e prima che gli venisse associato un nome utente. Ad esempio:

![](./media/active-directory-reporting-unknown-actor/unknown-actor.png)

Si tratta di un bug noto e si sta lavorando per risolvere il problema.

<!-- ![](./media/active-directory-reporting-unknown-actor/uid-actor.png) -->

<!---HONumber=AcomDC_1203_2015-->