---
title: Informazioni di riferimento sulle attività di controllo di Azure Active Directory (Azure AD) | Microsoft Docs
description: Vedi una panoramica delle attività di controllo che possono essere registrate nei log di controllo in Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/24/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: a687823d1344ca11864523cb9187f48d7373fd41
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989625"
---
# <a name="azure-ad-audit-activity-reference"></a>Informazioni di riferimento sulle attività di controllo di Azure AD

I report di Azure Active Directory (Azure AD) offrono tutte le informazioni necessarie per determinare le prestazioni dell'ambiente.

L'architettura di reporting in Azure AD include i componenti seguenti:

- **Report attività** 
    - [Accessi](concept-sign-ins.md): fornisce informazioni sull'uso delle applicazioni gestite e sulle attività di accesso degli utenti
    - [Log di controllo](concept-audit-logs.md): i log consentono la tracciabilità di tutte le modifiche apportate da varie funzionalità all'interno di Azure AD. 
    
- **Report sulla sicurezza** 
    - [Accessi a rischio](concept-risky-sign-ins.md). Un accesso rischioso è indicativo di un tentativo di accesso che potrebbe essere stato eseguito da qualcuno che non è il legittimo proprietario di un account utente. 
    - [Utenti contrassegnati per il rischio](concept-user-at-risk.md). Un utente rischioso è indicativo di un account utente che potrebbe essere stato compromesso. 

Questo articolo elenca le attività di controllo che possono essere registrate nei log di controllo.

## <a name="access-reviews"></a>Verifiche di accesso

|Categoria di controllo|Attività|
|---|---|
|Verifiche di accesso|Verifica di accesso terminata|
|Verifiche di accesso|Aggiungere un responsabile approvazione all'approvazione delle richieste|
|Verifiche di accesso|Aggiungere un revisore alla verifica di accesso|
|Verifiche di accesso|Applica la verifica di accesso|
|Verifiche di accesso|Crea la verifica di accesso|
|Verifiche di accesso|Creare un programma|
|Verifiche di accesso|Creare un'approvazione richiesta|
|Verifiche di accesso|Elimina la verifica di accesso|
|Verifiche di accesso|Eliminare un programma|
|Verifiche di accesso|Creare un collegamento al controllo del programma|
|Verifiche di accesso|Eseguire l'onboarding alle verifiche di accesso di Azure AD|
|Verifiche di accesso|Rimuovere un revisore dalla verifica di accesso|
|Verifiche di accesso|Richiedere l'arresto della verifica|
|Verifiche di accesso|Richiedi l'applicazione del risultato della verifica|
|Verifiche di accesso|Verificare l'appartenenza al ruolo di controllo degli accessi in base al ruolo|
|Verifiche di accesso|Verificare l'assegnazione di app|
|Verifiche di accesso|Verificare l'appartenenza a gruppi|
|Verifiche di accesso|Verificare la richiesta di approvazione richiesta|
|Verifiche di accesso|Eliminare il collegamento al controllo del programma|
|Verifiche di accesso|Aggiornare la verifica di accesso|
|Verifiche di accesso|Aggiornare lo stato dell'onboarding delle verifiche di accesso di Azure AD|
|Verifiche di accesso|Aggiorna le impostazioni per le notifiche tramite posta elettronica delle verifiche dell'accesso|
|Verifiche di accesso|Aggiorna l'impostazione del numero di ricorrenze delle verifiche di accesso|
|Verifiche di accesso|Aggiorna l'impostazione della durata in giorni delle ricorrenze delle verifiche di accesso|
|Verifiche di accesso|Aggiorna l'impostazione del tipo di fine ricorrenza delle verifiche di accesso|
|Verifiche di accesso|Aggiorna l'impostazione del tipo di ricorrenza delle verifiche di accesso|
|Verifiche di accesso|Aggiorna le impostazioni per i promemoria delle verifiche dell'accesso|
|Verifiche di accesso|Aggiornare un programma|
|Verifiche di accesso|Aggiornare un'approvazione richiesta|
|Verifiche di accesso|Utente disabilitato|

## <a name="account-provisioning"></a>Provisioning degli account

|Categoria di controllo|Attività|
|---|---|
|Gestione applicazioni|Recuperare le concessioni di autorizzazione per l'applicazione V2|
|Gestione applicazioni|Recuperare le entità servizio dell'applicazione V2 nel tenant corrente|
|Gestione applicazioni|Aggiornare l'applicazione V1|
|Gestione applicazioni|Aggiornare l'applicazione V2|
|Gestione applicazioni|Aggiornare le concessioni di autorizzazione per l'applicazione V2|
|Gestione applicazioni|Aggiungere OAuth2PermissionGrant|
|Gestione applicazioni|Aggiungere un'assegnazione di ruolo app a un'entità servizio|

