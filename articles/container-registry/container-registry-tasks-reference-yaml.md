---
title: Riferimenti ad attività di Registro Azure Container - YAML
description: Riferimento per la definizione di attività in YAML per Attività di Registro Azure Container, incluse le proprietà delle attività, i tipi e le proprietà dei passaggi e le variabili predefinite.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 11/13/2018
ms.author: danlep
ms.openlocfilehash: c9b4a27ff1b5467eb752e8cfc09f697ca1a966ba
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55820386"
---
# <a name="acr-tasks-reference-yaml"></a>Riferimento ad Attività del Registro Azure Container: YAML

La definizione di attività in più passaggi in Attività di Registro Azure Container offre una primitiva di calcolo specifica per contenitori e basata su compilazione, test e applicazione di patch sui contenitori. Questo articolo illustra i comandi, i parametri, le proprietà e la sintassi per i file YAML che definiscono le attività in più passaggi.

L'articolo contiene informazioni di riferimento per la creazione di file YAML di attività in più passaggi per Attività di Registro Azure Container. Per un'introduzione ad Attività di Registro Azure Container, vedere [ACR Tasks overview](container-registry-tasks-overview.md) (Panoramica di Attività di Registro Azure Container).

> [!IMPORTANT]
> La funzionalità di attività in più passaggi di Registro Azure Container è attualmente in anteprima. Le anteprime vengono rese disponibili a condizione che l'utente accetti le [condizioni supplementari per l'utilizzo][terms-of-use]. Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

## <a name="acr-taskyaml-file-format"></a>Formato del file acr-task.yaml

Attività di Registro Azure Container supporta la dichiarazione di attività in più passaggi nella sintassi YAML standard. I passaggi di un'attività vengono definiti in un file YAML che è possibile eseguire manualmente o sono generati automaticamente quando viene eseguito il commit del codice in Git o quando l'immagine di base del contenitore viene aggiornata. Sebbene questo articolo si riferisca a `acr-task.yaml` come file che contiene i passaggi, Attività di Registro Azure Container può usare qualsiasi nome di file valido con un'[estensione supportata](#supported-task-filename-extensions).

Le primitive `acr-task.yaml` di livello superiore sono le **proprietà delle attività**, i **tipi di passaggi** e le **proprietà dei passaggi**.

