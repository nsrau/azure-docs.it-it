---
title: Problema nell&quot;installazione dell&quot;estensione del browser per il pannello di accesso dell&quot;applicazione | Microsoft Docs
description: Informazioni su come correggere gli errori comuni riscontrabili durante l&quot;installazione dell&quot;estensione del browser per il pannello di accesso
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: asteen
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 759631bca9b29134098dba55ef07513d0ee42549
ms.contentlocale: it-it
ms.lasthandoff: 05/26/2017


---

# <a name="problem-installing-the-application-access-panel-browser-extension"></a>Problema nell'installazione dell'estensione del browser per il pannello di accesso dell'applicazione

Il pannello di accesso è un portale basato sul Web che permette a un utente che ha un account aziendale o di istituto di istruzione in Azure Active Directory (Azure AD) di visualizzare e avviare applicazioni basate sul cloud a cui l'amministratore di Azure AD ha concesso l'accesso. Un utente dotato di edizioni di Azure AD può anche usare le funzionalità di gestione self-service di gruppi e app tramite il pannello di accesso. Il pannello di accesso è separato dal portale di Azure e non richiede una sottoscrizione Azure.

Per usare il servizio Single Sign-On (SSO) basato su password nel pannello di accesso, è necessario installare l'estensione del pannello di accesso nel browser dell'utente. Questa estensione viene scaricata automaticamente quando un utente seleziona un'applicazione configurata per il servizio Single Sign-On basato su password.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Applicazione dei requisiti del browser per il pannello di accesso

Per il pannello di accesso è necessario un browser che supporti JavaScript e in cui sia abilitato CSS. Per usare il servizio Single Sign-On (SSO) basato su password nel pannello di accesso, è necessario installare l'estensione del pannello di accesso nel browser dell'utente. Questa estensione viene scaricata automaticamente quando un utente seleziona un'applicazione configurata per il servizio Single Sign-On basato su password.

Per l'accesso Single Sign-On basato su password il browser dell'utente finale può essere uno dei seguenti:

-   Internet Explorer 8, 9, 10, 11 su Windows 7 o versioni successive

-   Chrome in Windows 7 o versione successiva e MacOS X o versione successiva

-   Firefox 26.0 o versione successiva in Windows XP SP2 o versione successiva e in Mac OS X 10.6 o versione successiva

**Nota**: l'estensione per l'accesso SSO basato su password sarà disponibile per Edge in Windows 10 quando le estensioni del browser saranno supportate per Edge.

## <a name="how-to-install-the-access-panel-browser-extension"></a>Come installare l'estensione del browser per il pannello di accesso

Per installare l'estensione del browser per il pannello di accesso, seguire questa procedura:

1.  Aprire il [pannello di accesso](https://myapps.microsoft.com) in uno dei browser supportati e accedere come **utente** ad Azure AD.

2.  Fare clic su un'**applicazione con accesso SSO basato su password** nel pannello di accesso.

3.  Quando viene richiesto di installare il software, selezionare **Installa ora**.

4.  A seconda del browser in uso, si verrà indirizzati al collegamento per il download. **Aggiungere** l'estensione al browser.

5.  Se richiesto dal browser, scegliere se **abilitare** o **consentire** l'uso dell'estensione.

6.  Al termine dell'installazione, **riavviare** la sessione del browser.

7.  Accedere al pannello di accesso e verificare se è possibile **avviare** le applicazioni con accesso SSO basato su password.

È anche possibile scaricare l'estensione per Chrome e Firefox dai collegamenti diretti seguenti:

-   [Estensione Pannello di accesso per Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Estensione Pannello di accesso per Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="setting-up-a-group-policy-for-internet-explorer"></a>Impostazione dei Criteri di gruppo per Internet Explorer

È possibile impostare Criteri di gruppo che consentono di installare l'estensione del pannello di accesso per Internet Explorer nei computer degli utenti in modalità remota.

I prerequisiti includono:

-   [Servizi di dominio Active Directory](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)è già stato configurato e i computer degli utenti sono stati aggiunti al dominio.

-   Per modificare l'oggetto Criteri di gruppo è necessaria l'autorizzazione "Modifica impostazione". Questa autorizzazione è assegnata per impostazione predefinita ai membri dei gruppi di sicurezza seguenti: Domain Administrators, Enterprise Administrators e Group Policy Creator Owners. [Altre informazioni](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Per istruzioni passo passo su come configurare i Criteri di gruppo e distribuirli agli utenti, seguire l'esercitazione [Come distribuire l'estensione Pannello di accesso per Internet Explorer con Criteri di gruppo](active-directory-saas-ie-group-policy.md).

## <a name="troubleshoot-the-access-panel-in-internet-explorer"></a>Risoluzione dei problemi del pannello di accesso in Internet Explorer

Per accedere a uno strumento diagnostico e per istruzioni passo passo sulla configurazione dell'estensione per Internet Explorer, seguire la procedura nella sezione sulla [risoluzione dei problemi relativi all'estensione del pannello di accesso per Internet Explorer](active-directory-saas-ie-troubleshooting.md).

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Se questi passaggi per la risoluzione dei problemi non risolvono il problema

Aprire un ticket di supporto con le informazioni seguenti, se disponibili:

-   ID errore di correlazione

-   UPN (indirizzo di posta elettronica dell'utente)

-   ID tenant

-   Tipo di browser

-   Fuso orario e ora o intervallo di tempo durante il quale si verifica l'errore

-   Tracce Fiddler

## <a name="next-steps"></a>Passaggi successivi
[Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