## <a name="application-proxy"></a>Proxy dell'applicazione

|Categoria di controllo|Attività|
|---|---|
|Gestione applicazioni|Aggiungi applicazione|
|Gestione applicazioni|Aggiungere un proprietario a un'applicazione|
|Gestione applicazioni|Aggiungere un proprietario a un'entità servizio|
|Gestione applicazioni|Aggiungere un criterio a un'entità servizio|
|Gestione directory|Aggiunta di un'entità servizio|
|Gestione directory|Aggiunta delle credenziali dell'entità servizio|
|Gestione directory|Dare il consenso a un'applicazione|
|Gestione directory|Elimina applicazione|
|Gestione directory|Eliminare definitivamente un'applicazione|
|Gestione directory|Rimuovere OAuth2PermissionGrant|
|Gestione directory|Rimuovere un'assegnazione di ruolo app da un'entità servizio|
|Gestione directory|Rimuovere il proprietario da un'applicazione|
|Risorsa|Rimuovere il proprietario da un'entità servizio|
|Risorsa|Rimuovere i criteri da un'entità servizio|
|Risorsa|Rimozione di un'entità servizio|


## <a name="automated-password-rollover"></a>Rollover automatizzato delle password

|Categoria di controllo|Attività|
|---|---|
|Gestione applicazioni|Rimozione delle credenziali dell'entità servizio|


## <a name="b2c"></a>B2C

