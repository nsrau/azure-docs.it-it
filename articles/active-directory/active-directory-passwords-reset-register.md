---
title: 'Azure AD: registrazione per la reimpostazione password self-service | Microsoft Docs'
description: Registrare i dati di autenticazione per la reimpostazione password self-service di Azure AD
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: joflore
ms.custom: end-user
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: bd0175558a94a146a365e8c1a32522f1e54c184e
ms.contentlocale: it-it
ms.lasthandoff: 05/03/2017


---
# <a name="register-for-self-service-password-reset"></a>Eseguire la registrazione per la reimpostazione password self-service

Come utente finale, è possibile reimpostare la password o sbloccare l'account senza la necessità di rivolgersi a un'altra persona usando la funzionalità di reimpostazione della password self-service (SSPR). Per poter usare questa funzionalità, è prima necessario registrare i metodi di autenticazione o confermare i metodi di autenticazione predefiniti inseriti dall'amministratore.

## <a name="register-or-confirm-authentication-data-with-sspr"></a>Registrare o confermare i dati di autenticazione con la reimpostazione password self-service

1. Aprire il Web browser sul dispositivo e andare alla [pagina di registrazione per la reimpostazione della password](http://aka.ms/ssprsetup).
2. Immettere il nome utente e la password forniti dall'amministratore.
3. A seconda delle configurazione definita dal personale IT, una o più delle opzioni seguenti sono disponibili per essere configurate e verificate. L'amministratore può specificare alcune di queste opzioni se ha l'autorizzazione per usare le informazioni.
    * Il telefono ufficio può essere impostato solo dall'amministratore
    * Il telefono per l'autenticazione deve essere impostato su un altro numero di telefono a cui l'utente può accedere, ad esempio un telefono cellulare in grado di ricevere un SMS o una chiamata.
    * L'indirizzo di posta elettronica per l'autenticazione deve essere impostato su un indirizzo di posta elettronica alternativo a cui si può accedere senza la password da reimpostare.
    * Le domande di sicurezza sono costituite da un elenco di domande approvate dall'amministratore a cui l'utente deve rispondere. Non è possibile usare la stessa domanda o rispondere più di una volta.
4. Specificare e verificare le informazioni richieste dall'amministratore. Se sono disponibili più opzioni, è consigliabile registrare più metodi per assicurare flessibilità quando un altro metodo non è disponibile, ad esempio quando si è in viaggio e non si ha accesso al telefono dell'ufficio.

    ![Registrare i metodi di autenticazione e fare clic su Fine][Register]

5. Dopo aver completato il passaggio 4, scegliere **Fine**. Sarà così possibile usare in futuro la reimpostazione password self-service quando necessario.

I dati eventualmente immessi in Telefono per l'autenticazione o Indirizzo di posta elettronica per l'autenticazione non sono visibili nella directory globale. Possono essere visualizzati soltanto dall'utente stesso e dagli amministratori. Le risposte alle domande di sicurezza possono essere visualizzate solo dall'utente.

Gli amministratori possono richiedere all'utente di confermare i metodi di autenticazione dopo un determinato periodo di tempo per verificare che i metodi registrati siano ancora appropriati.

## <a name="next-steps"></a>Passaggi successivi

* [Come modificare la password usando la reimpostazione password self-service](active-directory-passwords-update-your-own-password.md)
* [Pagina di registrazione per la reimpostazione della password](http://aka.ms/ssprsetup)
* [Portale di reimpostazione della password](https://passwordreset.microsoftonline.com/)
* [Non è possibile accedere all'account Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "Pagina di registrazione per la reimpostazione della password con metodi registrati e pulsante Fine"


