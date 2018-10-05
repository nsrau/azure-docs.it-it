---
title: Comprendere il funzionamento degli avvisi delle metriche in Monitoraggio di Azure.
description: È disponibile una panoramica delle operazioni eseguibili con gli avvisi delle metriche e del relativo funzionamento in Monitoraggio di Azure.
author: snehithm
ms.author: snmuvva
ms.date: 9/18/2018
ms.topic: conceptual
ms.service: azure-monitor
ms.component: alerts
ms.openlocfilehash: 586ced5b239b77dd9ae596a754613a66cee371a9
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2018
ms.locfileid: "47405921"
---
# <a name="understand-how-metric-alerts-work-in-azure-monitor"></a>Comprendere il funzionamento degli avvisi delle metriche in Monitoraggio di Azure

Gli avvisi delle metriche in Monitoraggio di Azure si basano su metriche multidimensionali. Queste metriche possono essere metriche di piattaforma, [metriche personalizzate](metrics-custom-overview.md), [log comuni di Log Analytics convertiti in metriche](monitoring-metric-alerts-logs.md) e metriche standard di Application Insights. Gli avvisi delle metriche eseguono valutazioni a intervalli regolari per verificare se le condizioni in una o più serie temporale di metriche vengono soddisfatte e, in caso affermativo, inviano una notifica. Gli avvisi delle metriche sono avvisi con stato, ovvero inviano notifiche solo quando lo stato cambia.

## <a name="how-do-metric-alerts-work"></a>Funzionamento degli avvisi delle metriche

È possibile definire una regola di avviso per la metrica specificando una risorsa di destinazione da monitorare, il nome e la condizione della metrica (un operatore e una soglia) e un gruppo di azioni da attivare quando viene generata la regola di avviso.
Si supponga di aver creato una semplice regola di avviso per la metrica come indicato di seguito:

