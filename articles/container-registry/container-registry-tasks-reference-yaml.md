---
title: YAML reference - ACR Tasks
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

La definizione di attività in più passaggi in Attività di Registro Azure Container offre una primitiva di calcolo specifica per contenitori e basata su compilazione, test e applicazione di patch sui contenitori. This article covers the commands, parameters, properties, and syntax for the YAML files that define your multi-step tasks.

L'articolo contiene informazioni di riferimento per la creazione di file YAML di attività in più passaggi per Attività di Registro Azure Container. Per un'introduzione ad Attività di Registro Azure Container, vedere [ACR Tasks overview](container-registry-tasks-overview.md) (Panoramica di Attività di Registro Azure Container).

## <a name="acr-taskyaml-file-format"></a>Formato del file acr-task.yaml

Attività di Registro Azure Container supporta la dichiarazione di attività in più passaggi nella sintassi YAML standard. You define a task's steps in a YAML file. You can then run the task manually by passing the file to the [az acr run][az-acr-run] command. Or, use the file to create a task with [az acr task create][az-acr-task-create] that's triggered automatically on a Git commit or base image update. Sebbene questo articolo si riferisca a `acr-task.yaml` come file che contiene i passaggi, Attività di Registro Azure Container può usare qualsiasi nome di file valido con un'[estensione supportata](#supported-task-filename-extensions).

Le primitive `acr-task.yaml` di livello superiore sono le **proprietà delle attività**, i **tipi di passaggi** e le **proprietà dei passaggi**.

