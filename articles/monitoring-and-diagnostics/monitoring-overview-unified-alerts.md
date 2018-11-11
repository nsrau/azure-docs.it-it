---
title: Avvisi unificati in Monitoraggio di Azure
description: Descrizione degli avvisi unificati in Azure che consentono di gestire avvisi e regole di avviso fra i servizi di Azure.
author: manishsm-msft
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: mamit
ms.component: alerts
ms.openlocfilehash: 30b2d60868702c6113612668b8e4cf9975aa2c40
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/02/2018
ms.locfileid: "50962198"
---
# <a name="unified-alerts-in-azure-monitor"></a>Avvisi unificati in Monitoraggio di Azure

## <a name="overview"></a>Panoramica

> [!NOTE]
>  Una nuova esperienza di avvisi unificati che consente di gestire gli avvisi da più sottoscrizioni e introduce i concetti di stato dell'avviso e gruppo intelligente è attualmente disponibile in anteprima pubblica. Per una descrizione di questa esperienza avanzata e del processo per abilitarla, vedere l'ultima sezione di questo articolo.


Questo articolo descrive l'esperienza degli avvisi unificati in Monitoraggio di Azure. L'[esperienza di avviso precedente](monitoring-overview-alerts.md) è disponibile dall'opzione **Avvisi (versione classica)** nel menu Monitoraggio di Azure. 

## <a name="features-of-the-unified-alert-experience"></a>Funzionalità dell'esperienza degli avvisi unificati

L'esperienza unificata offre i vantaggi seguenti rispetto all'esperienza della versione classica:

-   **Sistema di notifica migliorato**: i [gruppi di azioni](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) sono gruppi di notifiche e azioni con nome che possono essere riutilizzati in più avvisi. 
- **Esperienza di creazione unificata**: è possibile gestire gli avvisi e le regole di avviso per metriche, log e log attività in Monitoraggio di Azure, Log Analytics e Application Insights da un'unica posizione. 
- **Visualizzazione degli avvisi di Log Analytics attivati nel portale di Azure**: è ora possibile visualizzare gli avvisi di Log Analytics con avvisi provenienti da altre origini nel portale di Azure. In precedenza, gli avvisi provenienti da altre origini erano visualizzabili in un portale separato.
- **Separazione degli avvisi attivati e delle regole di avviso**: le regole di avviso ora sono distinte dagli avvisi. Una regola di avviso è la definizione di una condizione che attiva un avviso. Un avviso è un'istanza dell'attivazione di una regola di avviso.
- **Flusso di lavoro migliorato**: l'esperienza di creazione di avvisi unificati guida l'utente attraverso il processo di configurazione di una regola di avviso.
 
Gli avvisi di metrica hanno i vantaggi seguenti rispetto agli avvisi di metrica classici:

-   **Latenza migliorata**: gli avvisi di metrica possono essere eseguiti con una frequenza di un minuto. Gli avvisi di metrica classici si eseguono sempre con una frequenza di 5 minuti. Gli avvisi di log hanno ancora un ritardo superiore a un minuto a causa del tempo necessario per inserire i log. 
-   **Supporto delle metriche multidimensionali**: è possibile inviare un avviso per le metriche multidimensionali, in modo da monitorare un'istanza specifica della metrica.
-   **Maggiore controllo sulle condizioni della metrica**: è possibile definire regole di avviso più dettagliate che supportano i valori massimi, minimi, medi e totali delle metriche di monitoraggio.
-   **Monitoraggio combinato di più metriche**: è possibile monitorare un massimo di due metriche con una singola regola. Viene attivato un avviso se entrambe le metriche violano le rispettive soglie per il periodo di tempo specificato.
-   **Metriche dai log** (anteprima pubblica limitata): alcuni dati di log diretti a Log Analytics possono essere ora estratti e convertiti in metriche di Monitoraggio di Azure e quindi essere oggetto di avvisi come le altre metriche. 


## <a name="alert-rules"></a>Regole di avviso
L'esperienza degli avvisi unificati si basa sui concetti seguenti per separare le regola di avviso dagli avvisi, offrendo al tempo stesso un'esperienza di creazione unificata per tipi di avvisi diversi.

