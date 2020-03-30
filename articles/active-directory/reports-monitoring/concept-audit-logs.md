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
ms.openlocfilehash: ad399fc24b2cdfbdc51e7feccba2c05786216b19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253234"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Report delle attività di controllo nel portale di Azure Active Directory 

I report di Azure Active Directory (Azure AD) offrono tutte le informazioni necessarie per determinare le prestazioni dell'ambiente.

L'architettura di report è costituita dai componenti seguenti:

- **Attività** 
    - **Accessi**: il [report degli accessi](concept-sign-ins.md) fornisce informazioni sull'uso delle applicazioni gestite e sulle attività di accesso degli utenti.
    - **Log di controllo**: i log consentono la tracciabilità di tutte le modifiche apportate da varie funzionalità all'interno di Azure AD. I log di controllo registrano, ad esempio, le modifiche apportate a qualsiasi risorsa di Azure AD, ad esempio l'aggiunta o la rimozione di utenti, app, gruppi, ruoli e criteri.
- **Sicurezza** 
    - **Adesempiomenti rischiosi:** un [accesso rischioso](concept-risky-sign-ins.md) è un indicatore di un tentativo di accesso che potrebbe essere stato eseguito da un utente che non è il legittimo proprietario di un account utente. 
    - **Utenti contrassegnati per il rischio:** un [utente rischioso](concept-user-at-risk.md) è un indicatore per un account utente che potrebbe essere stato compromesso.

Questo articolo fornisce una panoramica del report di controllo.
 
## <a name="who-can-access-the-data"></a>Chi può accedere ai dati?

* Utenti con i ruoli **Amministratore sicurezza**, **Lettore sicurezza,** **Lettore report** , **Lettore globale** o Amministratore **globale**

## <a name="audit-logs"></a>Log di controllo

I log di controllo di Azure AD forniscono i record delle attività di sistema per la conformità. Per accedere al report di controllo, selezionare Log di **controllo** nella sezione **Monitoraggio** di Azure **Active Directory.** Si noti che i log di controllo possono avere una latenza fino a un'ora, pertanto potrebbe essere necessario molto tempo prima che i dati dell'attività di controllo vengano visualizzati nel portale dopo aver completato l'attività.



Un log di controllo è una visualizzazione elenco predefinita che include:

- Data e ora dell'occorrenza.
- il servizio che ha registrato l'occorrenza
- la categoria e il nome dell'attività (*cosa*) 
- lo stato dell'attività (esito positivo o negativo)
- Destinazione
- Iniziatore/attore di un'attività (chi)

![Log di controllo](./media/concept-audit-logs/listview.png "Log di controllo")

È possibile personalizzare la visualizzazione elenco facendo clic su **Colonne** nella barra degli strumenti.

![Log di controllo](./media/concept-audit-logs/columns.png "Log di controllo")

In questo modo è possibile visualizzare campi aggiuntivi o rimuovere campi già visualizzati.

![Log di controllo](./media/concept-audit-logs/columnselect.png "Log di controllo")

Selezionare un elemento nella visualizzazione elenco per ottenere maggiori informazioni dettagliate.

![Log di controllo](./media/concept-audit-logs/details.png "Log di controllo")


## <a name="filtering-audit-logs"></a>Filtro dei log di controllo

È possibile filtrare i dati di controllo in base ai campi seguenti:

- Service
- Category
- Attività
- Stato
- Destinazione
- Azione avviata da (attore)
- Intervallo di date

![Log di controllo](./media/concept-audit-logs/filter.png "Log di controllo")

Il filtro **Servizio** consente di selezionare da un elenco a discesa dei seguenti servizi:

- Tutti
- AAD Management UX
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
- Applicazioni personali
- PIM
- Gestione gruppi self-service
- Gestione delle password self-service
- Condizioni per l'utilizzo

Il filtro **Categoria** consente di selezionare uno dei seguenti filtri:

- Tutti
- AdministrativeUnit
- ApplicationManagement (Gestione applicazioni)
- Authentication
- Autorizzazione
- Contatto
- Dispositivo
- DeviceConfiguration
- Gestione directory
- Gestione diritti
- GroupManagement
- Dominio Kerberos
- Gestione delle chiaviKeyManagement
- Etichetta
- Altri
- PermissionGrantPolicy (criteri di gruppo)
- Policy
- Gestione risorse
- Gestione ruolo
- Gestione utente

