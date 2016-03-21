<properties
	pageTitle="Notifiche di Report di Azure Active Directory"
	description="Come utilizzare le notifiche di report di Azure Active Directory per gli accessi sospetti."
	services="active-directory"
	documentationCenter=""
	authors="dhanyahk"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/07/2016"
	ms.author="dhanyahk"/>

# Notifiche di Report di Azure Active Directory

## Quali report generano notifiche tramite posta elettronica

A questo punto, solo il report dell’attività di accesso irregolare attiva le notifiche di posta elettronica.

## Che cos'è un "Accesso irregolare"?

Gli accessi irregolari sono quelli individuati dagli algoritmi di Machine Learning in base a una condizione di tipo "tempo di spostamento impossibile" combinata con una posizione e un dispositivo di accesso anomali. È possibile che un pirata informatico abbia tentato di accedere con questo account.

## A chi vengono inviate le notifiche tramite posta elettronica?

Il messaggio di posta elettronica viene inviato a tutti gli amministratori globali ai quali è stata assegnata una licenza di Active Directory Premium. Per garantire il recapito, il messaggio viene inviato anche all'indirizzo di posta elettronica alternativo degli amministratori . Per essere certi di ricevere il messaggio, gli amministratori devono aggiungere aad-alerts-noreply@mail.windowsazure.com nei rispettivi elenchi di mittenti attendibili.

## Con quale frequenza vengono inviati i messaggi di posta elettronica?

Il messaggio di posta elettronica viene inviato se si verificano 10 nuove attività di accesso irregolare negli ultimi 30 giorni o dopo l'ultimo messaggio di posta elettronica inviato, a seconda del periodo più breve.

## Come si accede al report indicato nel messaggio di posta elettronica?

Quando si fa clic sul collegamento, si verrà reindirizzati alla pagina del report nel portale di Azure classico. Per accedere al report, è necessario essere:

- Un amministratore o un coamministratore della sottoscrizioni di Azure

- Un amministratore globale nella directory e assegnata una licenza di Active Directory Premium. Per altre informazioni, vedere [Edizioni di Azure Active Directory](active-directory-editions.md).

## È possibile disattivare questi messaggi di posta elettronica?

Sì, per disattivare le notifiche correlate ad accessi anomali all'interno del portale di Azure classico, fare clic su **Configura** e quindi selezionare **Disabilitato** sotto la sezione **Notifiche**.

## Passaggi successivi
- Per informazioni sui report di sicurezza, controllo e attività disponibili, vedere [Report di sicurezza, controllo e attività di Azure AD](active-directory-view-access-usage-reports.md)
- [Introduzione ad Azure Active Directory Premium](active-directory-get-started-premium.md)
- [Aggiungere informazioni personalizzate distintive dell'azienda alla pagina di accesso e al pannello di accesso](active-directory-add-company-branding.md)

<!---HONumber=AcomDC_0309_2016-->