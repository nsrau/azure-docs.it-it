---
title: Anteprima di Collaborazione B2B di Azure Active Directory | Microsoft Docs
description: "Collaborazione B2B di Azure Active Directory supporta le relazioni tra società consentendo ai partner commerciali di accedere in modo selettivo alle applicazioni aziendali."
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
ms.date: 03/18/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 4bfd76a00ba694096ef0b045b14beb757ba0809f
ms.lasthandoff: 03/21/2017


---

# <a name="about-the-azure-ad-b2b-collaboration-preview"></a>Informazioni sull'anteprima di Collaborazione B2B di Azure AD
Questo articolo illustra gli obiettivi, le funzionalità e i vantaggi dell'aggiornamento dell'anteprima pubblica di Collaborazione B2B di Azure Active Directory (Azure AD).

Sia i professionisti IT sia gli information worker possono sfruttare molte funzionalità di Collaborazione B2B di Azure Active Directory. È possibile lavorare a stretto contatto con partner di altre organizzazioni ovunque si trovino, indipendentemente dalla loro dimensione, settore o requisiti di conformità e governance. Ed è possibile fornire loro l'accesso a documenti, risorse e applicazioni, mantenendo il controllo completo sui propri dati interni.

Gli sviluppatori possono usare le API di Azure AD B2B per scrivere applicazioni che permettono alle organizzazioni di collaborare in modo sicuro. Per gli utenti che sono information worker l'elaborazione e la navigazione sono semplificate.

## <a name="how-b2b-collaboration-works"></a>Come funziona Collaborazione B2B

Nella versione di anteprima corrente, i professionisti IT e gli information worker stabiliscono relazioni con le organizzazioni partner aggiungendo i loro utenti uno o pochi alla volta tramite il portale di Azure o l'API di gestione degli inviti.

