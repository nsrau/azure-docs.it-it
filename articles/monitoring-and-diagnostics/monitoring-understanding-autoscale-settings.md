---
title: "Informazioni sulle impostazioni di scalabilità automatica | Microsoft Docs"
description: "Analisi dettagliata delle impostazioni di scalabilità automatica e del relativo funzionamento."
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
ms.openlocfilehash: 79602cf053d834bf3d6dc6b4d5568637b179d5c7
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2018
---
# <a name="understand-autoscale-settings"></a>Informazioni sulle impostazioni di scalabilità automatica
Le impostazioni di scalabilità automatica consentono di avere la giusta quantità di risorse in esecuzione per gestire il carico variabile dell'applicazione. È possibile configurare le impostazioni di scalabilità automatica in modo che vengano attivate in base alle metriche che indicano le prestazioni o il carico oppure in una data e ora pianificate. Questo articolo analizza in dettaglio un'impostazione di scalabilità automatica. L'articolo inizia descrivendo lo schema e le proprietà di un'impostazione, quindi illustra i diversi tipi di profili che possono essere configurati e infine spiega come la scalabilità automatica valuta quale profilo eseguire in un determinato momento.

## <a name="autoscale-setting-schema"></a>Schema delle impostazioni di scalabilità automatica
Per illustrare lo schema delle impostazione di scalabilità automatica, viene usata l'impostazione di scalabilità automatica seguente. È importante notare che questa impostazione di scalabilità automatica ha:
- Un profilo 
- Due regole metriche in questo profilo, una per lo scale-out e una per lo scale-in.
- La regola di scale-out viene attivata quando la metrica relativa alla percentuale CPU media del set di scalabilità di macchine virtuali è superiore all'85% per gli ultimi 10 minuti.
- La regola di scale-in viene attivata quando la media del set di scalabilità di macchine virtuali è inferiore al 60% per l'ultimo minuto.

