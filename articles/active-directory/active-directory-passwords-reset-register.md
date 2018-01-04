---
title: 'Azure AD: registrazione per la reimpostazione password self-service | Microsoft Docs'
description: Registrare i dati di autenticazione per la reimpostazione password self-service di Azure AD
services: active-directory
keywords: 
documentationcenter: 
author: barlanmsft
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: barlan
ms.custom: end-user
ms.openlocfilehash: bae62762decf530521ba10ce684d5db9afa0b86c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="register-for-self-service-password-reset"></a>Eseguire la registrazione per la reimpostazione password self-service

> [!IMPORTANT]
> Se si sta visitando questa pagina perché non si riesce a effettuare l'accesso, vedere [Reimpostare la password aziendale o scolastica](active-directory-passwords-update-your-own-password.md).

Gli utenti finali possono reimpostare la password o sbloccare l'account autonomamente tramite la funzionalità di reimpostazione della password self-service (SSPR) di Azure Active Directory (Azure AD). Per poter usare questa funzionalità, è prima necessario registrare i metodi di autenticazione in uso o confermare i metodi di autenticazione predefiniti inseriti dall'amministratore.

## <a name="register-or-confirm-authentication-data-with-sspr"></a>Registrare o confermare i dati di autenticazione con la reimpostazione password self-service

1. Aprire il Web browser nel dispositivo e andare alla [pagina di registrazione per la reimpostazione della password](http://aka.ms/ssprsetup).
2. Immettere il nome utente e la password forniti dall'amministratore.
3. A seconda della configurazione definita dal personale IT, una o più delle opzioni seguenti sono disponibili per la configurazione e la verifica da parte dell'utente. Se l'utente ha autorizzato l'amministratore a usare le proprie informazioni personali, può compilare alcune opzioni per conto dell'utente stesso.
    * **Telefono ufficio**: solo l'amministratore può impostare questa opzione.
    * **Telefono per l'autenticazione**: impostare questa opzione su un altro numero di telefono a cui si ha accesso, ad esempio un telefono cellulare in grado di ricevere SMS o telefonate.
    * **Indirizzo di posta elettronica per l'autenticazione**: impostare questa opzione su un indirizzo di posta elettronica alternativo a cui si possa accedere senza dover usare la password da reimpostare.
    * **Domande di sicurezza**: elenco di domande approvato dall'amministratore a cui l'utente deve rispondere. Non è possibile usare la stessa domanda o rispondere più volte.
4. Specificare e verificare le informazioni richieste dall'amministratore. Se sono disponibili più opzioni, è consigliabile registrare più metodi, per avere la flessibilità necessaria nel caso in cui uno dei metodi non sia disponibile. Quando si è in viaggio, ad esempio, non è possibile accedere al telefono dell'ufficio.

    ![Registrare i metodi di autenticazione e fare clic su fine][Register]

5. Selezionare **fine**. È ora possibile usare la funzionalità di reimpostazione della password self-service quando necessario.

I dati immessi in **Telefono per l'autenticazione** o **Indirizzo di posta elettronica per l'autenticazione** non sono visibili nella directory globale. Possono essere visualizzati soltanto dall'utente stesso e dagli amministratori. Le risposte alle domande di sicurezza possono essere visualizzate solo dall'utente.

Gli amministratori possono richiedere all'utente di confermare i metodi di autenticazione dopo un determinato periodo di tempo per verificare che i metodi registrati siano ancora appropriati.

## <a name="common-problems-and-their-solutions"></a>Problemi frequenti e relative soluzioni

 Di seguito sono riportati alcuni degli errori più comuni e le relative soluzioni:

| Scenario di errore| Tipo di errore visualizzato| Soluzione |
| --- | --- | --- |
| Dopo aver immesso l'ID utente viene visualizzata una pagina "Contattare l'amministratore" | Contattare l'amministratore. <br> <br> È stato rilevato che la password dell'account utente non è gestita da Microsoft. Di conseguenza, non può essere reimpostata automaticamente. <br> <br> Contattare il personale IT per ricevere ulteriore assistenza. | Questo messaggio viene visualizzato perché il personale IT gestisce la password nell'ambiente locale e non consente di reimpostarla dal collegamento **Problemi di accesso all'account?**. <br> <br> Per ricevere assistenza nella reimpostazione della password, contattare direttamente il personale IT e richiedere che venga abilitata la funzionalità di reimpostazione della password per il proprio account utente.|
| Dopo aver immesso l'ID utente viene visualizzato il messaggio di errore "Account non abilitato per la reimpostazione della password"  | Account non abilitato per la reimpostazione della password. <br> <br> Il personale IT non ha configurato l'account per l'uso di questo servizio. <br> <br> Se necessario, Microsoft può contattare un amministratore dell'organizzazione e richiedere la reimpostazione della password. | Questo messaggio viene visualizzato perché il personale IT non ha abilitato la reimpostazione della password per l'organizzazione dal collegamento **Problemi di accesso all'account?** o non ha autorizzato l'utente a usare questa funzionalità. <br> <br> Per reimpostare la password, selezionare il collegamento **contattare un amministratore**. Verrà inviato un messaggio di posta elettronica al personale IT della società con la richiesta dell'utente che venga abilitata la funzionalità di reimpostazione della password per il proprio account. |
| Dopo aver immesso l'ID utente viene visualizzato il messaggio di errore "Non è stato possibile verificare l'account". | Non è stato possibile verificare l'account. <br> <br> Se necessario, Microsoft può contattare un amministratore dell'organizzazione e richiedere la reimpostazione della password. | Questo messaggio viene visualizzato perché la reimpostazione self-service della password è abilitata, ma l'utente non è stato registrato per l'uso del servizio. Per registrarsi per la reimpostazione della password, dopo aver recuperato l'accesso all'account andare alla [pagina di registrazione per la reimpostazione della password](http://aka.ms/ssprsetup). <br> <br> Per reimpostare la password, inviare un messaggio di posta elettronica al personale IT della società selezionando il collegamento **contattare un amministratore**. |

## <a name="next-steps"></a>Passaggi successivi

* [Modificare la password usando la reimpostazione della password self-service](active-directory-passwords-update-your-own-password.md)
* [Pagina di registrazione per la reimpostazione della password](http://aka.ms/ssprsetup)
* [Portale di reimpostazione della password](https://passwordreset.microsoftonline.com/)
* [Se non si riesce ad accedere all'account Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "Pagina di registrazione per la reimpostazione della password con i metodi registrati e il pulsante fine"

