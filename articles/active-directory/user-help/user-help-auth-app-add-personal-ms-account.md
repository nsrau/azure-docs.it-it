---
title: Aggiungere l'account Microsoft personale all'app Microsoft Authenticator - Azure Active Directory | Microsoft Docs
description: Come aggiungere gli account Microsoft personali, ad esempio per Outlook.com oppure Xbox LIVE all'app Microsoft Authenticator per la verifica a due fattori.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.collection: M365-identity-device-management
ms.openlocfilehash: eba77f79ad4625319a9b6e3eae46bf8470f43607
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56203778"
---
# <a name="add-your-personal-microsoft-accounts"></a>Aggiungere l'account Microsoft personale
Aggiungere gli account Microsoft personali, ad esempio per Outlook.com e Xbox LIVE all'app Microsoft Authenticator per il processo di verifica a due fattori standard e il metodo di accesso telefono senza password.

- **Metodo standard di verifica a due fattori.** Digitare il nome utente e la password nel dispositivo al quale si esegue l'accesso e quindi scegliere se l'app Microsoft Authenticator invii una notifica o se copiare il codice di verifica associato dalla schermata **Account**  dell'App Microsoft Authenticator.

- **Accesso senza password.** Digitare il nome utente nel dispositivo al quale si esegue l'accesso per l'account Microsoft personale e quindi usare il proprio dispositivo mobile per verificare l'identità tramite l'impronta digitale, il viso o il PIN. Per questo metodo, non è necessario immettere la password.

>[!Important]
>Prima di poter aggiungere l'account, è necessario scaricare e installare l'app Microsoft Authenticator. Se non è ancora stato fatto, seguire i passaggi descritti nell'articolo [Scaricare e installare l'app](user-help-auth-app-download-install.md).

## <a name="add-your-personal-microsoft-account"></a>Aggiungere l'account Microsoft personale
È possibile aggiungere l'account Microsoft personale attivando la prima verifica a due fattori e quindi aggiungendo l'account all'app.

>[!Note]
>Se si prevede di usare solo l'accesso tramite telefono senza password per l'account Microsoft personale, non è necessario attivare la verifica a due fattori. Tuttavia, per un'ulteriore sicurezza dell'account, è consigliabile attivare la verifica a due fattori.

### <a name="turn-on-two-factor-verification"></a>Attivare la verifica a due fattori

1. Nel PC in uso, andare alla pagina [nozioni fondamentali sulla sicurezza](https://account.microsoft.com/security) e accedere usando l'account Microsoft personale. Ad esempio: alain@outlook.com.

2. In fondo alla pagina **nozioni fondamentali sulla sicurezza**, scegliere il collegamento **Altre opzioni di sicurezza**.

    ![La pagina di nozioni fondamentali sulla sicurezza con il collegamento "altre opzioni di sicurezza" evidenziato](./media/user-help-auth-app-add-personal-ms-account/more-security-options-link.png)

3. Andare alla sezione **verifica in due passaggi** e scegliere di abilitare la funzionalità **Attiva**. È possibile anche disattivarla se non si desidera più usarla con l'account personale.

### <a name="add-your-microsoft-account-to-the-app"></a>Aggiungere l'account Microsoft all'app

1. Aprire l'app Microsoft Authenticator nel dispositivo mobile.

2. Selezionare **Aggiungi account** dall'icona **Personalizza e controlla** in alto a destra.

    ![Pagina account con l'icona Personalizza e controlla evidenziata](./media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon.png)

3. Nella pagina **Aggiungi account**, scegliere **Account personale**.

4. Accedi all'account personale, usando l'indirizzo di posta elettronica appropriato (ad esempio alain@outlook.com), quindi scegliere **Successivo**.

    >[!Note]
    >Se non si dispone di un account Microsoft personale, è possibile crearne uno qui.

5. Immettere la password e quindi scegliere **Accedi**.

    L'account personale viene aggiunto all'app Microsoft Authenticator.

## <a name="next-steps"></a>Passaggi successivi

- Dopo aver aggiunto gli account all'app, è possibile accedere usando l'app Authenticator nel dispositivo. Per altre informazioni, consultare [Accedere con l'app](user-help-auth-app-sign-in.md).

- Per i dispositivi che eseguono iOS, è anche possibile eseguire il backup nel cloud delle credenziali dell'account e delle relative impostazioni dell'app, ad esempio l'ordine degli account. Per altre informazioni, consultare [Eseguire il backup e il ripristino con l'app Microsoft Authenticator](user-help-auth-app-backup-recovery.md).
