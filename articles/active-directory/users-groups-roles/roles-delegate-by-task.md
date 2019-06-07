---
title: Delegare ruoli con privilegi minimi dall'attività di amministratore - Azure Active Directory | Microsoft Docs
description: Ruoli da delegare per attività di identità in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 05/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3f21f552add551ac2434618b184eb18c53ad5be
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2019
ms.locfileid: "66752227"
---
# <a name="administrator-roles-by-admin-task-in-azure-active-directory"></a>Ruoli di amministratore dall'attività di amministratore in Azure Active Directory

In questo articolo, è possibile trovare le informazioni necessarie per limitare le autorizzazioni di amministratore dell'utente tramite l'assegnazione di ruoli con privilegi minimi in Azure Active Directory (Azure AD). Sono disponibili le attività di amministrazione organizzate in aree di funzionalità e il ruolo meno privilegiato richiesto per eseguire ogni attività, insieme ai ruoli aggiuntivi di amministratore non globale che può eseguire l'attività.

## <a name="application-proxy"></a>Proxy dell'applicazione

Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi
---- | --------------------- | ----------------
Configurare l'app del proxy dell'applicazione | Amministratore di applicazioni | 
Configurare le proprietà del gruppo connettore | Amministratore di applicazioni | 
Creare una registrazione dell'applicazione quando la capacità è disabilitata per tutti gli utenti | Sviluppatore di applicazioni | Amministratore applicazione cloud, amministratore dell'applicazione
Creare gruppo di connettori | Amministratore di applicazioni | 
Eliminare gruppo di connettori | Amministratore di applicazioni | 
Disabilitare il proxy di applicazione | Amministratore di applicazioni | 
Scaricare servizio connettore | Amministratore di applicazioni | 
Leggere tutta la configurazione | Amministratore di applicazioni | 

