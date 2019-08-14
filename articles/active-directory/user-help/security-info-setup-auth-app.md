---
title: Configurare le informazioni di sicurezza (anteprima) per l'uso di un'app di autenticazione - Azure Active Directory | Microsoft Docs
description: Configurare le informazioni di sicurezza per verificare l'identità tramite l'app Microsoft Authenticator.
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
ms.openlocfilehash: a5fff27094881ffbada2bda7937fa714ffc07133
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68954359"
---
# <a name="set-up-security-info-preview-to-use-an-authenticator-app"></a>Configurare le informazioni di sicurezza (anteprima) per l'uso di un'app di autenticazione

È possibile seguire questi passaggi per aggiungere i metodi di verifica a due fattori e di reimpostazione della password. Dopo averle configurate per la prima volta, è possibile tornare alla pagina **Informazioni di sicurezza** per aggiungere, aggiornare o eliminare le informazioni di sicurezza.

Se viene chiesto di configurarle subito dopo aver effettuato l'accesso all'account aziendale o dell'istituto di istruzione, vedere i passaggi dettagliati nell'articolo [Configurare le informazioni di sicurezza (anteprima) dal messaggio di richiesta della pagina di accesso](security-info-setup-signin.md).

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Se l'opzione relativa all'app di autenticazione non viene visualizzata, è possibile che l'organizzazione non consenta l'uso di un'app di autenticazione per la verifica. In questo caso, è necessario scegliere un altro metodo oppure contattare l'amministratore per ricevere assistenza.

## <a name="set-up-the-microsoft-authenticator-app-from-the-security-info-page"></a>Configurare l'app Microsoft Authenticator dalla pagina Informazioni di sicurezza

A seconda delle impostazioni dell'organizzazione, potrebbe essere possibile usare un'app di autenticazione come metodo per le informazioni di sicurezza. Non è necessario usare l'app Microsoft Authenticator ed è possibile scegliere un'app diversa durante il processo di configurazione. Tuttavia, in questo articolo viene usata l'app Microsoft Authenticator.

### <a name="to-set-up-the-microsoft-authenticator-app"></a>Per configurare l'app Microsoft Authenticator

1. Accedere all'account aziendale o dell'istituto di istruzione, quindi andare alla pagina https://myprofile.microsoft.com/.

    ![Pagina Profilo personale con i collegamenti evidenziati delle Informazioni di sicurezza](media/security-info/securityinfo-myprofile-page.png)

