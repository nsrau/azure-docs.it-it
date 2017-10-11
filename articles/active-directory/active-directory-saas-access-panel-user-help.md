---
title: Problemi riscontrati con il portale App personali in Azure Active Directory | Microsoft Docs
description: "Vengono fornite le istruzioni per eseguire le attività comuni quando si lavora nell'Access Panel (Riquadro di accesso)."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: c67cd675-b567-41e1-8bc2-e06fe0b38d3b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/15/2017
ms.author: markvi
ms.reviewer: japere
ms.openlocfilehash: 613c68d5c13793a3b696b6afbfc0e1a31595e201
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2017
---
# <a name="do-you-need-help-with-the-my-apps-portal"></a>Problemi riscontrati con il portale App personali

Questa pagina viene in genere visualizzata quando si verifica un problema durante l'uso del portale App personali. In alcuni casi il problema richiede l'intervento del supporto tecnico o dell'amministratore, ma in altri casi può essere utile vedere prima uno dei seguenti argomenti di risoluzione dei problemi.

## <a name="i-am-having-trouble-signing-into-the-my-apps-portal"></a>Si è verificato un problema durante l'accesso al portale App personali

Problemi generali da verificare:

- Verificare di accedere all'URL corretto: [https://myapps.microsoft.com](https://myapps.microsoft.com)

- Provare ad aggiungere l'URL ai siti attendibili del browser.

- Verificare che la password non sia scaduta o non sia stata dimenticata. Vedere [qui](active-directory-passwords-update-your-own-password.md) informazioni dettagliate per l'aggiornamento della password.

- Verificare che le informazioni di contatto per l'autenticazione siano aggiornate e non impediscano l'accesso. Vedere [qui](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/end-user/multi-factor-authentication-end-user) altri dettagli per l'impostazione delle informazioni di autenticazione.

- Cancellare i cookie del browser e riprovare ad accedere.

Se i problemi persistono e non consentono l'accesso, rivolgersi all'amministratore per ulteriore assistenza.


## <a name="how-do-i-update-my-password"></a>Come aggiornare la password

Se si dimentica la password, la si vuole modificare, il personale IT non l'ha mai inviata oppure se l'account è stato bloccato, vedere [Help, I forgot my Azure AD password](active-directory-passwords-update-your-own-password.md) (Password di Azure AD dimenticata).

## <a name="how-do-i-register-for-password-reset"></a>Come registrarsi per la reimpostazione della password

Come utente finale, è possibile reimpostare la password o sbloccare l'account senza la necessità di rivolgersi a un'altra persona usando la funzionalità di reimpostazione della password self-service (SSPR). Per poter usare questa funzionalità, è prima necessario registrare i metodi di autenticazione o confermare i metodi di autenticazione predefiniti inseriti dall'amministratore. Per altre informazioni, vedere [Eseguire la registrazione per la reimpostazione password self-service](active-directory-passwords-reset-register.md).


## <a name="i-am-having-trouble-installing-the-my-apps-portal-browser-extension"></a>Si riscontrano problemi con l'installazione dell'estensione browser per il portale App personali

Verificare se i requisiti del browser sono soddisfatti:

- Per il portale è necessario un browser che supporti JavaScript e in cui sia abilitato CSS. Se si usano app con accesso Single Sign-On basato su password è necessario installare anche l'estensione corrispondente. L'estensione viene scaricata automaticamente quando si avvia un'applicazione configurata per le app con accesso Single Sign-On basato su password.

- I requisiti del browser per l'estensione sono i seguenti:
    - Internet Explorer 8, 9, 10, 11 su Windows 7 o versioni successive
    - Edge su Windows 10 Anniversary Edition o versioni successive
    - Chrome in Windows 7 o versione successiva e in MacOS X o versione successiva
    - Firefox 26.0 o versione successiva in Windows XP SP2 o versione successiva e in Mac OS X 10.6 o versione successiva

È anche possibile scaricare l'estensione per Chrome ed Edge dai collegamenti diretti seguenti:

- [Estensione per Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

- [Estensione per Edge](https://www.microsoft.com/store/apps/9pc9sckkzk84)

Se dopo l'installazione si riscontrano problemi, seguire questa procedura:

- Verificare che l'estensione sia abilitata nelle impostazioni delle estensioni del browser.

- Riavviare il browser e accedere al portale App personali.

- Cancellare i cookie del browser e accedere al portale App personali.

## <a name="how-do-i-add-a-new-app"></a>Come aggiungere una nuova app

1.  Nella pagina **App** fare clic su **Aggiungi app**.

2.  Cercare l'applicazione da aggiungere e quindi fare clic su **Aggiungi**.

**Osservazioni:**

- Si ha accesso a questa opzione solo se è stata abilitata dall'amministratore per l'account.

- Se l'applicazione richiede l'autorizzazione può essere necessario attendere l'approvazione dell'amministratore.


## <a name="how-do-i-manage-my-group-memberships"></a>Come gestire l'appartenenza ai gruppi

1. Fare clic nel riquadro delle app Gruppi. 
2. Per creare un gruppo, in Gruppi di cui si è proprietari fare clic su Crea gruppo, quindi seguire le istruzioni.
3. Per partecipare a un gruppo, in Gruppi a cui si appartiene fare clic su Partecipa a gruppo, quindi seguire le istruzioni.

**Osservazioni:**

- Si ha accesso a questa opzione solo se è stata abilitata dall'amministratore per l'account.

- Nei gruppi di appartenenza è possibile visualizzare i dettagli o abbandonare il gruppo.

- Nei gruppi di cui si proprietari è possibile visualizzare i dettagli, aggiungere o rimuovere membri e abbandonare il gruppo.


## <a name="next-steps"></a>Passaggi successivi

Per informazioni correlate alla risoluzione dei problemi, vedere [Problemi relativi all'uso del sito Web o dell'applicazione per dispositivi mobili del pannello di accesso dell'applicazione](active-directory-application-access-panel-content-map.md)

