---
title: Modificare il metodo e le impostazioni di verifica a due fattori - Azure Active DirectoryChange your two-factor verification method and settings - Azure Active Directory
description: Scopri come modificare il metodo e le impostazioni di verifica della sicurezza per il tuo account aziendale o dell'istituto di istruzione dalla pagina Verifica di sicurezza aggiuntiva.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: richagi
ms.assetid: d3372d9a-9ad1-4609-bdcf-2c4ca9679a3b
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: curtand
ms.openlocfilehash: 5949f04ecc28a88e340a9c2de530031793f193a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253247"
---
# <a name="change-your-two-factor-verification-method-and-settings"></a>Modificare il metodo e le impostazioni di verifica a due fattori

Dopo aver configurato i metodi di verifica della sicurezza per l'account aziendale o dell'istituto di istruzione, è possibile aggiornare i dettagli correlati, tra cui:

- Metodo di verifica della sicurezza predefinito

- Dettagli del metodo di verifica della sicurezza, ad esempio il numero di telefono

- Configurazione dell'app Authenticator o eliminazione di un dispositivo dall'app di autenticazione

## <a name="using-the-additional-security-verification-page"></a>Utilizzo della pagina Verifica di sicurezza aggiuntiva

Se l'organizzazione ha fornito passaggi specifici su come attivare e gestire la verifica a due fattori, è necessario seguire prima queste istruzioni. In caso contrario, è possibile accedere alle impostazioni del metodo di verifica della sicurezza dalla pagina [Verifica di sicurezza aggiuntiva.](https://docs.microsoft.com/azure/active-directory/user-help/multi-factor-authentication-end-user-first-time)

>[!Note]
>Se ciò che viene visualizzato sullo schermo non corrisponde a quanto trattato in questo articolo, significa che l'amministratore ha attivato l'esperienza Info di **sicurezza (anteprima)** o che l'organizzazione ha fornito il proprio portale personalizzato. Per ulteriori informazioni sulla nuova esperienza di info di sicurezza, consultate Panoramica delle informazioni di [sicurezza (anteprima).](user-help-security-info-overview.md) Per ulteriori informazioni sul portale personalizzato dell'organizzazione, è necessario contattare l'help desk dell'organizzazione.

### <a name="to-get-to-the-additional-security-verification-page"></a>Per accedere alla pagina Verifica di sicurezza aggiuntiva

È possibile seguire questo link alla [pagina Verifica di sicurezza aggiuntiva](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1).

![Pagina di verifica della sicurezza aggiuntiva, con i dettagli del metodo di verifica della sicurezza disponibile](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page.png)

È inoltre possibile accedere alla pagina **Verifica di sicurezza aggiuntiva** attenendosi alla seguente procedura:

