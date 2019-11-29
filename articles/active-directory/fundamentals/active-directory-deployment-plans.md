---
title: Piani di distribuzione - Azure Active Directory | Microsoft Docs
description: Linee guida end-to-end su come distribuire molte funzionalità di Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17e6708225262349d56c6e261895882e9c31677f
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74558522"
---
# <a name="azure-active-directory-deployment-plans"></a>Piani di distribuzione di Azure Active Directory
Per istruzioni end-to-end sulla distribuzione delle funzionalità di Azure Active Directory (Azure AD), Azure AD piani di distribuzione illustrano il valore aziendale, le considerazioni sulla pianificazione e le procedure operative necessarie per distribuire correttamente le funzionalità di Azure AD comuni.

Da una delle pagine del piano, utilizzare la funzionalità di stampa del browser in formato PDF per creare una versione aggiornata della documentazione offline.
## <a name="include-the-right-stakeholders"></a>Includi gli stakeholder appropriati

Quando si inizia la pianificazione della distribuzione per una nuova funzionalità, è importante includere i principali stakeholder nell'intera organizzazione. Si consiglia di identificare e documentare la persona o gli utenti che soddisfano ognuno dei seguenti ruoli e di collaborare con essi per determinarne il coinvolgimento nel progetto.  

I ruoli possono includere quanto segue 

|Ruolo |Description |
|-|-|
|Utente finale|Gruppo rappresentativo di utenti per i quali verrà implementata la funzionalità. Spesso Visualizza l'anteprima delle modifiche in un programma pilota.
|Responsabile supporto IT|Supporto tecnico IT che può fornire l'input sul supporto di questa modifica dal punto di vista del supporto tecnico.  
|Progettista identità o amministratore globale di Azure|Rappresentante del team di gestione delle identità responsabile della definizione del modo in cui questa modifica è allineata con l'infrastruttura di gestione delle identità di base dell'organizzazione.|
|Proprietario aziendale dell'applicazione |Il proprietario aziendale generale delle applicazioni interessate, che può includere la gestione dell'accesso.  Può inoltre fornire l'input sull'esperienza utente e l'utilità di questa modifica dal punto di vista dell'utente finale.
|Proprietario della sicurezza|Un rappresentante del team addetto alla sicurezza che può disconnettere il piano soddisferà i requisiti di sicurezza dell'organizzazione.|
|Compliance Manager|Persona all'interno dell'organizzazione responsabile di garantire la conformità ai requisiti aziendali, di settore o governativi.|

**I livelli di coinvolgimento possono includere:**

- Esponsible **R**per l'implementazione del piano e del risultato del progetto 

- **Un**pproval del piano e del risultato del progetto 

- **C**ontributor al piano e al risultato del progetto 

- **I**nformed del piano e del risultato del progetto


## <a name="best-practices-for-a-pilot"></a>Procedure consigliate per un progetto pilota
Un progetto pilota consente di eseguire test con un piccolo gruppo prima di attivare una funzionalità per tutti gli utenti. Assicurarsi che nell'ambito del test ogni caso di utilizzo all'interno dell'organizzazione venga testato accuratamente. È consigliabile fare riferimento a un gruppo specifico di utenti pilota prima di distribuirlo nell'intera organizzazione.

Nella prima ondata, è possibile indirizzare IT, usabilità e altri utenti appropriati che possono testare e fornire commenti e suggerimenti. Questo feedback dovrebbe essere usato per sviluppare ulteriormente le comunicazioni e le istruzioni inviate agli utenti e per fornire informazioni dettagliate sui tipi di problemi che possono essere visualizzati dal personale di supporto. 

L'ampliamento dell'implementazione a gruppi più grandi di utenti deve essere eseguito aumentando l'ambito dei gruppi di destinazione. Questa operazione può essere eseguita tramite [l'appartenenza dinamica ai gruppi](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)o aggiungendo manualmente utenti ai gruppi di destinazione.


## <a name="deploy-authentication"></a>Distribuisci autenticazione