> [!NOTE]
> Un'impostazione può avere più profili. Per altre informazioni, vedere la sezione sui [profili](#autoscale-profiles).
> Per un profilo possono anche essere definite più regole di scale-out e di scale-in. Per informazioni su come vengono valutate, vedere la [sezione sulla valutazione](#autoscale-evaluation)

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
| Profilo | name | Nome del profilo. È possibile scegliere qualsiasi nome consenta di identificare il profilo. |
| Profilo | Capacity.maximum | Capacità massima consentita. Assicura che la scalabilità automatica, quando si esegue questo profilo, non ridimensioni la risorsa al di sopra di questo numero. |
| Profilo | Capacity.minimum | Capacità minima consentita. Assicura che la scalabilità automatica, quando si esegue questo profilo, non ridimensioni la risorsa al di sotto di questo numero. |
| Profilo | Capacity.default | Se si verifica un problema relativo alla metrica delle risorse (in questo caso, la CPU "vmss1") e la capacità corrente è inferiore alla capacità predefinita, per assicurare la disponibilità della risorsa, la funzionalità di scalabilità automatica aumenta il numero di istanze fino all'impostazione predefinita. Se la capacità corrente è già superiore alla capacità predefinita, la funzionalità di scalabilità automatica non ridurrà il numero di istanze. |
| Profilo | regole | La scalabilità automatica esegue automaticamente il ridimensionamento tra le capacità massime e minime usando le regole del profilo. È possibile avere più regole in un profilo. Lo scenario di base prevede due regole, una per determinare quando aumentare il numero di istanze e l'altra per determinare quando ridurle. |
| Regola | metricTrigger | Definisce la condizione metrica della regola. |
| metricTrigger | metricName | Nome della metrica. |
| metricTrigger |  metricResourceUri | ID della risorsa che emette la metrica. Nella maggior parte dei casi, è lo stesso della risorsa da ridimensionare. In alcuni casi, può essere diverso. È ad esempio possibile ridimensionare un set di scalabilità di macchine virtuali in base al numero di messaggi in una coda di archiviazione. |
| metricTrigger | timeGrain | La durata del campionamento delle metriche. Ad esempio, TimeGrain = "PT1M" indica che le metriche verranno aggregate ogni minuto usando il metodo di aggregazione specificato in "statistic". |
| metricTrigger | statistic | Metodo di aggregazione nel periodo specificato per timeGrain. Ad esempio, statistic = "Average" e timeGrain = "PT1M" indicano che le metriche verranno aggregate ogni minuto in base alla media. Questa proprietà determina come viene campionata la metrica. |
| metricTrigger | timeWindow | Quantità di tempo da considerare per le metriche. Ad esempio, se timeWindow = "PT10M", ogni volta che viene eseguita la funzionalità Scalabilità automatica, viene eseguita una query sulle metriche per i 10 minuti precedenti. L'intervallo di tempo consente alle metriche di essere normalizzate e di non reagire a picchi temporanei. |
| metricTrigger | timeAggregation | Metodo di aggregazione usato per aggregare le metriche campionate. Ad esempio, TimeAggregation = "Average" aggregherà le metriche campionate in base alla media. Nel caso precedente considerare i dieci esempi da 1 minuto e calcolarne la media. |
| Regola | scaleAction | Azione da intraprendere quando viene attivato l'elemento metricTrigger della regola. |
| scaleAction | direction | "Increase" per aumentare il numero di istanze, "Decrease" per ridurle|
| scaleAction | value | Indica di quanto aumentare o ridurre la capacità della risorsa |
| scaleAction | cooldown | Tempo di attesa necessario dopo un'operazione di ridimensionamento prima di avviarne un'altra. Se ad esempio cooldown = "PT10M" e viene eseguita un'operazione di ridimensionamento, non verrà eseguito un altro tentativo di ridimensionamento prima di 10 minuti. Il raffreddamento consente alle metriche di stabilizzarsi dopo l'aggiunta o la rimozione di istanze. |

## <a name="autoscale-profiles"></a>Profili di scalabilità automatica

Esistono tre tipi di profili di scalabilità automatica:

1. **Profilo normale:** è quello più comune. Se non è necessario ridimensionare la risorsa in modo diverso in base al giorno della settimana oppure in un determinato giorno, è sufficiente configurare un profilo normale nell'impostazione di scalabilità automatica. Questo profilo può quindi essere configurato con le regole metriche che determinano quando aumentare il numero di istanze e quando ridurle. È consigliabile avere un solo profilo normale definito.

    Il profilo di esempio usato in precedenza in questo articolo è un esempio di profilo normale. Si noti che è anche possibile impostare un profilo per eseguire il ridimensionamento in base a un numero di istanze statico per la risorsa.

2. **Profilo Data fissa:** si supponga che, con il profilo normale definito, sia previsto un importante evento il 26 dicembre 2017 (PST) e di voler modificare le capacità minima/massima della risorsa per quel giorno, ma di voler continuare a eseguire il ridimensionamento in base alle stesse metriche. In questo caso, è consigliabile aggiungere un profilo Data fissa all'elenco di profili dell'impostazione. Il profilo è configurato per essere eseguito solo il giorno dell'evento. In qualsiasi altro giorno viene eseguito il profilo normale.

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
    
3. **Profilo Ricorrenza:** questo tipo di profilo consente di assicurarsi che in un determinato giorno della settimana venga usato sempre questo profilo. I profili Ricorrenza hanno solo un'ora di inizio, quindi vengono eseguiti finché il successivo profilo Ricorrenza o profilo Data fissa non viene impostato per l'avvio. Un'impostazione di scalabilità automatica con un solo profilo Ricorrenza esegue tale profilo anche se nella stessa impostazione è definito un profilo normale. I due esempi seguenti illustrano l'utilizzo di questo profilo:

    **Esempio 1: Giorno feriale e fine settimana** Si supponga che nei fine settimana la capacità massima debba essere impostata su 4, ma che nei giorni feriali, poiché è previsto un carico maggiore, la capacità massima debba essere pari a 10. In questo caso, l'impostazione conterrà due profili Ricorrenza, uno da eseguire nei fine settimana e l'altro nei giorni feriali.
    L'impostazione sarà simile alla seguente:

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

    Osservando l'impostazione precedente, si noterà che ogni profilo Ricorrenza ha una pianificazione che determina quando viene avviata l'esecuzione del profilo. L'esecuzione del profilo si arresta al momento di eseguire un altro profilo.

    Nell'impostazione precedente, ad esempio, l'avvio di "weekdayProfile" è impostato su lunedì a mezzanotte, vale a dire che l'esecuzione di questo profilo inizia lunedì a mezzanotte. L'esecuzione prosegue fino a sabato a mezzanotte, quando è pianificato l'avvio dell'esecuzione di "weekendProfile".

    **Esempio 2: Orario di ufficio** È anche possibile, ad esempio, che la soglia della metrica debba essere "x" durante l'orario di ufficio dalle 9 alle 17 e che dalle 17 alle 9 del giorno successivo la soglia della metrica debba essere "y".
    L'impostazione sarà simile alla seguente:
    
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
    
    Esaminando l'impostazione precedente, si nota che l'esecuzione di "businessHoursProfile" inizia lunedì alle 9 e prosegue fino alle 17 perché in quel momento viene avviata l'esecuzione di "nonBusinessHoursProfile". "nonBusinessHoursProfile" viene eseguito fino alle 9 di martedì e quindi subentra "businessHoursProfile". Questo ciclo si ripete fino a venerdì alle 17, quando "nonBusinessHoursProfile" viene eseguito ininterrottamente fino a lunedì alle 9 perché l'esecuzione di "businessHoursProfile" non viene avviata fino a lunedì alle 9.
    
> [!Note]
> L'esperienza utente di scalabilità automatica nel portale di Azure applica le ore di fine per i profili Ricorrenza e inizia l'esecuzione del profilo predefinito dell'impostazione di scalabilità automatica tra i profili Ricorrenza.
    
## <a name="autoscale-evaluation"></a>Valutazione della scalabilità automatica
Considerato che le impostazioni di scalabilità automatica possono avere più profili di scalabilità automatica e ogni profilo può avere più regole relative alle metriche, è importante sapere come viene valutata un'impostazione di scalabilità automatica. Ogni volta che il processo di scalabilità automatica viene eseguito, inizia scegliendo il profilo applicabile. Dopo avere scelto il profilo, la funzionalità di scalabilità automatica valuta i valori minimi e massimi e le regole delle metriche nel profilo e determina se è necessaria un'azione di ridimensionamento.

### <a name="which-profile-will-autoscale-pick"></a>Profilo selezionato dalla scalabilità automatica
- La funzionalità di scalabilità automatica prima cerca un profilo Data fissa configurato da eseguire subito e, se presente, lo esegue. Se sono presenti più profili Data fissa che devono essere eseguiti, la funzionalità di scalabilità automatica selezionerà il primo.
- Se non sono presenti profili Data fissa, la funzionalità di scalabilità automatica esamina i profili Ricorrenza, se disponibili, quindi ne esegue uno.
- Se non sono presenti profili Data fissa o Ricorrenza, la funzionalità di scalabilità automatica esegue il profilo normale.

### <a name="how-does-autoscale-evaluate-multiple-rules"></a>Valutazione di più regole con la scalabilità automatica

Dopo che la funzionalità di scalabilità automatica ha determinato quale profilo deve essere eseguito, avvia la valutazione di tutte le regole di scale-out del profilo (regole con direction = "Increase").
- Se una o più regole di scale-out vengono attivate, la funzionalità di scalabilità automatica calcola la nuova capacità determinata dall'elemento scaleAction di ogni regola. Aumenta quindi le istanze fino al numero massimo consentito da tali capacità per garantire la disponibilità del servizio.
- Ad esempio: se è presente un set di scalabilità di macchine virtuali con una capacità corrente pari a 10 e sono presenti due regole di scale-out, una che aumenta la capacità del 10% e una che aumenta la capacità di 3, la prima regola restituirà una nuova capacità pari a 11 e la seconda regola restituirà una capacità pari a 13. Per garantire la disponibilità del servizio, la funzionalità di scalabilità automatica sceglie l'azione che restituisce la capacità massima, ovvero la seconda regola.

Se non vengono attivate regole di scale-out, la funzionalità di scalabilità automatica valuta tutte le regole di scale-in (regole con direction = "Decrease"). La funzionalità di scalabilità automatica esegue un'azione di scale-in se tutte le regole di scale-in vengono attivate.
- La funzionalità di scalabilità automatica calcola la nuova capacità determinata dall'elemento scaleAction di ogni regola, quindi sceglie l'azione di ridimensionamento che restituisce il massimo di tali capacità per garantire la disponibilità del servizio.
- Ad esempio: se è presente un set di scalabilità di macchine virtuali con una capacità corrente pari a 10 e sono presenti due regole di scale-in, una che riduce la capacità del 50% e una che riduce la capacità di 3, la prima regola restituirà una nuova capacità pari a 5 e la seconda regola restituirà una capacità pari a 7. Per garantire la disponibilità del servizio, la funzionalità di scalabilità automatica sceglie l'azione che restituisce la capacità massima, ovvero la seconda regola.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla scalabilità automatica, vedere le risorse seguenti:

* [Panoramica della scalabilità automatica](monitoring-overview-autoscale.md)
* [Metriche comuni per la scalabilità automatica di Monitoraggio di Azure](insights-autoscale-common-metrics.md)
* [Procedure consigliate per la scalabilità automatica in Monitoraggio di Azure](insights-autoscale-best-practices.md)
* [Usare le azioni di ridimensionamento automatico per inviare notifiche di avviso di webhook e posta elettronica in Azure Insights](insights-autoscale-to-webhook-email.md)
* [API REST per il ridimensionamento automatico](https://msdn.microsoft.com/library/dn931953.aspx)