* Le [proprietà dell'attività](#task-properties) si applicano a tutti i passaggi durante l'intera esecuzione dell'attività. There are several global task properties, including:
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

Sono disponibili diversi file di attività di esempio a cui si fa riferimento nelle sezioni seguenti di questo articolo. The sample tasks are in a public GitHub repository, [Azure-Samples/acr-tasks][acr-tasks]. You can run them with the Azure CLI command [az acr run][az-acr-run]. I comandi di esempio sono analoghi ai seguenti:

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

La formattazione dei comandi di esempio presuppone che sia stato configurato un registro contenitori predefinito nell'interfaccia della riga di comando di Azure e di conseguenza il parametro `--registry` viene omesso. To configure a default registry, use the [az configure][az-configure] command with the `--defaults` parameter, which accepts an `acr=REGISTRY_NAME` value.

Per configurare ad esempio l'interfaccia della riga di comando di Azure con un registro contenitori predefinito denominato "myregistry", vedere l'esempio seguente:

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>Proprietà delle attività

Task properties typically appear at the top of an `acr-task.yaml` file, and are global properties that apply throughout the full execution of the task steps. Alcune proprietà globali possono essere sostituite all'interno di un singolo passaggio.

| Proprietà | Type | Facoltativo | Description | Override supportato | Valore predefinito |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | string | SÌ | Versione del file `acr-task.yaml` come analizzato dal servizio Attività di Registro Azure Container. Attività di Registro Azure Container cerca di mantenere la compatibilità con le versioni precedenti e questo valore consente di mantenere la compatibilità in una versione definita. If unspecified, defaults to the latest version. | No | Nessuno |
| `stepTimeout` | intero (secondi) | SÌ | Numero massimo di secondi per l'esecuzione di un passaggio. If the property is specified on a task, it sets the default `timeout` property of all the steps. If the `timeout` property is specified on a step, it overrides the property provided by the task. | SÌ | 600 (10 minuti) |
| `workingDirectory` | string | SÌ | The working directory of the container during runtime. If the property is specified on a task, it sets the default `workingDirectory` property of all the steps. If specified on a step, it overrides the property provided by the task. | SÌ | `$HOME` |
| `env` | [stringa, stringa, ...] | SÌ |  Array of strings in `key=value` format that define the environment variables for the task. If the property is specified on a task, it sets the default `env` property of all the steps. If specified on a step, it overrides any environment variables inherited from the task. | Nessuno |
| `secrets` | [secret, secret, ...] | SÌ | Array of [secret](#secret) objects. | Nessuno |
| `networks` | [network, network, ...] | SÌ | Array of [network](#network) objects. | Nessuno |

### <a name="secret"></a>secret

The secret object has the following properties.

| Proprietà | Type | Facoltativo | Description | Valore predefinito |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | string | No | The identifier of the secret. | Nessuno |
| `keyvault` | string | SÌ | The Azure Key Vault Secret URL. | Nessuno |
| `clientID` | string | SÌ | The client ID of the [user-assigned managed identity](container-registry-tasks-authentication-managed-identity.md) for Azure resources. | Nessuno |

### <a name="network"></a>Rete

The network object has the following properties.

| Proprietà | Type | Facoltativo | Description | Valore predefinito |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | string | No | Nome della rete. | Nessuno |
| `driver` | string | SÌ | The driver to manage the network. | Nessuno |
| `ipv6` | bool | SÌ | Whether IPv6 networking is enabled. | `false` |
| `skipCreation` | bool | SÌ | Whether to skip network creation. | `false` |
| `isDefault` | bool | SÌ | Whether the network is a default network provided with Azure Container Registry | `false` |

## <a name="task-step-types"></a>Tipi di passaggi delle attività

Attività di Registro Azure Container supporta tre tipi di passaggi. Ogni tipo di passaggio supporta diverse proprietà, indicate in dettaglio nella sezione per ogni tipo.

| Tipo di passaggio | Description |
| --------- | ----------- |
| [`build`](#build) | Compila un'immagine del contenitore con la sintassi `docker build` nota. |
| [`push`](#push) | Esegue un'operazione `docker push` delle immagini compilate o contrassegnate nuovamente in un registro contenitori. Sono supportati Registro Azure Container, altri registri privati e l'hub Docker. |
| [`cmd`](#cmd) | Esegue un contenitore come un comando con parametri passati all'elemento `[ENTRYPOINT]` del contenitore. The `cmd` step type supports parameters like `env`, `detach`, and other familiar `docker run` command options, enabling unit and functional testing with concurrent container execution. |

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

| Parametro | Description | Facoltativo |
| --------- | ----------- | :-------: |
| `-t` &#124; `--image` | Definisce il percorso completo `image:tag` dell'immagine compilata.<br /><br />Poiché le immagini possono essere usate per le convalide di attività interne, ad esempio test funzionali, non tutte le immagini richiedono l'operazione `push` in un registro contenitori. Per creare un'istanza di un'immagine nell'esecuzione di un'attività, tuttavia, è necessario che all'immagine sia associato un nome di riferimento.<br /><br />Unlike `az acr build`, running ACR Tasks doesn't provide default push behavior. Con Attività di Registro Azure Container, lo scenario predefinito presuppone la possibilità di compilare e di convalidare un'immagine e quindi di eseguirne il push. Per informazioni su come eseguire il push facoltativo di immagini compilate, vedere [push](#push). | SÌ |
| `-f` &#124; `--file` | Specifica l'elemento Dockerfile passato a `docker build`. Se non specificato, viene usato il valore predefinito Dockerfile nella directory radice del contesto. To specify a Dockerfile, pass the filename relative to the root of the context. | SÌ |
| `context` | Directory radice passata a `docker build`. La directory radice di ogni attività è impostata su un oggetto condiviso [workingDirectory](#task-step-properties) e include la radice della directory clonata Git associata. | No |

### <a name="properties-build"></a>Proprietà: build

Il tipo di passaggio `build` supporta le proprietà seguenti. Find details of these properties in the [Task step properties](#task-step-properties) section of this article.

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Facoltativo |
| `disableWorkingDirectoryOverride` | bool | Facoltativo |
| `entryPoint` | string | Facoltativo |
| `env` | [stringa, stringa, ...] | Facoltativo |
| `expose` | [stringa, stringa, ...] | Facoltativo |
| `id` | string | Facoltativo |
| `ignoreErrors` | bool | Facoltativo |
| `isolation` | string | Facoltativo |
| `keep` | bool | Facoltativo |
| `network` | object | Facoltativo |
| `ports` | [stringa, stringa, ...] | Facoltativo |
| `pull` | bool | Facoltativo |
| `repeat` | int | Facoltativo |
| `retries` | int | Facoltativo |
| `retryDelay` | intero (secondi) | Facoltativo |
| `secret` | object | Facoltativo |
| `startDelay` | intero (secondi) | Facoltativo |
| `timeout` | intero (secondi) | Facoltativo |
| `when` | [stringa, stringa, ...] | Facoltativo |
| `workingDirectory` | string | Facoltativo |

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

Il tipo di passaggio `push` supporta le proprietà seguenti. Find details of these properties in the [Task step properties](#task-step-properties) section of this article.

| | | |
| -------- | ---- | -------- |
| `env` | [stringa, stringa, ...] | Facoltativo |
| `id` | string | Facoltativo |
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
| `entryPoint` | string | Facoltativo |
| `env` | [stringa, stringa, ...] | Facoltativo |
| `expose` | [stringa, stringa, ...] | Facoltativo |
| `id` | string | Facoltativo |
| `ignoreErrors` | bool | Facoltativo |
| `isolation` | string | Facoltativo |
| `keep` | bool | Facoltativo |
| `network` | object | Facoltativo |
| `ports` | [stringa, stringa, ...] | Facoltativo |
| `pull` | bool | Facoltativo |
| `repeat` | int | Facoltativo |
| `retries` | int | Facoltativo |
| `retryDelay` | intero (secondi) | Facoltativo |
| `secret` | object | Facoltativo |
| `startDelay` | intero (secondi) | Facoltativo |
| `timeout` | intero (secondi) | Facoltativo |
| `when` | [stringa, stringa, ...] | Facoltativo |
| `workingDirectory` | string | Facoltativo |

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

By using the standard `docker run` image reference convention, `cmd` can run images from any private registry or the public Docker Hub. Se si fa riferimento a immagini nello stesso registro contenitori in cui è in esecuzione Attività di Registro Azure Container, non è necessario specificare alcuna credenziale di registro.

* Run an image that's from an Azure container registry. The following example assumes you have a registry named `myregistry`, and a custom image `myimage:mytag`.

    ```yml
    version: v1.1.0
    steps:
        - cmd: myregistry.azurecr.io/myimage:mytag
    ```

* Generalize the registry reference with a Run variable or alias

    Instead of hard-coding your registry name in an `acr-task.yaml` file, you can make it more portable by using a [Run variable](#run-variables) or [alias](#aliases). The `Run.Registry` variable or `$Registry` alias expands at runtime to the name of the registry in which the task is executing.

    For example, to generalize the preceding task so that it works in any Azure container registry, reference the $Registry variable in the image name:

    ```yml
    version: v1.1.0
    steps:
      - cmd: $Registry/myimage:mytag
    ```

## <a name="task-step-properties"></a>Proprietà dei passaggi delle attività

Ogni tipo di passaggio supporta diverse proprietà appropriate per il tipo stesso. La tabella seguente definisce tutte le proprietà disponibili per un passaggio. Non tutti i tipi di passaggi supportano tutte le proprietà. Per visualizzare le proprietà disponibili per ogni tipo di passaggio, vedere le sezioni d riferimento al tipo di passaggio [cmd](#cmd), [build](#build) e [push](#push).

| Proprietà | Type | Facoltativo | Description | Valore predefinito |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | bool | SÌ | Indica se il contenitore deve essere disconnesso durante l'esecuzione. | `false` |
| `disableWorkingDirectoryOverride` | bool | SÌ | Whether to disable `workingDirectory` override functionality. Use this in combination with `workingDirectory` to have complete control over the container's working directory. | `false` |
| `entryPoint` | string | SÌ | Esegue l'override dell'elemento `[ENTRYPOINT]` di un contenitore del passaggio. | Nessuno |
| `env` | [stringa, stringa, ...] | SÌ | Matrice di stringhe in formato `key=value` che definiscono le variabili di ambiente per il passaggio. | Nessuno |
| `expose` | [stringa, stringa, ...] | SÌ | Array of ports that are exposed from the container. |  Nessuno |
| [`id`](#example-id) | string | SÌ | Identifica in modo univoco il passaggio nell'attività. Altri passaggi nell'attività possono fare riferimento all'elemento `id` del passaggio, ad esempio per il controllo delle dipendenze con `when`.<br /><br />`id` è anche il nome del contenitore in esecuzione. I processi in esecuzione in altri contenitori nell'attività, ad esempio, possono fare riferimento all'elemento `id` come nome host DNS o per accedervi con l'elemento [id] dei log di Docker. | `acb_step_%d`, where `%d` is the 0-based index of the step top-down in the YAML file |
| `ignoreErrors` | bool | SÌ | Whether to mark the step as successful regardless of whether an error occurred during container execution. | `false` |
| `isolation` | string | SÌ | The isolation level of the container. | `default` |
| `keep` | bool | SÌ | Indica se il contenitore del passaggio deve essere mantenuto dopo l'esecuzione. | `false` |
| `network` | object | SÌ | Identifies a network in which the container runs. | Nessuno |
| `ports` | [stringa, stringa, ...] | SÌ | Array of ports that are published from the container to the host. |  Nessuno |
| `pull` | bool | SÌ | Whether to force a pull of the container before executing it to prevent any caching behavior. | `false` |
| `privileged` | bool | SÌ | Whether to run the container in privileged mode. | `false` |
| `repeat` | int | SÌ | The number of retries to repeat the execution of a container. | 0 |
| `retries` | int | SÌ | The number of retries to attempt if a container fails its execution. A retry is only attempted if a container's exit code is non-zero. | 0 |
| `retryDelay` | intero (secondi) | SÌ | The delay in seconds between retries of a container's execution. | 0 |
| `secret` | object | SÌ | Identifies an Azure Key Vault secret or [managed identity for Azure resources](container-registry-tasks-authentication-managed-identity.md). | Nessuno |
| `startDelay` | intero (secondi) | SÌ | Number of seconds to delay a container's execution. | 0 |
| `timeout` | intero (secondi) | SÌ | Numero massimo di secondi per l'esecuzione di un passaggio prima che venga terminato. | 600 |
| [`when`](#example-when) | [stringa, stringa, ...] | SÌ | Configura la dipendenza di un passaggio in uno o più passaggi nell'attività. | Nessuno |
| `user` | string | SÌ | The user name or UID of a container | Nessuno |
| `workingDirectory` | string | SÌ | Imposta la directory di lavoro per un passaggio. Per impostazione predefinita, Attività di Registro Azure Container crea una directory radice come directory di lavoro. Se la compilazione prevede diversi passaggi, tuttavia, i passaggi precedenti possono condividere artefatti con quelli successivi specificando la stessa directory di lavoro. | `$HOME` |

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

Parallel images build:

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

The variable names are generally self-explanatory. Details follows for commonly used variables. As of YAML version `v1.1.0`, you can use an abbreviated, predefined [task alias](#aliases) in place of most run variables. For example, in place of `{{.Run.Registry}}`, use the `$Registry` alias.

### <a name="runid"></a>Run.ID

Each Run, through `az acr run`, or trigger based execution of tasks created through `az acr task create`, has a unique ID. che rappresenta l'esecuzione corrente.

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

### <a name="runregistryname"></a>Run.RegistryName

The name of the container registry. Typically used in task steps that don't require a fully qualified server name, for example, `cmd` steps that run Azure CLI commands on registries.

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

For a task triggered by a commit to a GitHub repository, the commit identifier.

### <a name="runbranch"></a>Run.Branch

For a task triggered by a commit to a GitHub repository, the branch name.

## <a name="aliases"></a>Alias

As of `v1.1.0`, ACR Tasks supports aliases that are available to task steps when they execute. Aliases are similar in concept to aliases (command shortcuts) supported in bash and some other command shells. 

With an alias, you can launch any command or group of commands (including options and filenames) by entering a single word.

ACR Tasks supports several predefined aliases and also custom aliases you create.

### <a name="predefined-aliases"></a>Predefined aliases

The following task aliases are available to use in place of [run variables](#run-variables):

| Alias | Run variable |
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

In task steps, precede an alias with the `$` directive, as in this example:

```yaml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID -f hello-world.dockerfile .
```

### <a name="image-aliases"></a>Image aliases

Each of the following aliases points to a stable image in Microsoft Container Registry (MCR). You can refer to each of them in the `cmd` section of a Task file without using a directive.

| Alias | Immagine |
| ----- | ----- |
| `acr` | `mcr.microsoft.com/acr/acr-cli:0.1` |
| `az` | `mcr.microsoft.com/acr/azure-cli:d0725bc` |
| `bash` | `mcr.microsoft.com/acr/bash:d0725bc` |
| `curl` | `mcr.microsoft.com/acr/curl:d0725bc` |

The following example task uses several aliases to [purge](container-registry-auto-purge.md) image tags older than 7 days in the repo `samples/hello-world` in the run registry:

```yaml
version: v1.1.0
steps:
  - cmd: acr tag list --registry $RegistryName --repository samples/hello-world
  - cmd: acr purge --registry $RegistryName --filter samples/hello-world:.* --ago 7d
```

### <a name="custom-alias"></a>Custom alias

Define a custom alias in your YAML file and use it as shown in the following example. An alias can contain only alphanumeric characters. The default directive to expand an alias is the `$` character.

```yml
version: v1.1.0
alias:
  values:
    repo: myrepo
steps:
  - build: -t $Registry/$repo/hello-world:$ID -f Dockerfile .
```

You can link to a remote or local YAML file for custom alias definitions. The following example links to a YAML file in Azure blob storage:

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
