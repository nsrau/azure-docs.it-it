<properties
	pageTitle="Informazioni su come inviare notifiche pianificate | Microsoft Azure"
	description="Questo argomento descrive l'uso di notifiche pianificate con Hub di notifica."
	services="notification-hubs"
	documentationCenter=".net"
	keywords="notifiche push, notifica push, pianificazione notifiche push"
	authors="wesmc7777"
	manager="erikre"
	editor=""/>
<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/29/2016"
	ms.author="wesmc"/>

# Procedura: Inviare le notifiche pianificate


##Panoramica

Si consideri uno scenario in cui si desidera inviare una notifica in un certo momento in futuro, ma non si dispone di una soluzione semplice per riattivare il codice di back-end per l'invio della notifica. Il livello Standard Hub di notifica supporta una funzionalità che consente di pianificare le notifiche fino a 7 giorni prima dell'invio.

Per l'invio di una notifica è sufficiente usare la classe [ScheduledNotification](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) classe nell'SDK di Hub di notifica, come illustrato nell'esempio seguente:

	Notification notification = new AppleNotification("{"aps":{"alert":"Happy birthday!"}}");
	var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));

È anche possibile annullare una notifica pianificata in precedenza usando il relativo notificationId:

	await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);

Non sono previsti limiti al numero di notifiche pianificate che è possibile inviare.

<!---HONumber=AcomDC_0706_2016-->