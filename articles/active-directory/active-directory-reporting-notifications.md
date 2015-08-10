<properties
	pageTitle="Notifiche di Report di Azure Active Directory"
	description="Come utilizzare le notifiche di report di Azure Active Directory per gli accessi sospetti."
	services="active-directory"
	documentationCenter=""
	authors="SSalahAhmed"
	manager="TerryLan"
	editor="LisaToft"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/24/2015"
	ms.author="kenhoff"/>

# Notifiche di Report di Azure Active Directory

## Quali report generano notifiche tramite posta elettronica

A questo punto, solo il rapporto Attività di accesso con anomalie e Utenti con anomalie dell'attività di accesso usano il sistema di notifica tramite posta elettronica.

## Che cosa attiva l'invio della notifica tramite posta elettronica?

Per impostazione predefinita, Azure Active Directory è impostato per l'invio automatico delle notifiche tramite posta elettronica a tutti gli amministratori globali. Il messaggio viene inviato quando si verificano le condizioni seguenti per ogni report.

Per il report Utenti con anomalie dell'attività di accesso:

- Origini sconosciute: 10 eventi
- Più errori: 10 eventi
- Accessi da indirizzi IP con attività sospetta: 10 eventi
- Dispositivi infetti: 10 eventi

Report Utenti con anomalie dell'attività di accesso

- Origini sconosciute: 10 eventi
- Più errori: 10 eventi
- Accessi da indirizzi IP con attività sospetta: 10 eventi
- Dispositivi infetti: 5 eventi
- Report accessi anomali: 15 eventi

Il messaggio di posta elettronica viene inviato se una delle condizioni precedenti viene soddisfatta entro 30 giorni o poiché l'ultimo messaggio di posta elettronica è stato inviato se è inferiore a 30 giorni.

Per accessi anomali si intendono quelli che sono stati identificati come "anomali" dagli algoritmi di Machine Learning, sulla base di posizioni di accesso impreviste, ora del giorno e posizioni impreviste o una combinazione di questi fattori. È possibile che un pirata informatico abbia tentato di accedere con questo account. Per altre informazioni su questo report, vedere la tabella riportata in precedenza.

## A chi vengono inviate le notifiche tramite posta elettronica?

Il messaggio di posta elettronica viene inviato a tutti gli amministratori globali ai quali è stata assegnata una licenza di Active Directory Premium. Per garantire il recapito, il messaggio viene inviato anche all'indirizzo di posta elettronica alternativo degli amministratori . Per essere certi di ricevere il messaggio, gli amministratori devono aggiungere aad-alerts-noreply@mail.windowsazure.com nei rispettivi elenchi di mittenti attendibili.

## Con quale frequenza vengono inviati i messaggi di posta elettronica?

Dopo l'invio di un messaggio di posta elettronica, quello successivo verrà inviato solo se entro 30 giorni dall'invio si verificano almeno 10 o nuovi eventi di accesso anomalo. Come si accede al report indicato nel messaggio di posta elettronica?

Quando si fa clic sul collegamento, si verrà reindirizzati alla pagina del report nel portale di gestione di Azure. Per accedere al report, è necessario essere:

- Un amministratore o un coamministratore della sottoscrizioni di Azure
- Un amministratore globale nella directory e assegnata una licenza di Active Directory Premium. Per altre informazioni, vedere Informazioni su Azure Active Directory.

## È possibile disattivare questi messaggi di posta elettronica?

Sì, per disattivare le notifiche correlate a anomali accessi all'interno del portale di gestione di Azure, fare clic su **Configura** e quindi selezionare **Disabilitato** sotto la sezione **Notifiche**.

## Passaggi successivi
- Per informazioni sui report di sicurezza, controllo e attività disponibili, vedere [Report di sicurezza, controllo e attività di Azure AD](active-directory-view-access-usage-reports.md)
- [Introduzione ad Azure Active Directory Premium](active-directory-get-started-premium.md)
- [Aggiungere informazioni personalizzate distintive dell'azienda alla pagina di accesso e al pannello di accesso](active-directory-add-company-branding.md)

<!---HONumber=July15_HO5-->