1. Accedere a [https://myapps.microsoft.com](https://myapps.microsoft.com).

1. Selezionare il nome dell'account in alto a destra, quindi selezionare **profilo**.

1. Selezionare **Verifica di sicurezza aggiuntiva**.  

    ![Collegamento App personali alla pagina Verifica di sicurezza aggiuntiva](./media/multi-factor-authentication-end-user-manage-settings/mfa-myapps-link.png)

>[!Note]
>Per informazioni sull'uso della sezione **Password app** della pagina Verifica di **sicurezza aggiuntiva,** vedere Gestire le password delle app per la verifica a [due fattori.](multi-factor-authentication-end-user-app-passwords.md) Le password delle app devono essere usate solo per le app che non supportano la verifica a due fattori.

## <a name="change-your-default-security-verification-method"></a>Modificare il metodo di verifica della sicurezza predefinito

Dopo aver eseguito l'accesso all'account aziendale o dell'istituto di istruzione con il nome utente e la password, verrà visualizzato automaticamente il metodo di verifica della sicurezza scelto. A seconda dei requisiti dell'organizzazione, può trattarsi di un codice di notifica o di verifica tramite un'app di autenticazione, un messaggio di testo o una telefonata.

Se decidi di modificare il metodo di verifica della sicurezza predefinito che stai utilizzando, puoi farlo da qui.

### <a name="to-change-your-default-security-verification-method"></a>Per modificare il metodo di verifica della sicurezza predefinito

1. Nella pagina **Verifica di sicurezza aggiuntiva,** selezionare il metodo da utilizzare **dall'elenco Qual è l'opzione preferita.** Verranno visualizzate tutte le opzioni, ma è possibile selezionare solo quelle rese disponibili dall'organizzazione.

    - **Avvisami tramite app**: Riceverai una notifica tramite l'app di autenticazione che hai una richiesta di verifica in attesa.

    - **Chiama il mio telefono**di autenticazione : Riceverai una telefonata sul tuo dispositivo mobile, che ti chiederà di verificare le tue informazioni.

    - **Codice di testo al mio telefono**di autenticazione : Si otterrà un codice di verifica come parte di un messaggio di testo sul dispositivo mobile. Devi inserire questo codice nella richiesta di verifica per il tuo account aziendale o dell'istituto di istruzione.

    - **Chiama il mio telefono dell'ufficio**: Riceverai una telefonata sul tuo telefono dell'ufficio, che ti chiederà di verificare le tue informazioni.

    - Usa il codice di **verifica dall'app:** userai l'app di autenticazione per ottenere un codice di verifica che digiterai nel prompt del tuo account aziendale o dell'istituto di istruzione.

2. Selezionare **Salva**.

## <a name="add-or-change-your-phone-number"></a>Aggiungere o modificare il numero di telefono

È possibile aggiungere nuovi numeri di telefono o aggiornare i numeri esistenti dalla pagina **Verifica di sicurezza aggiuntiva.**

>[!Important]
>Ti consigliamo vivamente di aggiungere un numero di telefono secondario per evitare di essere bloccato dal tuo account in caso di smarrimento o furto del telefono principale o se ricevi un nuovo telefono e non hai più il tuo numero di telefono principale originale.

### <a name="to-change-your-phone-numbers"></a>Per modificare i numeri di telefono

1. Dalla sezione **Come si desidera rispondere?** della pagina Verifica di **sicurezza aggiuntiva** aggiornare le informazioni sul numero di telefono per l'autenticazione (il dispositivo mobile principale) e il **telefono dell'ufficio**. **Authentication phone**

1. Selezionare la casella accanto all'opzione Telefono di **autenticazione alternativo** e quindi digitare un numero di telefono secondario in cui è possibile ricevere messaggi di testo o telefonate se non è possibile accedere al dispositivo principale.

1. Selezionare **Salva**.

## <a name="add-a-new-account-to-the-microsoft-authenticator-app"></a>Aggiungere un nuovo account all'app di autenticazione Microsoft

Puoi configurare il tuo account aziendale o dell'istituto di istruzione nell'app Microsoft Authenticator per [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) o [iOS.](https://apps.apple.com/app/microsoft-authenticator/id983156458)

Se hai già configurato il tuo account aziendale o dell'istituto di istruzione nell'app Microsoft Authenticator, non è necessario farlo di nuovo.

1. Dalla sezione **Come vuoi rispondere?** della pagina **Verifica di sicurezza aggiuntiva,** seleziona Configura **app Authenticator**.

    ![Configurare l'account aziendale o dell'istituto di istruzione nell'app Microsoft Authenticator](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page-auth-app.png)

1. Seguire le istruzioni visualizzate sullo schermo, incluso l'utilizzo del dispositivo mobile per eseguire la scansione del codice QR, quindi selezionare **Avanti**.

    Ti verrà chiesto di approvare una notifica tramite l'app Microsoft Authenticator per verificare le tue informazioni.

1. Selezionare **Salva**.

## <a name="delete-your-account-or-device-from-the-microsoft-authenticator-app"></a>Eliminare l'account o il dispositivo dall'app Microsoft Authenticator

È possibile eliminare l'account dall'app Microsoft Authenticator e il dispositivo dall'account aziendale o dell'istituto di istruzione. In genere si elimina il dispositivo per rimuovere definitivamente un dispositivo smarrito, rubato o vecchio dall'account e si elimina l'account per tentare di risolvere alcuni problemi di connessione o per risolvere una modifica dell'account, ad esempio un nuovo nome utente.

### <a name="to-delete-your-device-from-your-work-or-school-account"></a>Per eliminare il dispositivo dall'account aziendale o dell'istituto di istruzione

1. Nella sezione **Come vuoi rispondere?** della pagina Verifica di **sicurezza aggiuntiva,** seleziona il pulsante Configura **app Authenticator.**

1. Selezionare **Salva**.

### <a name="to-delete-your-account-from-the-microsoft-authenticator-app"></a>Per eliminare l'account dall'app Microsoft Authenticator

Nell'app Microsoft Authenticator selezionare il pulsante **Elimina** accanto al dispositivo da eliminare.

## <a name="turn-on-two-factor-verification-prompts-on-a-trusted-device"></a>Attivare le richieste di verifica a due fattori in un dispositivo attendibile

A seconda delle impostazioni dell'organizzazione, è possibile che venga visualizzata una casella di controllo che indica **non chiedere più per X giorni** quando si esegue la verifica a due fattori nel browser. Se hai selezionato questa opzione per interrompere le richieste di verifica a due fattori e quindi perdi il dispositivo o il dispositivo è potenzialmente compromesso, dovresti riattivare le richieste di verifica a due fattori per proteggere il tuo account. È necessario attivare le richieste per tutti i dispositivi contemporaneamente. Sfortunatamente, non è possibile riattivare i prompt solo per un dispositivo specifico.

### <a name="to-turn-two-factor-verification-prompts-back-on-for-your-devices"></a>Per riattivare le richieste di verifica a due fattori per i dispositivi

Nella [pagina **Verifica di sicurezza aggiuntiva** ](#to-get-to-the-additional-security-verification-page)selezionare Ripristina autenticazione a **più fattori su dispositivi precedentemente attendibili.** Al successivo accesso da qualsiasi dispositivo, verrà richiesto di eseguire la verifica a due fattori.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver aggiunto o aggiornato le impostazioni di verifica a due fattori, puoi gestire le password dell'app, accedere o ottenere assistenza per alcuni problemi comuni relativi alla verifica a due fattori.

- [Gestisci le password delle app per](multi-factor-authentication-end-user-app-passwords.md) la verifica a due fattori per tutte le app che non supportano la verifica a due fattori.

- [Come accedere utilizzando la verifica a due fattori](multi-factor-authentication-end-user-signin.md)

- [Risolvere i problemi comuni relativi alla verifica a due fattori](multi-factor-authentication-end-user-troubleshoot.md)