|Categoria di controllo|Attività|
|---|---|
|Gestione applicazioni|Ripristinare l'applicazione|
|Gestione applicazioni|Revocare il consenso|
|Gestione applicazioni|Aggiornare l'applicazione|
|Gestione applicazioni|Vengono aggiornati i segreti esterni.|
|Gestione applicazioni|Aggiornare un'entità servizio|
|Gestione applicazioni|Emettere un token di accesso per l'applicazione|
|Gestione applicazioni|Emettere un codice di autorizzazione per l'applicazione|
|Gestione applicazioni|Emettere un id_token per l'applicazione|
|Gestione applicazioni|Convalidare le credenziali dell'account utente locale|
|Gestione applicazioni|Convalidare l'autenticazione utente|
|Gestione applicazioni|Aggiungere autorizzazioni applicazione V2|
|Gestione applicazioni|Aggiungere una chiave basata sul segreto ASCII a un contenitore di chiavi CPIM|
|Gestione applicazioni|Aggiungere una chiave a un contenitore di chiavi CPIM|
|Gestione applicazioni|AdminPolicyDatas-SetResources|
|Gestione applicazioni|AdminUserJourneys-GetResources|
|Gestione applicazioni|AdminUserJourneys-RemoveResources|
|Authentication|AdminUserJourneys-SetResources|
|Authentication|Creare un provider di identità|
|Authentication|Creare un'applicazione V1|
|Authentication|Creare un'applicazione V2|
|Authentication|Creare un dominio personalizzato nel tenant|
|Authorization|Crea un nuovo AdminUserJourney|
|Authorization|Creare JSON di risorse localizzate|
|Authorization|Creare un nuovo provider di identità personalizzato|
|Authorization|Creare un nuovo provider di identità|
|Authorization|Creare o aggiornare una risorsa della directory B2C|
|Authorization|Crea criteri|
|Authorization|Creare criteri trustFramework|
|Authorization|Creare criteri trustFramework con prefisso configurabile|
|Authorization|Creare un attributo utente|
|Authorization|CreateTrustFrameworkPolicy|
|Authorization|Creare o aggiornare un nuovo AdminUserJourney|
|Authorization|Eliminare un provider di identità|
|Authorization|Eliminare un provider di identità|
|Authorization|Eliminare un'applicazione V1|
|Authorization|Eliminare un'applicazione V2|
|Authorization|Eliminare le concessioni di autorizzazione per l'applicazione V2|
|Authorization|Eliminare una risorsa della directory B2C|
|Authorization|Eliminare un contenitore di chiavi CPIM|
|Authorization|Eliminare criteri trustFramework|
|Authorization|Eliminare un attributo utente|
|Authorization|Abilitare la funzionalità B2C|
|Authorization|Ottenere le risorse di directory B2C in una sottoscrizione|
|Authorization|Ottenere un provider di identità personalizzato|
|Authorization|Ottenere un provider di identità|
|Authorization|Ottenere applicazioni V1 e V2|
|Authorization|Ottenere applicazione V1|
|Authorization|Ottenere applicazioni V1|
|Authorization|Ottenere applicazione V2|
|Authorization|Ottenere applicazioni V2|
|Authorization|Ottenere una risorsa della directory B2C|
|Authorization|Ottenere un elenco di domini personalizzati nel tenant|
|Authorization|Ottenere un percorso utente|
|Authorization|Ottenere le attestazioni dell'applicazione per il percorso utente|
|Authorization|Ottenere le attestazioni autocertificate consentite per il percorso utente|
|Authorization|Ottenere le attestazioni autocertificate consentite dei criteri|
|Authorization|Ottenere l'elenco di attestazioni di output disponibili|
|Authorization|Ottenere le definizioni del contenuto per il percorso utente|
|Authorization|Ottenere i provider di identità per un flusso di amministrazione specifico|
|Authorization|Ottenere i metadati di chiave attivi del contenitore di chiavi in JWK|
|Authorization|Ottenere l'elenco di tutti i flussi di amministrazione|
|Authorization|Ottenere l'elenco di tag per tutti i flussi di amministratore per tutti gli utenti|
|Authorization|Ottenere l'elenco di tenant per un utente|
|Authorization|Ottenere le attestazioni autocertificate degli account locali|
|Authorization|Ottenere JSON di risorse localizzate|
|Authorization|Ottenere le operazioni del provider di risorse Microsoft.AzureActiveDirectory|
|Authorization|Ottenere criteri|
|Authorization|Ottenere un criterio|
|Authorization|Ottenere le proprietà delle risorse di un tenant|
|Authorization|Ottenere l'elenco di provider di identità supportati|
|Authorization|Ottenere l'elenco di provider di identità supportati del percorso utente|
|Authorization|Ottenere le informazioni del tenant|
|Authorization|Ottenere le funzionalità consentite del tenant|
|Authorization|Ottenere un elenco di provider di identità personalizzati definiti dal tenant|
|Authorization|Ottenere un elenco di provider di identità definiti dal tenant|
|Authorization|Ottenere un elenco di provider di identità locali definiti dal tenant|
|Authorization|Ottenere i dettagli del tenant per un utente per la creazione di risorse|
|Authorization|Ottenere l'elenco di tenant|
|Authorization|Ottenere tenantDomains|
|Authorization|Ottenere le impostazioni cultura supportate predefinite per CPIM|
|Authorization|Ottenere i dettagli di un flusso di amministrazione|
|Authorization|Ottenere l'elenco di percorsi utente per questo tenant|
|Authorization|Ottenere il set di impostazioni cultura supportate disponibili per CPIM|
|Authorization|Ottenere il criterio trustFramework|
|Authorization|Ottenere il criterio trustFramework in formato XML|
|Authorization|Ottenere l'attributo utente|
|Authorization|Ottenere gli attributi utente|
|Authorization|Ottenere un elenco di percorsi utente|
|Authorization|GetIEFPolicies|
|Authorization|GetIdentityProviders|
|Authorization|GetTrustFrameworkPolicy|
|Authorization|Ottiene un contenitore di chiavi CPIM in formato JWK|
|Authorization|Ottiene un elenco di contenitori di chiavi nel tenant|
|Authorization|Ottiene il tipo di tenant|
|Authorization|MigrateTenantMetadata|
|Authorization|Applicare patch a un provider di identità|
|Authorization|PutTrustFrameworkPolicy|
|Authorization|PutTrustFrameworkpolicy|
|Authorization|Rimuovere un percorso utente|
|Authorization|Ripristinare un backup del contenitore di chiavi CPIM|
|Authorization|Recuperare le concessioni di autorizzazione per l'applicazione V2|
|Authorization|Recuperare le entità servizio dell'applicazione V2 nel tenant corrente|
|Authorization|Aggiornare un provider di identità personalizzato|
|Authorization|Aggiornare un provider di identità|
|Authorization|Aggiorna IdP locale|
|Authorization|Aggiornare l'applicazione V1|
|Authorization|Aggiornare l'applicazione V2|
|Authorization|Aggiornare le concessioni di autorizzazione per l'applicazione V2|
|Authorization|Criteri di aggiornamento|
|Authorization|Aggiornare l'attributo utente|
|Authorization|Caricare una chiave crittografata CPIM|
|Authorization|Autorizzazione utente: l'API è disabilitata per il set di funzionalità del tenant|
|Authorization|Autorizzazione utente: accesso concesso all'utente come 'Amministratore tenant'|
|Authorization|Autorizzazione utente: all'utente sono stati concessi diritti di accesso 'Utenti autenticati'|
|Authorization|Verificare se la funzionalità B2C è abilitata|
|Authorization|Verificare se la funzionalità è abilitata|
|Authorization|Creare un programma|
|Authorization|Eliminare un programma|
|Authorization|Creare un collegamento al controllo del programma|
|Authorization|Eseguire l'onboarding alle verifiche di accesso di Azure AD|
|Authorization|Eliminare il collegamento al controllo del programma|
|Authorization|Aggiornare un programma|
|Authorization|Disabilitare l'accesso Single Sign-On del desktop|
|Authorization|Disabilitare l'accesso Single Sign-On del desktop per un dominio specifico|
|Authorization|Disabilita proxy di applicazione|
|Authorization|Disabilitare l'autenticazione pass-through|
|Authorization|Abilitare l'accesso Single Sign-On del desktop|
|Gestione directory|Abilitare l'accesso Single Sign-On del desktop per un dominio specifico|
|Gestione directory|Abilitare il proxy di applicazione|
|Gestione directory|Abilitare l'autenticazione pass-through|
|Gestione directory|Creare un dominio personalizzato nel tenant|
|Gestione directory|Abilitare la funzionalità B2C|
|Gestione directory|Ottenere un elenco di domini personalizzati nel tenant|
|Gestione directory|Ottenere le proprietà delle risorse di un tenant|
|Gestione directory|Ottenere le informazioni del tenant|
|Gestione directory|Ottenere le funzionalità consentite del tenant|
|Gestione directory|Ottenere tenantDomains|
|Chiave|Ottiene il tipo di tenant|
|Chiave|Verificare se la funzionalità B2C è abilitata|
|Chiave|Verificare se la funzionalità è abilitata|
|Chiave|Aggiunta di un partner alla società|
|Chiave|Aggiungere un dominio non verificato|
|Chiave|Aggiungere un dominio verificato|
|Chiave|Creare un'azienda|
|Chiave|Vengono create le impostazioni aziendali.|
|Chiave|Vengono eliminate le impostazioni aziendali.|
|Chiave|Abbassare un partner di livello|
|Chiave|Directory eliminata|
|Altro|Directory eliminata in modo permanente|
|Altro|Directory pianificata per l'eliminazione|
|Risorsa|Innalzare un'azienda a livello di partner|
|Risorsa|Ripulire le proprietà di Rights Management|
|Risorsa|Rimuovere un partner dalla società|
|Risorsa|Rimuovere un dominio non verificato|
|Risorsa|Rimuovere un dominio verificato|
|Risorsa|Impostazione informazioni società|
|Risorsa|Impostare la funzionalità DirSync|
|Risorsa|Impostare il flag DirSyncEnabled|
|Risorsa|Configurare una relazione|
|Risorsa|Configurare una soglia di eliminazione accidentale|
|Risorsa|Configurare la posizione dei dati consentita per l'azienda|
|Risorsa|Abilitare la funzionalità multinazionale per l'azienda|
|Risorsa|Impostare una funzionalità directory nel tenant|
|Risorsa|Impostazione dell'autenticazione del dominio|
|Risorsa|Configurazione delle impostazioni di federazione nel dominio|
|Risorsa|Impostare il criterio password|
|Risorsa|Impostare le proprietà di Rights Management|
|Risorsa|Aggiornare l'azienda|
|Risorsa|Aggiornare le impostazioni aziendali|
|Risorsa|Aggiorna il dominio|
|Risorsa|Verifica dominio|
|Risorsa|Verifica del dominio tramite la verifica di posta elettronica|
|Risorsa|Onboarding|
|Risorsa|Aggiorna le impostazioni dell'avviso|
|Risorsa|Aggiornare le impostazioni del digest settimanale|
|Risorsa|Disabilitare il writeback delle password per la directory|
|Risorsa|Abilitare il writeback delle password per la directory|
|Risorsa|Aggiungere un'assegnazione di ruolo app a un gruppo|
|Risorsa|Aggiungi gruppo|
|Risorsa|Aggiungi membro al gruppo|
|Risorsa|Aggiungere un proprietario a un gruppo|
|Risorsa|Creare le impostazioni dei gruppi|
|Risorsa|Elimina gruppo|
|Risorsa|Eliminare le impostazioni dei gruppi|
|Risorsa|Terminare l'applicazione di licenze basate sui gruppi agli utenti|
|Risorsa|Eliminare definitivamente un gruppo|
|Risorsa|Rimuovere un'assegnazione di ruolo app da un gruppo|
|Risorsa|Rimuovi membro dal gruppo|
|Risorsa|Rimuovere il proprietario da un gruppo|
|Risorsa|Ripristinare un gruppo|
|Risorsa|Configurare la licenza di un gruppo|
|Risorsa|È stato configurato il gruppo che deve essere gestito dall'utente.|
|Risorsa|Avviare l'applicazione di licenze basate sui gruppi agli utenti|
|Risorsa|Attivare il ricalcolo della licenza del gruppo|
|Risorsa|Aggiornare un gruppo|
|Risorsa|Aggiornare le impostazioni dei gruppi|
|Risorsa|Aggiungi membro|
|Risorsa|Creare un gruppo|
|Risorsa|Elimina gruppo|
|Risorsa|Rimuovi membro|
|Risorsa|Aggiorna gruppo|
|Risorsa|Approvare una richiesta di partecipazione a un gruppo in sospeso|
|Risorsa|Annullare una richiesta di partecipazione a un gruppo in sospeso|
|Risorsa|Creare criteri di gestione del ciclo di vita|
|Risorsa|Eliminare una richiesta di partecipazione a un gruppo in sospeso|
|Risorsa|Rifiutare una richiesta di partecipazione a un gruppo in sospeso|
|Risorsa|Rinnova il gruppo|
|Risorsa|Richiedere di partecipare a un gruppo|
|Risorsa|Impostare proprietà di gruppi dinamici|
|Risorsa|Aggiornare criteri di gestione del ciclo di vita|
|Risorsa|Aggiungere una chiave basata sul segreto ASCII a un contenitore di chiavi CPIM|
|Risorsa|Aggiungere una chiave a un contenitore di chiavi CPIM|
|Risorsa|Eliminare un contenitore di chiavi CPIM|
|Risorsa|Eliminare un contenitore di chiavi|
|Risorsa|Ottenere i metadati di chiave attivi del contenitore di chiavi in JWK|
|Risorsa|Ottenere i metadati del contenitore di chiavi|
|Risorsa|Ottiene un contenitore di chiavi CPIM in formato JWK|
|Risorsa|Ottiene un elenco di contenitori di chiavi nel tenant|
|Risorsa|Ripristinare un backup del contenitore di chiavi CPIM|
|Risorsa|Salvare un contenitore di chiavi|
|Risorsa|Caricare una chiave crittografata CPIM|
|Risorsa|Emettere un codice di autorizzazione per l'applicazione|
|Risorsa|Emettere un id_token per l'applicazione|


