---
title: Modificare il metodo di verifica a due fattori e le impostazioni Azure AD
description: Informazioni su come modificare il metodo di verifica della sicurezza e le impostazioni per l'account aziendale o dell'Istituto di istruzione dalla pagina verifica aggiuntiva di sicurezza.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: richagi
ms.assetid: d3372d9a-9ad1-4609-bdcf-2c4ca9679a3b
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: f7fbc18d8141c44c2b0863547c33b5c5193e928b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231917"
---
# <a name="change-your-two-factor-verification-method-and-settings"></a>Modificare il metodo e le impostazioni di verifica a due fattori

Dopo aver configurato i metodi di verifica della sicurezza per l'account aziendale o dell'Istituto di istruzione, è possibile aggiornare i dettagli correlati, tra cui:

- Scelta del metodo di verifica di sicurezza predefinito.

- Aggiunta o aggiornamento dei dettagli del metodo di verifica della sicurezza, ad esempio il numero di telefono.

- Configurazione di una nuova app Authenticator o eliminazione di un dispositivo dall'app Authenticator.

## <a name="using-the-additional-security-verification-page"></a>Utilizzo della pagina verifica aggiuntiva di sicurezza

Se l'organizzazione ha fornito passaggi specifici su come attivare e gestire la verifica a due fattori, è necessario seguire queste istruzioni. In caso contrario, è possibile ottenere le impostazioni del metodo di verifica della sicurezza dalla pagina [verifica aggiuntiva di sicurezza](https://aka.ms/mfasetup) .

>[!Note]
>Se gli elementi visualizzati sullo schermo non corrispondono a quelli trattati in questo articolo, significa che l'amministratore ha attivato l'esperienza di sicurezza (anteprima) o che l'organizzazione dispone di un portale personalizzato. Per altre informazioni sull'esperienza di sicurezza, vedere [Panoramica delle informazioni di sicurezza (anteprima)](user-help-security-info-overview.md). Per ulteriori informazioni sul portale personalizzato dell'organizzazione, è necessario contattare il supporto tecnico.

### <a name="to-get-to-the-additional-security-verification-page"></a>Per ottenere la pagina verifica aggiuntiva di sicurezza

