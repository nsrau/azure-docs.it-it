<properties
	pageTitle="Accessi da dispositivi potenzialmente infetti"
	description="Un report che include i tentativi di accesso che sono stati effettuati da dispositivi in cui potrebbe essere in esecuzione un software dannoso (malware)."
	services="active-directory"
	documentationCenter=""
	authors="SSalahAhmed"
	manager="gchander"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/04/2016"
	ms.author="saah;kenhoff"/>


# Accessi da dispositivi potenzialmente infetti
Questo report tenta di identificare i dispositivi degli utenti che sono stati infettati e fanno ora parte di una botnet. Gli indirizzi IP di accesso degli utenti vengono messi in correlazione con gli indirizzi IP in contatto con i server della botnet.

Raccomandazione: questo report contrassegna gli indirizzi IP, non i dispositivi dell'utente. Per verificarlo, si consiglia di contattare l'utente e di eseguire la scansione di tutti i dispositivi dell'utente. È inoltre possibile che un dispositivo personale dell'utente sia stato infettato o che un altro utente, che stava usando lo stesso indirizzo IP, abbia un dispositivo infetto.

Per altre informazioni su come risolvere problemi correlati alle infezioni malware, vedere [Malware Protection Center](http://go.microsoft.com/fwlink/?linkid=335773).

![Accessi da dispositivi potenzialmente infetti](./media/active-directory-reporting-sign-ins-from-possibly-infected-devices/signInsFromPossiblyInfectedDevices.PNG)

<!---HONumber=AcomDC_0309_2016-->