## <a name="core-directory"></a>Directory principale

|Categoria di controllo|Attività|
|---|---|
|Gestione delle unità amministrative|Scaricare un singolo tipo di evento di rischio|
|Gestione delle unità amministrative|Scaricare amministratori e stato del consenso esplicito per il digest settimanale|
|Gestione delle unità amministrative|Scaricare tutti i tipi di eventi di rischio|
|Gestione delle unità amministrative|Scaricare gli eventi di rischio degli utenti del piano gratuito|
|Gestione delle unità amministrative|Scaricare gli utenti contrassegnati per il rischio|
|Gestione applicazioni|Inviti batch elaborati|
|Gestione applicazioni|Inviti batch caricati|
|Gestione applicazioni|Aggiungere un proprietario a un criterio|
|Gestione applicazioni|Aggiungi criteri|
|Gestione applicazioni|Elimina criteri|
|Gestione applicazioni|Rimuovere le credenziali dei criteri|
|Gestione applicazioni|Criteri di aggiornamento|
|Gestione applicazioni|Impostare i criteri della registrazione MFA|
|Gestione applicazioni|Impostare i criteri di rischio di accesso|
|Gestione applicazioni|Impostare i criteri di rischio utente|
|Gestione applicazioni|Accettare le condizioni per l'utilizzo|
|Gestione applicazioni|Creare le condizioni per l'utilizzo|
|Gestione applicazioni|Rifiutare le condizioni per l'utilizzo|
|Gestione applicazioni|Eliminare le condizioni per l'utilizzo|
|Gestione applicazioni|Modificare le condizioni per l'utilizzo|
|Gestione applicazioni|Pubblicare le condizioni per l'utilizzo|
|Gestione applicazioni|Annullare la pubblicazione delle condizioni per l'utilizzo|
|Gestione applicazioni|Aggiungere il certificato SSL dell'applicazione|
|Gestione applicazioni|Eliminare il binding SSL|
|Gestione applicazioni|Registrare il connettore|
|Gestione applicazioni|AdminPolicyDatas-RemoveResources|
|Gestione applicazioni|AdminPolicyDatas-SetResources|
|Gestione applicazioni|AdminUserJourneys-GetResources|
|Gestione directory|AdminUserJourneys-RemoveResources|
|Gestione directory|AdminUserJourneys-SetResources|
|Gestione directory|Creare un provider di identità|
|Gestione directory|Crea un nuovo AdminUserJourney|
|Gestione directory|Creare JSON di risorse localizzate|
|Gestione directory|Creare un nuovo provider di identità personalizzato|
|Gestione directory|Creare un nuovo provider di identità|
|Gestione directory|Creare o aggiornare una risorsa della directory B2C|
|Gestione directory|Crea criteri|
|Gestione directory|Creare criteri trustFramework|
|Gestione directory|Creare criteri trustFramework con prefisso configurabile|
|Gestione directory|Creare un attributo utente|
|Gestione directory|CreateTrustFrameworkPolicy|
|Gestione directory|Eliminare un provider di identità|
|Gestione directory|Eliminare un provider di identità|
|Gestione directory|Eliminare una risorsa della directory B2C|
|Gestione directory|Eliminare criteri trustFramework|
|Gestione directory|Eliminare un attributo utente|
|Gestione directory|Ottenere le risorse di directory B2C in un gruppo di risorse|
|Gestione directory|Ottenere le risorse di directory B2C in una sottoscrizione|
|Gestione directory|Ottenere un provider di identità personalizzato|
|Gestione directory|Ottenere un provider di identità|
|Gestione directory|Ottenere una risorsa della directory B2C|
|Gestione directory|Ottenere un percorso utente|
|Gestione directory|Ottenere le attestazioni dell'applicazione per il percorso utente|
|Gestione directory|Ottenere le attestazioni autocertificate consentite per il percorso utente|
|Gestione directory|Ottenere le attestazioni autocertificate consentite dei criteri|
|Gestione directory|Ottenere l'elenco di attestazioni di output disponibili|
|Gestione directory|Ottenere le definizioni del contenuto per il percorso utente|
|Gestione directory|Ottenere i provider di identità per un flusso di amministrazione specifico|
|Gestione directory|Ottenere l'elenco di tutti i flussi di amministrazione|
|Gestione directory|Ottenere l'elenco di tag per tutti i flussi di amministratore per tutti gli utenti|
|Gestione gruppo|Ottenere l'elenco di tenant per un utente|
|Gestione gruppo|Ottenere le attestazioni autocertificate degli account locali|
|Gestione gruppo|Ottenere JSON di risorse localizzate|
|Gestione gruppo|Ottenere le operazioni del provider di risorse Microsoft.AzureActiveDirectory|
|Gestione gruppo|Ottenere criteri|
|Gestione gruppo|Ottenere un criterio|
|Gestione gruppo|Ottenere l'elenco di provider di identità supportati|
|Gestione gruppo|Ottenere l'elenco di provider di identità supportati del percorso utente|
|Gestione gruppo|Ottenere un elenco di provider di identità personalizzati definiti dal tenant|
|Gestione gruppo|Ottenere un elenco di provider di identità definiti dal tenant|
|Gestione gruppo|Ottenere un elenco di provider di identità locali definiti dal tenant|
|Gestione gruppo|Ottenere i dettagli del tenant per un utente per la creazione di risorse|
|Gestione gruppo|Ottenere le impostazioni cultura supportate predefinite per CPIM|
|Gestione gruppo|Ottenere i dettagli di un flusso di amministrazione|
|Gestione gruppo|Ottenere l'elenco di percorsi utente per questo tenant|
|Gestione gruppo|Ottenere il set di impostazioni cultura supportate disponibili per CPIM|
|Gestione gruppo|Ottenere il criterio trustFramework|
|Gestione gruppo|Ottenere il criterio trustFramework in formato XML|
|Gestione gruppo|Ottenere l'attributo utente|
|Gestione dei criteri|Ottenere gli attributi utente|
|Gestione dei criteri|Ottenere un elenco di percorsi utente|
|Gestione dei criteri|GetIEFPolicies|
|Gestione dei criteri|GetIdentityProviders|
|Gestione dei criteri|GetTrustFrameworkPolicy|
|Risorsa|MigrateTenantMetadata|
|Risorsa|Sposta risorse|
|Risorsa|Applicare patch a un provider di identità|
|Risorsa|PutTrustFrameworkPolicy|
|Risorsa|PutTrustFrameworkpolicy|
|Risorsa|Rimuovere un percorso utente|
|Risorsa|Aggiornare un provider di identità personalizzato|
|Risorsa|Aggiornare un provider di identità|
|Risorsa|Aggiorna IdP locale|
|Risorsa|Aggiornare una risorsa della directory B2C|
|Risorsa|Criteri di aggiornamento|
|Risorsa|Aggiornare lo stato della sottoscrizione|
|Gestione dei ruoli|Aggiornare l'attributo utente|
|Gestione dei ruoli|Convalidare lo spostamento di risorse|
|Gestione dei ruoli|Aggiungere un dispositivo|
|Gestione dei ruoli|Aggiungere una configurazione del dispositivo|
|Gestione dei ruoli|Aggiungere un proprietario registrato a un dispositivo|
|Gestione dei ruoli|Aggiungere utenti registrati a un dispositivo|
|Gestione dei ruoli|Elimina dispositivo|
|Gestione dei ruoli|Eliminazione della configurazione del dispositivo|
|Gestione dei ruoli|Dispositivo non più conforme|
|Gestione dei ruoli|Dispositivo non più gestito|
|Gestione utenti|Rimuovere il proprietario registrato da un dispositivo|
|Gestione utenti|Rimuovere utenti registrati da un dispositivo|
|Gestione utenti|Aggiorna dispositivo|
|Gestione utenti|Aggiornare la configurazione di un dispositivo|
|Gestione utenti|Aggiungere un membro idoneo al ruolo|
|Gestione utenti|Aggiungere un membro a un ruolo|
|Gestione utenti|Aggiungere l'assegnazione di ruolo alla definizione del ruolo|
|Gestione utenti|Aggiungere un ruolo da un modello|
|Gestione utenti|Aggiungere un membro con ambito a un ruolo|
|Gestione utenti|Rimuovere un membro idoneo da un ruolo|
|Gestione utenti|Rimuovi il membro dal ruolo|
|Gestione utenti|Rimuovere l'assegnazione di ruolo dalla definizione del ruolo|
|Gestione utenti|Rimuovere un membro con ambito da un ruolo|
|Gestione utenti|Aggiornare un ruolo|
|Gestione utenti|AccessReview_Review|
|Gestione utenti|AccessReview_Update|
|Gestione utenti|ActivationAborted|
|Gestione utenti|ActivationApproved|
|Gestione utenti|ActivationCanceled|
|Gestione utenti|ActivationRequested|
|Gestione utenti|Aggiunto|
|Gestione utenti|Assegna|


