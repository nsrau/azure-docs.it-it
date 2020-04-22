---
title: Panoramica degli avvisi e del monitoraggio delle notifiche in Azure
description: Panoramica degli avvisi in Azure. Avvisi, avvisi classici e l'interfaccia degli avvisi.
ms.subservice: alerts
ms.topic: conceptual
ms.date: 01/28/2018
ms.openlocfilehash: e02c23623062f5cb3e4c597b0bb257b30aa9f44a
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769777"
---
# <a name="overview-of-alerts-in-microsoft-azure"></a>Panoramica degli avvisi in Microsoft Azure 

Questo articolo descrive cosa sono gli avvisi, quali vantaggi offrono e come iniziare a farne uso.  

## <a name="what-are-alerts-in-microsoft-azure"></a>Cosa sono gli avvisi in Microsoft Azure?
Gli avvisi notificano in modo proattivo quando vengono riscontrate importanti condizioni nei dati di monitoraggio. Consentono di identificare e risolvere i problemi prima che si palesino agli utenti. 

Questo articolo illustra l'esperienza di avviso unificato in Monitoraggio di Azure, che include avvisi gestiti in precedenza da Log Analytics e Application Insights.This article discusses the unified alert experience in Azure Monitor, which includes alerts that were previously managed by Log Analytics and Application Insights. L'[esperienza degli avvisi precedente](alerts-classic.overview.md) e i tipi di avviso sono definiti *avvisi classici*. È possibile visualizzare questa esperienza precedente e il tipo di avviso precedente selezionando **Visualizza avvisi classici** nella parte superiore della pagina di avviso. 

## <a name="overview"></a>Panoramica

La figura seguente rappresenta il flusso di avvisi. 

![Diagramma del flusso di avvisi](media/alerts-overview/Azure-Monitor-Alerts.svg)

Le regole di avviso sono separate dagli avvisi e dalle azioni eseguite quando viene generato un avviso. La regola di avviso acquisisce la destinazione e i criteri per gli avvisi. Lo stato della regola di avviso può essere impostato su Abilitato o Disabilitato. Gli avvisi vengono generati solo quando sono abilitati. 

Di seguito sono riportati gli attributi chiave di una regola di avviso:

**Risorsa di destinazione**: Definisce l'ambito e i segnali disponibili per gli avvisi. La destinazione può essere una qualsiasi risorsa di Azure. Esempi di destinazione: una macchina virtuale, un account di archiviazione, un set di scalabilità di macchine virtuali, un'area di lavoro Log Analytics o una risorsa di Application Insights. Per alcune risorse, ad esempio le macchine virtuali, è possibile specificare più risorse come destinazione della regola di avviso.

**Signal**: Emesso dalla risorsa di destinazione. I segnali possono essere dei seguenti tipi: metrica, log attività, Application Insights e log.

**Criteri**: Combinazione di segnale e logica applicata su una risorsa di destinazione. Esempi: 

- Percentuale CPU > 70%
- Tempo di risposta del server > 4 ms 
- Numero di risultati di una query di log > 100

**Nome avviso**: Un nome specifico per la regola di avviso configurata dall'utente.

**Descrizione avviso**: Descrizione della regola di avviso configurata dall'utente.

**Gravità:** la gravità dell'avviso dopo il soddisfacito dei criteri specificati nella regola di avviso. La gravità è compresa tra 0 e 4.

- Sev 0 - Critico
- Sev 1 - Errore
- Sev 2 - Avvertenza
- Sev 3 - Informativo
- Sev 4 - Dettagliato 

**Azione:** azione specifica eseguita quando viene generato l'avviso. Per ulteriori informazioni, vedere [Gruppi di azioni](../../azure-monitor/platform/action-groups.md).

## <a name="what-you-can-alert-on"></a>Oggetto degli avvisi

