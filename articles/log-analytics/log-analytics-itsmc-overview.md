---
title: IT Service Management Connector in Azure Log Analytics | Microsoft Docs
description: Questo articolo fornisce una panoramica del Connettore della gestione dei servizi IT (ITSMC) e delle informazioni su come usare questa soluzione per monitorare e gestire centralmente gli elementi di lavoro di ITSM in OMS Log Analytics e risolvere rapidamente eventuali problemi.
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
ms.date: 06/19/2017
ms.author: v-jysur
ms.openlocfilehash: bd384255b3c46b3ae88b1269ab26e0ddaa6f6e77
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2017
---
# <a name="centrally-manage-itsm-work-items-using-it-service-management-connector-preview"></a>Gestire centralmente gli elementi di lavoro ITSM con IT Service Management Connector (anteprima)

![Simbolo di IT Service Management Connector](./media/log-analytics-itsmc/itsmc-symbol.png)

Connettore di Gestione dei servizi IT offre un'integrazione bidirezionale tra un prodotto/servizio Gestione dei servizi IT supportato e Log Analytics.  Grazie a questa connessione, è possibile creare eventi imprevisti, avvisi o eventi in un prodotto Gestione dei servizi IT in base ad avvisi o record di log di Log Analytics o avvisi di Azure. Il connettore importa anche dati, ad esempio eventi imprevisti e richieste di modifica dal prodotto ITSM in Log Analytics di OMS.

Con Gestione dei servizi IT è possibile:

  - Integrare gli avvisi operativi con le procedure di gestione degli eventi imprevisti nello strumento Gestione dei servizi IT di propria scelta.
    - Creare elementi di lavoro, ad esempio avvisi, eventi, eventi imprevisti, in ITSM dagli avvisi OMS e tramite la ricerca log.
    - Creare elementi di lavoro basati sugli avvisi del log attività di Azure tramite un'azione ITSM nei gruppi di azioni.

  - Unificare i dati di monitoraggio, dei log e di gestione dei servizi usati nell'organizzazione.
    - Correlare i dati degli eventi imprevisti e delle richieste di modifica dagli strumenti ITSM con i dati di log rilevanti nell'area di lavoro di Log Analytics.   
    - Visualizzare i dashboard di primo livello per una panoramica di eventi imprevisti, richieste di modifica e sistemi interessati.
    - Scrivere query di Log Analytics per ottenere informazioni dettagliate sui dati di gestione dei servizi.

## <a name="adding-the-it-service-management-connector-solution"></a>Aggiunta della soluzione IT Service Management Connector

Aggiungere la soluzione IT Service Management Connector all'area di lavoro di Log Analytics usando la procedura descritta in [Aggiungere soluzioni di Log Analytics dalla raccolta soluzioni](log-analytics-add-solutions.md).

Ecco il riquadro di Gestione dei servizi IT come appare nella Raccolta soluzioni:

![riquadro connettore](./media/log-analytics-itsmc/itsmc-solutions-tile.png)

Dopo averlo aggiunto correttamente, IT Service Management Connector viene visualizzato in **OMS** > **Impostazioni** > **Origini connesse.**

![ITSMC connessi](./media/log-analytics-itsmc/itsmc-overview-solution-in-connected-sources.png)

> [!NOTE]

> Per impostazione predefinita, Gestione dei servizi IT aggiorna i dati della connessione ogni 24 ore. Per aggiornare i dati della connessione immediatamente in caso di eventuali modifiche o aggiornamenti del modello, fare clic sul pulsante "Aggiorna" posto accanto alla connessione.

 ![Aggiornamento di ITSMC refresh](./media/log-analytics-itsmc/itsmc-connection-refresh.png)


## <a name="configuring-the-itsmc-connection-with-your-itsm-productsservices"></a>Configurazione della connessione ITSMC con i prodotti/servizi ITSM