## <a name="identity-protection"></a>Identity Protection

|Categoria di controllo|Attività|
|---|---|
|Gestione directory|Eleva|
|Gestione directory|Rimosso|
|Gestione directory|Modifiche alle impostazioni dei ruoli|
|Altro|ScanAlertsNow|
|Altro|Iscrizione|
|Altro|Annullare elevazione|
|Altro|UpdateAlertSettings|
|Altro|UpdateCurrentState|
|Gestione dei criteri|Verifica di accesso terminata|
|Gestione dei criteri|Aggiungere un responsabile approvazione all'approvazione delle richieste|
|Gestione dei criteri|Aggiungere un revisore alla verifica di accesso|
|Gestione utenti|Applica la verifica di accesso|
|Gestione utenti|Crea la verifica di accesso|


## <a name="invited-users"></a>Utenti invitati

|Categoria di controllo|Attività|
|---|---|
|Altro|Creare un'approvazione richiesta|
|Altro|Elimina la verifica di accesso|
|Gestione utenti|Rimuovere un revisore dalla verifica di accesso|
|Gestione utenti|Richiedi l'applicazione del risultato della verifica|
|Gestione utenti|Richiedere l'arresto della verifica|
|Gestione utenti|Verificare l'assegnazione di app|
|Gestione utenti|Verificare l'appartenenza a gruppi|
|Gestione utenti|Verificare l'appartenenza al ruolo di controllo degli accessi in base al ruolo|


