---
title: Esecuzione e trigger di pipeline in Azure Data Factory | Microsoft Docs
description: "Questo articolo contiene informazioni sulla modalità di esecuzione di una pipeline in Azure Data Factory su richiesta o mediante la creazione di un trigger."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/03/2018
ms.author: shlo
ms.openlocfilehash: fc34cfbab796c6e1e4cd25ce13dcc63c39c6699d
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2018
---
# <a name="pipeline-execution-and-triggers-in-azure-data-factory"></a>Esecuzione e trigger di pipeline in Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of the Data Factory service that you're using:"]
> * [Versione 1 - Disponibilità generale](v1/data-factory-scheduling-and-execution.md)
> * [Versione 2 - Anteprima](concepts-pipeline-execution-triggers.md)

_Esecuzione di pipeline_ in Azure Data Factory versione 2 definisce un'istanza dell'esecuzione di una pipeline. Se una pipeline viene eseguita alle ore 8:00, alle 9:00 e alle 10:00, ad esempio, si hanno tre esecuzioni di pipeline separate. Ogni esecuzione di pipeline dispone di un ID di esecuzione della pipeline univoco, ovvero un GUID che definisce in modo univoco questa esecuzione di pipeline specifica. Le istanze delle esecuzioni di pipeline vengono in genere create passando argomenti ai parametri definiti nelle pipeline. Esistono due modi per eseguire una pipeline: manualmente o tramite un _trigger_. Questo articolo fornisce informazioni dettagliate su entrambe le modalità di esecuzione di una pipeline.

> [!NOTE]
> Questo articolo è applicabile ad Azure Data Factory versione 2, attualmente in versione di anteprima. Se si usa Azure Data Factory versione 1, disponibile a livello generale, vedere [Pianificazione ed esecuzione in Azure Data Factory versione 1](v1/data-factory-scheduling-and-execution.md).

## <a name="manual-execution-on-demand"></a>Esecuzione manuale (su richiesta)
L'esecuzione manuale di una pipeline è detta anche esecuzione _su richiesta_.

Si supponga ad esempio di avere una pipeline semplice denominata **copyPipeline** che si vuole eseguire. La pipeline ha una singola attività che esegue una copia da una cartella di origine in Archiviazione BLOB di Azure a una cartella di destinazione nella stessa risorsa di archiviazione. La definizione JSON seguente illustra questa pipeline di esempio:

```json
{
  "name": "copyPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "name": "CopyBlobtoBlob",
        "inputs": [
          {
            "referenceName": "sourceBlobDataset",
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sinkBlobDataset",
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      }
    }
  }
}
```

Nella definizione JSON la pipeline accetta due parametri: **sourceBlobContainer** e **sinkBlobContainer**. Passare valori a questi parametri in fase di esecuzione.

È possibile eseguire manualmente la pipeline usando i metodi seguenti:
- .NET SDK.
- Modulo di Azure PowerShell.
- API REST.
- Python SDK.

### <a name="the-rest-api"></a>API REST
Il comando di esempio seguente illustra come eseguire manualmente la pipeline usando l'API REST:  

```
POST
https://management.azure.com/subscriptions/mySubId/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory/pipelines/copyPipeline/createRun?api-version=2017-03-01-preview
```

