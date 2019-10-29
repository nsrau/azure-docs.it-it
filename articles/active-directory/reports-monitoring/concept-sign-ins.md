---
title: Report delle attività di accesso nel portale di Azure Active Directory | Microsoft Docs
description: Introduzione ai report delle attività di accesso nel portale di Azure Active Directory
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 10/28/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 553d882600203d0fa01cd876562d9ced7985d7a8
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73043681"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Report delle attività di accesso nel portale di Azure Active Directory

L'architettura di report in Azure Active Directory (Azure AD) include i componenti seguenti:

- **Attività** 
    - **Accessi**: informazioni sull'uso delle applicazioni gestite e sulle attività di accesso degli utenti.
    - I **log di controllo** - log di [controllo](concept-audit-logs.md) forniscono informazioni sulle attività di sistema relative agli utenti e alla gestione dei gruppi, alle applicazioni gestite e alle attività di directory.
- **Sicurezza** 
    - **Accessi a rischio** : un [accesso rischioso](concept-risky-sign-ins.md) è un indicatore del tentativo di accesso da parte di un utente che non è il proprietario legittimo di un account utente.
    - **Utenti contrassegnati per il rischio**: un [utente a rischio](concept-user-at-risk.md) indica un account utente che potrebbe essere stato compromesso.

Questo articolo fornisce una panoramica del report degli accessi.

## <a name="prerequisites"></a>Prerequisiti

### <a name="who-can-access-the-data"></a>Chi può accedere ai dati?

* Utenti nei ruoli amministratore sicurezza, lettore sicurezza e lettore report
* Amministratori globali
* Qualsiasi utente (non amministratore) può visualizzare i propri accessi 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Quale licenza di Azure AD è necessaria per visualizzare le attività di accesso?

* Per visualizzare il report completo delle attività di accesso, è necessario che al tenant sia associata una licenza di Azure AD Premium. Per l'aggiornamento dell'edizione Azure Active Directory, vedere [Introduzione a Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) . Verranno visualizzati alcuni giorni prima che i dati vengano visualizzati nei report dopo l'aggiornamento a una licenza Premium senza attività di dati prima dell'aggiornamento.

## <a name="sign-ins-report"></a>Report sugli accessi

Il report relativo agli accessi utente fornisce le risposte alle domande seguenti:

* Qual è il modello di accesso di un utente?
* Quanti utenti hanno effettuato l'accesso nell'arco di una settimana?
* Qual è lo stato di questi accessi?

