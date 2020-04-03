---
title: Sincronizzare il repository GitHub con la configurazione dell'app
description: Usare le azioni GitHub per aggiornare automaticamente l'istanza di configurazione dell'app quando si aggiorna il repository GitHub.Use GitHub Actions to automatically update your App Configuration instance when you update your GitHub repository.
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/20/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 602ccddf97938022df3c5903b573608558fe5d35
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585488"
---
# <a name="sync-your-github-repository-to-app-configuration"></a>Sincronizzare il repository GitHub con la configurazione dell'app

I team che desiderano continuare a usare le procedure di controllo del codice sorgente esistenti possono usare le azioni GitHub per sincronizzare automaticamente il repository GitHub con l'archivio di configurazione delle app. In questo modo è possibile apportare modifiche ai file di configurazione come si farebbe normalmente, ottenendo i vantaggi di configurazione dell'app come: <br>
&nbsp;&nbsp;&nbsp;&nbsp;- Configurazione centralizzata al di fuori del codice <br>
&nbsp;&nbsp;&nbsp;&nbsp;Aggiornamento della configurazione senza ridistribuire l'intera app <br>
&nbsp;&nbsp;&nbsp;&nbsp;Integrazione con servizi come Il servizio app di Azure e funzioni. 

Un flusso [di lavoro](https://help.github.com/articles/about-github-actions#workflow) Azioni GitHub definisce un processo automatizzato in un repository GitHub.A GitHub Actions workflow defines an automated process in a GitHub repository. L'azione di *sincronizzazione* della configurazione dell'app di Azure attiva gli aggiornamenti a un'istanza di Configurazione app quando vengono apportate modifiche al repository di origine. Utilizza un file YAML (.yml) `/.github/workflows/` trovato nel percorso del repository per definire i passaggi e i parametri. Puoi attivare gli aggiornamenti della configurazione durante il push, la revisione o la diramazione dei file di configurazione dell'app come con il codice dell'app.

La [documentazione](https://help.github.com/actions/automating-your-workflow-with-github-actions/configuring-a-workflow) di GitHub fornisce una visione approfondita dei flussi di lavoro e delle azioni di GitHub.The GitHub documentation provides a-depth view of GitHub workflows and actions. 

## <a name="enable-github-actions-in-your-repository"></a>Abilitare le azioni GitHub nel repositoryEnable GitHub Actions in your repository
Per iniziare a utilizzare questa azione GitHub, accedere al repository e selezionare la scheda **Azioni.** Fare clic su **Nuovo flusso di lavoro**, quindi **impostare un flusso di lavoro manualmente**. Infine, cercare "Azure App Configuration Sync" nel marketplace.
> [!div class="mx-imgBorder"]
> ![Selezionare la scheda Azione](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![Selezionare l'azione di sincronizzazione della configurazione dell'app](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>Sincronizzare i file di configurazione dopo un push
Questa azione consente di sincronizzare i file `appsettings.json`di configurazione delle app di Azure quando viene inserita una modifica in . Quando uno sviluppatore invia `appsettings.json`una modifica a , l'azione Sincronizzazione configurazione app aggiorna l'istanza di configurazione dell'app con i nuovi valori.

La prima sezione di questo flusso di lavoro specifica `appsettings.json` che l'azione viene attivata *su* un *push* contenente al ramo *master.* Nella seconda sezione sono elencati i processi eseguiti dopo l'attivazione dell'azione. L'azione estrae i file pertinenti e aggiorna l'istanza di Configurazione app usando la stringa di connessione archiviata come segreto nel repository.  Per altre informazioni sull'uso di segreti in GitHub, vedere l'articolo di GitHub sulla creazione e [l'uso](https://help.github.com/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets) di segreti crittografati.

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

## <a name="use-a-dynamic-label-on-sync"></a>Usare un'etichetta dinamica durante la sincronizzazioneUse a dynamic label on sync
L'azione precedente aggiorna l'istanza di Configurazione app ogni volta che `appsettings.json` viene aggiornata. Questa azione inserisce un'etichetta dinamica in ogni sincronizzazione, assicurando che ogni sincronizzazione possa essere identificata in modo univoco e consentendo il mapping delle modifiche al codice alle modifiche di configurazione.

La prima sezione di questo flusso di lavoro specifica `appsettings.json` che l'azione viene attivata *su* un *push* contenente al ramo *master.* Nella seconda sezione viene eseguito un processo che crea un'etichetta univoca per l'aggiornamento della configurazione in base all'hash di commit. Il processo aggiorna quindi l'istanza di configurazione app con i nuovi valori e l'etichetta univoca per questo aggiornamento.

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

## <a name="use-strict-sync"></a>Utilizzare la sincronizzazione rigorosa
Quando è attivata la modalità rigorosa, la sincronizzazione garantisce che l'istanza di configurazione app corrisponda esattamente al file di configurazione per il prefisso e l'etichetta indicati. Le coppie chiave-valore con lo stesso prefisso ed etichetta non presenti nel file di configurazione vengono eliminate. 
 
Se la modalità rigorosa non è abilitata, la sincronizzazione imposterà solo le coppie chiave-valore dal file di configurazione. Non verrà eliminata alcuna coppia chiave-valore. 

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

## <a name="use-max-depth-to-limit-github-action"></a>Usare la profondità massima per limitare l'azione GitHubUse max depth to limit GitHub Action
Il comportamento predefinito per gli attributi JSON annidati consiste nell'appiattire l'intero oggetto.  Il codice JSON seguente definisce questa coppia chiave-valore:The JSON below defines this key-value pair:

| Chiave | valore |
| --- | --- |
| Oggetto:Inner:InnerKey | InnerValue (Valore interno) |

```json
{ "Object": 
    { "Inner":
        {
        "InnerKey": "InnerValue"
        }
    }
}
```

Se l'oggetto annidato deve essere il valore inserito nell'istanza Configuration, è possibile utilizzare il valore di *profondità* per interrompere la conversione alla profondità appropriata. 

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

Data una profondità pari a 2, l'esempio precedente ora restituisce la seguente coppia chiave-valore:

| Chiave | valore |
| --- | --- |
| Oggetto:Interno | "InnerKey": "InnerValue" |

## <a name="understand-action-inputs"></a>Comprendere gli input delle azioni
I parametri di input specificano i dati utilizzati dall'azione durante il runtime.  La tabella seguente contiene i parametri di input accettati dalla sincronizzazione della configurazione dell'app e i valori previsti per ognuno di essi.  Per ulteriori informazioni sugli input di azione per le azioni GitHub, vedere la [documentazione](https://help.github.com/actions/automating-your-workflow-with-github-actions/metadata-syntax-for-github-actions#inputs)di GitHub .

> [!Note]
> Gli ID di input non fanno distinzione tra maiuscole e minuscole.


| Nome input | Obbligatorio? | valore |
|----|----|----|
| configurationFile | Sì | Percorso relativo del file di configurazione nel repository.  I modelli Glob sono supportati e possono includere più file. |
| format | Sì | Formato di file del file di configurazione.  I formati validi sono: JSON, YAML, properties. |
| connectionString | Sì | Stringa di connessione per l'istanza di Configurazione app. La stringa di connessione deve essere archiviata come segreto nel repository GitHub e nel flusso di lavoro deve essere usato solo il nome del segreto. |
| separator | Sì | Separatore utilizzato quando si appiattisce il file di configurazione in coppie chiave-valore.  I valori validi sono: . , ; : - _ __ / |
| prefix | No | Prefisso da aggiungere all'inizio delle chiavi. |
| label | No | Etichetta utilizzata durante l'impostazione di coppie chiave-valore. Se non specificato, viene utilizzata un'etichetta null. |
| strict | No | Valore booleano che determina se è abilitata la modalità rigorosa. Il valore predefinito è false. |
| Profondità | No | Profondità massima per la conversione del file di configurazione.  La profondità deve essere un numero positivo.  L'impostazione predefinita non avrà una profondità massima. |
| tags | No | Specifica il tag impostato sulle coppie chiave-valore.  Il formato previsto è una forma stringaficata di un oggetto JSON della forma seguente: :[nomeproprietà: stringa]: stringa; Ogni nome-valore della proprietà diventa un tag. |

## <a name="next-steps"></a>Passaggi successivi

In questo articolo, hai appreso l'azione GitHub di sincronizzazione della configurazione dell'app e come può essere usata per automatizzare gli aggiornamenti all'istanza di configurazione dell'app. Per informazioni su come Azure App Configuration reagisce alle modifiche nelle coppie chiave-valore, passare [all'articolo](./concept-app-configuration-event.md)successivo.
