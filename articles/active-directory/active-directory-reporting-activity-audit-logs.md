---
title: "Report delle attività di controllo nel portale di Azure Active Directory, anteprima | Microsoft Docs"
description: "Introduzione ai report delle attività di controllo nell&quot;anteprima del portale di Azure Active Directory"
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
ms.date: 04/07/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: d88f8bed0cbd14ee49986d6749396731a810034b
ms.lasthandoff: 04/18/2017


---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal---preview"></a>Report delle attività di controllo nel portale di Azure Active Directory, anteprima

I report nell'[anteprima](active-directory-preview-explainer.md) di Azure Active Directory offrono tutte le informazioni necessarie per determinare come lo stato dell'ambiente.

L'architettura di reporting in Azure Active Directory include i componenti seguenti:

- **Attività** 
    - **Attività di accesso** : informazioni sull'utilizzo delle applicazioni gestite e sulle attività di accesso utente
    - **Log di controllo**: informazioni relative alle attività di sistema sulla gestione di utenti e gruppi, sulle applicazioni gestite e sulle attività di directory.
- **Sicurezza** 
    - **Accessi a rischio**. Un accesso rischioso è indicativo di un tentativo di accesso che potrebbe essere stato eseguito da qualcuno che non è il legittimo proprietario di un account utente. Per informazioni dettagliate, vedere Accessi a rischio.
    - **Utenti contrassegnati per il rischio**. Un utente rischioso è indicativo di un account utente che potrebbe essere stato compromesso. Per informazioni dettagliate, vedere Utenti contrassegnati per il rischio.

In questo argomento viene offerta una panoramica delle attività di controllo.
 


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
- Provisioning degli account
- Rollover automatizzato delle password
- Utenti invitati
- Servizio MIM

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

