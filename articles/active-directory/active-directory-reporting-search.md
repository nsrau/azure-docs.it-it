<properties
	pageTitle="Ricerca di Report di Azure Active Directory"
	description="Come cercare la sicurezza, l’attività e i report di controllo dell'Azure Active Directory"
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
	ms.date="09/29/2015"
	ms.author="kenhoff"/>

# Ricerca di Report di Azure Active Directory

Azure Active Directory fornisce i amministratori di directory la capacità di ricercare la sicurezza l’attività e dell'utente, attività e gli eventi di controllo in più report.

Per individuare il pannello di ricerca, passare a **portale di gestione di Azure -> Your Azure Active Directory -> Reports.** Il pannello è disponibile nella parte superiore dell'elenco di report.

Per cercare le attività o gli eventi di controllo per un determinato utente, selezionare un intervallo di date nei campi Da e A, digitare nell’UPN dell’utente o nel nome visualizzato e fare clic sul pulsante di segno di spunta.

## Report inclusi nella ricerca

Non tutti i report sono ancora inclusi nei risultati della ricerca. Questa tabella indica quali report sono inclusi.

Report | Incluso
--------------------------------------------------- | --------
Accessi da origini sconosciute | No
Accessi dopo più errori | No
Accessi da più aree geografiche | No
Accessi da indirizzi IP con attività sospetta | No
Accessi da dispositivi potenzialmente infetti | No
Attività di accesso irregolare | No
Utenti con anomalie dell'attività di accesso | No
Utenti con credenziali perse | No
Report di controllo | Sì
Attività di reimpostazione password | Sì
Attività di registrazione reimpostazione password | Sì
Attività dei gruppi self-service | Sì
Utilizzo applicazioni | No
Attività di provisioning dell'account | Sì
Stato rollover della password | No
Errori di provisioning dell'account | No
Utilizzo di RMS | No
Utenti RMS più attivi | No
Utilizzo dispositivi RMS | No


## Altre informazioni

 - [Report di Azure Active Directory](active-directory-view-access-usage-reports.md)
 - [Enti di controllo per il report di Azure Active Directory](active-directory-reporting-audit-events.md)

<!---HONumber=Oct15_HO1-->