| Funzionalità | Description|
| -| -|
| [Multi-Factor Authentication](https://aka.ms/deploymentplans/mfa)| Azure Multi-Factor Authentication (MFA) è una soluzione di verifica in due passaggi di Microsoft. Con i metodi di autenticazione approvati dall'amministratore, l'autenticazione a più fattori di Azure consente di proteggere l'accesso ai dati e alle applicazioni, soddisfacendo la richiesta di un processo di accesso semplice. |
| [Accesso condizionale](https://aka.ms/deploymentplans/ca)| Con l'accesso condizionale è possibile implementare decisioni automatiche di controllo degli accessi per gli utenti che possono accedere alle app cloud in base alle condizioni. |
| [Reimpostazione della password self-service](https://aka.ms/deploymentplans/sspr)| La reimpostazione della password self-service consente agli utenti di reimpostare le password senza l'intervento dell'amministratore, quando e dove necessario. |
| [Senza password](https://aka.ms/deploymentplans/passwordless) | Implementare l'autenticazione con password con l'app Microsoft Authenticator o le chiavi di sicurezza FIDO2 nell'organizzazione |

## <a name="deploy-application-management"></a>Distribuire la gestione delle applicazioni

| Funzionalità | Description|
| -| - |
| [Single Sign-On](https://aka.ms/deploymentplans/sso)| Single Sign-on consente agli utenti di accedere alle app e alle risorse necessarie per svolgere le attività di business durante l'accesso una sola volta. Dopo aver eseguito l'accesso, è possibile passare da Microsoft Office a SalesForce a box alle applicazioni interne senza dover immettere le credenziali una seconda volta. |
| [Pannello di accesso](https://aka.ms/deploymentplans/accesspanel)| Offre agli utenti un hub semplice per individuare tutte le applicazioni e accedervi. Consentire loro di essere più produttivi con le funzionalità self-service, ad esempio richiedere l'accesso ad app e gruppi o gestire l'accesso alle risorse per conto di altri utenti. |


## <a name="deploy-hybrid-scenarios"></a>Distribuire scenari ibridi

| Funzionalità | Description|
| -| -|
| [AD FS per la sincronizzazione dell'hash delle password](https://aka.ms/deploymentplans/adfs2phs)| Con la sincronizzazione dell'hash delle password, gli hash delle password utente vengono sincronizzati da Active Directory locali a Azure AD, consentendo Azure AD autenticare gli utenti senza alcuna interazione con la Active Directory locale |
| [AD FS per l'autenticazione pass-through](https://aka.ms/deploymentplans/adfs2pta)| Azure AD autenticazione pass-through consente agli utenti di accedere alle applicazioni locali e basate sul cloud usando le stesse password. Questa funzionalità offre agli utenti un'esperienza migliore, ovvero una password in meno da ricordare, e riduce i costi del supporto tecnico perché è meno probabile che gli utenti dimentichino come eseguire l'accesso. Quando gli utenti eseguono l'accesso tramite Azure AD, la funzionalità ne convalida direttamente le password rispetto ad Active Directory locale. |
| [Azure Active Directory Application Proxy](https://aka.ms/deploymentplans/appproxy)| Oggi i dipendenti vogliono essere produttivi in qualsiasi luogo, in qualsiasi momento e da qualsiasi dispositivo. Devono accedere alle app SaaS nel cloud e nelle app aziendali locali. Azure AD proxy di applicazione consente questo accesso affidabile senza reti private virtuali (VPN) o zone demilitarizzata (reti perimetrali) costose e complesse. |
| [Seamless SSO](../hybrid/how-to-connect-sso-quick-start.md)| L'accesso Single Sign-On (SSO) facile di Azure Active Directory consente agli utenti di eseguire l'accesso automaticamente dai dispositivi di proprietà dell'azienda connessi alla rete aziendale. Con questa funzionalità, gli utenti non dovranno digitare le password per accedere a Azure AD e, in genere, non dovranno immettere i propri nomi utente. Questa funzionalità consente agli utenti autorizzati di accedere agevolmente alle applicazioni basate sul cloud senza che siano necessari altri componenti locali. |

## <a name="deploy-user-provisioning"></a>Distribuire il provisioning utenti

| Funzionalità | Description|
| -| -|
| [Provisioning utenti](https://aka.ms/deploymentplans/userprovisioning)| Azure AD semplifica l'automazione di creazione, manutenzione e rimozione delle identità utente in applicazioni cloud (SaaS) come Dropbox, SalesForce, ServiceNow e così via. |
| [Provisioning utenti cloud HR](https://aka.ms/deploymentplans/cloudhr)| Il provisioning utenti cloud HR per Active Directory crea una base per la governance delle identità in corso e migliora la qualità dei processi aziendali basati sui dati di identità autorevoli. Usando questa funzionalità con il prodotto HR cloud, ad esempio la giornata lavorativa o SuccessFactors, è possibile gestire senza difficoltà il ciclo di vita delle identità dei dipendenti e dei lavoratori condizionali configurando le regole che mappano i processi di join-Mover-Leaver, ad esempio il nuovo noleggio, terminano Trasferimento) per le azioni di provisioning IT (ad esempio creazione, abilitazione, disabilitazione) |

## <a name="deploy-governance-and-reporting"></a>Distribuire la governance e la creazione di report

| Funzionalità | Description|
| -| -|
| [Privileged Identity Management](https://aka.ms/deploymentplans/pim)| Azure AD Privileged Identity Management (PIM) consente di gestire i ruoli amministrativi con privilegi in Azure AD, nelle risorse di Azure e in altri servizi online di Microsoft. PIM offre diverse soluzioni, come l'accesso just-in-time, i flussi di lavoro di richiesta di approvazione e le verifiche di accesso completamente integrate, per consentire di identificare, scoprire e impedire in tempo reale eventuali attività dannose di ruoli con privilegi. |
| [Creazione di report e monitoraggio](https://aka.ms/deploymentplans/reporting)| La progettazione di Azure AD soluzione di Reporting e monitoraggio dipende dai requisiti legali, di sicurezza e operativi, nonché dall'ambiente e dai processi esistenti. Questo articolo presenta le varie opzioni di progettazione e guida la strategia di distribuzione corretta. |
