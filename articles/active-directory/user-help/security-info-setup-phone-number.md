---
title: Configurare le informazioni di sicurezza per l'uso di chiamate telefoniche - Azure Active Directory | Microsoft Docs
description: Configurare le informazioni di sicurezza per verificare la propria identità usando un dispositivo mobile o il numero di telefono aziendale.
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
ms.openlocfilehash: 494557ce3836fde295ac583821dbc7024207a8a4
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55171916"
---
# <a name="set-up-security-info-to-use-phone-calls-preview"></a>Configurare le informazioni di sicurezza per l'uso di chiamate telefoniche (anteprima)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

La configurazione delle informazioni di sicurezza richiede l'accesso all'account aziendale o dell'istituto di istruzione e il completamento del processo di registrazione. Se le informazioni di sicurezza non sono mai state configurate, verrà chiesto di farlo.

## <a name="set-up-phone-calls"></a>Configurare chiamate telefoniche

A seconda delle impostazioni dell'organizzazione, al momento dell'accesso può essere chiesto di aggiungere un numero telefonico alle informazioni di sicurezza. In caso contrario, per iniziare a configurare le chiamate telefoniche nelle informazioni di sicurezza, seguire la procedura descritta in [Gestire le informazioni di sicurezza](security-info-manage-settings.md).

>[!Note]
>Le informazioni di sicurezza non supportano l'uso dei numeri di interno. Anche se si aggiunge il formato corretto, + 1 4255551234X12345, i numeri di interno vengono rimossi prima della chiamata.<br>Se l'opzione relativa al telefono non viene visualizzata, è possibile che l'organizzazione non consenta l'uso di chiamate telefoniche per la verifica. In questo caso, è necessario scegliere un altro metodo oppure contattare l'amministratore per ottenere assistenza.

### <a name="to-use-your-phone-number"></a>Per usare il numero di telefono

1. Selezionare l'opzione **Telefono**.

    Verrà visualizzata la procedura guidata **Configura il telefono**.

    ![Configurare il codice del paese o dell'area geografica e il numero di telefono](media/security-info/security-info-keep-secure-setup-phone.png)

2. Selezionare **il paese o l'area geografica** dalla casella di riepilogo a discesa, digitare il numero di telefono (incluso il prefisso, se applicabile) nella casella **Numero di telefono**, selezionare l'opzione **Chiamami** e quindi selezionare **Avanti**.

    Si riceve una chiamata telefonica per verificare che sia che stato digitato il numero di telefono corretto. A quel punto viene chiesto di premere il tasto cancelletto (#) per confermare e completare la configurazione.

    ![Verificare la schermata del telefono, mostra che si è risposto alla chiamata](media/security-info/security-info-keep-secure-verify-phone-call.png)

    Le informazioni di sicurezza vengono aggiornate per usare il numero di telefono al fine di controllare l'identità quando si usa la verifica in due passaggi o la reimpostazione della password in modalità self-service.

    >[!Note]
    >Se si vuole ricevere un SMS invece di una telefonata sul proprio dispositivo mobile, seguire la procedura descritta nell'articolo [Configurare le informazioni di sicurezza per l'invio di SMS](security-info-setup-text-msg.md).

## <a name="additional-security-info-options"></a>Opzioni aggiuntive per le informazioni di sicurezza

In base all'operazione da eseguire, sono disponibili opzioni aggiuntive relative alle modalità di contatto da parte dell'organizzazione per verificare l'identità dell'utente, tra cui:

- **App di autenticazione.** Scaricare e usare un'app di autenticazione per ottenere una notifica di approvazione o un codice di approvazione generato in modo casuale per la verifica in due passaggi o la reimpostazione della password. Per istruzioni dettagliate su come configurare e usare l'app Microsoft Authenticator, vedere [Configurare le informazioni di sicurezza per l'uso di un'app di autenticazione](security-info-setup-auth-app.md).

- **Mobile device text.** (SMS su dispositivo mobile) Immettere il numero del dispositivo mobile per ricevere via SMS un codice da usare per la verifica in due passaggi o la reimpostazione della password. Per istruzioni dettagliate su come verificare l'identità con un SMS, vedere [Configurare le informazioni di sicurezza per l'invio di SMS](security-info-setup-text-msg.md).

- **Indirizzo di posta elettronica.** Immettere l'indirizzo di posta elettronica aziendale o dell'istituto di istruzione per ricevere un messaggio di posta elettronica per la reimpostazione della password. Questa opzione non è disponibile per la verifica in due passaggi. Per istruzioni dettagliate su come configurare l'indirizzo di posta elettronica, vedere [Configurare le informazioni di sicurezza per l'uso della posta elettronica](security-info-setup-email.md).

- **Domande di sicurezza.** Rispondere ad alcune domande di sicurezza create dall'amministratore per l'organizzazione. Questa opzione è disponibile solo per la reimpostazione della password e non per la verifica in due passaggi. Per istruzioni dettagliate su come configurare le domande di sicurezza, vedere l'articolo [Configurare le informazioni di sicurezza per l'uso di domande di sicurezza](security-info-setup-questions.md).
    
    >[!Note]
    >Se alcune opzioni non sono disponibili, è probabile che l'organizzazione non consenta di usare questi metodi. In questo caso, è necessario scegliere un metodo disponibile oppure contattare l'amministratore per ottenere assistenza.

## <a name="next-steps"></a>Passaggi successivi

- Se è necessario aggiornare le informazioni di sicurezza, seguire le istruzioni descritte nell'articolo [Gestire le informazioni di sicurezza](security-info-manage-settings.md).

- Se la password viene persa o dimenticata, reimpostarla dal [portale per la reimpostazione della password](https://passwordreset.microsoftonline.com/) oppure seguire i passaggi nell'articolo [Reimpostare la password aziendale o dell'istituto di istruzione](user-help-reset-password.md).

- Ottenere informazioni e suggerimenti per la risoluzione dei problemi di accesso nell'articolo [Quando non riesci ad accedere al tuo account Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
