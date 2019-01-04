---
title: Panoramica degli avvisi e del monitoraggio delle notifiche in Azure
description: Panoramica degli avvisi in Azure. Avvisi, avvisi classici, interfaccia degli avvisi.
author: rboucher
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: robb
ms.component: alerts
ms.openlocfilehash: b24b98760e276c207bb360687f32ae902b3306fb
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2019
ms.locfileid: "53976954"
---
# <a name="overview-of-alerts-in-microsoft-azure"></a>Panoramica degli avvisi in Microsoft Azure 

Questo articolo descrive cosa sono gli avvisi, quali vantaggi offrono e come iniziare a farne uso.  


## <a name="what-are-alerts-in-microsoft-azure"></a>Cosa sono gli avvisi in Microsoft Azure?
Gli avvisi notificano in modo proattivo quando vengono riscontrate importanti condizioni nei dati di monitoraggio. Consentono di identificare e risolvere i problemi prima che si palesino agli utenti. 

Questo articolo illustra l'esperienza degli avvisi unificata in Monitoraggio di Azure, che ora include Log Analytics e Application Insights. L'[esperienza degli avvisi precedente](alerts-overview.md) e i tipi di avviso sono definiti **avvisi classici**. È possibile visualizzare questa esperienza precedente e il tipo di avviso precedente facendo clic su **Visualizza avvisi classici** nella parte superiore della pagina degli avvisi. 

## <a name="overview"></a>Panoramica

La figura seguente rappresenta il flusso di avvisi. 

![Flusso di avvisi](media/alerts-overview/Azure-Monitor-Alerts.svg)

Le regole di avviso sono separate dagli avvisi e dalle azioni che vengono intraprese quando viene generato un avviso. 

**Regola di avviso**: la regola di avviso acquisisce la destinazione e i criteri per l'invio dell'avviso. Lo stato della regola di avviso può essere impostato su Abilitato o Disabilitato. Gli avvisi vengono generati solo quando sono abilitati. 

Gli attributi chiave di una regola di avviso sono:

**Risorsa di destinazione**: definisce l'ambito e i segnali disponibili per la generazione di avvisi. La destinazione può essere una qualsiasi risorsa di Azure. Esempi di destinazione: una macchina virtuale, un account di archiviazione, un set di scalabilità di macchine virtuali, un'area di lavoro di Log Analytics o una risorsa di Application Insights. Per alcune risorse, ad esempio Macchine virtuali, è possibile specificare più risorse come destinazione della regola di avviso.

**Segnale**: i segnali vengono emessi dalla risorsa di destinazione e possono essere di vari tipi: Metrica, Log attività, Application Insights e Log.

**Criteri**: una combinazione di segnale e logica applicata a una risorsa di destinazione. Esempi: 
   - Percentuale CPU > 70%
   - Tempo di risposta del server > 4 ms 
   - Numero di risultati di una query di log > 100

**Nome avviso**: nome specifico per la regola di avviso configurato dall'utente

**Descrizione avviso**: descrizione specifica per la regola di avviso configurata dall'utente

**Gravità**: gravità dell'avviso dopo che sono stati soddisfatti i criteri specificati nella regola di avviso. La gravità è compresa tra 0 e 4.

**Azione**: un'azione specifica eseguita quando viene attivato l'avviso. Per altre informazioni, vedere [Gruppi di azioni](../../azure-monitor/platform/action-groups.md).

## <a name="what-you-can-alert-on"></a>Oggetto degli avvisi

È possibile inviare avvisi sulle metriche e i log come descritto nell'articolo relativo al [monitoraggio delle origini dati](../../azure-monitor/platform/data-sources-reference.md). Sono incluse, ad esempio:
- Valori della metrica
- Query di ricerca log
- Eventi del log attività
- Integrità della piattaforma Azure sottostante
- Test per la disponibilità del sito Web

## <a name="manage-alerts"></a>Gestisci avvisi
È possibile impostare lo stato di un avviso per specificare dove i trova nel processo di risoluzione. Quando vengono soddisfatti i criteri specificati nella regola di avviso, viene creato o generato un avviso con lo stato impostato su *Nuovo*. È possibile modificare lo stato dell'avviso dopo averlo confermato e chiuso. Tutte le modifiche apportate allo stato vengono archiviate nella cronologia dell'avviso.