- Risorsa di destinazione (risorsa di Azure da monitorare): myVM
- Metrica: CPU percentuale
- Aggregazione temporale (statistica eseguita su valori di metriche non elaborati. Le aggregazioni temporali supportate sono Min, Max, Avg, Total (Min, Max, Media, Totale): Average (Media)
- Periodo (finestra temporale a fronte della quale vengono controllati i valori delle metriche): Negli ultimi 5 minuti
- Frequenza (frequenza con cui l'avviso della metrica controlla se vengono soddisfatte le condizioni): 1 min
- Operatore:     Maggiore di
- Soglia:      70

Dal momento in cui viene creata la regola di avviso, il monitoraggio viene eseguito ogni minuto, analizza i valori delle metriche per gli ultimi 5 minuti e controlla se la media di questi valori supera 70. Se la condizione viene soddisfatta, ovvero il valore medio di CPU percentuale per gli ultimi 5 minuti supera 70, la regola di avviso genera una notifica attivata. Se è stata configurata un'azione di webhook o posta elettronica nel gruppo di azioni associato alla regola di avviso, si riceverà una notifica attivata su entrambe.

Questa istanza specifica di generazione della regola di avviso può essere visualizzata anche nel portale di Azure, nel pannello Tutti gli avvisi.

Se l'utilizzo in "myVM" rimane al di sopra della soglia nei controlli successivi, la regola di avviso non verrà nuovamente generata fino alla risoluzione della condizione.

Dopo un po' di tempo, se l'utilizzo in "myVM" torna alla condizione normale, ovvero scende sotto la soglia specificata, la regola di avviso monitora la condizione altre due volte, per inviare una notifica risolta. La regola di avviso invia una notifica risolta/disattivata se la condizione di avviso non viene soddisfatta per tre volte consecutive, per ridurre il rumore in caso di condizioni instabili.

Quando la notifica risolta viene inviata tramite posta elettronica o webhook, anche lo stato dell'istanza di avviso (denominato stato di monitoraggio) nel portale di Azure viene impostato come risolto.

## <a name="monitoring-at-scale-using-metric-alerts-in-azure-monitor"></a>Monitoraggio su larga scala mediante gli avvisi delle metriche in Monitoraggio di Azure

### <a name="using-dimensions"></a>Uso delle dimensioni

Gli avvisi delle metriche in Monitoraggio di Azure supportano anche il monitoraggio di più combinazioni di valori di dimensioni con una sola regola. I motivi per cui usare più combinazioni di dimensioni vengono qui illustrati con l'aiuto di un esempio.

Si supponga di avere un piano di servizio app per il sito Web. Si intende monitorare l'utilizzo della CPU in più istanze che eseguono il sito Web o l'app. A tale scopo, è possibile usare una regola di avviso per la metrica come indicato di seguito

- Risorsa di destinazione: myAppServicePlan
- Metrica: CPU percentuale
- Dimensioni
  - Istanza = InstanceName1, InstanceName2
- Aggregazione temporale: Media
- Periodo: Negli ultimi 5 minuti
- Frequenza: 1 minuto
- Operatore: Maggiore di
- Soglia: 70

Come in precedenza, questa regola monitora se l'utilizzo medio della CPU negli ultimi 5 minuti supera il 70%. Tuttavia, con la stessa regola è possibile monitorare due istanze che eseguono il sito Web. Ogni istanza verrà monitorata individualmente e si riceveranno notifiche individuali.

Si supponga di avere un'app Web che registra una richiesta elevata ed è necessario aggiungere altre istanze. La regola precedente continua a monitorare solo due istanze. Tuttavia, è possibile creare una regola come indicato di seguito.

- Risorsa di destinazione: myAppServicePlan
- Metrica: CPU percentuale
- Dimensioni
  - Istanza = *
- Aggregazione temporale: Media
- Periodo: Negli ultimi 5 minuti
- Frequenza: 1 minuto
- Operatore: Maggiore di
- Soglia: 70

Questa regola monitorerà automaticamente tutti i valori per l'istanza, ovvero è possibile monitorare le istanze in tempo reale senza dover modificare nuovamente la regola di avviso per la metrica.

### <a name="monitoring-multiple-resources-using-metric-alerts"></a>Monitoraggio di più risorse con gli avvisi delle metriche

Come si è visto nella sezione precedente, è possibile avere una singola regola di avviso per la metrica che monitora ogni singola combinazione di dimensioni (ovvero, una serie temporale di metriche). In precedenza monitoraggio era comunque limitato a una singola risorsa alla volta. Monitoraggio di Azure supporta anche il monitoraggio di più risorse con una regola di avviso per la metrica. Questa funzionalità è attualmente in anteprima e supportata solo nelle macchine virtuali. Un singolo avviso per la metrica, inoltre, può monitorare le risorse in una sola area di Azure.

È possibile specificare l'ambito del monitoraggio con un singolo avviso per la metrica in uno dei tre modi seguenti:

- come elenco di macchine virtuali in un'area di Azure all'interno di una sottoscrizione
- tutte le macchine virtuali (in un'area di Azure) in uno o più gruppi di risorse in una sottoscrizione
- tutte le macchine virtuali (in un'area di Azure) in una sottoscrizione

La creazione di regole di avviso per le metriche che monitorano più risorse non è attualmente supportata tramite il portale di Azure. È possibile creare queste regole tramite [modelli di Azure Resource Manager](monitoring-create-metric-alerts-with-templates.md#resource-manager-template-for-metric-alert-that-monitors-multiple-resources). Si riceveranno notifiche specifiche per ogni macchina virtuale. 

## <a name="typical-latency"></a>Latenza tipica

Per gli avvisi delle metriche si riceve in genere una notifica in meno di 5 minuti se si imposta la frequenza delle regole di avviso su 1 minuto. In caso di carico elevato per i sistemi di notifica, la latenza potrebbe essere superiore.

## <a name="supported-resource-types-for-metric-alerts"></a>Tipi di risorse supportati per gli avvisi delle metriche

L'elenco completo dei tipi di risorse supportati è disponibile in questo [articolo](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported)

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su come creare, visualizzare e gestire gli avvisi delle metriche in Azure](alert-metric.md)
- [Informazioni su come distribuire gli avvisi delle metriche mediante modelli di Azure Resource Manager](monitoring-create-metric-alerts-with-templates.md)
- [Altre informazioni sui gruppi di azioni](monitoring-action-groups.md)
