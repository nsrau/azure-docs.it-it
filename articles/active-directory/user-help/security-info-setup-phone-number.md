---
title: Configurare le informazioni di sicurezza per l'uso di chiamate telefoniche (anteprima) - Azure Active Directory Domain Services | Microsoft Docs
description: Configurare le informazioni di sicurezza per verificare l'identità tramite le chiamate telefoniche.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: b28330f8e81efbb496fb30a14e8ae10415ded27f
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160738"
---
# <a name="set-up-security-info-preview-to-use-phone-calls"></a>Configurare le informazioni di sicurezza per l'uso di chiamate telefoniche (anteprima)

È possibile seguire questi passaggi per aggiungere i metodi di verifica a due fattori e di reimpostazione della password. Dopo averle configurate per la prima volta, è possibile tornare alla pagina **Informazioni di sicurezza** per aggiungere, aggiornare o eliminare le informazioni di sicurezza.

Se viene chiesto di configurarle subito dopo aver effettuato l'accesso all'account aziendale o dell'istituto di istruzione, vedere i passaggi dettagliati nell'articolo [Configurare le informazioni di sicurezza (anteprima) dal messaggio di richiesta della pagina di accesso](security-info-setup-signin.md).

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

> [!Note]
> Le informazioni di sicurezza non supportano l'uso dei numeri di interno. Anche se si aggiunge il formato corretto, + 1 4255551234X12345, i numeri di interno vengono rimossi prima della chiamata.
>
> Se non viene visualizzata un'opzione relativa al telefono, è possibile che l'organizzazione non consenta l'uso di un numero di telefono per la verifica. In questo caso, è necessario scegliere un altro metodo oppure contattare l'amministratore per ricevere assistenza.

## <a name="set-up-phone-calls-from-the-security-info-page"></a>Configurare le chiamate telefoniche dalla pagina Informazioni di sicurezza

A seconda delle impostazioni dell'organizzazione, potrebbe essere possibile usare le chiamate telefoniche come metodo per le informazioni di sicurezza.

>[!Note]
>Se si vuole ricevere un SMS invece di una telefonata, seguire i passaggi descritti nell'articolo [Configurare le informazioni di sicurezza per l'uso di SMS](security-info-setup-text-msg.md).

### <a name="to-set-up-phone-calls"></a>Per configurare chiamate telefoniche

1. Accedere all'account aziendale o dell'istituto di istruzione, quindi andare alla pagina https://myprofile.microsoft.com/.

    ![Pagina Profilo personale con i collegamenti evidenziati delle Informazioni di sicurezza](media/security-info/securityinfo-myprofile-page.png)