Per un esempio completo, vedere [Avvio rapido: Creare una data factory usando l'API REST](quickstart-create-data-factory-rest-api.md).

### <a name="azure-powershell"></a>Azure PowerShell
Il comando di esempio seguente illustra come eseguire manualmente la pipeline usando Azure PowerShell:

```powershell
Invoke-AzureRmDataFactoryV2Pipeline -DataFactory $df -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
```

I parametri vengono passati nel corpo nel payload della richiesta. In .NET SDK, Azure PowerShell e Python SDK passare i valori in un dizionario passato come argomento alla chiamata:

```json
{
  “sourceBlobContainer”: “MySourceFolder”,
  “sinkBlobCountainer”: “MySinkFolder”
}
```

Il payload di risposta è un ID univoco dell'esecuzione della pipeline:

```json
{
  "runId": "0448d45a-a0bd-23f3-90a5-bfeea9264aed"
}
```

Per un esempio completo, vedere [Avvio rapido: Creare una data factory con Azure PowerShell](quickstart-create-data-factory-powershell.md).

### <a name="the-net-sdk"></a>.NET SDK
La chiamata di esempio seguente illustra come eseguire manualmente la pipeline usando .NET SDK:

```csharp
client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, parameters)
```

Per un esempio completo, vedere [Avvio rapido: Creare una data factory con .NET SDK](quickstart-create-data-factory-dot-net.md).

> [!NOTE]
> È possibile usare .NET SDK per richiamare le pipeline di Azure Data Factory da Funzioni di Azure, dai propri servizi Web e così via.

<h2 id="triggers">Attivare l'esecuzione</h2>
I trigger sono il secondo modo per avviare l'esecuzione di una pipeline. I trigger rappresentano un'unità di elaborazione che determina quando deve essere avviata l'esecuzione di una pipeline. Azure Data Factory supporta attualmente due tipi di trigger:
- Trigger di pianificazione: un trigger che richiama una pipeline con una pianificazione basata sul tempo reale.
- Trigger di finestra a cascata: un trigger che viene attivato a intervalli periodici, mantenendo al tempo stesso lo stato. Azure Data Factory attualmente non supporta i trigger basati su eventi, ad esempio il trigger per un'esecuzione di pipeline che risponde a un evento di ricezione di un file.

Pipeline e trigger hanno una relazione molti-a-molti. Più trigger possono avviare una singola pipeline e un singolo trigger può avviare più pipeline. Nella definizione di trigger seguente la proprietà **pipelines** fa riferimento a un elenco di pipeline attivate dal trigger specifico. La definizione della proprietà include i valori dei parametri della pipeline.

### <a name="basic-trigger-definition"></a>Definizione del trigger di base

```json
    "properties": {
        "name": "MyTrigger",
        "type": "<type of trigger>",
        "typeProperties": {
            …
        },
        "pipelines": [
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "<Name of your pipeline>"
                },
                "parameters": {
                    "<parameter 1 Name>": {
                        "type": "Expression",
                          "value": "<parameter 1 Value>"
                    },
                    "<parameter 2 Name>" : "<parameter 2 Value>"
                }
            }
        ]
    }
```

## <a name="schedule-trigger"></a>Trigger di pianificazione
Un trigger di pianificazione esegue le pipeline con una pianificazione basata sul tempo reale. Questo trigger supporta opzioni di calendario periodiche e avanzate. Il trigger supporta, ad esempio, intervalli come "settimanale" o "Lunedì alle 17:00 e giovedì alle 21:00". Il trigger di pianificazione è flessibile perché il criterio del set di dati è indipendente e il trigger non distingue tra dati di serie temporali e non temporali.

Per altre informazioni ed esempi sui trigger di pianificazione, vedere [Creare un trigger di pianificazione](how-to-create-schedule-trigger.md).

## <a name="tumbling-window-trigger"></a>Trigger di finestra a cascata
I trigger di finestra a cascata vengono attivati in base a un intervallo di tempo periodico a partire da un'ora di inizio specificata, mantenendo al tempo stesso lo stato. Le finestre a cascata sono costituite da una serie di intervalli temporali di dimensioni fisse, contigui e non sovrapposti. Per altre informazioni ed esempi sui trigger di finestra a cascata, vedere [Procedura: Creare un trigger di finestra a cascata](how-to-create-tumbling-window-trigger.md).

## <a name="schedule-trigger-definition"></a>Definizione del trigger di pianificazione
Quando si crea un trigger di pianificazione, si specificano la pianificazione e la ricorrenza usando una definizione JSON. 

Per fare in modo che il trigger di pianificazione attivi l'esecuzione di una pipeline, includere un riferimento di pipeline della pipeline specifica nella definizione del trigger. Pipeline e trigger hanno una relazione molti-a-molti. Più trigger possono attivare una singola pipeline. Un singolo trigger può attivare più pipeline.

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Year>>,
        "interval": <<int>>,             // How often to fire
        "startTime": <<datetime>>,
        "endTime": <<datetime>>,
        "timeZone": "UTC"
        "schedule": {                    // Optional (advanced scheduling specifics)
          "hours": [<<0-24>>],
          "weekDays": ": [<<Monday-Sunday>>],
          "minutes": [<<0-60>>],
          "monthDays": [<<1-31>>],
          "monthlyOccurences": [
               {
                    "day": <<Monday-Sunday>>,
                    "occurrence": <<1-5>>
               }
           ] 
        }
      }
    },
   "pipelines": [
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "<Name of your pipeline>"
                },
                "parameters": {
                    "<parameter 1 Name>": {
                        "type": "Expression",
                        "value": "<parameter 1 Value>"
                    },
                    "<parameter 2 Name>" : "<parameter 2 Value>"
                }
           }
      ]
  }
}
```

> [!IMPORTANT]
> **parameters** è una proprietà obbligatoria dell'elemento **pipelines**. Se la pipeline non accetta parametri, è necessario includere una definizione JSON vuota per la proprietà **parameters**.

### <a name="schema-overview"></a>Panoramica dello schema
La tabella seguente fornisce una panoramica generale degli elementi dello schema principali correlati alla ricorrenza e alla pianificazione di un trigger:

| Proprietà JSON | DESCRIZIONE |
|:--- |:--- |
| **startTime** | Valore di data e ora. Per le pianificazioni semplici, il valore della proprietà **startTime** si applica alla prima occorrenza. Per le pianificazioni complesse, il trigger viene attivato non prima del valore **startTime** specificato. |
| **endTime** | Data e ora di fine per il trigger. Il trigger non viene eseguito dopo la data e ora di fine specificata. Il valore della proprietà non può essere nel passato. <!-- This property is optional. --> |
| **timeZone** | Fuso orario. È attualmente supportato solo il fuso orario UTC. |
| **recurrence** | Oggetto recurrence che specifica le regole di ricorrenza per il trigger. L'oggetto recurrence supporta gli elementi **frequency**, **interval**, **endTime**, **count** e **schedule**. Quando viene definito un oggetto recurrence, l'elemento **frequency** è obbligatorio. Gli altri elementi dell'oggetto recurrence sono facoltativi. |
| **frequency** | Unità di frequenza con cui il trigger si ripete. I valori supportati includono "minute", "hour", "day", "week" e "month". |
| **interval** | Numero intero positivo indicante l'intervallo per il valore **frequency**, che determina la frequenza con cui viene eseguito il trigger. Se, ad esempio, **interval** è 3 e **frequency** è "week", il trigger si ripete ogni 3 settimane. |
| **schedule** | Pianificazione della ricorrenza per il trigger. Un trigger con un valore **frequency** specificato modifica la ricorrenza in base a una pianificazione di ricorrenza. La proprietà **schedule** contiene modifiche per la ricorrenza basate su minuti, ore, giorni della settimana, giorni del mese e numero settimana.

### <a name="schedule-trigger-example"></a>Esempio di trigger di pianificazione

```json
{
    "properties": {
        "name": "MyTrigger",
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Hour",
                "interval": 1,
                "startTime": "2017-11-01T09:00:00-08:00",
                "endTime": "2017-11-02T22:00:00-08:00"
            }
        },
        "pipelines": [{
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "SQLServerToBlobPipeline"
                },
                "parameters": {}
            },
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "SQLServerToAzureSQLPipeline"
                },
                "parameters": {}
            }
        ]
    }
}
```

### <a name="schema-defaults-limits-and-examples"></a>Impostazioni predefinite dello schema, limiti ed esempi

| Proprietà JSON | type | Obbligatoria | Valore predefinito | Valori validi | Esempio |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **startTime** | string | Sì | Nessuna | Date-Ore ISO-8601 | `"startTime" : "2013-01-09T09:30:00-08:00"` |
| **recurrence** | Oggetto | Sì | Nessuna | Oggetto ricorrenza | `"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **interval** | Number | No  | 1 | Da 1 a 1.000 | `"interval":10` |
| **endTime** | string | Sì | Nessuna | Valore di data e ora che fa riferimento a un momento nel futuro. | `"endTime" : "2013-02-09T09:30:00-08:00"` |
| **schedule** | Oggetto | No  | Nessuna | Oggetto pianificazione | `"schedule" : { "minute" : [30], "hour" : [8,17] }` |

