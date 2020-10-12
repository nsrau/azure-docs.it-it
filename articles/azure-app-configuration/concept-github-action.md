---
title: Sincronizzare il repository GitHub con Configurazione app
description: Usare GitHub Actions per aggiornare automaticamente l'istanza di Configurazione app quando si aggiorna il repository GitHub.
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/20/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 66d0e32e7dfdd5ab2abee5108ac8ce54c5222747
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87371822"
---
# <a name="sync-your-github-repository-to-app-configuration"></a>Sincronizzare il repository GitHub con Configurazione app

I team che vogliono continuare a usare le procedure di controllo del codice sorgente esistenti possono usare GitHub Actions per sincronizzare automaticamente il repository GitHub con il loro archivio di Configurazione app. In questo modo è possibile apportare modifiche ai file di configurazione come si farebbe normalmente, ottenendo al tempo stesso vantaggi per Configurazione app, ad esempio: <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Configurazione centralizzata all'esterno del codice <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Aggiornamento della configurazione senza dover ridistribuire l'intera app <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Integrazione con servizi come Servizio app e Funzioni di Azure. 

Un [flusso di lavoro](https://help.github.com/articles/about-github-actions#workflow) di GitHub Actions definisce un processo automatizzato in un repository GitHub. L'azione *Sincronizzazione di Configurazione app di Azure* attiva gli aggiornamenti a un'istanza di Configurazione app quando si apportano modifiche al repository di origine. L'azione usa un file YAML (.yml) disponibile nel percorso `/.github/workflows/` del repository per definire i passaggi e i parametri. È possibile attivare gli aggiornamenti della configurazione quando si effettua il push, la revisione o la diramazione dei file di configurazione delle app, proprio come avviene con il codice delle app.

La [documentazione](https://help.github.com/actions/automating-your-workflow-with-github-actions/configuring-a-workflow) di GitHub offre una panoramica approfondita dei flussi di lavoro e delle azioni di GitHub. 

## <a name="enable-github-actions-in-your-repository"></a>Abilitare GitHub Actions nel repository
Per iniziare a usare questa azione GitHub, passare al repository e selezionare la scheda **azioni** . Selezionare **nuovo flusso di lavoro**, quindi **configurare manualmente un flusso di lavoro**. Infine, cercare "Sincronizzazione di Configurazione app di Azure" nel marketplace.
> [!div class="mx-imgBorder"]
> ![Selezionare la scheda Azione](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![Selezionare l'azione di sincronizzazione di Configurazione app](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>Sincronizzare i file di configurazione dopo un push
Questa azione sincronizza i file di Configurazione app di Azure quando viene eseguito il push di una modifica in `appsettings.json`. Quando uno sviluppatore esegue il push di una modifica in `appsettings.json`, l'azione di sincronizzazione di Configurazione app aggiorna l'istanza di Configurazione app con i nuovi valori.

La prima sezione di questo flusso di lavoro specifica che l'azione attiva (*on*) un *push* contenente `appsettings.json` nel ramo *master*. Nella seconda sezione vengono elencati i processi eseguiti dopo l'attivazione dell'azione. L'azione estrae i file rilevanti e aggiorna l'istanza di Configurazione app usando la stringa di connessione archiviata come segreto nel repository.  Per altre informazioni sull'uso dei segreti in GitHub, vedere l'[articolo di GitHub](https://help.github.com/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets) sulla creazione e l'uso di segreti crittografati.

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

## <a name="use-strict-sync"></a>Usare la sincronizzazione in modalità Strict
Per impostazione predefinita, l'azione GitHub non abilita la modalità Strict. Questo significa che la sincronizzazione aggiungerà solo coppie chiave-valore dal file di configurazione all'istanza di Configurazione app (non saranno eliminate coppie chiave-valore). Se si abilita la modalità Strict, le coppie chiave-valore che non sono presenti nel file di configurazione vengono eliminate dall'istanza di Configurazione app, in modo che corrisponda al file di configurazione. Se si esegue la sincronizzazione da più origini o si usa Azure Key Vault con Configurazione app, è consigliabile usare prefissi o etichette diversi con una sincronizzazione in modalità Strict per evitare di cancellare le impostazioni di configurazione di altri file (vedere gli esempi riportati di seguito). 

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
## <a name="sync-multiple-files-in-one-action"></a>Sincronizzare più file in un'unica azione 

Se la configurazione si trova in più file, è possibile usare il criterio seguente per attivare una sincronizzazione quando uno dei file viene modificato. Questo modello usa la libreria glob https://www.npmjs.com/package/glob . Si noti che se il nome del file di configurazione contiene una virgola, è possibile usare una barra rovesciata per eseguire l'escape della virgola. 

```json
on:
  push:
    branches:
      - 'master'
    paths:
      - 'appsettings.json'
      - 'appsettings2.json'

jobs:
  syncconfig:
    runs-on: ubuntu-latest
    steps:
      # checkout done so that files in the repo can be read by the sync
      - uses: actions/checkout@v1
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: '{appsettings.json,appsettings2.json}'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
```

## <a name="sync-by-prefix-or-label"></a>Sincronizzazione per prefisso o etichetta
Se si specificano prefissi o etichette nell'azione di sincronizzazione, viene sincronizzato solo quel particolare set. Questa operazione è importante per usare la sincronizzazione Strict su più file. A seconda della modalità di configurazione, è possibile associare un prefisso o un'etichetta a ogni file, per poi sincronizzare separatamente ogni prefisso o etichetta per evitare sovrascritture. In genere i prefissi vengono usati per applicazioni o servizi diversi e le etichette vengono usate per ambienti diversi. 

Sincronizzazione per prefisso: 

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
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
          prefix: 'Prefix::'
```

Sincronizzazione per etichetta: 

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
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
          label: 'Label'

```

## <a name="use-a-dynamic-label-on-sync"></a>Usare un'etichetta dinamica durante la sincronizzazione
L'azione seguente consente di inserire un'etichetta dinamica a ogni sincronizzazione, assicurando che ogni sincronizzazione possa essere identificata in modo univoco e consentendo il mapping delle modifiche del codice alle modifiche di configurazione.

La prima sezione di questo flusso di lavoro specifica che l'azione attiva (*on*) un *push* contenente `appsettings.json` nel ramo *master*. La seconda sezione esegue un processo che crea un'etichetta univoca per l'aggiornamento della configurazione in base all'hash di commit. Il processo aggiorna quindi l'istanza di Configurazione app con i nuovi valori e l'etichetta univoca per l'aggiornamento.

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

## <a name="use-azure-key-vault-with-github-action"></a>Usare Azure Key Vault con GitHub Actions
Gli sviluppatori che usano Azure Key Vault con Configurazione app devono usare due file distinti, in genere appsettings.json e secretreferences.json. Il file secretreferences.json conterrà l'URL del segreto dell'insieme di credenziali delle chiavi.

{ "mySecret": "{\"uri\":\"https://myKeyVault.vault.azure.net/secrets/mySecret"}" }

L'azione GitHub può quindi essere configurata in modo da eseguire una sincronizzazione Strict in appsettings.json, seguita da una sincronizzazione non Strict in secretreferences.json. L'esempio seguente attiverà una sincronizzazione quando viene aggiornato uno dei due file:

```json
on:
  push:
    branches:
      - 'master'
    paths:
      - 'appsettings.json'
      - 'secretreferences.json'

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
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
          strict: true
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: 'secretreferences.json'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
          contentType: 'application/vnd.microsoft.appconfig.keyvaultref+json;charset=utf-8'

```

## <a name="use-max-depth-to-limit-github-action"></a>Usare la profondità massima per limitare l'azione di GitHub
Il comportamento predefinito per gli attributi JSON annidati consiste nel rendere flat l'intero oggetto.  Il codice JSON seguente definisce questa coppia chiave-valore:

| Chiave | valore |
| --- | --- |
| Object:Inner:InnerKey | InnerValue |

```json
{ "Object": 
    { "Inner":
        {
        "InnerKey": "InnerValue"
        }
    }
}
```

Se l'oggetto annidato è destinato a essere il valore di cui è stato eseguito il push nell'istanza di configurazione, è possibile usare il valore *depth* per arrestare la procedura di flattening alla profondità appropriata. 

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
| Object:Inner | {"InnerKey":"InnerValue"} |

## <a name="understand-action-inputs"></a>Informazioni sugli input di azione
I parametri di input specificano i dati usati dall'azione durante il runtime.  La tabella seguente contiene i parametri di input accettati dalla sincronizzazione di Configurazione app e i valori previsti per ognuno di essi.  Per altre informazioni sugli input di azione per GitHub Actions, vedere la [documentazione](https://help.github.com/actions/automating-your-workflow-with-github-actions/metadata-syntax-for-github-actions#inputs) di GitHub.

> [!Note]
> Per i gli ID input non viene fatta distinzione tra maiuscole e minuscole.


| Nome input | Obbligatorio? | valore |
|----|----|----|
| configurationFile | Sì | Percorso relativo del file di configurazione nel repository.  I criteri GLOB sono supportati e possono includere più file. |
| format | Sì | Il formato di file del file di configurazione.  Formati validi: JSON, YAML, properties. |
| connectionString | Sì | Stringa di connessione per l'istanza di Configurazione app. La stringa di connessione deve essere archiviata come segreto nel repository GitHub e nel flusso di lavoro deve essere usato solo il nome del segreto. |
| separator | Sì | Separatore usato quando si rende flat il file di configurazione in coppie chiave-valore.  Valori validi: . , ; : - _ __ / |
| prefix | No | Prefisso da aggiungere all'inizio delle chiavi. |
| label | No | Etichetta usata per l'impostazione di coppie chiave-valore. Se non è specificato, viene usata un'etichetta null. |
| strict | No | Valore booleano che determina se è abilitata la modalità Strict. Il valore predefinito è false. |
| depth | No | Profondità massima per rendere flat il file di configurazione.  La profondità deve essere un numero positivo.  Il valore predefinito non avrà profondità massima. |
| tags | No | Specifica il tag impostato sulle coppie chiave-valore.  Il formato previsto è un oggetto JSON in stringa con la forma seguente: { [propertyName: string]: string; }. Ogni valore propertyName diventa un tag. |

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato come usare l'azione di GitHub di sincronizzazione di Configurazione app per automatizzare gli aggiornamenti all'istanza di Configurazione app. Per informazioni su come Configurazione app di Azure reagisce alle modifiche nelle coppie chiave-valore, continuare con l'[articolo](./concept-app-configuration-event.md) successivo.
