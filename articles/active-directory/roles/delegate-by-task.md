---
title: Delega ruoli per attività di amministrazione-Azure Active Directory | Microsoft Docs
description: Ruoli da delegare per attività di identità in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 11/05/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e83ff37140369cb073f4bdc2bd24ac08ee9b1ab
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93379095"
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

## <a name="external-identitiesb2c"></a>Identità esterne/B2C

Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi
---- | --------------------- | ----------------
Creare directory di Azure AD B2C | Tutti gli utenti non guest ([vedere la documentazione](../fundamentals/users-default-permissions.md)) | 
Creare applicazioni B2C | Amministratore globale | 
Creare applicazioni aziendali | Amministratore applicazione cloud | Amministratore applicazione
Creare, leggere, aggiornare ed eliminare criteri B2C | Amministratore dei criteri IEF B2C | 
Creare, leggere, aggiornare ed eliminare provider di identità | Amministratore dei provider di identità esterni | 
Creare, leggere, aggiornare ed eliminare flussi utente con ripristino delle password | Amministratore flusso utente ID esterno | 
Creare, leggere, aggiornare ed eliminare flussi utente con modifica dei profili | Amministratore flusso utente ID esterno | 
Creare, leggere, aggiornare ed eliminare flussi utente con accesso | Amministratore flusso utente ID esterno | 
Creare, leggere, aggiornare ed eliminare flussi utente con iscrizione |Amministratore flusso utente ID esterno | 
Creare, leggere, aggiornare ed eliminare attributi utente | Amministratore attributo flusso utente ID esterno | 
Creare, leggere, aggiornare ed eliminare utenti | Amministratore utenti
Leggere tutta la configurazione | Ruolo con autorizzazioni di lettura globali | 
Log di controllo di B2C di lettura | Lettore globale ([vedere la documentazione](../../active-directory-b2c/faq.md)) | 

> [!NOTE]
> Azure AD B2C i lettori globali non hanno le stesse autorizzazioni degli amministratori globali Azure AD. Se si dispone di Azure AD B2C privilegi di amministratore globale, assicurarsi di trovarsi in una directory Azure AD B2C e non in una directory di Azure AD.

## <a name="company-branding"></a>Informazioni personalizzate distintive dell'azienda

Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi
---- | --------------------- | ----------------
Configurare la personalizzazione aziendale | Amministratore globale | 
Leggere tutta la configurazione | Ruoli con autorizzazioni di lettura nella directory | Ruolo utente predefinito ([vedere la documentazione](../fundamentals/users-default-permissions.md))

## <a name="company-properties"></a>Proprietà della società

Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi
---- | --------------------- | ----------------
Configurare le proprietà aziendali | Amministratore globale | 

## <a name="connect"></a>Connessione

Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi
---- | --------------------- | ----------------
Autenticazione pass-through | Amministratore globale  | 
Leggere tutta la configurazione | Ruolo con autorizzazioni di lettura globali | Amministratore globale  |
Accesso Single Sign-On facile (Seamless SSO) | Amministratore globale  | 

## <a name="cloud-provisioning"></a>Provisioning cloud

Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi
---- | --------------------- | ----------------
Autenticazione pass-through | Amministratore delle identità ibride  | 
Leggere tutta la configurazione | Ruolo con autorizzazioni di lettura globali | Amministratore delle identità ibride  |
Accesso Single Sign-On facile (Seamless SSO) | Amministratore delle identità ibride  | 