2. Selezionare **Informazioni di sicurezza** dal riquadro di spostamento a sinistra o dal collegamento nel blocco **Informazioni di sicurezza**, quindi selezionare **Aggiungi metodo** dalla pagina **Informazioni di sicurezza**.

    ![Pagina Informazioni di sicurezza con l'opzione Aggiungi metodo evidenziata](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Nella pagina **Aggiungi un metodo** selezionare **App Authenticator** dall'elenco a discesa e scegliere **Aggiungi**.

    ![Aggiungere la casella metodo con l'app Authenticator selezionata](media/security-info/securityinfo-myprofile-addauthapp.png)

4. Nella pagina **Ottenere prima di tutto l'app** selezionare **Scarica adesso** per scaricare e installare l'app Microsoft Authenticator sul dispositivo mobile, quindi selezionare **Avanti**.

    Per altre informazioni su come scaricare e installare l'app, vedere [Scaricare e installare l'app Microsoft Authenticator](user-help-auth-app-download-install.md).

    ![Pagina Ottenere prima di tutto l'app](media/security-info/securityinfo-myprofile-getauthapp.png)

   > [!Note]
   > Se si vuole usare un'app di autenticazione diversa dall'app Microsoft Authenticator, fare clic sul collegamento **Desidero usare un'app di autenticazione diversa**.
   >
   > Se l'organizzazione consente di scegliere un metodo diverso oltre all'app di autenticazione, è possibile fare clic sul collegamento **Desidero configurare un metodo diverso**.

5. Rimanere sulla pagina **Configurare l'account** durante la configurazione dell'app Microsoft Authenticator sul dispositivo mobile.

    ![Pagina Configurare l'app di autenticazione](media/security-info/securityinfo-myprofile-setupauthapp.png)

6. Aprire l'app Microsoft Authenticator, scegliere di consentire le notifiche (se richiesto), selezionare **Aggiungi account** dall'icona **Customize and control** (Personalizza e controlla) in alto a destra e quindi selezionare **Account aziendale o dell'istituto di istruzione**.

    >[!Note]
    >Se è la prima volta che si configura l'app Microsoft Authenticator, è possibile che venga visualizzato un messaggio che chiede se consentire all'app di accedere alla fotocamera (iOS) o per consentire all'app di scattare foto e registrare video (Android). È necessario selezionare **Consenti** , in modo che l'app Authenticator possa accedere alla fotocamera per scattare un'immagine del codice a matrice nel passaggio successivo. Se non si consente la fotocamera, è comunque possibile configurare l'app Authenticator, ma è necessario aggiungere manualmente le informazioni sul codice. Per informazioni su come aggiungere manualmente il codice, vedere vedere [aggiungere manualmente un account all'app](user-help-auth-app-add-account-manual.md).

7. Tornare alla pagina **Configura l'account** sul computer e quindi selezionare **Avanti**.

    Verrà visualizzata la pagina **Esegui la scansione del codice a matrice**.

    ![Eseguire la scansione del codice a matrice tramite l'app Authenticator](media/security-info/securityinfo-myprofile-qrcodeauthapp.png)

8. Eseguire la scansione del codice fornito con il lettore di codice a matrice dell'app Microsoft Authenticator, visualizzato sul dispositivo mobile dopo la creazione dell'account aziendale o dell'istituto di istruzione nel passaggio 6.

    L'app di autenticazione dovrebbe aggiungere correttamente l'account aziendale o dell'istituto di istruzione senza richiedere informazioni aggiuntive. Se invece il lettore di codice a matrice non riesce a leggere il codice, è possibile selezionare il link **Non è possibile eseguire la scansione del codice a matrice** e immettere manualmente il codice e l'URL nell'app Microsoft Authenticator. Per altre informazioni sull'aggiunta manuale di un codice, vedere [Aggiungere manualmente un account all'app](user-help-auth-app-add-account-manual.md).

9. Selezionare **Avanti** nella pagina **Esegui la scansione del codice a matrice** sul computer.

    Verrà inviata una notifica all'app Microsoft Authenticator sul dispositivo mobile per testare l'account.

    ![Testare l'account con l'app di autenticazione](media/security-info/securityinfo-myprofile-tryitauthapp.png)

10. Approvare la notifica nell'app Microsoft Authenticator, quindi selezionare **Avanti**.

     ![Notifica di operazione riuscita e connessione dell'app e dell'account](media/security-info/securityinfo-myprofile-successauthapp.png)

     Le informazioni di sicurezza vengono aggiornate in modo da usare per impostazione predefinita l'app Microsoft Authenticator per verificare l'identità quando si usa la verifica in due passaggi o la reimpostazione della password.

## <a name="delete-your-authenticator-app-from-your-security-info-methods"></a>Eliminare l'app di autenticazione dai metodi di gestione delle informazioni di sicurezza

Per non usare più l'app di autenticazione come metodo di gestione delle informazioni di sicurezza, è possibile rimuoverla dalla pagina **Informazioni di sicurezza**. Questa opzione funziona per tutte le app di autenticazione, non solo l'app Microsoft Authenticator. Dopo aver eliminato l'app, è necessario accedere all'app di autenticazione sul dispositivo mobile ed eliminare l'account.

>[!Important]
>Se si elimina per errore l'app di autenticazione, non è possibile annullare l'azione. Sarà necessario aggiungere nuovamente l'app di autenticazione, seguendo i passaggi illustrati nella sezione [Configurare l'app Authenticator](#set-up-the-microsoft-authenticator-app-from-the-security-info-page) di questo articolo.

### <a name="to-delete-the-authenticator-app"></a>Per eliminare l'app di autenticazione

1. Nella pagina **Informazioni di sicurezza** selezionare il collegamento **Elimina** accanto all'app Authenticator.

    ![Collegamento per eliminare l'app di autenticazione dalle informazioni di sicurezza](media/security-info/securityinfo-myprofile-deleteauthapp.png)

2. Selezionare **Sì** nella casella di conferma per eliminare l'app di autenticazione. L'app di autenticazione, dopo essere stata eliminata, viene rimossa dalle informazioni di sicurezza e non viene più visualizzata nella pagina **Informazioni di sicurezza**. Se l'app di autenticazione è il metodo predefinito, verrà sostituito con un altro metodo disponibile.

3. Aprire l'app di autenticazione sul dispositivo mobile, selezionare **Modifica account**e quindi eliminare l'account aziendale o dell'istituto di istruzione dall'app di autenticazione.

    L'account verrà completamente rimosso dall'app di autenticazione per le richieste di verifica a due fattori e reimpostazione della password.

## <a name="change-your-default-security-info-method"></a>Modificare il metodo di gestione delle informazioni di sicurezza predefinito

Se si desidera impostare l'app di autenticazione come metodo predefinito da usare quando si accede all'account aziendale o dell'istituto di istruzione con la verifica a due fattori o per le richieste di reimpostazione della password, è possibile farlo dalla pagina **Informazioni di sicurezza**.

### <a name="to-change-your-default-security-info-method"></a>Per modificare il metodo di gestione delle informazioni di sicurezza predefinito

1. Nella pagina **Informazioni di sicurezza** selezionare il collegamento **Modifica** accanto alle informazioni **Metodo di accesso predefinito**.

    ![Modificare il collegamento per il metodo di accesso predefinito](media/security-info/securityinfo-myprofile-changedefaultauthapp.png)

2. Scegliere **Microsoft Authenticator - Notifica** dall'elenco a discesa di metodi disponibili. Se non si usa l'app Microsoft Authenticator, selezionare l'opzione **App Authenticator o token hardware**.

    ![Scegliere il metodo di accesso predefinito](media/security-info/securityinfo-myprofile-defaultauthapp.png)

3. Selezionare **Conferma**.

    Il metodo predefinito usato per l'accesso passa all'app Microsoft Authenticator.

## <a name="additional-security-info-methods"></a>Opzioni aggiuntive per le informazioni di sicurezza

In base all'operazione da eseguire, sono disponibili opzioni aggiuntive relative alle modalità di contatto da parte dell'organizzazione per verificare l'identità dell'utente, Queste opzioni includono:

- **SMS su dispositivo mobile.** Immettere il numero del dispositivo mobile per ricevere via SMS un codice da usare per la verifica in due passaggi o la reimpostazione della password. Per istruzioni dettagliate su come verificare l'identità con un SMS, vedere [Configurare le informazioni di sicurezza per l'invio di SMS](security-info-setup-text-msg.md).

- **Chiamata sul dispositivo mobile o sul telefono dell'ufficio.** Immettere il numero del dispositivo mobile per ricevere una chiamata per la verifica in due passaggi o la reimpostazione della password. Per istruzioni dettagliate su come verificare l'identità con un numero di telefono, vedere [Configurare le informazioni di sicurezza per l'uso di chiamate telefoniche](security-info-setup-phone-number.md).

- **Chiave di sicurezza.** Registrare la chiave di sicurezza compatibile con Microsoft e usarla insieme a un PIN per la verifica in due passaggi o la reimpostazione della password. Per istruzioni dettagliate su come verificare l'identità con una chiave di sicurezza, vedere [configurare le informazioni di sicurezza per l'uso di una chiave di sicurezza](security-info-setup-security-key.md).

- **Indirizzo di posta elettronica.** Immettere l'indirizzo di posta elettronica aziendale o dell'istituto di istruzione per ricevere un messaggio di posta elettronica per la reimpostazione della password. Questa opzione non è disponibile per la verifica in due passaggi. Per istruzioni dettagliate su come configurare l'indirizzo di posta elettronica, vedere [Configurare le informazioni di sicurezza per l'uso della posta elettronica](security-info-setup-email.md).

- **Domande di sicurezza.** Rispondere ad alcune domande di sicurezza create dall'amministratore per l'organizzazione. Questa opzione è disponibile solo per la reimpostazione della password e non per la verifica in due passaggi. Per istruzioni dettagliate su come configurare le domande di sicurezza, vedere l'articolo [Configurare le informazioni di sicurezza per l'uso di domande di sicurezza](security-info-setup-questions.md).

    >[!Note]
    >Se alcune opzioni non sono disponibili, è probabile che l'organizzazione non consenta di usare questi metodi. In questo caso, è necessario scegliere un metodo disponibile oppure contattare l'amministratore per ottenere assistenza.

## <a name="next-steps"></a>Passaggi successivi

- Se la password viene persa o dimenticata, reimpostarla dal [portale per la reimpostazione della password](https://passwordreset.microsoftonline.com/) oppure seguire i passaggi nell'articolo [Reimpostare la password aziendale o dell'istituto di istruzione](user-help-reset-password.md).

- Ottenere informazioni e suggerimenti per la risoluzione dei problemi di accesso nell'articolo [Quando non riesci ad accedere al tuo account Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
