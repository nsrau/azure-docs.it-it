---
title: Gestire le impostazioni di verifica in due passaggi - Azure Active Directory | Microsoft Docs
description: Gestire l'utilizzo di Azure Multi-Factor Authentication, inclusa la modifica delle informazioni di contatto e la configurazione dei dispositivi.
services: active-directory
keywords: client di multi-factor authentication, problema di autenticazione, ID di correlazione
author: eross-msft
manager: daveba
ms.reviewer: richagi
ms.assetid: d3372d9a-9ad1-4609-bdcf-2c4ca9679a3b
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 433c2d712ca4867a5ec59f86c333511070b6d507
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58665050"
---
# <a name="manage-your-settings-for-two-step-verification"></a>Gestire le impostazioni per la verifica in due passaggi
Questo articolo risponde a domande su come aggiornare le impostazioni della verifica in due passaggi o della Multi-Factor Authentication. Se si sono verificati problemi di accesso all'account, vedere le informazioni relative ai [problemi con la verifica in due passaggi](multi-factor-authentication-end-user-troubleshoot.md) che potrebbero essere utili.

## <a name="where-to-find-the-settings-page"></a>Dove trovare la pagina delle impostazioni
In base al modo in cui l'azienda ha configurato Azure Multi-Factor Authentication, le impostazioni come il numero di telefono possono essere modificate in diverse posizioni.

Se il supporto tecnico aziendale ha fornito un URL o passaggi specifici per la gestione della verifica in due passaggi, seguire tali istruzioni. In caso contrario, le istruzioni seguenti dovrebbero funzionare per tutti gli utenti. Se si esegue questa procedura ma non vengono visualizzate le stesse opzioni, significa che l'azienda o l'istituto di istruzione ha personalizzato il proprio portale. Chiedere all'amministratore il collegamento al portale di Azure Multi-Factor Authentication.

**Per visualizzare la pagina Verifica aggiuntiva di sicurezza**

- Passare a https://aka.ms/MFASetup.

    ![Verifica](./media/multi-factor-authentication-end-user-manage-settings/proofup.png)

Se facendo clic su questo collegamento non si ottiene il risultato desiderato, è possibile accedere alla pagina **Verifica aggiuntiva di sicurezza** anche tramite questa procedura:

1. Accedere a [https://myapps.microsoft.com](https://myapps.microsoft.com).  

2. Selezionare il nome dell'account in alto a destra, quindi selezionare **profilo**.

3. Selezionare **Verifica aggiuntiva di sicurezza**.  

    ![Myapps](./media/multi-factor-authentication-end-user-manage-settings/myapps1.png)

4. La pagina Verifica aggiuntiva di sicurezza viene caricata e mostra le impostazioni attive.

    ![Verifica](./media/multi-factor-authentication-end-user-manage-settings/proofup.png)

## <a name="i-want-to-change-my-phone-number-or-add-a-secondary-number"></a>Si desidera modificare il numero di telefono o aggiungere un numero secondario
È importante configurare un numero di telefono di autenticazione secondario.  Poiché il numero di telefono principale e l'app per dispositivi mobili si trovano probabilmente sullo stesso telefono, il numero di telefono secondario è il modo più rapido sarà possibile rientrare nel proprio account, se il telefono viene smarrito o rubato.

> [!NOTE]
> Se non si ha accesso al proprio numero di telefono principale e si ha bisogno di assistenza per accedere al proprio account, vedere l'articolo [Having trouble with two-step verification](multi-factor-authentication-end-user-troubleshoot.md) (Informazioni sulla verifica in due passaggi).  

**Per modificare il numero di telefono principale:**  

1. Nella pagina **Verifica aggiuntiva di sicurezza** selezionare la casella di testo con il numero di telefono corrente e modificarla con il nuovo numero di telefono.  
2. Selezionare **Salva**.  
3. Se questo è il numero usato per l'opzione di verifica preferita, è necessario verificare il nuovo numero prima di poterlo salvare.  

**Per aggiungere un numero di telefono secondario:**  

1. Nella pagina Verifica aggiuntiva di sicurezza selezionare la casella di controllo accanto a **Telefono per l'autenticazione alternativo**.  
2. Immettere il numero di telefono secondario nella casella di testo.  
3. Selezionare **Salva** e le modifiche sono terminate.  

## <a name="require-two-step-verification-again-on-a-device-youve-marked-as-trusted"></a>Richiedere di nuovo la verifica in due passaggi in un dispositivo che è stato contrassegnato come attendibile

A seconda delle impostazioni dell'organizzazione si potrebbe avere una casella di controllo del tipo "Non mostrare più la richiesta per **X** giorni" quando si esegue la verifica in due passaggi nel browser. Se si seleziona la casella di controllo e poi si perde il dispositivo o si ritiene che l'account sia stato violato, si deve ripristinare la verifica in due passaggi per tutti i dispositivi.

1. Nella pagina Verifica aggiuntiva di sicurezza selezionare **Ripristina Multi-Factor Authentication nei dispositivi precedentemente attendibili**.
2. Al successivo accesso da qualsiasi dispositivo verrà richiesto di eseguire la verifica in due passaggi.

## <a name="how-do-i-clean-up-microsoft-authenticator-from-my-old-device-and-move-to-a-new-one"></a>Come si rimuove Microsoft Authenticator dal dispositivo precedente per passare a un nuovo dispositivo?
Quando si disinstalla l'app dal dispositivo o si ripristina il dispositivo, l'attivazione sul back-end non verrà rimossa. Per altre informazioni, vedere [Microsoft Authenticator](user-help-auth-app-download-install.md).

## <a name="next-steps"></a>Passaggi successivi
* Leggere le informazioni di guida relative ai [problemi con la verifica in due passaggi](multi-factor-authentication-end-user-troubleshoot.md)
* Impostare [password di app](multi-factor-authentication-end-user-app-passwords.md) per tutte le applicazioni che non supportano la verifica in due passaggi.
