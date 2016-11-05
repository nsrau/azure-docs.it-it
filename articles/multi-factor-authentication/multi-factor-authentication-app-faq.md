---
title: Domande frequenti sull'app Microsoft Authenticator
description: Elenca le domande frequenti e le risposte correlate all'app Microsoft Authentication e ad Azure Multi-Factor Authentication.
services: multi-factor-authentication
documentationcenter: ''
author: kgremban
manager: femila
editor: pblachar, librown

ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2016
ms.author: kgremban

---
# Domande frequenti sull'applicazione Microsoft Authenticator
L'app Microsoft Authenticator ha sostituito l'app Azure Authenticator ed è consigliata quando si usa Azure Multi-Factor Authentication. Questa applicazione è disponibile per Windows Phone, Android e iOS.

## Domande frequenti
* **Sto già usando l'app Microsoft Authenticator per i codici di sicurezza. Come posso attivare le notifiche push con un clic?**
  
    Se si usa un account Microsoft per l'account personale e si vuole passare alle notifiche push, è necessario aggiungere nuovamente l'account perché l'app usa una password monouso. Registrare nuovamente il dispositivo con il proprio account e impostare le notifiche push.
  
    Se per l'account non è attivata la verifica in due passaggi, vedere [Informazioni sulla verifica in due passaggi](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) per decidere se è adatta alle proprie esigenze.
* **Quando potrò usare le notifiche push con un clic su iPhone o iPad?**
  
    Questa funzionalità è in versione beta fino alla fine di agosto, quando verrà resa disponibile per gli account Microsoft. Se si vuole partecipare al programma beta, inviare un messaggio di posta elettronica all'indirizzo msauthenticator@microsoft.com. Includere nome, cognome e ID Apple nel messaggio.
* **Le notifiche push con un clic funzionano con gli account non Microsoft?**
  
    No, le notifiche push funzionano solo con gli account Microsoft e gli account Azure Active Directory. Se l'azienda o l'istituto di istruzione usa account di Azure AD, è possibile che disabilitino questa funzionalità.
* **Ho ripristinato il mio dispositivo da una copia di backup e i codici del mio account mancano o non funzionano. Che cosa è successo?**
  
    A scopo di sicurezza, in questo momento gli account non vengono ripristinati dalle copie di backup dell'app. Se si ripristina l'app per iOS da una copia di backup, gli account vengono comunque visualizzati ma non possono ricevere le verifiche di accesso o generare i codici di sicurezza. Dopo aver ripristinato l'app, eliminare gli account e aggiungerli di nuovo.

## Argomenti correlati
* [Domande frequenti su Azure Multi-Factor Authentication](multi-factor-authentication-faq.md)
* [Informazioni sulla verifica in due passaggi](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) per l'account Microsoft
* [Domande frequenti sulle app di verifica dell'identità](https://support.microsoft.com/help/12414/microsoft-account-identity-verification-apps-faq)

<!---HONumber=AcomDC_0921_2016-->