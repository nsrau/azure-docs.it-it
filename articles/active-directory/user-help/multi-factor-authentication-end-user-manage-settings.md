---
title: Modificare le impostazioni e il metodo di verifica a due fattori - Azure Active Directory
description: Informazioni su come modificare le impostazioni e il metodo di verifica di sicurezza per l'account aziendale o dell'istituto di istruzione dalla pagina Verifica aggiuntiva di sicurezza.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: richagi
ms.assetid: d3372d9a-9ad1-4609-bdcf-2c4ca9679a3b
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 07/06/2020
ms.author: curtand
ms.openlocfilehash: e0a6c566e8e0dfb77b5899f735020d0f1facf3d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88798381"
---
# <a name="change-your-two-factor-verification-method-and-settings"></a>Modificare le impostazioni e il metodo di verifica a due fattori

Dopo aver configurato i metodi di verifica di sicurezza per l'account aziendale o dell'istituto di istruzione, è possibile aggiornare i dettagli correlati, tra cui:

- Metodo di verifica di sicurezza predefinito

- Dettagli del metodo di verifica di sicurezza, ad esempio il numero di telefono

- Configurazione dell'app Authenticator o eliminazione di un dispositivo dall'app Authenticator

## <a name="using-the-additional-security-verification-page"></a>Uso della pagina Verifica aggiuntiva di sicurezza

Se l'organizzazione ha fornito passaggi specifici su come attivare e gestire la verifica a due fattori, è consigliabile seguire prima tali istruzioni. In caso contrario, è possibile ottenere le impostazioni del metodo di verifica di sicurezza dalla pagina [Verifica aggiuntiva di sicurezza](./multi-factor-authentication-end-user-first-time.md).

>[!Note]
>Se quanto visualizzato sullo schermo non corrisponde alle descrizioni di questo articolo, significa che l'amministratore ha attivato l'esperienza **Informazioni di sicurezza (anteprima)** o che l'organizzazione ha fornito un portale personalizzato. Per altri dettagli sull'esperienza relativa alle informazioni di sicurezza, vedere [Panoramica delle informazioni di sicurezza (anteprima)](./security-info-setup-signin.md). Per altre informazioni sul portale personalizzato dell'organizzazione, è necessario contattare il supporto tecnico dell'organizzazione.

### <a name="to-get-to-the-additional-security-verification-page"></a>Per visualizzare la pagina Verifica aggiuntiva di sicurezza

Seguire questo link per passare alla pagina [Verifica aggiuntiva di sicurezza](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1).

![Pagina Verifica aggiuntiva di sicurezza con i dettagli del metodo di verifica di sicurezza disponibile](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page.png)

È possibile accedere alla pagina **Verifica aggiuntiva di sicurezza** anche tramite questa procedura:

1. Accedere a [https://myapps.microsoft.com](https://myapps.microsoft.com).

1. Selezionare il nome dell'account in alto a destra, quindi selezionare **profilo**.

1. Selezionare **Verifica aggiuntiva di sicurezza**.  

    ![Collegamento App personali alla pagina Verifica aggiuntiva di sicurezza](./media/multi-factor-authentication-end-user-manage-settings/mfa-myapps-link.png)

>[!Note]
>Per informazioni sull'uso della sezione **Password dell'app** della pagina **Verifica aggiuntiva di sicurezza**, vedere [Gestire le password dell'app per la verifica a due fattori](multi-factor-authentication-end-user-app-passwords.md). Le password dell'app devono essere usate solo per le app che non supportano la verifica a due fattori.

## <a name="change-your-default-security-verification-method"></a>Modificare il metodo di verifica di sicurezza predefinito

Dopo aver effettuato l'accesso all'account aziendale o dell'istituto di istruzione con il nome utente e la password, verrà visualizzato automaticamente il metodo di verifica di sicurezza scelto. A seconda dei requisiti dell'organizzazione, può trattarsi di una notifica o di un codice di verifica tramite un'app di autenticazione, un SMS o una telefonata.

Se si decide di modificare il metodo di verifica di sicurezza predefinito in uso, è possibile farlo da qui.

### <a name="to-change-your-default-security-verification-method"></a>Per modificare il metodo di verifica di sicurezza predefinito

1. Nella pagina **Verifica aggiuntiva di sicurezza**, selezionare il metodo da usare nell'elenco **What's your preferred option** (Opzione preferita). Anche se verranno visualizzate tutte le opzioni, sarà possibile selezionare solo quelle rese disponibili dall'organizzazione.

    - **Avvisa via app**: Si riceverà una notifica tramite l'app Authenticator per informare che è presente una richiesta di verifica in attesa.

    - **Chiama telefono per l'autenticazione utente**: Si riceverà una telefonata sul dispositivo mobile, in cui verrà chiesto di verificare i propri dati.

    - **Invia il codice al telefono per l'autenticazione dell'utente**: Si otterrà un codice di verifica tramite SMS sul dispositivo mobile. È necessario immettere questo codice nella richiesta di verifica per l'account aziendale o dell'Istituto di istruzione.

    - **Chiama telefono ufficio**: Si riceverà una telefonata sul telefono dell'ufficio, in cui verrà chiesto di verificare i propri dati.

    - **Usa il codice di verifica dall'app**: Si userà l'app Authenticator per ottenere un codice di verifica che verrà digitato nella richiesta dall'account aziendale o dell'istituto di istruzione.

2. Selezionare **Salva**.

## <a name="add-or-change-your-phone-number"></a>Aggiungere o modificare un numero di telefono

È possibile aggiungere nuovi numeri di telefono o aggiornare i numeri esistenti dalla pagina **Verifica aggiuntiva di sicurezza**.

>[!Important]
>Si consiglia vivamente di aggiungere un numero di telefono secondario per impedire che l'account venga bloccato se il telefono primario viene smarrito o rubato o se si riceve un nuovo telefono e non è più disponibile il numero di telefono principale originale.

### <a name="to-change-your-phone-numbers"></a>Per modificare un numero di telefono

1. Dalla sezione **Specificare la modalità di risposta** della pagina **Verifica aggiuntiva di sicurezza**, aggiornare le informazioni sul numero di telefono per il **Telefono per l'autenticazione** (dispositivo mobile primario) e il **Telefono ufficio**.

1. Selezionare la casella accanto all'opzione **telefono per l'autenticazione alternativo** , quindi digitare un numero di telefono secondario in cui è possibile ricevere chiamate telefoniche se non è possibile accedere al dispositivo primario.

1. Selezionare **Salva**.

## <a name="add-a-new-account-to-the-microsoft-authenticator-app"></a>Aggiungere un nuovo account all'app Microsoft Authenticator

È possibile configurare l'account aziendale o dell'istituto di istruzione sull'app Microsoft Authenticator per [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) o [iOS](https://apps.apple.com/app/microsoft-authenticator/id983156458).

Se l'account aziendale o dell'istituto di istruzione è già stato configurato nell'app Microsoft Authenticator, non è necessario ripetere l'operazione.

1. Dalla sezione **Specificare la modalità di risposta** della pagina **Verifica aggiuntiva di sicurezza**, selezionare **Configura l'app Authenticator**.

    ![Configurare l'account aziendale o dell'istituto di istruzione nell'app Microsoft Authenticator](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page-auth-app.png)

1. Seguire le istruzioni visualizzate, incluso l'uso del dispositivo mobile, per eseguire la scansione del codice a matrice, quindi selezionare **Avanti**.

    Verrà chiesto di approvare una notifica tramite l'app Microsoft Authenticator per verificare le informazioni.

1. Selezionare **Salva**.

## <a name="delete-your-account-or-device-from-the-microsoft-authenticator-app"></a>Eliminare l'account o il dispositivo dall'app Microsoft Authenticator

È possibile eliminare l'account dall'app Microsoft Authenticator ed eliminare il dispositivo dall'account aziendale o dell'istituto di istruzione. In genere si elimina il dispositivo per rimuovere definitivamente un dispositivo perso, rubato od obsoleto dall'account, mentre si elimina l'account per tentare di risolvere alcuni problemi di connessione o in caso di modifiche dell'account, ad esempio un nuovo nome utente.

### <a name="to-delete-your-device-from-your-work-or-school-account"></a>Per eliminare il dispositivo dall'account aziendale o dell'istituto di istruzione

1. Dalla sezione **Specificare la modalità di risposta** della pagina **Verifica aggiuntiva di sicurezza**, selezionare il pulsante **Configura l'app Authenticator**.

1. Selezionare **Salva**.

### <a name="to-delete-your-account-from-the-microsoft-authenticator-app"></a>Per eliminare l'account dall'app Microsoft Authenticator

Dall'app Microsoft Authenticator, selezionare il pulsante **Elimina** accanto al dispositivo da eliminare.

## <a name="turn-on-two-factor-verification-prompts-on-a-trusted-device"></a>Attivare richieste di verifica a due fattori su un dispositivo considerato attendibile

A seconda delle impostazioni dell'organizzazione si potrebbe visualizzare una casella di controllo del tipo **Non mostrare più la richiesta per X giorni** quando si esegue la verifica a due fattori nel browser. Se è stata selezionata questa opzione per arrestare i prompt di verifica a due fattori e il dispositivo viene smarrito o potenzialmente compromesso, è necessario attivare la richiesta di verifica a due fattori per proteggere l'account. È necessario attivare i prompt per tutti i dispositivi contemporaneamente. Sfortunatamente, non è possibile attivare di nuovo i prompt per un dispositivo specifico.

### <a name="to-turn-two-factor-verification-prompts-back-on-for-your-devices"></a>Per attivare le richieste di verifica a due fattori per un dispositivo

Nella pagina [**Verifica aggiuntiva di sicurezza**](#to-get-to-the-additional-security-verification-page), selezionare **Ripristina Multi-Factor Authentication nei dispositivi precedentemente attendibili**. Al successivo accesso da qualsiasi dispositivo verrà richiesto di eseguire la verifica a due fattori.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver aggiunto o aggiornato le impostazioni di verifica a due fattori, è possibile gestire le password per le app, effettuare l'accesso oppure ottenere informazioni su alcuni problemi comuni relativi alla verifica a due fattori.

- [Gestire le password per le app per la verifica a due fattori](multi-factor-authentication-end-user-app-passwords.md) per app che non supportano la verifica a due fattori.

- [Procedura di accesso mediante verifica a due fattori](multi-factor-authentication-end-user-signin.md)

- [Risolvere i problemi comuni relativi alla verifica a due fattori](multi-factor-authentication-end-user-troubleshoot.md)