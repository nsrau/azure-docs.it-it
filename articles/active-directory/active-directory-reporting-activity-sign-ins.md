---
title: "Report delle attività di accesso nel portale di Azure Active Directory | Microsoft Docs"
description: "Introduzione ai report delle attività di accesso nel portale di Azure Active Directory"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/21/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: b764c813910b8188b2da7a8168d7e8ab8af70762
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2017
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Report delle attività di accesso nel portale di Azure Active Directory

I report di Azure Active Directory (Azure AD) nel [portale di Azure](https://portal.azure.com) offrono tutte le informazioni necessarie per determinare lo stato dell'ambiente.

L'architettura di reporting in Azure Active Directory include i componenti seguenti:

- **Attività** 
    - **Attività di accesso** : informazioni sull'utilizzo delle applicazioni gestite e sulle attività di accesso utente
    - **Log di controllo**: informazioni relative alle attività di sistema sulla gestione di utenti e gruppi, sulle applicazioni gestite e sulle attività di directory.
- **Sicurezza** 
    - **Accessi a rischio**. Un accesso rischioso è indicativo di un tentativo di accesso che potrebbe essere stato eseguito da qualcuno che non è il legittimo proprietario di un account utente. Per informazioni dettagliate, vedere Accessi a rischio.
    - **Utenti contrassegnati per il rischio**. Un utente rischioso è indicativo di un account utente che potrebbe essere stato compromesso. Per informazioni dettagliate, vedere Utenti contrassegnati per il rischio.

In questo argomento viene offerta una panoramica delle attività di accesso.

## <a name="pre-requisite"></a>Prerequisito.

### <a name="who-can-access-the-data"></a>Chi può accedere ai dati?
* Gli utenti con ruolo di amministratore della sicurezza o con autorizzazioni di lettura per la sicurezza
* Gli amministratori globali
* Qualsiasi utente (non amministratore) può visualizzare i propri accessi 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Quale licenza di Azure AD è necessaria per visualizzare le attività di accesso?
* Per visualizzare il report completo delle attività di accesso, è necessario che al tenant sia associata una licenza di Azure AD Premium


## <a name="signs-in-activities"></a>Attività di accesso

Le informazioni contenute nel report relativo all'accesso utente consentono di rispondere a domande come le seguenti:

* Qual è il modello di accesso di un utente?
* Quanti utenti hanno effettuato l'accesso nell'arco di una settimana?
* Qual è lo stato di questi accessi?

Il primo punto di ingresso a tutte le attività di accesso è **Accessi** nella sezione **Attività** di Azure Active


![Attività di accesso](./media/active-directory-reporting-activity-sign-ins/61.png "Attività di accesso")


Un log di controllo è una visualizzazione elenco predefinita che include:

- Utente correlato.
- Applicazione a cui l'utente ha eseguito l'accesso.
- Stato dell'accesso.
- Orario di accesso.

![Attività di accesso](./media/active-directory-reporting-activity-sign-ins/41.png "Attività di accesso")

Per personalizzare la visualizzazione elenco, fare clic su **Colonne** nella barra degli strumenti.

![Attività di accesso](./media/active-directory-reporting-activity-sign-ins/19.png "Attività di accesso")

In questo modo è possibile visualizzare campi aggiuntivi o rimuovere campi già visualizzati.

![Attività di accesso](./media/active-directory-reporting-activity-sign-ins/42.png "Attività di accesso")

Facendo clic su un elemento nella visualizzazione elenco, è possibile ottenere tutti i dettagli disponibili sull'elemento.

![Attività di accesso](./media/active-directory-reporting-activity-sign-ins/43.png "Attività di accesso")


## <a name="filtering-sign-in-activities"></a>Filtro delle attività di accesso

Per limitare i dati segnalati in base alle esigenze, è possibile filtrare i dati di accesso usando i campi seguenti:

- Intervallo di tempo
- Utente
- Applicazione
- Client
- Stato accesso

![Attività di accesso](./media/active-directory-reporting-activity-sign-ins/44.png "Attività di accesso")


Il filtro **Intervallo di tempo** permette di definire un intervallo di tempo per i dati restituiti.  
I valori possibili sono:

- 1 mese
- 7 giorni
- 24 ore
- Personalizzate

Quando si seleziona un intervallo di tempo personalizzato, è possibile configurare un'ora di inizio e un'ora di fine.

Il filtro **Utente** permette di specificare il nome o il nome dell'entità utente (UPN) per l'utente richiesto.

Il filtro **Applicazione** permette di specificare il nome dell'applicazione richiesta.

Il filtro **Client** permette di specificare informazioni sul dispositivo richiesto.

Il filtro **Stato accesso** permette di selezionare uno dei filtri seguenti:

- Tutti
- Success
- Esito negativo


## <a name="sign-in-activities-shortcuts"></a>Collegamenti alle attività di accesso

Oltre ad Azure Active Directory, il portale di Azure offre due ulteriori punti di ingresso ai dati sulle attività di accesso:

- Utenti e gruppi
- Applicazioni aziendali


### <a name="users-and-groups-sign-ins-activities"></a>Attività di accesso di utenti e gruppi

Le informazioni contenute nel report relativo all'accesso utente consentono di rispondere a domande come le seguenti:

- Qual è il modello di accesso di un utente?
- Quanti utenti hanno effettuato l'accesso nell'arco di una settimana?
- Qual è lo stato di questi accessi?



Il punto di ingresso a questi dati è il grafico relativo agli accessi utente della sezione **Panoramica** in **Utenti e gruppi**.

![Attività di accesso](./media/active-directory-reporting-activity-sign-ins/45.png "Attività di accesso")

Il grafico degli accessi utente visualizza le aggregazioni settimanali degli accessi per tutti gli utenti in un determinato periodo di tempo. Il periodo di tempo predefinito è di 30 giorni.

![Attività di accesso](./media/active-directory-reporting-activity-sign-ins/46.png "Attività di accesso")

Quando si fa clic su un giorno nel grafico degli accessi, si ottiene un elenco dettagliato delle attività di accesso per tale giorno.

![Attività di accesso](./media/active-directory-reporting-activity-sign-ins/41.png "Attività di accesso")

Ogni riga nell'elenco di attività di accesso offre informazioni dettagliate sull'accesso selezionato, ad esempio:

* Chi ha effettuato l'accesso?
* Qual era il nome UPN correlato?
* Qual era l'applicazione di destinazione dell'accesso?
* Qual è l'indirizzo IP dell'accesso?
* Qual era lo stato dell'accesso?

L'opzione **Accessi** offre una panoramica completa di tutti gli accessi utente.

![Attività di accesso](./media/active-directory-reporting-activity-sign-ins/51.png "Attività di accesso")



## <a name="usage-of-managed-applications"></a>Utilizzo di applicazioni gestite

Con una visualizzazione dei dati di accesso basata sulle applicazioni, è possibile rispondere a domande come:

* Chi sta usando le applicazioni?
* Quali sono le prime 3 applicazioni nell'organizzazione?
* Di recente è stata implementata un'applicazione. Come sta andando?

Il punto di ingresso a questi dati sono le prime 3 applicazioni nell'organizzazione nel report sugli ultimi 30 giorni della sezione **Panoramica** in **Applicazioni aziendali**.

![Attività di accesso](./media/active-directory-reporting-activity-sign-ins/64.png "Attività di accesso")

Il grafico sull'utilizzo delle app visualizza le aggregazioni settimanali degli accessi per le prime 3 applicazioni in un determinato periodo di tempo. Il periodo di tempo predefinito è di 30 giorni.

![Attività di accesso](./media/active-directory-reporting-activity-sign-ins/47.png "Attività di accesso")

Se si preferisce, è possibile mettere in evidenza un'applicazione specifica.


![Report](./media/active-directory-reporting-activity-sign-ins/single_spp_usage_graph.png "Report")

Quando si fa clic su un giorno nel grafico dell'utilizzo dell'app, si ottiene un elenco dettagliato delle attività di accesso.


![Attività di accesso](./media/active-directory-reporting-activity-sign-ins/48.png "Attività di accesso")


L'opzione **Accessi** offre una panoramica completa di tutti gli eventi di accesso nell'applicazione.

![Attività di accesso](./media/active-directory-reporting-activity-sign-ins/49.png "Attività di accesso")



## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui codici di errore dell'attività di accesso, vedere [Codici di errore del report delle attività di accesso nel portale di Azure Active Directory](active-directory-reporting-activity-sign-ins-errors.md).

