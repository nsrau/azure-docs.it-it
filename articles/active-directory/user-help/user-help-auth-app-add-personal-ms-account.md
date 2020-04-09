---
title: Aggiungere un account Microsoft personale all'app Microsoft Authenticator - Azure ADAdd a personal Microsoft account to the Microsoft Authenticator app - Azure AD
description: Aggiungere account Microsoft personali, ad esempio ad Outlook.com o Xbox LIVE all'app Microsoft Authenticator, per verificare l'identità durante l'utilizzo della verifica a due fattori.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: cef4f83881aed67c46477110de530bbf191ee39f
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984753"
---
# <a name="add-personal-microsoft-accounts-to-the-microsoft-authenticator-app"></a>Aggiungere account Microsoft personali all'app Microsoft Authenticator

Aggiungere gli account Microsoft personali, ad esempio per gli account Outlook.com e Xbox LIVE, all'app Microsoft Authenticator sia per il processo di verifica standard a due fattori che per l'accesso telefonico senza password.

- **Metodo standard di verifica a due fattori.** Digitare il nome utente e la password nel dispositivo a cui si accede, quindi scegliere se l'app Microsoft Authenticator invia una notifica o se si preferisce copiare il codice di verifica associato dalla schermata **Account** dell'app Microsoft Authenticator.

- **Accesso senza password.** Digitare il nome utente nel dispositivo a cui si accede utilizzando l'account Microsoft personale e quindi utilizzare il dispositivo mobile per verificare che si utilizzi l'impronta digitale, il volto o il PIN. Per questo metodo, non è necessario immettere la password.

>[!Important]
>Prima di poter aggiungere l'account, è necessario scaricare e installare l'app Microsoft Authenticator. Se non è ancora stato fatto, seguire i passaggi descritti nell'articolo [Scaricare e installare l'app](user-help-auth-app-download-install.md).

È possibile aggiungere l'account Microsoft personale attivando la prima verifica a due fattori e quindi aggiungendo l'account all'app. Non è necessario attivare la verifica a due fattori per utilizzare solo l'accesso telefonico senza password per il tuo account, ma ti consigliamo vivamente di attivare la verifica a due fattori per una maggiore sicurezza dell'account.

## <a name="turn-on-two-factor-verification"></a>Attivare la verifica a due fattori

1. Nel computer in uso andare alla pagina [Informazioni di base sulla sicurezza](https://account.microsoft.com/security) e accedere usando l'account Microsoft personale. Ad esempio: alain@outlook.com.

2. In fondo alla pagina **nozioni fondamentali sulla sicurezza**, scegliere il collegamento **Altre opzioni di sicurezza**.

    ![La pagina di nozioni fondamentali sulla sicurezza con il collegamento "altre opzioni di sicurezza" evidenziato](./media/user-help-auth-app-add-personal-ms-account/more-security-options-link.png)

3. Andare alla sezione **verifica in due passaggi** e scegliere di abilitare la funzionalità **Attiva**. È possibile anche disattivarla se non si desidera più usarla con l'account personale.

## <a name="add-your-microsoft-account-to-the-app"></a>Aggiungere l'account Microsoft all'app

1. Aprire l'app Microsoft Authenticator nel dispositivo mobile.

1. In Android, seleziona **Aggiungi account** dall'icona Personalizza **e controlla** in alto a destra.

    ![Pagine di selezione dell'account Android](media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon.png)

    In iOS, seleziona l'icona più in alto a destra.

    ![Versione iOS dell'esperienza di selezione dell'account](media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon-ios.png)

1. Nella pagina **Aggiungi account**, scegliere **Account personale**.

1. Seleziona **Accedi con Microsoft** per aggiungere il tuo account. Un codice QR può essere utilizzato quando disponibile, ma puoi sempre aggiungere il tuo account accedendo con il tuo nome utente e password.

    ![Selezionare un account Microsoft o eseguire la scansione di un codice QR quando disponibile](media/user-help-auth-app-add-personal-ms-account/add-account-android.png)

1. Accedere all'account personale utilizzando l'indirizzo di alain@outlook.composta elettronica appropriato , ad esempio , e quindi selezionare **Avanti**.

    >[!Note]
    >Se non si dispone di un account Microsoft personale, [è possibile crearne uno](https://account.microsoft.com/account/Account?refd=www.bing.com&ru=https%3A%2F%2Faccount.microsoft.com%2F%3Frefd%3Dwww.bing.com&destrt=home-index).

1. Immettere la password e quindi selezionare **Accedi**. L'account personale viene aggiunto all'app Microsoft Authenticator.

## <a name="next-steps"></a>Passaggi successivi

- Dopo aver aggiunto gli account all'app, è possibile accedere usando l'app Authenticator nel dispositivo. Per altre informazioni, consultare [Accedere con l'app](user-help-auth-app-sign-in.md).

- In caso di problemi nell'ottenere il codice di verifica per il tuo account Microsoft personale, consulta la sezione **Risoluzione dei problemi** relativi ai codici di verifica nell'articolo Codici di [sicurezza dell'account Microsoft &.](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes)

- Per i dispositivi che eseguono iOS, è anche possibile eseguire il backup nel cloud delle credenziali dell'account e delle relative impostazioni dell'app, ad esempio l'ordine degli account. Per altre informazioni, consultare [Eseguire il backup e il ripristino con l'app Microsoft Authenticator](user-help-auth-app-backup-recovery.md).
