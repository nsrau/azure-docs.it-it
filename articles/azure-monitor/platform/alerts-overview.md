---
title: Panoramica degli avvisi e del monitoraggio delle notifiche in Azure
description: Panoramica degli avvisi in Azure. Avvisi, avvisi classici e l'interfaccia degli avvisi.
ms.subservice: alerts
ms.topic: conceptual
ms.date: 01/28/2018
ms.openlocfilehash: f58175d105e1dd36d58fbe4d8b68109810797b2a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91317141"
---
# <a name="overview-of-alerts-in-microsoft-azure"></a>Panoramica degli avvisi in Microsoft Azure 

Questo articolo descrive cosa sono gli avvisi, quali vantaggi offrono e come iniziare a farne uso.  

## <a name="what-are-alerts-in-microsoft-azure"></a>Cosa sono gli avvisi in Microsoft Azure?

Gli avvisi inviano notifiche in modo proattivo quando si riscontrano problemi con l'infrastruttura o l'applicazione usando i dati di monitoraggio in monitoraggio di Azure. Consentono di identificare e risolvere i problemi prima che si palesino agli utenti. 

## <a name="overview"></a>Panoramica

La figura seguente rappresenta il flusso di avvisi. 

![Diagramma del flusso di avvisi](media/alerts-overview/Azure-Monitor-Alerts.svg)

Le regole di avviso sono separate dagli avvisi e dalle azioni eseguite quando viene attivato un avviso. La regola di avviso acquisisce la destinazione e i criteri per l'invio di avvisi. Lo stato della regola di avviso può essere impostato su Abilitato o Disabilitato. Gli avvisi vengono generati solo quando sono abilitati. 

Di seguito sono riportati gli attributi chiave di una regola di avviso:

**Risorsa di destinazione**: definisce l'ambito e i segnali disponibili per la generazione di avvisi. La destinazione può essere una qualsiasi risorsa di Azure. Destinazioni di esempio:

- macchine virtuali.
- Account di archiviazione.
- Area d lavoro Log Analytics.
- Application Insights. 

Per alcune risorse, ad esempio le macchine virtuali, è possibile specificare più risorse come destinazione della regola di avviso.

**Segnale** emesso dalla risorsa di destinazione. I segnali possono essere dei tipi seguenti: metrica, log attività, Application Insights e log.

**Criteri** : una combinazione di segnale e logica applicata a una risorsa di destinazione. Esempi: 

- Percentuale CPU > 70%
- Tempo di risposta del server > 4 ms 
- Numero di risultati di una query di log > 100

**Nome avviso** : un nome specifico per la regola di avviso configurata dall'utente.

**Descrizione avviso** : Descrizione della regola di avviso configurata dall'utente.

**Gravità** : la gravità dell'avviso dopo che sono stati soddisfatti i criteri specificati nella regola di avviso. La gravità è compresa tra 0 e 4.

- Gravità 0 = critico
- Gravità 1 = errore
- Gravità 2 = avviso
- Gravità 3 = Informativa
- SEV 4 = verbose 

**Azione**: un'azione specifica eseguita quando viene attivato l'avviso. Per ulteriori informazioni, vedere [gruppi di azioni](./action-groups.md).

## <a name="what-you-can-alert-on"></a>Oggetto degli avvisi

È possibile inviare un avviso sulle metriche e sui log, come descritto in [monitoraggio delle origini dati](./data-sources.md). I segnali includono ma non sono limitati a:

- Valori delle metriche
- Query di ricerca log
- Eventi dei log attività
- Integrità della piattaforma Azure sottostante
- Test per la disponibilità del sito Web

## <a name="manage-alerts"></a>Gestire gli avvisi

È possibile impostare lo stato di un avviso per specificare dove i trova nel processo di risoluzione. Quando vengono soddisfatti i criteri specificati nella regola di avviso, viene creato o generato un avviso e il relativo stato è *nuovo*. È possibile modificare lo stato dell'avviso dopo averlo confermato e chiuso. Tutte le modifiche apportate allo stato vengono archiviate nella cronologia dell'avviso.

Sono supportati i tipi di avviso seguenti.

| State | Descrizione |
|:---|:---|
| Nuovo | Il problema è stato rilevato e non è ancora stato esaminato. |
| Confermato | Un amministratore ha esaminato l'avviso e ha iniziato a lavorare a esso. |
| Chiusa | Il problema è stato risolto. Dopo che un avviso è stato chiuso, è possibile riaprirlo modificandone lo stato. |