### <a name="starttime-property"></a>Proprietà startTime
La tabella seguente illustra come la proprietà **startTime** controlla l'esecuzione di un trigger:

| Valore startTime | Ricorrenza senza pianificazione | Ricorrenza con pianificazione |
|:--- |:--- |:--- |
| Ora di inizio nel passato | Calcola l'ora della prima esecuzione futura dopo l'ora di inizio e avvia l'esecuzione in corrispondenza di tale ora.<br/><br/>Avvia le esecuzioni successive basandosi sul calcolo dall'ultima esecuzione.<br/><br/>Vedere l'esempio sotto a questa tabella. | Il trigger viene avviato _non prima_ dell'ora di inizio specificata. La prima occorrenza è basata sulla pianificazione calcolata dall'ora di inizio.<br/><br/>Avvia le esecuzioni successive in base alla pianificazione di ricorrenza. |
| Ora di inizio nel futuro o nel presente | Avvia l'esecuzione una sola volta all'ora di inizio specificata.<br/><br/>Avvia le esecuzioni successive basandosi sul calcolo dall'ultima esecuzione. | Il trigger viene avviato _non prima_ dell'ora di inizio specificata. La prima occorrenza è basata sulla pianificazione calcolata dall'ora di inizio.<br/><br/>Avvia le esecuzioni successive in base alla pianificazione di ricorrenza. |

