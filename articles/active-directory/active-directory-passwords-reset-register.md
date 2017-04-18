---
title: 'Azure AD: registrazione per la reimpostazione password self-service | Microsoft Docs'
description: Registrare i dati di autenticazione per la reimpostazione password self-service
services: active-directory
keywords: gestione delle password in Active Directory, gestione delle password, reimpostazione password self-service di Azure AD, SSPR
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/16/2017
ms.author: joflore
ms.custom: end-user
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 0f10ad4904b10a9554e949e77f0192edcb8f624f
ms.lasthandoff: 04/12/2017


---
# <a name="register-for-self-service-password-reset"></a>Eseguire la registrazione per la reimpostazione password self-service

Se consentito dall'amministratore, un utente finale può reimpostare la propria password o sbloccare il proprio account senza dover contattare nessuno usando la reimpostazione password self-service. Per poter usare questa funzionalità, è prima necessario registrare i metodi di autenticazione o confermare i metodi di autenticazione predefiniti inseriti dall'amministratore.

## <a name="register-or-confirm-authentication-data-with-sspr"></a>Registrare o confermare i dati di autenticazione con la reimpostazione password self-service

1. Aprire il Web browser sul dispositivo e andare alla [pagina di registrazione per la reimpostazione della password](http://aka.ms/ssprsetup).
2. Immettere il nome utente e la password forniti dall'amministratore.
3. A seconda delle opzioni approvate dall'amministratore, verranno visualizzati per la configurazione o la verifica uno o più degli elementi seguenti, che verranno usati se sarà necessario reimpostare la password.
    * Telefono ufficio: questa opzione può essere impostata solo dall'amministratore.
    * Telefono per l'autenticazione: questa opzione dovrà essere impostata su un altro numero di telefono a cui si ha accesso, ad esempio un telefono cellulare che può ricevere SMS o chiamate. Il numero di telefono cellulare dell'utente può essere inserito dall'amministratore, se è stato autorizzato dall'utente a usare tale informazione.
    * Indirizzo di posta elettronica per l'autenticazione: questa opzione dovrà essere impostata su un indirizzo di posta elettronica alternativo a cui si può accedere senza la password che deve essere reimpostata.
    * Domande di sicurezza: questa opzione offre un elenco di domande approvate dall'amministratore a cui l'utente dovrà rispondere. Non è possibile usare la stessa risposta per più domande.
4. Specificare e verificare le informazioni richieste dall'amministratore. Se sono disponibili più opzioni, è consigliabile registrare più metodi per assicurarsi flessibilità quando un altro metodo non è disponibile, ad esempio quando si è in viaggio e non si ha accesso al telefono dell'ufficio.

    ![Registrare i metodi di autenticazione e fare clic su Fine][Register]

5. Dopo aver completato il passaggio 4, scegliere **Fine**. Sarà così possibile usare in futuro la reimpostazione password self-service quando necessario.

I dati eventualmente immessi in Telefono per l'autenticazione o Indirizzo di posta elettronica per l'autenticazione non sono visibili nella directory globale. Possono essere visualizzati soltanto dall'utente stesso e dagli amministratori. Le risposte alle domande di sicurezza possono essere visualizzate solo dall'utente.

Gli amministratori possono richiedere all'utente di confermare i metodi di autenticazione dopo un determinato periodo di tempo per verificare che i metodi registrati siano ancora appropriati.

## <a name="next-steps"></a>Passaggi successivi

* [Come modificare la password usando la reimpostazione password self-service](active-directory-passwords-update-your-own-password.md)
* [Pagina di registrazione per la reimpostazione della password](http://aka.ms/ssprsetup)
* [Portale di reimpostazione della password](https://passwordreset.microsoftonline.com/)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "Pagina di registrazione per la reimpostazione della password con metodi registrati e pulsante Fine"