Lo *stato dell'avviso* è diverso ed è indipendente dalla *condizione del monitoraggio*. Lo stato dell'avviso viene impostato dall'utente, mentre la condizione del monitoraggio viene impostata dal sistema. Quando viene generato un avviso, la condizione di monitoraggio dell'avviso è impostata su *"attivato"* e quando la condizione sottostante che ha causato l'attivazione dell'avviso viene cancellata, la condizione di monitoraggio è impostata su *"risolto"*. 

Lo stato dell'avviso non viene modificato fino a quando l'utente non esegue l'operazione di modifica. Informazioni su [come modificare lo stato degli avvisi e dei gruppi intelligenti](https://aka.ms/managing-alert-smart-group-states).

## <a name="alerts-experience"></a>Esperienza degli avvisi 
La pagina avvisi predefiniti fornisce un riepilogo degli avvisi creati in un intervallo di tempo specifico. Vengono visualizzati gli avvisi totali per ogni gravità, con colonne che identificano il numero totale di avvisi in ogni stato per ogni gravità. Selezionare uno dei valori di gravità per aprire la pagina [Tutti gli avvisi](#all-alerts-page) filtrata in base al livello di gravità specificato.

È invece possibile [enumerare a livello di codice le istanze di avviso generate nelle sottoscrizioni usando le API REST](#manage-your-alert-instances-programmatically).

> [!NOTE]
   >  È possibile accedere solo agli avvisi generati negli ultimi 30 giorni.

Non Mostra o tiene traccia degli avvisi classici. È possibile modificare le sottoscrizioni o i parametri di filtro per aggiornare la pagina. 

![Screenshot della pagina degli avvisi](media/alerts-overview/alerts-page.png)

È possibile filtrare questa visualizzazione selezionando i valori nei menu a discesa nella parte superiore della pagina.

| Colonna | Descrizione |
|:---|:---|
| Subscription | Selezionare le sottoscrizioni di Azure per cui si desidera visualizzare gli avvisi. Facoltativamente, è possibile scegliere di selezionare tutte le sottoscrizioni. Nella vista sono inclusi solo gli avvisi a cui si ha accesso nelle sottoscrizioni selezionate. |
| Resource group | Selezionare un singolo gruppo di risorse. Sono inclusi nella visualizzazione solo gli avvisi con destinazioni nel gruppo di risorse selezionato. |
| Intervallo di ore | Nella vista sono inclusi solo gli avvisi generati nell'intervallo di tempo selezionato. I valori supportati sono l'ultima ora, le ultime 24 ore, gli ultimi 7 giorni e gli ultimi 30 giorni. |

Selezionare i valori seguenti nella parte superiore della pagina degli avvisi per aprire un'altra pagina:

| Valore | Descrizione |
|:---|:---|
| Totale avvisi | Numero totale di avvisi che corrispondono ai criteri selezionati. Selezionare questo valore per aprire la visualizzazione Tutti gli avvisi senza filtri. |
| Gruppi intelligenti | Numero totale di gruppi intelligenti creati dagli avvisi che corrispondono ai criteri selezionati. Selezionare questo valore per aprire l'elenco dei gruppi intelligenti nella visualizzazione Tutti gli avvisi.
| Totale regole di avviso | Numero totale di regole di avviso nella sottoscrizione o nel gruppo di risorse selezionato. Selezionare questo valore per aprire la visualizzazione Regole, filtrata in base alla sottoscrizione e al gruppo di risorse selezionati.


## <a name="manage-alert-rules"></a>Gestire le regole di avviso
Per visualizzare la pagina **regole** , selezionare **Gestisci regole di avviso**. La pagina regole è un'unica posizione per la gestione di tutte le regole di avviso tra le sottoscrizioni di Azure. Include l'elenco di tutte le regole di avviso che può essere ordinato in base alle risorse di destinazione, ai gruppi di risorse, al nome della regola o allo stato. Da questa pagina è inoltre possibile modificare, abilitare o disabilitare le regole di avviso.  

 ![Screenshot della pagina delle regole](./media/alerts-overview/alerts-preview-rules.png)


## <a name="create-an-alert-rule"></a>Creare una regola di avviso
È possibile creare regole di avviso in modo coerente, indipendentemente dal servizio di monitoraggio o dal tipo di segnale.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tflw]

 
Ecco come creare una nuova regola di avviso:
1. Selezionare la _destinazione_ per l'avviso.
1. Selezionare il _segnale_ dai segnali disponibili per la destinazione.
1. Specificare la _logica_ da applicare ai dati del segnale.

Per eseguire questa procedura semplificata, l'utente non deve più conoscere l'origine di monitoraggio o i segnali supportati prima di selezionare una risorsa di Azure. L'elenco dei segnali disponibili è filtrato automaticamente in base alla risorsa di destinazione selezionata. Inoltre, in base a tale destinazione, viene illustrata la definizione automatica della logica della regola di avviso.  

Altre informazioni su come creare regole di avviso sono disponibili in [Creare, visualizzare e gestire gli avvisi tramite Monitoraggio di Azure](./alerts-metric.md).

Gli avvisi sono disponibili in vari servizi di monitoraggio di Azure. Per informazioni su come e quando usare ognuno di questi servizi, vedere [Monitoraggio di applicazioni e risorse di Azure](../overview.md). 


## <a name="all-alerts-page"></a>Pagina Tutti gli avvisi 
Per visualizzare la pagina **tutti gli avvisi** , selezionare **Total Alerts**. Qui è possibile visualizzare un elenco di avvisi creati nell'intervallo di tempo selezionato. È possibile visualizzare un elenco dei singoli avvisi o un elenco dei gruppi intelligenti contenente gli avvisi. Selezionare il banner nella parte superiore della pagina per passare da una visualizzazione all'altra.

![Screenshot della pagina tutti gli avvisi](media/alerts-overview/all-alerts-page.png)

È possibile filtrare la visualizzazione selezionando i valori seguenti nei menu a discesa nella parte superiore della pagina:

| Colonna | Descrizione |
|:---|:---|
| Subscription | Selezionare le sottoscrizioni di Azure per cui si desidera visualizzare gli avvisi. Facoltativamente, è possibile scegliere di selezionare tutte le sottoscrizioni. Nella vista sono inclusi solo gli avvisi a cui si ha accesso nelle sottoscrizioni selezionate. |
| Resource group | Selezionare un singolo gruppo di risorse. Sono inclusi nella visualizzazione solo gli avvisi con destinazioni nel gruppo di risorse selezionato. |
| Tipo di risorsa | Selezionare uno o più tipi di risorsa. Sono inclusi nella visualizzazione solo gli avvisi con destinazioni del tipo selezionato. Questa colonna risulta disponibile solo dopo che è stato specificato un gruppo di risorse. |
| Risorsa | Selezionare una risorsa. Nella visualizzazione vengono inclusi solo gli avvisi con tale risorsa definita come destinazione. Questa colonna risulta disponibile solo dopo che è stato specificato un tipo di risorsa. |
| Gravità | Selezionare un livello di gravità dell'avviso oppure selezionare **tutto** per includere gli avvisi di tutti i livelli di gravità. |
| Condizione di monitoraggio | Selezionare una condizione di monitoraggio oppure selezionare **tutto** per includere gli avvisi di tutte le condizioni. |
| Stato dell'avviso | Selezionare uno stato di avviso o selezionare **tutti** per includere gli avvisi di tutti gli Stati. |
| Servizio di monitoraggio | Selezionare un servizio oppure selezionare **tutto** per includere tutti i servizi. Sono inclusi solo gli avvisi creati da regole che usano tale servizio come destinazione. |
| Intervallo di ore | Nella vista sono inclusi solo gli avvisi generati nell'intervallo di tempo selezionato. I valori supportati sono l'ultima ora, le ultime 24 ore, gli ultimi 7 giorni e gli ultimi 30 giorni. |

Selezionare le **colonne** nella parte superiore della pagina per selezionare le colonne da visualizzare. 

## <a name="alert-details-page"></a>Pagina dei dettagli dell'avviso
Quando si seleziona un avviso, in questa pagina vengono forniti i dettagli dell'avviso e viene possibile modificarne lo stato.

![Screenshot della pagina dei dettagli dell'avviso](media/alerts-overview/alert-detail2.png)

Nella pagina Dettagli avviso sono incluse le seguenti sezioni:

| Sezione | Descrizione |
|:---|:---|
| Riepilogo | Mostra le proprietà e altre informazioni significative sull'avviso. |
| Cronologia | Elenca tutte le azioni eseguite dall'avviso e tutte le modifiche apportate all'avviso. Questa opzione è attualmente limitata alle modifiche di stato. |
| Diagnostica | Informazioni sul gruppo intelligente in cui è incluso l'avviso. *Conteggio avvisi* si riferisce al numero di avvisi inclusi nel gruppo intelligente. Include altri avvisi nello stesso gruppo intelligente creati negli ultimi 30 giorni, indipendentemente dal filtro temporale nella pagina dell'elenco degli avvisi. Selezionare un avviso per visualizzarne i dettagli. |

## <a name="role-based-access-control-rbac-for-your-alert-instances"></a>Controllo degli accessi in base al ruolo (RBAC) per le istanze di avviso

Per il consumo e la gestione delle istanze di avviso è necessario che l'utente disponga dei ruoli predefiniti di Azure di [monitoraggio collaboratore](../../role-based-access-control/built-in-roles.md#monitoring-contributor) o del [lettore di monitoraggio](../../role-based-access-control/built-in-roles.md#monitoring-reader). Questi ruoli sono supportati in qualsiasi ambito Azure Resource Manager, dal livello di sottoscrizione alle assegnazioni granulari a livello di risorsa. Se, ad esempio, un utente dispone solo dell'accesso di monitoraggio collaboratore per la macchina virtuale `ContosoVM1` , tale utente potrà utilizzare e gestire solo gli avvisi generati in `ContosoVM1` .

## <a name="manage-your-alert-instances-programmatically"></a>Gestire le istanze di avviso a livello di codice

Potrebbe essere necessario eseguire una query a livello di codice per gli avvisi generati per la sottoscrizione. È possibile che le query creino visualizzazioni personalizzate al di fuori della portale di Azure o per analizzare gli avvisi per identificare modelli e tendenze.

È possibile eseguire una query per gli avvisi generati per le sottoscrizioni usando l' [API REST di gestione avvisi](https://aka.ms/alert-management-api) o il [grafico delle risorse di Azure](../../governance/resource-graph/overview.md) e l' [API REST per le risorse](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources).

L'API REST del grafico risorse per le risorse consente di eseguire una query per le istanze di avviso su larga scala. Il grafico risorse è consigliato quando è necessario gestire gli avvisi generati in più sottoscrizioni. 

La richiesta di esempio seguente all'API REST del grafico risorse restituisce il numero di avvisi all'interno di una sottoscrizione:

```json
{
  "subscriptions": [
    <subscriptionId>
  ],
  "query": "AlertsManagementResources | where type =~ 'Microsoft.AlertsManagement/alerts' | summarize count()"
}
```

È anche possibile visualizzare il risultato di questa query sul grafico delle risorse nel portale con Azure Resource Graph Explorer: [Portal.Azure.com](https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/AlertsManagementResources%20%7C%20where%20type%20%3D~%20%27Microsoft.AlertsManagement%2Falerts%27%20%7C%20summarize%20count())

È possibile eseguire una query sugli avvisi per i campi [essenziali](alerts-common-schema-definitions.md#essentials) .

Usare l' [API REST di gestione avvisi](https://aka.ms/alert-management-api) per ottenere altre informazioni su avvisi specifici, inclusi i relativi campi di [contesto degli avvisi](alerts-common-schema-definitions.md#alert-context) .

## <a name="smart-groups"></a>Gruppi intelligenti

I gruppi intelligenti sono aggregazioni di avvisi basati sugli algoritmi di Machine Learning, che possono contribuire a ridurre il rumore degli avvisi e ad agevolare la risoluzione dei problemi. [Sono disponibili altre informazioni sui gruppi intelligenti](https://aka.ms/smart-groups) e su [come gestire i gruppi intelligenti](https://aka.ms/managing-smart-groups).

## <a name="next-steps"></a>Passaggi successivi

- [Altre informazioni sui gruppi intelligenti](https://aka.ms/smart-groups)
- [Informazioni sui gruppi di azioni](./action-groups.md)
- [Managing your alert instances in Azure](https://aka.ms/managing-alert-instances) (Gestione delle istanze di avvio in Azure)
- [Gestione di gruppi intelligenti](https://aka.ms/managing-smart-groups)
- [Scopri di più sui prezzi per gli avvisi di Azure](https://azure.microsoft.com/pricing/details/monitor/)
