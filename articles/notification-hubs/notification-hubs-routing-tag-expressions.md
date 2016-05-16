<properties
	pageTitle="Espressioni di routing e tag"
	description="Questo argomento illustra le espressioni di routing e tag per gli hub di notifica di Azure."
	services="notification-hubs"
	documentationCenter=".net"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/28/2016"
	ms.author="wesmc"/>

# Espressioni di routing e tag

##Panoramica

Le espressioni tag consentono di avere come destinazione set specifici di dispositivi, o più specificamente registrazioni, quando si invia una notifica push tramite hub di notifica.


## Destinazione su registrazioni specifiche

L'unico modo per avere come destinazione registrazioni di notifiche specifiche consiste nell'associare tag ad esse, quindi utilizzare come destinazione tali tag. Come descritto in [Gestione delle registrazioni](notification-hubs-registration-management.md), per ricevere le notifiche push un'app deve registrare un handle di dispositivo in un hub di notifica push. Dopo aver creato una registrazione su un hub di notifica, il back-end dell'applicazione può inviare ad esso notifiche push. Il back-end dell'applicazione può scegliere le registrazioni da utilizzare come destinazione con una notifica specifica nei modi seguenti:

1. **Trasmissione**: tutte le registrazioni nell'hub di notifica ricevono la notifica.
2. **Tag**: tutte le registrazioni contenenti il tag specificato ricevono la notifica.
3. **Espressione tag**: tutte le registrazioni il cui set di tag corrisponde all'espressione specificata ricevono la notifica.

## Tag

Un tag può essere qualsiasi stringa, fino a 120 caratteri, che contiene caratteri alfanumerici e i seguenti caratteri non alfanumerici: ‘\_’, ‘@’, ‘#’, ‘.’, ‘:’, ‘-’. Nell'esempio seguente viene illustrata un'applicazione da cui è possibile ricevere notifiche di tipo avviso popup su gruppi musicali specifici. In questo scenario, un modo semplice per instradare le notifiche è etichettare le registrazioni con tag che rappresentano i diversi gruppi musicali, come illustrato nell'immagine seguente.

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags.png)

In questa immagine, il messaggio con tag **Beatles** raggiunge solo il tablet registrato con il tag **Beatles**.

Per ulteriori informazioni sulla creazione di registrazioni per i tag, vedere [Gestione delle registrazioni](notification-hubs-registration-management.md).

È possibile inviare notifiche ai tag usando i metodi di notifiche di invio della classe `Microsoft.Azure.NotificationHubs.NotificationHubClient` nell’SDK [hub di notifica di Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). È inoltre possibile utilizzare Node.js o le API REST delle notifiche push. Di seguito è riportato un esempio con l’SDK.


	Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

	// Windows 8.1 / Windows Phone 8.1
	var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
	"You requested a Beatles notification</text></binding></visual></toast>";
	outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Beatles");

	// Windows 10
	toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
	"You requested a Wailers notification</text></binding></visual></toast>";
	outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Wailers");




Per tag non deve necessariamente essere eseguito il pre-provisioning e questi possono fare riferimento a più concetti specifici dell'app. Ad esempio, gli utenti di questa applicazione di esempio possono commentare i gruppi e desiderano ricevere avvisi popup, non solo per i commenti sui propri gruppi preferiti, ma anche per tutti i commenti degli amici, indipendentemente dal gruppo che stanno commentando. Nell'immagine seguente viene illustrato un esempio di questo scenario:



![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags2.png)

In questa immagine, Alice è interessata a ricevere aggiornamenti sui Beatles e Bob è interessato a ricevere aggiornamenti sui Wailers. Bob è anche interessato ai commenti di Charlie e Charlie è interessato ai Wailers. Quando viene inviata una notifica per un commento di Charlie sui Beatles, sia Alice che Bob la ricevono.

Sebbene sia possibile codificare più aspetti nei tag (ad esempio, "gruppo\_Beatles" o "segue\_Charlie"), i tag sono semplici stringhe e non proprietà con valori. Esiste una corrispondenza per la registrazione solo in presenza o assenza di un tag specifico.

Per un'esercitazione completa dettagliata su come usare i tag per l'invio a gruppi di interesse, vedere [Ultime notizie](notification-hubs-windows-store-dotnet-send-breaking-news.md).


## Uso dei tag per considerare come obiettivo gli utenti

Un altro modo per utilizzare i tag consiste nell'identificare tutti i dispositivi di un particolare utente. Le registrazioni possono essere contrassegnate con un tag che contiene un id utente, come illustrato nell'immagine seguente:


![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags3.png)

In questa immagine, il messaggio con tag uid:Alice raggiunge tutte le registrazioni con tag uid:Alice; di conseguenza, tutti i dispositivi di Alice.


##Espressioni tag

Vi sono casi in cui una notifica ha come destinazione un set di registrazioni identificato non da un singolo tag, ma da un'espressione booleana su tag.

Si consideri un'applicazione sportiva che invia un promemoria su una partita tra Red Sox e Cardinals a tutte le persone di Boston. Se l'app client registra i tag relativi all'interesse per squadre e località, la notifica deve essere destinata a tutte le persone di Boston interessate ai Red Sox o ai Cardinals. Questa condizione può essere espressa con l'espressione booleana seguente:

	(follows_RedSox || follows_Cardinals) && location_Boston


![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags4.png)

Le espressioni tag possono contenere tutti gli operatori booleani, quali AND (&&), OR (||) e NOT (!). Possono inoltre contenere parentesi. Le espressioni tag sono limitate a 20 tag se contengono solo operatori OR; in caso contrario sono limitate a 6 tag.

Di seguito è riportato un esempio per l'invio di notifiche con espressioni tag usando l’SDK.


	Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

	String userTag = "(location_Boston && !follows_Cardinals)";	

	// Windows 8.1 / Windows Phone 8.1
	var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
	"You want info on the Red Socks</text></binding></visual></toast>";
	outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

	// Windows 10
	toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
	"You want info on the Red Socks</text></binding></visual></toast>";
	outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

<!---HONumber=AcomDC_0504_2016-->