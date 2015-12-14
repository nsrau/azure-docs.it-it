<properties
   pageTitle="Tempi di recupero delle informazioni dei report di Azure Active Directory | Microsoft Azure"
   description="Tempo necessario per la visualizzazione in Azure Active Directory degli eventi dei report precedenti"
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

# Criteri di conservazione dei report di Azure Active Directory

## Articoli di documentazione della creazione di report

 - [API di creazione report](active-directory-reporting-api-getting-started.md)
 - [Eventi di controllo](active-directory-reporting-audit-events.md)
 - [Conservazione](active-directory-reporting-retention.md)
 - [Anteprime](active-directory-reporting-previews.md)
 - [Search](active-directory-reporting-search.md)
 - [Recupero delle informazioni](active-directory-reporting-backfill.md)
 - [Latenze](active-directory-reporting-latencies.md)
 - [Evento "Attore sconosciuto"](active-directory-reporting-unknown-actor.md)

Dopo aver scelto una directory per i report, i report recuperano le informazioni per un determinato numero di giorni, indicato qui.

Report | Descrizione
------------------------------------------------------- | -----------
Accessi da origini sconosciute | 0 giorni
Accessi dopo più errori | 0 giorni
Accessi da più aree geografiche | 0 giorni
Accessi da indirizzi IP con attività sospetta | 0 giorni
Accessi da dispositivi potenzialmente infetti | 0 giorni
Attività di accesso irregolare | 0 giorni
Utenti con anomalie dell'attività di accesso | 0 giorni
Utenti con credenziali perse | 0 giorni
Report di controllo | 30 giorni
Attività di reimpostazione password (Azure AD) | 0 giorni
Attività di reimpostazione password (Gestione delle identità) | 0 giorni
Attività di registrazione per la reimpostazione password (Azure AD) | 0 giorni
Attività di registrazione di reimpostazione della password (Gestione delle identità) | 0 giorni
Attività dei gruppi self-service (Azure AD) | 0 giorni
Attività dei gruppi self-service (Gestione delle identità) | 0 giorni
Utilizzo applicazioni | 0 giorni
Attività di provisioning dell'account | 0 giorni
Stato rollover della password | 0 giorni
Errori di provisioning dell'account | 0 giorni
Utilizzo di RMS | 0 giorni
Utenti RMS più attivi | 0 giorni
Utilizzo dispositivi RMS | 0 giorni
Utilizzo applicazioni abilitate per RMS | 0 giorni

<!---HONumber=AcomDC_1203_2015-->