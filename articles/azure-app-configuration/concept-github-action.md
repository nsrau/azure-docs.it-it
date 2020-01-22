---
title: Usare le azioni di GitHub con la sincronizzazione della configurazione app Azure
description: Usare le azioni di GitHub per attivare un aggiornamento dell'istanza di configurazione dell'app quando vengono eseguite azioni definite in un repository GitHub
author: jpconnock
ms.author: jeconnoc
ms.date: 01/14/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 1b7586e6321318a3fe4566e8c744c17a1d7ee179
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310613"
---
# <a name="sync-your-app-configuration-instance-using-github-actions"></a>Sincronizzare l'istanza di configurazione dell'app usando le azioni di GitHub
App Azure configurazione usa le azioni di GitHub per aggiornare un'istanza di configurazione dell'app quando viene attivata da un'azione eseguita in un repository GitHub. È possibile sfruttare i flussi di lavoro GitHub per aggiornare la configurazione dell'app, consentendo l'integrazione degli aggiornamenti della configurazione dell'app nello stesso flusso di lavoro usato per aggiornare il codice dell'app.

Un flusso di [lavoro](https://help.github.com/articles/about-github-actions#workflow) di azioni di GitHub è un processo automatico definito nel repository GitHub. Questo processo spiega a GitHub come compilare e distribuire il progetto GitHub. App Azure configurazione fornisce l'azione di *sincronizzazione della configurazione app Azure* per abilitare gli aggiornamenti a un'istanza di configurazione dell'app quando vengono apportate modifiche al repository di origine. 

Un flusso di lavoro è definito da un file YAML (. yml) trovato nel percorso `/.github/workflows/` del repository. Questa definizione contiene i vari passaggi e parametri che definiscono il flusso di lavoro.

Gli eventi di GitHub, ad esempio un push in un repository, possono attivare un flusso di lavoro di azione GitHub.  Azure fornisce l'azione di *sincronizzazione della configurazione app Azure* per consentire l'attivazione di un aggiornamento di un'istanza di configurazione dell'app quando si verifica un'azione GitHub specificata. Questo consente ai team di sfruttare le funzionalità di base di GitHub quando si effettua il push, la revisione o la creazione di rami dei file di configurazione delle app esattamente come avviene con il codice dell'app.

La [documentazione](https://help.github.com/actions/automating-your-workflow-with-github-actions/configuring-a-workflow) di GitHub fornisce una panoramica approfondita dei flussi di lavoro e delle azioni di GitHub. 

## <a name="enable-github-actions-in-your-repository"></a>Abilitare le azioni di GitHub nel repository
Per iniziare a usare questa azione GitHub, passare al repository e selezionare la scheda **azioni** . trovare e selezionare l'azione GitHub nel Marketplace cercando "app Azure Sync Configuration". 

> [!div class="mx-imgBorder"]
> ![selezionare la scheda azione](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![selezionare l'azione SYN configurazione app](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>Sincronizza i file di configurazione dopo un push
Questa azione Sincronizza i file di configurazione app Azure quando viene effettuato il push di una modifica in `appsettings.json`. Quando uno sviluppatore apporta e inserisce una modifica in `appsettings.json`, l'azione di sincronizzazione della configurazione dell'app Aggiorna l'istanza di configurazione dell'app con i nuovi valori.

La prima sezione di questo flusso di lavoro specifica che l'azione *attiva un* *push* contenente `appsettings.json` al ramo *Master* . Nella seconda sezione vengono elencati i processi eseguiti dopo l'attivazione dell'azione. L'azione estrae i file rilevanti e aggiorna l'istanza di configurazione dell'app usando la stringa di connessione archiviata come segreto nel repository.  Per altre informazioni sull'uso dei segreti in GitHub, vedere [questo articolo](https://help.github.com/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets) sulla creazione e l'uso di segreti crittografati.

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your                        
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }} 
          separator: ':' 
```

## <a name="use-a-dynamic-label-on-sync"></a>Usa un'etichetta dinamica durante la sincronizzazione
L'azione precedente ha semplicemente aggiornato l'istanza di configurazione dell'app ogni volta che `appsettings.json` viene aggiornato. Questa azione inserisce un'etichetta dinamica a ogni sincronizzazione, assicurando che ogni sincronizzazione possa essere identificata in modo univoco.  Questo consente di eseguire rapidamente il mapping delle modifiche del codice alle modifiche di configurazione.

La prima sezione di questo flusso di lavoro specifica che l'azione *attiva un* *push* contenente `appsettings.json` al ramo *Master* . La seconda sezione esegue un processo che crea un'etichetta univoca per l'aggiornamento di configurazione in base all'hash di commit. Il processo aggiorna quindi l'istanza di configurazione dell'app con i nuovi valori e l'etichetta univoca per l'aggiornamento.

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # Creates a label based on the branch name and the first 8 characters          
      # of the commit hash 
      - id: determine_label 
        run: echo ::set-output name=LABEL::"${GITHUB_REF#refs/*/}/${GITHUB_SHA:0:8}" 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          label: ${{ steps.determine_label.outputs.LABEL }} 
```

## <a name="use-strict-sync"></a>Usare la sincronizzazione Strict
Quando è abilitata la modalità Strict, la sincronizzazione garantisce che l'istanza di configurazione dell'app corrisponda esattamente al file di configurazione per il prefisso e l'etichetta specificati. Le coppie chiave-valore con lo stesso prefisso e l'etichetta che non sono presenti nel file di configurazione vengono eliminate. 
 
Se la modalità Strict non è abilitata, la sincronizzazione imposterà solo i valori chiave dal file di configurazione. Non verranno eliminate coppie chiave-valore. 

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          label: 'Label' 
          prefix: 'Prefix:' 
          strict: true 
```

## <a name="use-max-depth-to-limit-github-action"></a>Usare la profondità massima per limitare l'azione di GitHub
Il comportamento predefinito per gli attributi JSON annidati consiste nel rendere flat l'intero oggetto.  Il codice JSON seguente definisce questa coppia chiave-valore:

| Chiave | Valore |
| --- | --- |
| Oggetto: interno: InnerKey | InnerValue |

```json
{ "Object": 
    { "Inner":
        {
        "InnerKey": "InnerValue"
        }
    }
}
```
Se l'oggetto annidato è destinato a essere il valore inserito nell'istanza di configurazione, è possibile utilizzare il valore *Depth* per arrestare la bidimensionale alla profondità appropriata. 

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          depth: 2 
```

Data la profondità 2, nell'esempio precedente viene restituita la coppia chiave: valore seguente:

| Chiave | Valore |
| --- | --- |
| Oggetto: interno | {"InnerKey": "InnerValue"} |

## <a name="understand-action-inputs"></a>Informazioni sugli input azione
I parametri di input specificano i dati usati dall'azione durante il Runtime.  La tabella seguente contiene i parametri di input accettati dalla sincronizzazione della configurazione dell'app e i valori previsti per ognuno di essi.  Per ulteriori informazioni sugli input azione per le azioni di GitHub, vedere la [documentazione](https://help.github.com/actions/automating-your-workflow-with-github-actions/metadata-syntax-for-github-actions#inputs)di GitHub.

> [!Note]
> ID di input senza distinzione tra maiuscole e minuscole.


| Nome input | Obbligatorio? | Valore |
|----|----|----|
| configurationFile | Sì | Percorso del file di configurazione nel repository rispetto alla radice del repository.  I modelli Glob sono supportati e possono includere più file. |
| format | Sì | Formato di file del file di configurazione.  I formati validi sono: JSON, YAML, Properties. |
| connectionString | Sì | Stringa di connessione per l'istanza di configurazione dell'app. La stringa di connessione deve essere archiviata come segreto nel repository GitHub e il flusso di lavoro deve essere usato solo per il nome del segreto. |
| separator | Sì | Separatore utilizzato quando si rende flat il file di configurazione a coppie chiave-valore.  I valori validi sono:. , ; : - _ __ / |
| prefix | No | Prefisso da aggiungere all'inizio delle chiavi. |
| label | No | Etichetta utilizzata per l'impostazione di coppie chiave-valore. Se non è specificato, viene utilizzata un'etichetta null. |
| strict | No | Valore booleano che determina se è abilitata la modalità Strict. Il valore predefinito è false. |
| profondità | No | Profondità massima per rendere flat il file di configurazione.  La profondità deve essere un numero positivo.  Il valore predefinito non avrà profondità massima. |
| tags | No | Specifica il tag impostato sulle coppie chiave-valore.  Il formato previsto è un form file di un oggetto JSON con la forma seguente: {[propertyName: String]: String;} Ogni proprietà nome-valore diventa un tag. |

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come usare l'azione GitHub di sincronizzazione della configurazione dell'app e come può essere usata per automatizzare gli aggiornamenti all'istanza di configurazione dell'app. Per informazioni sul modo in cui app Azure configurazione reagisce alle modifiche nelle coppie chiave-valore, continuare con l' [articolo](./concept-app-configuration-event.md)successivo.
