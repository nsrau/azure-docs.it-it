---
title: "Configurare le informazioni di sicurezza per l'uso di domande di sicurezza: Azure Active Directory | Microsoft Docs"
description: Configurare le informazioni di sicurezza per verificare l'identità tramite le domande di sicurezza predefinite.
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
ms.openlocfilehash: b4913d55ee3d254f197512d9251ae750d8896f53
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/07/2018
ms.locfileid: "44160439"
---
# <a name="set-up-security-info-to-use-pre-defined-security-questions-preview"></a>Configurare le informazioni di sicurezza per l'uso di domande di sicurezza predefinite (anteprima)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

La configurazione delle informazioni di sicurezza richiede l'accesso all'account aziendale o dell'istituto di istruzione e il completamento del processo di registrazione. Se le informazioni di sicurezza non sono mai state configurate, verrà chiesto di farlo.

## <a name="set-up-security-questions"></a>Configurare le domande di sicurezza

A seconda delle impostazioni dell'organizzazione, quando si effettua l'accesso è possibile che venga richiesto di aggiungere domande di sicurezza alle informazioni sulla sicurezza. In caso contrario, per iniziare a configurare le domande di sicurezza nelle informazioni sulla sicurezza, seguire i passaggi riportati in [Gestire le informazioni di sicurezza](security-info-manage-settings.md).

Se vengono usate le domande di sicurezza, è consigliabile combinare questo metodo con un altro. Le domande di sicurezza possono risultare meno sicure rispetto ad altri metodi perché alcuni utenti potrebbero conoscere le risposte alle domande di un altro utente.

>[!Note]
>Le domande di sicurezza vengono archiviate privatamente e in modo sicuro in un oggetto utente nella directory e gli utenti possono rispondere a tali domande solo durante la registrazione. All'amministratore non è consentito leggere o modificare le domande e le risposte di un utente.<br>Se l'opzione relativa alle domande di sicurezza non viene visualizzata, è possibile che l'organizzazione non consenta di usare le domande di sicurezza per la verifica. In questo caso, è necessario scegliere un altro metodo oppure contattare l'amministratore per ottenere assistenza.

### <a name="to-choose-and-answer-your-security-questions"></a>Per scegliere e rispondere alle domande di sicurezza

1. Selezionare **Domande di sicurezza**, quindi scegliere le domande di sicurezza alle quali si intende rispondere. 

    Il numero di domande di sicurezza che è necessario scegliere è stabilito dall'amministratore.

    ![Pagina delle informazioni di sicurezza, selezionare le domande di sicurezza](media/security-info/security-info-keep-secure-setup-pick-questions.png)

2. Fornire risposte per le domande selezionate, quindi selezionare **Fatto**.

## <a name="additional-security-info-options"></a>Opzioni aggiuntive per le informazioni di sicurezza

Sono disponibili opzioni relative alle modalità di contatto da parte dell'organizzazione per verificare l'identità dell'utente, a seconda dell'operazione che si sta cercando di eseguire. Queste opzioni includono:

- **App di autenticazione.** Scaricare e usare un'app di autenticazione per ottenere una notifica di approvazione o un codice di approvazione generato in modo casuale per la verifica in due passaggi o la reimpostazione della password. Per istruzioni dettagliate su come configurare e usare l'app Microsoft Authenticator, vedere [Configurare le informazioni di sicurezza per l'uso di un'app di autenticazione](security-info-setup-auth-app.md).

- **Mobile device text.** (SMS su dispositivo mobile) Immettere il numero del dispositivo mobile per ricevere via SMS un codice da usare per la verifica in due passaggi o la reimpostazione della password. Per istruzioni dettagliate su come verificare l'identità con un SMS, vedere [Configurare le informazioni di sicurezza per l'invio di SMS](security-info-setup-text-msg.md).

- **Chiamata sul dispositivo mobile o sul telefono dell'ufficio.** Immettere il numero del dispositivo mobile per ricevere una chiamata per la verifica in due passaggi o la reimpostazione della password. Per istruzioni dettagliate su come verificare l'identità con un numero di telefono, vedere [Configurare le informazioni di sicurezza per l'uso di chiamate telefoniche](security-info-setup-phone-number.md).

- **Indirizzo di posta elettronica.** Immettere l'indirizzo di posta elettronica aziendale o dell'istituto di istruzione per ricevere un messaggio di posta elettronica per la reimpostazione della password. Questa opzione non è disponibile per la verifica in due passaggi. Per istruzioni dettagliate su come configurare l'indirizzo di posta elettronica, vedere [Configurare le informazioni di sicurezza per l'uso della posta elettronica](security-info-setup-email.md).
   
    >[!Note]
    >Se alcune opzioni non sono disponibili, è probabile che l'organizzazione non consenta di usare questi metodi. In questo caso, è necessario scegliere un metodo disponibile oppure contattare l'amministratore per ottenere assistenza.

## <a name="next-steps"></a>Passaggi successivi

- Se è necessario aggiornare le informazioni di sicurezza, seguire le istruzioni descritte nell'articolo [Gestire le informazioni di sicurezza](security-info-manage-settings.md).

- Se la password viene persa o dimenticata, reimpostarla dal [portale per la reimpostazione della password](https://passwordreset.microsoftonline.com/) oppure seguire i passaggi nell'articolo [Reimpostare la password aziendale o dell'istituto di istruzione](user-help-reset-password.md).

- Ottenere informazioni e suggerimenti per la risoluzione dei problemi di accesso nell'articolo [Quando non riesci ad accedere al tuo account Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).