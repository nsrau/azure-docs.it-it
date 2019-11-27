---
title: Riferimento YAML-attività ACR
description: Riferimento per la definizione di attività in YAML per Attività di Registro Azure Container, incluse le proprietà delle attività, i tipi e le proprietà dei passaggi e le variabili predefinite.
ms.topic: article
ms.date: 10/23/2019
ms.openlocfilehash: a27f55d08a7ed5d7bf3360030eabefc4b7720b82
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454644"
---
# <a name="acr-tasks-reference-yaml"></a>Riferimenti ad Attività di Registro Azure Container: YAML

La definizione di attività in più passaggi in Attività di Registro Azure Container offre una primitiva di calcolo specifica per contenitori e basata su compilazione, test e applicazione di patch sui contenitori. Questo articolo illustra i comandi, i parametri, le proprietà e la sintassi per i file YAML che definiscono le attività in più passaggi.

L'articolo contiene informazioni di riferimento per la creazione di file YAML di attività in più passaggi per Attività di Registro Azure Container. Per un'introduzione ad Attività di Registro Azure Container, vedere [ACR Tasks overview](container-registry-tasks-overview.md) (Panoramica di Attività di Registro Azure Container).

## <a name="acr-taskyaml-file-format"></a>Formato del file acr-task.yaml

Attività di Registro Azure Container supporta la dichiarazione di attività in più passaggi nella sintassi YAML standard. È possibile definire i passaggi di un'attività in un file YAML. È quindi possibile eseguire manualmente l'attività passando il file al comando [AZ ACR Run][az-acr-run] . In alternativa, usare il file per creare un'attività con [AZ ACR task create][az-acr-task-create] che viene attivata automaticamente in un commit Git o in un aggiornamento di un'immagine di base. Sebbene questo articolo si riferisca a `acr-task.yaml` come file che contiene i passaggi, Attività di Registro Azure Container può usare qualsiasi nome di file valido con un'[estensione supportata](#supported-task-filename-extensions).

Le primitive `acr-task.yaml` di livello superiore sono le **proprietà delle attività**, i **tipi di passaggi** e le **proprietà dei passaggi**.

