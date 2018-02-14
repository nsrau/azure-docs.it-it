---
title: "Informazioni sulle impostazioni di scalabilità automatica in Azure | Microsoft Docs"
description: "Analisi dettagliata delle impostazioni di scalabilità automatica e del loro funzionamento."
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ce2930aa-fc41-4b81-b0cb-e7ea922467e1
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: ancav
ms.openlocfilehash: 73c79ec4ee1beb5220e088421c78ffffd932eef1
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2018
---
# <a name="understand-autoscale-settings"></a>Informazioni sulle impostazioni di scalabilità automatica
Le impostazioni di scalabilità automatica permettono di eseguire la giusta quantità di risorse per gestire il carico variabile dell'applicazione. È possibile configurare le impostazioni di scalabilità automatica in modo che vengano attivate in base alle metriche che indicano le prestazioni o il carico oppure in base a una data e un'ora pianificate. Questo articolo analizza in dettaglio l'anatomia di un'impostazione di scalabilità automatica. L'articolo inizia dallo schema e dalle proprietà di un'impostazione e quindi descrive i diversi tipi di profilo che è possibile configurare. L'articolo descrive infine in che modo la funzionalità di scalabilità automatica determina quale profilo eseguire in ogni momento specifico.

## <a name="autoscale-setting-schema"></a>Schema delle impostazioni di scalabilità automatica
Per illustrare lo schema delle impostazioni di scalabilità automatica, viene usata l'impostazione seguente. È importante notare che questa impostazione di scalabilità automatica include gli elementi seguenti:
- Un profilo. 
- Due regole metriche nel profilo, una per l'aumento e una per la riduzione della capacità.
  - La regola di aumento viene attivata quando la metrica relativa alla percentuale di CPU media del set di scalabilità di macchine virtuali è maggiore dell'85% per gli ultimi 10 minuti.
  - La regola di riduzione viene attivata quando la media del set di scalabilità di macchine virtuali è minore del 60% per l'ultimo minuto.

> [!NOTE]
> Un'impostazione può includere diversi profili. Per altre informazioni, vedere la sezione dei [profili](#autoscale-profiles). Per un profilo possono essere definite più regole di aumento e di riduzione. Per esaminare in che modo vengono valutate queste regole, vedere la sezione relativa alla [valutazione](#autoscale-evaluation).

```JSON
{
  "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/setting1",
  "name": "setting1",
  "type": "Microsoft.Insights/autoscaleSettings",
  "location": "East US",
  "properties": {
    "enabled": true,
    "targetResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
    "profiles": [
      {
        "name": "mainProfile",
        "capacity": {
          "minimum": "1",
          "maximum": "4",
          "default": "1"
        },
        "rules": [
          {
            "metricTrigger": {
              "metricName": "Percentage CPU",
              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT10M",
              "timeAggregation": "Average",
              "operator": "GreaterThan",
              "threshold": 85
            },
            "scaleAction": {
              "direction": "Increase",
              "type": "ChangeCount",
              "value": "1",
              "cooldown": "PT5M"
            }
          },
    {
            "metricTrigger": {
              "metricName": "Percentage CPU",
              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT10M",
              "timeAggregation": "Average",
              "operator": "LessThan",
              "threshold": 60
            },
            "scaleAction": {
              "direction": "Decrease",
              "type": "ChangeCount",
              "value": "1",
              "cooldown": "PT5M"
            }
          }
        ]
      }
    ]
  }
}
```

