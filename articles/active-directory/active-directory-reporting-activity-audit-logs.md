---
title: Report delle attività di controllo nel portale di Azure Active Directory | Microsoft Docs
description: Introduzione ai report delle attività di controllo nel portale di Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/31/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 97ea32a1e0f8815accff6201251771ab8c088859
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2018
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Report delle attività di controllo nel portale di Azure Active Directory 

I report di Azure Active Directory (Azure AD) offrono tutte le informazioni necessarie per determinare lo stato dell'ambiente.

L'architettura di reporting in Azure AD include i componenti seguenti:

- **Attività** 
    - **Attività di accesso** : informazioni sull'utilizzo delle applicazioni gestite e sulle attività di accesso utente
    - **Log di controllo**: informazioni relative alle attività di sistema sulla gestione di utenti e gruppi, sulle applicazioni gestite e sulle attività di directory.
- **Sicurezza** 
    - **Accessi a rischio**. Un accesso rischioso è indicativo di un tentativo di accesso che potrebbe essere stato eseguito da qualcuno che non è il legittimo proprietario di un account utente. Per informazioni dettagliate, vedere Accessi a rischio.
    - **Utenti contrassegnati per il rischio**. Un utente rischioso è indicativo di un account utente che potrebbe essere stato compromesso. Per informazioni dettagliate, vedere Utenti contrassegnati per il rischio.

In questo argomento viene offerta una panoramica delle attività di controllo.
 
## <a name="who-can-access-the-data"></a>Chi può accedere ai dati?
* Gli utenti con ruolo di amministratore della sicurezza o con autorizzazioni di lettura per la sicurezza
* Gli amministratori globali
* I singoli utenti (non amministratori) possono visualizzare le proprie attività


## <a name="audit-logs"></a>Log di controllo

I log di controllo in Azure Active Directory forniscono i record delle attività di sistema per la conformità.  
Il primo punto di ingresso a tutti i dati di controllo è **Log di controllo** nella sezione **Attività** di **Azure Active Directory**.

![Log di controllo](./media/active-directory-reporting-activity-audit-logs/61.png "Log di controllo")

Un log di controllo è una visualizzazione elenco predefinita che include:

- Data e ora dell'occorrenza.
- Iniziatore o attore di un'attività (*chi*) 
- Attività (*cosa*) 
- Destinazione

![Log di controllo](./media/active-directory-reporting-activity-audit-logs/18.png "Log di controllo")

Per personalizzare la visualizzazione elenco, fare clic su **Colonne** nella barra degli strumenti.

![Log di controllo](./media/active-directory-reporting-activity-audit-logs/19.png "Log di controllo")

In questo modo è possibile visualizzare campi aggiuntivi o rimuovere campi già visualizzati.

![Log di controllo](./media/active-directory-reporting-activity-audit-logs/21.png "Log di controllo")


Facendo clic su un elemento nella visualizzazione elenco, è possibile ottenere tutti i dettagli disponibili sull'elemento.

![Log di controllo](./media/active-directory-reporting-activity-audit-logs/22.png "Log di controllo")


## <a name="filtering-audit-logs"></a>Filtro dei log di controllo

Per limitare i dati segnalati in base alle esigenze, è possibile filtrare i dati di controllo usando i campi seguenti:

- Intervallo di date
- Azione avviata da (attore)
- Categoria
- Activity resource type (Tipo di risorsa dell'attività)
- Attività

![Log di controllo](./media/active-directory-reporting-activity-audit-logs/23.png "Log di controllo")


Il filtro **Intervallo di date** permette di definire un intervallo di tempo per i dati restituiti.  
I valori possibili sono:

- 1 mese
- 7 giorni
- 24 ore
- Personalizzate

Quando si seleziona un intervallo di tempo personalizzato, è possibile configurare un'ora di inizio e un'ora di fine.

Il filtro **Avviato da** permette di definire il nome di un attore o il relativo nome UPN (Universal Principal Name).

Il filtro **Categoria** permette di selezionare uno dei filtri seguenti:

- Tutti
- Categoria principale
- Directory principale
- Gestione delle password self-service
- Gestione di gruppi self-service
- Provisioning degli account e rollover automatizzato delle password
- Utenti invitati
- Servizio MIM
- Identity Protection
- B2C

Il filtro **Tipo di risorsa attività** permette di selezionare uno dei filtri seguenti:

- Tutti 
- Group
- Directory
- Utente
- Applicazione
- Criterio
- Dispositivo
- Altri

Quando si seleziona **Gruppo** come **Tipo di risorsa attività**, si ottiene una categoria di filtro aggiuntiva che permette di specificare anche un'**Origine**:

- Azure AD
- O365


Il filtro **Attività** filtro si basa sulla categoria e sul tipo di risorsa attività selezionati. È possibile selezionare un'attività specifica da visualizzare o selezionarle tutte. 

È possibile ottenere l'elenco di tutte le attività di controllo usando l'API Graph https://graph.windows.net/$tenantdomain/activities/auditActivityTypes?api-version=beta, dove $tenantdomain è il nome del dominio. In alternativa, vedere l'articolo relativo agli [eventi del report di controllo](active-directory-reporting-audit-events.md).


## <a name="audit-logs-shortcuts"></a>Collegamenti ai log di controllo

Oltre ad **Azure Active Directory**, il portale di Azure offre due ulteriori punti di ingresso ai dati di controllo:

- Utenti e gruppi
- Applicazioni aziendali

### <a name="users-and-groups-audit-logs"></a>Log di controllo di utenti e gruppi

Con i report di controllo basati su utenti e gruppi, è possibile ottenere risposte a domande come:

- Quali tipi di aggiornamenti sono stati applicati agli utenti?

- Quanti utenti sono stati modificati?

- Quante password sono state modificate?

- Quali operazioni ha eseguito un amministratore in una directory?

- Quali sono i gruppi che sono stati aggiunti?

- Sono presenti gruppi con modifiche all'appartenenza?

- I proprietari dei gruppi sono stati modificati?

- Quali licenze sono state assegnate a un gruppo o a un utente?

Per esaminare semplicemente i dati di controllo relativi a utenti e gruppi, è disponibile una visualizzazione filtrata in **Log di controllo** nella sezione **Attività** di **Utenti e gruppi**. Per questo punto di ingresso, **Tipo di risorsa attività** preselezionato è **Utenti e gruppi**.

![Log di controllo](./media/active-directory-reporting-activity-audit-logs/93.png "Log di controllo")

### <a name="enterprise-applications-audit-logs"></a>Log di controllo di applicazioni aziendali

Con i report di controllo basati sulle applicazioni, è possibile ottenere risposte a domande come:

* Quali sono le applicazioni che sono state aggiunte o aggiornate?
* Quali sono le applicazioni che sono state rimosse?
* È stata modificata un'entità servizio per un'applicazione?
* I nomi delle applicazioni sono stati modificati?
* Chi ha dato il consenso a un'applicazione?

Per esaminare semplicemente i dati di controllo relativi alle applicazioni, è disponibile una visualizzazione filtrata in **Log di controllo** nella sezione **Attività** del pannello **Applicazioni aziendali**. Per questo punto di ingresso, il **Tipo di risorsa attività** preselezionato è **Applicazioni aziendali**.

![Log di controllo](./media/active-directory-reporting-activity-audit-logs/134.png "Log di controllo")

È possibile filtrare ulteriormente questa visualizzazione per vedere solo i **gruppi** o gli **utenti**.

![Log di controllo](./media/active-directory-reporting-activity-audit-logs/25.png "Log di controllo")



## <a name="azure-ad-audit-activity-list"></a>Elenco di attività di controllo di Azure AD

Questa sezione fornisce un elenco di tutte le attività che possono essere registrate. 


|Nome servizio|Categoria di controllo|Tipo di risorsa di attività|Attività|
|---|---|---|---|
|Provisioning degli account|Gestione di applicazioni|Applicazione|Administration|
|Provisioning degli account|Gestione di applicazioni|Applicazione|Operazione sulla directory|
|Provisioning degli account|Gestione di applicazioni|Applicazione|Esportazione|
|Provisioning degli account|Gestione di applicazioni|Applicazione|Importa|
|Provisioning degli account|Gestione di applicazioni|Applicazione|Altri|
|Provisioning degli account|Gestione di applicazioni|Applicazione|Elaborare il deposito|
|Provisioning degli account|Gestione di applicazioni|Applicazione|Azione della regola di sincronizzazione|
|Proxy dell'applicazione|Gestione di applicazioni|Applicazione|Aggiunta di un'applicazione|
|Proxy dell'applicazione|Risorsa|Risorsa|Aggiungere il certificato SSL dell'applicazione|
|Proxy dell'applicazione|Risorsa|Risorsa|Eliminare il binding SSL|
|Proxy dell'applicazione|Gestione di applicazioni|Applicazione|Eliminare applicazione|
|Proxy dell'applicazione|Gestione directory|Directory|Disabilitare l'accesso Single Sign-On del desktop|
|Proxy dell'applicazione|Gestione directory|Directory|Disabilitare l'accesso Single Sign-On del desktop per un dominio specifico|
|Proxy dell'applicazione|Gestione directory|Directory|Disabilitare il proxy di applicazione|
|Proxy dell'applicazione|Gestione directory|Directory|Disabilitare l'autenticazione pass-through|
|Proxy dell'applicazione|Gestione directory|Directory|Abilitare l'accesso Single Sign-On del desktop|
|Proxy dell'applicazione|Gestione directory|Directory|Abilitare l'accesso Single Sign-On del desktop per un dominio specifico|
|Proxy dell'applicazione|Gestione directory|Directory|Abilitare il proxy di applicazione|
|Proxy dell'applicazione|Gestione directory|Directory|Abilitare l'autenticazione pass-through|
|Proxy dell'applicazione|Risorsa|Risorsa|Registrare il connettore|
|Proxy dell'applicazione|Gestione di applicazioni|Applicazione|Aggiornare l'applicazione|
|Proxy dell'applicazione|Gestione di applicazioni|Applicazione|Aggiornare la modalità di accesso Single Sign-On dell'applicazione|
|Automated Password Rollover (Rollover automatizzato delle password)|Gestione di applicazioni|Applicazione|Automated Password Rollover (Rollover automatizzato delle password)|
|B2C|Gestione di applicazioni|Applicazione|Aggiungere autorizzazioni applicazione V2|
|B2C|Authorization|Authorization|Aggiungere autorizzazioni applicazione V2|
|B2C|Chiave|Chiave|Aggiungere una chiave basata sul segreto ASCII a un contenitore di chiavi CPIM|
|B2C|Authorization|Authorization|Aggiungere una chiave basata sul segreto ASCII a un contenitore di chiavi CPIM|
|B2C|Chiave|Chiave|Aggiungere una chiave a un contenitore di chiavi CPIM|
|B2C|Authorization|Authorization|Aggiungere una chiave a un contenitore di chiavi CPIM|
|B2C|Risorsa|Risorsa|AdminPolicyDatas-RemoveResources|
|B2C|Authorization|Authorization|AdminPolicyDatas-SetResources|
|B2C|Risorsa|Risorsa|AdminPolicyDatas-SetResources|
|B2C|Risorsa|Risorsa|AdminUserJourneys-GetResources|
|B2C|Authorization|Authorization|AdminUserJourneys-GetResources|
|B2C|Authorization|Authorization|AdminUserJourneys-RemoveResources|
|B2C|Risorsa|Risorsa|AdminUserJourneys-RemoveResources|
|B2C|Risorsa|Risorsa|AdminUserJourneys-SetResources|
|B2C|Authorization|Authorization|AdminUserJourneys-SetResources|
|B2C|Authorization|Authorization|Creare un provider di identità|
|B2C|Risorsa|Risorsa|Creare un provider di identità|
|B2C|Authorization|Authorization|Creare un'applicazione V1|
|B2C|Gestione di applicazioni|Applicazione|Creare un'applicazione V1|
|B2C|Gestione di applicazioni|Applicazione|Creare un'applicazione V2|
|B2C|Authorization|Authorization|Creare un'applicazione V2|
|B2C|Gestione directory|Directory|Creare un dominio personalizzato nel tenant|
|B2C|Authorization|Authorization|Creare un dominio personalizzato nel tenant|
|B2C|Authorization|Authorization|Crea un nuovo AdminUserJourney|
|B2C|Risorsa|Risorsa|Crea un nuovo AdminUserJourney|
|B2C|Risorsa|Risorsa|Creare JSON di risorse localizzate|
|B2C|Authorization|Authorization|Creare JSON di risorse localizzate|
|B2C|Authorization|Authorization|Creare un nuovo provider di identità personalizzato|
|B2C|Risorsa|Risorsa|Creare un nuovo provider di identità personalizzato|
|B2C|Risorsa|Risorsa|Creare un nuovo provider di identità|
|B2C|Authorization|Authorization|Creare un nuovo provider di identità|
|B2C|Authorization|Authorization|Creare o aggiornare una risorsa della directory B2C|
|B2C|Risorsa|Risorsa|Creare o aggiornare una risorsa della directory B2C|
|B2C|Risorsa|Risorsa|Crea criteri|
|B2C|Authorization|Authorization|Crea criteri|
|B2C|Authorization|Authorization|Creare criteri trustFramework|
|B2C|Risorsa|Risorsa|Creare criteri trustFramework|
|B2C|Authorization|Authorization|Creare criteri trustFramework con prefisso configurabile|
|B2C|Risorsa|Risorsa|Creare criteri trustFramework con prefisso configurabile|
|B2C|Risorsa|Risorsa|Creare un attributo utente|
|B2C|Authorization|Authorization|Creare un attributo utente|
|B2C|Authorization|Authorization|CreateTrustFrameworkPolicy|
|B2C|Risorsa|Risorsa|CreateTrustFrameworkPolicy|
|B2C|Authorization|Authorization|Creare o aggiornare un nuovo AdminUserJourney|
|B2C|Risorsa|Risorsa|Eliminare un provider di identità|
|B2C|Authorization|Authorization|Eliminare un provider di identità|
|B2C|Risorsa|Risorsa|Eliminare un provider di identità|
|B2C|Authorization|Authorization|Eliminare un provider di identità|
|B2C|Gestione di applicazioni|Applicazione|Eliminare un'applicazione V1|
|B2C|Authorization|Authorization|Eliminare un'applicazione V1|
|B2C|Gestione di applicazioni|Applicazione|Eliminare un'applicazione V2|
|B2C|Authorization|Authorization|Eliminare un'applicazione V2|
|B2C|Authorization|Authorization|Eliminare le concessioni di autorizzazione per l'applicazione V2|
|B2C|Gestione di applicazioni|Applicazione|Eliminare le concessioni di autorizzazione per l'applicazione V2|
|B2C|Risorsa|Risorsa|Eliminare una risorsa della directory B2C|
|B2C|Authorization|Authorization|Eliminare una risorsa della directory B2C|
|B2C|Chiave|Chiave|Eliminare un contenitore di chiavi CPIM|
|B2C|Authorization|Authorization|Eliminare un contenitore di chiavi CPIM|
|B2C|Chiave|Chiave|Eliminare un contenitore di chiavi|
|B2C|Risorsa|Risorsa|Eliminare criteri trustFramework|
|B2C|Authorization|Authorization|Eliminare criteri trustFramework|
|B2C|Risorsa|Risorsa|Eliminare un attributo utente|
|B2C|Authorization|Authorization|Eliminare un attributo utente|
|B2C|Authorization|Authorization|Abilitare la funzionalità B2C|
|B2C|Gestione directory|Directory|Abilitare la funzionalità B2C|
|B2C|Risorsa|Risorsa|Ottenere le risorse di directory B2C in un gruppo di risorse|
|B2C|Risorsa|Risorsa|Ottenere le risorse di directory B2C in una sottoscrizione|
|B2C|Authorization|Authorization|Ottenere le risorse di directory B2C in una sottoscrizione|
|B2C|Authorization|Authorization|Ottenere un provider di identità personalizzato|
|B2C|Risorsa|Risorsa|Ottenere un provider di identità personalizzato|
|B2C|Risorsa|Risorsa|Ottenere un provider di identità|
|B2C|Authorization|Authorization|Ottenere un provider di identità|
|B2C|Authorization|Authorization|Ottenere applicazioni V1 e V2|
|B2C|Gestione di applicazioni|Applicazione|Ottenere applicazioni V1 e V2|
|B2C|Authorization|Authorization|Ottenere applicazione V1|
|B2C|Gestione di applicazioni|Applicazione|Ottenere applicazione V1|
|B2C|Authorization|Authorization|Ottenere applicazioni V1|
|B2C|Gestione di applicazioni|Applicazione|Ottenere applicazioni V1|
|B2C|Gestione di applicazioni|Applicazione|Ottenere applicazione V2|
|B2C|Authorization|Authorization|Ottenere applicazione V2|
|B2C|Gestione di applicazioni|Applicazione|Ottenere applicazioni V2|
|B2C|Authorization|Authorization|Ottenere applicazioni V2|
|B2C|Risorsa|Risorsa|Ottenere una risorsa della directory B2C|
|B2C|Authorization|Authorization|Ottenere una risorsa della directory B2C|
|B2C|Authorization|Authorization|Ottenere un elenco di domini personalizzati nel tenant|
|B2C|Gestione directory|Directory|Ottenere un elenco di domini personalizzati nel tenant|
|B2C|Authorization|Authorization|Ottenere un percorso utente|
|B2C|Risorsa|Risorsa|Ottenere un percorso utente|
|B2C|Risorsa|Risorsa|Ottenere le attestazioni dell'applicazione per il percorso utente|
|B2C|Authorization|Authorization|Ottenere le attestazioni dell'applicazione per il percorso utente|
|B2C|Risorsa|Risorsa|Ottenere le attestazioni autocertificate consentite per il percorso utente|
|B2C|Authorization|Authorization|Ottenere le attestazioni autocertificate consentite per il percorso utente|
|B2C|Risorsa|Risorsa|Ottenere le attestazioni autocertificate consentite dei criteri|
|B2C|Authorization|Authorization|Ottenere le attestazioni autocertificate consentite dei criteri|
|B2C|Risorsa|Risorsa|Ottenere l'elenco di attestazioni di output disponibili|
|B2C|Authorization|Authorization|Ottenere l'elenco di attestazioni di output disponibili|
|B2C|Risorsa|Risorsa|Ottenere le definizioni del contenuto per il percorso utente|
|B2C|Authorization|Authorization|Ottenere le definizioni del contenuto per il percorso utente|
|B2C|Authorization|Authorization|Ottenere i provider di identità per un flusso di amministrazione specifico|
|B2C|Risorsa|Risorsa|Ottenere i provider di identità per un flusso di amministrazione specifico|
|B2C|Chiave|Chiave|Ottenere i metadati di chiave attivi del contenitore di chiavi in JWK|
|B2C|Authorization|Authorization|Ottenere i metadati di chiave attivi del contenitore di chiavi in JWK|
|B2C|Chiave|Chiave|Ottenere i metadati del contenitore di chiavi|
|B2C|Risorsa|Risorsa|Ottenere l'elenco di tutti i flussi di amministrazione|
|B2C|Authorization|Authorization|Ottenere l'elenco di tutti i flussi di amministrazione|
|B2C|Authorization|Authorization|Ottenere l'elenco di tag per tutti i flussi di amministratore per tutti gli utenti|
|B2C|Risorsa|Risorsa|Ottenere l'elenco di tag per tutti i flussi di amministratore per tutti gli utenti|
|B2C|Risorsa|Risorsa|Ottenere l'elenco di tenant per un utente|
|B2C|Authorization|Authorization|Ottenere l'elenco di tenant per un utente|
|B2C|Risorsa|Risorsa|Ottenere le attestazioni autocertificate degli account locali|
|B2C|Authorization|Authorization|Ottenere le attestazioni autocertificate degli account locali|
|B2C|Risorsa|Risorsa|Ottenere JSON di risorse localizzate|
|B2C|Authorization|Authorization|Ottenere JSON di risorse localizzate|
|B2C|Authorization|Authorization|Ottenere le operazioni del provider di risorse Microsoft.AzureActiveDirectory|
|B2C|Risorsa|Risorsa|Ottenere le operazioni del provider di risorse Microsoft.AzureActiveDirectory|
|B2C|Risorsa|Risorsa|Ottenere criteri|
|B2C|Authorization|Authorization|Ottenere criteri|
|B2C|Risorsa|Risorsa|Ottenere un criterio|
|B2C|Authorization|Authorization|Ottenere un criterio|
|B2C|Gestione directory|Directory|Ottenere le proprietà delle risorse di un tenant|
|B2C|Authorization|Authorization|Ottenere le proprietà delle risorse di un tenant|
|B2C|Risorsa|Risorsa|Ottenere l'elenco di provider di identità supportati|
|B2C|Authorization|Authorization|Ottenere l'elenco di provider di identità supportati|
|B2C|Risorsa|Risorsa|Ottenere l'elenco di provider di identità supportati del percorso utente|
|B2C|Authorization|Authorization|Ottenere l'elenco di provider di identità supportati del percorso utente|
|B2C|Gestione directory|Directory|Ottenere le informazioni del tenant|
|B2C|Authorization|Authorization|Ottenere le informazioni del tenant|
|B2C|Gestione directory|Directory|Ottenere le funzionalità consentite del tenant|
|B2C|Authorization|Authorization|Ottenere le funzionalità consentite del tenant|
|B2C|Authorization|Authorization|Ottenere un elenco di provider di identità personalizzati definiti dal tenant|
|B2C|Risorsa|Risorsa|Ottenere un elenco di provider di identità personalizzati definiti dal tenant|
|B2C|Risorsa|Risorsa|Ottenere un elenco di provider di identità definiti dal tenant|
|B2C|Authorization|Authorization|Ottenere un elenco di provider di identità definiti dal tenant|
|B2C|Risorsa|Risorsa|Ottenere un elenco di provider di identità locali definiti dal tenant|
|B2C|Authorization|Authorization|Ottenere un elenco di provider di identità locali definiti dal tenant|
|B2C|Risorsa|Risorsa|Ottenere i dettagli del tenant per un utente per la creazione di risorse|
|B2C|Authorization|Authorization|Ottenere i dettagli del tenant per un utente per la creazione di risorse|
|B2C|Authorization|Authorization|Ottenere l'elenco di tenant|
|B2C|Authorization|Authorization|Ottenere tenantDomains|
|B2C|Gestione directory|Directory|Ottenere tenantDomains|
|B2C|Risorsa|Risorsa|Ottenere le impostazioni cultura supportate predefinite per CPIM|
|B2C|Authorization|Authorization|Ottenere le impostazioni cultura supportate predefinite per CPIM|
|B2C|Risorsa|Risorsa|Ottenere i dettagli di un flusso di amministrazione|
|B2C|Authorization|Authorization|Ottenere i dettagli di un flusso di amministrazione|
|B2C|Authorization|Authorization|Ottenere l'elenco di percorsi utente per questo tenant|
|B2C|Risorsa|Risorsa|Ottenere l'elenco di percorsi utente per questo tenant|
|B2C|Authorization|Authorization|Ottenere il set di impostazioni cultura supportate disponibili per CPIM|
|B2C|Risorsa|Risorsa|Ottenere il set di impostazioni cultura supportate disponibili per CPIM|
|B2C|Authorization|Authorization|Ottenere il criterio trustFramework|
|B2C|Risorsa|Risorsa|Ottenere il criterio trustFramework|
|B2C|Authorization|Authorization|Ottenere il criterio trustFramework in formato XML|
|B2C|Risorsa|Risorsa|Ottenere il criterio trustFramework in formato XML|
|B2C|Risorsa|Risorsa|Ottenere l'attributo utente|
|B2C|Authorization|Authorization|Ottenere l'attributo utente|
|B2C|Authorization|Authorization|Ottenere gli attributi utente|
|B2C|Risorsa|Risorsa|Ottenere gli attributi utente|
|B2C|Authorization|Authorization|Ottenere un elenco di percorsi utente|
|B2C|Risorsa|Risorsa|Ottenere un elenco di percorsi utente|
|B2C|Authorization|Authorization|GetIEFPolicies|
|B2C|Risorsa|Risorsa|GetIEFPolicies|
|B2C|Authorization|Authorization|GetIdentityProviders|
|B2C|Risorsa|Risorsa|GetIdentityProviders|
|B2C|Risorsa|Risorsa|GetTrustFrameworkPolicy|
|B2C|Authorization|Authorization|GetTrustFrameworkPolicy|
|B2C|Chiave|Chiave|Ottiene un contenitore di chiavi CPIM in formato JWK|
|B2C|Authorization|Authorization|Ottiene un contenitore di chiavi CPIM in formato JWK|
|B2C|Chiave|Chiave|Ottiene un elenco di contenitori di chiavi nel tenant|
|B2C|Authorization|Authorization|Ottiene un elenco di contenitori di chiavi nel tenant|
|B2C|Authorization|Authorization|Ottiene il tipo di tenant|
|B2C|Gestione directory|Directory|Ottiene il tipo di tenant|
|B2C|Authentication|Authentication|Emettere un token di accesso per l'applicazione|
|B2C|Authentication|Authentication|Emettere un codice di autorizzazione per l'applicazione|
|B2C|Altri|Altri|Emettere un codice di autorizzazione per l'applicazione|
|B2C|Authentication|Authentication|Emettere un id_token per l'applicazione|
|B2C|Altri|Altri|Emettere un id_token per l'applicazione|
|B2C|Authorization|Authorization|MigrateTenantMetadata|
|B2C|Risorsa|Risorsa|MigrateTenantMetadata|
|B2C|Risorsa|Risorsa|Spostare le risorse|
|B2C|Authorization|Authorization|Applicare patch a un provider di identità|
|B2C|Risorsa|Risorsa|Applicare patch a un provider di identità|
|B2C|Risorsa|Risorsa|PutTrustFrameworkPolicy|
|B2C|Authorization|Authorization|PutTrustFrameworkPolicy|
|B2C|Authorization|Authorization|PutTrustFrameworkpolicy|
|B2C|Risorsa|Risorsa|PutTrustFrameworkpolicy|
|B2C|Risorsa|Risorsa|Rimuovere un percorso utente|
|B2C|Authorization|Authorization|Rimuovere un percorso utente|
|B2C|Authorization|Authorization|Ripristinare un backup del contenitore di chiavi CPIM|
|B2C|Chiave|Chiave|Ripristinare un backup del contenitore di chiavi CPIM|
|B2C|Gestione di applicazioni|Applicazione|Recuperare le concessioni di autorizzazione per l'applicazione V2|
|B2C|Authorization|Authorization|Recuperare le concessioni di autorizzazione per l'applicazione V2|
|B2C|Gestione di applicazioni|Applicazione|Recuperare le entità servizio dell'applicazione V2 nel tenant corrente|
|B2C|Authorization|Authorization|Recuperare le entità servizio dell'applicazione V2 nel tenant corrente|
|B2C|Chiave|Chiave|Salvare un contenitore di chiavi|
|B2C|Authorization|Authorization|Aggiornare un provider di identità personalizzato|
|B2C|Risorsa|Risorsa|Aggiornare un provider di identità personalizzato|
|B2C|Risorsa|Risorsa|Aggiornare un provider di identità|
|B2C|Authorization|Authorization|Aggiornare un provider di identità|
|B2C|Risorsa|Risorsa|Aggiornare un provider di identità locale|
|B2C|Authorization|Authorization|Aggiornare un provider di identità locale|
|B2C|Gestione di applicazioni|Applicazione|Aggiornare l'applicazione V1|
|B2C|Authorization|Authorization|Aggiornare l'applicazione V1|
|B2C|Gestione di applicazioni|Applicazione|Aggiornare l'applicazione V2|
|B2C|Authorization|Authorization|Aggiornare l'applicazione V2|
|B2C|Gestione di applicazioni|Applicazione|Aggiornare le concessioni di autorizzazione per l'applicazione V2|
|B2C|Authorization|Authorization|Aggiornare le concessioni di autorizzazione per l'applicazione V2|
|B2C|Risorsa|Risorsa|Aggiornare una risorsa della directory B2C|
|B2C|Risorsa|Risorsa|Aggiorna criteri|
|B2C|Authorization|Authorization|Aggiorna criteri|
|B2C|Risorsa|Risorsa|Aggiornare lo stato della sottoscrizione|
|B2C|Risorsa|Risorsa|Aggiornare l'attributo utente|
|B2C|Authorization|Authorization|Aggiornare l'attributo utente|
|B2C|Chiave|Chiave|Caricare una chiave crittografata CPIM|
|B2C|Authorization|Authorization|Caricare una chiave crittografata CPIM|
|B2C|Authorization|Authorization|Autorizzazione utente: l'API è disabilitata per il set di funzionalità del tenant|
|B2C|Authorization|Authorization|Autorizzazione utente: accesso concesso all'utente come 'Amministratore tenant'|
|B2C|Authorization|Authorization|Autorizzazione utente: all'utente sono stati concessi diritti di accesso 'Utenti autenticati'|
|B2C|Authentication|Authentication|Convalidare le credenziali dell'account utente locale|
|B2C|Risorsa|Risorsa|Convalidare lo spostamento di risorse|
|B2C|Authentication|Authentication|Convalidare l'autenticazione utente|
|B2C|Gestione directory|Directory|Verificare se la funzionalità B2C è abilitata|
|B2C|Authorization|Authorization|Verificare se la funzionalità B2C è abilitata|
|B2C|Authorization|Authorization|Verificare se la funzionalità è abilitata|
|B2C|Gestione directory|Directory|Verificare se la funzionalità è abilitata|
|Directory principale|Gestione di applicazioni|Applicazione|Aggiungere OAuth2PermissionGrant|
|Directory principale|Gestione delle unità amministrative|AdministrativeUnit|Aggiungere un'unità amministrativa|
|Directory principale|User Management|Utente|Aggiungere un'assegnazione di ruolo app a un utente|
|Directory principale|Gestione di gruppi|Group|Aggiungere un'assegnazione di ruolo app a un gruppo|
|Directory principale|Gestione di applicazioni|Applicazione|Aggiungere un'assegnazione di ruolo app a un'entità servizio|
|Directory principale|Gestione di applicazioni|Applicazione|Aggiunta di un'applicazione|
|Directory principale|Risorsa|Risorsa|Aggiungere un dispositivo|
|Directory principale|Risorsa|Risorsa|Aggiungere una configurazione del dispositivo|
|Directory principale|Gestione dei ruoli|Ruolo|Aggiungere un membro idoneo al ruolo|
|Directory principale|Gestione di gruppi|Group|Aggiungi gruppo|
|Directory principale|Gestione delle unità amministrative|AdministrativeUnit|Aggiungere un membro all'unità amministrativa|
|Directory principale|Gestione di gruppi|Group|Aggiungere un membro al gruppo|
|Directory principale|Gestione dei ruoli|Ruolo|Aggiungere un membro a un ruolo|
|Directory principale|Gestione di applicazioni|Applicazione|Aggiungere un proprietario a un'applicazione|
|Directory principale|Gestione di gruppi|Group|Aggiungere un proprietario a un gruppo|
|Directory principale|Gestione dei criteri|Criterio|Aggiungere un proprietario a un criterio|
|Directory principale|Gestione di applicazioni|Applicazione|Aggiungere un proprietario a un'entità servizio|
|Directory principale|Gestione directory|Directory|Aggiunta di un partner alla società|
|Directory principale|Gestione dei criteri|Criterio|Aggiungi criteri|
|Directory principale|Gestione di applicazioni|Applicazione|Aggiungere un criterio a un'entità servizio|
|Directory principale|Risorsa|Risorsa|Aggiungere un proprietario registrato a un dispositivo|
|Directory principale|Risorsa|Risorsa|Aggiungere utenti registrati a un dispositivo|
|Directory principale|Gestione dei ruoli|Ruolo|Aggiungere l'assegnazione di ruolo alla definizione del ruolo|
|Directory principale|Gestione dei ruoli|Ruolo|Aggiungere un ruolo da un modello|
|Directory principale|Gestione dei ruoli|Ruolo|Aggiungere un membro con ambito a un ruolo|
|Directory principale|Gestione di applicazioni|Applicazione|Aggiunta di un'entità servizio|
|Directory principale|Gestione di applicazioni|Applicazione|Aggiunta delle credenziali dell'entità servizio|
|Directory principale|Gestione directory|Directory|Aggiungere un dominio non verificato|
|Directory principale|User Management|Utente|Add user|
|Directory principale|User Management|Utente|Aggiungere dettagli dell'app telefonica per l'autenticazione avanzata degli utenti|
|Directory principale|Gestione directory|Directory|Aggiungere un dominio verificato|
|Directory principale|User Management|Utente|Modifica licenza utente|
|Directory principale|User Management|Utente|Modifica password utente|
|Directory principale|Gestione di applicazioni|Applicazione|Dare il consenso a un'applicazione|
|Directory principale|User Management|Utente|Convertire un utente federato in utente gestito|
|Directory principale|User Management|Utente|Creare una password applicazione per un utente|
|Directory principale|Gestione directory|Directory|Creare un'azienda|
|Directory principale|Gestione directory|Directory|Vengono create le impostazioni aziendali.|
|Directory principale|Gestione di gruppi|Group|Creare le impostazioni dei gruppi|
|Directory principale|Gestione delle unità amministrative|AdministrativeUnit|Eliminare un'unità amministrativa|
|Directory principale|Gestione di applicazioni|Applicazione|Eliminare applicazione|
|Directory principale|User Management|Utente|Eliminare una password applicazione per un utente|
|Directory principale|Gestione directory|Directory|Vengono eliminate le impostazioni aziendali.|
|Directory principale|Risorsa|Risorsa|Eliminare un dispositivo|
|Directory principale|Risorsa|Risorsa|Eliminazione della configurazione del dispositivo|
|Directory principale|Gestione di gruppi|Group|Eliminare gruppo|
|Directory principale|Gestione di gruppi|Group|Eliminare le impostazioni dei gruppi|
|Directory principale|Gestione dei criteri|Criterio|Elimina criteri|
|Directory principale|User Management|Utente|Eliminare un utente|
|Directory principale|Gestione directory|Directory|Abbassare un partner di livello|
|Directory principale|Risorsa|Risorsa|Dispositivo non più conforme|
|Directory principale|Risorsa|Risorsa|Dispositivo non più gestito|
|Directory principale|Gestione directory|Directory|Directory eliminata|
|Directory principale|Gestione directory|Directory|Directory eliminata in modo permanente|
|Directory principale|Gestione directory|Directory|Directory pianificata per l'eliminazione|
|Directory principale|User Management|Utente|Disabilitare l'account|
|Directory principale|User Management|Utente|Abilitare l'autenticazione avanzata|
|Directory principale|Gestione di gruppi|Group|Terminare l'applicazione di licenze basate sui gruppi agli utenti|
|Directory principale|Gestione di applicazioni|Applicazione|Eliminare definitivamente un'applicazione|
|Directory principale|Gestione di gruppi|Group|Eliminare definitivamente un gruppo|
|Directory principale|User Management|Utente|Eliminare definitivamente un utente|
|Directory principale|Gestione directory|Directory|Innalzare un'azienda a livello di partner|
|Directory principale|Gestione directory|Directory|Ripulire le proprietà di Rights Management|
|Directory principale|Gestione di applicazioni|Applicazione|Rimuovere OAuth2PermissionGrant|
|Directory principale|Gestione di gruppi|Group|Rimuovere un'assegnazione di ruolo app da un gruppo|
|Directory principale|Gestione di applicazioni|Applicazione|Rimuovere un'assegnazione di ruolo app da un'entità servizio|
|Directory principale|User Management|Utente|Rimuovere un'assegnazione di ruolo app da un utente|
|Directory principale|Gestione dei ruoli|Ruolo|Rimuovere un membro idoneo da un ruolo|
|Directory principale|Gestione delle unità amministrative|AdministrativeUnit|Rimuovere un membro da un'unità amministrativa|
|Directory principale|Gestione di gruppi|Group|Rimuovere un membro dal gruppo|
|Directory principale|Gestione dei ruoli|Ruolo|Rimuovere un membro da un ruolo|
|Directory principale|Gestione di applicazioni|Applicazione|Rimuovere il proprietario da un'applicazione|
|Directory principale|Gestione di gruppi|Group|Rimuovere il proprietario da un gruppo|
|Directory principale|Gestione di applicazioni|Applicazione|Rimuovere il proprietario da un'entità servizio|
|Directory principale|Gestione directory|Directory|Rimuovere un partner dalla società|
|Directory principale|Gestione dei criteri|Criterio|Rimuovere le credenziali dei criteri|
|Directory principale|Gestione di applicazioni|Applicazione|Rimuovere i criteri da un'entità servizio|
|Directory principale|Risorsa|Risorsa|Rimuovere il proprietario registrato da un dispositivo|
|Directory principale|Risorsa|Risorsa|Rimuovere utenti registrati da un dispositivo|
|Directory principale|Gestione dei ruoli|Ruolo|Rimuovere l'assegnazione di ruolo dalla definizione del ruolo|
|Directory principale|Gestione dei ruoli|Ruolo|Rimuovere un membro con ambito da un ruolo|
|Directory principale|Gestione di applicazioni|Applicazione|Rimozione di un'entità servizio|
|Directory principale|Gestione di applicazioni|Applicazione|Rimozione delle credenziali dell'entità servizio|
|Directory principale|Gestione directory|Directory|Rimuovere un dominio non verificato|
|Directory principale|User Management|Utente|Rimuovere dettagli dell'app telefonica per l'autenticazione avanzata degli utenti|
|Directory principale|Gestione directory|Directory|Rimuovere un dominio verificato|
|Directory principale|User Management|Utente|Reimpostazione password utente|
|Directory principale|Gestione di gruppi|Group|Ripristinare un gruppo|
|Directory principale|Gestione di applicazioni|Applicazione|Ripristinare l'applicazione|
|Directory principale|User Management|Utente|Ripristinare un utente|
|Directory principale|Gestione di applicazioni|Applicazione|Revocare il consenso|
|Directory principale|Gestione directory|Directory|Impostazione informazioni società|
|Directory principale|Gestione directory|Directory|Impostare la funzionalità DirSync|
|Directory principale|Gestione directory|Directory|Impostare il flag DirSyncEnabled|
|Directory principale|Gestione directory|Directory|Configurare una relazione|
|Directory principale|Gestione directory|Directory|Configurare una soglia di eliminazione accidentale|
|Directory principale|Gestione directory|Directory|Configurare la posizione dei dati consentita per l'azienda|
|Directory principale|Gestione directory|Directory|Abilitare la funzionalità multinazionale per l'azienda|
|Directory principale|Gestione directory|Directory|Impostare una funzionalità directory nel tenant|
|Directory principale|Gestione directory|Directory|Impostazione dell'autenticazione del dominio|
|Directory principale|Gestione directory|Directory|Configurazione delle impostazioni di federazione nel dominio|
|Directory principale|User Management|Utente|Impostazione forzatura per la modifica delle password utente|
|Directory principale|Gestione di gruppi|Group|Configurare la licenza di un gruppo|
|Directory principale|Gestione di gruppi|Group|È stato configurato il gruppo che deve essere gestito dall'utente.|
|Directory principale|Gestione directory|Directory|Impostare il criterio password|
|Directory principale|Gestione directory|Directory|Impostare le proprietà di Rights Management|
|Directory principale|User Management|Utente|Impostare la gestione utenti|
|Directory principale|User Management|Utente|Abilitare i metadati del token OATH degli utenti|
|Directory principale|Gestione di gruppi|Group|Avviare l'applicazione di licenze basate sui gruppi agli utenti|
|Directory principale|Gestione di gruppi|Group|Attivare il ricalcolo della licenza del gruppo|
|Directory principale|User Management|Utente|Aggiornare il timestamp StsRefreshTokenValidFrom|
|Directory principale|Gestione delle unità amministrative|AdministrativeUnit|Aggiornare un'unità amministrativa|
|Directory principale|Gestione di applicazioni|Applicazione|Aggiornare l'applicazione|
|Directory principale|Gestione directory|Directory|Aggiornare l'azienda|
|Directory principale|Gestione directory|Directory|Aggiornare le impostazioni aziendali|
|Directory principale|Risorsa|Risorsa|Aggiornare un dispositivo|
|Directory principale|Risorsa|Risorsa|Aggiornare la configurazione di un dispositivo|
|Directory principale|Gestione directory|Directory|Aggiornamento dominio|
|Directory principale|User Management|Utente|Vengono aggiornati i segreti esterni.|
|Directory principale|Gestione di applicazioni|Applicazione|Vengono aggiornati i segreti esterni.|
|Directory principale|Gestione di gruppi|Group|Aggiornare un gruppo|
|Directory principale|Gestione di gruppi|Group|Aggiornare le impostazioni dei gruppi|
|Directory principale|Gestione dei criteri|Criterio|Aggiorna criteri|
|Directory principale|Gestione dei ruoli|Ruolo|Aggiornare un ruolo|
|Directory principale|Gestione di applicazioni|Applicazione|Aggiornare un'entità servizio|
|Directory principale|User Management|Utente|Aggiornamento utente|
|Directory principale|Gestione directory|Directory|Verifica dominio|
|Directory principale|Gestione directory|Directory|Verifica del dominio tramite la verifica di posta elettronica|
|Identity Protection|User Management|Utente|L'amministrazione genera una password temporanea|
|Identity Protection|User Management|Utente|L'amministratore richiede all'utente di reimpostare la password|
|Identity Protection|Altri|Altri|Scaricare un singolo tipo di evento di rischio|
|Identity Protection|Altri|Altri|Scaricare amministratori e stato del consenso esplicito per il digest settimanale|
|Identity Protection|Altri|Altri|Scaricare tutti i tipi di eventi di rischio|
|Identity Protection|Altri|Altri|Scaricare gli eventi di rischio degli utenti del piano gratuito|
|Identity Protection|Altri|Altri|Scaricare gli utenti contrassegnati per il rischio|
|Identity Protection|Gestione directory|Directory|Onboarding|
|Identity Protection|Gestione dei criteri|Criterio|Impostare i criteri della registrazione MFA|
|Identity Protection|Gestione dei criteri|Criterio|Impostare i criteri di rischio di accesso|
|Identity Protection|Gestione dei criteri|Criterio|Impostare i criteri di rischio utente|
|Identity Protection|Gestione directory|Directory|Aggiornare le impostazioni dell'avviso|
|Identity Protection|Gestione directory|Directory|Aggiornare le impostazioni del digest settimanale|
|Utenti invitati|User Management|Utente|Assegnare utenti esterni all'applicazione|
|Utenti invitati|Altri|Altri|Inviti batch elaborati|
|Utenti invitati|Altri|Altri|Inviti batch caricati|
|Utenti invitati|User Management|Utente|Messaggio di posta elettronica non inviato, l'utente ha annullato la sottoscrizione|
|Utenti invitati|User Management|Utente|Invitare utente esterno|
|Utenti invitati|User Management|Utente|Riscattare un invito per utente esterno|
|Utenti invitati|User Management|Utente|Creazione del tenant virale|
|Utenti invitati|User Management|Utente|Creazione dell'utente virale|
|Microsoft Identity Manager (MIM)|Gestione di gruppi|Group|Aggiungere un membro|
|Microsoft Identity Manager (MIM)|Gestione di gruppi|Group|Creare un gruppo|
|Microsoft Identity Manager (MIM)|Gestione di gruppi|Group|Eliminare un gruppo|
|Microsoft Identity Manager (MIM)|Gestione di gruppi|Group|Rimuovere un membro|
|Microsoft Identity Manager (MIM)|Gestione di gruppi|Group|Aggiornare un gruppo|
|Microsoft Identity Manager (MIM)|User Management|Utente|Registrazione della password utente|
|Microsoft Identity Manager (MIM)|User Management|Utente|Reimpostazione della password utente|
|Privileged Identity Management|Gestione dei ruoli|Ruolo|AccessReview_Review|
|Privileged Identity Management|Gestione dei ruoli|Ruolo|AccessReview_Update|
|Privileged Identity Management|Gestione dei ruoli|Ruolo|ActivationAborted|
|Privileged Identity Management|Gestione dei ruoli|Ruolo|ActivationApproved|
|Privileged Identity Management|Gestione dei ruoli|Ruolo|ActivationCanceled|
|Privileged Identity Management|Gestione dei ruoli|Ruolo|ActivationRequested|
|Privileged Identity Management|Gestione dei ruoli|Ruolo|Aggiunto|
|Privileged Identity Management|Gestione dei ruoli|Ruolo|Assegnare|
|Privileged Identity Management|Gestione dei ruoli|Ruolo|Elevare|
|Privileged Identity Management|Gestione dei ruoli|Ruolo|Rimosso|
|Privileged Identity Management|Gestione dei ruoli|Ruolo|Modifiche alle impostazioni dei ruoli|
|Privileged Identity Management|Gestione dei ruoli|Ruolo|ScanAlertsNow|
|Privileged Identity Management|Gestione dei ruoli|Ruolo|Iscrizione|
|Privileged Identity Management|Gestione dei ruoli|Ruolo|Annullare elevazione|
|Privileged Identity Management|Gestione dei ruoli|Ruolo|UpdateAlertSettings|
|Privileged Identity Management|Gestione dei ruoli|Ruolo|UpdateCurrentState|
|Gestione gruppi self-service|Gestione di gruppi|Group|Approvare una richiesta di partecipazione a un gruppo in sospeso|
|Gestione gruppi self-service|Gestione di gruppi|Group|Annullare una richiesta di partecipazione a un gruppo in sospeso|
|Gestione gruppi self-service|Gestione di gruppi|Group|Creare criteri di gestione del ciclo di vita|
|Gestione gruppi self-service|Gestione di gruppi|Group|Eliminare una richiesta di partecipazione a un gruppo in sospeso|
|Gestione gruppi self-service|Gestione di gruppi|Group|Rifiutare una richiesta di partecipazione a un gruppo in sospeso|
|Gestione gruppi self-service|Gestione di gruppi|Group|Rinnovare un gruppo|
|Gestione gruppi self-service|Gestione di gruppi|Group|Richiedere di partecipare a un gruppo|
|Gestione gruppi self-service|Gestione di gruppi|Group|Impostare proprietà di gruppi dinamici|
|Gestione gruppi self-service|Gestione di gruppi|Group|Aggiornare criteri di gestione del ciclo di vita|
|Gestione delle password self-service|User Management|Utente|Bloccato dalla reimpostazione password self-service|
|Gestione delle password self-service|User Management|Utente|Modificare la password (self-service)|
|Gestione delle password self-service|Gestione directory|Directory|Disabilitare il writeback delle password per la directory|
|Gestione delle password self-service|Gestione directory|Directory|Abilitare il writeback delle password per la directory|
|Gestione delle password self-service|User Management|Utente|Reimpostare la password (amministratore)|
|Gestione delle password self-service|User Management|Utente|Reimpostare la password (self-service)|
|Gestione delle password self-service|User Management|Utente|Stato dell'attività di reimpostazione delle password self-service|
|Gestione delle password self-service|User Management|Utente|Stato dell'attività di reimpostazione delle password self-service|
|Gestione delle password self-service|User Management|Utente|Sbloccare l'account utente (self-service)|
|Gestione delle password self-service|User Management|Utente|Utente registrato per la reimpostazione della password self-service|
|Condizioni per l'utilizzo|Gestione dei criteri|Criterio|Accettare le condizioni per l'utilizzo|
|Condizioni per l'utilizzo|Gestione dei criteri|Criterio|Creare le condizioni per l'utilizzo|
|Condizioni per l'utilizzo|Gestione dei criteri|Criterio|Rifiutare le condizioni per l'utilizzo|
|Condizioni per l'utilizzo|Gestione dei criteri|Criterio|Eliminare le condizioni per l'utilizzo|
|Condizioni per l'utilizzo|Gestione dei criteri|Criterio|Modificare le condizioni per l'utilizzo|
|Condizioni per l'utilizzo|Gestione dei criteri|Criterio|Pubblicare le condizioni per l'utilizzo|
|Condizioni per l'utilizzo|Gestione dei criteri|Criterio|Annullare la pubblicazione delle condizioni per l'utilizzo|




## <a name="next-steps"></a>Passaggi successivi

Per una panoramica della creazione di report, vedere [Creazione di report in Azure Active Directory](active-directory-reporting-azure-portal.md).

