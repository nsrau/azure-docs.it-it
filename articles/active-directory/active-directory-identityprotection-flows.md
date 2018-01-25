---
title: Esperienze di accesso con Azure AD Identity Protection | Microsoft Docs
description: "Presenta una panoramica dell'esperienza utente quando Identity Protection ha mitigato o risolto la situazione di rischio di un utente o quando l'autenticazione a più fattori è richiesta da una policy."
services: active-directory
keywords: "azure active directory identity protection, cloud app discovery, gestione applicazioni, sicurezza, rischio, livello di rischio, vulnerabilità, criteri di sicurezza"
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: de5bf637-75a7-4104-b6d8-03686372a319
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 558d1e04e352d5f33201bb15cb7a2dd1c18f524f
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2018
---
# <a name="sign-in-experiences-with-azure-ad-identity-protection"></a>Esperienze di accesso con Azure AD Identity Protection
Con Azure Active Directory Identity Protection è possibile:

* richiedere la registrazione degli utenti per l'autenticazione a più fattori
* gestire gli accessi rischiosi e gli utenti compromessi

La risposta del sistema a questi problemi ha un impatto sull’esperienza di accesso dell’utente, in quanto non sarà più possibile effettuare l’accesso in modo diretto fornendo semplicemente il nome utente e la password. Saranno necessari passaggi aggiuntivi per consentire all’utente un accesso sicuro al sistema.

Questo argomento presenta una panoramica dell'esperienza di accesso dell'utente per tutti i casi possibili.

**Autenticazione a più fattori**

* Registrazione per l'autenticazione a più fattori

**Accesso a rischio**

* Ripristino di un accesso rischioso
* Accesso rischioso bloccato
* Registrazione per l'autenticazione a più fattori durante un accesso rischioso

**Utente a rischio**

* Ripristino di account compromessi
* Account compromesso bloccato

## <a name="multi-factor-authentication-registration"></a>Registrazione per l'autenticazione a più fattori
Sia per il ripristino di un account compromesso che per l'accesso rischioso, la migliore esperienza utente si ottiene quando l'utente può eseguire il ripristino automatico. Se gli utenti sono registrati per l’autenticazione a più fattori, hanno già un numero di telefono associato con l’account che può essere usato per trasmettere le richieste di sicurezza. Non è necessario coinvolgere il supporto tecnico o l'amministratore per ripristinare un account compromesso. È quindi consigliabile fare in modo che gli utenti siano registrati per l'autenticazione a più fattori. 

Gli amministratori possono:

* Impostare criteri che richiedono agli utenti di configurare l'account per la verifica di sicurezza aggiuntiva. 
* Consentire di ignorare la registrazione per l'autenticazione a più fattori per un massimo di 30 giorni, per dare agli utenti un periodo di tolleranza prima della registrazione.

**La registrazione per l’autenticazione a più fattori prevede tre passaggi:**

1. Nel primo passaggio l'utente riceve una notifica della necessità di impostare l'account per l’autenticazione a più fattori. 
   
    ![Correzione](./media/active-directory-identityprotection-flows/140.png "Correzione")
2. Per impostare l'autenticazione a più fattori, occorre indicare al sistema in che modo si desidera essere contattati.
   
    ![Correzione](./media/active-directory-identityprotection-flows/141.png "Correzione")
3. Il sistema invia una richiesta ed è necessario rispondere.
   
    ![Correzione](./media/active-directory-identityprotection-flows/142.png "Correzione")

## <a name="risky-sign-in-recovery"></a>Ripristino di un accesso rischioso
Se un amministratore ha configurato dei criteri per i rischi di accesso, gli utenti interessati ricevono una notifica quando provano ad accedere. 

**Il flusso per l'accesso rischioso prevede due passaggi:** 

1. L’utente è informato del fatto che qualcosa di insolito è stato rilevato in merito al suo accesso, ad esempio l’accesso da una nuova posizione, dispositivo o app. 
   
    ![Correzione](./media/active-directory-identityprotection-flows/120.png "Correzione")
2. L'utente deve dimostrare la propria identità rispondendo a una richiesta di sicurezza. Se l'utente è registrato per l'autenticazione a più fattori, deve eseguire il round trip di un codice di sicurezza al proprio numero di telefono. Poiché questo è solo un accesso rischioso e non un account compromesso, l’utente non dovrà cambiare la password in questo flusso. 
   
    ![Correzione](./media/active-directory-identityprotection-flows/121.png "Correzione")

## <a name="risky-sign-in-blocked"></a>Accesso rischioso bloccato
Gli amministratori possono anche impostare criteri di rischio di accesso per bloccare gli utenti al momento dell'accesso in base al livello di rischio. Per essere sbloccati, gli utenti finali devono contattare un amministratore o il supporto tecnico oppure possono provare a eseguire l'accesso da una posizione o un dispositivo noto. Il ripristino automatico tramite l'autenticazione a più fattori non è possibile in questo caso.

![Correzione](./media/active-directory-identityprotection-flows/200.png "Correzione")

## <a name="compromised-account-recovery"></a>Ripristino di account compromessi
Dopo che sono stati configurati criteri di sicurezza per il rischio utente, gli utenti che rientrano nel livello di rischio utente specificato nei criteri, e quindi considerati compromessi, devono seguire il flusso di ripristino di utenti compromessi per poter eseguire l'accesso. 

**Il flusso di ripristino di utenti compromessi è composto da tre passaggi:**

1. L'utente viene informato che la sicurezza dell'account è a rischio a causa di attività sospette o di credenziali perse.
   
    ![Correzione](./media/active-directory-identityprotection-flows/101.png "Correzione")
2. L'utente deve dimostrare la propria identità rispondendo a una richiesta di sicurezza. Se l'utente è registrato per l'autenticazione a più fattori può eseguire il ripristino automatico da eventuali compromissioni. Deve eseguire il round trip di un codice di sicurezza al proprio numero di telefono. 
   
   ![Correzione](./media/active-directory-identityprotection-flows/110.png "Correzione")
3. Infine, all'utente viene richiesto di modificare la password perché qualcun altro potrebbe aver avuto accesso all'account. 
   Sono incluse per riferimento le screenshot di questa esperienza.
   
   ![Correzione](./media/active-directory-identityprotection-flows/111.png "Correzione")

## <a name="compromised-account-blocked"></a>Account compromesso bloccato
Per sbloccare un utente bloccato da criteri di sicurezza per il rischio utente, è necessario contattare un amministratore o il supporto tecnico. Il ripristino automatico tramite l'autenticazione a più fattori non è possibile in questo caso.

![Correzione](./media/active-directory-identityprotection-flows/104.png "Correzione")

## <a name="reset-password"></a>Reimposta password
Se l’accesso degli utenti compromessi è bloccato, un amministratore può generare una password temporanea per consentire l’accesso. Gli utenti dovranno modificare la password all'accesso successivo.

![Correzione](./media/active-directory-identityprotection-flows/160.png "Correzione")

## <a name="see-also"></a>Vedere anche 
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md) 

