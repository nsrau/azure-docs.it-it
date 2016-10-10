<properties
	pageTitle="Ricerca di Report di Azure Active Directory"
	description="Come cercare la sicurezza, l’attività e i report di controllo dell'Azure Active Directory"
	services="active-directory"
	documentationCenter=""
	authors="dhanyahk"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="03/07/2016"
	ms.author="dhanyahk"/>

# Ricerca di Report di Azure Active Directory

*Questo documento fa parte della [guida alla creazione di report in Azure Active Directory](active-directory-reporting-guide.md).*

Azure Active Directory (Azure AD) offre agli amministratori di directory la capacità di cercare in più report gli eventi relativi alla sicurezza, alle attività e al controllo dell’utente.

Per trovare il pannello di ricerca, passare a **Portale di Azure classico > Azure Active Directory > Report.** Il pannello è disponibile nella parte superiore dell'elenco di report.

Per cercare gli eventi di attività o controllo per un determinato utente, selezionare un intervallo di date nei campi Da e A, immettere l’UPN dell’utente o il nome visualizzato e selezionare il pulsante con il segno di spunta (OK).

## Report inclusi nella ricerca

Non tutti i report sono ancora inclusi nei risultati della ricerca. Questa tabella indica quali report sono inclusi.

Report | Incluso
--------------------------------------------------- | --------
Accessi da origini sconosciute | No
Accessi dopo più errori | No
Accessi da più aree geografiche | No
Accessi da indirizzi IP con attività sospette | No
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

<!---HONumber=AcomDC_0928_2016-->