Per stabilire relazioni gli amministratori possono usare gli strumenti nel nuovo portale di Azure (https://portal.azure.com) e PowerShell.

Gli information worker possono usare gli strumenti del riquadro di accesso all'indirizzo http://myapps.microsoft.com.

Gli sviluppatori possono creare applicazioni tramite l'API di gestione degli inviti in B2B di Azure AD per aggiungere utenti di Collaborazione B2B e personalizzare i flussi di lavoro di invito e onboarding.

Gli utenti di Collaborazione B2B vengono generalmente aggiunti attraverso una procedura di invito e riscatto. Ecco come funziona:

1. John Doe di WoodGrove vuole aggiungere Sam Oogle come utente con il suo indirizzo Gmail (gsamoogle@gmail.com).

2. John Doe apre il portale WoodGrove (portal.azure.com) o il riquadro di accesso (myapps.microsoft.com), esegue l'accesso e quindi aggiunge l'utente Sam Oogle alla directory, al gruppo o all'applicazione WoodGrove.

3. John Doe specifica un messaggio di invito personalizzato da inviare a Sam Oogle.

4. Quando John Doe invia l'invito, in Azure AD viene creato l'utente Sam Oogle per WoodGrove, come illustrato di seguito:

  ![Interfaccia dell'utente amministratore in portal.azure.com](media/active-directory-b2b-what-is-azure-ad-b2b/user-is-added.png)

5. Dopo aver creato l'utente, Azure AD invia un invito tramite posta elettronica a Sam Oogle:

  ![E-mail di invito inviata a Sam Oogle](media/active-directory-b2b-what-is-azure-ad-b2b/invitation-mail-sent-to-sam.png)

6. Nell'invito Sam Oogle seleziona **Introduzione**.  
Il portale di Azure si apre.

7. SAM Oogle accede al portale di Azure.

8. Azure AD Aggiorna l'oggetto utente Sam Oogle in Azure AD con le informazioni del suo token, come illustrato di seguito:

  ![Il profilo utente di SAM Oogle nel portale di Azure](media/active-directory-b2b-what-is-azure-ad-b2b/user-profile-is-populated.png)

9. Ora che l'utente Sam Oogle ha riscattato l'invito, potrà accedere alle risorse WoodGrove. E come qualsiasi altro utente in Azure AD, l'utente Sam Oogle può essere gestito dall'amministratore. L'elenco degli utenti è riportato di seguito:

  ![Elenco degli utenti di Azure AD](media/active-directory-b2b-what-is-azure-ad-b2b/sam-now-user-in-azure-ad.png)

## <a name="public-preview-features"></a>Funzionalità di anteprima pubblica
In base ai commenti degli utenti, l'aggiornamento di anteprima pubblica di Collaborazione B2B fornisce le seguenti funzionalità principali:

* Per gli amministratori: ottenere miglioramenti dell'interfaccia utente nel [portale Azure](https://portal.azure.com). Gli amministratori, per esempio, possono invitare gli utenti B2B nella directory o in qualsiasi gruppo o applicazione.  

* Per gli information worker: ottenere funzionalità di inviti self-service di Collaborazione B2B nel [riquadro di accesso](https://myapps.microsoft.com). Gli Information Worker possono invitare gli utenti di Collaborazione B2B in un qualsiasi gruppo o applicazione self-service da loro gestiti.

* Consentire agli utenti invitati di avere qualsiasi indirizzo di posta elettronica. Che sia un indirizzo di Office 365 o locale di Microsoft Exchange, un indirizzo outlook.com o qualsiasi indirizzo di social network (Gmail, Yahoo e così via), gli utenti possono accedere all'organizzazione invitata con la creazione di un account Azure AD o Microsoft.

* Creare un'e-mail di invito professionale e con marchio tenant.

* Personalizzare l'orientamento utente tramite le API di invito.

* Configurare l'autenticazione a più fattori per gli utenti di Collaborazione B2B nell'organizzazione che ha inviato l'invito.

* Delegare gli inviti agli utenti non amministratori.

* Fornire il supporto di PowerShell per Collaborazione B2B.

* Fornire funzionalità di controllo e creazione di report.

## <a name="help-us-shape-your-features"></a>Aiutaci a creare le funzionalità che ti servono
 
Ascoltiamo costantemente commenti e suggerimenti su come migliorare la collaborazione B2B. Partecipa alla discussione, condividi gli scenari di utente e le procedure consigliate che conosci e comunicaci cosa apprezzi di Collaborazione B2B di Azure AD su [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b)
 
Invia le tue idee e vota le funzionalità future sul sito [B2B Collaboration Ideas](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas).

## <a name="next-steps"></a>Passaggi successivi

Vedere gli altri articoli su Azure AD B2B Collaboration.

* [Procedura per aggiungere utenti di Collaborazione B2B ad Azure Active Directory da parte degli amministratori](active-directory-b2b-admin-add-users.md)
* [Procedura di aggiunta di utenti di Collaborazione B2B da parte di information worker](active-directory-b2b-iw-add-users.md)
* [Elementi del messaggio di posta elettronica di invito per la Collaborazione B2B](active-directory-b2b-invitation-email.md)
* [Riscatto dell'invito di Collaborazione B2B](active-directory-b2b-redemption-experience.md)
* [Licenze per la Collaborazione B2B di Azure AD](active-directory-b2b-licensing.md)
* [Risoluzione dei problemi di Collaborazione B2B di Azure Active Directory](active-directory-b2b-troubleshooting.md)
* [Domande frequenti su Collaborazione B2B di Azure Active Directory](active-directory-b2b-faq.md)
* [API e personalizzazione per Collaborazione B2B di Azure Active Directory](active-directory-b2b-api.md)
* [Autenticazione a più fattori per utenti di Collaborazione B2B](active-directory-b2b-mfa-instructions.md)
* [Aggiungere gli utenti per la Collaborazione B2B senza un invito](active-directory-b2b-add-user-without-invite.md)
* [Controllo e creazione di report per un utente di Collaborazione B2B](active-directory-b2b-auditing-and-reporting.md)
* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)

