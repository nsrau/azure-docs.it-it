---
title: "Informazioni sulle impostazioni di scalabilità automatica | Documenti Microsoft"
description: "Una suddivisione dettagliata delle impostazioni di scalabilità automatica e il relativo funzionamento."
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
ms.openlocfilehash: cff2be1818417a19f36da08d8c2eaa227bb945ec
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2017
---
# <a name="understand-autoscale-settings"></a>Informazioni sulle impostazioni di scalabilità automatica
Impostazioni di scalabilità automatica consentono di avere a disposizione la giusta quantità di risorse per gestire il carico dinamico dell'applicazione in esecuzione. È possibile configurare le impostazioni di scalabilità automatica deve essere attivata in base alle metriche che indicano le prestazioni o carico, o il trigger in una data e ora pianificate. In questo articolo richiede un'analisi approfondita l'anatomia di un'impostazione di scalabilità automatica. L'articolo inizia con la comprensione dello schema e della proprietà di un'impostazione, quindi vengono illustrati i diversi tipi di profilo che possono essere configurati e infine viene illustrata la scalabilità automatica restituisce il profilo da eseguire in qualsiasi momento.

## <a name="autoscale-setting-schema"></a>Impostazione di scalabilità automatica schema
Per illustrare l'impostazione di scalabilità automatica schema, viene utilizzata la seguente impostazione di scalabilità automatica. È importante notare che questa impostazione di scalabilità automatica ha:
- Un profilo 
- Presenta due regole di metrica in questo profilo. uno per scalabilità orizzontale e uno per la scala.
- La regola di scalabilità orizzontale viene attivata quando metriche Percentuale CPU Media del set di scalabilità della macchina virtuale è maggiore di 85% per le ultime 10 minuti.
- La regola di scala viene attivata quando la media del set di scalabilità della macchina virtuale è inferiore al 60% nell'ultimo minuto.

