---

title: "Report delle attività di controllo nel portale di Azure Active Directory | Microsoft Docs"
description: "Introduzione ai report delle attività di controllo nel portale di Azure Active Directory"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: d8c49272789e7d33c6f0684875765a1ecea5a2ff
ms.contentlocale: it-it
ms.lasthandoff: 05/26/2017


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
- Gruppo
- Directory
- Utente
- Applicazione
- Criteri
- Dispositivo
- Altri

Quando si seleziona **Gruppo** come **Tipo di risorsa attività**, si ottiene una categoria di filtro aggiuntiva che permette di specificare anche un'**Origine**:

- Azure AD
- O365


Il filtro **Attività** filtro si basa sulla categoria e sul tipo di risorsa attività selezionati. È possibile selezionare un'attività specifica da visualizzare o selezionarle tutte. 

È possibile ottenere l'elenco di tutte le attività di controllo usando l'API Graph https://graph.windows.net/$tenantdomain/activities/auditActivityTypes?api-version=beta, dove $tenantdomain è il nome del dominio. In alternativa, vedere l'articolo relativo agli [eventi del report di controllo](active-directory-reporting-audit-events.md#list-of-audit-report-events).


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


## <a name="next-steps"></a>Passaggi successivi
Vedere [Guida alla creazione di report in Azure Active Directory](active-directory-reporting-guide.md).


