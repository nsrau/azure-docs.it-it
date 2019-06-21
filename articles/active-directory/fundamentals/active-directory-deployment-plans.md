---
title: Piani di distribuzione - Azure Active Directory | Microsoft Docs
description: Linee guida end-to-end su come distribuire molte funzionalità di Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: lizross
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f59cae87af5f29d900159639798f9cd031f3d765
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67302377"
---
# <a name="azure-active-directory-deployment-plans"></a>Piani di distribuzione di Azure Active Directory
Per chi è alla ricerca di linee guida end-to-end su come distribuire alcune delle funzionalità di Azure Active Directory, i piani di distribuzione seguenti presentano in modo dettagliato il valore aziendale, alcune considerazioni sulla pianificazione, la progettazione e le procedure operative necessarie per implementare correttamente alcune delle più comuni funzionalità di Azure AD. 

I documenti contengono modelli di posta elettronica, diagrammi dell'architettura del sistema, test case comuni e altro ancora. 

Eventuali commenti e suggerimenti su questi documenti sono ben accetti. Completare questo breve [sondaggio](https://aka.ms/deploymentplanfeedback) per esprimere il proprio parere sull'utilità dei documenti. 

## <a name="include-the-right-stakeholders"></a>Includere gli stakeholder a destra

Quando si inizia la pianificazione della distribuzione per una nuova funzionalità, è importante includere principali parti interessate all'interno dell'organizzazione. Si consiglia di identificare e documentare la persona o persone che soddisfano ognuno dei seguenti ruoli e collaborare con lui per determinare il suo coinvolgimento nel progetto.  

I ruoli possono includere quanto segue 

|Ruolo |Descrizione |
|-|-|
|Per l'utente finale|Gruppo rappresentativo degli utenti per cui la funzionalità verrà implementata. Spesso Visualizza in anteprima le modifiche in un programma pilota.
|Responsabile del supporto IT|IT dell'organizzazione rappresentante del supporto in grado di fornire input riguardanti il supporto di questa modifica dal punto di vista dell'help desk.  
|Amministratore globale Identity Architect o in Azure|Rappresentante di team Identity management responsabile della definizione di come questa modifica è allineata con l'infrastruttura di gestione delle identità core all'interno dell'organizzazione.|
|Proprietario di Business dell'applicazione |Il titolare dell'organizzazione generale delle applicazioni interessate, che possono includere la gestione dell'accesso.  Può inoltre fornire input sull'esperienza utente e utilità di questa modifica dal punto di vista dell'utente finale.
|Proprietario della protezione|Un rappresentante dal team di sicurezza che possono richiedere che il piano soddisferà i requisiti di sicurezza dell'organizzazione.|
|Compliance Manager|La persona all'interno dell'organizzazione responsabile di garantire la conformità con corporate, settore o requisiti governativi.|

**I livelli di coinvolgimento possono includere:**

- **R**esponsible per l'implementazione del piano del progetto e il risultato 

- **Oggetto**pprovazione del piano del progetto e il risultato 

- **C**ontributor al piano del progetto e il risultato 

- **Ho**nformed del piano del progetto e il risultato
 
## <a name="deployment-plans"></a>Piani di distribuzione



|Scenario |Descrizione |
|-|-|
|[Autenticazione a più fattori](../authentication/howto-mfa-getstarted.md)|Azure Multi-Factor Authentication (MFA) è una soluzione di verifica in due passaggi di Microsoft. Usando metodi di autenticazione approvati dell'amministratore, Azure MFA contribuisce a salvaguardare l'accesso a dati e applicazioni, rispondendo alla richiesta degli utenti di poter usare un semplice processo di accesso.|
|[Accesso condizionale](https://aka.ms/deploymentplans/ca)|Grazie all'accesso condizionale, è possibile implementare l'accesso automatizzato decisioni relative al controllo di chi può accedere alle App cloud, in base alle condizioni.|
|[Reimpostazione della password self-service](https://aka.ms/SSPRDPDownload)|La reimpostazione self-service delle password offre agli utenti la possibilità di reimpostare la propria password, senza alcun intervento da parte dell'amministratore, quando e dove necessario.|
|[Privileged Identity Management](https://aka.ms/deploymentplans/pim)|Azure AD Privileged Identity Management (PIM) consente di gestire i ruoli amministrativi con privilegi in Azure AD, nelle risorse di Azure e in altri servizi online di Microsoft. PIM offre diverse soluzioni, come l'accesso just-in-time, i flussi di lavoro di richiesta di approvazione e le verifiche di accesso completamente integrate, per consentire di identificare, scoprire e impedire in tempo reale eventuali attività dannose di ruoli con privilegi.|
|[Single Sign-On](https://aka.ms/SSODPDownload)|Single Sign-On permette di accedere a tutte le app e le risorse necessarie per le attività aziendali effettuando l'accesso una sola volta con un singolo account utente. Dopo aver effettuato l'accesso, è possibile passare a Microsoft Office, SalesForce, Box e così via senza dover ripetere l'autenticazione (ad esempio, digitando una password) una seconda volta.|
|[Seamless SSO](https://aka.ms/SeamlessSSODPDownload)|L'accesso Single Sign-On (SSO) facile di Azure Active Directory consente agli utenti di eseguire l'accesso automaticamente dai dispositivi di proprietà dell'azienda connessi alla rete aziendale. Dopo aver attivato questa funzionalità, gli utenti non devono digitare le proprie password per accedere ad Azure AD e, in genere, non devono digitare neppure i propri nomi utente. Gli utenti possono accedere facilmente alle applicazioni basate sul cloud senza usare componenti aggiuntivi in locale.|
|[Pannello di accesso](https://aka.ms/AccessPanelDPDownload)|Offre agli utenti un hub semplice per individuare tutte le applicazioni e accedervi. Consente loro di essere più produttivi grazie alle funzionalità self-service, ad esempio per richiedere l'accesso alle nuove app e ai nuovi gruppi o per gestire l'accesso a queste risorse per conto di altri.|
|[AD FS per la sincronizzazione dell'hash delle password](https://aka.ms/deploymentplans/adfs2phs)|Con la sincronizzazione dell'hash delle password, gli hash delle password degli utenti vengono sincronizzati da Active Directory locale ad Azure AD, permettendo ad Azure AD di autenticare gli utenti senza alcuna interazione con Active Directory locale|
|[AD FS per l'autenticazione pass-through](https://aka.ms/deploymentplans/adfs2pta)|L'autenticazione pass-through di Azure AD permette agli utenti di accedere ad applicazioni sia locali sia basate sul cloud usando le stesse password. Questa funzionalità offre agli utenti un'esperienza migliore, visto che hanno una password in meno da ricordare, e riduce i costi del supporto tecnico poiché è meno probabile che gli utenti dimentichino come eseguire l'accesso. Quando gli utenti eseguono l'accesso tramite Azure AD, la funzionalità ne convalida direttamente le password rispetto ad Active Directory locale.|
|[Azure Active Directory Application Proxy](https://aka.ms/deploymentplans/appproxy)|Oggi i dipendenti vogliono essere produttivi in qualsiasi luogo, in qualsiasi momento e da qualsiasi dispositivo. Vogliono lavorare con i propri dispositivi, che si tratti di tablet, di telefoni o di portatili, e si aspettano di poter accedere a tutte le applicazioni, sia alle app SaaS nel cloud sia alle app aziendali locali. Per fornire l'accesso alle applicazioni locali, sono sempre state necessarie reti private virtuali (VPN) o reti perimetrali . Queste soluzioni non sono solo complesse e difficili da proteggere, ma sono anche costose da configurare e gestire. C'è una soluzione migliore. - Azure Active Directory Application Proxy|
|[Provisioning utenti](https://aka.ms/UserProvisioningDPDownload)|Azure AD semplifica l'automazione di creazione, manutenzione e rimozione delle identità utente in applicazioni cloud (SaaS) come Dropbox, SalesForce, ServiceNow e così via.|
|[Provisioning utenti in ingresso basato sulla giornata lavorativa](https://aka.ms/WorkdayDeploymentPlan)|Il provisioning utenti in ingresso basato sulla giornata lavorativa in Active Directory crea le basi per la governance delle identità continuativa e migliora la qualità dei processi aziendali che si basano sui dati delle identità autorevoli. Con questa funzionalità è possibile gestire facilmente il ciclo di vita delle identità di dipendenti e lavoratori occasionali, configurando regole che eseguono il mapping dei processi Joiner-Mover-Leaver (ad esempio, nuova assunzione, licenziamento, trasferimento) alle azioni di provisioning IT (ad esempio, creazione, disabilitazione, eliminazione di account).|
