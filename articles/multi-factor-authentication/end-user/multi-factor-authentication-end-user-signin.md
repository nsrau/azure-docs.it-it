---
title: Accesso con approccio Azure MFA con verifica in due passaggi | Microsoft Docs
description: Questa pagina offre indicazioni su dove trovare le istruzioni per visualizzare i diversi metodi di accesso disponibili con Azure MFA.
keywords: autenticazione utente, esperienza di accesso con telefono cellulare, accesso con telefono dell&quot;ufficio
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: pblachar
ms.assetid: b310b762-471b-4b26-887a-a321c9e81d46
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: e972446ea92e8fd31406c9ccff7832b7441f3a2a
ms.lasthandoff: 03/04/2017


---
# <a name="the-sign-in-experience-with-azure-multi-factor-authentication"></a>Esperienza di accesso con Azure Multi-Factor Authentication
> [!NOTE]
> Lo scopo di questo articolo è di illustrare un'esperienza di accesso tipico. Per informazioni sull’accesso o per la risoluzione dei problemi, vedere [Problemi con Multi-Factor Authentication di Azure](multi-factor-authentication-end-user-troubleshoot.md).

## <a name="what-will-your-sign-in-experience-be"></a>Come sarà l'esperienza di accesso?
L'esperienza di accesso varia a seconda di ciò che si desidera usare come secondo fattore: una telefonata, un'app di autenticazione o un messaggio. Scegliere l'opzione che meglio descrive l'operazione:

| Come si accede? | 
| --- |
| [Con una telefonata al cellulare o al telefono dell'ufficio](#signing-in-with-a-phone-call) |
| [Con un messaggio sul cellulare](#signing-in-with-a-text-message)
| [Con le notifiche dell'app Microsoft Authenticator](#signing-in-with-the-microsoft-authenticator-app-using-notification) |
| [Con i codici di verifica dell'app Microsoft Authenticator](#signing-in-with-the-microsoft-authenticator-app-using-verification-code) |
| [Con un metodo alternativo, perché non è possibile usare subito il metodo preferito](#signing-in-with-an-alternate-method) |

## <a name="signing-in-with-a-phone-call"></a>Accesso tramite telefonata
Le informazioni seguenti descrivono l'esperienza di verifica in due passaggi con una telefonata al cellulare o al telefono dell'ufficio.

1. Accedere a un'applicazione o servizio come Office 365 usando nome utente e password.  
2. Microsoft telefona all'utente.  
3. Rispondere al telefono e premere il tasto #.  
4. Ora dovrebbe essere stato effettuato l'accesso.  

## <a name="signing-in-with-a-text-message"></a>Accesso tramite messaggio di testo
Le informazioni seguenti descrivono l'esperienza di verifica in due passaggi tramite messaggio di testo al telefono cellulare:

1. Accedere a un'applicazione o servizio come Office 365 usando nome utente e password. 
2. Microsoft invia un messaggio di testo che contiene un codice numerico. 
3. Immettere il codice nell'apposita casella sulla pagina di accesso. 
4. Ora dovrebbe essere stato effettuato l'accesso. 

## <a name="signing-in-with-the-microsoft-authenticator-app"></a>Accesso con l'app Authenticator Microsoft 
Le informazioni seguenti descrivono l'esperienza d'uso dell'app Microsoft Authenticator per le verifiche in due passaggi. Esistono due modi diversi di usare l'app. È possibile ricevere notifiche push sul dispositivo oppure aprire l'app per ottenere un codice di verifica.

### <a name="to-sign-in-with-a-notification-from-the-microsoft-authenticator-app"></a>Per eseguire l'accesso con una notifica dell'app Microsoft Authenticator
1. Accedere a un'applicazione o servizio come Office 365 usando nome utente e password.
2. Microsoft invia una notifica all'app Microsoft Authenticator sul dispositivo dell'utente.

  ![Microsoft invia una notifica](./media/multi-factor-authentication-end-user-signin/notify.png)

3. Aprire la notifica sul telefono e quindi selezionare la chiave **Verifica**. Se la società richiede un PIN, immetterlo qui.
4. Ora dovrebbe essere stato effettuato l'accesso.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>Per effettuare l'accesso usando un codice di verifica con l'app Microsoft Authenticator

Se si usa l'app Microsoft Authenticator per ottenere i codici di verifica, quando si apre l'app verrà visualizzato un numero nel nome dell'account. Questo numero cambia ogni trenta secondi, pertanto non è possibile usare lo stesso numero di due volte. Quando viene richiesto un codice di verifica, aprire l'app e utilizzare il numero attualmente visualizzato. 

1. Accedere a un'applicazione o servizio come Office 365 usando nome utente e password.
2. Microsoft richiede un codice di verifica.

  ![Inserire il codice di verifica dell'app](./media/multi-factor-authentication-end-user-signin/verify3.png)

3. Aprire l'app Microsoft Authenticator sul telefono e immettere il codice nella casella da cui si sta effettuando l'accesso.
4. Ora dovrebbe essere stato effettuato l'accesso.

## <a name="signing-in-with-an-alternate-method"></a>Accesso con un metodo alternativo
A volte non si dispone del telefono o del dispositivo configurato come metodo di verifica preferito. Per questo motivo è consigliabile configurare metodi di backup per l'account. Nella sezione seguente viene mostrato come effettuare l'accesso con un metodo alternativo quando il metodo principale può non essere disponibile.

1. Accedere a un'applicazione o servizio come Office 365 usando nome utente e password.
2. Selezionare **Usa un'opzione di verifica diversa**. Verranno visualizzate diverse opzioni di verifica in base al numero di opzioni configurate.

  ![Utilizzare un metodo alternativo](./media/multi-factor-authentication-end-user-signin/alt.png)

3. Scegliere un metodo alternativo ed effettuare l’accesso.

## <a name="next-steps"></a>Passaggi successivi

In caso di problemi di accesso con la verifica in due passaggi, è possibile ottenere altre informazioni in [Problemi con Multi-Factor Authentication di Azure](multi-factor-authentication-end-user-troubleshoot.md).

Informazioni su come [Gestire le impostazioni della verifica in due passaggi](multi-factor-authentication-end-user-manage-settings.md).

Altre informazioni sull'[introduzione all'app Microsoft Authenticator](microsoft-authenticator-app-how-to.md) per poter effettuare l'accesso tramite notifiche, invece che con messaggi e telefonate. 
