---
title: Azure MFA Accedi con la verifica in due passaggi - Azure Active Directory | Microsoft Docs
description: Questa pagina offre indicazioni su dove trovare le istruzioni per visualizzare i diversi metodi di accesso disponibili con Azure MFA.
keywords: autenticazione utente, esperienza di accesso con telefono cellulare, accesso con telefono dell'ufficio
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: b310b762-471b-4b26-887a-a321c9e81d46
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 04/02/2017
ms.author: lizross
ms.reviewer: librown
ms.custom: end-user, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1350b2d86e18f213d99f1c27d64e371451f5f9b7
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57840877"
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
| [Con le notifiche dell'app Microsoft Authenticator](#to-sign-in-with-a-notification-from-the-microsoft-authenticator-app) |
| Con i codici di verifica dell'app Microsoft Authenticator |
| [Con un metodo alternativo, perché non è possibile usare subito il metodo preferito](#signing-in-with-an-alternate-method) |

## <a name="signing-in-with-a-phone-call"></a>Accesso tramite telefonata
Le informazioni seguenti descrivono l'esperienza di verifica in due passaggi con una telefonata al cellulare o al telefono dell'ufficio.

1. Accedere a un'applicazione o servizio come Office 365 usando nome utente e password.  
2. Microsoft telefona all'utente.  
3. Rispondere al telefono e premere il tasto #.  

## <a name="signing-in-with-a-text-message"></a>Accesso tramite messaggio di testo
Le informazioni seguenti descrivono l'esperienza di verifica in due passaggi tramite messaggio di testo al telefono cellulare:

1. Accedere a un'applicazione o servizio come Office 365 usando nome utente e password.
2. Microsoft invia un messaggio di testo che contiene un codice numerico.
3. Immettere il codice nell'apposita casella sulla pagina di accesso.

## <a name="signing-in-with-the-microsoft-authenticator-app"></a>Accesso con l'app Authenticator Microsoft
Le informazioni seguenti descrivono l'esperienza d'uso dell'app Microsoft Authenticator per le verifiche in due passaggi. Esistono due modi diversi di usare l'app. È possibile ricevere notifiche push sul dispositivo oppure aprire l'app per ottenere un codice di verifica.

### <a name="to-sign-in-with-a-notification-from-the-microsoft-authenticator-app"></a>Per eseguire l'accesso con una notifica dell'app Microsoft Authenticator
1. Accedere a un'applicazione o servizio come Office 365 usando nome utente e password.
2. Microsoft invia una notifica all'app Microsoft Authenticator sul dispositivo dell'utente.

   ![Microsoft invia una notifica](./media/multi-factor-authentication-end-user-signin/notify.png)

3. Aprire la notifica sul telefono e quindi selezionare la chiave **Verifica**. Se la società richiede un PIN, immetterlo qui.
4. Ora dovrebbe essere stato effettuato l'accesso.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>Per effettuare l'accesso usando un codice di verifica con l'app Microsoft Authenticator

Se si usa l'app Microsoft Authenticator per ottenere i codici di verifica, quando si apre l'app viene visualizzato un numero nel nome dell'account. Questo numero cambia ogni trenta secondi, pertanto non è possibile usare lo stesso numero di due volte. Quando viene richiesto un codice di verifica, aprire l'app e utilizzare il numero attualmente visualizzato.

1. Accedere a un'applicazione o servizio come Office 365 usando nome utente e password.
2. Microsoft richiede un codice di verifica.

   ![Inserire il codice di verifica dell'app](./media/multi-factor-authentication-end-user-signin/verify3.png)

3. Aprire l'app Microsoft Authenticator sul telefono e immettere il codice nella casella da cui si sta effettuando l'accesso.

## <a name="signing-in-with-an-alternate-method"></a>Accesso con un metodo alternativo
A volte non si dispone del telefono o del dispositivo configurato come metodo di verifica preferito. Per questo motivo è consigliabile configurare metodi di backup per l'account. Nella sezione seguente viene mostrato come effettuare l'accesso con un metodo alternativo quando il metodo principale può non essere disponibile.

1. Accedere a un'applicazione o servizio come Office 365 usando nome utente e password.
2. Selezionare **Usa un'opzione di verifica diversa**. Vengono visualizzate diverse opzioni di verifica in base al numero di opzioni configurate.
3. Scegliere un metodo alternativo ed effettuare l’accesso.

   ![Utilizzare un metodo alternativo](./media/multi-factor-authentication-end-user-signin/alt.png)

## <a name="next-steps"></a>Passaggi successivi
- In caso di problemi di accesso con la verifica in due passaggi, è possibile ottenere altre informazioni in [Problemi con Multi-Factor Authentication di Azure](multi-factor-authentication-end-user-troubleshoot.md).

- Informazioni su come [Gestire le impostazioni della verifica in due passaggi](multi-factor-authentication-end-user-manage-settings.md).

- Altre informazioni sull'[introduzione all'app Microsoft Authenticator](user-help-auth-app-download-install.md) per poter effettuare l'accesso tramite notifiche, invece che con messaggi e telefonate.
