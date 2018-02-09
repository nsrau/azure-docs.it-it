---
title: Installare l'estensione del browser per il pannello di accesso alle applicazioni | Microsoft Docs
description: Risolvere gli errori comuni riscontrabili durante l'installazione dell'estensione del browser per il pannello di accesso.
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: asteen
ms.reviewer: japere
ms.openlocfilehash: c49cfad5f362f4402be476066f0e8c0158f20d73
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2018
---
# <a name="install-the-access-panel-browser-extension"></a>Installare l'estensione del browser per il pannello di accesso

Il pannello di accesso è un portale basato sul Web. Se si ha un account aziendale o dell'istituto di istruzione in Azure Active Directory (Azure AD), è possibile usare il pannello di accesso per visualizzare e avviare le applicazioni basate sul cloud a cui un amministratore di Azure AD ha concesso l'accesso. 

Se si usano edizioni di Azure AD, tramite il pannello di accesso è anche possibile usare funzionalità di gestione self-service di gruppi e app. 

Il pannello di accesso è separato dal portale di Azure. Non è necessario avere una sottoscrizione di Azure.

## <a name="web-browser-requirements"></a>Requisiti del Web browser

Il pannello di accesso richiede almeno un browser con supporto di JavaScript e abilitazione di CSS. Per accedere alle applicazioni tramite SSO basato su password nel pannello di accesso, è necessario che nel browser sia installata l'estensione per il pannello. L'estensione viene scaricata automaticamente quando si seleziona un'applicazione configurata per SSO basato su password.

Per l'accesso SSO basato su password, è possibile usare uno qualsiasi dei browser seguenti.

- **Edge**: in Windows 10 Anniversary Edition o versioni successive. 
- **Chrome**: in Windows 7 o versioni successive e in MacOS X o versioni successive.
- **Firefox 26.0 o versioni successive**: in Windows XP SP2 o versioni successive e in Mac OS X 10.6 o versioni successive.
- **Internet Explorer 8, 9, 10 e 11**: in Windows 7 o versioni successive (supporto limitato).

## <a name="install-the-access-panel-browser-extension"></a>Installare l'estensione del browser per il pannello di accesso

Per installare l'estensione del browser per il pannello di accesso, seguire questa procedura:

1.  In uno dei browser supportati aprire il [pannello di accesso](https://myapps.microsoft.com) e quindi accedere come utente nell'account Azure AD.

2.  Selezionare un'applicazione con SSO basato su password.

3.  Quando richiesto, selezionare **Installa**.  
    Si verrà indirizzati al collegamento per il download per il browser selezionato. 
    
4.  Selezionare **Aggiungi**.

5.  Se richiesto, **abilitare** o **consentire** l'estensione.

6.  Al termine dell'installazione, riavviare il browser.

7.  Accedere al pannello di accesso e verificare se è possibile avviare le applicazioni con SSO basato su password.

È anche possibile scaricare l'estensione per Chrome ed Edge direttamente dai siti seguenti:

- [Estensione per Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)
- [Estensione per Edge](https://www.microsoft.com/store/apps/9pc9sckkzk84) 

## <a name="use-the-my-apps-secure-sign-in-extension"></a>Usare l'estensione per l'accesso sicuro alle app personali
* Se si usa un URL delle app personali diverso da `https://myapps.microsoft.com`, configurare l'URL predefinito seguendo questa procedura:
   1. *Senza* aver prima eseguito l'accesso all'estensione, fare clic con il pulsante destro del mouse sull'icona dell'estensione.
   2. Dal menu scegliere **My Apps URL** (URL app personali).
   3. Selezionare l'URL predefinito.
   4. Selezionare l'icona dell'estensione.
   5. Per accedere all'estensione, selezionare **Sign in to get started** (Accedi per iniziare).

* Per accedere direttamente a un'app dal browser, seguire questa procedura:
   1. Dopo aver installato l'estensione, accedervi selezionando **Sign in to get started** (Accedi per iniziare).
   2. Accedere all'app con l'URL di accesso.  
       L'URL di accesso è in genere l'URL dell'app che visualizza il modulo di accesso.
      L'estensione dovrebbe cambiare stato e segnalare che è disponibile una password.
   3. Per accedere, selezionare l'icona dell'estensione.

* Per avviare un'app dall'estensione, seguire questa procedura:
   1. Dopo aver installato l'estensione, accedervi selezionando **Sign in to get started** (Accedi per iniziare).
   2. Selezionare l'icona dell'estensione per aprirne il menu.
   3. Cercare un'app disponibile nel portale delle app personali.
   4. Nell'elenco dei risultati della ricerca selezionare l'app.  
       Nell'elenco di collegamenti **Recently Used** (Usate di recente) verranno visualizzate le ultime tre app usate.

> [!NOTE]
> Le opzioni precedenti sono disponibili solo per Edge, Chrome e Firefox.

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Configurare un criterio di gruppo per Internet Explorer

È possibile configurare un criterio di gruppo che consenta l'installazione remota dell'estensione per il pannello di accesso per Internet Explorer nei computer degli utenti.

Prima di configurare un criterio di gruppo, verificare quanto segue:

-   [Servizi di dominio Active Directory](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)è già stato configurato e i computer degli utenti sono stati aggiunti al dominio.

-   Per modificare l'oggetto Criteri di gruppo, sono necessarie autorizzazioni *Modifica impostazioni*. Per impostazione predefinita, questa autorizzazione viene concessa ai membri dei gruppi di sicurezza degli amministratori di dominio, degli amministratori dell'organizzazione e dei proprietari autori di criteri di gruppo.

Per istruzioni dettagliate per la configurazione del criterio di gruppo e la relativa distribuzione agli utenti, vedere l'articolo su come [distribuire l'estensione per il pannello di accesso per Internet Explorer usando criteri di gruppo](active-directory-saas-ie-group-policy.md).

## <a name="troubleshoot-the-access-panel-extension-in-internet-explorer"></a>Risolvere i problemi relativi all'estensione per il pannello di accesso in Internet Explorer

Per accedere a uno strumento di diagnostica e per informazioni sulla configurazione dell'estensione per Internet Explorer, vedere l'articolo su come [risolvere i problemi relativi all'estensione per il pannello di accesso per Internet Explorer](active-directory-saas-ie-troubleshooting.md).

> [!NOTE]
> Internet Explorer è coperto da supporto limitato e non riceve più nuovi aggiornamenti software. Il browser consigliato è Edge.

## <a name="if-the-preceding-steps-do-not-resolve-the-issue"></a>Se i passaggi precedenti non risolvono il problema

Aprire un ticket di supporto con le informazioni seguenti, se disponibili:

-   ID errore di correlazione
-   UPN (indirizzo di posta elettronica dell'utente)
-   ID tenant
-   Tipo di browser
-   Fuso orario e ora o intervallo di tempo in cui si è verificato l'errore
-   Tracce Fiddler

## <a name="next-steps"></a>Passaggi successivi
[Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)
