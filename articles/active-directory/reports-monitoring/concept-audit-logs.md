---
title: Report delle attività di controllo nel portale di Azure Active Directory | Microsoft Docs
description: Introduzione ai report delle attività di controllo nel portale di Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/17/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8883e3ade7e5d52f3a62346bf8be51ac36042f82
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74008533"
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

* Utenti nei ruoli amministratore **sicurezza**, **lettore sicurezza**, **lettore report** o **amministratore globale**

## <a name="audit-logs"></a>Log di controllo

I log di controllo di Azure AD forniscono i record delle attività di sistema per la conformità. Per accedere ai log di controllo, selezionare **Log di controllo** nella sezione **Attività** di **Azure Active Directory**. Si noti che i log di controllo possono avere una latenza fino a un'ora, pertanto la visualizzazione dei dati di attività di controllo nel portale potrebbe richiedere un tempo di attesa simile dopo aver completato l'attività.



Un log di controllo è una visualizzazione elenco predefinita che include:

- Data e ora dell'occorrenza.
- il servizio che ha registrato l'occorrenza
- categoria e nome dell'attività (*cosa*) 
- stato dell'attività (esito positivo o negativo)
- Destinazione
- Iniziatore/attore di un'attività (chi)

![Log di controllo](./media/concept-audit-logs/listview.png "Log di controllo")

Per personalizzare la visualizzazione elenco, fare clic su **Colonne** nella barra degli strumenti.

![Log di controllo](./media/concept-audit-logs/columns.png "Log di controllo")

In questo modo è possibile visualizzare campi aggiuntivi o rimuovere campi già visualizzati.

![Log di controllo](./media/concept-audit-logs/columnselect.png "Log di controllo")

Selezionare un elemento nella visualizzazione elenco per ottenere maggiori informazioni dettagliate.

![Log di controllo](./media/concept-audit-logs/details.png "Log di controllo")


## <a name="filtering-audit-logs"></a>Filtro dei log di controllo

È possibile filtrare i dati di controllo in base ai campi seguenti:

- Servizio
- Categoria
- Attività
- Stato
- Destinazione
- Azione avviata da (attore)
- Intervallo di date

![Log di controllo](./media/concept-audit-logs/filter.png "Log di controllo")

Il filtro del **servizio** consente di effettuare una selezione da un elenco a discesa dei servizi seguenti:

- Tutti
- Verifiche di accesso
- Provisioning degli account 
- SSO applicazione
- Metodi di autenticazione
- B2C
- Accesso condizionale
- Directory principale
- Gestione entitlement
- Identity Protection
- Utenti invitati
- PIM
- Gestione gruppi self-service
- Gestione delle password self-service
- Condizioni per l'utilizzo

Il filtro **Category** consente di selezionare uno dei filtri seguenti:

- Tutti
- AdministrativeUnit
- ApplicationManagement
- Autenticazione
- Autorizzazione
- Contatto
- Dispositivo
- DeviceConfiguration
- DirectoryManagement
- EntitlementManagement
- GroupManagement
- Altre
- Criteri
- ResourceManagement
- RoleManagement
- UserManagement

Il filtro **attività** è basato sulla selezione del tipo di risorsa Activity e Category effettuata dall'utente. È possibile selezionare un'attività specifica da visualizzare o selezionarle tutte. 

È possibile ottenere l'elenco di tutte le attività di controllo usando il API Graph: `https://graph.windows.net/<tenantdomain>/activities/auditActivityTypesV2?api-version=beta`

Il filtro di **stato** consente di filtrare in base allo stato di un'operazione di controllo. Lo stato può essere uno dei seguenti:

- Tutti
- Operazione completata
- Esito negativo

Il filtro di **destinazione** consente di cercare una determinata destinazione in base al nome o al nome dell'entità utente (UPN). Il nome di destinazione e l'UPN fanno distinzione tra maiuscole e minuscole. 

Il filtro **avviato da** consente di definire il nome di un attore o un nome dell'entità universale (UPN). Il nome e l'UPN fanno distinzione tra maiuscole e minuscole.

Il filtro **intervallo di date** consente di definire un intervallo di tempo per i dati restituiti.  
I valori possibili sono:

- 1 mese
- 7 giorni
- 24 ore
- Personalizzate

Quando si seleziona un intervallo di tempo personalizzato, è possibile configurare un'ora di inizio e un'ora di fine.

È anche possibile scegliere di scaricare i dati filtrati, fino a 250.000 record, selezionando il pulsante **download** . È possibile scaricare i log in formato CSV o JSON. Il numero di record che è possibile scaricare è limitato dai [criteri di conservazione dei report di Azure Active Directory](reference-reports-data-retention.md).

![Log di controllo](./media/concept-audit-logs/download.png "Log di controllo")

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

Per rivedere solo i dati di controllo relativi agli utenti, è possibile trovare una visualizzazione filtrata in **log di controllo** nella sezione **attività** della scheda **utenti** . Questo punto di ingresso ha **UserManagement** come categoria preselezionata.

![Log di controllo](./media/concept-audit-logs/users.png "Log di controllo")

Per rivedere solo i dati di controllo relativi ai gruppi, è possibile trovare una visualizzazione filtrata in **log di controllo** nella sezione **attività** della scheda **gruppi** . Questo punto di ingresso ha **GroupManagement** come categoria preselezionata.

![Log di controllo](./media/concept-audit-logs/groups.png "Log di controllo")

### <a name="enterprise-applications-audit-logs"></a>Log di controllo di applicazioni aziendali

Con i report di controllo basati sulle applicazioni, è possibile ottenere risposte a domande come:

* Quali applicazioni sono state aggiunte o aggiornate?
* Quali applicazioni sono state rimosse?
* Un'entità servizio per un'applicazione è stata modificata?
* I nomi delle applicazioni sono stati modificati?
* Chi ha dato il consenso a un'applicazione?

Per esaminare i dati di controllo relativi alle applicazioni, è disponibile una visualizzazione filtrata in **Log di controllo** nella sezione **Attività** del pannello **Applicazioni aziendali**. Questo punto di ingresso dispone di **applicazioni aziendali** preselezionate come **tipo di applicazione**.

![Log di controllo](./media/concept-audit-logs/enterpriseapplications.png "Log di controllo")

## <a name="office-365-activity-logs"></a>Log attività di Office 365

È possibile visualizzare i log attività di Office 365 dall'interfaccia di [amministrazione di Microsoft 365](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). Anche se l'attività di Office 365 e i log attività Azure AD condividono numerose risorse della directory, solo l'interfaccia di amministrazione di Microsoft 365 fornisce una visualizzazione completa dei log attività di Office 365. 

È anche possibile accedere ai log attività di Office 365 a livello di codice usando le [API di gestione di office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni di riferimento sulle attività di controllo di Azure AD](reference-audit-activities.md)
- [Criteri di conservazione dei report di Azure Active Directory](reference-reports-data-retention.md)
- [Latenze dei report di Azure Active Directory](reference-reports-latencies.md)