- Passare a https://aka.ms/mfasetup.

    ![Schermata di verifica aggiuntiva di sicurezza con i dettagli del metodo di verifica sicurezza disponibile](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page.png)

    Se facendo clic su questo collegamento non si ottiene il risultato desiderato, è possibile accedere alla pagina **Verifica aggiuntiva di sicurezza** anche tramite questa procedura:

    1. Accedere a [https://myapps.microsoft.com](https://myapps.microsoft.com).

    2. Selezionare il nome dell'account in alto a destra, quindi selezionare **profilo**.

    3. Selezionare **Verifica aggiuntiva di sicurezza**.  

        ![Collegamento app personali alla pagina verifica aggiuntiva di sicurezza](./media/multi-factor-authentication-end-user-manage-settings/mfa-myapps-link.png)

>[!Note]
>Per informazioni sull'uso della sezione **password app** della pagina **verifica aggiuntiva di sicurezza** , vedere gestire le password delle [app per la verifica a due fattori](multi-factor-authentication-end-user-app-passwords.md). Le password dell'app devono essere usate solo per le app che non supportano ancora la verifica a due fattori.

## <a name="change-your-default-security-verification-method"></a>Modificare il metodo di verifica di sicurezza predefinito

Dopo aver effettuato l'accesso all'account aziendale o dell'Istituto di istruzione con il nome utente e la password, verrà visualizzato automaticamente il metodo di verifica della sicurezza scelto. A seconda dei requisiti dell'organizzazione, può trattarsi di un codice di notifica o di verifica tramite un'app di autenticazione, un SMS o una telefonata.

Se si decide di modificare il metodo di verifica di sicurezza predefinito in uso, è possibile farlo da qui.

### <a name="to-change-your-default-security-verification-method"></a>Per modificare il metodo di verifica di sicurezza predefinito

1. Nella pagina **verifica aggiuntiva di sicurezza** selezionare il metodo da usare nell'elenco a discesa delle **opzioni preferite** . Verranno visualizzate tutte le opzioni, ma sarà possibile scegliere solo quelle disponibili all'utente dall'organizzazione in uso.

    - **Invia una notifica tramite l'app.** Si riceverà una notifica tramite l'app Authenticator che è presente una richiesta di verifica in attesa.

    - **Chiamare il telefono per l'autenticazione.** Si riceverà una telefonata sul dispositivo mobile, in cui viene chiesto di verificare le informazioni.

    - **Scrivere il codice nel telefono per l'autenticazione.** Si otterrà un codice di verifica come parte di un SMS sul dispositivo mobile. È necessario immettere questo codice nella richiesta di verifica per l'account aziendale o dell'Istituto di istruzione.

    - **Chiamare il telefono dell'ufficio.** Si riceverà una telefonata sul telefono dell'ufficio, in cui viene chiesto di verificare le informazioni.

    - **Usare il codice di verifica dall'app.** Si userà l'app Authenticator per ottenere un codice di verifica che verrà digitato nella richiesta dall'account aziendale o dell'Istituto di istruzione.

2. Selezionare **Salva**.

## <a name="add-or-change-your-phone-number"></a>Aggiungere o modificare il numero di telefono

È possibile aggiungere nuovi numeri di telefono o aggiornare i numeri esistenti dalla pagina **verifica aggiuntiva di sicurezza** .

>[!Important]
>Si consiglia vivamente di aggiungere un numero di telefono secondario per impedire che venga bloccato dall'account se il telefono primario viene smarrito o rubato o se si riceve un nuovo telefono e non è più disponibile il numero di telefono principale originale.

### <a name="to-change-your-phone-numbers"></a>Per modificare i numeri di telefono

1. Nella sezione **come si desidera rispondere?** della pagina **verifica aggiuntiva di sicurezza** aggiornare le informazioni sul numero di telefono per il **telefono per l'autenticazione** (il dispositivo mobile primario) e il **telefono dell'ufficio**.

2. Selezionare la casella accanto all'opzione **telefono per l'autenticazione alternativo** , quindi digitare un numero di telefono secondario in cui è possibile ricevere messaggi di testo o telefonate se non è possibile accedere al dispositivo primario.

3. Selezionare **Salva**.

## <a name="add-a-new-account-to-the-microsoft-authenticator-app"></a>Aggiungere un nuovo account all'app Microsoft Authenticator

È possibile configurare l'account aziendale o dell'Istituto di istruzione nell'app Microsoft Authenticator per [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) o [iOS](https://apps.apple.com/app/microsoft-authenticator/id983156458).

Se in precedenza è stato configurato l'account aziendale o dell'Istituto di istruzione nell'app Microsoft Authenticator, non è necessario ripetere l'operazione.

1. Nella sezione stabilire **come si desidera rispondere?** della pagina **verifica aggiuntiva di sicurezza** Selezionare il pulsante dell' **app per l'autenticazione** .

    ![Configurare l'account aziendale o dell'Istituto di istruzione nell'app Microsoft Authenticator](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page-auth-app.png)

2. Seguire le istruzioni visualizzate, incluso l'uso del dispositivo mobile per eseguire la scansione del codice a matrice e quindi fare clic su **Avanti**.

    Verrà richiesto di approvare una notifica tramite l'app Microsoft Authenticator per verificare le informazioni.

3. Selezionare **Salva**.

## <a name="delete-your-account-or-device-from-the-microsoft-authenticator-app"></a>Eliminare l'account o il dispositivo dall'app Microsoft Authenticator

È possibile eliminare l'account dall'app Microsoft Authenticator ed è possibile eliminare il dispositivo dall'account aziendale o dell'Istituto di istruzione. In genere si elimina il dispositivo per rimuovere definitivamente un dispositivo perso, rubato o obsoleto dall'account e si elimina l'account per tentare di risolvere alcuni problemi di connessione o per risolvere una modifica dell'account, ad esempio un nuovo nome utente.

### <a name="to-delete-your-device-from-your-work-or-school-account"></a>Per eliminare il dispositivo dall'account aziendale o dell'Istituto di istruzione

1. Nella sezione stabilire **come si desidera rispondere?** della pagina **verifica aggiuntiva di sicurezza** Selezionare il pulsante dell' **app per l'autenticazione** .

2. Selezionare **Salva**.

### <a name="to-delete-your-account-from-the-microsoft-authenticator-app"></a>Per eliminare l'account dall'app Microsoft Authenticator

- Dall'app Microsoft Authenticator selezionare il pulsante **Elimina** accanto al dispositivo che si vuole eliminare.

## <a name="turn-on-two-factor-verification-prompts-on-a-trusted-device"></a>Attivare richieste di verifica a due fattori su un dispositivo attendibile

A seconda delle impostazioni dell'organizzazione, è possibile che venga visualizzata una casella di controllo che indica **di non chiedere di nuovo per X giorni** quando si esegue la verifica a due fattori nel browser. Se è stata selezionata questa casella per arrestare i prompt di verifica a due fattori e si perde il dispositivo o il dispositivo è potenzialmente compromesso, è necessario attivare la richiesta di verifica a due fattori per proteggere l'account. Sfortunatamente, non è possibile attivare di nuovo i prompt per un singolo dispositivo. È necessario attivare i prompt per tutti i dispositivi contemporaneamente.

### <a name="to-turn-two-factor-verification-prompts-back-on-for-your-devices"></a>Per attivare la richiesta di verifica a due fattori per i dispositivi

- Nella pagina **verifica aggiuntiva di sicurezza** selezionare **Ripristina autenticazione a più fattori nei dispositivi precedentemente attendibili**.

    Al successivo accesso a qualsiasi dispositivo, verrà richiesto di eseguire la verifica a due fattori.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver aggiunto o aggiornato le impostazioni di verifica a due fattori, è possibile gestire le password dell'app, effettuare l'accesso o ottenere assistenza per alcuni problemi comuni relativi alla verifica a due fattori.

- [Gestire le password dell'app per la verifica a due fattori](multi-factor-authentication-end-user-app-passwords.md) per le app che non supportano la verifica a due fattori.

- [Accedere usando la verifica a due fattori](multi-factor-authentication-end-user-signin.md)

- [Risoluzione dei problemi della verifica a due fattori](multi-factor-authentication-end-user-troubleshoot.md)
