---
title: Problemi riscontrati con il portale App personali in Azure Active Directory | Microsoft Docs
description: "Vengono fornite le istruzioni per eseguire le attività comuni quando si lavora nell'Access Panel (Riquadro di accesso)."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: c67cd675-b567-41e1-8bc2-e06fe0b38d3b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: japere
ms.openlocfilehash: 7a7a5d04c55adc33db5ccce761efd622935acefb
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2018
---
# <a name="do-you-need-help-with-the-my-apps-portal"></a>Problemi riscontrati con il portale App personali

Questa pagina viene in genere visualizzata quando si verifica un problema durante l'uso del portale App personali. In alcuni casi il problema richiede l'intervento del supporto tecnico o dell'amministratore, ma in altri casi può essere utile vedere prima uno dei seguenti argomenti di risoluzione dei problemi.

## <a name="i-am-having-trouble-signing-into-the-my-apps-portal"></a>Si è verificato un problema durante l'accesso al portale App personali

Problemi generali da verificare:

- Verificare di accedere all'URL corretto: [https://myapps.microsoft.com](https://myapps.microsoft.com)

- Provare ad aggiungere l'URL ai siti attendibili del browser.

- Verificare che la password non sia scaduta o non sia stata dimenticata. Vedere [qui](active-directory-passwords-update-your-own-password.md) informazioni dettagliate per l'aggiornamento della password.

- Verificare che le informazioni di contatto per l'autenticazione siano aggiornate e non impediscano l'accesso. Vedere [qui](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/multi-factor-authentication-end-user) altri dettagli per l'impostazione delle informazioni di autenticazione.

- Cancellare i cookie del browser e riprovare ad accedere.

Se i problemi persistono e non consentono l'accesso, rivolgersi all'amministratore per ulteriore assistenza.


## <a name="how-do-i-update-my-password"></a>Come aggiornare la password

Se si dimentica la password, la si vuole modificare, il personale IT non l'ha mai inviata oppure se l'account è stato bloccato, vedere [Help, I forgot my Azure AD password](active-directory-passwords-update-your-own-password.md) (Password di Azure AD dimenticata).

## <a name="how-do-i-register-for-password-reset"></a>Come registrarsi per la reimpostazione della password

Come utente finale, è possibile reimpostare la password o sbloccare l'account senza la necessità di rivolgersi a un'altra persona usando la funzionalità di reimpostazione della password self-service (SSPR). Per poter usare questa funzionalità, è prima necessario registrare i metodi di autenticazione o confermare i metodi di autenticazione predefiniti inseriti dall'amministratore. Per altre informazioni, vedere [Eseguire la registrazione per la reimpostazione password self-service](active-directory-passwords-reset-register.md).


## <a name="i-am-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>Si è verificato un problema durante l'installazione dell'estensione accesso proteggere le app personali

Verificare se i requisiti del browser sono soddisfatti:

- Per il portale è necessario un browser che supporti JavaScript e in cui sia abilitato CSS. Se si usano app con accesso Single Sign-On basato su password è necessario installare anche l'estensione corrispondente. L'estensione viene scaricata automaticamente quando si avvia un'applicazione configurata per le app con accesso Single Sign-On basato su password.

- I requisiti del browser per l'estensione sono i seguenti:
    - Edge su Windows 10 Anniversary Edition o versioni successive
    - Chrome in Windows 7 o versione successiva e in MacOS X o versione successiva
    - Firefox 26.0 o versione successiva in Windows XP SP2 o versione successiva e in Mac OS X 10.6 o versione successiva
    - Internet Explorer 8, 9, 10, 11 su Windows 7 o versioni successive (supporto limitato)

È anche possibile scaricare l'estensione per Chrome ed Edge dai collegamenti diretti seguenti:

- [Estensione per Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

- [Estensione per Edge](https://www.microsoft.com/store/apps/9pc9sckkzk84)

Se dopo l'installazione si riscontrano problemi, seguire questa procedura:

- Verificare che l'estensione sia abilitata nelle impostazioni delle estensioni del browser.

- Riavviare il browser e accedere al portale App personali.

- Cancellare i cookie del browser e accedere al portale App personali.
- Seguire il [risoluzione dei problemi di estensione del Pannello di accesso per Internet Explorer](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-ie-troubleshooting) Guida per l'accesso a uno strumento di diagnostica e istruzioni dettagliate su come configurare l'estensione per Internet Explorer.

## <a name="how-do-i-use-the-my-apps-secure-sign-in-extension"></a>Come è possibile utilizzare l'estensione Accedi proteggere le app personali?
Modifica l'URL predefinito di App personali per l'estensione

Se si utilizza un altro URL App personali di https://myapps.microsoft.com quindi è necessario configurare l'URL predefinito tramite la procedura seguente:
1. Mentre non è firmato nell'estensione, **destro del mouse su** l'icona di estensione.
2. Fare clic su **selezionare un URL App personali** dal menu.
3. **Selezionare** l'URL predefinito.
4. Fare clic sull'icona di estensione.
5. Accedi all'estensione selezionando **Accedi per iniziare**.

Accedere direttamente in un'app dal browser
1. Dopo aver installato l'estensione, accedi per l'estensione selezionando **Accedi per iniziare**.
2. Passare il **sign-on URL** dell'app per accedere a, si tratta in genere l'URL dell'app che consente di visualizzare il form di accesso.
3. L'estensione deve modificare lo stato e consentono di conoscere la password è disponibile, fare clic su di **icona estensione** per accedere

Avviare un'app dall'estensione
1. Dopo aver installato l'estensione, accedi per l'estensione selezionando **Accedi per iniziare**.
2. Fare clic sull'icona per aprire il menu di estensione.
3. **Ricerca** per un'app disponibile nel portale di App personali.
4. Scegliere l'applicazione di **i risultati della ricerca** per avviarlo.
5. Le ultime tre App avviate verranno inoltre visualizzata nel **usati di recente** elenco di scelta rapida

> [!NOTE]
> Queste opzioni sono disponibili solo per Edge, Chrome, Firefox.

## <a name="how-do-i-add-a-new-app"></a>Come aggiungere una nuova app

1.  Nella pagina **App** fare clic su **Aggiungi app**.

2.  Cercare l'applicazione da aggiungere e quindi fare clic su **Aggiungi**.

**Osservazioni:**

- Si ha accesso a questa opzione solo se è stata abilitata dall'amministratore per l'account.

- Se l'applicazione richiede l'autorizzazione può essere necessario attendere l'approvazione dell'amministratore.


## <a name="how-do-i-manage-my-group-memberships"></a>Come gestire l'appartenenza ai gruppi

1. Fare clic su di **gruppi** riquadro. 
2. Per creare un gruppo, in gruppi di proprietà, fare clic su **Crea gruppo**e quindi seguire le istruzioni.
3. Per aggiungere un gruppo, in gruppi in, fare clic su **partecipa a gruppo**e quindi seguire le istruzioni.

**Osservazioni:**

- Si ha accesso a questa opzione solo se è stata abilitata dall'amministratore per l'account.

- Nei gruppi di appartenenza è possibile visualizzare i dettagli o abbandonare il gruppo.

- Nei gruppi di cui si proprietari è possibile visualizzare i dettagli, aggiungere o rimuovere membri e abbandonare il gruppo.


## <a name="next-steps"></a>Passaggi successivi

Per informazioni correlate alla risoluzione dei problemi, vedere [Problemi relativi all'uso del sito Web o dell'applicazione per dispositivi mobili del pannello di accesso dell'applicazione](active-directory-application-access-panel-content-map.md)