* Le [proprietà dell'attività](#task-properties) si applicano a tutti i passaggi durante l'intera esecuzione dell'attività. Sono presenti tre proprietà dell'attività globali:
  * version
  * stepTimeout
  * totalTimeout
* I [tipi di passaggi di attività](#task-step-types) rappresentano i tipi di azioni che possono essere eseguite in un'attività. Sono presenti tre tipi di passaggi:
  * build
  * push
  * cmd
* Le [proprietà dei passaggi dell'attività](#task-step-properties) sono parametri che si applicano a un singolo passaggio. Sono presenti diverse proprietà dei passaggi, tra cui:
  * startDelay
  * timeout
  * when
  * e altre ancora.

Di seguito viene indicato il formato di base di un file `acr-task.yaml` che contiene alcune proprietà comuni dei passaggi. Sebbene non sia una rappresentazione completa di tutte le proprietà dei passaggi disponibili o di tutti i possibili usi dei tipi di passaggi, l'esempio offre una panoramica generale del formato di file di base.

```yaml
version: # acr-task.yaml format version.
stepTimeout: # Seconds each step may take.
totalTimeout: # Total seconds allowed for all steps to complete.
steps: # A collection of image or container actions.
    build: # Equivalent to "docker build," but in a multi-tenant environment
    push: # Push a newly built or retagged image to a registry.
      when: # Step property that defines either parallel or dependent step execution.
    cmd: # Executes a container, supports specifying an [ENTRYPOINT] and parameters.
      startDelay: # Step property that specifies the number of seconds to wait before starting execution.
```

### <a name="supported-task-filename-extensions"></a>Estensioni del nome di file di attività supportate

Per Attività di Registro Azure Container sono presenti diverse estensioni del nome di file, ad esempio `.yaml`, che viene elaborato come un file delle attività. Qualsiasi estensione *non* presente nell'elenco seguente viene considerata da Attività di Registro Azure Container come un file Docker: .yaml, .yml, .toml, .json, .sh, .bash, .zsh, .ps1, .ps, .cmd, .bat, .ts, .js, .php, .py, .rb, .lua

YAML è l'unico formato di file attualmente supportato da Attività di Registro Azure Container. Le altre estensioni del nome di file sono riservate per un possibile supporto futuro.

## <a name="run-the-sample-tasks"></a>Eseguire le attività di esempio

Sono disponibili diversi file di attività di esempio a cui si fa riferimento nelle sezioni seguenti di questo articolo. Le attività di esempio si trovano in un repository GitHub pubblico, [Azure-Samples/acr-tasks][acr-tasks]. È possibile eseguirle con il comando dell'interfaccia della riga di comando di Azure [az acr run][az-acr-run]. I comandi di esempio sono analoghi ai seguenti:

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

La formattazione dei comandi di esempio presuppone che sia stato configurato un registro contenitori predefinito nell'interfaccia della riga di comando di Azure e di conseguenza il parametro `--registry` viene omesso. Per configurare un registro contenitori predefinito, usare il comando [az configure][az-configure] con il parametro `--defaults`, che accetta un valore `acr=REGISTRY_NAME`.

Per configurare ad esempio l'interfaccia della riga di comando di Azure con un registro contenitori predefinito denominato "myregistry", vedere l'esempio seguente:

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>Proprietà delle attività

Le proprietà delle attività vengono in genere visualizzate nella parte superiore di un file `acr-task.yaml` e sono proprietà globali applicate durante l'intera esecuzione dell'attività. Alcune proprietà globali possono essere sostituite all'interno di un singolo passaggio.

| Proprietà | Type | Facoltativo | DESCRIZIONE | Override supportato | Valore predefinito |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | stringa | No  | Versione del file `acr-task.yaml` come analizzato dal servizio Attività di Registro Azure Container. Attività di Registro Azure Container cerca di mantenere la compatibilità con le versioni precedenti e questo valore consente di mantenere la compatibilità in una versione definita. | No  | Nessuna |
| `stepTimeout` | intero (secondi) | Sì | Numero massimo di secondi per l'esecuzione di un passaggio. Questa proprietà può essere sottoposta a override in un passaggio impostando la proprietà timeout del passaggio. | Sì | 600 (10 minuti) |
| `totalTimeout` | intero (secondi) | Sì | Numero massimo di secondi per l'esecuzione di un'attività. Un'esecuzione include l'elaborazione e il completamento di tutti i passaggi dell'attività, sia con esito positivo che negativo. L'esecuzione include anche la stampa dell'output dell'attività, ad esempio le dipendenze delle immagini rilevato e lo stato di esecuzione dell'attività. | No  | 3600 (1 ora) |

## <a name="task-step-types"></a>Tipi di passaggi delle attività

Attività di Registro Azure Container supporta tre tipi di passaggi. Ogni tipo di passaggio supporta diverse proprietà, indicate in dettaglio nella sezione per ogni tipo.

| Tipo di passaggio | DESCRIZIONE |
| --------- | ----------- |
| [`build`](#build) | Compila un'immagine del contenitore con la sintassi `docker build` nota. |
| [`push`](#push) | Esegue un'operazione `docker push` delle immagini compilate o contrassegnate nuovamente in un registro contenitori. Sono supportati Registro Azure Container, altri registri privati e l'hub Docker.
| [`cmd`](#cmd) | Esegue un contenitore come un comando con parametri passati all'elemento `[ENTRYPOINT]` del contenitore. Il tipo di passaggio `cmd` supporta parametri come env, detach e altre opzioni familiari del comando `docker run`, consentendo in tal modo il test funzionale e di unità con l'esecuzione simultanea dei contenitori. |

## <a name="build"></a>build

Compila un'immagine del contenitore. Il tipo di passaggio `build` rappresenta uno strumento multi-tenant sicuro per l'esecuzione di `docker build` nel cloud come primitiva di prima classe.

### <a name="syntax-build"></a>Sintassi: build

```yaml
version: 1.0-preview-1
steps:
    - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
      [property]: [value]
```

Il tipo di passaggio `build` supporta i parametri nella tabella seguente. Il tipo di passaggio `build` supporta anche tutte le opzioni di compilazione del comando [docker build](https://docs.docker.com/engine/reference/commandline/build/), ad esempio `--build-arg`, per impostare le variabili in fase di compilazione.

| Parametro | DESCRIZIONE | Facoltativo |
| --------- | ----------- | :-------: |
| `-t` &#124; `--image` | Definisce il percorso completo `image:tag` dell'immagine compilata.<br /><br />Poiché le immagini possono essere usate per le convalide di attività interne, ad esempio test funzionali, non tutte le immagini richiedono l'operazione `push` in un registro contenitori. Per creare un'istanza di un'immagine nell'esecuzione di un'attività, tuttavia, è necessario che all'immagine sia associato un nome di riferimento.<br /><br />A differenza di `az acr build`, l'esecuzione di Attività di Registro Azure Container non prevede un comportamento del passaggio push predefinito. Con Attività di Registro Azure Container, lo scenario predefinito presuppone la possibilità di compilare e di convalidare un'immagine e quindi di eseguirne il push. Per informazioni su come eseguire il push facoltativo di immagini compilate, vedere [push](#push). | Sì |
| `-f` &#124; `--file` | Specifica l'elemento Dockerfile passato a `docker build`. Se non specificato, viene usato il valore predefinito Dockerfile nella directory radice del contesto. Per specificare un elemento Dockerfile alternativo, passare il nome del file relativo alla directory radice del contesto. | Sì |
| `context` | Directory radice passata a `docker build`. La directory radice di ogni attività è impostata su un oggetto condiviso [workingDirectory](#task-step-properties) e include la radice della directory clonata Git associata. | No  |

### <a name="properties-build"></a>Proprietà: build

Il tipo di passaggio `build` supporta le proprietà seguenti. I dettagli di tali proprietà sono descritti nella sezione [Proprietà dei passaggi delle attività](#task-step-properties) di questo articolo.

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Facoltativo |
| `entryPoint` | stringa | Facoltativo |
| `env` | [stringa, stringa, ...] | Facoltativo |
| `id` | stringa | Facoltativo |
| `ignoreErrors` | bool | Facoltativo |
| `keep` | bool | Facoltativo |
| `startDelay` | intero (secondi) | Facoltativo |
| `timeout` | intero (secondi) | Facoltativo |
| `when` | [stringa, stringa, ...] | Facoltativo |
| `workingDirectory` | stringa | Facoltativo |

### <a name="examples-build"></a>Esempi: build

#### <a name="build-image---context-in-root"></a>Compilare un'immagine - contesto nella directory radice

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml --> [!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>Compilare un'immagine - contesto nella sottodirectory

```yaml
version: 1.0-preview-1
steps:
- build: -t {{.Run.Registry}}/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>push

Esegue il push di una o più immagini compilate o contrassegnate nuovamente in un registro contenitori. Supporta il push in registri privati, ad esempio in Registro Azure Container o nell'hub Docker pubblico.

### <a name="syntax-push"></a>Sintassi: push

Il tipo di passaggio `push` supporta una raccolta di immagini. La sintassi delle raccolte YAML supporta formati annidati e inline. L'esecuzione del push di una singola immagine è in genere rappresentato usando la sintassi inline:

```yaml
version: 1.0-preview-1
steps:
  # Inline YAML collection syntax
  - push: ["{{.Run.Registry}}/hello-world:{{.Run.ID}}"]
```

Per migliorare la leggibilità, usare la sintassi annidata quando si esegue il push di più immagini:

```yaml
version: 1.0-preview-1
steps:
  # Nested YAML collection syntax
  - push:
    - {{.Run.Registry}}/hello-world:{{.Run.ID}}
    - {{.Run.Registry}}/hello-world:latest
```

### <a name="properties-push"></a>Proprietà: push

Il tipo di passaggio `push` supporta le proprietà seguenti. I dettagli di tali proprietà sono descritti nella sezione [Proprietà dei passaggi delle attività](#task-step-properties) di questo articolo.

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

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml --> [!code-yml[task](~/acr-tasks/build-push-hello-world.yaml)]

#### <a name="build-push-and-run"></a>Compilare, eseguire il push ed eseguire

```azurecli
az acr run -f build-run-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml --> [!code-yml[task](~/acr-tasks/build-run-hello-world.yaml)]

## <a name="cmd"></a>cmd

Il tipo di passaggio `cmd` esegue un contenitore.

### <a name="syntax-cmd"></a>Sintassi: cmd

```yaml
version: 1.0-preview-1
steps:
    - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>Proprietà: cmd

Il tipo di passaggio `cmd` supporta le proprietà seguenti:

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Facoltativo |
| `entryPoint` | stringa | Facoltativo |
| `env` | [stringa, stringa, ...] | Facoltativo |
| `id` | stringa | Facoltativo |
| `ignoreErrors` | bool | Facoltativo |
| `keep` | bool | Facoltativo |
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

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml --> [!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>Eseguire l'immagine bash e il comando echo "hello world"

Questo comando esegue il file di attività `bash-echo.yaml`, che fa riferimento all'immagine [bash](https://hub.docker.com/_/bash/) nell'hub Docker.

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml --> [!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>Applicare il tag all'immagine bash specifica

Per eseguire una versione di immagine specifica, indicare il tag in `cmd`.

Questo comando esegue il file di attività `bash-echo-3.yaml`, che fa riferimento all'immagine [bash:3.0](https://hub.docker.com/_/bash/) nell'hub Docker.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml --> [!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>Eseguire immagini personalizzate

Il tipo di passaggio `cmd` fa riferimento alle immagini usando il formato `docker run` standard. Le immagini non precedute da un registro contenitori vengono considerate come provenienti da docker.io. L'esempio precedente potrebbe essere ugualmente rappresentato come:

```yaml
version: 1.0-preview-1
steps:
    - cmd: docker.io/bash:3.0 echo hello world
```

Se si usa la convenzione di riferimento alle immagini `docker run` standard, `cmd` può possibile eseguire immagini che si trovano in qualsiasi registro contenitori privato o nell'hub Docker pubblico. Se si fa riferimento a immagini nello stesso registro contenitori in cui è in esecuzione Attività di Registro Azure Container, non è necessario specificare alcuna credenziale di registro.

* Eseguire un'immagine che si trova in un registro contenitori di Azure

    Sostituire `[myregistry]` con il nome del proprio registro contenitori:

    ```yaml
    version: 1.0-preview-1
    steps:
        - cmd: [myregistry].azurecr.io/bash:3.0 echo hello world
    ```

* Generalizzare il riferimento al registro contenitori con una variabile Run

    Anziché impostare il nome del registro contenitori come hardcoded in un file `acr-task.yaml`, è possibile aumentarne la portabilità usando una [variabile di esecuzione](#run-variables). La variabile `Run.Registry` si espande in fase di esecuzione nel nome del registro contenitori in cui l'attività in esecuzione.

    Per generalizzare l'attività precedente in modo che funzioni in qualsiasi Registro Azure Container, fare riferimento alla variabile [Run.Registry](#runregistry) nel nome dell'immagine:

    ```yaml
    version: 1.0-preview-1
    steps:
        - cmd: {{.Run.Registry}}/bash:3.0 echo hello world
    ```

## <a name="task-step-properties"></a>Proprietà dei passaggi delle attività

Ogni tipo di passaggio supporta diverse proprietà appropriate per il tipo stesso. La tabella seguente definisce tutte le proprietà disponibili per un passaggio. Non tutti i tipi di passaggi supportano tutte le proprietà. Per visualizzare le proprietà disponibili per ogni tipo di passaggio, vedere le sezioni d riferimento al tipo di passaggio [cmd](#cmd), [build](#build) e [push](#push).

| Proprietà | Type | Facoltativo | DESCRIZIONE |
| -------- | ---- | -------- | ----------- |
| `detach` | bool | Sì | Indica se il contenitore deve essere disconnesso durante l'esecuzione. |
| `entryPoint` | stringa | Sì | Esegue l'override dell'elemento `[ENTRYPOINT]` di un contenitore del passaggio. |
| `env` | [stringa, stringa, ...] | Sì | Matrice di stringhe in formato `key=value` che definiscono le variabili di ambiente per il passaggio. |
| [`id`](#example-id) | stringa | Sì | Identifica in modo univoco il passaggio nell'attività. Altri passaggi nell'attività possono fare riferimento all'elemento `id` del passaggio, ad esempio per il controllo delle dipendenze con `when`.<br /><br />`id` è anche il nome del contenitore in esecuzione. I processi in esecuzione in altri contenitori nell'attività, ad esempio, possono fare riferimento all'elemento `id` come nome host DNS o per accedervi con l'elemento [id] dei log di Docker. |
| `ignoreErrors` | bool | Sì | Se impostato su `true`, il passaggio è contrassegnato come completato indipendentemente dal fatto che si sia verificato un errore durante l'esecuzione. Impostazione predefinita: `false`. |
| `keep` | bool | Sì | Indica se il contenitore del passaggio deve essere mantenuto dopo l'esecuzione. |
| `startDelay` | intero (secondi) | Sì | Numero di secondi di ritardo per l'esecuzione di un passaggio. |
| `timeout` | intero (secondi) | Sì | Numero massimo di secondi per l'esecuzione di un passaggio prima che venga terminato. |
| [`when`](#example-when) | [stringa, stringa, ...] | Sì | Configura la dipendenza di un passaggio in uno o più passaggi nell'attività. |
| `workingDirectory` | stringa | Sì | Imposta la directory di lavoro per un passaggio. Per impostazione predefinita, Attività di Registro Azure Container crea una directory radice come directory di lavoro. Se la compilazione prevede diversi passaggi, tuttavia, i passaggi precedenti possono condividere artefatti con quelli successivi specificando la stessa directory di lavoro. |

### <a name="examples-task-step-properties"></a>Esempi: Proprietà dei passaggi delle attività

#### <a name="example-id"></a>Esempio: id

Compilare due immagini, creando un'istanza di un'immagine di test funzionale. Ogni passaggio è identificato da un elemento `id` univoco, a cui altri passaggi nell'attività fanno riferimento nella proprietà `when`.

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml --> [!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>Esempio: when

La proprietà `when` specifica la dipendenza di un passaggio in altri passaggi nell'attività. Supporta due valori di parametro:

* `when: ["-"]` - Indica che in altri passaggi non è presente alcuna dipendenza. Un passaggio con la specifica `when: ["-"]` inizia immediatamente l'esecuzione e consente l'esecuzione simultanea dei passaggi.
* `when: ["id1", "id2"]` - Indica che il passaggio dipende dai passaggi con `id` "id1" e `id` "id2". Questo passaggio non viene eseguito fino a quando i passaggi "id1" e "id2" non sono completati.

Se `when` non è specificato in un passaggio, quest'ultimo dipende dal completamento di quello precedente nel file `acr-task.yaml`.

Esecuzione sequenziale di passaggi senza `when`:

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml --> [!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

Esecuzione sequenziale di passaggi con `when`:

```azurecli
az acr run -f when-sequential-id.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-id.yaml --> [!code-yml[task](~/acr-tasks/when-sequential-id.yaml)]

Compilazione di immagini in parallelo:

```azurecli
az acr run -f when-parallel.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml --> [!code-yml[task](~/acr-tasks/when-parallel.yaml)]

Compilazione di immagini in parallelo e test di dipendenza:

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml --> [!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

## <a name="run-variables"></a>Variabili di esecuzione

Attività di Registro Azure Container contiene un set predefinito di variabili disponibili per i passaggi delle attività quando vengono eseguiti. A tali variabili è possibile accedere usando il formato `{{.Run.VariableName}}`, dove l'elemento `VariableName` è uno dei seguenti:

* `Run.ID`
* `Run.Registry`
* `Run.Date`

### <a name="run46id"></a>Run&#46;ID

A ogni esecuzione, avviata tramite `az acr run` o generata in base all'esecuzione di attività create con `az acr task create`, è associato un ID univoco che rappresenta l'esecuzione corrente.

La variabile viene usata in genere per assegnare univocamente un tag a un'immagine:

```yaml
version: 1.0-preview-1
steps:
    - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
```

### <a name="runregistry"></a>Run.Registry

Nome completo del server del registro contenitori usato in genere per fare riferimento in modo generico al registro contenitori in cui l'attività è in esecuzione.

```yaml
version: 1.0-preview-1
steps:
    - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
```

### <a name="rundate"></a>Run.Date

Ora UTC corrente in cui l'esecuzione è stata avviata.

## <a name="next-steps"></a>Passaggi successivi

Per una panoramica delle attività in più passaggi, vedere [Run multi-step build, test, and patch tasks in ACR Tasks](container-registry-tasks-multi-step.md) (Eseguire attività di compilazione, test e applicazione di patch in più passaggi in Attività di Registro Azure Container).

Per le compilazioni in un singolo passaggio, vedere [ACR Tasks overview](container-registry-tasks-overview.md) (Panoramica di Attività di Registro Azure Container).

<!-- IMAGES -->

<!-- LINKS - External -->
[acr-tasks]: https://github.com/Azure-Samples/acr-tasks
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-configure]: /cli/azure/reference-index#az-configure
