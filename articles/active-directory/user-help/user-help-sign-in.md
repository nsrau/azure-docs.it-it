---
title: Accedere usando le informazioni di autenticazione dell'identità - Azure ADSign-in using your identity authentication info - Azure AD
description: Scopri come accedere usando i vari metodi di verifica dell'identità nelle info di sicurezza.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: curtand
ms.custom: user-help
ms.openlocfilehash: 7d1d27cede6c593150d3937bb3aa0ca59394eea9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062167"
---
# <a name="sign-in-using-two-step-verification-or-security-info"></a>Eseguire l'accesso usando la verifica in due passaggi o le informazioni di sicurezza

Dopo aver configurato la verifica in due passaggi o le info di sicurezza, potrai accedere al tuo account utilizzando il metodo di verifica specificato.

> [!Note]
> Se stai ancora utilizzando l'esperienza di verifica in due passaggi, dovrai configurare i metodi di verifica seguendo le istruzioni nell'articolo [Configurare il mio account per](multi-factor-authentication-end-user-first-time.md) la verifica in due passaggi.
>
> Se l'amministratore ha attivato l'esperienza delle info di sicurezza, dovrai impostare i metodi di verifica usando questi articoli dettagliati:<ul><li>[Configurare le informazioni di sicurezza per l'uso di un'app di autenticazione](security-info-setup-auth-app.md)</li><li>[Configurare le informazioni di sicurezza per l'uso di SMS](security-info-setup-text-msg.md)</li><li>[Configurare le informazioni di sicurezza per l'uso di una chiamata telefonica](security-info-setup-phone-number.md)</li><li>[Configurare le informazioni di sicurezza per l'uso di una chiave di sicurezza](security-info-setup-security-key.md)</li></ul>

## <a name="sign-in-using-an-authenticator-app-notification-on-your-mobile-device"></a>Eseguire l'accesso usando una notifica dell'app di autenticazione nel dispositivo mobile

1. Accedere all'account con il nome utente e la password.

2. Selezionare **Approva** dalla notifica di approvazione inviata al dispositivo mobile.

## <a name="sign-in-using-an-authenticator-app-code-on-your-mobile-device"></a>Eseguire l'accesso usando un codice dell'app di autenticazione nel dispositivo mobile

1. Accedere all'account con il nome utente e la password.

2. Aprire l'app di autenticazione e digitare il codice generato in modo casuale per l'account nella casella **Immettere il codice**.

## <a name="sign-in-using-your-phone-number"></a>Eseguire l'accesso con il numero di telefono

1. Accedere all'account con il nome utente e la password.

2. Rispondere alla chiamata e seguire le istruzioni.

## <a name="sign-in-using-a-text-message"></a>Eseguire l'accesso con un SMS

1. Accedere all'account con il nome utente e la password.

2. Aprire il messaggio SMS e digitare il codice contenuto nel messaggio nella casella **Immettere il codice**.

## <a name="sign-in-using-a-security-key-at-the-lock-screen"></a>Accedere utilizzando una chiave di sicurezza nella schermata di blocco

1. Dopo aver registrato la chiave di sicurezza, seleziona l'immagine della chiave di sicurezza dalla schermata di blocco di Windows 10.

2. Inserisci la chiave di sicurezza nella porta USB del dispositivo e accedi a Windows utilizzando il PIN della chiave di sicurezza.

    ![Accesso con chiave di sicurezza nella schermata di blocco di Windows 10](./media/security-info/security-info-windows-10-lock-screen-security-key.png)

## <a name="sign-in-using-a-security-key-and-the-microsoft-edge-browser"></a>Accedere utilizzando una chiave di sicurezza e il browser Microsoft Edge

1. Dopo aver registrato la chiave di sicurezza, aprire il browser Microsoft Edge.

2. Quando ti viene richiesto di accedere, inserisci la chiave di sicurezza nella porta USB del dispositivo e accedi a Windows utilizzando il PIN della chiave di sicurezza.

    ![Accesso alla chiave di sicurezza tramite il browser Microsoft Edge](./media/security-info/security-info-edge-security-key.png)

    >[!NOTE]
    >Per informazioni sull'accesso tramite l'app Microsoft Authenticator, vedere l'articolo [Accedere agli account utilizzando l'app Microsoft Authenticator](user-help-auth-app-sign-in.md).

## <a name="sign-in-using-another-verification-method"></a>Accedere con un altro metodo di verifica

Se per qualche motivo non riesci a utilizzare il metodo di accesso principale, puoi utilizzare un altro metodo di verifica configurato in precedenza.

1. Accedere normalmente all'account e quindi scegliere il collegamento **Accedi in un altro modo** nella pagina Verifica in due **passaggi.**

    ![Modificare il metodo di verifica dell'accesso](media/security-info/two-factor-auth-signin-another-way.png)

    >[!Note]
    >Se non vedi il collegamento **Accedi in un altro modo,** significa che non hai configurato altri metodi di verifica e che dovrai contattare l'amministratore per assistenza nell'accesso al tuo account. Dopo che l'amministratore ti ha effettuato l'accesso, assicurati di aggiungere ulteriori metodi di verifica. Per altre informazioni sui metodi di verifica, vedere l'articolo [Gestire le impostazioni per la verifica in due passaggi](multi-factor-authentication-end-user-manage-settings.md).
    >
    >Se vedi il link **Accedi in un altro modo,** ma ancora non vedi altri metodi di verifica, dovrai contattare l'amministratore per assistenza nell'accesso al tuo account.

2. Scegliere il metodo di verifica alternativo e continuare il processo di verifica in due passaggi.

3. Dopo aver eseguito l'accesso al proprio account, è possibile aggiornare i metodi di verifica (se necessario). Per altre informazioni su come aggiungere o modificare i propri metodi, vedere l'articolo [Gestire le impostazioni per la verifica in due passaggi](multi-factor-authentication-end-user-manage-settings.md).

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle informazioni di sicurezza sono disponibili nell'articolo [Panoramica delle informazioni di sicurezza (anteprima)](user-help-security-info-overview.md).

- Informazioni sulla verifica in due passaggi sono disponibili nell'articolo [Panoramica della verifica in due passaggi](user-help-two-step-verification-overview.md).

- Se la password è stata persa o dimenticata, è possibile reimpostarla dal [portale per la reimpostazione della password](https://passwordreset.microsoftonline.com/)

- Ottenere informazioni e suggerimenti per la risoluzione dei problemi di accesso nell'articolo [Quando non riesci ad accedere al tuo account Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