Iniziare con [portale di Azure](https://portal.azure.com). Per accedere al report degli accessi, **selezionare**accessi, continuare con il **monitoraggio.** Potrebbero essere necessarie fino a due ore per la visualizzazione di alcuni record di accesso nel portale.

![Attività di accesso](./media/concept-sign-ins/reporting-azure-sign-in-screen.png "Attività di accesso")

> [!IMPORTANT]
> Il report degli accessi mostra solo gli accessi **interattivi**, ovvero gli accessi eseguiti manualmente da un utente usando nome utente e password. Gli accessi non interattivi, ad esempio l'autenticazione da servizio a servizio, non vengono visualizzati nel report degli accessi. 

Un log di accesso ha una visualizzazione elenco predefinita che include:

- Data di accesso
- Utente correlato
- Applicazione a cui l'utente ha effettuato l'accesso
- Stato dell'accesso
- Stato di rilevamento rischi
- Stato del requisito di autenticazione a più fattori (MFA)

![Attività di accesso](./media/concept-sign-ins/sign-in-activity.png "Attività di accesso")

Per personalizzare la visualizzazione elenco, fare clic su **Colonne** nella barra degli strumenti.

![Attività di accesso](./media/concept-sign-ins/19.png "Attività di accesso")

Visualizza campi aggiuntivi o Rimuovi campi già visualizzati.

![Attività di accesso](./media/concept-sign-ins/02.png "Attività di accesso")

Selezionare un elemento nella visualizzazione elenco per ottenere maggiori informazioni dettagliate.

![Attività di accesso](./media/concept-sign-ins/basic-sign-in.png "Attività di accesso")

> [!NOTE]
> I clienti possono ora risolvere i problemi relativi ai criteri di accesso condizionale tramite tutti i report di accesso. Facendo clic sulla scheda **accesso condizionale** per un record di accesso, i clienti possono esaminare lo stato di accesso condizionale e approfondire i dettagli dei criteri applicati all'accesso e il risultato per ogni criterio.
> Per altre informazioni, vedere [Domande frequenti sulle informazioni di CA in tutti gli accessi](reports-faq.md#conditional-access).



## <a name="filter-sign-in-activities"></a>Filtrare le attività di accesso

Per prima cosa, limitare i dati segnalati a un livello che funziona per l'utente. In secondo luogo, filtrare i dati degli accessi usando il campo data come filtro predefinito. Azure AD offre un'ampia gamma di filtri aggiuntivi che è possibile impostare.

![Attività di accesso](./media/concept-sign-ins/04.png "Attività di accesso")

Il filtro **Utente** permette di specificare il nome o il nome dell'entità utente (UPN) per l'utente richiesto.

Il filtro **Applicazione** permette di specificare il nome dell'applicazione richiesta.

Il filtro **Stato accesso** permette di selezionare:

- Tutto
- Success
- Esito negativo

Il filtro **Accesso condizionale** consente di selezionare lo stato dei criteri di accesso condizionale per l'accesso:

- Tutto
- Non applicato
- Success
- Esito negativo

Il filtro **Date** (Data) permette di definire un intervallo di tempo per i dati restituiti.  
I valori possibili sono:

- Un mese
- 7 giorni
- 24 ore
- Intervallo di tempo personalizzato

Quando si seleziona un intervallo di tempo personalizzato, è possibile configurare un'ora di inizio e un'ora di fine.

Se si aggiungono altri campi alla visualizzazione degli accessi, questi campi verranno aggiunti automaticamente all'elenco dei filtri. Ad esempio, se si aggiunge il campo **App client** all'elenco, si otterrà un'altra opzione di filtro che consente di impostare i filtri seguenti:  
![Attività di accesso](./media/concept-sign-ins/12.png "Attività di accesso")

- **Browser**  
    Questo filtro Mostra tutti gli eventi in cui sono stati tentati tentativi di accesso usando i flussi del browser.
- **Exchange ActiveSync (supportato)**  
    Questo filtro Mostra tutti i tentativi di accesso in cui è stato effettuato il tentativo di eseguire il protocollo di Exchange ActiveSync (EAS) dalle piattaforme supportate come iOS, Android e Windows Phone.
- **Exchange ActiveSync (non supportato)**  
    Questo filtro Mostra tutti i tentativi di accesso in cui il protocollo EAS è stato provato da piattaforme non supportate come le distribuzioni Linux.
- **App per dispositivi mobili e client desktop** Il filtro Mostra tutti i tentativi di accesso che non usavano i flussi del browser. Ad esempio, app per dispositivi mobili da qualsiasi piattaforma che usa qualsiasi protocollo o da app client desktop come Office in Windows o MacOS.
  
- **Altri client**
    - **IMAP**  
        Un client di posta legacy che usa IMAP per recuperare la posta elettronica.
    - **MAPI**  
        Office 2013, in cui ADAL è abilitato e utilizza MAPI.
    - **Client Office precedenti**  
        Office 2013 nella configurazione predefinita in cui ADAL non è abilitato ed è in uso MAPI oppure Office 2016 dove ADAL è stato disabilitato.
    - **POPUP**  
        Un client di posta legacy che usa POP3 per recuperare la posta elettronica.
    - **SMTP**  
        Un client di posta legacy che usa SMTP per inviare messaggi di posta elettronica.

## <a name="download-sign-in-activities"></a>Scaricare le attività di accesso

Fare clic sull'opzione di **download** per creare un file CSV o JSON dei record 250.000 più recenti. Iniziare a [scaricare i dati di accesso](quickstart-download-sign-in-report.md) se si vuole usarli al di fuori della portale di Azure.  

![Scaricare](./media/concept-sign-ins/71.png "Download")

> [!IMPORTANT]
> Il numero di record che è possibile scaricare è limitato dai [criteri di conservazione dei report di Azure Active Directory](reference-reports-data-retention.md).  


## <a name="sign-ins-data-shortcuts"></a>Tasti di scelta rapida per i dati degli accessi

Azure AD e i portale di Azure forniscono punti di ingresso aggiuntivi per i dati di accesso:

- Panoramica Identity Security e Protection
- Utenti
- Gruppi
- Applicazioni aziendali

### <a name="users-sign-ins-data-in-identity-security-protection"></a>Dati degli accessi degli utenti in Identity Security e Protection

Il grafo di accesso utente nella pagina Panoramica della **protezione delle identità di sicurezza** Mostra le aggregazioni settimanali degli accessi. Il valore predefinito per il periodo di tempo è 30 giorni.

![Attività di accesso](./media/concept-sign-ins/06.png "Attività di accesso")

Quando si fa clic su un giorno nel grafico degli accessi, si ottiene una panoramica delle attività di accesso per tale giorno.

Ogni riga dell'elenco delle attività di accesso mostra:

* Chi ha effettuato l'accesso?
* Qual era l'applicazione di destinazione dell'accesso?
* Qual è lo stato dell'accesso?
* Qual è lo stato MFA dell'accesso?

Facendo clic su un elemento, si ottengono altri dettagli sull'operazione di accesso:

- ID utente
- Utente
- Username
- ID applicazione
- Richiesta
- Client
- Località
- Indirizzo IP
- Data
- Autenticazione a più fattori obbligatoria
- Stato accesso

> [!NOTE]
> gli indirizzi IP vengono rilasciati in modo che non esista una connessione certa tra un IP e la posizione in cui si trova fisicamente il computer con tale indirizzo. Il mapping degli indirizzi IP è complicato dal fatto che provider di telefonia mobile e VPN possono rilasciare indirizzi IP da pool centrali spesso molto distanti dal luogo in cui viene effettivamente usato il dispositivo client. Attualmente nei report di Azure AD la conversione di un indirizzo IP in una posizione fisica è un'approssimazione basata su tracce, dati del Registro di sistema, ricerche inverse e altre informazioni.

Nella pagina **Utenti** è possibile accedere a una panoramica completa di tutti i accessi degli utenti facendo clic su **Accessi** nella sezione **Attività**.

![Attività di accesso](./media/concept-sign-ins/08.png "Attività di accesso")

## <a name="usage-of-managed-applications"></a>Utilizzo di applicazioni gestite

Con una visualizzazione dei dati di accesso basata sulle applicazioni, è possibile rispondere a domande come:

* Chi sta usando le applicazioni?
* Quali sono le prime tre applicazioni nell'organizzazione?
* In che modo l'applicazione più recente?

Il punto di ingresso a questi dati è le prime tre applicazioni dell'organizzazione. I dati sono contenuti nel report ultimi 30 giorni della sezione **Panoramica** in **applicazioni aziendali**.

![Attività di accesso](./media/concept-sign-ins/10.png "Attività di accesso")

I grafici dell'utilizzo delle app sono aggregazioni settimanali degli accessi per le prime tre applicazioni in un determinato periodo di tempo. Il periodo di tempo predefinito è di 30 giorni.

![Attività di accesso](./media/concept-sign-ins/graph-chart.png "Attività di accesso")

Se si preferisce, è possibile mettere in evidenza un'applicazione specifica.

![Creazione di report](./media/concept-sign-ins/single-app-usage-graph.png "Creazione report")

Quando si fa clic su un giorno nel grafico dell'utilizzo dell'app, si ottiene un elenco dettagliato delle attività di accesso.

L'opzione **Accessi** offre una panoramica completa di tutti gli eventi di accesso nell'applicazione.

## <a name="office-365-activity-logs"></a>Log attività di Office 365

È possibile visualizzare i log attività di Office 365 dall'interfaccia di [amministrazione di Microsoft 365](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). Si consideri il fatto che l'attività di Office 365 e i log attività Azure AD condividono un numero significativo di risorse di directory. Solo l'interfaccia di amministrazione di Microsoft 365 fornisce una visualizzazione completa dei log attività di Office 365. 

È anche possibile accedere ai log attività di Office 365 a livello di codice usando le [API di gestione di office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Passaggi successivi

* [Codici di errore del report delle attività di accesso](reference-sign-ins-error-codes.md)
* [Criteri di conservazione dei report di Azure AD](reference-reports-data-retention.md)
* [Latenze dei report di Azure AD](reference-reports-latencies.md)