2. Selezionare **Informazioni di sicurezza** dal riquadro di spostamento a sinistra o dal collegamento nel blocco **Informazioni di sicurezza**, quindi selezionare **Aggiungi metodo** dalla pagina **Informazioni di sicurezza**.

    ![Pagina Informazioni di sicurezza con l'opzione Aggiungi metodo evidenziata](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Nella pagina **Aggiungi un metodo** selezionare **Telefono** dall'elenco a discesa e selezionare **Aggiungi**.

    ![Casella Aggiungi un metodo, con Telefono selezionato](media/security-info/securityinfo-myprofile-addphonetext.png)

4. Nella pagina **Telefono** digitare il numero di telefono del proprio dispositivo mobile, scegliere **Chiamami**, quindi selezionare **Avanti**.

    ![Aggiungere il numero di telefono e scegliere chiamate telefoniche](media/security-info/securityinfo-myprofile-phonecall-addnumber.png)

5. Rispondere alla chiamata telefonica di verifica, effettuata sul numero di telefono immesso e seguire le istruzioni.

    La pagina cambia per visualizzare l'esito positivo dell'operazione.

    ![Notifica di operazione riuscita, connessione del numero di telefono, scelta per ricevere chiamate telefoniche e account](media/security-info/securityinfo-myprofile-phonetext-success.png)

    Le informazioni di sicurezza vengono aggiornate ed è possibile usare le chiamate telefoniche per verificare l'identità, quando si usa la verifica in due passaggi o la reimpostazione della password. Per impostare le chiamate telefoniche come metodo predefinito, vedere la sezione [Cambiare il metodo di gestione delle informazioni di sicurezza predefinito](#change-your-default-security-info-method) di questo articolo.

## <a name="delete-phone-calls-from-your-security-info-methods"></a>Eliminare le chiamate telefoniche dai metodi di gestione delle informazioni di sicurezza

Per non usare più le chiamate telefoniche come metodo di gestione delle informazioni di sicurezza, è possibile rimuoverle dalla pagina **Informazioni di sicurezza**.

>[!Important]
>Se si elimina per errore l'uso di chiamate telefoniche, non è possibile annullare l'azione. Sarà necessario aggiungere nuovamente il metodo, seguendo i passaggi illustrati nella sezione [Configurare le chiamate telefoniche](#set-up-phone-calls-from-the-security-info-page) di questo articolo.

### <a name="to-delete-phone-calls"></a>Per eliminare le chiamate telefoniche

1. Nella pagina **Informazioni di sicurezza** selezionare il collegamento **Elimina** accanto all'opzione **Telefono**.

    ![Collegamento per eliminare il metodo basato sul telefono dalle informazioni di sicurezza](media/security-info/securityinfo-myprofile-phonetext-delete.png)

2. Selezionare **Sì** nella casella di conferma per eliminare il numero di **telefono**. Il numero di telefono, dopo essere stato eliminato, viene rimosso dalle informazioni di sicurezza e non viene più visualizzato nella pagina **Informazioni di sicurezza**. Se **Telefono** è il metodo predefinito, verrà sostituito con un altro metodo disponibile.

## <a name="change-your-default-security-info-method"></a>Modificare il metodo di gestione delle informazioni di sicurezza predefinito

Per impostare le chiamate telefoniche come metodo predefinito da usare quando si accede all'account aziendale o dell'istituto di istruzione con la verifica a due fattori o per le richieste di reimpostazione della password, è possibile impostarle dalla pagina **Informazioni di sicurezza**.

### <a name="to-change-your-default-security-info-method"></a>Per modificare il metodo di gestione delle informazioni di sicurezza predefinito

1. Nella pagina **Informazioni di sicurezza** selezionare il collegamento **Modifica** accanto alle informazioni **Metodo di accesso predefinito**.

    ![Modificare il collegamento per il metodo di accesso predefinito](media/security-info/securityinfo-myprofile-phonetext-defaultchange.png)

2. Selezionare **Telefono - chiamata ( *_numero_telefono_utente_* )** dall'elenco a discesa di metodi disponibili, quindi selezionare **Conferma**.

    ![Scegliere il metodo di accesso predefinito](media/security-info/securityinfo-myprofile-phonecall-changeddefault.png)

    Il metodo predefinito usato per l'accesso passa a **Telefono - chiamata ( *_numero_telefono_utente_* )** .

## <a name="additional-security-info-methods"></a>Opzioni aggiuntive per le informazioni di sicurezza

In base all'operazione da eseguire, sono disponibili opzioni aggiuntive relative alle modalità di contatto da parte dell'organizzazione per verificare l'identità dell'utente, tra cui:

- **App di autenticazione.** Scaricare e usare un'app di autenticazione per ottenere una notifica di approvazione o un codice di approvazione generato in modo casuale per la verifica in due passaggi o la reimpostazione della password. Per istruzioni dettagliate su come configurare e usare l'app Microsoft Authenticator, vedere [Configurare le informazioni di sicurezza per l'uso di un'app di autenticazione](security-info-setup-auth-app.md).

- **Mobile device text.** (SMS su dispositivo mobile) Immettere il numero del dispositivo mobile per ricevere via SMS un codice da usare per la verifica in due passaggi o la reimpostazione della password. Per istruzioni dettagliate su come verificare l'identità con un SMS, vedere [Configurare le informazioni di sicurezza per l'invio di SMS](security-info-setup-text-msg.md).

- **Chiave di sicurezza.** Registrare la chiave di sicurezza compatibile con Microsoft e usarla insieme a un PIN per la verifica in due passaggi o la reimpostazione della password. Per istruzioni dettagliate su come verificare l'identità con una chiave di sicurezza, vedere [configurare le informazioni di sicurezza per l'uso di una chiave di sicurezza](security-info-setup-security-key.md).

- **Indirizzo di posta elettronica.** Immettere l'indirizzo di posta elettronica aziendale o dell'istituto di istruzione per ricevere un messaggio di posta elettronica per la reimpostazione della password. Questa opzione non è disponibile per la verifica in due passaggi. Per istruzioni dettagliate su come configurare l'indirizzo di posta elettronica, vedere [Configurare le informazioni di sicurezza per l'uso della posta elettronica](security-info-setup-email.md).

- **Domande di sicurezza.** Rispondere ad alcune domande di sicurezza create dall'amministratore per l'organizzazione. Questa opzione è disponibile solo per la reimpostazione della password e non per la verifica in due passaggi. Per istruzioni dettagliate su come configurare le domande di sicurezza, vedere l'articolo [Configurare le informazioni di sicurezza per l'uso di domande di sicurezza](security-info-setup-questions.md).

    >[!Note]
    >Se alcune opzioni non sono disponibili, è probabile che l'organizzazione non consenta di usare questi metodi. In questo caso, è necessario scegliere un metodo disponibile oppure contattare l'amministratore per ottenere assistenza.

## <a name="next-steps"></a>Passaggi successivi

- Se la password viene persa o dimenticata, reimpostarla dal [portale per la reimpostazione della password](https://passwordreset.microsoftonline.com/) oppure seguire i passaggi nell'articolo [Reimpostare la password aziendale o dell'istituto di istruzione](active-directory-passwords-update-your-own-password.md).

- Ottenere informazioni e suggerimenti per la risoluzione dei problemi di accesso nell'articolo [Quando non riesci ad accedere al tuo account Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