## <a name="b2c"></a>B2C

Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi
---- | --------------------- | ----------------
Creare directory di Azure AD B2C | Tutti gli utenti non guest ([vedere la documentazione](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Creare applicazioni B2C | Amministratore globale | 
Creare applicazioni aziendali | Amministratore applicazione cloud | Amministratore di applicazioni
Creare, leggere, aggiornare ed eliminare criteri B2C | Amministratore globale | 
Creare, leggere, aggiornare ed eliminare provider di identità | Amministratore globale | 
Creare, leggere, aggiornare ed eliminare flussi utente con ripristino delle password | Amministratore globale | 
Creare, leggere, aggiornare ed eliminare flussi utente con modifica dei profili | Amministratore globale | 
Creare, leggere, aggiornare ed eliminare flussi utente con accesso | Amministratore globale | 
Creare, leggere, aggiornare ed eliminare flussi utente con iscrizione |Amministratore globale | 
Creare, leggere, aggiornare ed eliminare attributi utente | Amministratore globale | 
Creare, leggere, aggiornare ed eliminare utenti | Amministratore globale ([vedere la documentazione](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-faqs))
Leggere tutta la configurazione | Amministratore globale | 
Log di controllo di B2C di lettura | Amministratore globale ([vedere la documentazione](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-faqs)) | 

> [!NOTE]
> Gli amministratori globali di Azure AD B2C non si dispongono delle stesse autorizzazioni degli amministratori globali di Azure AD. Se si dispone di privilegi di amministratore globale di Azure AD B2C, assicurarsi che si trova in una directory di Azure AD B2C e non una directory di Azure AD.

## <a name="company-branding"></a>Informazioni personalizzate distintive dell'azienda

Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi
---- | --------------------- | ----------------
Configurare la personalizzazione aziendale | Amministratore globale | 
Leggere tutta la configurazione | Ruoli con autorizzazioni di lettura nella directory | Ruolo utente predefinito ([vedere la documentazione](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))

## <a name="company-properties"></a>Proprietà della società

Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi
---- | --------------------- | ----------------
Configurare le proprietà aziendali | Amministratore globale | 

## <a name="connect"></a>Connettere

Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi
---- | --------------------- | ----------------
Autenticazione pass-through | Amministratore globale | 
Leggere tutta la configurazione | Amministratore globale | 
Accesso Single Sign-On facile (Seamless SSO) | Amministratore globale | 

## <a name="connect-health"></a>Connect Health

Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi
---- | --------------------- | ----------------
Aggiungere o eliminare i servizi | Proprietario ([vedere la documentazione](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations)) | 
Applicare le correzioni agli errori di sincronizzazione | Collaboratore ([vedere la documentazione](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Proprietario
Configurare le notifiche | Collaboratore ([vedere la documentazione](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Proprietario
Configurare le impostazioni | Proprietario ([vedere la documentazione](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations)) | 
Configurare le notifiche di sincronizzazione | Collaboratore ([vedere la documentazione](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Proprietario
Leggere report sulla sicurezza del file system distribuito di Azure (ADFS) | Ruolo con autorizzazioni di lettura per la sicurezza | Collaboratore, proprietario
Leggere tutta la configurazione | Lettore ([vedere la documentazione](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Collaboratore, proprietario
Leggere errori di sincronizzazione | Lettore ([vedere la documentazione](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Collaboratore, proprietario
Leggere servizi di sincronizzazione | Lettore ([vedere la documentazione](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Collaboratore, proprietario
Visualizzare metriche e avvisi | Lettore ([vedere la documentazione](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Collaboratore, proprietario
Visualizzare metriche e avvisi | Lettore ([vedere la documentazione](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Collaboratore, proprietario
Visualizzare le metriche del servizio di sincronizzazione e gli avvisi | Lettore ([vedere la documentazione](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Collaboratore, proprietario


## <a name="custom-domain-names"></a>Nomi di dominio personalizzati

Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi
---- | --------------------- | ----------------
Gestire domini | Amministratore globale | 
Leggere tutta la configurazione | Ruoli con autorizzazioni di lettura nella directory | Ruolo utente predefinito ([vedere la documentazione](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))

## <a name="domain-services"></a>Servizi di dominio

Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi
---- | --------------------- | ----------------
Creare un'istanza di Azure AD Domain Services | Amministratore globale | 
Eseguire tutte le attività di servizi di dominio Azure Active Directory | Gruppo di amministratori Azure AD DC ([vedere la documentazione](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-administer-domain#administrative-tasks-you-can-perform-on-a-managed-domain)) | 
Leggere tutta la configurazione | Lettore nella sottoscrizione di Azure che contiene il servizio Active Directory Domain Services | 

## <a name="devices"></a>Dispositivi

Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi
---- | --------------------- | ----------------
Disabilitare dispositivo | Amministratore dispositivo cloud | 
Abilitare dispositivo | Amministratore dispositivo cloud | 
Leggere configurazione di base | Ruolo utente predefinito ([vedere la documentazione](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Leggere le chiavi BitLocker | Ruolo con autorizzazioni di lettura per la sicurezza | Amministratore password/amministratore sicurezza

## <a name="enterprise-applications"></a>Applicazioni aziendali

Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi
---- | --------------------- | ----------------
Fornire il consenso per eventuali autorizzazioni delegate | Amministratore di applicazioni cloud | Amministratore di applicazioni
Fornire il consenso alle autorizzazioni dell'applicazione senza includere Microsoft Graph o Azure AD Graph | Amministratore di applicazioni cloud | Amministratore di applicazioni
Fornire il consenso alle autorizzazioni dell'applicazione a Microsoft Graph o Azure AD Graph | Amministratore globale | 
Fornire il consenso alle applicazioni che accedono ai propri dati | Ruolo utente predefinito ([vedere la documentazione](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Creare applicazioni aziendali | Amministratore di applicazioni cloud | Amministratore di applicazioni
Gestire proxy di applicazione | Amministratore di applicazioni | 
Gestire le impostazioni dell'utente | Amministratore globale | 
Leggere la verifica di accesso di un gruppo o di un'applicazione | Ruolo con autorizzazioni di lettura per la sicurezza | Amministratore sicurezza/amministratore utente
Leggere tutta la configurazione | Ruolo utente predefinito ([vedere la documentazione](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Aggiornare le assegnazioni di applicazioni aziendali | Proprietario dell'applicazione aziendale ([vedere la documentazione](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Amministratore applicazione cloud, amministratore dell'applicazione
Aggiornare i proprietari di applicazioni aziendali | Proprietario dell'applicazione aziendale ([vedere la documentazione](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Amministratore applicazione cloud, amministratore dell'applicazione
Aggiornare le proprietà di applicazioni aziendali | Proprietario dell'applicazione aziendale ([vedere la documentazione](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Amministratore applicazione cloud, amministratore dell'applicazione
Aggiornare il provisioning di applicazioni aziendali | Proprietario dell'applicazione aziendale ([vedere la documentazione](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Amministratore applicazione cloud, amministratore dell'applicazione
Aggiornare il self-service di applicazioni aziendali | Proprietario dell'applicazione aziendale ([vedere la documentazione](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Amministratore applicazione cloud, amministratore dell'applicazione
Aggiornare le proprietà del servizio Single Sign-On | Proprietario dell'applicazione aziendale ([vedere la documentazione](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Amministratore applicazione cloud, amministratore dell'applicazione

## <a name="groups"></a>Gruppi

Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi
---- | --------------------- | ----------------
Assegnare una licenza | Amministratore utenti | 
Creare un gruppo | Amministratore utenti | 
Creare, aggiornare o cancellare la verifica di accesso di un gruppo o di un'applicazione | Amministratore utenti | 
Gestire la scadenza dei gruppi | Amministratore utenti | 
Gestire le impostazioni dei gruppi | Amministratore globale | 
Leggere tutta la configurazione (eccetto l'appartenenza nascosta) | Ruoli con autorizzazioni di lettura nella directory | Ruolo utente predefinito ([vedere la documentazione](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))
Leggere le appartenenze nascoste | Membro di un gruppo | Il proprietario del gruppo, amministratore Password, amministratore di Exchange, l'amministratore di SharePoint, amministratore del team, l'utente amministratore
Leggere l'appartenenza dei gruppi con appartenenza nascosta | Amministratore supporto tecnico | Utente, amministratore di team
Revocare la licenza | Amministratore licenze | Amministratore utenti
Aggiornare l'appartenenza al gruppo | Proprietario del gruppo ([vedere la documentazione](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Amministratore utenti
Aggiornare i proprietari dei gruppi | Proprietario del gruppo ([vedere la documentazione](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Amministratore utenti
Aggiornare proprietà del gruppo | Proprietario del gruppo ([vedere la documentazione](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Amministratore utenti

## <a name="identity-protection"></a>Identity Protection

Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi
---- | --------------------- | ----------------
Configurare notifiche di avviso| Amministratore della sicurezza | 
Configurare e abilitare o disabilitare i criteri di autenticazione a più fattori| Amministratore della sicurezza | 
Configurare e abilitare o disabilitare i criteri di rischio di accesso| Amministratore della sicurezza | 
Configurare e abilitare o disabilitare i criteri di rischio utente | Amministratore della sicurezza | 
Configurare digest settimanale | Amministratore della sicurezza| 
Ignorare tutti gli eventi di rischio | Amministratore della sicurezza | 
Correggere o ignorare una vulnerabilità | Amministratore della sicurezza | 
Leggere tutta la configurazione | Ruolo con autorizzazioni di lettura per la sicurezza | 
Leggere tutti gli eventi di rischio | Ruolo con autorizzazioni di lettura per la sicurezza | 
Leggere le vulnerabilità | Ruolo con autorizzazioni di lettura per la sicurezza | 

## <a name="licenses"></a>Licenze

Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi
---- | --------------------- | ----------------
Assegnare una licenza | Amministratore licenze | Amministratore utenti
Leggere tutta la configurazione | Ruoli con autorizzazioni di lettura nella directory | Ruolo utente predefinito ([vedere la documentazione](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))
Revocare la licenza | Amministratore licenze | Amministratore utenti
Provare o acquistare la sottoscrizione | Amministratore fatturazione | 


## <a name="monitoring---audit-logs"></a>Monitoraggio - log di controllo

Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi
---- | --------------------- | ----------------
Leggere i log di controllo | Lettore di report | Ruolo con autorizzazioni di lettura per la sicurezza, amministratore della sicurezza

## <a name="monitoring---sign-ins"></a>Monitoraggio - accessi

Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi
---- | --------------------- | ----------------
Leggere log di accesso | Lettore di report | Ruolo con autorizzazioni di lettura per la sicurezza, amministratore della sicurezza

## <a name="multi-factor-authentication"></a>Autenticazione a più fattori

Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi
---- | --------------------- | ----------------
Eliminare tutte le password dell'app esistenti generate dagli utenti selezionati | Amministratore globale | 
Disabilitare autenticazione a più fattori | Amministratore globale | 
Abilitare MFA | Amministratore globale | 
Gestire le impostazioni del servizio di autenticazione a più fattori | Amministratore globale | 
Richiedere agli utenti selezionati di fornire di nuovo i metodi di contatto | Amministratore dell'autenticazione | 
Ripristinare l'autenticazione a più fattori in tutti i dispositivi memorizzati  | Amministratore dell'autenticazione | 

## <a name="mfa-server"></a>Server MFA

Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi
---- | --------------------- | ----------------
Blocca/Sblocca utenti | Amministratore globale | 
Configurare blocco degli account | Amministratore globale | 
Configurare regole di memorizzazione nella cache | Amministratore globale | 
Configurare gli avvisi di illecito | Amministratore globale
Configurare le notifiche | Amministratore globale | 
Configurare un bypass monouso | Amministratore globale | 
Configurare impostazioni telefonata | Amministratore globale | 
Configurare provider | Amministratore globale | 
Configurare le impostazioni del server | Amministratore globale | 
Leggere il report attività | Amministratore globale | 
Leggere tutta la configurazione | Amministratore globale | 
Leggere lo stato del server | Amministratore globale |  

## <a name="organizational-relationships"></a>Relazioni aziendali

Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi
---- | --------------------- | ----------------
Gestire i provider di identità | Amministratore globale | 
Gestire le impostazioni | Amministratore globale | 
Gestire le condizioni per l'utilizzo | Amministratore globale | 
Leggere tutta la configurazione | Amministratore globale | 

## <a name="password-reset"></a>Reimpostazione delle password

Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi
---- | --------------------- | ----------------
Configurare i metodi di autenticazione | Amministratore globale |
Configurare personalizzazione | Amministratore globale |
Configurare notifica | Amministratore globale |
Configurare integrazione locale | Amministratore globale |
Configurare le proprietà di reimpostazione della password | Amministratore utenti | Amministratore globale
Configurare registrazione | Amministratore globale |
Leggere tutta la configurazione | Amministratore della sicurezza | Amministratore utenti |

## <a name="privileged-identity-management"></a>Privileged Identity Management

Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi
---- | --------------------- | ----------------
Assegnare utenti ai ruoli | Amministratore dei ruoli con privilegi | 
Configurare le impostazioni dei ruoli | Amministratore dei ruoli con privilegi | 
Visualizzare attività di controllo | Ruolo con autorizzazioni di lettura per la sicurezza | 
Visualizzare i membri dei ruoli | Ruolo con autorizzazioni di lettura per la sicurezza | 

## <a name="roles-and-administrators"></a>Ruoli e amministratori

Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi
---- | --------------------- | ----------------
Gestire le assegnazioni di ruoli | Amministratore dei ruoli con privilegi | 
Leggere verifica di accesso di un ruolo di Azure AD  | Ruolo con autorizzazioni di lettura per la sicurezza | Amministratore della sicurezza, amministratore dei ruoli con privilegi
Leggere tutta la configurazione | Ruolo utente predefinito ([vedere la documentazione](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 

## <a name="security---authentication-methods"></a>Sicurezza - metodi di autenticazione

Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi
---- | --------------------- | ----------------
Configurare i metodi di autenticazione | Amministratore globale | 
Leggere tutta la configurazione | Amministratore globale | 

## <a name="security---conditional-access"></a>Sicurezza - accesso condizionale

Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi
---- | --------------------- | ----------------
Configurare indirizzi IP attendibili MFA | Amministratore di accesso condizionale | 
Creare controlli personalizzati | Amministratore di accesso condizionale | Amministratore della sicurezza
Creare posizioni specifiche | Amministratore di accesso condizionale | Amministratore della sicurezza
Creare criteri | Amministratore di accesso condizionale | Amministratore della sicurezza
Creare condizioni per l'utilizzo | Amministratore di accesso condizionale | Amministratore della sicurezza
Creare il certificato di connettività VPN | Amministratore di accesso condizionale | Amministratore della sicurezza
Eliminare un criterio classico | Amministratore di accesso condizionale | Amministratore della sicurezza
Eliminare le condizioni per l'utilizzo | Amministratore di accesso condizionale | Amministratore della sicurezza
Eliminare il certificato di connettività VPN | Amministratore di accesso condizionale | Amministratore della sicurezza
Disabilitare i criteri classici | Amministratore di accesso condizionale | Amministratore della sicurezza
Gestire controlli personalizzati | Amministratore di accesso condizionale | Amministratore della sicurezza
Gestire posizioni specifiche | Amministratore di accesso condizionale | Amministratore della sicurezza
Gestire le condizioni per l'utilizzo | Amministratore di accesso condizionale | Amministratore della sicurezza
Leggere tutta la configurazione | Ruolo con autorizzazioni di lettura per la sicurezza | Amministratore della sicurezza
Leggere posizioni specifiche | Ruolo con autorizzazioni di lettura per la sicurezza | Amministratore di accesso condizionale, amministratore della sicurezza

## <a name="security---identity-security-score"></a>Sicurezza - punteggio di sicurezza delle identità

Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi | 
---- | --------------------- | ----------------
Leggere tutta la configurazione | Ruolo con autorizzazioni di lettura per la sicurezza | Amministratore della sicurezza
Leggere punteggio di sicurezza | Ruolo con autorizzazioni di lettura per la sicurezza | Amministratore della sicurezza
Aggiornare stato dell'evento | Amministratore della sicurezza | 

## <a name="security---risky-sign-ins"></a>Sicurezza - accessi a rischio

Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi
---- | --------------------- | ----------------
Leggere tutta la configurazione | Ruolo con autorizzazioni di lettura per la sicurezza | 
Leggere gli accessi a rischio | Ruolo con autorizzazioni di lettura per la sicurezza | 

## <a name="security---users-flagged-for-risk"></a>Sicurezza - utenti contrassegnati per il rischio

Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi
---- | --------------------- | ----------------
eliminare tutti gli eventi | Amministratore della sicurezza | 
Leggere tutta la configurazione | Ruolo con autorizzazioni di lettura per la sicurezza | 
Leggere utenti contrassegnati per il rischio | Ruolo con autorizzazioni di lettura per la sicurezza | 

## <a name="users"></a>Utenti

Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi
---- | --------------------- | ----------------
Aggiungere utenti al ruolo della directory | Amministratore dei ruoli con privilegi | 
Aggiungere utenti al gruppo | Amministratore utenti | 
Assegnare una licenza | Amministratore licenze | Amministratore utenti
Creare utente guest | Mittente dell'invito guest | Amministratore utenti
Create user | Amministratore utenti | 
Eliminare gli utenti | Amministratore utenti | 
Invalidare i token di aggiornamento degli amministratori con limitazioni (vedere la documentazione) | Amministratore utenti | 
Invalidare i token di aggiornamento dei non amministratori (vedere la documentazione) | Amministratore password | Amministratore utenti
Invalidare i token di aggiornamento degli amministratori con privilegi (vedere la documentazione) | Amministratore globale | 
Leggere configurazione di base | Ruolo utente predefinito ([vedere la documentazione](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Reimpostare la password per gli amministratori con limitazioni (vedere la documentazione) | Amministratore utenti | 
Reimpostare la password per i non amministratori (vedere la documentazione) | Amministratore password | Amministratore utenti
Reimpostare la password degli amministratori con privilegi | Amministratore globale | 
Revocare la licenza | Amministratore licenze | Amministratore utenti
Aggiornare tutte le proprietà, ad eccezione del nome dell'entità utente | Amministratore utenti | 
Aggiornare il nome dell'entità utente per gli amministratori con limitazioni (vedere la documentazione) | Amministratore utenti | 
Aggiornare la proprietà del nome dell'entità utente per gli amministratori con privilegi (vedere la documentazione) | Amministratore globale | 
Aggiornare le impostazioni dell'utente | Amministratore globale | 


## <a name="support"></a>Supporto

Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi
---- | --------------------- | ----------------
Inviare un ticket di supporto | Amministratore del servizio | Amministratore dell'applicazione, amministratore di Azure Information Protection, Password dell'amministratore, amministratore applicazione Cloud, conformità, amministratore Dynamics 365, Analitica Desktop, amministratore Exchange, di fatturazione Amministratore, amministratore di Intune, Skype per amministratori di Business, amministratore di Power BI, amministratore con privilegi di autenticazione, l'amministratore di SharePoint, amministratore le comunicazioni del team, i team, Amministratore utenti, Amministratore di Analitica all'area di lavoro

## <a name="next-steps"></a>Passaggi successivi

* [Come assegnare o rimuovere ruoli di amministratore di Azure AD](directory-manage-roles-portal.md)
* [Riferimento dei ruoli di amministratore di Azure AD](directory-assign-admin-roles.md)