| Categoria di attività| Tipo di risorsa di attività| Attività |
| :-- | :-: | :-- |
| Directory principale| Gruppo| Eliminare le impostazioni dei gruppi|
| Directory principale| Directory| Aggiornare un dominio|
| Directory principale| Directory| Rimuovere un partner dalla società|
| Directory principale| Utente| Aggiornare un ruolo|
| Directory principale| Utente| Aggiungere un ruolo da un modello|
| Directory principale| Gruppo| Aggiungere un'assegnazione di ruolo app a un gruppo|
| Directory principale| Gruppo| Avviare l'applicazione di licenze basate sui gruppi agli utenti|
| Directory principale| Applicazione| Aggiungere un'entità servizio|
| Directory principale| Criteri| Aggiornare i criteri|
| Directory principale| Criteri| Aggiungere criteri all'entità servizio|
| Directory principale| Dispositivo| Aggiungere un proprietario registrato a un dispositivo|
| Directory principale| Dispositivo| Aggiungere utenti registrati a un dispositivo|
| Directory principale| Dispositivo| Aggiornare la configurazione di un dispositivo|
| Gestione delle password self-service| Utente| Reimpostare le password (self-service)|
| Gestione delle password self-service| Utente| Sbloccare l'account utente (self-service)|
| Gestione delle password self-service| Utente| Reimpostare le password (amministratore)|
| Gestione gruppi self-service| Gruppo| Eliminare una richiesta di partecipazione a un gruppo in sospeso|
| Provisioning degli account| Applicazione| Elaborare il deposito|
| Automated Password Rollover (Rollover automatizzato delle password)| Applicazione| Automated Password Rollover (Rollover automatizzato delle password)|
| Utenti invitati| Altri| Inviti batch elaborati|
| Directory principale| Directory| Rimuovere un dominio verificato|
| Directory principale| Directory| Aggiungere un dominio non verificato|
| Directory principale| Directory| Aggiungere un dominio verificato|
| Directory principale| Directory| Configurare una funzionalità directory nel tenant|
| Directory principale| Directory| Impostare il flag DirSyncEnabled|
| Directory principale| Directory| Create le impostazioni aziendali|
| Directory principale| Directory| Aggiornare le impostazioni aziendali|
| Directory principale| Directory| Eliminare le impostazioni aziendali|
| Directory principale| Directory| Configurare la posizione dei dati consentita per l'azienda|
| Directory principale| Directory| Abilitare la funzionalità multinazionale per l'azienda|
| Directory principale| Utente| Aggiornare un utente|
| Directory principale| Utente| Eliminazione di un utente.|
| Directory principale| Gruppo| Rimuovere un membro da un gruppo|
| Directory principale| Gruppo| Configurare la licenza di un gruppo|
| Directory principale| Gruppo| Creare le impostazioni dei gruppi|
| Directory principale| Applicazione| Aggiornare un'entità servizio|
| Directory principale| Applicazione| Elimina applicazione|
| Directory principale| Applicazione| Aggiornare un'applicazione|
| Directory principale| Applicazione| Rimuovere un'entità servizio|
| Directory principale| Applicazione| Aggiungere le credenziali di un'entità servizio|
| Directory principale| Applicazione| Rimuovere un'assegnazione di ruolo app da un'entità servizio|
| Directory principale| Applicazione| Rimuovere il proprietario da un'applicazione|
| Directory principale| Dispositivo| Rimuovere il proprietario registrato da un dispositivo|
| Gestione delle password self-service| Utente| Stato dell'attività di reimpostazione delle password self-service|
| Provisioning degli account| Applicazione| Amministrazione|
| Provisioning degli account| Applicazione| Eseguire operazioni sulla directory|
| Servizio MIM| Gruppo| Rimuovere un membro|
| Directory principale| Criteri| Eliminare criteri|
| Utenti invitati| Utente| Creare il tenant virale|
| Directory principale| Directory| Aggiornare i segreti esterni|
| Directory principale| Directory| Configurare le proprietà di Rights Management|
| Directory principale| Directory| Aggiornare le impostazioni aziendali|
| Directory principale| Utente| Aggiunta di un utente|
| Directory principale| Utente| Convertire un utente federato in utente gestito|
| Directory principale| Utente| Creare password applicazione per un utente|
| Directory principale| Gruppo| Aggiungere un membro a un gruppo|
| Directory principale| Gruppo| Aggiungere un gruppo|
| Directory principale| Applicazione| Dare il consenso a un'applicazione|
| Directory principale| Applicazione| Aggiungere un'applicazione|
| Directory principale| Applicazione| Aggiungere un proprietario a un'entità servizio|
| Directory principale| Applicazione| Rimuovere Oauth2Permissiongrant|
| Directory principale| Criteri| Rimuovere le credenziali dei criteri|
| Directory principale| Dispositivo| Eliminare la configurazione di un dispositivo|
| Gestione gruppi self-service| Gruppo| Impostare proprietà di gruppi dinamici|
| Gestione gruppi self-service| Gruppo| Aggiornare criteri di gestione del ciclo di vita|
| Provisioning degli account| Applicazione| Azione della regola di sincronizzazione|
| Utenti invitati| Altri| Inviti batch caricati|
| Servizio MIM| Gruppo| Aggiungere un membro|
| Directory principale| Utente| Impostare le proprietà della licenza|
| Directory principale| Utente| Ripristinare un utente|
| Directory principale| Utente| Rimuovere un membro da un ruolo|
| Directory principale| Utente| Rimuovere un'assegnazione di ruolo app da un utente|
| Directory principale| Utente| Rimuovere un membro con ambito da un ruolo|
| Directory principale| Gruppo| Aggiornare un gruppo|
| Directory principale| Gruppo| Aggiungere un proprietario a un gruppo|
| Directory principale| Gruppo| Terminare l'applicazione di licenze basate sui gruppi agli utenti|
| Directory principale| Gruppo| Rimuovere un'assegnazione di ruolo app da un gruppo|
| Directory principale| Gruppo| Configurare il gruppo che deve essere gestito da un utente|
| Directory principale| Applicazione| Aggiungere Oauth2Permissiongrant|
| Directory principale| Applicazione| Aggiungere un'assegnazione di ruolo app a un'entità servizio|
| Directory principale| Applicazione| Rimuovere le credenziali di un'entità servizio|
| Directory principale| Criteri| Rimuovere i criteri da un'entità servizio|
| Directory principale| Dispositivo| Aggiornare un dispositivo|
| Directory principale| Dispositivo| Aggiungere un dispositivo|
| Directory principale| Dispositivo| Aggiungere una configurazione del dispositivo|
| Gestione delle password self-service| Utente| Modificare la password (self-service)|
| Gestione delle password self-service| Utente| Utente registrato per la reimpostazione password self-service|
| Gestione gruppi self-service| Gruppo| Approvare una richiesta di partecipazione a un gruppo in sospeso|
| Directory principale| Directory| Rimuovere un dominio non verificato|
| Directory principale| Directory| Verificare un dominio|
| Directory principale| Directory| Impostare l'autenticazione di un dominio|
| Directory principale| Directory| Impostazione criteri password|
| Directory principale| Directory| Aggiungere un partner alla società|
| Directory principale| Directory| Innalzare un'azienda a livello di partner|
| Directory principale| Directory| Configurare una relazione|
| Directory principale| Directory| Configurare una soglia di eliminazione accidentale|
| Directory principale| Directory| Abbassare di livello un partner|
| Utenti invitati| Utente| Invitare utenti esterni|
| Provisioning degli account| Applicazione| Importa|
| Directory principale| Applicazione| Rimuovere il proprietario da un'entità servizio|
| Directory principale| Dispositivo| Rimuovere utenti registrati da un dispositivo|
| Directory principale| Directory| Impostazione informazioni società|
| Directory principale| Directory| Configurare le impostazioni di federazione in un dominio|
| Directory principale| Directory| Creare un'azienda|
| Directory principale| Directory| Eliminare le proprietà di Rights Management|
| Directory principale| Directory| Configurare la funzionalità DirSync|
| Directory principale| Directory| Verificare il dominio tramite la verifica di posta elettronica|
| Directory principale| Utente| Modificare un licenza utente|
| Directory principale| Utente| Modificare una password utente|
| Directory principale| Utente| Reimpostare una password utente|
| Directory principale| Utente| Aggiungere un'assegnazione di ruolo app a un utente|
| Directory principale| Utente| Aggiungere un membro a un ruolo|
| Directory principale| Utente| Eliminare password applicazione per un utente|
| Directory principale| Utente| Aggiornare le credenziali di un utente|
| Directory principale| Utente| Impostare la gestione utenti|
| Directory principale| Utente| Aggiungere un membro con ambito a un ruolo|
| Directory principale| Gruppo| Eliminare un gruppo|
| Directory principale| Gruppo| Rimuovere il proprietario da un gruppo|
| Directory principale| Gruppo| Aggiornare le impostazioni dei gruppi|
| Directory principale| Applicazione| Aggiungere un proprietario a un'applicazione|
| Directory principale| Applicazione| Revocare il consenso|
| Directory principale| Criteri| Aggiungere criteri|
| Directory principale| Dispositivo| Eliminare un dispositivo|
| Gestione delle password self-service| Utente| Bloccato dalla reimpostazione password self-service|
| Gestione gruppi self-service| Gruppo| Richiedere di unirsi a un gruppo|
| Gestione gruppi self-service| Gruppo| Creare criteri di gestione del ciclo di vita|
| Gestione gruppi self-service| Gruppo| Rifiutare una richiesta di partecipazione a un gruppo in sospeso|
| Gestione gruppi self-service| Gruppo| Annullare una richiesta di partecipazione a un gruppo in sospeso|
| Gestione gruppi self-service| Gruppo| Rinnovare un gruppo|
| Provisioning degli account| Applicazione| Esporta|
| Provisioning degli account| Applicazione| Altri|
| Utenti invitati| Utente| Riscattare un invito utente esterno|
| Utenti invitati| Utente| Creare un utente virale|
| Utenti invitati| Utente| Assegnare utenti esterni a un'applicazione|




## <a name="audit-logs-shortcuts"></a>Collegamenti ai log di controllo

Oltre ad **Azure Active Directory**, il portale di Azure offre due ulteriori punti di ingresso ai dati di controllo:

- Utenti e gruppi
- Applicazioni aziendali

Per un elenco completo delle attività dei report di controllo, vedere l' [elenco di eventi dei report di controllo](active-directory-reporting-audit-events.md#list-of-audit-report-events).


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