## <a name="connect-health"></a>Connect Health

Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi
---- | --------------------- | ----------------
Aggiungere o eliminare i servizi | Proprietario ([vedere la documentazione](../hybrid/how-to-connect-health-operations.md)) | 
Applicare le correzioni agli errori di sincronizzazione | Collaboratore ([vedere la documentazione](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Proprietario
Configurare le notifiche | Collaboratore ([vedere la documentazione](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Proprietario
Configurare le impostazioni | Proprietario ([vedere la documentazione](../hybrid/how-to-connect-health-operations.md)) | 
Configurare le notifiche di sincronizzazione | Collaboratore ([vedere la documentazione](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Proprietario
Leggere report sulla sicurezza del file system distribuito di Azure (ADFS) | Ruolo con autorizzazioni di lettura per la sicurezza | Collaboratore, proprietario
Leggere tutta la configurazione | Lettore ([vedere la documentazione](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Collaboratore, proprietario
Leggere errori di sincronizzazione | Lettore ([vedere la documentazione](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Collaboratore, proprietario
Leggere servizi di sincronizzazione | Lettore ([vedere la documentazione](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Collaboratore, proprietario
Visualizzare metriche e avvisi | Lettore ([vedere la documentazione](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Collaboratore, proprietario
Visualizzare metriche e avvisi | Lettore ([vedere la documentazione](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Collaboratore, proprietario
Visualizzare le metriche del servizio di sincronizzazione e gli avvisi | Lettore ([vedere la documentazione](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Collaboratore, proprietario

## <a name="custom-domain-names"></a>Nomi di dominio personalizzati

Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi
---- | --------------------- | ----------------
Gestire domini | Amministratore globale | 
Leggere tutta la configurazione | Ruoli con autorizzazioni di lettura nella directory | Ruolo utente predefinito ([vedere la documentazione](../fundamentals/users-default-permissions.md))

## <a name="domain-services"></a>Servizi di dominio

Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi
---- | --------------------- | ----------------
Creare un'istanza di Azure AD Domain Services | Amministratore globale | 
Eseguire tutte le attività di servizi di dominio Azure Active Directory | Gruppo di amministratori Azure AD DC ([vedere la documentazione](../../active-directory-domain-services/tutorial-create-management-vm.md#administrative-tasks-you-can-perform-on-a-managed-domain)) | 
Leggere tutta la configurazione | Lettore nella sottoscrizione di Azure che contiene il servizio Active Directory Domain Services | 

## <a name="devices"></a>Dispositivi

Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi
---- | --------------------- | ----------------
Disabilitare dispositivo | Amministratore dispositivo cloud | 
Abilitare dispositivo | Amministratore dispositivo cloud | 
Leggere configurazione di base | Ruolo utente predefinito ([vedere la documentazione](../fundamentals/users-default-permissions.md)) | 
Leggere le chiavi BitLocker | Ruolo con autorizzazioni di lettura per la sicurezza | Amministratore password/amministratore sicurezza

## <a name="enterprise-applications"></a>Applicazioni aziendali

Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi
---- | --------------------- | ----------------
Fornire il consenso per eventuali autorizzazioni delegate | Amministratore di applicazioni cloud | Amministratore di applicazioni
Il consenso alle autorizzazioni dell'applicazione non include Microsoft Graph | Amministratore di applicazioni cloud | Amministratore di applicazioni
Consenso alle autorizzazioni dell'applicazione per Microsoft Graph | Amministratore dei ruoli con privilegi | 
Fornire il consenso alle applicazioni che accedono ai propri dati | Ruolo utente predefinito ([vedere la documentazione](../fundamentals/users-default-permissions.md)) | 
Creare applicazioni aziendali | Amministratore di applicazioni cloud | Amministratore di applicazioni
Gestire proxy di applicazione | Amministratore di applicazioni | 
Gestire le impostazioni dell'utente | Amministratore globale | 
Leggere la verifica di accesso di un gruppo o di un'applicazione | Ruolo con autorizzazioni di lettura per la sicurezza | Amministratore sicurezza/amministratore utente
Leggere tutta la configurazione | Ruolo utente predefinito ([vedere la documentazione](../fundamentals/users-default-permissions.md)) | 
Aggiornare le assegnazioni di applicazioni aziendali | Proprietario dell'applicazione aziendale ([vedere la documentazione](../fundamentals/users-default-permissions.md)) | Amministratore applicazione cloud, amministratore dell'applicazione
Aggiornare i proprietari di applicazioni aziendali | Proprietario dell'applicazione aziendale ([vedere la documentazione](../fundamentals/users-default-permissions.md)) | Amministratore applicazione cloud, amministratore dell'applicazione
Aggiornare le proprietà di applicazioni aziendali | Proprietario dell'applicazione aziendale ([vedere la documentazione](../fundamentals/users-default-permissions.md)) | Amministratore applicazione cloud, amministratore dell'applicazione
Aggiornare il provisioning di applicazioni aziendali | Proprietario dell'applicazione aziendale ([vedere la documentazione](../fundamentals/users-default-permissions.md)) | Amministratore applicazione cloud, amministratore dell'applicazione
Aggiornare il self-service di applicazioni aziendali | Proprietario dell'applicazione aziendale ([vedere la documentazione](../fundamentals/users-default-permissions.md)) | Amministratore applicazione cloud, amministratore dell'applicazione
Aggiornare le proprietà del servizio Single Sign-On | Proprietario dell'applicazione aziendale ([vedere la documentazione](../fundamentals/users-default-permissions.md)) | Amministratore applicazione cloud, amministratore dell'applicazione

## <a name="entitlement-management"></a>Gestione entitlement
Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi
---- | --------------------- | ----------------
Aggiungere risorse a un catalogo | Amministratore utenti | Con la gestione dei diritti, è possibile delegare questa attività al proprietario del catalogo ([vedere la documentazione](../governance/entitlement-management-catalog-create.md#add-additional-catalog-owners))
Aggiungere i siti di SharePoint Online al catalogo | Amministratore globale


## <a name="groups"></a>Gruppi

Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi
---- | --------------------- | ----------------
Assegnare una licenza | Amministratore utenti | 
Creare un gruppo | Amministratore di gruppi | Amministratore utenti
Creare, aggiornare o cancellare la verifica di accesso di un gruppo o di un'applicazione | Amministratore utenti | 
Gestire la scadenza dei gruppi | Amministratore utenti | 
Gestire le impostazioni dei gruppi | Amministratore di gruppi | Amministratore utenti | 
Leggere tutta la configurazione (eccetto l'appartenenza nascosta) | Ruoli con autorizzazioni di lettura nella directory | Ruolo utente predefinito ([vedere la documentazione](../fundamentals/users-default-permissions.md))
Leggere le appartenenze nascoste | Membro di un gruppo | Proprietario del gruppo, amministratore password, amministratore di Exchange, amministratore di SharePoint, amministratore team, Amministratore utenti
Leggere l'appartenenza dei gruppi con appartenenza nascosta | Amministratore del supporto tecnico | Amministratore utenti, amministratore team
Revocare la licenza | Amministratore licenze | Amministratore utenti
Aggiornare l'appartenenza al gruppo | Proprietario del gruppo ([vedere la documentazione](../fundamentals/users-default-permissions.md)) | Amministratore utenti
Aggiornare i proprietari dei gruppi | Proprietario del gruppo ([vedere la documentazione](../fundamentals/users-default-permissions.md)) | Amministratore utenti
Aggiornare proprietà del gruppo | Proprietario del gruppo ([vedere la documentazione](../fundamentals/users-default-permissions.md)) | Amministratore utenti
Eliminare gruppo | Amministratore di gruppi | Amministratore utenti

## <a name="identity-protection"></a>Identity Protection

Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi
---- | --------------------- | ----------------
Configurare notifiche di avviso| Amministratore della protezione | 
Configurare e abilitare o disabilitare i criteri di autenticazione a più fattori| Amministratore della protezione | 
Configurare e abilitare o disabilitare i criteri di rischio di accesso| Amministratore della protezione | 
Configurare e abilitare o disabilitare i criteri di rischio utente | Amministratore della protezione | 
Configurare digest settimanale | Amministratore della protezione| 
Ignora tutti i rilevamenti di rischio | Amministratore della protezione | 
Correggere o ignorare una vulnerabilità | Amministratore della protezione | 
Leggere tutta la configurazione | Ruolo con autorizzazioni di lettura per la sicurezza | 
Leggi tutti i rilevamenti di rischio | Ruolo con autorizzazioni di lettura per la sicurezza | 
Leggere le vulnerabilità | Ruolo con autorizzazioni di lettura per la sicurezza | 

## <a name="licenses"></a>Licenses

Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi
---- | --------------------- | ----------------
Assegnare una licenza | Amministratore licenze | Amministratore utenti
Leggere tutta la configurazione | Ruoli con autorizzazioni di lettura nella directory | Ruolo utente predefinito ([vedere la documentazione](../fundamentals/users-default-permissions.md))
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
Ripristinare l'autenticazione a più fattori in tutti i dispositivi memorizzati  | Amministratore dell'autenticazione | 

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
Leggere il report attività | Ruolo con autorizzazioni di lettura globali | 
Leggere tutta la configurazione | Ruolo con autorizzazioni di lettura globali | 
Leggere lo stato del server | Ruolo con autorizzazioni di lettura globali |  

## <a name="organizational-relationships"></a>Relazioni aziendali

Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi
---- | --------------------- | ----------------
Gestire i provider di identità | Amministratore dei provider di identità esterni | 
Gestire le impostazioni | Amministratore globale | 
Gestire le condizioni per l'utilizzo | Amministratore globale | 
Leggere tutta la configurazione | Ruolo con autorizzazioni di lettura globali | 

## <a name="password-reset"></a>Reimpostazione della password

Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi
---- | --------------------- | ----------------
Configurare i metodi di autenticazione | Amministratore globale |
Configurare personalizzazione | Amministratore globale |
Configurare notifica | Amministratore globale |
Configurare integrazione locale | Amministratore globale |
Configurare le proprietà di reimpostazione della password | Amministratore utenti | Amministratore globale
Configurare registrazione | Amministratore globale |
Leggere tutta la configurazione | Amministratore della protezione | Amministratore utenti |

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
Leggere tutta la configurazione | Ruolo utente predefinito ([vedere la documentazione](../fundamentals/users-default-permissions.md)) | 

## <a name="security---authentication-methods"></a>Sicurezza - metodi di autenticazione

Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi
---- | --------------------- | ----------------
Configurare i metodi di autenticazione | Amministratore globale | 
Configurare la protezione con password | Amministratore della sicurezza
Configurare il blocco intelligente | Amministratore della sicurezza
Leggere tutta la configurazione | Ruolo con autorizzazioni di lettura globali | 

## <a name="security---conditional-access"></a>Sicurezza-accesso condizionale

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
Leggere posizioni specifiche | Ruolo con autorizzazioni di lettura per la sicurezza | Amministratore dell'accesso condizionale, amministratore della sicurezza

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
eliminare tutti gli eventi | Amministratore della protezione | 
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
Invalidare i token di aggiornamento degli amministratori con privilegi (vedere la documentazione) | Amministratore autenticazione con privilegi | 
Leggere configurazione di base | Ruolo utente predefinito ([vedere la documentazione](../fundamentals/users-default-permissions.md) | 
Reimpostare la password per gli amministratori con limitazioni (vedere la documentazione) | Amministratore utenti | 
Reimpostare la password per i non amministratori (vedere la documentazione) | Amministratore password | Amministratore utenti
Reimpostare la password degli amministratori con privilegi | Amministratore autenticazione con privilegi | 
Revocare la licenza | Amministratore licenze | Amministratore utenti
Aggiornare tutte le proprietà, ad eccezione del nome dell'entità utente | Amministratore utenti | 
Aggiornare il nome dell'entità utente per gli amministratori con limitazioni (vedere la documentazione) | Amministratore utenti | 
Aggiornare la proprietà del nome dell'entità utente per gli amministratori con privilegi (vedere la documentazione) | Amministratore globale | 
Aggiornare le impostazioni dell'utente | Amministratore globale | 
Aggiornare i metodi di autenticazione | Amministratore dell'autenticazione | Amministratore di autenticazione con privilegi, amministratore globale


## <a name="support"></a>Supporto

Attività | Ruolo con privilegi minimi | Ruoli aggiuntivi
---- | --------------------- | ----------------
Inviare un ticket di supporto | Amministratore del servizio | Amministratore applicazione, amministratore Azure Information Protection, amministratore fatturazione, amministratore applicazione cloud, amministratore di conformità, amministratore di Dynamics 365, amministratore di analisi desktop, amministratore di Exchange, amministratore password, amministratore di Intune, amministratore di Skype for business, amministratore Power BI, amministratore di autenticazione con privilegi, amministratore di SharePoint, amministratore delle comunicazioni dei team, amministratore dei team, amministratore utente, amministratore di analisi dell'area di lavoro

## <a name="next-steps"></a>Passaggi successivi

* [Come assegnare o rimuovere i ruoli di amministratore di Azure AD](manage-roles-portal.md)
* [Riferimento dei ruoli di amministratore di Azure AD](permissions-reference.md)