> [!NOTE]
> Un'impostazione può avere più profili, passare il [profili](#autoscale-profiles) sezione per altre informazioni.
> Un profilo può inoltre avere scalabilità più regole e le regole di scala definite, passare il [sezione valutazione](#autoscale-evaluation) per vedere come vengono valutate

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
| Impostazione | ID | ID di risorsa. dell'impostazione di scalabilità automatica Impostazioni di scalabilità automatica sono una risorsa di gestione risorse di Azure. |
| Impostazione | name | Il nome dell'impostazione di scalabilità automatica. |
| Impostazione | location | La posizione dell'impostazione di scalabilità automatica. Questo percorso può essere diverso dal percorso della risorsa in corso il ridimensionamento. |
| properties | dell'oggetto targetResourceUri | L'ID risorsa della risorsa in corso il ridimensionamento. È possibile avere solo un'impostazione di scalabilità automatica per ogni risorsa. |
| properties | Profili | Un'impostazione di scalabilità automatica è costituita da uno o più profili. Ogni volta che viene eseguito il motore di scalabilità automatica, viene eseguito un profilo. |
| Profilo | name | Il nome del profilo, è possibile scegliere qualsiasi nome che consente di identificare il profilo. |
| Profilo | Capacity.Maximum | La capacità massima consentita. Assicura la scalabilità automatica, quando si esegue questo profilo, non è adatta la risorsa di sopra di questo numero. |
| Profilo | Capacity.Minimum | La capacità minima consentita. Assicura la scalabilità automatica, quando si esegue questo profilo, non è adatta la risorsa di sotto di questo numero. |
| Profilo | Capacity.default | Se è presente un problema durante la lettura la metrica di risorsa (in questo caso, la cpu del "vmss1") e la capacità corrente è di sotto della capacità predefinita, quindi per garantire la disponibilità della risorsa, scalabilità automatica scale-out per il valore predefinito. Se la capacità corrente è maggiore della capacità predefinita, scalabilità automatica verranno non scala-in. |
| Profilo | regole | Scalabilità automatica può essere ridimensionato automaticamente tra le capacità massime e minime utilizzando le regole del profilo. È possibile avere più regole in un profilo. Lo scenario di base è che le due regole, uno per determinare quando scalabilità orizzontale e l'altro per determinare quando in scala. |
| Regola | metricTrigger | Definisce la condizione della regola di metrica. |
| metricTrigger | metricName | Il nome della metrica. |
| metricTrigger |  metricResourceUri | L'ID risorsa della risorsa che genera la metrica. Nella maggior parte dei casi, è lo stesso come risorsa in corso il ridimensionamento. In alcuni casi, può essere diverso, ad esempio è possibile ridimensionare un set di scalabilità della macchina virtuale in base al numero di messaggi in una coda di archiviazione. |
| metricTrigger | timeGrain | La durata di campionamento di metrica. Ad esempio, TimeGrain = "PT1M" significa che le metriche devono essere aggregati di ogni minuto utilizzando il metodo di aggregazione specificato in "statistica". |
| metricTrigger | statistic | Il metodo di aggregazione entro il periodo di intervallo di tempo. Statistiche, ad esempio = "Medio" e l'intervallo di tempo = "PT1M" significa che devono essere le metriche aggregate intervalli di 1 minuto considerando la Media. Questa proprietà determina come viene campionata la metrica. |
| metricTrigger | timeWindow | La quantità di tempo per eseguire ricerche indietro per le metriche. Ad esempio, timeWindow = "PT10M" significa che ogni volta che viene eseguito scalabilità automatica, viene eseguita una query metriche negli ultimi 10 minuti. L'intervallo di tempo consente le metriche normalizzare ed evita di reazione a picchi temporanei. |
| metricTrigger | timeAggregation | Il metodo di aggregazione utilizzato per aggregare le metriche campionate. Ad esempio, di aggregazione temporale = "Medio" deve aggregare le metriche campionate considerando la Media. Nel caso precedente dieci campioni 1 minuto e la media di essi. |
| Regola | scaleAction | L'azione da intraprendere quando viene attivato metricTrigger della regola. |
| scaleAction | direction | "Aumentare" per scalabilità orizzontale, "Diminuire" in scala-in|
| scaleAction | value | La quantità per aumentare o diminuire la capacità della risorsa |
| scaleAction | cooldown | La quantità di tempo di attesa dopo un'operazione di scala prima di passare nuovamente. Ad esempio, se raffreddamento = "PT10M" dopo l'esecuzione di un'operazione di scala, scalabilità automatica non tenterà di scalare nuovamente per altri 10 minuti. Il raffreddamento consiste nel consentire le metriche stabilizzare dopo l'aggiunta o rimozione di istanze. |

## <a name="autoscale-profiles"></a>Profili di scalabilità automatica

Esistono tre tipi di profili di scalabilità automatica:

1. **Profilo regolare:** profilo più comune. Se non è necessario ridimensionare la risorsa in modo diverso in base il giorno della settimana o in un determinato giorno, quindi è solo necessario configurare un profilo regolari nell'impostazione di scalabilità automatica. Questo profilo può quindi essere configurato con metrica regole che determinano quando a scalabilità orizzontale e scalabilità-in. È sufficiente un profilo regolare definito.

    Il profilo di esempio usato in precedenza in questo articolo è riportato un esempio di un profilo regolare. Non che non è inoltre possibile impostare un profilo per la scala su un numero di istanze statica per la risorsa.

2. **Profilo data fissa:** con il profilo regolare definito, si supponga che si dispone di un evento importante presentarsi in 26 dicembre 2017 (PST) e si desidera il minimo o massimo le capacità della risorsa diversi nella stessa giornata, ma con una scala comunque le stesse metriche . In questo caso, è necessario aggiungere un profilo data fissa all'elenco di profili dell'impostazione. Il profilo è configurato per eseguire solo il giorno dell'evento. Per qualsiasi giorno, viene eseguito il profilo regolare.

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
    
3. **Profilo ricorrenza:** questo tipo di profilo consente di garantire che il profilo è sempre usato in un determinato giorno della settimana. Profili di ricorrenza hanno solo un'ora di inizio, di conseguenza vengono eseguiti fino al profilo ricorrenza o profilo data fissa è impostata su Avvia. Un'impostazione con un solo profilo ricorrenza, di scalabilità automatica esegue tale profilo anche se è presente un profilo regolare definito con la stessa impostazione. Due esempi seguenti illustrano l'utilizzo di questo profilo:

    **Esempio 1: vs giorno della settimana. I fine settimana** supponiamo che nei fine settimana si desidera che la capacità massima a 4 che nei giorni feriali, poiché si prevede un carico maggiore la capacità massima a 10. In questo caso, l'impostazione conterrebbe due profili di ricorrenza, uno per l'esecuzione nei fine settimana e l'altro nei giorni feriali.
    L'impostazione avrà un aspetto simile al seguente:

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

    Osservando l'impostazione precedente, si noterà che ogni profilo ricorrenza dispone di una pianificazione, la pianificazione determina quando il profilo viene avviata l'esecuzione. Il profilo viene interrotta l'esecuzione al momento di eseguire un altro profilo.

    Ad esempio, nell'impostazione del precedente, "weekdayProfile" viene impostato per avviarsi lunedì alle ore 12, che significa che il profilo avvia l'esecuzione dal lunedì al 12.am. Continua l'esecuzione fino a sabato 12a.m. quando è pianificato "weekendProfile" per avviare l'esecuzione.

    **Esempio 2: orario di ufficio** Ecco un altro esempio, è possibile disporre di soglia per le metriche = 'x' durante l'orario di ufficio, 9: 00 alle 17.00, quindi dal 17. alle 9 il giorno successivo, si desidera la soglia per le metriche da 'y'.
    L'impostazione avrà un aspetto simile al seguente:
    
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
    
    Osservando l'impostazione precedente, "businessHoursProfile" inizia l'esecuzione di lunedì alle 9. e mantiene l'esecuzione fino a 17. Poiché questa è la "nonBusinessHoursProfile" Avvia l'esecuzione. "nonBusinessHoursProfile" viene eseguita fino a 9: 00 Martedì e quindi assume "businessHoursProfile". Questo processo si ripete fino a quando venerdì 17: 00, a quel punto "nonBusinessHoursProfile" viene eseguita in lunedì 9: 00 Poiché "businessHoursProfile" non si avvia l'esecuzione fino a: lunedì 9: 00
    
> [!Note]
> L'esperienza utente di scalabilità automatica nel portale di Azure impone l'ora di fine per i profili di ricorrenza e inizia l'esecuzione di profilo predefinito dell'impostazione di scalabilità automatica tra i profili di ricorrenza.
    
## <a name="autoscale-evaluation"></a>Valutazione di scalabilità automatica
Dato che tale funzionalità impostazioni possono avere più profili di scalabilità automatica e ogni profilo può avere più regole di metrica, è importante comprendere la modalità di valutazione di un'impostazione di scalabilità automatica. Ogni volta che l'esecuzione del processo di scalabilità automatica inizia scegliendo il profilo che è applicabile, dopo aver scelto il profilo di scalabilità automatica valuta min, max valori e tutte le regole di metrica del profilo e decide se è necessaria un'azione di scalabilità.

### <a name="which-profile-will-autoscale-pick"></a>Il profilo selezionerà scalabilità automatica?
- Ridimensionamento automatico cerca innanzitutto qualsiasi fisso profilo data che è configurato per l'esecuzione di questo punto, se è presente, scalabilità automatica, che viene eseguito. Se sono presenti più profili data fissa che dovrebbero essere eseguite, scalabilità automatica selezionerà il primo.
- Se sono non presenti alcun profilo data fissa, scalabilità automatica esaminano i profili di ricorrenza, se trovato, viene eseguito.
- Se sono presenti n fissata o profili ricorrenza, scalabilità automatica, quindi eseguire il profilo regolare.

### <a name="how-does-autoscale-evaluate-multiple-rules"></a>Modalità di valutazione più regole scalabilità automatica

Una volta scalabilità automatica determina quale profilo dovrebbe per eseguire, avviare valutando tutte la regola di scalabilità orizzontale nel profilo (regole con direzione = "Aumentare").
- Se una o più regole di scalabilità orizzontale vengono attivate, scalabilità automatica consente di calcolare la nuova capacità di base di scaleAction di ognuno di tali regole. Quindi scale-out per il numero massimo di queste capacità per garantire la disponibilità del servizio.
- Ad esempio: se è presente un set di scalabilità della macchina virtuale con una capacità pari a 10 corrente e sono presenti due regole di scalabilità orizzontale. uno che aumenta la capacità di 10% e uno che aumenta la capacità di 3. La prima regola darà origine a una nuova capacità di 11 e la seconda regola avrebbe come risultato una capacità pari a 13. Per garantire la disponibilità del servizio scalabilità automatica sceglie l'azione che comporta la capacità massima, quindi la seconda regola viene scelto.

Se nessuna regola di scalabilità orizzontale viene attivata, scalabilità automatica valuta tutte le scale-in regole (regole con direzione = "Ridurre"). Scalabilità automatica ha solo un'azione di scalabilità se tutte le regole di scala vengono attivate.
- Scalabilità automatica consente di calcolare la nuova capacità di base di scaleAction di ognuno di tali regole. Quindi sceglie l'azione che restituisce il numero massimo di queste capacità per garantire la disponibilità del servizio.
- Ad esempio: se è presente un set di scalabilità della macchina virtuale con una capacità pari a 10 corrente e sono presenti regole di scalabilità due. uno che diminuisce la capacità di 50% e uno che diminuisce la capacità di 3. La prima regola darà origine a una nuova capacità di 5 e la seconda regola avrebbe come risultato una capacità pari a 7. Per garantire la disponibilità del servizio scalabilità automatica sceglie l'azione che comporta la capacità massima, quindi la seconda regola viene scelto.

## <a name="next-steps"></a>Fasi successive
Per ulteriori informazioni sulla scalabilità automatica vedere le risorse seguenti:

* [Panoramica della scalabilità automatica](monitoring-overview-autoscale.md)
* [Metriche comuni per la scalabilità automatica di Monitoraggio di Azure](insights-autoscale-common-metrics.md)
* [Procedure consigliate per la scalabilità automatica in Monitoraggio di Azure](insights-autoscale-best-practices.md)
* [Usare le azioni di ridimensionamento automatico per inviare notifiche di avviso di webhook e posta elettronica in Azure Insights](insights-autoscale-to-webhook-email.md)
* [API REST per il ridimensionamento automatico](https://msdn.microsoft.com/library/dn931953.aspx)
