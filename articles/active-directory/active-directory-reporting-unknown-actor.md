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
   ms.date="12/07/2015"
   ms.author="kenhoff"/>

# Evento ’Attore Sconosciuto’ per il report di Azure Active Directory

*Questo documento fa parte della [guida alla creazione di report in Azure Active Directory](active-directory-reporting-guide.md).*

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

<!---HONumber=AcomDC_1210_2015-->