Il filtro **Attività** si basa sulla selezione del tipo di risorsa categoria e attività effettuata. È possibile selezionare un'attività specifica da visualizzare o selezionarle tutte. 

È possibile ottenere l'elenco di tutte le attività di controllo utilizzando l'API Graph:You can get the list of all Audit Activities using the Graph API:`https://graph.windows.net/<tenantdomain>/activities/auditActivityTypesV2?api-version=beta`

Il filtro **Stato** consente di filtrare in base allo stato di un'operazione di controllo. Lo stato può essere uno dei seguenti:

- Tutti
- Operazione completata
- Operazioni non riuscite

Il filtro **Destinazione** consente di cercare una destinazione specifica in base all'inizio del nome o del nome dell'entità utente (UPN). Per il nome di destinazione e l'UPN viene fatta distinzione tra maiuscole e minuscole. 

Il filtro **Iniziato da** consente di definire il nome di un attore o un nome principale universale (UPN). Per il nome e l'UPN viene fatta distinzione tra maiuscole e minuscole.

Il filtro **Intervallo** di date consente di definire un intervallo di tempo per i dati restituiti.  
I valori possibili sono:

- 7 giorni
- 24 ore
- Personalizzato

Quando si seleziona un intervallo di tempo personalizzato, è possibile configurare un'ora di inizio e un'ora di fine.

È inoltre possibile scegliere di scaricare i dati filtrati, fino a 250.000 record, selezionando il pulsante **Download.** È possibile scaricare i log in formato CSV o JSON. Il numero di record che è possibile scaricare è limitato dai [criteri di conservazione dei report di Azure Active Directory](reference-reports-data-retention.md).

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

Se si desidera esaminare solo i dati di controllo correlati agli utenti, è possibile trovare una visualizzazione filtrata in Registri di **controllo** nella sezione **Monitoraggio** della scheda **Utenti.** Questo punto di ingresso ha **UserManagement** come categoria preselezionata.

![Log di controllo](./media/concept-audit-logs/users.png "Log di controllo")

Se si desidera esaminare solo i dati di controllo correlati ai gruppi, è possibile trovare una visualizzazione filtrata in **Registri di controllo** nella sezione **Monitoraggio** della scheda **Gruppi.** Questo punto di ingresso ha **GroupManagement** come categoria preselezionata.

![Log di controllo](./media/concept-audit-logs/groups.png "Log di controllo")

### <a name="enterprise-applications-audit-logs"></a>Log di controllo di applicazioni aziendali

Con i report di controllo basati sulle applicazioni, è possibile ottenere risposte a domande come:

* Quali applicazioni sono state aggiunte o aggiornate?
* Quali applicazioni sono state rimosse?
* Un'entità servizio per un'applicazione è stata modificata?
* I nomi delle applicazioni sono stati modificati?
* Chi ha dato il consenso a un'applicazione?

Per esaminare i dati di controllo relativi alle applicazioni, è disponibile una visualizzazione filtrata in **Log di controllo** nella sezione **Attività** del pannello **Applicazioni aziendali**. Questo punto di ingresso ha **applicazioni aziendali** preselezionate come tipo **di applicazione**.

![Log di controllo](./media/concept-audit-logs/enterpriseapplications.png "Log di controllo")

## <a name="office-365-activity-logs"></a>Log attività di Office 365

È possibile visualizzare i log attività di Office 365 dall'interfaccia di amministrazione di [Microsoft 365.](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center) Anche se l'attività di Office 365 e i log attività di Azure AD condividono molte risorse di directory, solo l'interfaccia di amministrazione di Microsoft 365 offre una visualizzazione completa dei log attività di Office 365. 

È inoltre possibile accedere ai log attività di Office 365 a livello di programmazione utilizzando le API di gestione di [Office 365.](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview)

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni di riferimento sulle attività di controllo di Azure AD](reference-audit-activities.md)
- [Criteri di conservazione dei report di Azure Active Directory](reference-reports-data-retention.md)
- [Latenze dei report di Azure Active Directory](reference-reports-latencies.md)
