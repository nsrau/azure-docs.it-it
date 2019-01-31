---
title: Risorse per la migrazione di app ad Azure Active Directory | Microsoft Docs
description: Risorse di supporto per la migrazione dell'accesso e dell'autenticazione delle applicazioni ad Azure Active Directory (Azure AD).
services: active-directory
author: barbkess
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 09/19/2018
ms.author: barbkess
ms.reviewer: baselden
ms.openlocfilehash: 40e9a6a7a8693f710fdb41e1c6738ecc70e5a0b7
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55189545"
---
# <a name="resources-for-migrating-applications-to-azure-active-directory"></a>Risorse per la migrazione delle applicazioni ad Azure Active Directory

Risorse di supporto per la migrazione dell'accesso e dell'autenticazione delle applicazioni ad Azure Active Directory (Azure AD). Partecipare a questo breve sondaggio (https://aka.ms/AppsMigrationFeedback) per fornire commenti e suggerimenti sull'esperienza di migrazione delle app ad Azure AD, inclusi eventuali ostacoli riscontrati per la migrazione, la necessità di strumenti/istruzioni o i motivi per cui mantenere il provider di identità locale). 

| Risorsa  | DESCRIZIONE  |
|:-----------|:-------------|
|[Migrazione delle app ad Azure AD](https://aka.ms/migrateapps/whitepaper) | Questo white paper presenta i vantaggi della migrazione e descrive come pianificare la migrazione in quattro fasi ben delineate: individuazione, classificazione, migrazione e gestione continua. Verranno fornite istruzioni dettagliate per riflettere sul processo e suddividere il progetto in parti facilmente utilizzabili. Nel documento vengono forniti collegamenti a importanti risorse di supporto per l'utente durante il processo. |
|[Guida alla soluzione: migrazione delle app da Active Directory Federation Services (AD FS) ad Azure AD](https://aka.ms/migrateapps/adfssolutionguide) | Questa guida illustra in modo dettagliato le quattro fasi di pianificazione ed esecuzione del progetto di migrazione delle applicazioni descritto a un livello più generico nel white paper sulla migrazione. In questa guida si apprenderà come applicare queste fasi allo specifico scopo di spostare un'applicazione da Azure Directory Federated Services (AD FS) ad Azure AD.|
| [Strumento: script per la conformità alla migrazione di Active Directory Federation Services](https://aka.ms/migrateapps/adfstools) | Si tratta di uno script è possibile eseguire nel server Active Directory Federation Services (AD FS) locale per determinare la conformità delle app per la migrazione ad Azure AD.|
| [Piano di distribuzione: migrazione da AD FS alla sincronizzazione dell'hash delle password](https://aka.ms/ADFSTOPHSDPDownload) | Con la sincronizzazione dell'hash delle password, gli hash delle password degli utenti vengono sincronizzati da Active Directory locale ad Azure AD. In questo modo Azure AD può autenticare gli utenti senza interagire con l'istanza locale di Active Directory.| 
| [Piano di distribuzione: migrazione da AD FS all'autenticazione pass-through](https://aka.ms/ADFSTOPTADPDownload)|L'autenticazione pass-through di Azure AD consente agli utenti di accedere ad applicazioni sia locali sia basate sul cloud usando la stessa password. Questa funzionalità migliora l'esperienza degli utenti, che avranno una password in meno da ricordare. Inoltre, riduce i costi dell'help desk IT perché gli utenti, avendo una sola password da ricordare, sono meno portati a dimenticare la procedura di accesso. Quando gli utenti eseguono l'accesso tramite Azure AD, la funzionalità ne convalida direttamente le password rispetto ad Active Directory locale.|
| [Piano di distribuzione: abilitazione di Single Sign-On in un'app SaaS con Azure AD](https://aka.ms/SSODPDownload) | Single Sign-On permette di accedere a tutte le app e le risorse necessarie per le attività aziendali consentendo di eseguire l'accesso una sola volta con un singolo account utente. Dopo aver eseguito l'accesso, l'utente può ad esempio spostarsi da Microsoft Office a SalesForce e successivamente a Box senza dover effettuare nuovamente l'autenticazione, ad esempio con la digitazione di una password. 
| [Piano di distribuzione: estensione delle app ad Azure AD con il proxy dell'applicazione](https://aka.ms/AppProxyDPDownload)| Per poter accedere alle applicazioni locali dai portatili e da altri dispositivi dei dipendenti, sono sempre state necessarie reti private virtuali (VPN) o reti perimetrali. Queste soluzioni non sono solo complesse e difficili da proteggere, ma sono anche costose da configurare e gestire. Azure AD Application Proxy consente di accedere alle applicazioni locali più facilmente. |
| [Piani di distribuzione](../fundamentals/active-directory-deployment-plans.md) | Sono disponibili altri piani per la distribuzione delle funzionalità, ad esempio l'autenticazione a più fattori, l'accesso condizionale, il provisioning utenti, l'accesso SSO facile, la reimpostazione della password self-service e altro ancora. |


