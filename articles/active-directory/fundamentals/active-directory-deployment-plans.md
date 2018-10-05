---
title: Piani di distribuzione di Azure Active Directory | Microsoft Docs
description: Contiene linee guida end-to-end su come distribuire funzionalità di Azure Active Directory
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: lizross
ms.openlocfilehash: f5a148cba172b4e1e4490b52afb3b500da3a593c
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/28/2018
ms.locfileid: "47434161"
---
# <a name="azure-active-directory-deployment-plans"></a>Piani di distribuzione di Azure Active Directory
Per chi è alla ricerca di linee guida end-to-end su come distribuire alcune delle funzionalità di Azure Active Directory, i piani di distribuzione seguenti presentano in modo dettagliato il valore aziendale, alcune considerazioni sulla pianificazione, la progettazione e le procedure operative necessarie per implementare correttamente alcune delle più comuni funzionalità di Azure AD. 

I documenti contengono modelli di posta elettronica, diagrammi dell'architettura del sistema, test case comuni e altro ancora. 

Eventuali commenti e suggerimenti su questi documenti sono ben accetti. Completare questo breve [sondaggio](https://aka.ms/deploymentplanfeedback) per esprimere il proprio parere sull'utilità dei documenti. 

|Scenario |DESCRIZIONE |
|-|-|
|[Single Sign-On](https://aka.ms/SSODPDownload)|Single Sign-On permette di accedere a tutte le app e le risorse necessarie per le attività aziendali effettuando l'accesso una sola volta con un singolo account utente. Dopo aver effettuato l'accesso, è possibile passare a Microsoft Office, SalesForce, Box e così via senza dover ripetere l'autenticazione (ad esempio, digitando una password) una seconda volta.|
|[Provisioning utenti in ingresso basato sulla giornata lavorativa](https://aka.ms/WorkdayDeploymentPlan)|Il provisioning utenti in ingresso basato sulla giornata lavorativa in Active Directory crea le basi per la governance delle identità continuativa e migliora la qualità dei processi aziendali che si basano sui dati delle identità autorevoli. Con questa funzionalità è possibile gestire facilmente il ciclo di vita delle identità di dipendenti e lavoratori occasionali, configurando regole che eseguono il mapping dei processi Joiner-Mover-Leaver (ad esempio, nuova assunzione, licenziamento, trasferimento) alle azioni di provisioning IT (ad esempio, creazione, disabilitazione, eliminazione di account).|
|[Pannello di accesso](https://aka.ms/AccessPanelDPDownload)|Offre agli utenti un hub semplice per individuare tutte le applicazioni e accedervi. Consente loro di essere più produttivi grazie alle funzionalità self-service, ad esempio per richiedere l'accesso alle nuove app e ai nuovi gruppi o per gestire l'accesso a queste risorse per conto di altri.|
|[Provisioning utenti](https://aka.ms/UserProvisioningDPDownload)|Azure AD semplifica l'automazione di creazione, manutenzione e rimozione delle identità utente in applicazioni cloud (SaaS) come Dropbox, SalesForce, ServiceNow e così via.|
|[Multi-Factor Authentication](https://aka.ms/MFADPDownload)|Azure Multi-Factor Authentication (MFA) è una soluzione di verifica in due passaggi di Microsoft. Usando metodi di autenticazione approvati dell'amministratore, Azure MFA contribuisce a salvaguardare l'accesso a dati e applicazioni, rispondendo alla richiesta degli utenti di poter usare un semplice processo di accesso.|
|[Accesso condizionale](https://aka.ms/CADPDownload)|Con l'accesso condizionale è possibile implementare decisioni di controllo di accesso automatiche relative a chi può accedere alle app cloud, in base a determinate condizioni.|
|[AD FS per l'autenticazione pass-through](https://aka.ms/ADFSTOPTADPDownload)|L'autenticazione pass-through di Azure AD permette agli utenti di accedere ad applicazioni sia locali sia basate sul cloud usando le stesse password. Questa funzionalità offre agli utenti un'esperienza migliore, visto che hanno una password in meno da ricordare, e riduce i costi del supporto tecnico poiché è meno probabile che gli utenti dimentichino come eseguire l'accesso. Quando gli utenti eseguono l'accesso tramite Azure AD, la funzionalità ne convalida direttamente le password rispetto ad Active Directory locale.|
|[AD FS per la sincronizzazione dell'hash delle password](https://aka.ms/ADFSTOPHSDPDownload)|Con la sincronizzazione dell'hash delle password, gli hash delle password degli utenti vengono sincronizzati da Active Directory locale ad Azure AD, permettendo ad Azure AD di autenticare gli utenti senza alcuna interazione con Active Directory locale|
|[Seamless SSO](https://aka.ms/SeamlessSSODPDownload)|L'accesso Single Sign-On (SSO) facile di Azure Active Directory consente agli utenti di eseguire l'accesso automaticamente dai dispositivi di proprietà dell'azienda connessi alla rete aziendale. Dopo aver attivato questa funzionalità, gli utenti non devono digitare le proprie password per accedere ad Azure AD e, in genere, non devono digitare neppure i propri nomi utente. Gli utenti possono accedere facilmente alle applicazioni basate sul cloud senza usare componenti aggiuntivi in locale.|
|[Reimpostazione della password self-service](https://aka.ms/SSPRDPDownload)|La reimpostazione self-service delle password offre agli utenti la possibilità di reimpostare la propria password, senza alcun intervento da parte dell'amministratore, quando e dove necessario.|
|[Azure Active Directory Application Proxy](https://aka.ms/AppProxyDPDownload)|Oggi i dipendenti vogliono essere produttivi in qualsiasi luogo, in qualsiasi momento e da qualsiasi dispositivo. Vogliono lavorare con i propri dispositivi, che si tratti di tablet, di telefoni o di portatili, e si aspettano di poter accedere a tutte le applicazioni, sia alle app SaaS nel cloud sia alle app aziendali locali. Per fornire l'accesso alle applicazioni locali, sono sempre state necessarie reti private virtuali (VPN) o reti perimetrali . Queste soluzioni non sono solo complesse e difficili da proteggere, ma sono anche costose da configurare e gestire. C'è una soluzione migliore. - Azure Active Directory Application Proxy|