* Le [proprietà dell'attività](#task-properties) si applicano a tutti i passaggi durante l'intera esecuzione dell'attività. Sono disponibili diverse proprietà di attività globali, tra cui:
  * `version`
  * `stepTimeout`
  * `workingDirectory`
* I [tipi di passaggi di attività](#task-step-types) rappresentano i tipi di azioni che possono essere eseguite in un'attività. Sono presenti tre tipi di passaggi:
  * `build`
  * `push`
  * `cmd`
* Le [proprietà dei passaggi dell'attività](#task-step-properties) sono parametri che si applicano a un singolo passaggio. Sono presenti diverse proprietà dei passaggi, tra cui:
  * `startDelay`
  * `timeout`
  * `when`
  * e altre ancora.

Di seguito viene indicato il formato di base di un file `acr-task.yaml` che contiene alcune proprietà comuni dei passaggi. Sebbene non sia una rappresentazione completa di tutte le proprietà dei passaggi disponibili o di tutti i possibili usi dei tipi di passaggi, l'esempio offre una panoramica generale del formato di file di base.

```yml
version: # acr-task.yaml format version.
stepTimeout: # Seconds each step may take.
steps: # A collection of image or container actions.
  - build: # Equivalent to "docker build," but in a multi-tenant environment
  - push: # Push a newly built or retagged image to a registry.
    when: # Step property that defines either parallel or dependent step execution.
  - cmd: # Executes a container, supports specifying an [ENTRYPOINT] and parameters.
    startDelay: # Step property that specifies the number of seconds to wait before starting execution.
```

### <a name="supported-task-filename-extensions"></a>Estensioni del nome di file di attività supportate

Per Attività di Registro Azure Container sono presenti diverse estensioni del nome di file, ad esempio `.yaml`, che viene elaborato come un file delle attività. Qualsiasi estensione *non* presente nell'elenco seguente viene considerata da Attività di Registro Azure Container come un file Docker: .yaml, .yml, .toml, .json, .sh, .bash, .zsh, .ps1, .ps, .cmd, .bat, .ts, .js, .php, .py, .rb, .lua

YAML è l'unico formato di file attualmente supportato da Attività di Registro Azure Container. Le altre estensioni del nome di file sono riservate per un possibile supporto futuro.

## <a name="run-the-sample-tasks"></a>Eseguire le attività di esempio

Sono disponibili diversi file di attività di esempio a cui si fa riferimento nelle sezioni seguenti di questo articolo. Le attività di esempio si trovano in un repository GitHub pubblico, [Azure-Samples/ACR-Tasks][acr-tasks]. È possibile eseguirli con il comando dell'interfaccia della riga di comando di Azure [AZ ACR Run][az-acr-run]. I comandi di esempio sono analoghi ai seguenti:

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

La formattazione dei comandi di esempio presuppone che sia stato configurato un registro contenitori predefinito nell'interfaccia della riga di comando di Azure e di conseguenza il parametro `--registry` viene omesso. Per configurare un registro di sistema predefinito, usare il comando [AZ Configure][az-configure] con il parametro `--defaults`, che accetta un valore `acr=REGISTRY_NAME`.

Per configurare ad esempio l'interfaccia della riga di comando di Azure con un registro contenitori predefinito denominato "myregistry", vedere l'esempio seguente:

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>Proprietà delle attività

Le proprietà delle attività vengono in genere visualizzate all'inizio di un file di `acr-task.yaml` e sono proprietà globali che si applicano durante l'esecuzione completa dei passaggi dell'attività. Alcune proprietà globali possono essere sostituite all'interno di un singolo passaggio.

| Proprietà | digitare | Facoltativo | DESCRIZIONE | Override supportato | Default value |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | stringa | Sì | Versione del file `acr-task.yaml` come analizzato dal servizio Attività di Registro Azure Container. Attività di Registro Azure Container cerca di mantenere la compatibilità con le versioni precedenti e questo valore consente di mantenere la compatibilità in una versione definita. Se non è specificato, l'impostazione predefinita è la versione più recente. | No | nessuno |
| `stepTimeout` | intero (secondi) | Sì | Numero massimo di secondi per l'esecuzione di un passaggio. Se la proprietà viene specificata in un'attività, imposta la proprietà `timeout` predefinita di tutti i passaggi. Se la proprietà `timeout` viene specificata in un passaggio, esegue l'override della proprietà fornita dall'attività. | Sì | 600 (10 minuti) |
| `workingDirectory` | stringa | Sì | Directory di lavoro del contenitore durante la fase di esecuzione. Se la proprietà viene specificata in un'attività, imposta la proprietà `workingDirectory` predefinita di tutti i passaggi. Se viene specificato in un passaggio, viene eseguito l'override della proprietà fornita dall'attività. | Sì | `$HOME` |
| `env` | [stringa, stringa, ...] | Sì |  Matrice di stringhe in formato `key=value` che definiscono le variabili di ambiente per l'attività. Se la proprietà viene specificata in un'attività, imposta la proprietà `env` predefinita di tutti i passaggi. Se viene specificato in un passaggio, viene eseguito l'override di tutte le variabili di ambiente ereditate dall'attività. | nessuno |
| `secrets` | [segreto, segreto,...] | Sì | Matrice di oggetti [Secret](#secret) . | nessuno |
| `networks` | [rete, rete,...] | Sì | Matrice di oggetti di [rete](#network) . | nessuno |

### <a name="secret"></a>secret

L'oggetto Secret presenta le proprietà seguenti.

| Proprietà | digitare | Facoltativo | DESCRIZIONE | Default value |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | stringa | No | Identificatore del segreto. | nessuno |
| `keyvault` | stringa | Sì | URL del segreto Azure Key Vault. | nessuno |
| `clientID` | stringa | Sì | ID client dell' [identità gestita assegnata dall'utente](container-registry-tasks-authentication-managed-identity.md) per le risorse di Azure. | nessuno |

### <a name="network"></a>Rete

L'oggetto di rete dispone delle proprietà seguenti.

| Proprietà | digitare | Facoltativo | DESCRIZIONE | Default value |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | stringa | No | Nome della rete. | nessuno |
| `driver` | stringa | Sì | Driver per gestire la rete. | nessuno |
| `ipv6` | bool | Sì | Indica se la rete IPv6 è abilitata. | `false` |
| `skipCreation` | bool | Sì | Indica se ignorare la creazione della rete. | `false` |
| `isDefault` | bool | Sì | Indica se la rete è una rete predefinita fornita con Azure Container Registry | `false` |

## <a name="task-step-types"></a>Tipi di passaggi delle attività

Attività di Registro Azure Container supporta tre tipi di passaggi. Ogni tipo di passaggio supporta diverse proprietà, indicate in dettaglio nella sezione per ogni tipo.

| Tipo di passaggio | DESCRIZIONE |
| --------- | ----------- |
| [`build`](#build) | Compila un'immagine del contenitore con la sintassi `docker build` nota. |
| [`push`](#push) | Esegue un'operazione `docker push` delle immagini compilate o contrassegnate nuovamente in un registro contenitori. Sono supportati Registro Azure Container, altri registri privati e l'hub Docker. |
| [`cmd`](#cmd) | Esegue un contenitore come un comando con parametri passati all'elemento `[ENTRYPOINT]` del contenitore. Il tipo di passaggio `cmd` supporta parametri come `env`, `detach`e altre opzioni di comando `docker run` note, abilitando unit test e test funzionali con l'esecuzione simultanea del contenitore. |

## <a name="build"></a>build

Compila un'immagine del contenitore. Il tipo di passaggio `build` rappresenta uno strumento multi-tenant sicuro per l'esecuzione di `docker build` nel cloud come primitiva di prima classe.

### <a name="syntax-build"></a>Sintassi: build

```yml
version: v1.1.0
steps:
  - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
    [property]: [value]
```

Il tipo di passaggio `build` supporta i parametri nella tabella seguente. Il tipo di passaggio `build` supporta anche tutte le opzioni di compilazione del comando [docker build](https://docs.docker.com/engine/reference/commandline/build/), ad esempio `--build-arg`, per impostare le variabili in fase di compilazione.

| . | DESCRIZIONE | Facoltativo |
| --------- | ----------- | :-------: |
| `-t` &#124; `--image` | Definisce il percorso completo `image:tag` dell'immagine compilata.<br /><br />Poiché le immagini possono essere usate per le convalide di attività interne, ad esempio test funzionali, non tutte le immagini richiedono l'operazione `push` in un registro contenitori. Per creare un'istanza di un'immagine nell'esecuzione di un'attività, tuttavia, è necessario che all'immagine sia associato un nome di riferimento.<br /><br />A differenza `az acr build`, l'esecuzione di attività ACR non fornisce un comportamento di push predefinito. Con Attività di Registro Azure Container, lo scenario predefinito presuppone la possibilità di compilare e di convalidare un'immagine e quindi di eseguirne il push. Per informazioni su come eseguire il push facoltativo di immagini compilate, vedere [push](#push). | Sì |
| `-f` &#124; `--file` | Specifica l'elemento Dockerfile passato a `docker build`. Se non specificato, viene usato il valore predefinito Dockerfile nella directory radice del contesto. Per specificare un Dockerfile, passare il nome file relativo alla radice del contesto. | Sì |
| `context` | Directory radice passata a `docker build`. La directory radice di ogni attività è impostata su un oggetto condiviso [workingDirectory](#task-step-properties) e include la radice della directory clonata Git associata. | No |

### <a name="properties-build"></a>Proprietà: build

Il tipo di passaggio `build` supporta le proprietà seguenti. Per informazioni dettagliate su queste proprietà, vedere la sezione relativa alle [proprietà del passaggio attività](#task-step-properties) di questo articolo.

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Facoltativo |
| `disableWorkingDirectoryOverride` | bool | Facoltativo |
| `entryPoint` | stringa | Facoltativo |
| `env` | [stringa, stringa, ...] | Facoltativo |
| `expose` | [stringa, stringa, ...] | Facoltativo |
| `id` | stringa | Facoltativo |
| `ignoreErrors` | bool | Facoltativo |
| `isolation` | stringa | Facoltativo |
| `keep` | bool | Facoltativo |
| `network` | oggetto | Facoltativo |
| `ports` | [stringa, stringa, ...] | Facoltativo |
| `pull` | bool | Facoltativo |
| `repeat` | int | Facoltativo |
| `retries` | int | Facoltativo |
| `retryDelay` | intero (secondi) | Facoltativo |
| `secret` | oggetto | Facoltativo |
| `startDelay` | intero (secondi) | Facoltativo |
| `timeout` | intero (secondi) | Facoltativo |
| `when` | [stringa, stringa, ...] | Facoltativo |
| `workingDirectory` | stringa | Facoltativo |

### <a name="examples-build"></a>Esempi: build

#### <a name="build-image---context-in-root"></a>Compilare un'immagine - contesto nella directory radice

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>Compilare un'immagine - contesto nella sottodirectory

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>push

Esegue il push di una o più immagini compilate o contrassegnate nuovamente in un registro contenitori. Supporta il push in registri privati, ad esempio in Registro Azure Container o nell'hub Docker pubblico.

### <a name="syntax-push"></a>Sintassi: push

Il tipo di passaggio `push` supporta una raccolta di immagini. La sintassi delle raccolte YAML supporta formati annidati e inline. L'esecuzione del push di una singola immagine è in genere rappresentato usando la sintassi inline:

```yml
version: v1.1.0
steps:
  # Inline YAML collection syntax
  - push: ["$Registry/hello-world:$ID"]
```

Per migliorare la leggibilità, usare la sintassi annidata quando si esegue il push di più immagini:

```yml
version: v1.1.0
steps:
  # Nested YAML collection syntax
  - push:
    - $Registry/hello-world:$ID
    - $Registry/hello-world:latest
```

### <a name="properties-push"></a>Proprietà: push

Il tipo di passaggio `push` supporta le proprietà seguenti. Per informazioni dettagliate su queste proprietà, vedere la sezione relativa alle [proprietà del passaggio attività](#task-step-properties) di questo articolo.

| | | |
| -------- | ---- | -------- |
| `env` | [stringa, stringa, ...] | Facoltativo |
| `id` | stringa | Facoltativo |
| `ignoreErrors` | bool | Facoltativo |
| `startDelay` | intero (secondi) | Facoltativo |
| `timeout` | intero (secondi) | Facoltativo |
| `when` | [stringa, stringa, ...] | Facoltativo |

### <a name="examples-push"></a>Esempi: push

#### <a name="push-multiple-images"></a>Eseguire il push di più immagini

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-push-hello-world.yaml)]

#### <a name="build-push-and-run"></a>Compilare, eseguire il push ed eseguire

```azurecli
az acr run -f build-run-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-run-hello-world.yaml)]

## <a name="cmd"></a>cmd

Il tipo di passaggio `cmd` esegue un contenitore.

### <a name="syntax-cmd"></a>Sintassi: cmd

```yml
version: v1.1.0
steps:
  - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>Proprietà: cmd

Il tipo di passaggio `cmd` supporta le proprietà seguenti:

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Facoltativo |
| `disableWorkingDirectoryOverride` | bool | Facoltativo |
| `entryPoint` | stringa | Facoltativo |
| `env` | [stringa, stringa, ...] | Facoltativo |
| `expose` | [stringa, stringa, ...] | Facoltativo |
| `id` | stringa | Facoltativo |
| `ignoreErrors` | bool | Facoltativo |
| `isolation` | stringa | Facoltativo |
| `keep` | bool | Facoltativo |
| `network` | oggetto | Facoltativo |
| `ports` | [stringa, stringa, ...] | Facoltativo |
| `pull` | bool | Facoltativo |
| `repeat` | int | Facoltativo |
| `retries` | int | Facoltativo |
| `retryDelay` | intero (secondi) | Facoltativo |
| `secret` | oggetto | Facoltativo |
| `startDelay` | intero (secondi) | Facoltativo |
| `timeout` | intero (secondi) | Facoltativo |
| `when` | [stringa, stringa, ...] | Facoltativo |
| `workingDirectory` | stringa | Facoltativo |

I dettagli di tali proprietà sono descritti nella sezione [Proprietà dei passaggi delle attività](#task-step-properties) di questo articolo.

### <a name="examples-cmd"></a>Esempi: cmd

#### <a name="run-hello-world-image"></a>Eseguire l'immagine hello-world

Questo comando esegue il file di attività `hello-world.yaml`, che fa riferimento all'immagine [hello-world](https://hub.docker.com/_/hello-world/) nell'hub Docker.

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml -->
[!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>Eseguire l'immagine bash e il comando echo "hello world"

Questo comando esegue il file di attività `bash-echo.yaml`, che fa riferimento all'immagine [bash](https://hub.docker.com/_/bash/) nell'hub Docker.

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>Applicare il tag all'immagine bash specifica

Per eseguire una versione di immagine specifica, indicare il tag in `cmd`.

Questo comando esegue il file di attività `bash-echo-3.yaml`, che fa riferimento all'immagine [bash:3.0](https://hub.docker.com/_/bash/) nell'hub Docker.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>Eseguire immagini personalizzate

Il tipo di passaggio `cmd` fa riferimento alle immagini usando il formato `docker run` standard. Le immagini non precedute da un registro contenitori vengono considerate come provenienti da docker.io. L'esempio precedente potrebbe essere ugualmente rappresentato come:

```yml
version: v1.1.0
steps:
  - cmd: docker.io/bash:3.0 echo hello world
```

Usando la convenzione di riferimento per le immagini `docker run` standard, `cmd` possibile eseguire immagini da qualsiasi registro privato o dall'hub Docker pubblico. Se si fa riferimento a immagini nello stesso registro contenitori in cui è in esecuzione Attività di Registro Azure Container, non è necessario specificare alcuna credenziale di registro.

* Eseguire un'immagine da un registro contenitori di Azure. Nell'esempio seguente si presuppone che si disponga di un registro denominato `myregistry`e di un'immagine personalizzata `myimage:mytag`.

    ```yml
    version: v1.1.0
    steps:
        - cmd: myregistry.azurecr.io/myimage:mytag
    ```

* Generalizzare il riferimento al registro di sistema con una variabile di esecuzione o un alias

    Anziché impostare come hardcoded il nome del registro di sistema in un file di `acr-task.yaml`, è possibile renderlo più portabile usando una [variabile di esecuzione](#run-variables) o un [alias](#aliases). La variabile `Run.Registry` o l'alias `$Registry` si espande in fase di esecuzione con il nome del registro di sistema in cui è in esecuzione l'attività.

    Ad esempio, per generalizzare l'attività precedente in modo che funzioni in qualsiasi registro contenitori di Azure, fare riferimento alla variabile $Registry nel nome dell'immagine:

    ```yml
    version: v1.1.0
    steps:
      - cmd: $Registry/myimage:mytag
    ```

## <a name="task-step-properties"></a>Proprietà dei passaggi delle attività

Ogni tipo di passaggio supporta diverse proprietà appropriate per il tipo stesso. La tabella seguente definisce tutte le proprietà disponibili per un passaggio. Non tutti i tipi di passaggi supportano tutte le proprietà. Per visualizzare le proprietà disponibili per ogni tipo di passaggio, vedere le sezioni d riferimento al tipo di passaggio [cmd](#cmd), [build](#build) e [push](#push).

| Proprietà | digitare | Facoltativo | DESCRIZIONE | Default value |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | bool | Sì | Indica se il contenitore deve essere disconnesso durante l'esecuzione. | `false` |
| `disableWorkingDirectoryOverride` | bool | Sì | Indica se disabilitare `workingDirectory` funzionalità di sostituzione. Usare questa combinazione con `workingDirectory` per avere il controllo completo sulla directory di lavoro del contenitore. | `false` |
| `entryPoint` | stringa | Sì | Esegue l'override dell'elemento `[ENTRYPOINT]` di un contenitore del passaggio. | nessuno |
| `env` | [stringa, stringa, ...] | Sì | Matrice di stringhe in formato `key=value` che definiscono le variabili di ambiente per il passaggio. | nessuno |
| `expose` | [stringa, stringa, ...] | Sì | Matrice di porte esposte dal contenitore. |  nessuno |
| [`id`](#example-id) | stringa | Sì | Identifica in modo univoco il passaggio nell'attività. Altri passaggi nell'attività possono fare riferimento all'elemento `id` del passaggio, ad esempio per il controllo delle dipendenze con `when`.<br /><br />`id` è anche il nome del contenitore in esecuzione. I processi in esecuzione in altri contenitori nell'attività, ad esempio, possono fare riferimento all'elemento `id` come nome host DNS o per accedervi con l'elemento [id] dei log di Docker. | `acb_step_%d`, dove `%d` è l'indice in base zero del passaggio dall'alto verso il basso nel file YAML |
| `ignoreErrors` | bool | Sì | Indica se contrassegnare il passaggio come completato, indipendentemente dal fatto che si sia verificato un errore durante l'esecuzione del contenitore. | `false` |
| `isolation` | stringa | Sì | Livello di isolamento del contenitore. | `default` |
| `keep` | bool | Sì | Indica se il contenitore del passaggio deve essere mantenuto dopo l'esecuzione. | `false` |
| `network` | oggetto | Sì | Identifica una rete in cui viene eseguito il contenitore. | nessuno |
| `ports` | [stringa, stringa, ...] | Sì | Matrice di porte pubblicate dal contenitore nell'host. |  nessuno |
| `pull` | bool | Sì | Indica se forzare un pull del contenitore prima di eseguirlo per evitare qualsiasi comportamento di memorizzazione nella cache. | `false` |
| `privileged` | bool | Sì | Indica se eseguire il contenitore in modalità privilegiata. | `false` |
| `repeat` | int | Sì | Numero di tentativi di ripetizione dell'esecuzione di un contenitore. | 0 |
| `retries` | int | Sì | Numero di tentativi di tentativo di esecuzione di un contenitore con esito negativo. Un nuovo tentativo viene eseguito solo se il codice di uscita di un contenitore è diverso da zero. | 0 |
| `retryDelay` | intero (secondi) | Sì | Ritardo in secondi tra i tentativi di esecuzione di un contenitore. | 0 |
| `secret` | oggetto | Sì | Identifica un segreto Azure Key Vault o un' [identità gestita per le risorse di Azure](container-registry-tasks-authentication-managed-identity.md). | nessuno |
| `startDelay` | intero (secondi) | Sì | Numero di secondi per ritardare l'esecuzione di un contenitore. | 0 |
| `timeout` | intero (secondi) | Sì | Numero massimo di secondi per l'esecuzione di un passaggio prima che venga terminato. | 600 |
| [`when`](#example-when) | [stringa, stringa, ...] | Sì | Configura la dipendenza di un passaggio in uno o più passaggi nell'attività. | nessuno |
| `user` | stringa | Sì | Nome utente o UID di un contenitore | nessuno |
| `workingDirectory` | stringa | Sì | Imposta la directory di lavoro per un passaggio. Per impostazione predefinita, Attività di Registro Azure Container crea una directory radice come directory di lavoro. Se la compilazione prevede diversi passaggi, tuttavia, i passaggi precedenti possono condividere artefatti con quelli successivi specificando la stessa directory di lavoro. | `$HOME` |

### <a name="examples-task-step-properties"></a>Esempi: proprietà dei passaggi delle attività

#### <a name="example-id"></a>Esempio: id

Compilare due immagini, creando un'istanza di un'immagine di test funzionale. Ogni passaggio è identificato da un elemento `id` univoco, a cui altri passaggi nell'attività fanno riferimento nella proprietà `when`.

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>Esempio: when

La proprietà `when` specifica la dipendenza di un passaggio in altri passaggi nell'attività. Supporta due valori di parametro:

* `when: ["-"]` - Indica che in altri passaggi non è presente alcuna dipendenza. Un passaggio con la specifica `when: ["-"]` inizia immediatamente l'esecuzione e consente l'esecuzione simultanea dei passaggi.
* `when: ["id1", "id2"]` - Indica che il passaggio dipende dai passaggi con `id` "id1" e `id` "id2". Questo passaggio non viene eseguito fino a quando i passaggi "id1" e "id2" non sono completati.

Se `when` non è specificato in un passaggio, quest'ultimo dipende dal completamento di quello precedente nel file `acr-task.yaml`.

Esecuzione sequenziale di passaggi senza `when`:

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

Esecuzione sequenziale di passaggi con `when`:

```azurecli
az acr run -f when-sequential-id.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-id.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-id.yaml)]

Compilazione di immagini parallele:

```azurecli
az acr run -f when-parallel.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel.yaml)]

Compilazione di immagini in parallelo e test di dipendenza:

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

## <a name="run-variables"></a>Variabili di esecuzione

Attività di Registro Azure Container contiene un set predefinito di variabili disponibili per i passaggi delle attività quando vengono eseguiti. A tali variabili è possibile accedere usando il formato `{{.Run.VariableName}}`, dove l'elemento `VariableName` è uno dei seguenti:

* `Run.ID`
* `Run.SharedVolume`
* `Run.Registry`
* `Run.RegistryName`
* `Run.Date`
* `Run.OS`
* `Run.Architecture`
* `Run.Commit`
* `Run.Branch`
* `Run.TaskName`

I nomi delle variabili sono in genere intuitivi. Di seguito sono riportati i dettagli delle variabili usate di frequente. A partire dalla versione YAML `v1.1.0`, è possibile usare un [alias di attività](#aliases) abbreviato e predefinito al posto della maggior parte delle variabili di esecuzione. Al posto di `{{.Run.Registry}}`, ad esempio, usare l'alias `$Registry`.

### <a name="runid"></a>Run.ID

Ogni esecuzione, tramite `az acr run`o l'esecuzione basata su trigger di attività create tramite `az acr task create`, dispone di un ID univoco. che rappresenta l'esecuzione corrente.

La variabile viene usata in genere per assegnare univocamente un tag a un'immagine:

```yml
version: v1.1.0
steps:
    - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistry"></a>Run.Registry

Nome completo del server del registro contenitori usato in genere per fare riferimento in modo generico al registro contenitori in cui l'attività è in esecuzione.

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistryname"></a>Esegui. RegistryName

Nome del registro contenitori. In genere viene usato nei passaggi dell'attività che non richiedono un nome di server completo, ad esempio `cmd` passaggi che eseguono i comandi dell'interfaccia della riga di comando di Azure nei registri.

```yml
version 1.1.0
steps:
# List repositories in registry
- cmd: az login --identity
- cmd: az acr repository list --name $RegistryName
```

### <a name="rundate"></a>Run.Date

Ora UTC corrente in cui l'esecuzione è stata avviata.

### <a name="runcommit"></a>Run.Commit

Per un'attività attivata da un commit in un repository GitHub, l'identificatore del commit.

### <a name="runbranch"></a>Esegui. Branch

Per un'attività attivata da un commit in un repository GitHub, il nome del ramo.

## <a name="aliases"></a>Alias

A partire da `v1.1.0`, le attività ACR supportano gli alias disponibili per i passaggi dell'attività quando vengono eseguiti. Gli alias sono concettualmente simili agli alias (collegamenti ai comandi) supportati in bash e altre shell dei comandi. 

Con un alias, è possibile avviare qualsiasi comando o gruppo di comandi (incluse le opzioni e i nomi file) immettendo una singola parola.

Le attività ACR supportano diversi alias predefiniti e anche alias personalizzati creati dall'utente.

### <a name="predefined-aliases"></a>Alias predefiniti

Gli alias delle attività seguenti sono disponibili per l'uso in sostituzione delle [variabili di esecuzione](#run-variables):

| Alias | Esegui variabile |
| ----- | ------------ |
| `ID` | `Run.ID` |
| `SharedVolume` | `Run.SharedVolume` |
| `Registry` | `Run.Registry` |
| `RegistryName` | `Run.RegistryName` |
| `Date` | `Run.Date` |
| `OS` | `Run.OS` |
| `Architecture` | `Run.Architecture` |
| `Commit` | `Run.Commit` |
| `Branch` | `Run.Branch` |

Nei passaggi dell'attività, anteporre un alias alla direttiva `$`, come nell'esempio seguente:

```yaml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID -f hello-world.dockerfile .
```

### <a name="image-aliases"></a>Alias di immagine

Ognuno degli alias seguenti punta a un'immagine stabile in Microsoft Container Registry. È possibile fare riferimento a ognuno di essi nella sezione `cmd` di un file di attività senza utilizzare una direttiva.

| Alias | Image |
| ----- | ----- |
| `acr` | `mcr.microsoft.com/acr/acr-cli:0.1` |
| `az` | `mcr.microsoft.com/acr/azure-cli:d0725bc` |
| `bash` | `mcr.microsoft.com/acr/bash:d0725bc` |
| `curl` | `mcr.microsoft.com/acr/curl:d0725bc` |

L'attività di esempio seguente usa diversi alias per [ripulire](container-registry-auto-purge.md) i tag di immagine anteriori a 7 giorni nel repository `samples/hello-world` nel registro di sistema Run:

```yaml
version: v1.1.0
steps:
  - cmd: acr tag list --registry $RegistryName --repository samples/hello-world
  - cmd: acr purge --registry $RegistryName --filter samples/hello-world:.* --ago 7d
```

### <a name="custom-alias"></a>Alias personalizzato

Definire un alias personalizzato nel file YAML e usarlo come illustrato nell'esempio seguente. Un alias può contenere solo caratteri alfanumerici. La direttiva predefinita per espandere un alias è il carattere `$`.

```yml
version: v1.1.0
alias:
  values:
    repo: myrepo
steps:
  - build: -t $Registry/$repo/hello-world:$ID -f Dockerfile .
```

È possibile collegarsi a un file YAML remoto o locale per le definizioni di alias personalizzate. L'esempio seguente contiene collegamenti a un file YAML nell'archivio BLOB di Azure:

```yml
version: v1.1.0
alias:
  src:  # link to local or remote custom alias files
    - 'https://link/to/blob/remoteAliases.yml?readSasToken'
[...]
```

## <a name="next-steps"></a>Passaggi successivi

Per una panoramica delle attività in più passaggi, vedere [Run multi-step build, test, and patch tasks in ACR Tasks](container-registry-tasks-multi-step.md) (Eseguire attività di compilazione, test e applicazione di patch in più passaggi in Attività di Registro Azure Container).

Per le compilazioni in un singolo passaggio, vedere [ACR Tasks overview](container-registry-tasks-overview.md) (Panoramica di Attività di Registro Azure Container).



<!-- IMAGES -->

<!-- LINKS - External -->
[acr-tasks]: https://github.com/Azure-Samples/acr-tasks

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-configure]: /cli/azure/reference-index#az-configure