Gestione dei servizi IT supporta le connessioni a **System Center Service Manager**, **ServiceNow**, **Provance** e **Cherwell**.

Usare le procedure seguenti in base alle proprie esigenze:

- [System Center Service Manager (SCSM)](log-analytics-itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-oms)

- [ServiceNow](log-analytics-itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-oms)

- [Provance](log-analytics-itsmc-connections.md#connect-provance-to-it-service-management-connector-in-oms)  

- [Cherwell](log-analytics-itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-oms)

## <a name="using-the-solution"></a>Uso della soluzione

Dopo averlo configurato, il connettore avvia la raccolta di dati dal prodotto/servizo Gestione dei servizi IT connesso. A seconda del numero di eventi imprevisti e di richieste di modifica nel prodotto/servizio Gestione dei servizi IT, la sincronizzazione iniziale verrà completata entro pochi minuti.

> [!NOTE]
> - I dati importati dal prodotto Gestione dei servizi IT con la soluzione Connettore di Gestione dei servizi IT vengono visualizzati in Log Analytics come record di log di tipo **ServiceDesk_CL**.
> - Un record di log contiene un campo denominato **ServiceDeskWorkItemType_s**, che è un evento imprevisto o una richiesta di modifica, i due tipi di dati importati dal prodotto Gestione dei servizi IT.

## <a name="data-synced-from-itsm-product"></a>Dati sincronizzati dal prodotto ITSM
La sincronizzazione di eventi imprevisti e richieste di modifica viene eseguita dal prodotto ITSM all'area di lavoro di Log Analytics.
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
- Descrizione
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
- Tipo
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
- Descrizione
- Computer

## <a name="output-data-for-a-servicenow-incident"></a>Dati di output per un evento imprevisto ServiceNow

| Campo OMS | Campo ITSM |
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

| Campo OMS | Campo ITSM |
|:--- |:--- |
| ServiceDeskId_s| Number |
| CreatedBy_s | Richiesto da |
| ClosedBy_s | Chiuso da |
| AssignedTo_s | Assegnato a  |
| Title_s|  Breve descrizione |
| Type_s|  Tipo |
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
| Description_s | Descrizione |
| Computer  | Elemento di configurazione |

**Schermata di esempio di Log Analytics per i dati ITSM:**

![Schermata di Log Analytics](./media/log-analytics-itsmc/itsmc-overview-sample-log-analytics.png)

## <a name="itsmc-integration-with-other-oms-solutions"></a>Integrazione di Connettore di Gestione dei servizi IT con altre soluzioni OMS

Il Connettore di Gestione dei servizi IT supporta attualmente l'integrazione con la soluzione Mapping dei servizi.

Elenco dei servizi individua automaticamente i componenti delle applicazioni nei sistemi Windows e Linux ed esegue la mappatura della comunicazione fra i servizi. Consente di visualizzare i server nel modo in cui si pensa a essi, ovvero come sistemi interconnessi che forniscono servizi critici. L'elenco dei servizi mostra le connessioni fra i server, i processi e le porte di tutte le architetture connesse via TCP senza il bisogno di alcuna configurazione a parte l'installazione di un agente.

Altre informazioni: [Elenco dei servizi](../operations-management-suite/operations-management-suite-service-map.md).

Se si usa anche la soluzione Mapping dei servizi, è possibile visualizzare gli elementi del service desk creati nelle soluzioni ITSM come illustrato nell'esempio seguente:

![Integrazione di Mapping dei servizi](./media/log-analytics-itsmc/itsmc-overview-integrated-solutions.png)
## <a name="create-itsm-work-items-for-oms-alerts"></a>Creare elementi di lavoro ITSM per avvisi di OMS

Con la soluzione Connettore di Gestione dei servizi IT disponibile, è possibile configurare Avvisi OMS per attivare la creazione di elementi di lavoro nello strumento del Connettore di Gestione dei servizi IT connesso. Usare la procedura seguente:

1. Dalla finestra **Ricerca Log** eseguire una query di ricerca log per visualizzare i dati. I risultati della query sono l'origine degli elementi di lavoro.
2. In **Ricerca Log** fare clic su **Avviso** per aprire la pagina **Aggiungi regola di avviso**.

    ![Schermata di Log Analytics](./media/log-analytics-itsmc/itsmc-work-items-for-oms-alerts.png)

3. Nella finestra **Aggiungi regola di avviso**, inserire i dettagli necessari per **Nome**, **Gravità**,  **Query di ricerca** e **Criteri avvisi** (misurazione dell'intervallo di tempo/metrica).
4. Selezionare **Sì** per **Azioni ITSM**.
5. Selezionare la connessione ITSM dall'elenco **Selezionare una connessione**.
6. Specificare i dettagli richiesti.
7. Per creare un elemento di lavoro separato per ogni voce di log dell'avviso, selezionare la casella di controllo **Crea elementi di lavoro singoli per ogni voce di log**.

    oppure

    non selezionare questa casella di controllo per creare un solo elemento di lavoro per il numero di voci di log in questo avviso.

7. Fare clic su **Salva**.

L'avviso OMS creato può essere visualizzato in **Impostazioni**>**Avvisi**. Gli elementi di lavoro della connessione ITSM corrispondente vengono creati quando viene soddisfatta la condizione dell'avviso specificata.

## <a name="create-itsm-work-items-from-oms-logs"></a>Creare elementi di lavoro ITSM da log di OMS

È anche possibile creare elementi di lavoro nelle origini del Connettore di Gestione dei servizi IT connesse direttamente da un record di log. Usare la procedura seguente:

1. Da **Ricerca Log** cercare i dati richiesti, selezionare i dettagli e fare clic su **Crea elemento di lavoro**.

    Viene visualizzata la finestra **Crea elemento di lavoro ITSM**:

    ![Schermata di Log Analytics](media/log-analytics-itsmc/itsmc-work-items-from-oms-logs.png)

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

## <a name="create-itsm-work-items-from-azure-alerts"></a>Creare elementi di lavoro di Gestione dei servizi IT dagli avvisi di Azure

Gestione dei servizi IT è integrato con Gruppi di azioni.

I [gruppi di azioni](../monitoring-and-diagnostics/monitoring-action-groups.md) consentono di attivare azioni modulari e riutilizzabili per gli avvisi di Azure. L'azione ITSM nei Gruppi di azioni consente di creare elementi di lavoro nel prodotto Gestione dei servizi IT dotato di una connessione esistente alla soluzione Connettore di Gestione dei servizi IT.

Usare la procedura seguente:

1. Nel portale di Azure fare clic su **Monitoraggio**.
2. Nel riquadro sinistro fare clic su **Gruppi di azioni**. Viene visualizzata la finestra **Aggiungi gruppo di azioni**.

    ![Gruppi di azioni](media/log-analytics-itsmc/action-groups.png)

3. Specificare il **nome** e il **nome breve** per il gruppo di azioni. Selezionare il **gruppo di risorse** e la **sottoscrizione** in cui si vuole creare il gruppo di azioni.

    ![Dettagli dei gruppi di azioni](media/log-analytics-itsmc/action-groups-details.png)

4. Nell'elenco Azioni selezionare **Gestione dei servizi IT** dal menu a discesa **Tipo di azione**. Specificare un **nome** per l'azione e fare clic su **Modifica dettagli**.
5. Selezionare la **sottoscrizione** in cui si trova l'area di lavoro di Log Analytics. Selezionare il nome della **Connessione**, ovvero il nome del Connettore di Gestione dei servizi IT, seguito dal nome dell'area di lavoro. ad esempio "MyITSMMConnector(MyWorkspace)".

    ![Informazioni dettagliate sulle azioni ITSM](./media/log-analytics-itsmc/itsm-action-details.png)

6. Selezionare il tipo di **Elemento di lavoro** dal menu a discesa.
   Scegliere di usare un modello esistente o compilare i campi necessari per il prodotto ITSM.
7. Fare clic su **OK**.

Quando si crea/modifica una regola di avviso di Azure, usare un gruppo di azioni con un'azione Gestione dei servizi IT. Quando l'avviso viene attivato, nello strumento ITSM viene creato l'elemento di lavoro.

>[!NOTE]

> Attualmente, solo gli avvisi del log attività supportano l'azione Gestione dei servizi IT, gli altri avvisi di Azure non la supportano.


## <a name="troubleshoot-itsm-connections-in-oms"></a>Risolvere i problemi delle connessioni ITSM in OMS
1.  Se si verifica un errore di connessione nell'interfaccia utente dell'origine connessa con un messaggio **Errore durante il salvataggio della connessione**, seguire questa procedura:
 - In caso di connessioni di ServiceNow, Cherwell e Provance, assicurarsi di immettere correttamente il nome utente, la password, l'ID e il segreto client per ognuna delle connessioni.
        - controllare se si hanno privilegi sufficienti nel prodotto Gestione dei servizi IT corrispondente per stabilire la connessione.
 - Per le connessioni di Service Manager, verificare che l'app Web sia stata distribuita correttamente e che la connessione ibrida sia stata creata. Per verificare che la connessione sia stata stabilita con il computer Service Manager on-premises, visitare l'URL dell'app Web come descritto nella documentazione per la creazione della [connessione ibrida](log-analytics-itsmc-connections.md#configure-the-hybrid-connection).

2.  Se i dati provenienti da ServiceNow non vengono sincronizzati con Log Analytics, assicurarsi che l'istanza del servizio ServiceNow non sia sospesa. Le istanze di sviluppo di ServiceNow a volte vanno in sospensione se rimangono inattive a lungo. In caso contrario, segnalare il problema.
3.  Se gli avvisi OMS vengono generati, ma gli elementi di lavoro non vengono creati nel prodotto ITSM o gli elementi di configurazione non vengono creati/collegati a elementi di lavoro o per qualsiasi altra informazione generica, esaminare le posizioni seguenti:
 -  Gestione dei servizi IT: la soluzione mostra un riepilogo delle connessioni/elementi di lavoro/computer e così via. Fare clic sul riquadro contenente **Stato connettore** per passare a **Ricerca log** con la query pertinente. Esaminare i record di log con ERROR LogType_S per altre informazioni.
 - Pagina **Ricerca log**: consente di visualizzare gli errori o le informazioni correlate direttamente usando la query *Type=ServiceDeskLog_CL*.

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Risolvere i problemi di distribuzione dell’app Web Service Manager
1.  In caso di problemi con la distribuzione dell'app Web, assicurarsi di disporre di autorizzazioni sufficienti nella sottoscrizione indicata per creare/distribuire risorse.
2.  Se viene visualizzato un errore **"Riferimento a oggetto non impostato sull'istanza di un oggetto"** quando si esegue lo [script](log-analytics-itsmc-service-manager-script.md), assicurarsi di aver immesso valori validi nella sezione **Configurazione utente**.
3.  Se non è possibile creare lo spazio dei nomi di inoltro del bus di servizio, assicurarsi che il provider di risorse richiesto sia registrato nella sottoscrizione. Qualora non fosse registrato, creare manualmente lo spazio dei nomi di inoltro del bus di servizio dal portale di Azure. È inoltre possibile crearlo durante [la creazione della connessione ibrida](log-analytics-itsmc-connections.md#configure-the-hybrid-connection) dal portale di Azure.


## <a name="contact-us"></a>Contatti

Per eventuali domande o commenti e suggerimenti su IT Service Management Connector, è possibile contattare [omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com).

## <a name="next-steps"></a>Passaggi successivi
[Aggiungere prodotti o servizi ITSM a IT Service Management Connector](log-analytics-itsmc-connections.md).
