<properties
	pageTitle="Accessi da origini sconosciute"
	description="Un report che indica gli utenti che hanno effettuato correttamente l'accesso alla directory da un indirizzo IP proxy anonimo."
	services="active-directory"
	documentationCenter=""
	authors="SSalahAhmed"
	manager="femila"
	editor=""/>  

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/04/2016"
	ms.author="saah;kenhoff"/>  

# Accessi da origini sconosciute
Questo report indica gli utenti che hanno eseguito correttamente l'accesso alla directory mentre era loro assegnato un indirizzo IP client riconosciuto da Microsoft come indirizzo IP proxy anonimo (ad esempio un indirizzo Tor IP). Questi proxy vengono spesso usati dagli utenti che vogliono nascondere l'indirizzo IP del computer e possono essere usati per attacchi dannosi.

I risultati del report mostreranno il numero di volte in cui un utente ha effettuato correttamente l'accesso alla directory da quell'indirizzo e l'indirizzo IP del proxy.


![Accessi da origini sconosciute](./media/active-directory-reporting-sign-ins-from-unknown-sources/signInsFromUnknownSources.PNG)

<!---HONumber=AcomDC_0928_2016-->