È possibile avvisare le metriche e i log, come descritto in [Monitoraggio delle origini dati](../../azure-monitor/platform/data-sources.md). Sono incluse, ad esempio:

- Valori della metrica
- Query di ricerca log
- Eventi dei log attività
- Integrità della piattaforma Azure sottostante
- Test per la disponibilità del sito Web

Nelle versioni precedenti, per le metriche di Monitoraggio di Azure, Application Insights, Log Analytics e Integrità dei servizi erano disponibili funzionalità di gestione degli avvisi separate. Nel corso del tempo, Azure ha migliorato e combinato sia l'interfaccia utente che i diversi metodi di gestione degli avvisi. Il consolidamento è ancora in corso. Alcune funzionalità di invio avvisi di conseguenza non sono ancora integrate nel nuovo sistema di avvisi.  

| **Origine di monitoraggio** | **Tipo di segnale**  | **Descrizione** |
|-------------|----------------|-------------|
| Service Health | Log attività  | Non supportato. Vedere [Creare gli avvisi del log attività per le notifiche del servizio](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).  |
| Application Insights | Test di disponibilità Web | Non supportato. Vedere [Avvisi di test Web](../../azure-monitor/app/monitor-web-app-availability.md). Disponibili in qualsiasi sito Web dotato delle funzionalità per l'invio di dati ad Application Insights. Ricevere una notifica quando la disponibilità o la velocità di risposta di un sito Web è inferiore alle aspettative. |

## <a name="manage-alerts"></a>Gestisci avvisi
È possibile impostare lo stato di un avviso per specificare dove i trova nel processo di risoluzione. Quando vengono soddisfatti i criteri specificati nella regola di avviso, viene creato o generato un avviso con stato *Nuovo*. È possibile modificare lo stato dell'avviso dopo averlo confermato e chiuso. Tutte le modifiche apportate allo stato vengono archiviate nella cronologia dell'avviso.

Sono supportati i tipi di avviso seguenti.

| State | Descrizione |
|:---|:---|
| Nuovo | Il problema è stato appena rilevato e non è ancora stato esaminato. |
| Confermato | Un amministratore ha esaminato l'avviso e ha iniziato a lavorare a esso. |
| Chiuso | Il problema è stato risolto. Dopo che un avviso è stato chiuso, è possibile riaprirlo modificandone lo stato. |

