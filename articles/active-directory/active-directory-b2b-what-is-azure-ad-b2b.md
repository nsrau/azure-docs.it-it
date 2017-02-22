---
title: "Che cos&quot;è l&quot;anteprima di Collaborazione B2B di Azure Active Directory? | Documentazione Microsoft"
description: "La collaborazione B2B di Azure Active Directory supporta le relazioni tra società abilitando i partner commerciali ad accedere in modo selettivo alle applicazioni aziendali"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 1464387b-ee8b-4c7c-94b3-2c5567224c27
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/02/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 1c1da3c7b1d5e096e03ac3814275f29afeffcaeb
ms.openlocfilehash: ad03b71052be46bdb64432b007aa471d120db0fa


---

# <a name="what-is-azure-ad-b2b-collaboration-preview"></a>Che cos'è l'anteprima di Collaborazione B2B di Azure AD?

Le funzionalità di Collaborazione B2B di Azure AD consentono a professionisti di IT e Information Worker di lavorare a stretto contatto con i partner in qualsiasi altra organizzazione del mondo. Essi possono infatti offrire accesso a documenti, risorse e applicazioni, mantenendo il controllo completo sui propri dati interni. Gli sviluppatori possono usare le API Business to Business di Azure AD per scrivere applicazioni in modo da consolidare e uniformare in maniera sicura e fluida la navigazione e l'aspetto di due organizzazioni diverse per gli Information Worker.

Le funzionalità di Collaborazione B2B di Azure AD consentono a organizzazioni di ogni dimensione e settore di lavorare con facilità e sicurezza con collaboratori in tutto il mondo, a prescindere dai requisiti di conformità e governance. Questo è l'obiettivo dell'aggiornamento dell'anteprima pubblica di Collaborazione B2B.

## <a name="how-does-it-work"></a>Come funziona?