| Sezione | Nome dell'elemento | DESCRIZIONE |
| --- | --- | --- |
| Impostazione | ID | ID risorsa dell'impostazione di scalabilità automatica. Le impostazioni di scalabilità automatica sono una risorsa di Azure Resource Manager. |
| Impostazione | name | Nome dell'impostazione di scalabilità automatica. |
| Impostazione | location | Posizione dell'impostazione di scalabilità automatica. Questa posizione può essere diversa dalla posizione della risorsa da ridimensionare. |
| properties | targetResourceUri | ID della risorsa da ridimensionare. È consentita una sola impostazione di scalabilità automatica per risorsa. |
| properties | Profili | Un'impostazione di scalabilità automatica è costituita da uno o più profili. Ogni volta che viene eseguito, il motore di scalabilità automatica esegue un profilo. |
| Profilo | name | Nome del profilo. È possibile scegliere qualsiasi nome che semplifichi l'identificazione del profilo. |
| Profilo | Capacity.maximum | Capacità massima consentita. Assicura che la scalabilità automatica, quando si esegue questo profilo, non ridimensioni la risorsa al di sopra di questo numero. |
| Profilo | Capacity.minimum | Capacità minima consentita. Assicura che la scalabilità automatica, quando si esegue questo profilo, non ridimensioni la risorsa al di sotto di questo numero. |
| Profilo | Capacity.default | Se si verifica un problema relativo alla metrica delle risorse (in questo caso, la CPU di "vmss1") e la capacità corrente è inferiore a quella predefinita, la funzionalità di scalabilità automatica aumenta la capacità fino all'impostazione predefinita. Questo avviene per garantire la disponibilità della risorsa. Se la capacità corrente è già superiore alla capacità predefinita, la funzionalità di scalabilità automatica non riduce la capacità. |
| Profilo | regole | La funzionalità di scalabilità automatica esegue automaticamente il ridimensionamento tra le capacità massima e minima usando le regole del profilo. È possibile avere più regole in un profilo. In genere esistono due regole, una per determinare quando aumentare la capacità e l'altra per determinare quando ridurla. |
| Regola | metricTrigger | Definisce la condizione metrica della regola. |
| metricTrigger | metricName | Nome della metrica. |
| metricTrigger |  metricResourceUri | ID della risorsa che emette la metrica. Nella maggior parte dei casi, è lo stesso della risorsa da ridimensionare. In alcuni casi, può essere diversa. Ad esempio, è possibile ridimensionare un set di scalabilità di macchine virtuali in base al numero di messaggi in una coda di archiviazione. |
| metricTrigger | timeGrain | La durata del campionamento delle metriche. Ad esempio, **TimeGrain = "PT1M"** indica che le metriche devono essere aggregate ogni minuto usando il metodo di aggregazione specificato nell'elemento statistic. |
| metricTrigger | statistic | Metodo di aggregazione nel periodo specificato per timeGrain. Ad esempio, **statistic = "Average"** e **timeGrain = "PT1M"** significano che le metriche devono essere aggregate ogni minuto, in base alla media. Questa proprietà determina come viene campionata la metrica. |
| metricTrigger | timeWindow | Quantità di tempo da considerare per le metriche. Ad esempio, **timeWindow = "PT10M"** significa che ogni volta che viene eseguita la funzionalità di scalabilità automatica, viene eseguita una query sulle metriche per i 10 minuti precedenti. L'intervallo di tempo permette la normalizzazione delle metriche per evitare di reagire a picchi temporanei. |
| metricTrigger | timeAggregation | Metodo di aggregazione usato per aggregare le metriche campionate. Ad esempio, **TimeAggregation = "Average"** deve aggregare le metriche campionate in base alla media. Nel caso precedente considerare i dieci esempi da 1 minuto e calcolarne la media. |
| Regola | scaleAction | Azione da intraprendere quando viene attivato l'elemento metricTrigger della regola. |
| scaleAction | direction | "Increase" per aumentare, "Decrease" per ridurre.|
| scaleAction | value | Indica di quanto aumentare o ridurre la capacità della risorsa. |
| scaleAction | cooldown | Tempo di attesa necessario dopo un'operazione di ridimensionamento prima di avviarne un'altra. Se, ad esempio, **cooldown = "PT10M"**, la funzionalità di ridimensionamento automatico non tenta un nuovo ridimensionamento per altri dieci minuti. Il raffreddamento consente alle metriche di stabilizzarsi dopo l'aggiunta o la rimozione di istanze. |

## <a name="autoscale-profiles"></a>Profili di scalabilità automatica

Esistono tre tipi di profili di scalabilità automatica:

- **Profilo normale**: è il profilo più comune. Se non è necessario ridimensionare la risorsa in modo diverso in base al giorno della settimana oppure in un determinato giorno, è possibile usare un profilo normale. Questo profilo può quindi essere configurato con le regole metriche che determinano quando aumentare la capacità e quando ridurla. È consigliabile avere un solo profilo normale definito.

    Il profilo di esempio usato in precedenza in questo articolo è un esempio di profilo normale. Si noti che è anche possibile impostare un profilo per eseguire il ridimensionamento in base a un numero di istanze statico per la risorsa.