| Elemento | Definizione |
|:---|:---|
| Regola di avviso | Definizione della condizione per creare un avviso. Una regola di avviso è composta da _risorsa di destinazione_, _segnale_, _criteri_ e _logica_. Una regola di avviso è attiva solo se si trova nello stato _abilitato_.
| Risorsa di destinazione | Definisce le risorse specifiche e i segnali disponibili per la generazione di avvisi. La destinazione può essere una qualsiasi risorsa di Azure.<br><br>Esempi: una macchina virtuale, un account di archiviazione, un set di scalabilità di macchine virtuali, un'area di lavoro di Log Analytics, una risorsa di Application Insights |
| Segnale | Origine dei dati generati dalla risorsa di destinazione. I tipi di segnale supportati sono *Metrica*, *Log attività*, *Application Insights* e *Log*. |
| Criteri | Combinazione di _segnale_ e _logica_ applicata a una risorsa di destinazione.<br><br>Esempi: Percentuale d'uso della CPU > 70%, Tempo di risposta del server > 4 ms, Numero di risultati di una query su log > 100 e così via |
| Logica | Logica definita dall'utente per verificare se il segnale è compreso nell'intervallo o nei valori previsti. |
| Azione | Azione da eseguire quando viene attivato l'avviso. Quando viene attivato un avviso, possono essere eseguite più azioni. Gli avvisi supportano i gruppi di azioni.<br><br>Esempi: invio di un messaggio a un indirizzo di posta elettronica o chiamata di un URL webhook |
| Condizione del monitoraggio | Indica se la condizione che ha creato un avviso di metrica è stata risolta. Le regole di avviso metrica campionano una metrica specifica a intervalli regolari. Se i criteri della regola di avviso sono soddisfatti, viene creato un nuovo avviso che ha come condizione "attivato".  Quando la metrica viene campionata nuovamente, se i criteri sono ancora soddisfatti, non accade nulla.  Se invece i criteri non sono soddisfatti, la condizione dell'avviso viene modificata in "risolto". La volta successiva in cui i criteri sono soddisfatti, viene creato un altro avviso che ha come condizione "attivato". |


## <a name="alert-pages"></a>Pagine di avviso
Gli avvisi unificati offrono un'unica posizione in cui visualizzare e gestire tutti gli avvisi di Azure. Le sezioni seguenti descrivono le funzioni di ogni singola pagina dell'esperienza unificata.

### <a name="alerts-overview-page"></a>Pagina Panoramica avvisi
La pagina di panoramica **Avvisi** mostra un riepilogo aggregato di tutti gli avvisi attivati e del numero totale delle regole di avviso abilitate. Se si cambiano le sottoscrizioni o i parametri di filtro, i dati aggregati e l'elenco degli avvisi attivati vengono aggiornati di conseguenza.

 ![Panoramica avvisi](./media/monitoring-overview-unified-alerts/alerts-preview-overview2.png) 

### <a name="alert-rules-management"></a>Gestione delle regole di avviso
**Regole** è una singola pagina per la gestione di tutte le regole di avviso nelle sottoscrizioni di Azure. Include l'elenco di tutte le regole di avviso che può essere ordinato in base alle risorse di destinazione, ai gruppi di risorse, al nome della regola o allo stato. Da questa pagina è possibile modificare, abilitare o disabilitare le regole di avviso.

 ![Regole di avviso](./media/monitoring-overview-unified-alerts/alerts-preview-rules.png)


## <a name="create-an-alert-rule"></a>Creare una regola di avviso
Gli avvisi possono essere creati in modo coerente, indipendentemente dal servizio di monitoraggio o dal tipo di segnale. Tutti gli avvisi attivati e i relativi dettagli sono disponibili in un'unica pagina.
 
Per creare una nuova regola di avviso è necessario eseguire i tre passaggi seguenti:
1. Selezionare la _destinazione_ per l'avviso.
1. Selezionare il _segnale_ dai segnali disponibili per la destinazione.
1. Specificare la _logica_ da applicare ai dati del segnale.
 
Per eseguire questa procedura semplificata, l'utente non deve più conoscere l'origine di monitoraggio o i segnali supportati prima di selezionare una risorsa di Azure. L'elenco dei segnali disponibili è filtrato automaticamente in base alla risorsa di destinazione selezionata e serve da guida nella definizione della logica della regola di avviso.

Altre informazioni su come creare regole di avviso sono disponibili in [Creare, visualizzare e gestire gli avvisi tramite Monitoraggio di Azure](alert-metric.md).