Nella versione di anteprima corrente, per stabilire una relazione con un'organizzazione i professionisti IT e gli Information Worker possono aggiungere uno o più utenti alla volta da un'organizzazione all'altra tramite il portale o l'API di gestione degli inviti. Per farlo, gli amministratori possono usare gli strumenti nel nuovo portale di Azure (https://portal.azure.com) e PowerShell. Gli Information Worker possono usare gli strumenti del riquadro di accesso all'indirizzo http://myapps.microsoft.com. Gli sviluppatori possono creare applicazioni tramite l'API di gestione degli inviti in B2B di Azure AD per aggiungere utenti di Collaborazione B2B e personalizzare i flussi di lavoro di invito e onboarding.

Il processo di onboarding per gli utenti di Collaborazione B2B è generalmente composto da una fase di invito e una fase di riscatto e funziona nel modo seguente:

1. John Doe di WoodGrove vuole aggiungere Sam Oogle usando il suo indirizzo Gmail (gsamoogle@gmail.com)

2. John apre il portale WoodGrove (portal.azure.com) o il riquadro di accesso (myapps.microsoft.com), effettua l'accesso e aggiunge l'utente alla directory, al gruppo o all'applicazione WoodGrove.

3. John specifica un messaggio di invito personalizzato da inviare a Sam.

4. Fatto ciò, viene creato l'utente seguente in AD di WoodGrove. Lo screenshot fa riferimento all'esperienza utente per l'amministratore all'indirizzo portal.azure.com:

  ![L'utente viene aggiunto](media/active-directory-b2b-what-is-azure-ad-b2b/user-is-added.png)

5. Non appena l'utente viene aggiunto, Azure AD invia un'e-mail di invito a Sam:

  ![E-mail di invito inviata a Sam](media/active-directory-b2b-what-is-azure-ad-b2b/invitation-mail-sent-to-sam.png)

6. Ora Sam seleziona **Introduzione** ed esegue l'accesso. A questo punto, Azure AD aggiorna l'oggetto utente di Sam nella directory con le informazioni del suo token. Lo screenshot fa riferimento all'esperienza utente per l'amministratore nel [portale di Azure](https://portal.azure.com):

  ![Il profilo utente viene popolato](media/active-directory-b2b-what-is-azure-ad-b2b/user-profile-is-populated.png)

7. Ora che l'invito di Sam è stato riscattato, Sam può accedere alle risorse di WoodGrove e può essere gestito dall'amministratore come ogni altro utente nella directory. Lo screenshot fa riferimento all'esperienza utente per l'amministratore nel [portale di Azure](https://portal.azure.com):

  ![Sam è ora un utente in Azure AD](media/active-directory-b2b-what-is-azure-ad-b2b/sam-now-user-in-azure-ad.png)

## <a name="public-preview-features"></a>Funzionalità dell'anteprima pubblica
Gli utenti hanno reagito molto positivamente alle funzionalità di Collaborazione B2B presenti nell'anteprima pubblica. Abbiamo raccolto e letto con cura le loro opinioni e pertanto stiamo creando dei pacchetti con tutti i miglioramenti apportati in questo aggiornamento dell'anteprima pubblica. Ecco le funzionalità chiave presenti nell'aggiornamento dell'anteprima pubblica di Collaborazione B2B:

1. Miglioramenti all'esperienza B2B per l'amministratore
  - alla pagina https://portal.azure.com
  - Possibilità per gli amministratori di invitare utenti B2B in una directory, in un gruppo o in un'applicazione.

2. Funzionalità di inviti self-service a Collaborazione B2B per gli Information Worker nel riquadro di accesso: https://myapps.microsoft.com. Gli Information Worker possono invitare gli utenti di Collaborazione B2B in un qualsiasi gruppo o applicazione self-service da loro gestiti.

3. È ora possibile invitare un utente con un qualsiasi indirizzo di posta elettronica. A prescindere dall'indirizzo usato dall'utente, ad esempio un indirizzo Office365, Microsoft Exchange locale, outlook.com o di social network come Gmail e Yahoo, egli può accedere senza problemi all'organizzazione a cui è stato invitato creando inline e in tutta semplicità un account Azure AD o un account Microsoft.

4. Possibilità di sfruttare un'e-mail di invito professionale e con marchio tenant.

5. Ampia possibilità di personalizzare l'onboarding usando le API di invito.

6. Autenticazione a più fattori per gli utenti di Collaborazione B2B nell'organizzazione che ha inviato l'invito.

7. Possibilità di delegare gli inviti agli utenti non amministratori.

8. Supporto di PowerShell per la Collaborazione B2B.

9. Funzionalità di controllo e creazione di report.

## <a name="next-steps"></a>Passaggi successivi

Vedere gli altri articoli su Azure AD B2B Collaboration.

* [Che cos'è Azure AD B2B Collaboration?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Procedura per aggiungere utenti di Collaborazione B2B ad Azure Active Directory da parte degli amministratori](active-directory-b2b-admin-add-users.md)
* [Procedura per aggiungere utenti di Collaborazione B2B da parte di Information Worker](active-directory-b2b-how-it-works.md)
* [Elementi del messaggio di posta elettronica di invito per la Collaborazione B2B](active-directory-b2b-invitation-email.md)
* [Riscatto dell'invito di Collaborazione B2B](active-directory-b2b-redemption-experience.md)
* [Licenze per la Collaborazione B2B di Azure AD](active-directory-b2b-licensing.md)
* [Risoluzione dei problemi di Collaborazione B2B di Azure Active Directory](active-directory-b2b-troubleshooting.md)
* [Domande frequenti su Collaborazione B2B di Azure Active Directory](active-directory-b2b-faq.md)
* [API e personalizzazione per Collaborazione B2B di Azure Active Directory](active-directory-b2b-api.md)
* [Autenticazione a più fattori per utenti di Collaborazione B2B](active-directory-b2b-mfa-instructions.md)
* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)



<!--HONumber=Feb17_HO1-->