È possibile esaminare uno scenario in cui l'ora di inizio è nel passato, con una ricorrenza, ma nessuna pianificazione. Si supponga che l'ora corrente sia `2017-04-08 13:00`, l'ora di inizio sia `2017-04-07 14:00` e la ricorrenza sia ogni due giorni. Il valore **recurrence** viene definito impostando la proprietà **frequency** su "day" e la proprietà **interval** su 2. Si noti che il valore **startTime** è nel passato e si verifica prima dell'ora corrente.

In queste condizioni, la prima esecuzione avviene alle `2017-04-09 at 14:00`. Il motore dell'utilità di pianificazione calcola le occorrenze dall'ora di inizio dell'esecuzione. Vengono eliminate tutte le istanze in passato. Il motore utilizza l'istanza successiva che si verifica in futuro. In questo scenario l'ora di inizio è `2017-04-07 at 2:00pm`, quindi l'istanza successiva viene eseguita due giorni dopo tale orario, ovvero il giorno `2017-04-09 at 2:00pm`.

Si noti che la prima esecuzione sarebbe la stessa anche se il valore **startTime** fosse `2017-04-05 14:00` o `2017-04-01 14:00`. Dopo la prima esecuzione, le esecuzioni successive vengono calcolate in base alla pianificazione. Le esecuzioni successive vengono quindi avviate il `2017-04-11 at 2:00pm`, poi il `2017-04-13 at 2:00pm` e infine il `2017-04-15 at 2:00pm` e così via.

Quando infine le ore o i minuti non sono impostati nella pianificazione di un trigger, le ore o i minuti della prima esecuzione vengono usati come impostazioni predefinite.

### <a name="schedule-property"></a>Proprietà schedule
Da un lato, l'uso di una pianificazione può limitare il numero di esecuzioni di un trigger. Se ad esempio la pianificazione di un trigger con frequenza mensile prevede l'esecuzione solo il giorno 31, il trigger viene eseguito solo nei mesi che includono 31 giorni.

Un oggetto schedule può tuttavia anche aumentare il numero di esecuzioni di un trigger. Ad esempio, un trigger con una frequenza mensile la cui esecuzione è pianificata per i giorni 1 e 2 del mese, viene eseguito il primo e il secondo giorno del mese, invece che una volta al mese.

Se vengono specificati più elementi **schedule**, l'ordine di valutazione è dall'impostazione della pianificazione più grande alla più piccola. La valutazione inizia con il numero della settimana, quindi prosegue con il giorno del mese, il giorno della settimana, l'ora e infine il minuto.

La tabella seguente illustra in modo dettagliato gli elementi **schedule**:

| Elemento JSON | DESCRIZIONE | Valori validi |
|:--- |:--- |:--- |
| **minutes** | Minuti dell'ora in cui verrà eseguito il trigger. | <ul><li>Integer</li><li>Matrice di numeri interi</li></ul>
| **hours** | Ore del giorno in cui verrà eseguito il trigger. | <ul><li>Integer</li><li>Matrice di numeri interi</li></ul> |
| **weekDays** | Giorni della settimana in cui verrà eseguito il trigger. Il valore può essere specificato solo con una frequenza settimanale. | <ul><li>Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday</li><li>Matrice di valori in giorni (la dimensione massima della matrice è 7)</li><li>I valori dei giorni non fanno distinzione tra maiuscole e minuscole</li></ul> |
| **monthlyOccurrences** | Giorni del mese in cui verrà eseguito il trigger. Il valore può essere specificato solo con una frequenza mensile. | <ul><li>Matrice di oggetti **monthlyOccurence**: `{ "day": day,  "occurrence": occurence }`.</li><li>L'attributo **day** è il giorno della settimana in cui verrà eseguito il trigger. Ad esempio, una proprietà **monthlyOccurrences** con un valore **day** uguale a `{Sunday}` indica ogni domenica del mese. L'attributo **day** è obbligatorio.</li><li>L'attributo **occurrence** indica l'occorrenza dell'attributo **day** specificato durante il mese. Ad esempio, una proprietà **monthlyOccurrences** con i valori **day** e **occurrence** uguali a `{Sunday, -1}` indica l'ultima domenica del mese. L'attributo **occurrence** è facoltativo.</li></ul> |
| **monthDays** | Giorno del mese in cui verrà eseguito il trigger. Il valore può essere specificato solo con una frequenza mensile. | <ul><li>Qualsiasi valore <= -1 e >= -31</li><li>Qualsiasi valore >= 1 e <= 31</li><li>Matrice di valori</li></ul> |

