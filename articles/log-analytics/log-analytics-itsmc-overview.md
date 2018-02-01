---
title: Connettore di Gestione dei servizi IT in Azure Log Analytics | Microsoft Docs
description: Questo articolo fornisce una panoramica di Connettore di Gestione dei servizi IT e informazioni su come usare questa soluzione per monitorare e gestire da una posizione centrale gli elementi di lavoro di Gestione dei servizi IT in Azure Log Analytics e risolvere rapidamente eventuali problemi.
services: log-analytics
documentationcenter: 
author: JYOTHIRMAISURI
manager: riyazp
editor: 
ms.assetid: 0b1414d9-b0a7-4e4e-a652-d3a6ff1118c4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2018
ms.author: v-jysur
ms.openlocfilehash: d586ee1b96b34d6ca83e1ffd76aee38e79bdd727
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2018
---
# <a name="connect-azure-to-itsm-tools-using-it-service-management-connector"></a>Connettere Azure agli strumenti di Gestione dei servizi IT usando Connettore di Gestione dei servizi IT

![Simbolo di Connettore di Gestione dei servizi IT](./media/log-analytics-itsmc/itsmc-symbol.png)

Connettore di Gestione dei servizi IT consente di connettere Azure e un prodotto o servizio di Gestione dei servizi IT supportato.

I servizi di Azure come Log Analytics e Monitoraggio di Azure forniscono strumenti per rilevare, analizzare e risolvere i problemi relativi a risorse di Azure e non di Azure. Gli elementi di lavoro correlati a un problema in genere si trovano tuttavia in un prodotto o un servizio di Gestione dei servizi IT. Connettore di Gestione dei servizi IT fornisce una connessione bidirezionale tra Azure e gli strumenti di Gestione dei servizi IT, per aiutare a risolvere i problemi più velocemente.

Connettore di Gestione dei servizi IT supporta le connessioni con gli strumenti di Gestione dei servizi IT seguenti:

-   ServiceNow
-   System Center Service Manager
-   Provance
-   Cherwell

Con Connettore di Gestione dei servizi IT, è possibile

-  Creare elementi di lavoro nello strumento di Gestione dei servizi IT in base agli avvisi di Azure (avvisi relativi alle metriche, avvisi del log attività e avvisi di Log Analytics).
-  Facoltativamente, è possibile sincronizzare i dati degli eventi imprevisti e delle richieste di modifica dallo strumento di Gestione dei servizi IT a un'area di lavoro di Azure Log Analytics.


È possibile iniziare a usare Connettore di Gestione dei servizi IT seguendo questa procedura:

