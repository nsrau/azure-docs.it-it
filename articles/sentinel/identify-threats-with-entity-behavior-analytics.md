---
title: Identificare le minacce avanzate con l'analisi del comportamento dell'utente e dell'entità (offrono dati) in Sentinel di Azure | Microsoft Docs
description: Creare linee di base comportamentali per le entità (utenti, nomi host, indirizzi IP) e usarle per rilevare il comportamento anomalo e identificare le minacce persistenti (APT) di zero giorni.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/19/2020
ms.author: yelevin
ms.openlocfilehash: 6597baa67bcd2e26f3b8aeaa98c1776b5fc47430
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90997145"
---
# <a name="identify-advanced-threats-with-user-and-entity-behavior-analytics-ueba-in-azure-sentinel"></a>Identificare le minacce avanzate con l'analisi del comportamento dell'utente e dell'entità (offrono dati) in Sentinel di Azure

## <a name="what-is-user-and-entity-behavior-analytics-ueba"></a>Che cos'è l'analisi del comportamento dell'utente e dell'entità (offrono dati)?

### <a name="the-concept"></a>Il concetto

Identificare le minacce all'interno dell'organizzazione e il loro impatto potenziale, a prescindere dal fatto che un'entità compromessa o un insider malintenzionato, sia sempre stato un processo che richiede molto tempo e laborioso. Il setaccio degli avvisi, la connessione dei punti e la ricerca attiva vengono tutti aggiunti a grandi quantità di tempo e impegno con una redditività minima e la possibilità di minacce sofisticate semplicemente di eludere l'individuazione. Minacce particolarmente elusive, ad esempio zero-day, mirate e minacce permanenti avanzate, possono essere le più pericolose per la propria organizzazione, rendendo il rilevamento più critico.

La funzionalità offrono dati in Sentinel di Azure Elimina la fatica dei carichi di lavoro degli analisti e l'incertezza dal loro lavoro e offre un'intelligenza praticabile e di grande fedeltà, per potersi concentrare sull'indagine e sulla correzione.

Azure Sentinel raccoglie log e avvisi da tutte le relative origini dati connesse, li analizza e compila i profili comportamentali di base delle entità dell'organizzazione (utenti, host, indirizzi IP, applicazioni e così via) tra il tempo e il gruppo peer Horizon. Usando un'ampia gamma di tecniche e funzionalità di Machine Learning, Sentinel può quindi identificare le attività anomale e determinare se un asset è stato compromesso. Non solo, ma può anche determinare la sensibilità relativa di determinate risorse, identificare i gruppi di risorse di pari livello e valutare l'impatto potenziale di qualsiasi asset compromesso specificato (il "raggio di esplosione"). Con queste informazioni, è possibile classificare in modo efficace le priorità dell'indagine e della gestione degli eventi imprevisti. 

### <a name="architecture-overview"></a>Panoramica dell'architettura

:::image type="content" source="media/identify-threats-with-entity-behavior-analytics/entity-behavior-analytics-architecture.png" alt-text="Architettura di analisi del comportamento delle entità":::

### <a name="security-driven-analytics"></a>Analisi basate sulla sicurezza

Ispirato dal paradigma di Gartner per le soluzioni offrono dati, Azure Sentinel offre un approccio "esterno a", basato su tre frame di riferimento:

- **Casi d'uso:** Classificando in ordine di priorità gli scenari e i vettori di attacco pertinenti in base alla ricerca di sicurezza allineata con l'ATT MITRE&il Framework CK di tattiche, tecniche e sottotecniche che inserisce varie entità come vittime, autori o punti pivot nella catena di Kill; Azure Sentinel si concentra in particolare sui log più importanti che ogni origine dati può fornire.

- **Origini dati:** Per prima cosa supportano origini dati di Azure, Azure Sentinel seleziona in modo accurato le origini dati di terze parti per fornire dati corrispondenti agli scenari di minaccia.

- **Analisi:** Usando diversi algoritmi di Machine Learning (ML), Sentinel di Azure identifica le attività anomale e presenta le evidenze in modo chiaro e conciso sotto forma di arricchimenti contestuali, alcuni esempi dei quali compaiono di seguito.

    :::image type="content" source="media/identify-threats-with-entity-behavior-analytics/behavior-analytics-top-down.png" alt-text="Architettura di analisi del comportamento delle entità" indica un'anomalia identificata:
- in posizioni geografiche, dispositivi e ambienti.
- negli orizzonti temporali e di frequenza (rispetto alla cronologia dell'utente).
- rispetto al comportamento dei peer.
- rispetto al comportamento dell'organizzazione.

    :::image type="content" source="media/identify-threats-with-entity-behavior-analytics/context.png" alt-text="Architettura di analisi del comportamento delle entità":::


### <a name="scoring"></a>Assegnazione dei punteggi

Ogni attività viene classificata con "Punteggio di priorità dell'indagine", che determina la probabilità di un utente specifico che esegue un'attività specifica, in base all'apprendimento comportamentale dell'utente e dei relativi peer. Le attività identificate come il più anomalo ricevono i punteggi più alti (su una scala di 0-10).

Per un esempio di come funziona, vedere come viene usata l'analisi del comportamento in [Microsoft cloud app Security](https://techcommunity.microsoft.com/t5/microsoft-security-and/prioritize-user-investigations-in-cloud-app-security/ba-p/700136) .



## <a name="entity-pages"></a>Pagine entità

Quando si verifica un'entità (attualmente limitata a utenti e host) in una ricerca, un avviso o un'indagine, è possibile selezionare l'entità e una **pagina di entità**, un foglio dati completo di informazioni utili su tale entità. I tipi di informazioni che si troveranno in questa pagina includono fact di base sull'entità, una sequenza temporale di eventi rilevanti correlati a questa entità e informazioni dettagliate sul comportamento dell'entità.
 
Le pagine entità sono costituite da tre parti:
- Il pannello a sinistra contiene le informazioni di identificazione dell'entità, raccolte da origini dati come Azure Active Directory, monitoraggio di Azure, Centro sicurezza di Azure e Microsoft Defender.

- Il pannello centrale mostra una sequenza temporale grafica e testuale degli eventi rilevanti correlati all'entità, ad esempio avvisi, segnalibri e attività. Le attività sono aggregazioni di eventi rilevanti da Log Analytics. Le query che rilevano tali attività vengono sviluppate dai team di ricerca della sicurezza Microsoft.

- Il pannello di destra presenta informazioni comportamentali sull'entità. Queste informazioni consentono di identificare rapidamente le anomalie e le minacce per la sicurezza. Le informazioni dettagliate sono sviluppate dai team di ricerca della sicurezza Microsoft e si basano sui modelli di rilevamento delle anomalie.

### <a name="the-timeline"></a>Sequenza temporale

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-timeline.png" alt-text="Architettura di analisi del comportamento delle entità":::

La sequenza temporale è una parte importante del contributo della pagina di entità all'analisi del comportamento in Sentinel di Azure. Viene presentata una storia sugli eventi correlati a entità, che consente di comprendere l'attività dell'entità in un intervallo di tempo specifico.

È possibile scegliere l' **intervallo di tempo** tra le diverse opzioni predefinite (ad esempio, le *ultime 24 ore*) o impostarlo su qualsiasi intervallo di tempo definito personalizzato. Inoltre, è possibile impostare i filtri che limitano le informazioni nella sequenza temporale a tipi specifici di eventi o avvisi.

Nella sequenza temporale sono inclusi i tipi di elementi seguenti:

- Avvisi: tutti gli avvisi in cui l'entità viene definita come **entità mappata**. Si noti che, se l'organizzazione ha creato [avvisi personalizzati con le regole di analisi](./tutorial-detect-threats-custom.md), è necessario assicurarsi che il mapping delle entità delle regole venga eseguito correttamente.

- Segnalibri: tutti i segnalibri che includono l'entità specifica visualizzata nella pagina.

- Attività: aggregazione di eventi rilevanti correlati all'entità. 
 
### <a name="entity-insights"></a>Informazioni dettagliate sulle entità
 
Entity Insights è una query definita dai ricercatori della sicurezza Microsoft per aiutare gli analisti a esaminare in modo più efficiente ed efficace. Le informazioni dettagliate vengono presentate come parte della pagina entità e forniscono informazioni di sicurezza importanti su host e utenti, sotto forma di dati tabulari e grafici. Con le informazioni significa che non devi deviare per Log Analytics. Le informazioni dettagliate includono i dati relativi ad accessi, aggiunte di gruppo, eventi anomali e altro ancora e includono algoritmi di Machine Learning avanzati per rilevare un comportamento anomalo. Le informazioni dettagliate sono basate sui tipi di dati seguenti:
- syslog
- SecurityEvent
- Log di controllo
- Log di accesso
- Attività di Office
- BehaviorAnalytics (offrono dati) 
 
### <a name="how-to-use-entity-pages"></a>Come utilizzare le pagine delle entità

Le pagine di entità sono progettate per essere parte di più scenari di utilizzo ed è possibile accedervi dalla gestione degli eventi imprevisti, dal grafico di analisi, dai segnalibri o direttamente dalla pagina di ricerca di entità in **analisi del comportamento delle entità** nel menu principale di Sentinel di Azure.

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-use-cases.png" alt-text="Architettura di analisi del comportamento delle entità":::


## <a name="data-schema"></a>Schema dei dati

### <a name="behavior-analytics-table"></a>Tabella di analisi del comportamento

| Campo                     | Descrizione                                                         |
|---------------------------|---------------------------------------------------------------------|
| TenantId                  | numero ID univoco del tenant                                      |
| SourceRecordId            | numero ID univoco dell'evento EBA                                   |
| TimeGenerated             | timestamp dell'occorrenza dell'attività                              |
| TimeProcessed             | timestamp dell'elaborazione dell'attività da parte del motore EBA            |
| ActivityType              | categoria di alto livello dell'attività                                 |
| ActionType                | nome normalizzato dell'attività                                     |
| UserName                  | nome utente dell'utente che ha avviato l'attività                    |
| UserPrincipalName         | nome utente completo dell'utente che ha avviato l'attività               |
| EventSource               | origine dati che ha fornito l'evento originale                        |
| SourceIPAddress           | Indirizzo IP da cui è stata avviata l'attività                        |
| SourceIPLocation          | paese da cui è stata avviata l'attività, arricchita dall'indirizzo IP |
| SourceDevice              | nome host del dispositivo che ha avviato l'attività                  |
| DestinationIPAddress      | Indirizzo IP della destinazione dell'attività                            |
| DestinationIPLocation     | paese della destinazione dell'attività, arricchito dall'indirizzo IP     |
| DestinationDevice         | nome del dispositivo di destinazione                                           |
| **UsersInsights**         | arricchimenti contestuali degli utenti in relazione                            |
| **DevicesInsights**       | arricchimenti contestuali di dispositivi                          |
| **ActivityInsights**      | analisi contestuale dell'attività in base alla profilatura              |
| **InvestigationPriority** | Punteggio di anomalia, compreso tra 0-10 (0 = benigno, 10 = molto anomalo)         |
|

### <a name="querying-behavior-analytics-data"></a>Esecuzione di query sui dati di analisi del comportamento

Usando [KQL](https://docs.microsoft.com/azure/data-explorer/kusto/query/), è possibile eseguire una query sulla tabella di analisi comportamentale.

Ad esempio, se si vuole trovare tutti i casi di un utente che non è riuscito ad accedere a una risorsa di Azure, dove è stato il primo tentativo dell'utente di connettersi da un determinato paese e le connessioni da tale paese non sono comuni anche per i peer dell'utente, è possibile usare la query seguente:

```Kusto
BehaviorAnalytics
| where ActivityType == "FailedLogOn"
| where FirstTimeUserConnectedFromCountry == True
| where CountryUncommonlyConnectedFromAmongPeers == True
```

### <a name="user-peers-metadata---table-and-notebook"></a>Metadati dei peer utente-tabella e notebook

I metadati dei peer utente forniscono un contesto importante nei rilevamenti delle minacce, durante l'analisi di un evento imprevisto e nella ricerca di una potenziale minaccia. Gli analisti della sicurezza possono osservare le normali attività dei peer di un utente per determinare se le attività dell'utente sono insolite rispetto a quelle dei suoi peer.

Azure Sentinel calcola e classifica i peer di un utente, in base all'appartenenza al gruppo di sicurezza dell'utente Azure AD, alla lista di distribuzione e alla Cetera e archivia i peer classificati 1-20 nella tabella **UserPeerAnalytics** . Lo screenshot seguente mostra lo schema della tabella UserPeerAnalytics e Visualizza i principali peer di otto classificati dell'utente Kendall Collins. Per normalizzare la ponderazione per il calcolo del rango, Sentinel di Azure usa il termine algoritmo di frequenza dei documenti con frequenza *inversa* (TF-IDF), più piccolo è il gruppo. 

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/user-peers-metadata.png" alt-text="Architettura di analisi del comportamento delle entità":::

È possibile usare il [notebook di Jupyter](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/BehaviorAnalytics/UserSecurityMetadata) disponibile nel repository GitHub di Azure Sentinel per visualizzare i metadati dei peer utente. Per istruzioni dettagliate su come usare il notebook, vedere l' [analisi guidata-notebook dei metadati di sicurezza utente](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/BehaviorAnalytics/UserSecurityMetadata/Guided%20Analysis%20-%20User%20Security%20Metadata.ipynb) .

### <a name="permission-analytics---table-and-notebook"></a>Analisi delle autorizzazioni-tabella e notebook

L'analisi delle autorizzazioni consente di determinare il potenziale impatto della compromissione di un asset aziendale da parte di un utente malintenzionato. Questo effetto è noto anche come "raggio di esplosione" dell'asset. Gli analisti della sicurezza possono usare queste informazioni per definire la priorità delle indagini e della gestione degli eventi imprevisti.

Azure Sentinel determina i diritti di accesso diretti e transitivi conservati da un determinato utente per le risorse di Azure, valutando le sottoscrizioni di Azure a cui l'utente può accedere direttamente o tramite gruppi o entità servizio. Queste informazioni, nonché l'elenco completo delle Azure AD appartenenza al gruppo di sicurezza dell'utente, vengono quindi archiviate nella tabella **UserAccessAnalytics** . La schermata seguente mostra una riga di esempio nella tabella UserAccessAnalytics, per l'utente Alex Johnson. L' **entità di origine** è l'account utente o dell'entità servizio e l'entità di **destinazione** è la risorsa a cui l'entità di origine ha accesso. I valori del **livello di accesso** e del **tipo di accesso** dipendono dal modello di controllo di accesso dell'entità di destinazione. Si noterà che Alex ha l'accesso collaboratore al tenant di *Contoso Hotels*della sottoscrizione di Azure. Il modello di controllo di accesso della sottoscrizione è RBAC.   

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/user-access-analytics.png" alt-text="Architettura di analisi del comportamento delle entità":::

È possibile usare il [notebook di Jupyter](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/BehaviorAnalytics/UserSecurityMetadata) (lo stesso notebook menzionato in precedenza) dal repository GitHub di Azure Sentinel per visualizzare i dati di analisi delle autorizzazioni. Per istruzioni dettagliate su come usare il notebook, vedere l' [analisi guidata-notebook dei metadati di sicurezza utente](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/BehaviorAnalytics/UserSecurityMetadata/Guided%20Analysis%20-%20User%20Security%20Metadata.ipynb) .

### <a name="hunting-queries-and-exploration-queries"></a>Query di ricerca e query di esplorazione

Azure Sentinel fornisce un set di query di ricerca, query di esplorazione e una cartella di lavoro in base alla tabella BehaviorAnalytics. Questi strumenti presentano dati arricchiti, incentrati su casi di utilizzo specifici, che indicano un comportamento anomalo. 

Altre informazioni sulla [ricerca e sul grafico di analisi](./hunting.md) in Sentinel di Azure.

## <a name="next-steps"></a>Passaggi successivi
In questo documento sono state illustrate le funzionalità di analisi del comportamento delle entità di Sentinel di Azure. Per informazioni pratiche sull'implementazione e per usare le informazioni acquisite, vedere gli articoli seguenti:

- [Abilitare l'analisi del comportamento delle entità](./enable-entity-behavior-analytics.md) in Sentinel di Azure.
- Cerca le [minacce per la sicurezza](./hunting.md).