Lo *stato dell'avviso* è diverso ed è indipendente dalla *condizione del monitoraggio*. Lo stato dell'avviso viene impostato dall'utente, mentre la condizione del monitoraggio viene impostata dal sistema. Quando si attiva un avviso, la relativa condizione del monitoraggio viene impostata su *Attivato*. Quando la condizione sottostante che ha determinato l'attivazione dell'avviso viene cancellata, la condizione del monitoraggio viene impostata su *Risolto*. Lo stato dell'avviso non viene modificato fino a quando l'utente non esegue l'operazione di modifica. Informazioni su [come modificare lo stato degli avvisi e dei gruppi intelligenti](https://aka.ms/managing-alert-smart-group-states).

## <a name="smart-groups"></a>Gruppi intelligenti 

I gruppi intelligenti sono aggregazioni di avvisi basati su algoritmi di apprendimento automatico, che consentono di ridurre il rumore degli avvisi e facilitare la risoluzione dei problemi. [Sono disponibili altre informazioni sui gruppi intelligenti](https://aka.ms/smart-groups) e su [come gestire i gruppi intelligenti](https://aka.ms/managing-smart-groups).


## <a name="alerts-experience"></a>Esperienza degli avvisi 
La pagina Avvisi predefinita fornisce un riepilogo degli avvisi creati in un determinato intervallo di tempo. Visualizza gli avvisi totali per ogni gravità, con colonne che identificano il numero totale di avvisi in ogni stato per ogni gravità. Selezionare uno dei valori di gravità per aprire la pagina [Tutti gli avvisi](#all-alerts-page) filtrata in base al livello di gravità specificato.

In alternativa, è possibile enumerare a livello di codice le istanze di [avviso generate nelle sottoscrizioni usando le API REST.](#manage-your-alert-instances-programmatically)

> [!NOTE]
   >  È possibile accedere agli avvisi generati solo negli ultimi 30 giorni.

Non mostra o tiene traccia degli avvisi classici. È possibile modificare le sottoscrizioni o i parametri di filtro per aggiornare la pagina. 

![Screenshot della pagina Avvisi](media/alerts-overview/alerts-page.png)

È possibile filtrare questa visualizzazione selezionando i valori nei menu a discesa nella parte superiore della pagina.

| Colonna | Descrizione |
|:---|:---|
| Subscription | Selezionare le sottoscrizioni di Azure per cui si desidera visualizzare gli avvisi. Facoltativamente, è possibile scegliere di selezionare tutti gli abbonamenti. Solo gli avvisi a cui si ha accesso nelle sottoscrizioni selezionate vengono inclusi nella visualizzazione. |
| Resource group | Selezionare un singolo gruppo di risorse. Sono inclusi nella visualizzazione solo gli avvisi con destinazioni nel gruppo di risorse selezionato. |
| Intervallo di ore | Solo gli avvisi generati nell'intervallo di tempo selezionato vengono inclusi nella vista. I valori supportati sono l'ultima ora, le ultime 24 ore, gli ultimi 7 giorni e gli ultimi 30 giorni. |

Selezionare i valori seguenti nella parte superiore della pagina Avvisi per aprire un'altra pagina:

| valore | Descrizione |
|:---|:---|
| Totale avvisi | Numero totale di avvisi che corrispondono ai criteri selezionati. Selezionare questo valore per aprire la visualizzazione Tutti gli avvisi senza filtri. |
| Gruppi intelligenti | Numero totale di gruppi intelligenti creati dagli avvisi che corrispondono ai criteri selezionati. Selezionare questo valore per aprire l'elenco dei gruppi intelligenti nella visualizzazione Tutti gli avvisi.
| Totale regole di avviso | Numero totale di regole di avviso nella sottoscrizione o nel gruppo di risorse selezionato. Selezionare questo valore per aprire la visualizzazione Regole, filtrata in base alla sottoscrizione e al gruppo di risorse selezionati.


## <a name="manage-alert-rules"></a>Gestire le regole di avviso
Per visualizzare la pagina **Regole,** selezionare **Gestisci regole di avviso**. La pagina Regole è un'unica posizione per la gestione di tutte le regole di avviso tra le sottoscrizioni di Azure.The Rules page is a single place for managing all alert rules across your Azure subscriptions. Include l'elenco di tutte le regole di avviso che può essere ordinato in base alle risorse di destinazione, ai gruppi di risorse, al nome della regola o allo stato. È inoltre possibile modificare, abilitare o disabilitare le regole di avviso da questa pagina.  

 ![Screenshot della pagina Regole](./media/alerts-overview/alerts-preview-rules.png)


## <a name="create-an-alert-rule"></a>Creare una regola di avviso
È possibile creare regole di avviso in modo coerente, indipendentemente dal servizio di monitoraggio o dal tipo di segnale.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tflw]

 
Ecco come creare una nuova regola di avviso:Here's how to create a new alert rule:
1. Selezionare la _destinazione_ per l'avviso.
1. Selezionare il _segnale_ dai segnali disponibili per la destinazione.
1. Specificare la _logica_ da applicare ai dati del segnale.
 
Per eseguire questa procedura semplificata, l'utente non deve più conoscere l'origine di monitoraggio o i segnali supportati prima di selezionare una risorsa di Azure. L'elenco dei segnali disponibili è filtrato automaticamente in base alla risorsa di destinazione selezionata. In base a tale destinazione, viene visualizzata una procedura per definire automaticamente la logica della regola di avviso.  

Altre informazioni su come creare regole di avviso sono disponibili in [Creare, visualizzare e gestire gli avvisi tramite Monitoraggio di Azure](../../azure-monitor/platform/alerts-metric.md).

Gli avvisi sono disponibili in vari servizi di monitoraggio di Azure. Per informazioni su come e quando usare ognuno di questi servizi, vedere [Monitoraggio di applicazioni e risorse di Azure](../../azure-monitor/overview.md). 


## <a name="all-alerts-page"></a>Pagina Tutti gli avvisi 
Per visualizzare la pagina **Tutti gli avvisi,** selezionare **Avvisi totali**. Qui è possibile visualizzare un elenco di avvisi creati entro il tempo selezionato. È possibile visualizzare un elenco dei singoli avvisi o un elenco dei gruppi intelligenti contenente gli avvisi. Selezionare il banner nella parte superiore della pagina per passare da una visualizzazione all'altra.

![Screenshot della pagina Tutti gli avvisi](media/alerts-overview/all-alerts-page.png)

È possibile filtrare la visualizzazione selezionando i seguenti valori nei menu a discesa nella parte superiore della pagina:

| Colonna | Descrizione |
|:---|:---|
| Subscription | Selezionare le sottoscrizioni di Azure per cui si desidera visualizzare gli avvisi. Facoltativamente, è possibile scegliere di selezionare tutti gli abbonamenti. Solo gli avvisi a cui si ha accesso nelle sottoscrizioni selezionate vengono inclusi nella visualizzazione. |
| Resource group | Selezionare un singolo gruppo di risorse. Sono inclusi nella visualizzazione solo gli avvisi con destinazioni nel gruppo di risorse selezionato. |
| Tipo di risorsa | Selezionare uno o più tipi di risorsa. Sono inclusi nella visualizzazione solo gli avvisi con destinazioni del tipo selezionato. Questa colonna risulta disponibile solo dopo che è stato specificato un gruppo di risorse. |
| Risorsa | Selezionare una risorsa. Nella visualizzazione vengono inclusi solo gli avvisi con tale risorsa definita come destinazione. Questa colonna risulta disponibile solo dopo che è stato specificato un tipo di risorsa. |
| Gravità | Selezionare un livello di gravità dell'avviso o **Tutti** per includere avvisi di tutti i livelli di gravità. |
| Condizione di monitoraggio | Selezionare una condizione di monitoraggio o **Tutti** per includere avvisi di tutte le condizioni. |
| Stato dell'avviso | Selezionare uno stato di avviso o **Tutti** per includere avvisi di tutti gli stati. |
| Servizio di monitoraggio | Selezionare un servizio o **Tutti** per includere tutti i servizi. Sono inclusi solo gli avvisi creati da regole che usano tale servizio come destinazione. |
| Intervallo di ore | Solo gli avvisi generati nell'intervallo di tempo selezionato vengono inclusi nella vista. I valori supportati sono l'ultima ora, le ultime 24 ore, gli ultimi 7 giorni e gli ultimi 30 giorni. |

Selezionare **Colonne** nella parte superiore della pagina per selezionare le colonne da visualizzare. 

## <a name="alert-details-page"></a>Pagina dei dettagli dell'avviso
Quando si seleziona un avviso, questa pagina fornisce i dettagli dell'avviso e consente di modificarne lo stato.

![Screenshot della pagina Dettagli avviso](media/alerts-overview/alert-detail2.png)

La pagina Dettagli avviso include le sezioni seguenti:The Alert details page includes the following sections:

| Sezione | Descrizione |
|:---|:---|
| Summary | Mostra le proprietà e altre informazioni significative sull'avviso. |
| Cronologia | Elenca tutte le azioni eseguite dall'avviso e tutte le modifiche apportate all'avviso. Questa opzione è attualmente limitata alle modifiche di stato. |
| Diagnostica | Informazioni sul gruppo intelligente in cui è incluso l'avviso. *Conteggio avvisi* si riferisce al numero di avvisi inclusi nel gruppo intelligente. Include altri avvisi nello stesso gruppo intelligente creati negli ultimi 30 giorni, indipendentemente dal filtro temporale nella pagina di elenco degli avvisi. Selezionare un avviso per visualizzarne i dettagli. |

## <a name="role-based-access-control-rbac-for-your-alert-instances"></a>Controllo degli accessi in base al ruolo per le istanze di avvisoRole-based access control (RBAC) for your alert instances

L'utilizzo e la gestione delle istanze di avviso richiedono all'utente di disporre dei ruoli RBAC predefiniti del [collaboratore](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) di monitoraggio o del lettore di [monitoraggio.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) Questi ruoli sono supportati in qualsiasi ambito di Azure Resource Manager, dal livello di sottoscrizione alle assegnazioni granulari a livello di risorsa. Ad esempio, se un utente dispone solo `ContosoVM1`dell'accesso dei collaboratori di `ContosoVM1`monitoraggio per la macchina virtuale, tale utente può utilizzare e gestire solo gli avvisi generati in .

## <a name="manage-your-alert-instances-programmatically"></a>Gestire le istanze di avviso a livello di codiceManage your alert instances programmatically

È possibile eseguire una query a livello di codice per gli avvisi generati sulla sottoscrizione. Potrebbe trattarsi di creare visualizzazioni personalizzate all'esterno del portale di Azure o di analizzare gli avvisi per identificare modelli e tendenze.

È possibile eseguire una query per gli avvisi generati sulle sottoscrizioni usando [l'API REST](https://aka.ms/alert-management-api) di gestione degli avvisi o usando Azure Resource [Graph](../../governance/resource-graph/overview.md) e l'API REST [per resources.](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources)

L'API REST per le risorse di Resource Graph consente di eseguire query per le istanze di avviso su larga scala. Questa operazione è consigliata quando è necessario gestire gli avvisi generati in molte sottoscrizioni. 

La richiesta REST di esempio seguente all'API REST di Resource Graph restituisce il numero di avvisi all'interno di una sottoscrizione:The following sample request to the Resource Graph REST API returns the count of alerts within one subscription:

```json
{
  "subscriptions": [
    <subscriptionId>
  ],
  "query": "AlertsManagementResources | where type =~ 'Microsoft.AlertsManagement/alerts' | summarize count()"
}
```

È anche possibile visualizzare il risultato di questa query Di questo grafico risorse nel portale con Azure Resource Graph [Explorer:You](https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/AlertsManagementResources%20%7C%20where%20type%20%3D~%20%27Microsoft.AlertsManagement%2Falerts%27%20%7C%20summarize%20count()) can also see the result of this Resource Graph query in the portal with Azure Resource Graph Explorer: portal.azure.com

È possibile interrogare gli avvisi per i campi [essenziali.](alerts-common-schema-definitions.md#essentials)

Usare [l'API REST di gestione degli avvisi](https://aka.ms/alert-management-api) per ottenere ulteriori informazioni su avvisi specifici, inclusi i relativi campi del contesto degli [avvisi.](alerts-common-schema-definitions.md#alert-context)

## <a name="next-steps"></a>Passaggi successivi

- [Altre informazioni sui gruppi intelligenti](https://aka.ms/smart-groups)
- [Informazioni sui gruppi di azioni](../../azure-monitor/platform/action-groups.md)
- [Managing your alert instances in Azure](https://aka.ms/managing-alert-instances) (Gestione delle istanze di avvio in Azure)
- [Managing Smart Groups](https://aka.ms/managing-smart-groups) (Gestione dei gruppi intelligenti)
- [Altre informazioni sui prezzi degli avvisi di AzureLearn more about Azure alerts pricing](https://azure.microsoft.com/pricing/details/monitor/)






