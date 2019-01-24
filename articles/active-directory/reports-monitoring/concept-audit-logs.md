---
title: Report delle attività di controllo nel portale di Azure Active Directory | Microsoft Docs
description: Introduzione ai report delle attività di controllo nel portale di Azure Active Directory
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 37c733a595285311b05bb420697c190ecabedc9b
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54811520"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Report delle attività di controllo nel portale di Azure Active Directory 

I report di Azure Active Directory (Azure AD) offrono tutte le informazioni necessarie per determinare le prestazioni dell'ambiente.

L'architettura di report è costituita dai componenti seguenti:

- **Attività** 
    - **Accessi**: il [report degli accessi](concept-sign-ins.md) fornisce informazioni sull'uso delle applicazioni gestite e sulle attività di accesso degli utenti.
    - **Log di controllo**: i log consentono la tracciabilità di tutte le modifiche apportate da varie funzionalità all'interno di Azure AD. I log di controllo registrano, ad esempio, le modifiche apportate a qualsiasi risorsa di Azure AD, ad esempio l'aggiunta o la rimozione di utenti, app, gruppi, ruoli e criteri.
- **Sicurezza** 
    - **Accessi a rischio**: un [accesso a rischio](concept-risky-sign-ins.md) indica un tentativo di accesso che potrebbe essere stato eseguito da qualcuno che non è il legittimo proprietario di un account utente. 
    - **Utenti contrassegnati per il rischio**: un [utente a rischio](concept-user-at-risk.md) indica un account utente che potrebbe essere stato compromesso.

Questo articolo fornisce una panoramica del report di controllo.
 
## <a name="who-can-access-the-data"></a>Chi può accedere ai dati?

* Utenti con il ruolo di **Amministratore della sicurezza** o **Amministratore globale** oppure con un **ruolo con autorizzazioni di lettura per la sicurezza**
* Inoltre, tutti gli utenti (non amministratori) possono visualizzare le proprie attività di controllo

## <a name="audit-logs"></a>Log di controllo

I log di controllo di Azure AD forniscono i record delle attività di sistema per la conformità. Per accedere ai log di controllo, selezionare **Log di controllo** nella sezione **Attività** di **Azure Active Directory**. Si noti che i log di controllo possono avere una latenza fino a un'ora, pertanto la visualizzazione dei dati di attività di controllo nel portale potrebbe richiedere un tempo di attesa simile dopo aver completato l'attività.

![Log di controllo](./media/concept-audit-logs/61.png "Log di controllo")

Un log di controllo è una visualizzazione elenco predefinita che include:

- Data e ora dell'occorrenza.
- Iniziatore o attore di un'attività (*chi*) 
- Attività (*cosa*) 
- Destinazione

![Log di controllo](./media/concept-audit-logs/18.png "Log di controllo")

Per personalizzare la visualizzazione elenco, fare clic su **Colonne** nella barra degli strumenti.

![Log di controllo](./media/concept-audit-logs/19.png "Log di controllo")

In questo modo è possibile visualizzare campi aggiuntivi o rimuovere campi già visualizzati.

![Log di controllo](./media/concept-audit-logs/21.png "Log di controllo")

Selezionare un elemento nella visualizzazione elenco per ottenere maggiori informazioni dettagliate.

![Log di controllo](./media/concept-audit-logs/22.png "Log di controllo")


## <a name="filtering-audit-logs"></a>Filtro dei log di controllo

È possibile filtrare i dati di controllo in base ai campi seguenti:

- Intervallo di date
- Azione avviata da (attore)
- Categoria
- Activity resource type (Tipo di risorsa dell'attività)
- Attività

![Log di controllo](./media/concept-audit-logs/23.png "Log di controllo")

Il filtro **Intervallo di date** permette di definire un intervallo di tempo per i dati restituiti.  
I valori possibili sono:

- 1 mese
- 7 giorni
- 24 ore
- Personalizzate

Quando si seleziona un intervallo di tempo personalizzato, è possibile configurare un'ora di inizio e un'ora di fine.

Il filtro **Avviato da** permette di definire un nome di attore o un nome UPN (Universal Principal Name).

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
- Gruppo
- Directory
- Utente
- Applicazione
- Policy
- Dispositivo
- Altri

Quando si seleziona **Gruppo** come **Tipo di risorsa attività**, si ottiene una categoria di filtro aggiuntiva che permette di specificare anche un'**Origine**:

- Azure AD
- O365


Il filtro **Attività** si basa sulla categoria e sul tipo di risorsa attività selezionati. È possibile selezionare un'attività specifica da visualizzare o selezionarle tutte. 

È possibile ottenere l'elenco di tutte le attività di controllo usando l'API Graph https://graph.windows.net/$tenantdomain/activities/auditActivityTypes?api-version=beta, dove $tenantdomain è il nome del dominio. In alternativa, vedere l'articolo relativo agli [eventi del report di controllo](reference-audit-activities.md).

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

![Log di controllo](./media/concept-audit-logs/93.png "Log di controllo")

### <a name="enterprise-applications-audit-logs"></a>Log di controllo di applicazioni aziendali

Con i report di controllo basati sulle applicazioni, è possibile ottenere risposte a domande come:

* Quali applicazioni sono state aggiunte o aggiornate?
* Quali applicazioni sono state rimosse?
* Un'entità servizio per un'applicazione è stata modificata?
* I nomi delle applicazioni sono stati modificati?
* Chi ha dato il consenso a un'applicazione?

Per esaminare i dati di controllo relativi alle applicazioni, è disponibile una visualizzazione filtrata in **Log di controllo** nella sezione **Attività** del pannello **Applicazioni aziendali**. Per **Tipo di risorsa attività** per questo punto di ingresso è preselezionato il valore **Applicazioni aziendali**.

![Log di controllo](./media/concept-audit-logs/134.png "Log di controllo")

È possibile filtrare ulteriormente questa visualizzazione per vedere i **gruppi** o gli **utenti**.

![Log di controllo](./media/concept-audit-logs/25.png "Log di controllo")

## <a name="office-365-activity-logs"></a>Log attività di Office 365

È possibile visualizzare i log attività di Office 365 dall'[interfaccia di amministrazione di Office 365](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). Sebbene i log attività di Office 365 e di Azure AD condividano numerose risorse della directory, solo l'interfaccia di amministrazione di Office 365 consente una visualizzazione completa dei log attività di Office 365. 

È possibile accedere ai log attività di Office 365 anche a livello di codice tramite le [API di gestione di Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni di riferimento sulle attività di controllo di Azure AD](reference-audit-activities.md)
- [Criteri di conservazione dei report di Azure Active Directory](reference-reports-data-retention.md)
- [Latenze dei report di Azure Active Directory](reference-reports-latencies.md)
