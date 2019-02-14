---
title: Configurare le informazioni di sicurezza per l'uso di SMS - Azure Active Directory | Microsoft Docs
description: Configurare le informazioni di sicurezza per verificare l'identità tramite un SMS.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9556c99e7e9655054ee5b834db36ba6e22e9e807
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56182652"
---
# <a name="set-up-security-info-to-use-text-messaging-preview"></a>Configurare le informazioni di sicurezza per l'uso di SMS (anteprima)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

La configurazione delle informazioni di sicurezza richiede l'accesso all'account aziendale o dell'istituto di istruzione e il completamento del processo di registrazione. Se le informazioni di sicurezza non sono mai state configurate, verrà chiesto di farlo.

## <a name="set-up-text-messaging"></a>Configurare SMS

A seconda delle impostazioni dell'organizzazione, al momento dell'accesso può venire chiesto di aggiungere gli SMS alle informazioni di sicurezza. In caso contrario, per iniziare a configurare gli SMS nelle informazioni di sicurezza, seguire i passaggi in [Gestire le informazioni di sicurezza](security-info-manage-settings.md).

L'opzione relativa agli SMS fa parte delle opzioni del telefono. La configurazione è quindi analoga a quella del numero di telefono, ma anziché scegliere di ricevere una telefonata da Microsoft, si sceglie di usare un SMS. Se l'opzione relativa al telefono non viene visualizzata, è possibile che l'organizzazione non consenta l'uso di un numero di telefono per la verifica. In questo caso, è necessario scegliere un altro metodo oppure contattare l'amministratore per ottenere assistenza.

### <a name="to-use-a-text-message"></a>Per usare un SMS

1. Selezionare l'opzione **Telefono**.

    Verrà visualizzata la procedura guidata **Configura il telefono**.

    ![Configurare il codice paese o area geografica e il numero di telefono](media/security-info/security-info-keep-secure-setup-text.png)

2. Selezionare **il paese o l'area geografica** dalla casella di riepilogo a discesa, digitare il numero di telefono (incluso il prefisso, se applicabile) nella casella **Numero di telefono**, selezionare l'opzione **Invia un SMS** e quindi selezionare **Avanti**.

    Si riceverà un SMS con un codice da immettere in una pagina di verifica.

    ![Pagina di verifica in cui immettere il codice dell'SMS](media/security-info/security-info-keep-secure-verify-text-msg.png)

    Le informazioni di sicurezza vengono aggiornate per l'invio di un SMS per verificare l'identità quando si usa la verifica in due passaggi o la reimpostazione della password self-service.

    >[!Note]
    >Se si vuole ricevere una chiamata telefonica anziché un SMS, seguire la procedura descritta nell'articolo [Configurare le informazioni di sicurezza per l'uso di chiamate telefoniche](security-info-setup-phone-number.md).

## <a name="additional-security-info-options"></a>Opzioni aggiuntive per le informazioni di sicurezza

Sono disponibili opzioni relative alle modalità di contatto da parte dell'organizzazione per verificare l'identità dell'utente, a seconda dell'operazione che si sta cercando di eseguire. Queste opzioni includono:

- **App di autenticazione.** Scaricare e usare un'app di autenticazione per ottenere una notifica di approvazione o un codice di approvazione generato in modo casuale per la verifica in due passaggi o la reimpostazione della password. Per istruzioni dettagliate su come configurare e usare l'app Microsoft Authenticator, vedere [Configurare le informazioni di sicurezza per l'uso di un'app di autenticazione](security-info-setup-auth-app.md).

- **Chiamata sul dispositivo mobile o sul telefono dell'ufficio.** Immettere il numero del dispositivo mobile per ricevere una chiamata per la verifica in due passaggi o la reimpostazione della password. Per istruzioni dettagliate su come verificare l'identità con un numero di telefono, vedere [Configurare le informazioni di sicurezza per l'uso di chiamate telefoniche](security-info-setup-phone-number.md).

- **Indirizzo di posta elettronica.** Immettere l'indirizzo di posta elettronica aziendale o dell'istituto di istruzione per ricevere un messaggio di posta elettronica per la reimpostazione della password. Questa opzione non è disponibile per la verifica in due passaggi. Per istruzioni dettagliate su come configurare l'indirizzo di posta elettronica, vedere [Configurare le informazioni di sicurezza per l'uso della posta elettronica](security-info-setup-email.md).
   
    >[!Note]
    >Se alcune opzioni non sono disponibili, è probabile che l'organizzazione non consenta di usare questi metodi. In questo caso, è necessario scegliere un metodo disponibile oppure contattare l'amministratore per ottenere assistenza.

- **Domande di sicurezza.** Rispondere ad alcune domande di sicurezza create dall'amministratore per l'organizzazione. Questa opzione è disponibile solo per la reimpostazione della password e non per la verifica in due passaggi. Per istruzioni dettagliate su come configurare le domande di sicurezza, vedere l'articolo [Configurare le informazioni di sicurezza per l'uso di domande di sicurezza](security-info-setup-questions.md).

## <a name="next-steps"></a>Passaggi successivi

- Se è necessario aggiornare le informazioni di sicurezza, seguire le istruzioni descritte nell'articolo [Gestire le informazioni di sicurezza](security-info-manage-settings.md).

- Se la password viene persa o dimenticata, reimpostarla dal [portale per la reimpostazione della password](https://passwordreset.microsoftonline.com/) oppure seguire i passaggi nell'articolo [Reimpostare la password aziendale o dell'istituto di istruzione](user-help-reset-password.md).

- Ottenere informazioni e suggerimenti per la risoluzione dei problemi di accesso nell'articolo [Quando non riesci ad accedere al tuo account Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
