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
ms.openlocfilehash: d7a596454a48a1d6fcee77634363dd38f34a4d58
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81603360"
---
# <a name="azure-active-directory-deployment-plans"></a>Piani di distribuzione di Azure Active Directory
Per informazioni generali sulla distribuzione delle funzionalità di Azure Active Directory (Azure AD)? I piani di distribuzione di Azure AD illustrano il valore aziendale, le considerazioni sulla pianificazione e le procedure operative necessarie per distribuire correttamente le funzionalità comuni di Azure AD.

Da una qualsiasi delle pagine del piano, utilizzare la funzionalità Stampa su PDF del browser per creare una versione offline aggiornata della documentazione.
## <a name="include-the-right-stakeholders"></a>Includi le parti interessate giuste

Quando si inizia la pianificazione della distribuzione per una nuova funzionalità, è importante includere le principali parti interessate all'interno dell'organizzazione. Ti consigliamo di identificare e documentare la persona o le persone che svolgono ciascuno dei seguenti ruoli e di collaborare con loro per determinare il loro coinvolgimento nel progetto.  

I ruoli possono includere i seguenti 

|Ruolo |Descrizione |
|-|-|
|Degli utenti finali|Gruppo rappresentativo di utenti per i quali verrà implementata la funzionalità. Spesso visualizza in anteprima le modifiche in un programma pilota.
|Responsabile del supporto IT|Rappresentante dell'organizzazione del supporto IT che può fornire input sulla supportabilità di questa modifica dal punto di vista dell'helpdesk.  
|Architetto dell'identità o amministratore globale di AzureIdentity Architect or Azure Global Administrator|Rappresentante del team di gestione delle identità incaricato di definire il modo in cui questa modifica è allineata con l'infrastruttura di gestione delle identità di base nell'organizzazione.|
|Proprietario dell'attività dell'applicazione |Il proprietario aziendale generale delle applicazioni interessate, che può includere la gestione dell'accesso.Può anche fornire input sull'esperienza utente e l'utilità di questa modifica dal punto di vista dell'utente finale.
|Proprietario della sicurezza|Un rappresentante del team di sicurezza che può firmare che il piano soddisferà i requisiti di sicurezza dell'organizzazione.|
|Compliance Manager|La persona all'interno dell'organizzazione responsabile di garantire la conformità ai requisiti aziendali, del settore o governativi.|

**I livelli di coinvolgimento possono includere:**

- **R**ammissibile per l'attuazione del piano e dei risultati del progetto 

- **Un**pòdicillo del piano e del risultato del progetto 

- **C**ontributor al piano di progetto e risultato 

- **Ho**nformato di piano di progetto e risultato


## <a name="best-practices-for-a-pilot"></a>Procedure consigliate per un progetto pilota
Un pilota consente di testare con un piccolo gruppo prima di attivare una capacità per tutti. Assicurarsi che, come parte dei test, ogni caso d'uso all'interno dell'organizzazione venga accuratamente testato. È consigliabile scegliere come destinazione un gruppo specifico di utenti pilota prima di distribuirlo all'intera organizzazione.

Nella prima ondata, indirizzare l'IT, l'usabilità e altri utenti appropriati che possono testare e fornire feedback. Questo feedback deve essere utilizzato per sviluppare ulteriormente le comunicazioni e le istruzioni inviate agli utenti e per fornire informazioni dettagliate sui tipi di problemi che il personale di supporto potrebbe vedere. 

L'ampliamento dell'implementazione a gruppi di utenti più ampi dovrebbe essere effettuato aumentando l'ambito dei gruppi interessati. Questa operazione può essere eseguita tramite [l'appartenenza dinamica al gruppo](../users-groups-roles/groups-dynamic-membership.md)o aggiungendo manualmente gli utenti ai gruppi di destinazione.


## <a name="deploy-authentication"></a>Distribuire l'autenticazioneDeploy authentication

| Funzionalità | Descrizione|
| -| -|
| [Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)| Azure Multi-Factor Authentication (MFA) è una soluzione di verifica in due passaggi di Microsoft. Usando i metodi di autenticazione approvati dall'amministratore, Azure MFA consente di proteggere l'accesso ai dati e alle applicazioni soddisfacendo al contempo la richiesta di un semplice processo di accesso. |
| [Accesso condizionale](../conditional-access/plan-conditional-access.md)| Con l'accesso condizionale, è possibile implementare decisioni di controllo di accesso automatizzate per chi può accedere alle app cloud, in base alle condizioni. |
| [Reimpostazione della password self-service](../authentication/howto-sspr-deployment.md)| La reimpostazione della password self-service consente agli utenti di reimpostare le password senza l'intervento dell'amministratore, quando e dove necessario. |
| [Accesso senza password](../authentication/howto-authentication-passwordless-deployment.md) | Implementare l'autenticazione senza password utilizzando l'app Microsoft Authenticator o le chiavi di protezione FIDO2 nell'organizzazione |

## <a name="deploy-application-management"></a>Distribuire la gestione delle applicazioni

