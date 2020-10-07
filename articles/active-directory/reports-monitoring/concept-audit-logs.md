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
ms.date: 09/17/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 994fe6c5005eeeab1399091dca5f72024fdd7262
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91812591"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Report delle attività di controllo nel portale di Azure Active Directory 

I report di Azure Active Directory (Azure AD) offrono tutte le informazioni necessarie per determinare le prestazioni dell'ambiente.



L'architettura di report è costituita dai componenti seguenti:

- **Attività** 
    - **Accessi**: il [report degli accessi](concept-sign-ins.md) fornisce informazioni sull'uso delle applicazioni gestite e sulle attività di accesso degli utenti.
    - **Log di controllo**: i log consentono la tracciabilità di tutte le modifiche apportate da varie funzionalità all'interno di Azure AD. I log di controllo registrano, ad esempio, le modifiche apportate a qualsiasi risorsa di Azure AD, ad esempio l'aggiunta o la rimozione di utenti, app, gruppi, ruoli e criteri.
    - Log di provisioning **Provisioning logs**  -  I [log di provisioning](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) consentono ai clienti di monitorare l'attività da parte del servizio di provisioning, ad esempio la creazione di un gruppo in ServiceNow o di un utente importato da giorni lavorativi. 
- **Sicurezza** 
    - **Accessi a rischio** : un [accesso rischioso](../identity-protection/overview-identity-protection.md) è un indicatore di un tentativo di accesso che potrebbe essere stato eseguito da un utente che non è il legittimo proprietario di un account utente. 
    - **Utenti contrassegnati per il rischio** : un [utente rischioso](../identity-protection/overview-identity-protection.md) è un indicatore per un account utente che potrebbe essere stato compromesso.

Questo articolo fornisce una panoramica del report di controllo.
 
## <a name="who-can-access-the-data"></a>Chi può accedere ai dati?

* Utenti nei ruoli **amministratore sicurezza**, **lettore sicurezza**, lettore **report** , **lettore globale** o **amministratore globale**

## <a name="audit-logs"></a>Log di controllo

I log di controllo di Azure AD forniscono i record delle attività di sistema per la conformità. Per accedere al report di controllo, selezionare **log di controllo** nella sezione **monitoraggio** del **Azure Active Directory**. 



Un log di controllo è una visualizzazione elenco predefinita che include:

- Data e ora dell'occorrenza.
- il servizio che ha registrato l'occorrenza
- categoria e nome dell'attività (*cosa*) 
- stato dell'attività (esito positivo o negativo)
- Destinazione
- Iniziatore/attore di un'attività (chi)

![Log di controllo](./media/concept-audit-logs/listview.png "Log di controllo")

È possibile personalizzare la visualizzazione elenco facendo clic su **colonne** nella barra degli strumenti.

![Colonne di controllo](./media/concept-audit-logs/columns.png "Colonne di controllo")

In questo modo è possibile visualizzare campi aggiuntivi o rimuovere campi già visualizzati.

![Rimuovi campi](./media/concept-audit-logs/columnselect.png "Rimuovi campi")

Selezionare un elemento nella visualizzazione elenco per ottenere maggiori informazioni dettagliate.

![Seleziona elemento](./media/concept-audit-logs/details.png "Seleziona elemento")


## <a name="filtering-audit-logs"></a>Filtro dei log di controllo

È possibile filtrare i dati di controllo in base ai campi seguenti:

- Servizio
- Categoria
- Attività
- Stato
- Destinazione
- Azione avviata da (attore)
- Intervallo di date

![Oggetto Filter](./media/concept-audit-logs/filter.png "Filter - oggetto")

Il filtro del **servizio** consente di effettuare una selezione da un elenco a discesa dei servizi seguenti:

- All
- UX di gestione AAD
- Verifiche di accesso
- Provisioning degli account
- Proxy dell'applicazione
- Authentication Methods
- B2C
- Accesso condizionale
- Directory principale
- Gestione dei diritti
- Autenticazione ibrida
- Identity Protection
- Utenti invitati
- Servizio MIM
- MyApps
- PIM
- Gestione gruppi self-service
- Gestione delle password self-service
- Condizioni per l'utilizzo

Il filtro **Category** consente di selezionare uno dei filtri seguenti:

- All
- AdministrativeUnit
- ApplicationManagement
- Authentication
- Autorizzazione
- Contatto
- Dispositivo
- DeviceConfiguration
- DirectoryManagement
- EntitlementManagement
- GroupManagement
- KerberosDomain
- KeyManagement
- Etichetta
- Altri
- PermissionGrantPolicy
- Policy
- ResourceManagement
- RoleManagement
- UserManagement

Il filtro **attività** è basato sulla selezione del tipo di risorsa Activity e Category effettuata dall'utente. È possibile selezionare un'attività specifica da visualizzare o selezionarle tutte. 

È possibile ottenere l'elenco di tutte le attività di controllo usando il API Graph: `https://graph.windows.net/<tenantdomain>/activities/auditActivityTypesV2?api-version=beta`

Il filtro di **stato** consente di filtrare in base allo stato di un'operazione di controllo. Lo stato può essere uno dei seguenti:

- All
- Operazione completata
- Operazioni non riuscite

Il filtro di **destinazione** consente di cercare una determinata destinazione dall'inizio del nome o del nome dell'entità utente (UPN). Il nome di destinazione e l'UPN fanno distinzione tra maiuscole e minuscole. 

Il filtro **avviato da** consente di definire il nome di un attore o un nome dell'entità universale (UPN). Il nome e l'UPN fanno distinzione tra maiuscole e minuscole.

Il filtro **intervallo di date** consente di definire un intervallo di tempo per i dati restituiti.  
I valori possibili sono:

- 7 giorni
- 24 ore
- Personalizzato

Quando si seleziona un intervallo di tempo personalizzato, è possibile configurare un'ora di inizio e un'ora di fine.

È anche possibile scegliere di scaricare i dati filtrati, fino a 250.000 record, selezionando il pulsante **download** . È possibile scaricare i log in formato CSV o JSON. Il numero di record che è possibile scaricare è limitato dai [criteri di conservazione dei report di Azure Active Directory](reference-reports-data-retention.md).

![Scaricare i dati](./media/concept-audit-logs/download.png "Scaricare i dati")

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

Per rivedere solo i dati di controllo relativi agli utenti, è possibile trovare una visualizzazione filtrata in **log di controllo** nella sezione **monitoraggio** della scheda **utenti** . Questo punto di ingresso ha **UserManagement** come categoria preselezionata.

![Utente](./media/concept-audit-logs/users.png "Utente")

Per rivedere solo i dati di controllo relativi ai gruppi, è possibile trovare una visualizzazione filtrata in **log di controllo** nella sezione **monitoraggio** della scheda **gruppi** . Questo punto di ingresso ha **GroupManagement** come categoria preselezionata.

![Filtra gruppi](./media/concept-audit-logs/groups.png "Filtra gruppi")

### <a name="enterprise-applications-audit-logs"></a>Log di controllo di applicazioni aziendali

Con i report di controllo basati sulle applicazioni, è possibile ottenere risposte a domande come:

* Quali applicazioni sono state aggiunte o aggiornate?
* Quali applicazioni sono state rimosse?
* Un'entità servizio per un'applicazione è stata modificata?
* I nomi delle applicazioni sono stati modificati?
* Chi ha dato il consenso a un'applicazione?

Per esaminare i dati di controllo relativi alle applicazioni, è disponibile una visualizzazione filtrata in **Log di controllo** nella sezione **Attività** del pannello **Applicazioni aziendali**. Questo punto di ingresso dispone di **applicazioni aziendali** preselezionate come **tipo di applicazione**.

![Applicazioni aziendali](./media/concept-audit-logs/enterpriseapplications.png "Applicazioni aziendali")

## <a name="microsoft-365-activity-logs"></a>Log attività Microsoft 365

È possibile visualizzare i log attività Microsoft 365 dall'interfaccia di [amministrazione di Microsoft 365](/office365/admin/admin-overview/about-the-admin-center). Anche se Microsoft 365 attività e i log attività Azure AD condividono numerose risorse della directory, solo l'interfaccia di amministrazione Microsoft 365 fornisce una visualizzazione completa dei log attività Microsoft 365. 

È anche possibile accedere ai log attività di Microsoft 365 a livello di codice usando le [API di gestione di Office 365](/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni di riferimento sulle attività di controllo di Azure AD](reference-audit-activities.md)
- [Criteri di conservazione dei report di Azure Active Directory](reference-reports-data-retention.md)
- [Latenze dei report di Azure Active Directory](reference-reports-latencies.md)