1.  [Aggiungere la soluzione Connettore di Gestione dei servizi IT](#adding-the-it-service-management-connector-solution)
2.  [Creare una connessione di Gestione dei servizi IT](#creating-an-itsm-connection)
3.  [Usare la connessione](#using-the-solution)


##  <a name="adding-the-it-service-management-connector-solution"></a>Aggiunta della soluzione Connettore di Gestione dei servizi IT

Prima di creare una connessione, è necessario aggiungere la soluzione Connettore di Gestione dei servizi IT.

1.  Nel portale di Azure fare clic sull'icona **+ Nuovo**.

    ![Nuova risorsa di Azure](./media/log-analytics-itsmc/azure-add-new-resource.png)

2.  Cercare **Connettore di Gestione dei servizi IT** nel Marketplace e fare clic su **Crea**.

    ![Aggiungere la soluzione Connettore di Gestione dei servizi IT](./media/log-analytics-itsmc/add-itsmc-solution.png)

3.  Nella sezione **Area di lavoro di OMS** selezionare l'area di lavoro di Azure Log Analytics dove si vuole installare la soluzione.
4.  Nella sezione **Impostazioni dell'area di lavoro OMS** selezionare il gruppo di risorse in cui si vuole creare la risorsa soluzione.

    ![Area di lavoro di Connettore di Gestione dei servizi IT](./media/log-analytics-itsmc/itsmc-solution-workspace.png)

5.  Fare clic su **Crea**.

Quando la risorsa soluzione viene distribuita, viene visualizzata una notifica nella parte superiore destra della finestra.


## <a name="creating-an-itsm--connection"></a>Creazione di una connessione di Gestione dei servizi IT

Una volta installata la soluzione, è possibile creare una connessione.

Per creare una connessione, è necessario preparare lo strumento di Gestione dei servizi IT per consentire la connessione dalla soluzione Connettore di Gestione dei servizi IT.  

A seconda del prodotto di Gestione dei servizi IT a cui si connette, attenersi alla procedura descritta di seguito:

- [System Center Service Manager (SCSM)](log-analytics-itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](log-analytics-itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](log-analytics-itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Cherwell](log-analytics-itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

Una volta preparati gli strumenti di Gestione dei servizi IT, seguire la procedura seguente per creare una connessione:

1.  Passare a **Tutte le risorse** e cercare **ServiceDesk(NomeAreaDiLavoro)**.
2.  In **ORIGINI DATI DELL'AREA DI LAVORO** nel riquadro a sinistra fare clic su **Connessioni di Gestione dei servizi IT**.
    ![Connessioni di Gestione dei servizi IT](./media/log-analytics-itsmc/itsm-connections.png)

    Questa pagina mostra l'elenco di connessioni.
3.  Fare clic su **Aggiungi connessione**.

    ![Aggiungere una connessione di Gestione dei servizi IT](./media/log-analytics-itsmc/add-new-itsm-connection.png)

4.  Specificare le impostazioni di connessione come descritto nell'articolo [Configurazione della connessione di Connettore di Gestione dei servizi IT con i prodotti e i servizi di Gestione dei servizi IT](log-analytics-itsmc-connections.md).

    > [!NOTE]

    > Per impostazione predefinita, Connettore di Gestione dei servizi IT aggiorna i dati di configurazione della connessione ogni 24 ore. Per aggiornare i dati della connessione immediatamente in caso di eventuali modifiche o aggiornamenti del modello, fare clic sul pulsante "Aggiorna" posto accanto alla connessione.

    ![Aggiornamento della connessione](./media/log-analytics-itsmc/itsmc-connections-refresh.png)


## <a name="using-the-solution"></a>Uso della soluzione
   Usando la soluzione Connettore di Gestione dei servizi IT, è possibile creare elementi di lavoro da avvisi di Azure, avvisi di Log Analytics e record di log di Log Analytics.

## <a name="create-itsm-work-items-from-azure-alerts"></a>Creare elementi di lavoro di Gestione dei servizi IT da avvisi di Azure

Una volta creata la connessione di Gestione dei servizi IT, è possibile creare uno o più elementi di lavoro nello strumento di Gestione dei servizi IT in base agli avvisi di Azure tramite **ITSM Action** (Azione di Gestione dei servizi IT) in **Gruppi di azioni**.

I gruppi di azioni forniscono un modo modulare e riutilizzabile per attivare le azioni per gli avvisi di Azure. È possibile usare i gruppi di azioni con avvisi relativi alle metriche, avvisi del log attività e avvisi di Azure Log Analytics nel portale di Azure.

Utilizzare la procedura seguente:

1. Nel portale di Azure fare clic su **Monitoraggio**.
2. Nel riquadro sinistro fare clic su **Gruppi di azioni**. Viene visualizzata la finestra **Aggiungi gruppo di azioni**.

    ![Gruppi di azioni](media/log-analytics-itsmc/action-groups.png)

3. Specificare il **nome** e il **nome breve** per il gruppo di azioni. Selezionare il **gruppo di risorse** e la **sottoscrizione** in cui si vuole creare il gruppo di azioni.

    ![Dettagli dei gruppi di azioni](media/log-analytics-itsmc/action-groups-details.png)

4. Nell'elenco Azioni selezionare **Gestione dei servizi IT** dal menu a discesa **Tipo di azione**. Specificare un **nome** per l'azione e fare clic su **Modifica dettagli**.
5. Selezionare la **sottoscrizione** in cui si trova l'area di lavoro di Log Analytics. Selezionare il nome della **Connessione**, ovvero il nome di Connettore di Gestione dei servizi IT, seguito dal nome dell'area di lavoro. ad esempio "MyITSMMConnector(MyWorkspace)".

    ![Informazioni dettagliate sulle azioni ITSM](./media/log-analytics-itsmc/itsm-action-details.png)

6. Selezionare il tipo di **Elemento di lavoro** dal menu a discesa.
   Scegliere di usare un modello esistente o compilare i campi necessari per il prodotto ITSM.
7. Fare clic su **OK**.

Quando si crea/modifica una regola di avviso di Azure, usare un gruppo di azioni con un'azione Gestione dei servizi IT. Quando l'avviso viene attivato, nello strumento di Gestione dei servizi IT viene creato o aggiornato un elemento di lavoro.

>[!NOTE]

> Per informazioni sui prezzi delle azioni di Gestione dei servizi IT, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/monitor/) per i gruppi di azioni.


## <a name="create-itsm-work-items-from-log-analytics-alerts"></a>Creare elementi di lavoro di Gestione dei servizi IT da avvisi di Log Analytics

È possibile configurare le regole di avviso nel portale di Azure Log Analytics per creare elementi di lavoro nello strumento di Gestione dei servizi IT, seguendo questa procedura.

1. Dalla finestra **Ricerca Log** eseguire una query di ricerca log per visualizzare i dati. I risultati della query sono l'origine degli elementi di lavoro.
2. In **Ricerca Log** fare clic su **Avviso** per aprire la pagina **Aggiungi regola di avviso**.

    ![Schermata di Log Analytics](./media/log-analytics-itsmc/itsmc-work-items-for-azure-alerts.png)

3. Nella finestra **Aggiungi regola di avviso**, inserire i dettagli necessari per **Nome**, **Gravità**,  **Query di ricerca** e **Criteri avvisi** (misurazione dell'intervallo di tempo/metrica).
4. Selezionare **Sì** per **Azioni ITSM**.
5. Selezionare la connessione ITSM dall'elenco **Selezionare una connessione**.
6. Specificare i dettagli richiesti.
7. Per creare un elemento di lavoro separato per ogni voce di log dell'avviso, selezionare la casella di controllo **Crea elementi di lavoro singoli per ogni voce di log**.

    Oppure

    non selezionare questa casella di controllo per creare un solo elemento di lavoro per il numero di voci di log in questo avviso.

7. Fare clic su **Save**.

È possibile visualizzare l'avviso di Log Analytics creato in **Impostazioni > Avvisi**. Gli elementi di lavoro della connessione ITSM corrispondente vengono creati quando viene soddisfatta la condizione dell'avviso specificata.


## <a name="create-itsm-work-items-from-log-analytics-log-records"></a>Creare elementi di lavoro di Gestione dei servizi IT da record di log di Log Analytics

È anche possibile creare elementi di lavoro nelle origini di Connettore di Gestione dei servizi IT connesse direttamente da un record di log. Tali elementi possono essere usati per verificare se la connessione funziona correttamente.


1. Da **Ricerca Log** cercare i dati richiesti, selezionare i dettagli e fare clic su **Crea elemento di lavoro**.

    Viene visualizzata la finestra **Crea elemento di lavoro ITSM**:

    ![Schermata di Log Analytics](media/log-analytics-itsmc/itsmc-work-items-from-azure-logs.png)

2.   Aggiungere i dettagli seguenti:

  - **Titolo elemento di lavoro**: il titolo dell'elemento di lavoro.
  - **Descrizione elemento di lavoro**: descrizione per il nuovo elemento di lavoro.
  - **Computer interessato**: nome del computer in cui sono stati trovati i dati del log.
  - **Selezionare una connessione**: connessione ITSM in cui si desidera creare questo elemento di lavoro.
  - **Elemento di lavoro**: tipo di elemento di lavoro.

3. Per usare un modello di elemento di lavoro esistente per un evento imprevisto, fare clic su **Sì** nell'opzione **Genera elemento di lavoro in base al modello** e quindi fare clic su **Crea**.

    Oppure

    Fare clic su **No** se si desidera specificare valori personalizzati.

4. Inserire i valori appropriati nelle caselle di testo **Tipo di contatto**, **Impatto**, **Urgenza**, **Categoria** e **Sottocategoria** e quindi fare clic su **Crea**.


##<a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Visualizzare e analizzare i dati degli eventi imprevisti e delle richieste di modifica

In base alla configurazione quando si imposta una connessione, Connettore di Gestione dei servizi IT può sincronizzare fino a 120 giorni di dati degli eventi imprevisti e delle richieste di modifica. Lo schema dei record di log per questi dati è indicato nella [sezione successiva](#additional-information).

I dati degli eventi imprevisti e delle richieste di modifica possono essere visualizzati usando il dashboard di Connettore di Gestione dei servizi IT nella soluzione.

![Schermata di Log Analytics](./media/log-analytics-itsmc/itsmc-overview-sample-log-analytics.png)

Il dashboard fornisce anche informazioni sullo stato del connettore, che è possibile usare come punto di partenza per analizzare i problemi con le connessioni.

È anche possibile visualizzare gli eventi imprevisti sincronizzati in relazione ai computer interessati, all'interno della soluzione Mapping dei servizi.

Elenco dei servizi individua automaticamente i componenti delle applicazioni nei sistemi Windows e Linux ed esegue la mappatura della comunicazione fra i servizi. Consente di visualizzare i server nel modo in cui si pensa a essi, ovvero come sistemi interconnessi che forniscono servizi critici. Mapping dei servizi mostra le connessioni fra i server, i processi e le porte di tutte le architetture connesse via TCP senza il bisogno di alcuna configurazione a parte l'installazione di un agente. [Altre informazioni](../operations-management-suite/operations-management-suite-service-map.md).

Se si usa anche la soluzione Mapping dei servizi, è possibile visualizzare gli elementi del service desk creati nelle soluzioni di Gestione dei servizi IT, come illustrato nell'esempio seguente:

![Schermata di Log Analytics](./media/log-analytics-itsmc/itsmc-overview-integrated-solutions.png)

Altre informazioni: [Mapping dei servizi](../operations-management-suite/operations-management-suite-service-map.md)


## <a name="additional-information"></a>Informazioni aggiuntive

### <a name="data-synced-from-itsm-product"></a>Dati sincronizzati dal prodotto ITSM
Gli eventi imprevisti e le richieste di modifica vengono sincronizzati dal prodotto di Gestione dei servizi IT all'area di lavoro di Log Analytics in base alla configurazione della connessione.

Le informazioni seguenti mostrano esempi di dati raccolti da Connettore di Gestione dei servizi IT:

> [!NOTE]

> A seconda del tipo di elemento di lavoro importato in Log Analytics, **ServiceDesk_CL** contiene i campi seguenti:

**Elemento di lavoro:** **Eventi imprevisti**  
ServiceDeskWorkItemType_s="Incident"

**Fields**

- ServiceDeskConnectionName
- ID Service Desk
- Stato
- Urgenza
- Impatto
- Priorità
- Riassegnazione
- Created By (Creato da)
- Resolved By (Risolto da)
- Closed By (Chiuso da)
- Sorgente
- Assegnato a 
- Categoria
- Titolo
- DESCRIZIONE
- Data di creazione
- Data di chiusura
- Data di risoluzione
- Data ultima modifica
- Computer


**Elemento di lavoro:****Richieste di modifica**

ServiceDeskWorkItemType_s="ChangeRequest"

**Fields**
- ServiceDeskConnectionName
- ID Service Desk
- Created By (Creato da)
- Closed By (Chiuso da)
- Sorgente
- Assegnato a 
- Titolo
- type
- Categoria
- Stato
- Riassegnazione
- Conflict Status (Stato di conflitto)
- Urgenza
- Priorità
- Rischio
- Impatto
- Assegnato a 
- Data di creazione
- Data di chiusura
- Data ultima modifica
- Data richiesta
- Planned Start Date (Data di inizio pianificata)
- Planned End Date (Data di fine pianificata)
- Work Start Date (Data di inizio lavoro)
- Work End Date (Data di fine pianificata)
- DESCRIZIONE
- Computer

## <a name="output-data-for-a-servicenow-incident"></a>Dati di output per un evento imprevisto ServiceNow

| Campo Log Analytics | Campo di ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Number |
| IncidentState_s | Stato |
| Urgency_s |Urgenza |
| Impact_s |Impatto|
| Priority_s | Priorità |
| CreatedBy_s | Aperto da |
| ResolvedBy_s | Risolto da|
| ClosedBy_s  | Chiuso da |
| Source_s| Tipo di contatto |
| AssignedTo_s | Assegnato a  |
| Category_s | Categoria |
| Title_s|  Breve descrizione |
| Description_s|  Note |
| CreatedDate_t|  Aperto |
| ClosedDate_t| closed|
| ResolvedDate_t|Risolto|
| Computer  | Elemento di configurazione |

## <a name="output-data-for-a-servicenow-change-request"></a>Dati di output per una richiesta di modifica ServiceNow

| Log Analytics | Campo di ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Number |
| CreatedBy_s | Richiesto da |
| ClosedBy_s | Chiuso da |
| AssignedTo_s | Assegnato a  |
| Title_s|  Breve descrizione |
| Type_s|  type |
| Category_s|  Categoria |
| CRState_s|  Stato|
| Urgency_s|  Urgenza |
| Priority_s| Priorità|
| Risk_s| Rischio|
| Impact_s| Impatto|
| RequestedDate_t  | Data richiesta |
| ClosedDate_t | Data di chiusura |
| PlannedStartDate_t  |     Data di inizio pianificata |
| PlannedEndDate_t  |   Data di fine pianificata |
| WorkStartDate_t  | Data di inizio effettiva |
| WorkEndDate_t | Data di fine effettiva|
| Description_s | DESCRIZIONE |
| Computer  | Elemento di configurazione |


## <a name="troubleshoot-itsm-connections"></a>Risolvere i problemi delle connessioni di Gestione dei servizi IT
1.  Se si verifica un errore di connessione nell'interfaccia utente dell'origine connessa con un messaggio **Errore durante il salvataggio della connessione**, seguire questa procedura:
- Per le connessioni ServiceNow, Cherwell e Provance,  
       - assicurarsi di immettere correttamente il nome utente, la password, l'ID client e il segreto client per ognuna delle connessioni.  
       - controllare se si hanno privilegi sufficienti nel prodotto Gestione dei servizi IT corrispondente per stabilire la connessione.  
- Per le connessioni Service Manager,  
       - verificare che l'app Web sia stata distribuita correttamente e che la connessione ibrida sia stata creata. Per verificare che la connessione sia stata stabilita con il computer Service Manager on-premises, visitare l'URL dell'app Web come descritto nella documentazione per la creazione della [connessione ibrida](log-analytics-itsmc-connections.md#configure-the-hybrid-connection).  

2.  Se i dati provenienti da ServiceNow non vengono sincronizzati con Log Analytics, assicurarsi che l'istanza del servizio ServiceNow non sia sospesa. Le istanze di sviluppo di ServiceNow a volte vanno in sospensione se rimangono inattive a lungo. In caso contrario, segnalare il problema.
3.  Se gli avvisi OMS vengono generati, ma gli elementi di lavoro non vengono creati nel prodotto ITSM o gli elementi di configurazione non vengono creati/collegati a elementi di lavoro o per qualsiasi altra informazione generica, esaminare le posizioni seguenti:
 -  Gestione dei servizi IT: la soluzione mostra un riepilogo delle connessioni/elementi di lavoro/computer e così via. Fare clic sul riquadro contenente **Stato connettore** per passare a **Ricerca log** con la query pertinente. Esaminare i record di log con ERROR LogType_S per altre informazioni.
 - Pagina **Ricerca log**: consente di visualizzare gli errori o le informazioni correlate direttamente usando la query *Type=ServiceDeskLog_CL*.

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Risolvere i problemi di distribuzione dell’app Web Service Manager
1.  In caso di problemi con la distribuzione dell'app Web, assicurarsi di disporre di autorizzazioni sufficienti nella sottoscrizione indicata per creare/distribuire risorse.
2.  Se viene visualizzato un errore **"Riferimento a oggetto non impostato sull'istanza di un oggetto"** quando si esegue lo [script](log-analytics-itsmc-service-manager-script.md), assicurarsi di aver immesso valori validi nella sezione **Configurazione utente**.
3.  Se non è possibile creare lo spazio dei nomi di inoltro del bus di servizio, assicurarsi che il provider di risorse richiesto sia registrato nella sottoscrizione. Qualora non fosse registrato, creare manualmente lo spazio dei nomi di inoltro del bus di servizio dal portale di Azure. È inoltre possibile crearlo durante [la creazione della connessione ibrida](log-analytics-itsmc-connections.md#configure-the-hybrid-connection) dal portale di Azure.


## <a name="contact-us"></a>Contatti

Per eventuali domande o commenti e suggerimenti su Connettore di Gestione dei servizi IT, è possibile contattare [omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com).

## <a name="next-steps"></a>Passaggi successivi
[Aggiungere prodotti o servizi ITSM a IT Service Management Connector](log-analytics-itsmc-connections.md).