Gli avvisi sono disponibili in vari servizi di monitoraggio di Azure. Per informazioni su come e quando usare ognuno di questi servizi, vedere [Monitoraggio di applicazioni e risorse di Azure](../azure-monitor/overview.md). La tabella seguente fornisce un elenco dei tipi di regole di avviso disponibili in Azure. La tabella elenca anche gli avvisi attualmente supportati dall'esperienza degli avvisi unificati.

| **Origine di monitoraggio** | **Tipo di segnale**  | **Descrizione** | 
|-------------|----------------|-------------|
| Monitoraggio di Azure | Metrica  | Denominati anche [avvisi di metrica near real time](monitoring-near-real-time-metric-alerts.md), supportano la valutazione delle condizioni delle metriche alla frequenza di un minuto e consentono l'uso di regole relative a più metriche e di regole relative a metriche multidimensionali. Un elenco dei tipi di risorse supportati è disponibile in [Avvisi delle metriche più recenti per i servizi di Azure nel portale di Azure](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported).<br>Nella nuova esperienza Avvisi non sono supportati gli [avvisi di metrica classici](monitoring-overview-alerts.md). Questi avvisi sono disponibili nella sezione Avvisi (versione classica) del portale di Azure. Gli avvisi classici supportano alcuni tipi di metriche che non sono ancora stati trasferiti nei nuovi avvisi. Per un elenco completo, vedere [Metriche supportate](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-supported-metrics). |
| Log Analytics | Log  | Questa categoria di avvisi consente di ricevere notifiche o eseguire azioni automatiche quando una query di ricerca di log soddisfa determinati criteri. Gli avvisi in Log Analytics [vengono copiati nella nuova esperienza](monitoring-alerts-extend.md). È disponibile un'[anteprima dei *log di Log Analytics come metriche*](monitoring-alerts-extend-tool.md). Questa anteprima consente di convertire alcuni tipi di log in metriche, che possono essere oggetto di avvisi usando la nuova esperienza di avviso. L'anteprima è utile se si hanno log non Azure che si intende gestire insieme alle metriche di Monitoraggio di Azure native. |
| Log attività | Log attività | Contiene i record di tutte le azioni Create, Update e Delete create dalla destinazione selezionata. |
| Integrità del servizio | Log attività  | Non supportato negli avvisi unificati. Vedere [Creare gli avvisi del log attività per le notifiche del servizio](monitoring-activity-log-alerts-on-service-notifications.md).  |
| Application Insights | Log  | Include i log con i dettagli sulle prestazioni dell'applicazione. Usando una query di analisi, è possibile definire le condizioni per le azioni da eseguire in base ai dati dell'applicazione. |
| Application Insights | Metrica | Non supportato negli avvisi unificati. Vedere [Avvisi di metrica](../application-insights/app-insights-alerts.md). |
| Application Insights | Test di disponibilità Web | Non supportato negli avvisi unificati.  Vedere [Avvisi di test Web](../application-insights/app-insights-monitor-web-app-availability.md). Disponibili in qualsiasi sito Web dotato delle funzionalità per l'invio di dati ad Application Insights. Ricevere una notifica quando la disponibilità o la velocità di risposta di un sito Web è inferiore alle aspettative. |

## <a name="enhanced-unified-alerts-public-preview"></a>Avvisi unificati avanzati (anteprima pubblica)

Un'esperienza di avvisi unificata avanzata è stata rilasciata come anteprima pubblica per Monitoraggio di Azure il 1° giugno 2018. Questa esperienza sfrutta i vantaggi degli [avvisi unificati](#overview), rilasciati nel marzo 2018, e offre la possibilità di gestire e aggregare i singoli avvisi e di modificare lo stato dell'avviso. Questa sezione descrive le nuove funzioni e come spostarsi nelle nuove pagine degli avvisi nel portale di Azure.

### <a name="enhanced-unified-alerts"></a>Avvisi unificati avanzati

La nuova esperienza offre le funzioni seguenti che non sono disponibili nell'esperienza unificata classica:

- **Visualizzare gli avvisi fra le sottoscrizioni**: è ora possibile visualizzare e gestire le singole istanze degli avvisi fra più sottoscrizioni in un'unica visualizzazione.
- **Gestire lo stato degli avvisi**: gli avvisi hanno ora uno stato che indica se sono stati confermati come chiusi.
- **Organizzare gli avvisi con i gruppi intelligenti**: i gruppi intelligenti raggruppano automaticamente gli avvisi correlati, in modo che siano gestibili come set anziché singolarmente.

### <a name="enable-enhanced-unified-alerts"></a>Abilitare gli avvisi unificati avanzati
Per abilitare la nuova esperienza degli avvisi unificati, selezionare il banner nella parte superiore della pagina Avvisi. Il processo crea un archivio degli avvisi che include gli avvisi attivati negli ultimi 30 giorni da tutti i servizi supportati. Dopo aver abilitato la nuova esperienza, è possibile alternare l'esperienza nuova e quella precedente selezionando il banner.

> [!NOTE]
>  L'abilitazione iniziale della nuova esperienza può richiedere alcuni minuti.

![Banner](media/monitoring-overview-unified-alerts/opt-in-banner.png)

Tutte le sottoscrizioni a cui si ha accesso vengono registrate quando si abilita la nuova esperienza. Anche se l'intera sottoscrizione è abilitata, solo gli utenti che hanno selezionato la nuova esperienza possono visualizzarla. Gli altri utenti con accesso alla sottoscrizione devono abilitare l'esperienza separatamente.

L'abilitazione della nuova esperienza di avviso non influisce sulla configurazione dei gruppi di azioni o le notifiche nelle regole di avviso. Cambia solo il modo di visualizzare e gestire le istanze attivate degli avvisi nel portale di Azure.

### <a name="smart-groups"></a>Gruppi intelligenti
I gruppi intelligenti riducono il rumore, consentendo di gestire gli avvisi correlati come singola unità, anziché come singoli avvisi. È possibile visualizzare i dettagli dei gruppi intelligenti e impostarne lo stato in modo simile agli avvisi. Ogni avviso appartiene a un unico gruppo intelligente.

I gruppi intelligenti vengono creati automaticamente tramite l'apprendimento automatico per combinare gli avvisi associati che rappresentano un singolo problema. Quando viene creato un avviso, l'algoritmo lo aggiunge a un gruppo intelligente nuovo o esistente in base a informazioni come i modelli cronologici e la somiglianza delle proprietà e della struttura. 

Attualmente, l'algoritmo considera solo gli avvisi dello stesso servizio di monitoraggio all'interno di una sottoscrizione. I gruppi intelligenti possono ridurre fino al 99% il rumore degli avvisi mediante questo consolidamento. È possibile visualizzare il motivo per cui gli avvisi sono stati inclusi in un gruppo nella pagina dei dettagli del gruppo intelligente.

Il nome di un gruppo intelligenti è il nome del suo primo avviso. Non è possibile creare o rinominare un gruppo intelligente.


### <a name="alert-states"></a>Stati dell'avviso
Gli avvisi unificati avanzati introducono il concetto di stato dell'avviso. È possibile impostare lo stato di un avviso per specificare dove i trova nel processo di risoluzione. Quando viene creato un avviso, il suo stato è *Nuovo*. È possibile modificare lo stato dell'avviso dopo averlo confermato e chiuso. Tutte le modifiche apportate allo stato vengono archiviate nella cronologia dell'avviso.

Sono supportati i tipi di avviso seguenti.

| Stato | DESCRIZIONE |
|:---|:---|
| Nuovo | Il problema è appena stato rilevato e non è ancora stato esaminato. |
| Confermato | Un amministratore ha esaminato l'avviso e ha iniziato a lavorare a esso. |
| Chiuso | Il problema è stato risolto. Dopo che un avviso è stato chiuso, è possibile riaprirlo modificandone lo stato. |

Lo stato di un avviso è diverso dalla condizione di monitoraggio. Le regole degli avvisi di metrica possono impostare un avviso sulla condizione _risolto_ quando la condizione di errore non è più soddisfatta. Lo stato dell'avviso è impostato dall'utente ed è indipendente dalla condizione di monitoraggio. Anche se il sistema può impostare la condizione di monitoraggio su "risolto", lo stato dell'avviso non può essere modificato senza l'intervento dell'utente.

#### <a name="change-the-state-of-an-alert-or-smart-group"></a>Modificare lo stato di un avviso o di un gruppo intelligente
È possibile modificare lo stato di un singolo avviso o gestire più avvisi contemporaneamente impostando lo stato di un gruppo intelligente.

