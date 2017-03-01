---
title: Inviare notifiche personalizzate con Azure Mobile Engagement
description: Come inviare notifiche personalizzate includendo informazioni sul profilo utente come il nome nelle notifiche
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 51007089-156a-4bac-bb1b-a590633bf2a2
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: all
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 06e16033435ed0a37d5688055743875827d3aec2
ms.openlocfilehash: f27ed36eddddfab1de424574b0cf8e9ef04a162d
ms.lasthandoff: 03/01/2017


---
# <a name="personalize-notifications-by-including-user-name"></a>Personalizzare le notifiche includendo il nome utente
Per rendere le notifiche più interessanti per gli utenti dell'app, è possibile personalizzarle. Ad esempio, è possibile usare in modo selettivo i nomi degli utenti dell'app per indirizzare le notifiche e renderle più personali. È importante precisare che l'aggiunta dei nomi degli utenti alle notifiche è una strategia da adottare con moderazione, perché se abusata rischia di mettere a disagio gli utenti dell'app. È anche necessario assicurarsi di dare all'utente la possibilità di scegliere di fornire queste informazioni personali e il proprio consenso all'interno dell'app per dispositivi mobili prima di iniziare a usare questa strategia. 

Con Azure Mobile Engagement è possibile personalizzare le notifiche seguendo la procedura illustrata di seguito, che descrive uno scenario in cui il nome utente viene incluso nelle notifiche. Viene usato il concetto di info app o tag i cui valori potrebbero essere passati dagli SDK integrati nell'app per dispositivi mobili per mezzo di API. Info app e Tag potrebbero essere usati:

1. per indirizzare le notifiche a utenti specifici in base ai valori di info app o 
2. come segnaposto nelle notifiche, da sostituire con valori specifici per l'utente o il dispositivo durante l'invio di notifiche al dispositivo. 

> [!IMPORTANT]
> Si noti che la velocità di invio delle notifiche risulterà ridotta a causa dell'elaborazione aggiuntiva necessaria per sostituire i valori di info app con le singole notifiche. 
> 
> 

## <a name="register-app-info-in-the-mobile-engagement-portal"></a>Registrare info app nel portale di Mobile Engagement
1) Si inizia con la registrazione di informazioni sull'applicazione o tag nel Portale di Azure. Andare su **Impostazioni** -> **Tag (App-Info)**.  

![][1]    

2) Fare clic su **Nuovo tag (app-info)** e specificare il nome come *nome_utente* e il tipo come *stringa*, quindi fare clic su **Invia**. 

![][2]

3) Alla fine verranno visualizzate informazioni sulle app come di seguito:

![][3]

## <a name="send-app-info-from-the-client-sdk"></a>Inviare info app dall'SDK del client
Qui viene usato l'esempio di app universale di Windows ma esistono metodi equivalenti anche per altri SDK. 

Se è disponibile un metodo nell'app per dispositivi mobili che permette di ottenere informazioni sul profilo da parte dell'utente, ad esempio il nome dell'utente dopo la sua autenticazione, chiamare qui il metodo `SendAppInfo` e popolare il valore dell'info app `user_name` registrata in precedenza nel back-end del servizio Mobile Engagement. 

    Dictionary<object, object> appInfo = new Dictionary<object, object>();
    appInfo.Add("user_name", str);
    EngagementAgent.Instance.SendAppInfo(appInfo); 

## <a name="send-personalized-notifications"></a>Inviare notifiche personalizzate
A questo punto è tutto pronto per inviare notifiche con questo **nome_utente**. 

1) Accedere al portale Mobile Engagement nella scheda **Copertura** per creare una notifica; è possibile usare questo segnaposto nel formato seguente in qualsiasi parte del titolo di notifica o nel corpo. 

![][4]    

> [!NOTE]
> Gli utenti per i quali l'info app user_name non è impostata non riceveranno notifiche. Se si esegue una campagna di notifica in modalità di test, e se le informazioni sull’app non sono impostate, verrà inviato un carattere '?' per sostituire il segnaposto. 
> 
> 

2) Quando Mobile Engagement selezionerà un dispositivo a cui inviare la notifica, cercherà le informazioni sulle app e sostituirà il valore nel segnaposto.  
Ad esempio, se è stato impostato `str = "Scott"` per un utente, la registrazione del dispositivo verrà associata alle informazioni sulle app di **nome_utente = SCOTT** per l'utente e l'utente visualizzerà una notifica push dall'app nel formato seguente. 

![][5]    

<!-- Images. -->
[1]: ./media/mobile-engagement-send-personalized-notifications/app-info.png
[2]: ./media/mobile-engagement-send-personalized-notifications/create-app-info.png
[3]: ./media/mobile-engagement-send-personalized-notifications/app-info-user-name.png
[4]: ./media/mobile-engagement-send-personalized-notifications/personal-notification.png
[5]: ./media/mobile-engagement-send-personalized-notifications/notification.png


