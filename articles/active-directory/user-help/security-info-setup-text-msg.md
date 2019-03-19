---
title: Impostare le tue info di sicurezza (anteprima) per usare la messaggistica di testo - Azure Active Directory | Microsoft Docs
description: Come configurare le informazioni di sicurezza per verificare la propria identità usando l'invio di SMS e il dispositivo mobile.
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
ms.openlocfilehash: ea9e4ae21ecc6538b33aed1566c10ddcd22b86c7
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57341035"
---
# <a name="set-up-security-info-preview-to-use-text-messaging"></a>Configurare le informazioni di sicurezza (anteprima) per l'uso di SMS
È possibile seguire questi passaggi per aggiungere i metodi di verifica a due fattori e di reimpostazione della password. Dopo averle configurate per la prima volta, è possibile tornare alla pagina **Informazioni di sicurezza** per aggiungere, aggiornare o eliminare le informazioni di sicurezza.

Se viene chiesto di configurarle subito dopo aver effettuato l'accesso all'account aziendale o dell'istituto di istruzione, vedere la procedura dettagliata nell'articolo [Configurare le informazioni di sicurezza dal prompt della pagina di accesso](security-info-setup-signin.md).

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Se non viene visualizzata un'opzione relativa al telefono, è possibile che l'organizzazione non consenta l'uso di un numero di telefono per la verifica. In questo caso, è necessario scegliere un altro metodo oppure contattare l'amministratore per ottenere assistenza.

## <a name="set-up-text-messages-from-the-security-info-page"></a>Configurare gli SMS dalla pagina Info di sicurezza
A seconda delle impostazioni dell'organizzazione, potrebbe essere possibile usare l'invio di SMS come metodo per le informazioni di sicurezza. L'opzione relativa agli SMS fa parte delle opzioni del telefono. La configurazione è quindi analoga a quella del numero di telefono, ma anziché scegliere di ricevere una telefonata da Microsoft, si sceglie di usare un SMS.

>[!Note]
>Se si vuole ricevere una chiamata telefonica anziché un SMS, seguire la procedura descritta nell'articolo [Configurare le informazioni di sicurezza per l'uso di chiamate telefoniche](security-info-setup-phone-number.md).

### <a name="to-set-up-text-messages"></a>Per configurare SMS

1. Accedere all'account aziendale o dell'istituto di istruzione e quindi passare alla pagina https://myprofile.microsoft.com/.

    ![Pagina Profilo personale con i collegamenti evidenziati delle Informazioni di sicurezza](media/security-info/securityinfo-myprofile-page.png)