Modificare lo stato di un avviso selezionando **Modifica stato avviso** nella relativa visualizzazione dei dettagli. In alternativa, modificare lo stato di un gruppo intelligente selezionando **Modifica stato del gruppo intelligente** nella relativa visualizzazione dei dettagli. È possibile modificare lo stato di più elementi contemporaneamente selezionandoli prima in una visualizzazione elenco e quindi facendo clic su **Cambia stato** nella parte superiore della pagina. 

In entrambi i casi, selezionare un nuovo stato dall'elenco a discesa. È possibile specificare anche un commento. Se si modifica un singolo elemento, è disponibile anche un'opzione per applicare le stesse modifiche a tutti gli avvisi del gruppo intelligente.

![Cambia stato](media/monitoring-overview-unified-alerts/change-tate.png)

### <a name="alerts-page"></a>Pagina degli avvisi
La pagina degli avvisi predefinita fornisce un riepilogo degli avvisi che vengono creati in un intervallo di tempo specifico. Mostra gli avvisi totali per ogni livello di gravità con colonne che identificano il numero totale di avvisi in ogni stato per ogni livello di gravità. Selezionare uno dei valori di gravità per aprire la pagina [Tutti gli avvisi](#all-alerts-page) filtrata in base al livello di gravità specificato.

![Pagina degli avvisi](media/monitoring-overview-unified-alerts/alerts-page.png)

È possibile filtrare questa visualizzazione selezionando i valori nei menu a discesa nella parte superiore della pagina.

| Colonna | DESCRIZIONE |
|:---|:---|
| Sottoscrizione | Selezionare fino a cinque sottoscrizioni di Azure. Sono inclusi nella visualizzazione solo gli avvisi delle sottoscrizioni selezionate. |
| Gruppo di risorse | Selezionare un singolo gruppo di risorse. Sono inclusi nella visualizzazione solo gli avvisi con destinazioni nel gruppo di risorse selezionato. |
| Intervallo di tempo | Nella visualizzazione vengono inclusi solo gli avvisi attivati nell'intervallo di tempo selezionato. I valori supportati sono l'ultima ora, le ultime 24 ore, gli ultimi 7 giorni e gli ultimi 30 giorni. |

Selezionare i valori seguenti nella parte superiore della pagina Avvisi per aprire un'altra pagina.

| Valore | DESCRIZIONE |
|:---|:---|
| Totale avvisi | Numero totale di avvisi che corrispondono ai criteri selezionati. Selezionare questo valore per aprire la visualizzazione Tutti gli avvisi senza filtri. |
| Gruppi intelligenti | Numero totale di gruppi intelligenti creati dagli avvisi che corrispondono ai criteri selezionati. Selezionare questo valore per aprire l'elenco dei gruppi intelligenti nella visualizzazione Tutti gli avvisi.
| Totale regole di avviso | Numero totale di regole di avviso nella sottoscrizione o nel gruppo di risorse selezionato. Selezionare questo valore per aprire la visualizzazione Regole, filtrata in base alla sottoscrizione e al gruppo di risorse selezionati.


### <a name="all-alerts-page"></a>Pagina Tutti gli avvisi 
La pagina Tutti gli avvisi consente di visualizzare un elenco degli avvisi creati nel periodo di tempo selezionato. È possibile visualizzare un elenco dei singoli avvisi o un elenco dei gruppi intelligenti contenente gli avvisi. Selezionare il banner nella parte superiore della pagina per passare da una visualizzazione all'altra.

![Pagina Tutti gli avvisi](media/monitoring-overview-unified-alerts/all-alerts-page.png)

È possibile filtrare la visualizzazione selezionando i valori seguenti nei menu a discesa nella parte superiore della pagina.

| Colonna | DESCRIZIONE |
|:---|:---|
| Sottoscrizione | Selezionare fino a cinque sottoscrizioni di Azure. Sono inclusi nella visualizzazione solo gli avvisi delle sottoscrizioni selezionate. |
| Gruppo di risorse | Selezionare un singolo gruppo di risorse. Sono inclusi nella visualizzazione solo gli avvisi con destinazioni nel gruppo di risorse selezionato. |
| Tipo di risorsa | Selezionare uno o più tipi di risorsa. Sono inclusi nella visualizzazione solo gli avvisi con destinazioni del tipo selezionato. Questa colonna risulta disponibile solo dopo che è stato specificato un gruppo di risorse. |
| Risorsa | Selezionare una risorsa. Nella visualizzazione vengono inclusi solo gli avvisi con tale risorsa definita come destinazione. Questa colonna risulta disponibile solo dopo che è stato specificato un tipo di risorsa. |
| Gravità | Selezionare un livello di gravità degli avvisi oppure *Tutti* per includere gli avvisi di tutti i livelli di gravità. |
| Condizione di monitoraggio | Selezionare una condizione di monitoraggio oppure *Tutti* per includere gli avvisi di tutte le condizioni. |
| Stato dell'avviso | Selezionare uno stato dell'avviso oppure *Tutti* per includere gli avvisi di tutti gli stati. |
| Servizio di monitoraggio | Selezionare un servizio oppure *Tutti* per includere tutti i servizi. Sono inclusi solo gli avvisi creati da regole che usano tale servizio come destinazione. |
| Intervallo di tempo | Nella visualizzazione vengono inclusi solo gli avvisi attivati nell'intervallo di tempo selezionato. I valori supportati sono l'ultima ora, le ultime 24 ore, gli ultimi 7 giorni e gli ultimi 30 giorni. |

Fare clic su **Colonne** nella parte superiore della pagina per selezionare le colonne da visualizzare. 

### <a name="alert-detail-page"></a>Pagina Dettagli avviso
La pagina dei dettagli dell'avviso viene visualizzata quando si seleziona un avviso. Fornisce i dettagli dell'avviso e consente di cambiarne lo stato.

![Dettagli dell'avviso](media/monitoring-overview-unified-alerts/alert-detail.png)

La pagina dei dettagli dell'avviso include le sezioni seguenti.

| Sezione | DESCRIZIONE |
|:---|:---|
| Informazioni di base | Mostra le proprietà e altre informazioni significative sull'avviso. |
| Cronologia | Elenca tutte le azioni eseguite dall'avviso e tutte le modifiche apportate all'avviso. Questa opzione è attualmente limitata alle modifiche di stato. |
| Gruppo intelligente | Informazioni sul gruppo intelligente in cui l'avviso sarà incluso. *Conteggio avvisi* si riferisce al numero di avvisi inclusi nel gruppo intelligente. Comprende gli altri avvisi inclusi nello stesso gruppo intelligente creato negli ultimi 30 giorni.  Questo è indipendente dal filtro orario nella pagina dell'elenco degli avvisi. Selezionare un avviso per visualizzarne i dettagli. |
| Altre informazioni | Mostra altre informazioni contestuali sull'avviso, che sono in genere specifiche del tipo di origine che ha creato l'avviso. |


### <a name="smart-group-detail-page"></a>Pagina dei dettagli del gruppo intelligente
La pagina dei dettagli del gruppo intelligente viene visualizzata quando si seleziona un gruppo intelligente. Fornisce dettagli relativi al gruppo intelligente, inclusi i motivi per cui il gruppo è stato creato, e consente di cambiarne lo stato.
 
![Dettagli del gruppo intelligente](media/monitoring-overview-unified-alerts/smart-group-detail.png)


La pagina dei dettagli del gruppo intelligente include le sezioni seguenti.

| Sezione | DESCRIZIONE |
|:---|:---|
| Avvisi | Elenca i singoli avvisi inclusi nel gruppo intelligente. Selezionare un avviso per aprire la pagina dei dettagli corrispondente. |
| Cronologia | Elenca tutte le azioni eseguite dal gruppo intelligente e le modifiche apportate a quest'ultimo. Questa opzione è attualmente limitata alle modifiche di stato e a quelle di appartenenza degli avvisi. |

## <a name="next-steps"></a>Passaggi successivi
- [Informazioni su come usare la nuova esperienza Avvisi per creare, visualizzare e gestire gli avvisi](alert-metric.md)
- [Informazioni sugli avvisi di log nell'esperienza Avvisi](monitor-alerts-unified-log.md)
- [Informazioni sugli avvisi di metrica nell'esperienza Avvisi](monitoring-near-real-time-metric-alerts.md)
- [Informazioni sugli avvisi del log attività nell'esperienza Avvisi](alert-activity-log.md)
