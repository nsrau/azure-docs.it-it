---
title: Sincronizzare il repository GitHub con la configurazione dell'app
description: Usare le azioni di GitHub per aggiornare automaticamente l'istanza di configurazione dell'app quando si aggiorna il repository GitHub.
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/20/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 602ccddf97938022df3c5903b573608558fe5d35
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80585488"
---
# <a name="sync-your-github-repository-to-app-configuration"></a>Sincronizzare il repository GitHub con la configurazione dell'app

I team che vogliono continuare a usare le procedure di controllo del codice sorgente esistenti possono usare le azioni di GitHub per sincronizzare automaticamente il repository GitHub con l'archivio di configurazione dell'app. In questo modo è possibile apportare modifiche ai file di configurazione come si farebbe normalmente, ottenendo al tempo stesso vantaggi per la configurazione delle app, ad esempio: <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Configurazione centralizzata all'esterno del codice <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Aggiornamento della configurazione senza ridistribuzione dell'intera app <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Integrazione con servizi come app Azure servizio e funzioni. 

Un flusso di [lavoro](https://help.github.com/articles/about-github-actions#workflow) di azioni di GitHub definisce un processo automatizzato in un repository GitHub. L'azione di *sincronizzazione della configurazione app Azure* attiva gli aggiornamenti a un'istanza di configurazione dell'app quando vengono apportate modifiche al repository di origine. Usa un file YAML (. yml) trovato nel `/.github/workflows/` percorso del repository per definire i passaggi e i parametri. È possibile attivare gli aggiornamenti della configurazione quando si effettua il push, si esaminano o si diramano i file di configurazione delle app esattamente come avviene con il codice dell'app.

La [documentazione](https://help.github.com/actions/automating-your-workflow-with-github-actions/configuring-a-workflow) di GitHub fornisce una panoramica approfondita dei flussi di lavoro e delle azioni di GitHub. 

## <a name="enable-github-actions-in-your-repository"></a>Abilitare le azioni di GitHub nel repository
Per iniziare a usare questa azione GitHub, passare al repository e selezionare la scheda **azioni** . fare clic su **nuovo flusso di lavoro**, quindi **configurare un flusso di lavoro manualmente**. Infine, cercare "app Azure Sync Configuration Sync" nel Marketplace.
> [!div class="mx-imgBorder"]
> ![Selezionare la scheda azione](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![Selezionare l'azione di sincronizzazione della configurazione dell'app](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>Sincronizza i file di configurazione dopo un push
Questa azione esegue la sincronizzazione dei file di configurazione app Azure quando viene effettuato `appsettings.json`il push di una modifica a. Quando uno sviluppatore effettua il push di una `appsettings.json`modifica a, l'azione di sincronizzazione della configurazione dell'app Aggiorna l'istanza di configurazione dell'app con i nuovi valori.

La prima sezione di questo flusso di lavoro specifica che l'azione *attiva un* *push* contenente `appsettings.json` nel ramo *Master* . Nella seconda sezione vengono elencati i processi eseguiti dopo l'attivazione dell'azione. L'azione estrae i file rilevanti e aggiorna l'istanza di configurazione dell'app usando la stringa di connessione archiviata come segreto nel repository.  Per altre informazioni sull'uso dei segreti in GitHub, vedere [l'articolo di GitHub](https://help.github.com/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets) sulla creazione e l'uso di segreti crittografati.

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
L'azione precedente aggiorna l'istanza di configurazione dell' `appsettings.json` app ogni volta che viene aggiornato. Questa azione inserisce un'etichetta dinamica a ogni sincronizzazione, assicurando che ogni sincronizzazione possa essere identificata in modo univoco e consentire il mapping delle modifiche del codice alle modifiche di configurazione.

La prima sezione di questo flusso di lavoro specifica che l'azione *attiva un* *push* contenente `appsettings.json` nel ramo *Master* . La seconda sezione esegue un processo che crea un'etichetta univoca per l'aggiornamento di configurazione in base all'hash di commit. Il processo aggiorna quindi l'istanza di configurazione dell'app con i nuovi valori e l'etichetta univoca per l'aggiornamento.

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

| Chiave | valore |
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

Data una profondità di 2, l'esempio precedente restituisce ora la coppia chiave-valore seguente:

| Chiave | valore |
| --- | --- |
| Oggetto: interno | {"InnerKey": "InnerValue"} |

## <a name="understand-action-inputs"></a>Informazioni sugli input azione
I parametri di input specificano i dati usati dall'azione durante il Runtime.  La tabella seguente contiene i parametri di input accettati dalla sincronizzazione della configurazione dell'app e i valori previsti per ognuno di essi.  Per ulteriori informazioni sugli input azione per le azioni di GitHub, vedere la [documentazione](https://help.github.com/actions/automating-your-workflow-with-github-actions/metadata-syntax-for-github-actions#inputs)di GitHub.

> [!Note]
> ID di input senza distinzione tra maiuscole e minuscole.


| Nome input | Necessaria? | valore |
|----|----|----|
| configurationFile | Sì | Percorso relativo del file di configurazione nel repository.  I modelli Glob sono supportati e possono includere più file. |
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