2. Selezionare **Informazioni di sicurezza** dal riquadro di spostamento a sinistra o dal collegamento nel blocco **Informazioni di sicurezza**, quindi selezionare **Aggiungi metodo** dalla pagina **Informazioni di sicurezza**.

    ![Pagina Informazioni di sicurezza con l'opzione Aggiungi metodo evidenziata](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Nella pagina **Aggiungi un metodo** selezionare **Telefono** dall'elenco a discesa e selezionare **Aggiungi**.

    ![Finestra di dialogo Aggiungi un metodo, con Telefono selezionato](media/security-info/securityinfo-myprofile-addphonetext.png)

4. Nella pagina **Telefono** digitare il numero di telefono del dispositivo mobile, scegliere **Invia un SMS** e quindi selezionare **Avanti**.

    ![Aggiungere il numero di telefono e scegliere gli SMS](media/security-info/securityinfo-myprofile-phonetext-addnumber.png)

5. Digitare il codice inviato tramite SMS al dispositivo mobile e quindi selezionare **Avanti**.

    ![Aggiungere il numero di telefono e scegliere gli SMS](media/security-info/securityinfo-myprofile-phonetext-entercode.png)

    La pagina cambia per visualizzare l'esito positivo dell'operazione.

    ![Notifica di operazione riuscita, connessione del numero di telefono, scelta di ricevere SMS e account](media/security-info/securityinfo-myprofile-phonetext-success.png)

    Le informazioni di sicurezza vengono aggiornate ed è possibile usare l'invio di SMS per verificare l'identità quando si usa la verifica in due passaggi o la reimpostazione della password. Per impostare l'invio di SMS come metodo predefinito, vedere la sezione [Cambiare il metodo di gestione delle informazioni di sicurezza predefinito](#change-your-default-security-info-method) di questo articolo.

## <a name="delete-text-messaging-from-your-security-info-methods"></a>Eliminare l'invio di SMS dai metodi di gestione delle informazioni di sicurezza
Per non usare più gli SMS come metodo di gestione delle informazioni di sicurezza, è possibile rimuoverlo dalla pagina **Info di sicurezza**.

>[!Important]
>Se si elimina per errore l'invio di SMS, non è possibile annullare l'azione. Sarà necessario aggiungere nuovamente il metodo, seguendo la procedura illustrata nella sezione [Configurare gli SMS](#set-up-text-messages-from-the-security-info-page) di questo articolo.

### <a name="to-delete-text-messaging"></a>Per eliminare l'invio di SMS

1. Nella pagina **Info di sicurezza** selezionare il collegamento **Elimina** accanto all'opzione **Telefono**.

    ![Collegamento per eliminare il metodo basato sul telefono e l'invio di SMS dalle informazioni di sicurezza](media/security-info/securityinfo-myprofile-phonetext-delete.png)

2. Selezionare **Sì** nella finestra di dialogo di conferma per eliminare il numero di **telefono**. Il numero di telefono, dopo essere stato eliminato, viene rimosso dalle informazioni di sicurezza e non viene più visualizzato nella pagina **Informazioni di sicurezza**. Se **Telefono** è il metodo predefinito, verrà sostituito con un altro metodo disponibile.

## <a name="change-your-default-security-info-method"></a>Cambiare il metodo di gestione delle informazioni di sicurezza predefinito
Per impostare l'invio di SMS come metodo predefinito da usare quando si accede all'account aziendale o dell'istituto di istruzione con la verifica a due fattori o per le richieste di reimpostazione della password, è possibile impostarlo dalla pagina **Info di sicurezza**.

### <a name="to-change-your-default-security-info-method"></a>Per cambiare il metodo di gestione delle informazioni di sicurezza predefinito

1. Nella pagina **Informazioni di sicurezza** selezionare il collegamento **Modifica** accanto alle informazioni **Metodo di accesso predefinito**.

    ![Collegamento Cambia per il metodo di accesso predefinito](media/security-info/securityinfo-myprofile-phonetext-defaultchange.png)

2. Selezionare **Telefono - SMS a (*_numero_telefono_utente_*)** dall'elenco a discesa di metodi disponibili e quindi selezionare **Conferma**.

    ![Scegliere il metodo per l'accesso predefinito](media/security-info/securityinfo-myprofile-phonetext-changeddefault.png)

    Il metodo predefinito usato per l'accesso passa a **Telefono - SMS a (*_numero_telefono_utente_*)**.

## <a name="additional-security-info-methods"></a>Metodi aggiuntivi per le informazioni di sicurezza
In base all'operazione da eseguire, sono disponibili opzioni aggiuntive relative alle modalità di contatto da parte dell'organizzazione per verificare l'identità dell'utente, tra cui:

- **App di autenticazione.** Scaricare e usare un'app di autenticazione per ottenere una notifica di approvazione o un codice di approvazione generato in modo casuale per la verifica in due passaggi o la reimpostazione della password. Per istruzioni dettagliate su come configurare e usare l'app Microsoft Authenticator, vedere [Configurare le informazioni di sicurezza per l'uso di un'app di autenticazione](security-info-setup-auth-app.md).

- **Chiamata sul dispositivo mobile o sul telefono dell'ufficio.** Immettere il numero del dispositivo mobile per ricevere una chiamata per la verifica in due passaggi o la reimpostazione della password. Per istruzioni dettagliate su come verificare l'identità con un numero di telefono, vedere [Configurare le informazioni di sicurezza per l'uso di chiamate telefoniche](security-info-setup-phone-number.md).

- **Indirizzo di posta elettronica.** Immettere l'indirizzo di posta elettronica aziendale o dell'istituto di istruzione per ricevere un messaggio di posta elettronica per la reimpostazione della password. Questa opzione non è disponibile per la verifica in due passaggi. Per istruzioni dettagliate su come configurare l'indirizzo di posta elettronica, vedere [Configurare le informazioni di sicurezza per l'uso della posta elettronica](security-info-setup-email.md).

- **Domande di sicurezza.** Rispondere ad alcune domande di sicurezza create dall'amministratore per l'organizzazione. Questa opzione è disponibile solo per la reimpostazione della password e non per la verifica in due passaggi. Per istruzioni dettagliate su come configurare le domande di sicurezza, vedere l'articolo [Configurare le informazioni di sicurezza per l'uso di domande di sicurezza](security-info-setup-questions.md).
    
    >[!Note]
    >Se alcune opzioni non sono disponibili, è probabile che l'organizzazione non consenta di usare questi metodi. In questo caso, è necessario scegliere un metodo disponibile oppure contattare l'amministratore per ottenere assistenza.

## <a name="next-steps"></a>Passaggi successivi

- Se la password viene persa o dimenticata, reimpostarla dal [portale per la reimpostazione della password](https://passwordreset.microsoftonline.com/) oppure seguire i passaggi nell'articolo [Reimpostare la password aziendale o dell'istituto di istruzione](user-help-reset-password.md).

- Ottenere informazioni e suggerimenti per la risoluzione dei problemi di accesso nell'articolo [Quando non riesci ad accedere al tuo account Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).