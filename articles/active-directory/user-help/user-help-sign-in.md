---
title: Accedere con in due passaggi verifica o sicurezza info - Azure Active Directory | Microsoft Docs
description: Informazioni su come eseguire l'accesso usando i vari metodi di verifica dell'identità disponibili nelle informazioni di sicurezza.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60e4d9a4cb555dfbc9f05961487a8f794d19e1bf
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59268355"
---
# <a name="sign-in-using-two-step-verification-or-security-info"></a>Eseguire l'accesso usando la verifica in due passaggi o le informazioni di sicurezza
Dopo aver impostato la verifica in due passaggi o le informazioni di sicurezza, sarà possibile accedere all'account usando il metodo di autenticazione specificato.

> [!Note]
> Se si usa ancora l'esperienza di verifica in due passaggi, è necessario configurare i metodi di autenticazione seguendo le istruzioni riportate nell'articolo [Configurare l'account per la verifica in due passaggi](multi-factor-authentication-end-user-first-time.md).
> 
> Se l'amministratore ha attivato l'esperienza per le informazioni di sicurezza, è necessario impostare i metodi di autenticazione usando questi articoli di procedure dettagliate:<ul><li>[Configurare le informazioni di sicurezza per l'uso di un'app di autenticazione](security-info-setup-auth-app.md)</li><li>[Configurare le informazioni di sicurezza per l'uso di SMS](security-info-setup-text-msg.md)</li><li>[Configurare le informazioni di sicurezza per l'uso di una chiamata telefonica](security-info-setup-phone-number.md)</li></ul>

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

## <a name="sign-in-using-another-verification-method"></a>Accedere con un altro metodo di verifica
Se per qualche motivo è possibile usare il metodo di accesso primario, è possibile usare un altro in precedenza consente di impostare il metodo di verifica.

1. Accedi al tuo account normalmente e quindi scegliere il **Accedi a un altro modo** sul collegamento il **verifica in due passaggi** pagina.

    ![Modificare il metodo di verifica dell'accesso](media/security-info/two-factor-auth-signin-another-way.png)

    >[!Note]
    >Se non viene visualizzato il **Accedi a un altro modo** collegamento, significa che è stato impostato alcun altro metodo di verifica e che è possibile contattare l'amministratore per informazioni sull'accesso all'account. Dopo che l'amministratore consente di eseguire l'accesso, assicurarsi di che aggiungere ulteriori metodi di verifica. Per altre informazioni sui metodi di verifica, vedere l'articolo [Gestire le impostazioni per la verifica in due passaggi](multi-factor-authentication-end-user-manage-settings.md).
    > 
    >Se viene visualizzato il **Accedi a un altro modo** collegare, ma non ancora visualizzati tutti gli altri metodi di verifica, è necessario contattare l'amministratore per assistenza per accedere al proprio account.

2. Scegliere il metodo di verifica alternativo e continuare il processo di verifica in due passaggi.

3. Dopo aver eseguito l'accesso al proprio account, è possibile aggiornare i metodi di verifica (se necessario). Per altre informazioni su come aggiungere o modificare i propri metodi, vedere l'articolo [Gestire le impostazioni per la verifica in due passaggi](multi-factor-authentication-end-user-manage-settings.md).

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle informazioni di sicurezza sono disponibili nell'articolo [Panoramica delle informazioni di sicurezza (anteprima)](user-help-security-info-overview.md).

- Informazioni sulla verifica in due passaggi sono disponibili nell'articolo [Panoramica della verifica in due passaggi](user-help-two-step-verification-overview.md). 

- Se la password è stata persa o dimenticata, è possibile reimpostarla dal [portale per la reimpostazione della password](https://passwordreset.microsoftonline.com/)

- Ottenere informazioni e suggerimenti per la risoluzione dei problemi di accesso nell'articolo [Quando non riesci ad accedere al tuo account Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