- **Profilo Data fissa**: profilo per casi speciali. Si supponga, ad esempio, un evento importante previsto per il 26 dicembre 2017 (PST). Le capacità minima e massima della risorse dovranno essere diverse in questo giorno, ma la scalabilità dovrà essere basata sulle stesse metriche. In questo caso, è consigliabile aggiungere un profilo Data fissa all'elenco di profili dell'impostazione. Il profilo è configurato per essere eseguito solo il giorno dell'evento. In qualsiasi altro giorno la scalabilità automatica userà il profilo normale.

    ``` JSON
    "profiles": [{
    "name": " regularProfile",
    "capacity": {
    ...
    },
    "rules": [{
    ...
    },
    {
    ...
    }]
    },
    {
    "name": "eventProfile",
    "capacity": {
    ...
    },
    "rules": [{
    ...
    }, {
    ...
    }],
    "fixedDate": {
        "timeZone": "Pacific Standard Time",
               "start": "2017-12-26T00:00:00",
               "end": "2017-12-26T23:59:00"
    }}
    ]
    ```
    
- **Profilo Ricorrenza:** questo tipo di profilo consente di assicurarsi che in un determinato giorno della settimana venga usato sempre questo profilo. I profili Ricorrenza hanno solo un'ora di inizio. Vengono eseguiti finché il successivo profilo Ricorrenza o Data fissa non viene impostato per l'avvio. Un'impostazione di scalabilità automatica con un solo profilo Ricorrenza esegue questo profilo, anche se nella stessa impostazione è definito un profilo normale. I due esempi seguenti mostrano l'uso di questo profilo:

    **Esempio 1: giorni feriali e fine settimana**
    
    Si supponga di voler impostare su 4 la capacità massima nei fine settimana. Nei giorni feriali, poiché si prevede un carico maggiore, la capacità massima dovrà essere impostata su 10. In questo caso, l'impostazione conterrà due profili Ricorrenza, uno da eseguire nei fine settimana e l'altro nei giorni feriali.
    L'impostazione è simile alla seguente:

    ``` JSON
    "profiles": [
    {
    "name": "weekdayProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }],
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday"
            ],
            "hours": [
                0
            ],
            "minutes": [
                0
            ]
        }
    }}
    },
    {
    "name": "weekendProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }]
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Saturday"
            ],
            "hours": [
                0
            ],
            "minutes": [
                0
            ]
        }
    }
    }]
    ```

    L'impostazione precedente mostra che ogni profilo Ricorrenza ha una pianificazione. Questa pianificazione determina quando deve essere avviata l'esecuzione del profilo. L'esecuzione del profilo si arresta al momento di eseguire un altro profilo.

    Nell'impostazione precedente, ad esempio, l'avvio di "weekdayProfile" è impostato per lunedì a mezzanotte. Questo significa che l'esecuzione del profilo inizia lunedì a mezzanotte. L'esecuzione prosegue fino a sabato a mezzanotte, quando è pianificato l'avvio dell'esecuzione di "weekendProfile".

    **Esempio 2: ore lavorative**
    
    Si supponga di voler definire una soglia per le metriche durante le ore lavorative (dalle 9.00 alle 17.00) e una diversa per tutte le altre ore. L'impostazione sarà simile alla seguente:
    
    ``` JSON
    "profiles": [
    {
    "name": "businessHoursProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }],
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday", “Tuesday”, “Wednesday”, “Thursday”, “Friday”
            ],
            "hours": [
                9
            ],
            "minutes": [
                0
            ]
        }
    }
    },
    {
    "name": "nonBusinessHoursProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }]
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday", “Tuesday”, “Wednesday”, “Thursday”, “Friday”
            ],
            "hours": [
                17
            ],
            "minutes": [
                0
            ]
        }
    }
    }]
    ```
    
    L'impostazione precedente mostra che l'esecuzione di "businessHoursProfile" inizia lunedì alle 9.00 e continua fino alle 17.00. A questo punto, viene avviata l'esecuzione di "nonBusinessHoursProfile". Il profilo "nonBusinessHoursProfile" viene eseguito fino a martedì alle 9.00, ora in cui subentra il profilo "businessHoursProfile". Questa successione si ripete fino a venerdì alle 17.00. A questo punto, viene eseguito ininterrottamente il profilo "nonBusinessHoursProfile" fino a lunedì alle 9.00.
    