## <a name="microsoft-identity-manager-mim"></a>Microsoft Identity Manager (MIM)

|Categoria di controllo|Attività|
|---|---|
|Gestione gruppo|Verificare la richiesta di approvazione richiesta|
|Gestione gruppo|Aggiornare la verifica di accesso|
|Gestione gruppo|Aggiorna le impostazioni per le notifiche tramite posta elettronica delle verifiche dell'accesso|
|Gestione gruppo|Aggiorna l'impostazione del numero di ricorrenze delle verifiche di accesso|
|Gestione gruppo|Aggiorna l'impostazione della durata in giorni delle ricorrenze delle verifiche di accesso|
|Gestione utenti|Aggiorna l'impostazione del tipo di fine ricorrenza delle verifiche di accesso|
|Gestione utenti|Aggiorna l'impostazione del tipo di ricorrenza delle verifiche di accesso|



## <a name="privileged-identity-management"></a>Privileged Identity Management

|Categoria di controllo|Attività|
|---|---|
|PIM|ActivationAborted|
|PIM|ActivationApproved|
|PIM|ActivationCanceled|
|PIM|ActivationDenied|
|PIM|ActivationRequested|
|PIM|Aggiunto|
|PIM|AddedOutsidePIM|
|PIM|Assegna|
|PIM|DismissAlert|
|PIM|Eleva|
|PIM|ReactivateAlert|
|PIM|Rimosso|
|PIM|RemovedOutsidePIM|
|PIM|Richiedere l'arresto della verifica|
|PIM|Modifiche alle impostazioni dei ruoli|
|PIM|ScanAlertsNow|
|PIM|Iscrizione|
|PIM|Annullare l'assegnazione|
|PIM|Annullare elevazione|
|PIM|UpdateAlertSettings|
|PIM|UpdateCurrentState|


