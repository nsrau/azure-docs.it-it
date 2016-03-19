<properties 
	pageTitle="Inviare notifiche personalizzate con Azure Mobile Engagement" 
	description="Come inviare notifiche personalizzate includendo informazioni sul profilo utente come il nome nelle notifiche"		
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="all" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/07/2015" 
	ms.author="piyushjo" />

#Personalizzare le notifiche includendo il nome utente

Per rendere le notifiche più interessanti per gli utenti dell'app, è possibile personalizzarle. Ad esempio, è possibile usare in modo selettivo i nomi degli utenti dell'app per indirizzare le notifiche e renderle più personali. È importante precisare che l'aggiunta dei nomi degli utenti alle notifiche è una strategia da adottare con moderazione, perché se abusata rischia di mettere a disagio gli utenti dell'app. È anche necessario assicurarsi di dare all'utente la possibilità di scegliere di fornire queste informazioni personali e il proprio consenso all'interno dell'app per dispositivi mobili prima di iniziare a usare questa strategia.

Con Azure Mobile Engagement è possibile personalizzare le notifiche seguendo la procedura illustrata di seguito, che descrive uno scenario in cui il nome utente viene incluso nelle notifiche. Viene usato il concetto di info app o tag i cui valori potrebbero essere passati dagli SDK integrati nell'app per dispositivi mobili per mezzo di API. Info app e Tag potrebbero essere usati:

1. per indirizzare le notifiche a utenti specifici in base ai valori di info app o 
2. come segnaposto nelle notifiche, da sostituire con valori specifici per l'utente o il dispositivo durante l'invio di notifiche al dispositivo. 

> [AZURE.IMPORTANT]Si noti che la velocità di invio delle notifiche risulterà ridotta a causa dell'elaborazione aggiuntiva necessaria per sostituire i valori di info app con le singole notifiche.

##Registrare info app nel portale di Mobile Engagement

1) Registrare prima di tutto le info app o i tag nel portale di Azure. Passare a **Impostazioni** -> **Tag (info app)**.

![][1]

2) Fare clic su **Nuovo tag (info app)** e specificare il nome come *user\_name* e il tipo come *Stringa* e fare clic su **Invia**.

![][2]

3) Le info app registrate verranno visualizzate come segue:

![][3]

##Inviare info app dall'SDK del client

Qui viene usato l'esempio di app universale di Windows ma esistono metodi equivalenti anche per altri SDK.

Se è disponibile un metodo nell'app per dispositivi mobili che permette di ottenere informazioni sul profilo da parte dell'utente, ad esempio il nome dell'utente dopo la sua autenticazione, chiamare qui il metodo `SendAppInfo` e popolare il valore dell'info app `user_name` registrata in precedenza nel back-end del servizio Mobile Engagement.

    Dictionary<object, object> appInfo = new Dictionary<object, object>();
    appInfo.Add("user_name", str);
    EngagementAgent.Instance.SendAppInfo(appInfo); 

##Inviare notifiche personalizzate

A questo punto è possibile inviare notifiche usando questo **user\_name**.

1) Passare al portale di Mobile Engagement nella scheda **Copertura** per creare una notifica. È possibile usare questo segnaposto nel formato seguente in qualsiasi parte del titolo o nel corpo della notifica.

![][4]

> [AZURE.NOTE]Gli utenti per i quali l'info app user\_name non è impostata non riceveranno notifiche. Se si esegue una campagna di notifica in modalità di test, e se le informazioni sull’app non sono impostate, verrà inviato un carattere '?' per sostituire il segnaposto.

2) Quando Mobile Engagement seleziona un dispositivo per l'invio della notifica, esamina questa info app e sostituisce il valore del segnaposto. Se, ad esempio, è stato impostato `str = "Scott"` per un utente, la registrazione del dispositivo verrà associata all'info app **user\_name = SCOTT** per l'utente e questi visualizzerà una notifica push fuori dall'app nel formato seguente:

![][5]

<!-- Images. -->
[1]: ./media/mobile-engagement-send-personalized-notifications/app-info.png
[2]: ./media/mobile-engagement-send-personalized-notifications/create-app-info.png
[3]: ./media/mobile-engagement-send-personalized-notifications/app-info-user-name.png
[4]: ./media/mobile-engagement-send-personalized-notifications/personal-notification.png
[5]: ./media/mobile-engagement-send-personalized-notifications/notification.png

<!---HONumber=AcomDC_1223_2015-->