> [!Note]
> L'interfaccia utente per la scalabilità automatica nel portale di Azure applica le ore di fine per i profili Ricorrenza e avvia l'esecuzione del profilo predefinito dell'impostazione di scalabilità automatica tra i profili Ricorrenza.
    
## <a name="autoscale-evaluation"></a>Valutazione della scalabilità automatica
Considerando che le impostazioni di scalabilità automatica possono includere più profili e che ogni profilo può includere più regole metriche, è importante identificare come viene valutata un'impostazione di scalabilità automatica. Ogni volta che viene eseguito il processo di scalabilità automatica, la funzionalità inizia scegliendo il profilo applicabile. La funzionalità di scalabilità automatica valuta quindi i valori minimo e massimo e tutte le regole metriche nel profilo e decide se è necessaria un'azione di scalabilità.

### <a name="which-profile-will-autoscale-pick"></a>Profilo selezionato dalla scalabilità automatica

La funzionalità di scalabilità automatica usa la sequenza seguente per selezionare il profilo:
1. Prima di tutto, identifica se è configurato un profilo Data fissa per l'esecuzione a questo punto. Se è configurato un profilo Data fissa, lo esegue. Se sono presenti più profili Data fissa che devono essere eseguiti, la funzionalità di scalabilità automatica seleziona il primo.
2. Se non sono presenti profili Data fissa, la funzionalità di scalabilità automatica verifica la presenza di profili Ricorrenza. Se trova un profilo Ricorrenza, lo esegue.
3. Se non sono presenti profili Data fissa o Ricorrenza, la funzionalità di scalabilità automatica esegue il profilo normale.

### <a name="how-does-autoscale-evaluate-multiple-rules"></a>Valutazione di più regole con la scalabilità automatica

Dopo che la funzionalità di scalabilità automatica ha determinato il profilo da eseguire, avvia la valutazione di tutte le regole di aumento della capacità nel profilo. Si tratta delle regole con **direction = "Increase"**.

Se vengono attivate una o più regole di aumento, la funzionalità di scalabilità automatica calcola la nuova capacità in base all'elemento **scaleAction** di ogni regola. La funzionalità aumenta quindi la capacità al valore massimo, per garantire la disponibilità del servizio.

Ad esempio, si supponga che vi sia un set di scalabilità di macchine virtuali con capacità corrente pari a 10. Esistono due regole di aumento: una che aumenta la capacità del 10% e una che la aumenta di 3 conteggi. la prima regola restituirà una nuova capacità pari a 11 e la seconda regola restituirà una capacità pari a 13. Per garantire la disponibilità del servizio, la funzionalità di scalabilità automatica sceglie l'azione che restituisce la capacità massima, ovvero la seconda regola.

Se non viene attivata alcuna regola di aumento, la funzionalità di scalabilità automatica valuta tutte le regole di riduzione, ovvero le regole con **direction = "Decrease"**. La funzionalità di scalabilità automatica esegue un'azione di scale-in se tutte le regole di scale-in vengono attivate.

La funzionalità di scalabilità automatica calcola la nuova capacità in base all'elemento **scaleAction** di ogni regola, quindi sceglie l'azione di ridimensionamento che restituisce il massimo di tali capacità per garantire la disponibilità del servizio.

Ad esempio, si supponga che vi sia un set di scalabilità di macchine virtuali con capacità corrente pari a 10. Esistono due regole di riduzione: una che diminuisce la capacità del 50% e una che la riduce di 3 conteggi. la prima regola restituirà una nuova capacità pari a 5 e la seconda regola restituirà una capacità pari a 7. Per garantire la disponibilità del servizio, la funzionalità di scalabilità automatica sceglie l'azione che restituisce la capacità massima, ovvero la seconda regola.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla scalabilità automatica, vedere le risorse seguenti:

* [Panoramica della scalabilità automatica](monitoring-overview-autoscale.md)
* [Metriche comuni per la scalabilità automatica di Monitoraggio di Azure](insights-autoscale-common-metrics.md)
* [Procedure consigliate per la scalabilità automatica in Monitoraggio di Azure](insights-autoscale-best-practices.md)
* [Usare le azioni di ridimensionamento automatico per inviare notifiche di avviso di webhook e posta elettronica in Azure Insights](insights-autoscale-to-webhook-email.md)
* [API REST per il ridimensionamento automatico](https://msdn.microsoft.com/library/dn931953.aspx)