## <a name="self-service-group-management"></a>Gestione di gruppi self-service

|Categoria di controllo|Attività|
|---|---|
|Gestione gruppo|Reimpostazione password utente|
|Gestione gruppo|Ripristina utente|
|Gestione gruppo|Impostazione forzatura per la modifica delle password utente|
|Gestione gruppo|Impostare la gestione utenti|
|Gestione gruppo|Abilitare i metadati del token OATH degli utenti|
|Gestione gruppo|Aggiornare il timestamp StsRefreshTokenValidFrom|
|Gestione gruppo|Vengono aggiornati i segreti esterni.|
|Gestione gruppo|Aggiorna utente|
|Gestione gruppo|L'amministrazione genera una password temporanea|


## <a name="self-service-password-management"></a>Gestione delle password self-service

|Categoria di controllo|Attività|
|---|---|
|Gestione directory|L'amministratore richiede all'utente di reimpostare la password|
|Gestione directory|Assegnare utenti esterni all'applicazione|
|Gestione utenti|Messaggio di posta elettronica non inviato, l'utente ha annullato la sottoscrizione|
|Gestione utenti|Invitare utente esterno|
|Gestione utenti|Riscattare un invito per utente esterno|
|Gestione utenti|Creazione del tenant virale|
|Gestione utenti|Creazione dell'utente virale|
|Gestione utenti|Registrazione della password utente|
|Gestione utenti|Reimpostazione della password utente|
|Gestione utenti|Bloccato dalla reimpostazione password self-service|


## <a name="terms-of-use"></a>Condizioni per l'utilizzo

|Categoria di controllo|Attività|
|---|---|
|Condizioni per l'utilizzo|Accettare le condizioni per l'utilizzo|
|Condizioni per l'utilizzo|Creare le condizioni per l'utilizzo|
|Condizioni per l'utilizzo|Rifiutare le condizioni per l'utilizzo|
|Condizioni per l'utilizzo|Eliminare il consenso|
|Condizioni per l'utilizzo|Eliminare le condizioni per l'utilizzo|
|Condizioni per l'utilizzo|Modificare le condizioni per l'utilizzo|
|Condizioni per l'utilizzo|Scadenza delle condizioni per l'utilizzo|
|Condizioni per l'utilizzo|Eliminare definitivamente le condizioni per l'utilizzo|
|Condizioni per l'utilizzo|Pubblicare le condizioni per l'utilizzo|
|Condizioni per l'utilizzo|Annullare la pubblicazione delle condizioni per l'utilizzo|


## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dei report di Azure AD](overview-reports.md).
- [Report dei log di controllo](concept-audit-logs.md). 
- [Accesso programmatico ai report di Azure AD](concept-reporting-api.md)
