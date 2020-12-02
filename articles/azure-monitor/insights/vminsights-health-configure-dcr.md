---
title: Configurare il monitoraggio in Monitoraggio di Azure per le macchine virtuali integrità Guest usando le regole di raccolta dati (anteprima)
description: Viene descritto come modificare il monitoraggio predefinito in Monitoraggio di Azure per le macchine virtuali integrità Guest su larga scala usando modelli Gestione risorse.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2020
ms.openlocfilehash: 55e5a587a0ad02fa1f8993027b46162a14a58832
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96448244"
---
# <a name="configure-monitoring-in-azure-monitor-for-vms-guest-health-using-data-collection-rules-preview"></a>Configurare il monitoraggio in Monitoraggio di Azure per le macchine virtuali integrità Guest usando le regole di raccolta dati (anteprima)
[Monitoraggio di Azure per le macchine virtuali integrità Guest](vminsights-health-overview.md) consente di visualizzare l'integrità di una macchina virtuale in base a quanto definito da un set di misurazioni delle prestazioni campionate a intervalli regolari. Questo articolo descrive come è possibile modificare il monitoraggio predefinito tra più macchine virtuali usando le regole di raccolta dati.


## <a name="monitors"></a>Monitoraggi
Lo stato di integrità di una macchina virtuale è determinato dal [rollup dello](vminsights-health-overview.md#health-rollup-policy) stato da ciascuno dei monitoraggi. Esistono due tipi di monitoraggi in Monitoraggio di Azure per le macchine virtuali integrità Guest, come illustrato nella tabella seguente.

| Monitorare | Descrizione |
|:---|:---|
| Monitoraggio unità | Misura alcuni aspetti di una risorsa o di un'applicazione. Questo potrebbe controllare un contatore delle prestazioni per determinare le prestazioni della risorsa o la relativa disponibilità. |
| Monitoraggio aggregato | Raggruppa più monitoraggi per fornire un singolo stato di integrità aggregato. Un monitoraggio aggregato può contenere uno o più monitoraggi unità e altri monitoraggi aggregati. |

Il set di monitoraggi utilizzati da Monitoraggio di Azure per le macchine virtuali integrità Guest e la relativa configurazione non possono essere modificati direttamente. È tuttavia possibile creare [sostituzioni](#overrides) che modificano il comportamento della configurazione predefinita. Le sostituzioni sono definite in regole di raccolta dati. È possibile creare più regole di raccolta dati ognuna contenente più sostituzioni per ottenere la configurazione di monitoraggio richiesta.

## <a name="monitor-properties"></a>Proprietà monitoraggio
Nella tabella seguente vengono descritte le proprietà che possono essere configurate in ogni monitoraggio.

| Proprietà | Monitoraggi | Descrizione |
|:---|:---|:---|
| Attivato | Aggregate<br>Unità | Se true, il monitoraggio dello stato viene calcolato e contribuisce all'integrità della macchina virtuale. Può attivare un avviso di avviso è abilitato. |
| Creazione di avvisi | Aggregate<br>Unità | Se true, viene attivato un avviso per il monitoraggio quando lo stato viene spostato in uno stato non integro. Se false, lo stato del monitoraggio contribuirà comunque all'integrità della macchina virtuale che potrebbe attivare un avviso. |
| Avviso | Unità | Criteri per lo stato di avviso. Se non è presente, il monitoraggio non entrerà mai in uno stato di avviso. |
| Critico | Unità | Criteri per lo stato critico. Se None, il monitoraggio non entrerà mai in uno stato critico. |
| Frequenza di valutazione | Unità | Frequenza con cui viene valutato lo stato di integrità. |
| Lookback | Unità | Dimensioni della finestra lookback in secondi. Per una descrizione dettagliata, vedere [elemento monitorConfiguration](#monitorconfiguration-element) . |
| Tipo di valutazione | Unità | Definisce il valore da utilizzare dal set di campionamento. Per una descrizione dettagliata, vedere [elemento monitorConfiguration](#monitorconfiguration-element) . |
| Esempio minimo | Unità | Numero minimo di valori da utilizzare per calcolare il valore. |
| Esempio max | Unità | Numero massimo di valori da utilizzare per calcolare il valore. |


## <a name="default-configuration"></a>Configurazione predefinita
Nella tabella seguente viene elencata la configurazione predefinita per ogni monitoraggio. Questa configurazione predefinita non può essere modificata direttamente, ma è possibile definire [sostituzioni](#overrides) che modificheranno la configurazione del monitoraggio per determinate macchine virtuali.


| Monitorare | Enabled | Creazione di avvisi | Avviso | Critico | Frequenza di valutazione | Lookback | Tipo di valutazione | Esempio minimo | Numero massimo di campioni |
|:---|:---|:---|:---|:---|:---|:---|:---|:---|:---|
| Uso della CPU  | Vero | Falso | Nessuno | \> 90%    | 60 secondi | 240 sec | Min | 2 | 3 |
| Memoria disponibile | Vero | Falso | Nessuno | \< 100 MB | 60 secondi | 240 sec | Max | 2 | 3 |
| File system      | Vero | Falso | Nessuno | \< 100 MB | 60 secondi | 120 sec | Max | 1 | 1 |


## <a name="overrides"></a>Override
Un *override* modifica una o più proprietà di un monitoraggio. Una sostituzione, ad esempio, potrebbe disabilitare un monitoraggio abilitato per impostazione predefinita, definire i criteri di avviso per il monitoraggio o modificare la soglia critica del monitoraggio. 

Le sostituzioni sono definite in una [regola di raccolta dati (DCR)](../platform/data-collection-rule-overview.md). È possibile creare più DCR con diversi set di sostituzioni e applicarli a più macchine virtuali. Per applicare un DCR a una macchina virtuale, creare un'associazione, come descritto in [configurare la raccolta dati per l'agente di monitoraggio di Azure (anteprima)](../platform/data-collection-rule-azure-monitor-agent.md#data-collection-rule-associations).


## <a name="multiple-overrides"></a>Più sostituzioni

Un singolo monitoraggio può avere più sostituzioni. Se le sostituzioni definiscono proprietà diverse, la configurazione risultante è una combinazione di tutte le sostituzioni.

Il monitoraggio, ad esempio, `memory|available` non specifica una soglia di avviso o Abilita gli avvisi per impostazione predefinita. Prendere in considerazione le sostituzioni seguenti applicate a questo monitoraggio:

- Override 1 definisce `alertConfiguration.isEnabled` il valore della proprietà come `true`
- L'override 2 definisce `monitorConfiguration.warningCondition` con una condizione di soglia di `< 250` .

La configurazione risultante è un monitoraggio che entra in uno stato di avviso quando è disponibile meno di 250Mb di memoria e crea un avviso di gravità 2 e passa allo stato di integrità critico quando è disponibile meno di 100 MB di memoria disponibile e crea la gravità dell'avviso 1 (o modifica l'avviso esistente dalla gravità 2 a 1 se è già esistito).

Se due override definiscono la stessa proprietà nello stesso monitoraggio, un valore avrà la precedenza. Gli override verranno applicati in base al relativo [ambito](#scopes-element), dal più generale al più specifico. Ciò significa che le sostituzioni più specifiche avranno la maggiore probabilità di essere applicate. L'ordine specifico è il seguente:

1. Globale 
2. Subscription
3. Resource group
4. Una macchina virtuale. 

Se più sostituzioni con lo stesso livello di ambito definiscono la stessa proprietà nello stesso monitor, verranno applicate nell'ordine in cui sono visualizzate nel DCR. Se le sostituzioni si trovano in DCR diversi, vengono applicate in ordine alfabetico degli ID di risorsa DCR.


## <a name="data-collection-rule-configuration"></a>Configurazione della regola di raccolta dati
Le sezioni seguenti illustrano gli elementi JSON della regola di raccolta dati che definiscono le sostituzioni. Un esempio completo è disponibile nella [regola di raccolta dati di esempio](#sample-data-collection-rule).

## <a name="extensions-structure"></a>struttura Extensions
L'integrità Guest viene implementata come un'estensione dell'agente di monitoraggio di Azure, pertanto le sostituzioni sono definite nell' `extensions` elemento della regola di raccolta dati. 

```json
"extensions": [
    {
        "name": "Microsoft-VMInsights-Health",
        "streams": [
            "Microsoft-HealthStateChange"
        ],
        "extensionName": "HealthExtension",
        "extensionSettings": {   }
    }
]
```

| Elemento | Obbligatoria | Descrizione |
|:---|:---|:---|
| `name` | Sì | Stringa definita dall'utente per l'estensione. |
| `streams` | Sì | Elenco dei flussi a cui verranno inviati i dati di integrità Guest. Deve includere **Microsoft-HealthStateChange**.  |
| `extensionName` | Sì | Nome dell'estensione. Deve essere **HealthExtension**. |
| `extensionSettings` | Sì | Matrice di `healthRuleOverride` elementi da applicare alla configurazione predefinita. |


## <a name="extensionsettings-element"></a>elemento extensionSettings
Contiene le impostazioni per l'estensione.

```json
"extensionSettings": {
    "schemaVersion": "1.0",
    "contentVersion": "",
    "healthRuleOverrides": [ ]
}
```

| Elemento | Obbligatoria | Descrizione |
|:---|:---|:---|
| `schemaVersion` | Sì | Stringa definita da Microsoft per rappresentare lo schema previsto dell'elemento. Attualmente deve essere impostato su 1,0 |
| `contentVersion` | No | Stringa definita dall'utente per tenere traccia delle diverse versioni della configurazione di integrità, se necessario. |
| `healthRuleOverrides` | Sì | Matrice di `healthRuleOverride` elementi da applicare alla configurazione predefinita. |

## <a name="healthrulesoverrides-element"></a>elemento healthRulesOverrides
Contiene uno o più `healthRuleOverride` elementi che definiscono una sostituzione.

```json
"healthRuleOverrides": [
    {
        "scopes": [ ],
        "monitors": [ ],
        "monitorConfiguration": { },
        "alertConfiguration": {  },
        "isEnabled": true|false
    }
]
```

| Elemento | Obbligatoria | Descrizione |
|:---|:---|:---|
| `scopes` | Sì | Elenco di uno o più ambiti che specificano le macchine virtuali a cui è applicabile questa sostituzione. Anche se il DCR è associato a una macchina virtuale, la macchina virtuale deve rientrare in un ambito per applicare la sostituzione. |
| `monitors` | Sì | Elenco di una o più stringhe che definiscono i monitoraggi che riceveranno questa sostituzione.  |
| `monitorConfiguration` | No | Configurazione per il monitoraggio, inclusi gli Stati di integrità e il modo in cui vengono calcolate. |
| `alertConfiguration` | No | Configurazione di avviso per il monitoraggio. |
| `isEnabled` | No | Controlla se il monitoraggio è abilitato o meno. Il monitoraggio disabilitato passa allo stato di integrità speciale *disabilitato* e gli Stati sono disabilitati se non riabilitati. Se omesso, il monitoraggio erediterà lo stato da monitoraggio padre nella gerarchia. |


## <a name="scopes-element"></a>Scopes-elemento
Ogni override ha uno o più ambiti che definiscono a quali macchine virtuali deve essere applicata la sostituzione. L'ambito può essere una sottoscrizione, un gruppo di risorse o una singola macchina virtuale. Anche se l'override si trova in un DCR associato a una determinata macchina virtuale, viene applicato solo a tale macchina virtuale se la macchina virtuale si trova in uno degli ambiti della sostituzione. Questo consente di associare in modo estensivo un numero minore di DCR a un set di macchine virtuali, ma di fornire un controllo granulare sull'assegnazione di ogni sostituzione all'interno del DCR stesso. È possibile creare un set limitato di DCR e associarli a un set di macchine virtuali usando i criteri, specificando gli override di Health Monitor per diversi subset di tali macchine virtuali usando l'elemento Scopes.

Nella tabella seguente vengono illustrati esempi di ambiti diversi.

| Scope | Esempio |
|:---|:---|
| Macchina virtuale singola | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rg-name/providers/Microsoft.Compute/virutalMachines/my-vm` |
| Tutte le macchine virtuali in un gruppo di risorse | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rg-name` |
| Tutte le macchine virtuali in una sottoscrizione | `/subscriptions/00000000-0000-0000-0000-000000000000/` |
| Tutte le macchine virtuali a cui è associata la regola di raccolta dati | `*` |


## <a name="monitors-element"></a>elemento Monitors
Elenco di una o più stringhe che definiscono quali monitoraggi nella gerarchia di integrità riceveranno questo override. Ogni elemento può essere un nome di monitoraggio o un nome di tipo che corrisponde a uno o più monitoraggi che riceveranno questa sostituzione. 

```json
"monitors": [
    "<monitor name>"
 ],
```



Nella tabella seguente sono elencati i nomi di monitoraggio attualmente disponibili.

| Nome tipo | Nome | Descrizione |
|:---|:---|:---|
| root | root | Monitoraggio di primo livello che rappresenta l'integrità della macchina virtuale. | |
| utilizzo CPU | utilizzo CPU | Monitoraggio dell'utilizzo della CPU. | |
| dischi logici | dischi logici | Monitoraggio aggregato dello stato di integrità di tutti i dischi monitorati nella macchina virtuale Windows. | |
| dischi logici\|* | dischi logici \| C:<br>dischi logici \| D: | Monitoraggio aggregato dello stato di rilevamento di un determinato disco nella macchina virtuale Windows. | 
| \| * \| spazio libero su disco logico | dischi logici \| C: \| spazio libero<br>dischi logici \| D: \| spazio libero | Monitoraggio dello spazio libero su disco nella macchina virtuale Windows. |
| filesystems | filesystems | Monitoraggio aggregato dello stato di tutti i filesystem nella macchina virtuale Linux. |
| filesystem\|* | filesystem\|/<br>filesystems \| /mnt | Monitoraggio aggregato dello stato di monitoraggio di un file System di una macchina virtuale Linux. | filesystems|/var/log |
| \| * \| spazio disponibile per i filesystem | \| / \| spazio disponibile per i filesystem<br>\|/mnt \| di spazio disponibile per i filesystem | Monitoraggio dello spazio libero su disco nel file System della macchina virtuale Linux. | 
| memoria | memoria | Monitoraggio aggregato dello stato della memoria della macchina virtuale. | |
| memoria \| disponibile| memoria \| disponibile | Monitorare la memoria disponibile per il rilevamento della macchina virtuale. | |


## <a name="alertconfiguration-element"></a>elemento alertConfiguration
Specifica se è necessario creare un avviso dal monitoraggio.

```json
"alertConfiguration": {
    "isEnabled": true|false
}
```

| Elemento | Obbligatorio | Descrizione | 
|:---|:---|:---|
| `isEnabled` | No | Se impostato su true, il monitoraggio genera un avviso quando passa a uno stato critico o di avviso e risolve un avviso quando torna allo stato integro. Se false o omesso, non viene generato alcun avviso.  |


## <a name="monitorconfiguration-element"></a>elemento monitorConfiguration
Si applica solo ai monitoraggi unità. Definisce la configurazione per il monitoraggio, inclusi gli Stati di integrità e il modo in cui vengono calcolate.

I parametri definiscono l'algoritmo per calcolare il valore della metrica da confrontare con le soglie. Anziché agire su un campione di dati della metrica sottostante, il monitoraggio valuta diversi esempi di metriche ricevuti all'interno della finestra dalla fase di valutazione e da `lookbackSec` prima. Tutti gli esempi ricevuti in tale intervallo di tempo sono considerati e se il numero di campioni è maggiore di `maxSamples` , gli esempi meno recenti `maxSamples` vengono ignorati. 

Se sono presenti meno campioni nell'intervallo di lookback di `minSamples` , il monitoraggio passerà allo stato di integrità *sconosciuto* indicante che non sono disponibili dati sufficienti per prendere decisioni informate sull'integrità delle metriche sottostanti. Se è disponibile un numero maggiore di campioni `minSamples` , una funzione di aggregazione specificata dal parametro evaluationType viene eseguita sul set per calcolare un singolo valore.


```json
"monitorConfiguration": {
        "evaluationType" : "<type-of-evaluation>",
        "lookbackSecs": <lookback-number-of-seconds>,
        "evaluationFrequencySecs": <evaluation-frequency-number-of-seconds>,
        "minSamples": <minimum-samples>,
        "maxSamples": <maximum-samples>,
        "warningCondition": {  },
        "criticalCondition": {  }
    }
}
```

| Elemento | Obbligatorio | Descrizione | 
|:---|:---|:---|
| `evaluationFrequencySecs` | No | Definisce la frequenza per la valutazione dello stato di integrità. Ogni monitoraggio viene valutato nel momento in cui l'agente viene avviato e a intervalli regolari definito da questo parametro in seguito. |
| `lookbackSecs`   | No | Dimensioni della finestra lookback in secondi. |
| `evaluationType` | No | `min` : accetta il valore minimo dall'intero set di campioni<br>`max` -accetta il valore massimo dall'intero set di campioni<br>`avg` : accetta la media dei valori impostati degli esempi<br>`all` : consente di confrontare ogni singolo valore del set con le soglie. Il monitoraggio attiva lo stato solo se tutti gli esempi nel set soddisfano la condizione di soglia. |
| `minSamples`     | No | Numero minimo di valori da utilizzare per calcolare il valore. |
| `maxSamples`     | No | Numero massimo di valori da utilizzare per calcolare il valore. |
| `warningCondition`  | No | Soglia e logica di confronto per la condizione di avviso. |
| `criticalCondition` | No | Soglia e logica di confronto per la condizione critica. |


## <a name="warningcondition-element"></a>elemento warningCondition
Definisce la soglia e la logica di confronto per la condizione di avviso. Se questo elemento non è incluso, il monitoraggio non passerà mai allo stato di integrità di avviso.

```json
"warningCondition": {
    "isEnabled": true|false,
    "operator": "<comparison-operator>",
    "threshold": <threshold-value>
},
```

| Proprietà | Obbligatorio | Descrizione | 
|:---|:---|:---|
| `isEnabled` | No | Specifica se la condizione è abilitata. Se è impostato su **false**, la condizione è disabilitata anche se è possibile impostare le proprietà di soglia e operatore. |
| `threshold` | No | Definisce la soglia per confrontare il valore valutato. |
| `operator`  | No | Definisce l'operatore di confronto da utilizzare nell'espressione di soglia. Valori possibili: >, <, >=, <=, = =. |


## <a name="criticalcondition-element"></a>elemento criticalCondition
Definisce la soglia e la logica di confronto per la condizione critica. Se questo elemento non è incluso, il monitoraggio non passerà mai allo stato di integrità critico.

```json
"criticalCondition": {
    "isEnabled": true|false,
    "operator": "<comparison-operator>",
    "threshold": <threshold-value>
},
```

| Proprietà | Obbligatorio | Descrizione | 
|:---|:---|:---|
| `isEnabled` | No | Specifica se la condizione è abilitata. Se è impostato su **false**, la condizione è disabilitata anche se è possibile impostare le proprietà di soglia e operatore. |
| `threshold` | No | Definisce la soglia per confrontare il valore valutato. |
| `operator`  | No | Definisce l'operatore di confronto da utilizzare nell'espressione di soglia. Valori possibili: >, <, >=, <=, = =. |

## <a name="sample-data-collection-rule"></a>Regola di raccolta dati di esempio
Per una regola di raccolta dati di esempio che Abilita il monitoraggio Guest, vedere [abilitare una macchina virtuale con gestione risorse modello](vminsights-health-enable.md#enable-a-virtual-machine-using-resource-manager-template).


## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle [regole di raccolta dati](../platform/data-collection-rule-overview.md).