| Funzionalità | Descrizione|
| -| - |
| [Single Sign-On](../manage-apps/plan-sso-deployment.md)| Single Sign-On consente agli utenti di accedere alle app e alle risorse necessarie per svolgere attività commerciali durante l'accesso una sola volta. Dopo aver effettuato l'accesso, possono passare da Microsoft Office a SalesForce a Box alle applicazioni interne senza dover immettere le credenziali una seconda volta. |
| [Pannello di accesso](../manage-apps/access-panel-deployment-plan.md)| Offre agli utenti un hub semplice per individuare tutte le applicazioni e accedervi. Consenti loro di essere più produttivi con funzionalità self-service, come la richiesta di accesso ad app e gruppi o la gestione dell'accesso alle risorse per conto di altri. |


## <a name="deploy-hybrid-scenarios"></a>Distribuire scenari ibridiDeploy hybrid scenarios

| Funzionalità | Descrizione|
| -| -|
| [AD FS per la sincronizzazione dell'hash delle password](../hybrid/plan-migrate-adfs-password-hash-sync.md)| Con la sincronizzazione dell'hash delle password, gli hash delle password utente vengono sincronizzati da Active Directory locale ad Azure AD, consentendo ad Azure AD di autenticare gli utenti senza alcuna interazione con Active Directory locale |
| [AD FS per l'autenticazione pass-through](../hybrid/plan-migrate-adfs-pass-through-authentication.md)| L'autenticazione pass-through di Azure AD consente agli utenti di accedere alle applicazioni locali e basate su cloud usando le stesse password. Questa funzionalità offre agli utenti un'esperienza migliore, ovvero una password in meno da ricordare, e riduce i costi dell'helpdesk IT perché è meno probabile che gli utenti dimentichino come accedere. Quando gli utenti eseguono l'accesso tramite Azure AD, la funzionalità ne convalida direttamente le password rispetto ad Active Directory locale. |
| [Proxy dell'applicazione di Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-deployment-plan) |Oggi i dipendenti vogliono essere produttivi in qualsiasi luogo, in qualsiasi momento e da qualsiasi dispositivo. Devono accedere alle app SaaS nel cloud e alle app aziendali in locale. Il proxy di applicazione di Azure AD consente questo accesso affidabile senza reti private virtuali (VPN) costose e complesse o zone demilitarizzate (DM). |
| [Seamless SSO](../hybrid/how-to-connect-sso-quick-start.md)| L'accesso Single Sign-On (SSO) facile di Azure Active Directory consente agli utenti di eseguire l'accesso automaticamente dai dispositivi di proprietà dell'azienda connessi alla rete aziendale. Con questa funzionalità, gli utenti non dovranno digitare le password per accedere ad Azure AD e in genere non dovranno immettere i nomi utente. Questa funzionalità consente agli utenti autorizzati di accedere facilmente alle applicazioni basate su cloud senza la necessità di componenti locali aggiuntivi. |

## <a name="deploy-user-provisioning"></a>Distribuire il provisioning degli utentiDeploy user provisioning

| Funzionalità | Descrizione|
| -| -|
| [Provisioning utenti](../app-provisioning/plan-auto-user-provisioning.md)| Azure AD semplifica l'automazione di creazione, manutenzione e rimozione delle identità utente in applicazioni cloud (SaaS) come Dropbox, SalesForce, ServiceNow e così via. |
| [Provisioning degli utenti cloud HR](../app-provisioning/plan-cloud-hr-provision.md)| Il provisioning degli utenti delle risorse umane cloud in Active Directory crea una base per una governance delle identità continua e migliora la qualità dei processi aziendali che si basano su dati di identità autorevoli. Utilizzando questa funzionalità con il prodotto HR cloud, ad esempio Workday o Successfactors, è possibile gestire senza problemi il ciclo di vita delle identità di dipendenti e dipendenti contingenti configurando regole che eseguono il mapping di processi Joiner-Mover-Leaver (ad esempio Nuova assunzione, Termina, Trasferisci) alle azioni di provisioning IT (ad esempio Crea, Abilita, Disabilita) |

## <a name="deploy-governance-and-reporting"></a>Distribuire governance e creazione di report

| Funzionalità | Descrizione|
| -| -|
| [Privileged Identity Management](../privileged-identity-management/pim-deployment-plan.md)| Azure AD Privileged Identity Management (PIM) consente di gestire i ruoli amministrativi con privilegi in Azure AD, nelle risorse di Azure e in altri servizi online di Microsoft. PIM offre diverse soluzioni, come l'accesso just-in-time, i flussi di lavoro di richiesta di approvazione e le verifiche di accesso completamente integrate, per consentire di identificare, scoprire e impedire in tempo reale eventuali attività dannose di ruoli con privilegi. |
| [Creazione di report e monitoraggio](../reports-monitoring/plan-monitoring-and-reporting.md)| La progettazione della soluzione di report e monitoraggio di Azure AD dipende dai requisiti legali, di sicurezza e operativi, nonché dall'ambiente e dai processi esistenti. In questo articolo vengono presentate le varie opzioni di progettazione e viene presentata la strategia di distribuzione corretta. |
