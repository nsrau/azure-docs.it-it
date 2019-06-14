---
title: Comprendere il funzionamento degli avvisi delle metriche in Monitoraggio di Azure.
description: È disponibile una panoramica delle operazioni eseguibili con gli avvisi delle metriche e del relativo funzionamento in Monitoraggio di Azure.
author: snehithm
ms.author: snmuvva
ms.date: 9/18/2018
ms.topic: conceptual
ms.service: azure-monitor
ms.subservice: alerts
ms.openlocfilehash: ce65d87142df64a9f0c27f3acdb4d6f25e86fb8a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071636"
---
# <a name="understand-how-metric-alerts-work-in-azure-monitor"></a>Comprendere il funzionamento degli avvisi delle metriche in Monitoraggio di Azure

Gli avvisi delle metriche in Monitoraggio di Azure si basano su metriche multidimensionali. Queste metriche possono essere [metriche di piattaforma](alerts-metric-near-real-time.md#metrics-and-dimensions-supported), [metriche personalizzate](../../azure-monitor/platform/metrics-custom-overview.md), [log comuni di Monitoraggio di Azure convertiti in metriche](../../azure-monitor/platform/alerts-metric-logs.md) e metriche di Application Insights. Gli avvisi delle metriche eseguono valutazioni a intervalli regolari per verificare se le condizioni in una o più serie temporale di metriche vengono soddisfatte e, in caso affermativo, inviano una notifica. Gli avvisi delle metriche sono avvisi con stato, ovvero inviano notifiche solo quando lo stato cambia.

## <a name="how-do-metric-alerts-work"></a>Funzionamento degli avvisi delle metriche

È possibile definire una regola di avviso delle metriche specificando una risorsa di destinazione da monitorare, il nome della metrica, il tipo di condizione (statica o dinamica) e la condizione (un operatore e una soglia/sensibilità), oltre a un gruppo di azioni da attivare quando viene generata la regola di avviso. I tipi di condizione influiscono sulla modalità di determinazione delle soglie. [Altre informazioni sulle opzioni di sensibilità e i tipi di condizione delle soglie dinamiche](alerts-dynamic-thresholds.md).

### <a name="alert-rule-with-static-condition-type"></a>Regola di avviso con tipo di condizione statica

Supponiamo di aver creato una semplice regola di avviso delle metriche con soglia statica, come indicato di seguito:

- Risorsa di destinazione (risorsa di Azure da monitorare): myVM
- Metrica: CPU percentuale
- Tipo di condizione: statico
- Aggregazione temporale (statistica eseguita su valori di metriche non elaborati. Le aggregazioni in tempo supportati sono Min, Max, Avg, totale, conteggio): Media
- Periodo (finestra temporale a fronte della quale vengono controllati i valori delle metriche): Negli ultimi 5 minuti
- Frequenza (frequenza con cui l'avviso della metrica controlla se vengono soddisfatte le condizioni): 1 min
- Operatore: Maggiore di
- Soglia: 70

Dal momento in cui viene creata la regola di avviso, il monitoraggio viene eseguito ogni minuto, analizza i valori delle metriche per gli ultimi 5 minuti e controlla se la media di questi valori supera 70. Se la condizione viene soddisfatta, ovvero il valore medio di CPU percentuale per gli ultimi 5 minuti supera 70, la regola di avviso genera una notifica attivata. Se è stata configurata un'azione di webhook o posta elettronica nel gruppo di azioni associato alla regola di avviso, si riceverà una notifica attivata su entrambe.

Quando si usa più condizioni in una sola regola, la regola "and" le condizioni tra loro.  Vale a dire, l'avviso viene attivato quando tutte le condizioni di avviso vengono valutate come true e risolvere quando una delle condizioni non è più true. Esempio di questo tipo di avviso può essere generare un avviso quando "Superiore al 90% della CPU" e "lunghezza coda è più di 300 elementi". 

### <a name="alert-rule-with-dynamic-condition-type"></a>Regola di avviso con tipo di condizione dinamica

Supponiamo di aver creato una semplice regola di avviso delle metriche con soglie dinamiche, come indicato di seguito:

- Risorsa di destinazione (risorsa di Azure da monitorare): myVM
- Metrica: CPU percentuale
- Tipo di condizione: Dynamic
- Aggregazione temporale (statistica eseguita su valori di metriche non elaborati. Le aggregazioni in tempo supportati sono Min, Max, Avg, totale, conteggio): Media
- Periodo (finestra temporale a fronte della quale vengono controllati i valori delle metriche): Negli ultimi 5 minuti
- Frequenza (frequenza con cui l'avviso della metrica controlla se vengono soddisfatte le condizioni): 1 min
- Operatore: Maggiore di
- Sensibilità: Media
- Periodi da controllare: 4
- Numero di violazioni: 4

Dopo la creazione della regola di avviso, l'algoritmo di Machine Learning relativo alle soglie dinamiche acquisisce i dati cronologici disponibili, calcola la soglia più adatta al modello di comportamento della serie di metriche e, in base ai nuovi dati, migliora continuamente l'apprendimento per rendere la soglia più precisa.

Dal momento in cui viene creata la regola di avviso, ogni minuto vengono monitorati i valori delle metriche degli ultimi 20 minuti raggruppati in periodi di 5 minuti per verificare se la media dei valori del periodo in ognuno dei 4 periodi supera la soglia prevista. Se la condizione viene soddisfatta, ossia se la media di Percentuale CPU negli ultimi 20 minuti (quattro periodi di 5 minuti) si è discostata per quattro volte dal comportamento previsto, la regola di avviso genera una notifica attivata. Se è stata configurata un'azione di webhook o posta elettronica nel gruppo di azioni associato alla regola di avviso, si riceverà una notifica attivata su entrambe.

### <a name="view-and-resolution-of-fired-alerts"></a>Visualizzazione e risoluzione degli avvisi generati

Gli esempi di generazione di regole di avviso riportati sopra possono essere visualizzati anche nel portale di Azure, nel pannello **Tutti gli avvisi**.

Se l'utilizzo in "myVM" rimane al di sopra della soglia nei controlli successivi, la regola di avviso non verrà nuovamente generata fino alla risoluzione delle condizioni.

Dopo un po' di tempo, se l'utilizzo in "myVM" torna alla condizione normale, ovvero scende sotto la soglia, la regola di avviso monitora la condizione altre due volte, per inviare una notifica risolta. La regola di avviso invia una notifica risolta/disattivata se la condizione di avviso non viene soddisfatta per tre volte consecutive, per ridurre il rumore in caso di condizioni instabili.

Quando la notifica risolta viene inviata tramite posta elettronica o webhook, anche lo stato dell'istanza di avviso (denominato stato di monitoraggio) nel portale di Azure viene impostato come risolto.

### <a name="using-dimensions"></a>Uso delle dimensioni

Gli avvisi delle metriche in Monitoraggio di Azure supportano anche il monitoraggio di più combinazioni di valori di dimensioni con una sola regola. I motivi per cui usare più combinazioni di dimensioni vengono qui illustrati con l'aiuto di un esempio.

Si supponga di avere un piano di servizio app per il sito Web. Si intende monitorare l'utilizzo della CPU in più istanze che eseguono il sito Web o l'app. A tale scopo, è possibile usare una regola di avviso delle metriche come indicato di seguito:

- Risorsa di destinazione: myAppServicePlan
- Metrica: CPU percentuale
- Tipo di condizione: statico
- Dimensioni
  - Istanza = InstanceName1, InstanceName2
- Aggregazione temporale: Media
- Periodo: Negli ultimi 5 minuti
- Frequenza: 1 min
- Operatore: GreaterThan
- Soglia: 70

Come in precedenza, questa regola monitora se l'utilizzo medio della CPU negli ultimi 5 minuti supera il 70%. Tuttavia, con la stessa regola è possibile monitorare due istanze che eseguono il sito Web. Ogni istanza verrà monitorata individualmente e si riceveranno notifiche individuali.

Supponiamo di avere un'app Web che registra un numero di richieste elevato, a causa delle quali è necessario aggiungere altre istanze. La regola precedente continua a monitorare solo due istanze. Tuttavia, è possibile creare una regola nel modo seguente:

- Risorsa di destinazione: myAppServicePlan
- Metrica: CPU percentuale
- Tipo di condizione: statico
- Dimensioni
  - Istanza = *
- Aggregazione temporale: Media
- Periodo: Negli ultimi 5 minuti
- Frequenza: 1 min
- Operatore: GreaterThan
- Soglia: 70

Questa regola monitorerà automaticamente tutti i valori per l'istanza, ovvero è possibile monitorare le istanze in tempo reale senza dover modificare nuovamente la regola di avviso per la metrica.

Quando vengono monitorate più dimensioni, le regole di avviso con soglie dinamiche possono definire soglie personalizzate per centinaia di serie di metriche alla volta. Le soglie dinamiche riducono il numero di regole di avviso da gestire e consentono un notevole risparmio di tempo sulla gestione e la creazione delle regole di avviso.

Supponiamo di avere un'app Web con numerose istanze e di non sapere quale sia la soglia più adatta. Le regole descritte sopra usano sempre una soglia del 70%. Tuttavia, è possibile creare una regola nel modo seguente:

- Risorsa di destinazione: myAppServicePlan
- Metrica: CPU percentuale
- Tipo di condizione: Dynamic
- Dimensioni
  - Istanza = *
- Aggregazione temporale: Media
- Periodo: Negli ultimi 5 minuti
- Frequenza: 1 min
- Operatore: GreaterThan
- Sensibilità: Media
- Periodi da controllare: 1
- Numero di violazioni: 1

Questa regola monitora se l'utilizzo medio della CPU negli ultimi 5 minuti supera il comportamento previsto per ogni istanza. Con la stessa regola è possibile monitorare le istanze in tempo reale senza dover modificare nuovamente la regola di avviso per la metrica. Ogni istanza otterrà una soglia adatta al modello di comportamento della serie di metriche e cambierà continuamente in base ai nuovi dati per rendere la soglia più precisa. Ogni istanza verrà monitorata individualmente e si riceveranno notifiche individuali.

È anche possibile aumentare i periodi da monitorare e il numero di violazioni per consentire il filtro degli avvisi in modo che vengano generati solo in base alla definizione di deviazione significativa specificata dall'utente. [Altre informazioni sulle opzioni avanzate delle soglie dinamiche](alerts-dynamic-thresholds.md#what-do-the-advanced-settings-in-dynamic-thresholds-mean).

## <a name="monitoring-at-scale-using-metric-alerts-in-azure-monitor"></a>Monitoraggio su larga scala mediante gli avvisi delle metriche in Monitoraggio di Azure

Fino a questo punto abbiamo visto che un solo avviso di metrica può essere usato per monitorare una o molte serie temporali di metriche correlate a una singola risorsa di Azure. Spesso si ha l'esigenza di applicare la stessa regola di avviso a molte risorse. Monitoraggio di Azure supporta anche il monitoraggio di più risorse con una regola di avviso per la metrica. Questa funzionalità è attualmente supportata solo nelle macchine virtuali. Un singolo avviso per la metrica, inoltre, può monitorare le risorse in una sola area di Azure.

È possibile specificare l'ambito del monitoraggio con un singolo avviso per la metrica in uno dei tre modi seguenti:

- come elenco di macchine virtuali in un'area di Azure all'interno di una sottoscrizione
- tutte le macchine virtuali (in un'area di Azure) in uno o più gruppi di risorse in una sottoscrizione
- tutte le macchine virtuali (in un'area di Azure) in una sottoscrizione

La creazione di regole di avviso per le metriche che monitorano più risorse equivale a [creare qualsiasi altro avviso per le metriche](alerts-metric.md) che monitora una sola risorsa. L'unica differenza è che occorre selezionare tutte le risorse da monitorare. È possibile creare queste regole anche tramite i [modelli di Azure Resource Manager](../../azure-monitor/platform/alerts-metric-create-templates.md#template-for-metric-alert-that-monitors-multiple-resources). Si riceveranno notifiche specifiche per ogni macchina virtuale.

## <a name="typical-latency"></a>Latenza tipica

Per gli avvisi delle metriche si riceve in genere una notifica in meno di 5 minuti se si imposta la frequenza delle regole di avviso su 1 minuto. In caso di carico elevato per i sistemi di notifica, la latenza potrebbe essere superiore.

## <a name="supported-resource-types-for-metric-alerts"></a>Tipi di risorse supportati per gli avvisi delle metriche

L'elenco completo dei tipi di risorse supportati è disponibile in questo [articolo](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported).

Se si usano avvisi delle metriche classici e si vuole verificare se gli avvisi delle metriche supportano tutti i tipi di risorsa usati, nella tabella seguente sono elencati i tipi di risorsa supportati dagli avvisi delle metriche classici e viene indicato se tali tipi di risorsa sono attualmente supportati dagli avvisi delle metriche oppure no.

|Tipo di risorsa supportato da avvisi delle metriche classici | Supportato dagli avvisi delle metriche |
|-------------------------------------------------|----------------------------|
| Microsoft.ApiManagement/service | Yes |
| Microsoft.Batch/batchAccounts| Yes|
|Microsoft.Cache/redis| Yes |
|Microsoft.ClassicCompute/virtualMachines | No |
|Microsoft.ClassicCompute/domainNames/slots/roles | No|
|Microsoft.CognitiveServices/accounts | No |
|Microsoft.Compute/virtualMachines | Yes|
|Microsoft.Compute/virtualMachineScaleSets| Yes|
|Microsoft.ClassicStorage/storageAccounts| No |
|Microsoft.DataFactory/datafactories | Yes|
|Microsoft.DBforMySQL/servers| Yes|
|Microsoft.DBforPostgreSQL/servers| Yes|
|Microsoft.Devices/IotHubs | No|
|Microsoft.DocumentDB/databaseAccounts| Yes|
|Microsoft.EventHub/namespaces | Yes|
|Microsoft.Logic/workflows | Yes|
|Microsoft.Network/loadBalancers |Yes|
|Microsoft.Network/publicIPAddresses| Yes|
|Microsoft.Network/applicationGateways| Yes|
|Microsoft.Network/expressRouteCircuits| Yes|
|Microsoft.Network/trafficManagerProfiles | Yes|
|Microsoft.Search/searchServices | Yes|
|Microsoft.ServiceBus/namespaces| Yes |
|Microsoft.Storage/storageAccounts | Yes|
|Microsoft.StreamAnalytics/streamingjobs| Yes|
|Microsoft.TimeSeriesInsights/environments | Yes|
|Microsoft. Web/serverfarms | Yes |
|Microsoft. Web/sites (escluse le funzioni) | Yes|
|Microsoft. Web/hostingEnvironments/multiRolePools | No|
|Microsoft. Web/hostingEnvironments/workerPools| No |
|Microsoft.SQL/Servers | No |

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su come creare, visualizzare e gestire gli avvisi delle metriche in Azure](alerts-metric.md)
- [Informazioni su come distribuire gli avvisi delle metriche mediante modelli di Azure Resource Manager](../../azure-monitor/platform/alerts-metric-create-templates.md)
- [Altre informazioni sui gruppi di azioni](action-groups.md)
- [Altre informazioni sul tipo di condizione delle soglie dinamiche](alerts-dynamic-thresholds.md)