Sono supportati i tipi di avviso seguenti.

| Stato | DESCRIZIONE |
|:---|:---|
| Nuovo | Il problema è appena stato rilevato e non è ancora stato esaminato. |
| Confermato | Un amministratore ha esaminato l'avviso e ha iniziato a lavorare a esso. |
| Chiuso | Il problema è stato risolto. Dopo che un avviso è stato chiuso, è possibile riaprirlo modificandone lo stato. |

Lo **stato dell'avviso** è diverso ed è indipendente dalla **condizione del monitoraggio**. Lo stato dell'avviso viene impostato dall'utente, mentre la condizione del monitoraggio viene impostata dal sistema. Quando si attiva un avviso, la relativa condizione del monitoraggio viene impostata su *Attivato*. Quando la condizione sottostante che ha determinato l'attivazione dell'avviso viene cancellata, la condizione del monitoraggio viene impostata su *Risolto*. Lo stato dell'avviso non viene modificato fino a quando l'utente non esegue l'operazione di modifica. Informazioni su [come modificare lo stato degli avvisi e dei gruppi intelligenti](https://aka.ms/managing-alert-smart-group-states).

## <a name="smart-groups"></a>Gruppi intelligenti 
I gruppi intelligenti sono una funzionalità della versione di anteprima. 

I gruppi intelligenti sono aggregazioni di avvisi basate su algoritmi di Machine Learning che consentono di ridurre la frequenza degli avvisi e facilitano la risoluzione dei problemi. [Sono disponibili altre informazioni sui gruppi intelligenti](https://aka.ms/smart-groups) e su [come gestire i gruppi intelligenti](https://aka.ms/managing-smart-groups).


## <a name="alerts-experience"></a>Esperienza degli avvisi 
La pagina degli avvisi predefinita fornisce un riepilogo degli avvisi che vengono creati in un intervallo di tempo specifico. Mostra gli avvisi totali per ogni livello di gravità con colonne che identificano il numero totale di avvisi in ogni stato per ogni livello di gravità. Selezionare uno dei valori di gravità per aprire la pagina [Tutti gli avvisi](#all-alerts-page) filtrata in base al livello di gravità specificato.

Non vengono visualizzati o monitorati gli [avvisi classici](#classic-alerts) delle versioni precedenti. È possibile modificare le sottoscrizioni o i parametri di filtro per aggiornare la pagina. 

![Pagina degli avvisi](media/alerts-overview/alerts-page.png)

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


## <a name="manage-alert-rules"></a>Gestire le regole di avviso
Fare clic su **Gestisci regole di avviso** per visualizzare la pagina **Regole**. **Regole** è una singola pagina in cui è possibile gestire tutte le regole di avviso nelle sottoscrizioni di Azure. Include l'elenco di tutte le regole di avviso che può essere ordinato in base alle risorse di destinazione, ai gruppi di risorse, al nome della regola o allo stato. Da questa pagina è possibile modificare, abilitare o disabilitare le regole di avviso.  

 ![Regole di avviso](./media/alerts-overview/alerts-preview-rules.png)


## <a name="create-an-alert-rule"></a>Creare una regola di avviso
Gli avvisi possono essere creati in modo coerente, indipendentemente dal servizio di monitoraggio o dal tipo di segnale. Tutti gli avvisi attivati e i relativi dettagli sono disponibili in un'unica pagina.
 
Per creare una nuova regola di avviso è necessario eseguire i tre passaggi seguenti:
1. Selezionare la _destinazione_ per l'avviso.
1. Selezionare il _segnale_ dai segnali disponibili per la destinazione.
1. Specificare la _logica_ da applicare ai dati del segnale.
 
Per eseguire questa procedura semplificata, l'utente non deve più conoscere l'origine di monitoraggio o i segnali supportati prima di selezionare una risorsa di Azure. L'elenco dei segnali disponibili è filtrato automaticamente in base alla risorsa di destinazione selezionata. In base a tale destinazione, viene visualizzata una procedura per definire automaticamente la logica della regola di avviso.  

Altre informazioni su come creare regole di avviso sono disponibili in [Creare, visualizzare e gestire gli avvisi tramite Monitoraggio di Azure](../../azure-monitor/platform/alerts-metric.md).

Gli avvisi sono disponibili in vari servizi di monitoraggio di Azure. Per informazioni su come e quando usare ognuno di questi servizi, vedere [Monitoraggio di applicazioni e risorse di Azure](../../azure-monitor/overview.md). La tabella seguente fornisce un elenco dei tipi di regole di avviso disponibili in Azure. Indica anche quali avvisi sono attualmente supportati e in quale esperienza degli avvisi.

Nelle versioni precedenti dei servizi Monitoraggio di Azure, Application Insights, Log Analytics e Integrità dei servizi le funzionalità di invio degli avvisi sono separate. Con il tempo Azure ha migliorato e combinato sia l'interfaccia utente che i diversi metodi di invio degli avvisi. Il consolidamento è ancora in corso. Alcune funzionalità di invio avvisi di conseguenza non sono ancora integrate nel nuovo sistema di avvisi.  

| **Origine di monitoraggio** | **Tipo di segnale**  | **Descrizione** | 
|-------------|----------------|-------------|
| Integrità del servizio | Log attività  | Non supportati. Vedere [Creare gli avvisi del log attività per le notifiche del servizio](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).  |
| Application Insights | Test di disponibilità Web | Non supportati. Vedere [Avvisi di test Web](../../azure-monitor/app/monitor-web-app-availability.md). Disponibili in qualsiasi sito Web dotato delle funzionalità per l'invio di dati ad Application Insights. Ricevere una notifica quando la disponibilità o la velocità di risposta di un sito Web è inferiore alle aspettative. |


## <a name="all-alerts-page"></a>Pagina Tutti gli avvisi 
Fare clic su Totale avvisi per visualizzare la pagina Tutti gli avvisi. In questa pagina è possibile visualizzare un elenco degli avvisi che sono stati creati nel periodo di tempo selezionato. È possibile visualizzare un elenco dei singoli avvisi o un elenco dei gruppi intelligenti contenente gli avvisi. Selezionare il banner nella parte superiore della pagina per passare da una visualizzazione all'altra.

![Pagina Tutti gli avvisi](media/alerts-overview/all-alerts-page.png)

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

## <a name="alert-detail-page"></a>Pagina Dettagli avviso
La pagina dei dettagli dell'avviso viene visualizzata quando si seleziona un avviso. Fornisce i dettagli dell'avviso e consente di cambiarne lo stato.

![Dettagli dell'avviso](media/alerts-overview/alert-detail2.png)

La pagina dei dettagli dell'avviso include le sezioni seguenti.

| Sezione | DESCRIZIONE |
|:---|:---|
| Informazioni di base | Mostra le proprietà e altre informazioni significative sull'avviso. |
| Cronologia | Elenca tutte le azioni eseguite dall'avviso e tutte le modifiche apportate all'avviso. Questa opzione è attualmente limitata alle modifiche di stato. |
| Gruppo intelligente | Informazioni sul gruppo intelligente in cui l'avviso sarà incluso. *Conteggio avvisi* si riferisce al numero di avvisi inclusi nel gruppo intelligente. Include gli altri avvisi dello stesso gruppo intelligente che sono stati creati negli ultimi 30 giorni indipendentemente dal filtro temporale specificato nella pagina dell'elenco degli avvisi. Selezionare un avviso per visualizzarne i dettagli. |
| Altre informazioni | Mostra altre informazioni contestuali sull'avviso, che sono in genere specifiche del tipo di origine che ha creato l'avviso. |


## <a name="classic-alerts"></a>Avvisi classici 

La funzionalità di invio avvisi del log attività e delle metriche di Monitoraggio di Azure prima del giugno 2018 è denominata "Avvisi (classici)". 

Per altre informazioni, vedere [Avvisi classici](./../../azure-monitor/platform/alerts-classic.overview.md).


## <a name="next-steps"></a>Passaggi successivi

- [Altre informazioni sui gruppi intelligenti](https://aka.ms/smart-groups)
- [Informazioni sui gruppi di azioni](../../azure-monitor/platform/action-groups.md)
- [Managing your alert instances in Azure](https://aka.ms/managing-alert-instances) (Gestione delle istanze di avvio in Azure)
- [Managing Smart Groups](https://aka.ms/managing-smart-groups) (Gestione dei gruppi intelligenti)





