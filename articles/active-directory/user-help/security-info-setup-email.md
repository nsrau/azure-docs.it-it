---
title: Configurare le informazioni di sicurezza per l'uso della posta elettronica - Azure Active Directory | Microsoft Docs
description: Configurare le informazioni di sicurezza per verificare la propria identità usando l'indirizzo di posta elettronica aziendale o dell'istituto di istruzione.
services: active-directory
author: eross-msft
manager: mtillman
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.component: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.openlocfilehash: 34696802735fd2a145cd6383dcdc7e4a4b6368e2
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2018
ms.locfileid: "39391410"
---
# <a name="set-up-security-info-to-use-email-preview"></a>Configurare le informazioni di sicurezza per l'uso della posta elettronica (anteprima)

[!INCLUDE[preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

La configurazione delle informazioni di sicurezza richiede l'accesso all'account aziendale o dell'istituto di istruzione e il completamento del processo di registrazione. Se le informazioni di sicurezza non sono mai state configurate, verrà chiesto di farlo.

## <a name="set-up-email"></a>Configurare la posta elettronica

A seconda delle impostazioni dell'organizzazione, al momento dell'accesso può essere chiesto di aggiungere alle informazioni di sicurezza un indirizzo di posta elettronica. In caso contrario, per iniziare a configurare la posta elettronica nelle informazioni di sicurezza, seguire la procedura descritta in [Gestire le informazioni di sicurezza](security-info-manage-settings.md).

>[!Note]
>È consigliabile usare un account di posta elettronica che non richieda alcuna password di rete per accedere.<br>Se l'opzione relativa alla posta elettronica non viene visualizzata, è possibile che l'organizzazione non consenta l'uso della posta elettronica per la verifica. In questo caso è necessario scegliere un altro metodo oppure contattare l'amministratore per ottenere assistenza.

### <a name="to-use-your-email-address"></a>Per usare il proprio indirizzo di posta elettronica

1. Selezionare l'opzione **Email**, quindi digitare l'indirizzo di posta elettronica nella casella. Questo non può essere l'indirizzo di posta elettronica aziendale o dell'istituto di istruzione.

     ![Pagina delle informazioni di sicurezza, con casella di immissione dell'indirizzo di posta elettronica](media/security-info/security-info-keep-secure-setup-email.png)

2. Controllare la presenza di un messaggio di posta elettronica da Microsoft per l'organizzazione, digitare il codice di verifica incluso nella casella **Verify your email** (Controlla la posta elettronica), quindi selezionare **Operazione completata**.

     ![Pagina delle informazioni di sicurezza, con casella di immissione del codice di verifica tramite posta elettronica](media/security-info/security-info-verify-email.png)

    >[!Note]
    >Se non viene visualizzato un messaggio di posta elettronica da Microsoft per conto dell'organizzazione, assicurarsi di aver digitato correttamente l'indirizzo di posta elettronica e quindi controllare le cartelle della posta indesiderata o dello spam.

3. Nella pagina **Proteggi l'account**, selezionare **Operazione completata**.

    Le informazioni di sicurezza vengono aggiornate per usare l'indirizzo di posta elettronica al fine di controllare l'identità quando si usa la verifica in due passaggi.

## <a name="additional-security-info-options"></a>Opzioni aggiuntive per le informazioni di sicurezza

Sono disponibili opzioni relative alle modalità di contatto da parte dell'organizzazione per verificare l'identità dell'utente, a seconda dell'operazione che si sta cercando di eseguire. Queste opzioni sono:

- **App Authenticator.** Scaricare e usare l'app Authenticator per ottenere una notifica di approvazione o un codice di approvazione generato in modo casuale per la verifica in due passaggi o la reimpostazione della password. Per istruzioni dettagliate su come configurare e usare l'app Microsoft Authenticator, vedere [Configurare le informazioni di sicurezza per l'uso di un'app di autenticazione](security-info-setup-auth-app.md).

- **SMS su dispositivo mobile.** Immettere il numero del dispositivo mobile per ricevere via SMS un codice da usare per la verifica in due passaggi o la reimpostazione della password. Per istruzioni dettagliate su come verificare l'identità con un messaggio di testo (SMS), vedere [Configurare le informazioni di sicurezza per l'invio di SMS](security-info-setup-text-msg.md).

- **Chiamata sul telefono cellulare o di lavoro.** Immettere il numero del dispositivo mobile per ricevere una chiamata per la verifica in due passaggi o la reimpostazione della password. Per istruzioni dettagliate su come verificare l'identità con un numero di telefono, vedere [Configurare le informazioni di sicurezza per l'uso di chiamate telefoniche](security-info-setup-phone-number.md).

- **Domande di sicurezza.** Rispondere ad alcune domande di sicurezza create dall'amministratore per l'organizzazione. Questa opzione è disponibile solo per la reimpostazione della password e non per la verifica in due passaggi. Per istruzioni dettagliate su come configurare le domande di sicurezza, vedere l'articolo [Configurare le informazioni di sicurezza per l'uso di domande di sicurezza](security-info-setup-questions.md).
    
    >[!Note]
    >Se alcune opzioni non sono disponibili, è probabile che l'organizzazione non consenta di usare questi metodi. In questo caso è necessario scegliere un metodo disponibile oppure contattare l'amministratore per ottenere assistenza.

## <a name="next-steps"></a>Passaggi successivi

- Se è necessario aggiornare le informazioni di sicurezza, seguire le istruzioni descritte nell'articolo [Gestire le informazioni di sicurezza](security-info-manage-settings.md).

- Se la password viene persa o dimenticata, reimpostarla nel [portale per la reimpostazione della password](https://passwordreset.microsoftonline.com/) oppure seguire la procedura descritta nell'articolo [Reimpostare la password aziendale o dell'istituto di istruzione](user-help-reset-password.md).

- Leggere l'articolo [Non è possibile accedere all'account Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) per ottenere informazioni e suggerimenti per la risoluzione dei problemi di accesso.