## <a name="examples-of-trigger-recurrence-schedules"></a>Esempi di pianificazioni di ricorrenza del trigger
Questa sezione fornisce esempi di pianificazioni di ricorrenza con attenzione all'oggetto **schedule** e ai relativi elementi.

Gli esempi presumono che il valore **interval** sia 1 e che il valore **frequency** sia corretto in base alla definizione della pianificazione. Non è ad esempio possibile avere un valore **frequency** uguale a "day" e anche una modifica "monthDays" nell'oggetto **schedule**. Restrizioni come queste sono indicate nella tabella nella sezione precedente.

| Esempio | DESCRIZIONE |
|:--- |:--- |
| `{"hours":[5]}` | Viene eseguito alle 5:00 ogni giorno. |
| `{"minutes":[15], "hours":[5]}` | Viene eseguito alle 05:15 ogni giorno. |
| `{"minutes":[15], "hours":[5,17]}` | Viene eseguito alle 05:15 e 17:15 ogni giorno. |
| `{"minutes":[15,45], "hours":[5,17]}` | Viene eseguito alle 05:15, 05:45, 17:15 e 17:45 ogni giorno. |
| `{"minutes":[0,15,30,45]}` | Viene eseguito ogni 15 minuti. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | Viene eseguito ogni ora. Il trigger viene eseguito ogni ora. I minuti vengono controllati dal valore **startTime**, se specificato. Se non è specificato un valore, i minuti vengono controllati dall'ora di creazione. Se ad esempio l'ora di inizio o l'ora di creazione (qualunque delle due si applichi) è 12:25, il trigger viene eseguito alle 00:25, 01:25, 02:25, ..., 23:25.<br/><br/>Questa pianificazione equivale a un trigger con un valore **frequency** pari a "hour", un valore **interval** di 1 e nessun oggetto **schedule**. Questa pianificazione può essere usata con valori diversi per **frequency** e **interval** per creare altri trigger. Quando ad esempio il valore **frequency** è "month", la pianificazione viene eseguita solo una volta al mese, invece che ogni giorno, quando il valore **frequency** è "day." |
| `{"minutes":[0]}` | Viene eseguito ogni ora all'inizio dell'ora. Questo trigger viene eseguito ogni ora con l'ora che inizia alle 00:00, 01:00, 02:00 e così via.<br/><br/>Questa pianificazione equivale a un trigger con un valore **frequency** pari a "hour" e un valore **startTime** di zero minuti oppure nessun oggetto **schedule**, ma un valore **frequency** pari a "day". Se il valore **frequency** è "week" o "month", la pianificazione viene rispettivamente eseguita solo una volta alla settimana o una volta al giorno. |
| `{"minutes":[15]}` | Viene eseguito 15 minuti dopo l'inizio di ogni ora. Questo trigger viene eseguito ogni ora, 15 minuti dopo l'inizio dell'ora a iniziare dalle 00:15, 01:15, 02:15 e così via e terminando alle 23:15. |
| `{"hours":[17], "weekDays":["saturday"]}` | Viene eseguito alle 17:00 di ogni sabato. |
| `{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Viene eseguito alle 17:00 di ogni lunedì, mercoledì e venerdì. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Viene eseguito alle 17:15 e 17:45 di ogni lunedì, mercoledì e venerdì. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Viene eseguito ogni 15 minuti nei giorni feriali. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Viene eseguito ogni 15 minuti nei giorni feriali tra le 09:00 e le 16:45. |
| `{"weekDays":["tuesday", "thursday"]}` | Viene eseguito ogni martedì e giovedì all'ora di inizio specificata. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` | Viene eseguito alle 06:00 il ventottesimo giorno di ogni mese (presumendo che il valore di **frequency** sia "month"). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` | Viene eseguito alle 06:00 dell'ultimo giorno di ogni mese. Per eseguire un trigger l'ultimo giorno del mese, usare -1 invece del giorno 28, 29, 30 o 31. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | Viene eseguito alle 06:00 il primo e l'ultimo giorno di ogni mese. |
| `{monthDays":[1,14]}` | Viene eseguito il primo e il quattordicesimo giorno di ogni mese all'ora di inizio specificata. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Viene eseguito il primo venerdì di ogni mese alle 05:00 |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Viene eseguito il primo venerdì di ogni mese all'ora di inizio specificata. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | Viene eseguito il terzultimo venerdì di ogni mese, all'ora di inizio specificata. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Viene eseguito il primo e l'ultimo venerdì di ogni mese alle 05:15. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Viene eseguito il primo e l'ultimo venerdì di ogni mese all'ora di inizio specificata. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | Viene eseguito il quinto venerdì di ogni mese all'ora di inizio specificata. Quando non ci sono cinque venerdì nel mese, la pipeline non viene eseguita, perché è pianificata per essere eseguita solo il quinto venerdì del mese. È consigliabile usare -1 invece di 5 per il valore **occurrence** per eseguire il trigger l'ultimo venerdì del mese. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | Viene eseguito ogni 15 minuti l'ultimo venerdì del mese. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | Viene eseguito alle 05:15, 05:45, 17:15 e 17:45 il terzo mercoledì di ogni mese. |

## <a name="trigger-type-comparison"></a>Confronto dei tipi di trigger
Il trigger di finestra a cascata e il trigger di pianificazione funzionano entrambi in base agli heartbeat temporali, ma presentano alcune differenze.

La tabella seguente contiene un confronto del trigger di finestra a cascata e del trigger di pianificazione:

|  | Trigger&nbsp;di finestra&nbsp;a cascata | Trigger&nbsp;di pianificazione |
|:--- |:--- |:--- |
| **Scenari&nbsp;di recupero delle informazioni** | Supportato. Si possono pianificare esecuzioni della pipeline per finestre nel passato. | Non supportati. Le esecuzioni della pipeline possono essere eseguite solo in periodi di tempo compresi tra il momento presente e il futuro. |
| **Affidabilità** | 100% di affidabilità. Le esecuzioni della pipeline possono essere pianificate per tutte le finestre da una data di inizio specificata senza intervalli. | Meno affidabile. |
| **Funzionalità&nbsp;di ripetizione** | Supportato. Le esecuzioni non riuscite delle pipeline hanno un criterio di ripetizione predefinito pari a 0 oppure un criterio specificato dall'utente nella definizione di un trigger. Esegue automaticamente un nuovo tentativo quando le esecuzioni della pipeline non riescono a causa di limiti di concorrenza/server/limitazione delle richieste (codici di stato 400: Errore utente, 429: Troppe richieste e 500: Errore interno del server). | Non supportati. |
| **Concorrenza** | Supportato. Gli utenti possono impostare in modo esplicito i limiti di concorrenza per il trigger. Consente da 1 a un massimo di 50 esecuzioni simultanee di pipeline attivate. | Non supportati. |
| **Variabili&nbsp;di sistema** | Supporta l'uso delle variabili di sistema **WindowStart** e **WindowEnd**. Gli utenti possono accedere a `triggerOutputs().windowStartTime` e a `triggerOutputs().windowEndTime` come variabile di sistema del trigger nella definizione del trigger. I valori vengono usati rispettivamente come ora di inizio della finestra e ora di fine della finestra. Ad esempio, per un trigger di finestra a cascata che viene eseguito ogni ora, per la finestra compresa tra la 01:00 e le 02:00, la definizione è `triggerOutputs().WindowStartTime = 2017-09-01T01:00:00Z` e `triggerOutputs().WindowEndTime = 2017-09-01T02:00:00Z`. | Non supportati. |
| **Relazione pipeline&#x2011;a&#x2011;trigger** | Supporta una relazione uno a uno. Può essere attivata una sola pipeline. | Supporta relazioni molti a molti. Più trigger possono attivare una singola pipeline. Un singolo trigger può attivare più pipeline. | 

## <a name="next-steps"></a>Passaggi successivi
Vedere le esercitazioni seguenti:

- [Guida introduttiva: Creare una data factory con .NET SDK](quickstart-create-data-factory-dot-net.md)
- [Creare un trigger di pianificazione](how-to-create-schedule-trigger.md)
- [Creare un trigger di finestra a cascata](how-to-create-tumbling-window-trigger.md)
