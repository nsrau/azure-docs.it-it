---
title: Configurare le informazioni di sicurezza per l'uso di un'app di autenticazione - Azure Active Directory | Microsoft Docs
description: Configurare le informazioni di sicurezza per verificare l'identità tramite l'app Microsoft Authenticator.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.component: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.openlocfilehash: 142d3442db9156876430b196a8e15b506db80018
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54814461"
---
# <a name="set-up-security-info-to-use-an-authenticator-app-preview"></a>Configurare le informazioni di sicurezza per l'uso di un'app di autenticazione (anteprima)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

La configurazione delle informazioni di sicurezza richiede l'accesso all'account aziendale o dell'istituto di istruzione e il completamento del processo di registrazione. Se le informazioni di sicurezza non sono mai state configurate, verrà chiesto di farlo.

## <a name="set-up-the-microsoft-authenticator-app"></a>Configurare l'app Microsoft Authenticator

A seconda delle impostazioni dell'organizzazione, può venire chiesto di configurare l'app Microsoft Authenticator al momento dell'accesso. In caso contrario, per iniziare a configurare l'app Microsoft Authenticator nelle informazioni di sicurezza, seguire i passaggi in [Gestire le informazioni di sicurezza](security-info-manage-settings.md).

Per scaricare l'app Microsoft Authenticator e ottenere altre informazioni, vedere [Introduzione all'app Microsoft Authenticator](microsoft-authenticator-app-how-to.md).

>[!Note]
>Se non si vuole usare l'app Microsoft Authenticator, è possibile scegliere un'app diversa durante la configurazione. In questo articolo viene usata l'app Microsoft Authenticator. Se l'opzione relativa all'app di autenticazione non viene visualizzata, è possibile che l'organizzazione non consenta l'uso di un'app di autenticazione per la verifica. In questo caso, è necessario scegliere un altro metodo oppure contattare l'amministratore per ottenere assistenza.

### <a name="to-use-the-microsoft-authenticator-app"></a>Per usare l'app Microsoft Authenticator

1. Selezionare l'opzione **App Authenticator**.

    Verrà visualizzata la procedura guidata **Scarica l'app**.

    ![Schermata iniziale della procedura guidata di download dell'app](media/security-info/security-info-auth-app-wizard.png)

    Se non si vuole usare l'app Microsoft Authenticator, è possibile fare clic sul collegamento **I want to use a different authenticator app** (Desidero usare un'app di autenticazione diversa) nella schermata **Scarica l'app**.

2. Dopo avere installato l'app Microsoft Authenticator, selezionare **Avanti**.

    Se viene chiesto, consentire le notifiche, aggiungere un nuovo account e quindi selezionare **Account aziendale o dell'istituto di istruzione**.

3. Selezionare **Avanti**.

    Verrà visualizzata la schermata **Esegui la scansione del codice a matrice**.

    ![Eseguire la scansione del codice a matrice tramite l'app Authenticator](media/security-info/security-info-scan-qr.png)

4. Aprire l'app Microsoft Authenticator, selezionare **Aggiungi account** dall'icona **Customize and control** (Personalizza e controlla) nell'angolo in alto a destra e quindi selezionare **Account aziendale o dell'istituto di istruzione**. 

5. Se si ha un'app lettore di codici a matrice, eseguire la scansione del codice fornito. Se non si ha un'app lettore di codici, è possibile selezionare **Can't scan the QR code link** (Impossibile eseguire la scansione del collegamento del codice a matrice) e immettere manualmente il codice e l'URL nell'app Microsoft Authenticator.

6. Usare l'app Microsoft Authenticator per approvare la notifica di attivazione dell'app.

    Le informazioni di sicurezza vengono aggiornate per usare l'app Microsoft Authenticator per verificare l'identità quando si usa la verifica in due passaggi o la reimpostazione della password self-service.

    >[!Note]
    >Se l'organizzazione lo consente, inoltre si otterrà un codice di verifica con la notifica dell'app Microsoft Authenticator. Se si desidera rendere il codice il metodo predefinito, seguire le istruzioni in [gestire le informazioni di sicurezza](security-info-setup-auth-app.md).

## <a name="additional-security-info-options"></a>Opzioni aggiuntive per le informazioni di sicurezza

In base all'operazione da eseguire, sono disponibili opzioni aggiuntive relative alle modalità di contatto da parte dell'organizzazione per verificare l'identità dell'utente, Queste opzioni includono:

- **SMS su dispositivo mobile.** Immettere il numero del dispositivo mobile per ricevere via SMS un codice da usare per la verifica in due passaggi o la reimpostazione della password. Per istruzioni dettagliate su come verificare l'identità con un SMS, vedere [Configurare le informazioni di sicurezza per l'invio di SMS](security-info-setup-text-msg.md).

- **Chiamata sul dispositivo mobile o sul telefono dell'ufficio.** Immettere il numero del dispositivo mobile per ricevere una chiamata per la verifica in due passaggi o la reimpostazione della password. Per istruzioni dettagliate su come verificare l'identità con un numero di telefono, vedere [Configurare le informazioni di sicurezza per l'uso di chiamate telefoniche](security-info-setup-phone-number.md).

- **Indirizzo di posta elettronica.** Immettere l'indirizzo di posta elettronica aziendale o dell'istituto di istruzione per ricevere un messaggio di posta elettronica per la reimpostazione della password. Questa opzione non è disponibile per la verifica in due passaggi. Per istruzioni dettagliate su come configurare l'indirizzo di posta elettronica, vedere [Configurare le informazioni di sicurezza per l'uso della posta elettronica](security-info-setup-email.md).

- **Domande di sicurezza.** Rispondere ad alcune domande di sicurezza create dall'amministratore per l'organizzazione. Questa opzione è disponibile solo per la reimpostazione della password e non per la verifica in due passaggi. Per istruzioni dettagliate su come configurare le domande di sicurezza, vedere l'articolo [Configurare le informazioni di sicurezza per l'uso di domande di sicurezza](security-info-setup-questions.md).
    
    >[!Note]
    >Se alcune opzioni non sono disponibili, è probabile che l'organizzazione non consenta di usare questi metodi. In questo caso, è necessario scegliere un metodo disponibile oppure contattare l'amministratore per ottenere assistenza.

## <a name="next-steps"></a>Passaggi successivi

- Se è necessario aggiornare le informazioni di sicurezza, seguire le istruzioni descritte nell'articolo [Gestire le informazioni di sicurezza](security-info-manage-settings.md).

- Se la password viene persa o dimenticata, reimpostarla dal [portale per la reimpostazione della password](https://passwordreset.microsoftonline.com/) oppure seguire i passaggi nell'articolo [Reimpostare la password aziendale o dell'istituto di istruzione](user-help-reset-password.md).

- Ottenere informazioni e suggerimenti per la risoluzione dei problemi di accesso nell'articolo [Quando non riesci ad accedere al tuo account Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).