---
title: Informazioni di riferimento sull'interfaccia della riga di comando di Gestione modelli di Azure Machine Learning | Microsoft Docs
description: Informazioni di riferimento sull'interfaccia della riga di comando di Gestione modelli di Azure Machine Learning.
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 11/08/2017
ms.openlocfilehash: 27361c5b92a8748a026d457875fadfc1f3529076
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="model-management-command-line-interface-reference"></a>Informazioni di riferimento sull'interfaccia della riga di comando di Gestione modelli

## <a name="base-cli-concepts"></a>Concetti CLI di base:

    account : Manage model management accounts. 
    env     : Manage compute environments.
    image   : Manage operationalization images.
    manifest: Manage operationalization manifests.
    model   : Manage operationalization models.
    service : Manage operationalized services.

## <a name="account-commands"></a>Comandi di account
Per usare i servizi, che consentono di distribuire e gestire modelli, è necessario un account di gestione modelli. Usare `az ml account modelmanagement -h` per visualizzare l'elenco seguente:

    create: Create a Model Management Account.
    delete: Delete a specified Model Management Account.
    list  : Gets the Model Management Accounts in the current subscriptiong.
    set   : Set the active Model Management Account.
    show  : Show a Model Management Account.
    update: Update an existing Model Management Account.

**Creare un account di gestione modelli**

Creare un account di gestione modelli per la fatturazione usando il comando seguente:

`az ml account modelmanagement create --location [Azure region e.g. eastus2] --name [new account name] --resource-group [resource group name to store the account in]`

Argomenti locali:

    --location -l       [Required]: Resource location.
    --name -n           [Required]: Name of the model management account.
    --resource-group -g [Required]: Resource group to create the model management account in.
    --description -d              : Description of the model management account.
    --sku-instances               : Number of instances of the selected SKU. Must be between 1 and
                                    16 inclusive.  Default: 1.
    --sku-name                    : SKU name. Valid names are S1|S2|S3|DevTest.  Default: S1.
    --tags -t                     : Tags for the model management account.  Default: {}.
    -v                            : Verbosity flag.

## <a name="environment-commands"></a>Comandi di ambiente

    cluster        : Switch the current execution context to 'cluster'.
    delete         : Delete an MLCRP-provisioned resource.
    get-credentials: List the keys for an environment.
    list           : List all environments in the current subscription.
    local          : Switch the current execution context to 'local'.
    set            : Set the active MLC environment.
    setup          : Sets up an MLC environment.
    show           : Show an MLC resource; if resource_group or cluster_name are not provided, shows
                     the active MLC env.

**Configurare l'ambiente di distribuzione**

Per il comando di configurazione è necessario avere l'accesso come collaboratore alla sottoscrizione. Se non si ha tale accesso, si deve avere almeno l'accesso come collaboratore al gruppo di risorse in cui viene eseguita la distribuzione. In questo secondo caso, è necessario specificare il nome del gruppo di risorse nel comando di configurazione usando il flag `-g`. 

Esistono due opzioni per la distribuzione: *locale* e *cluster*. L'impostazione del contrassegno `--cluster` (o `-c`) consente la distribuzione cluster, che esegue il provisioning di un cluster ACS. La sintassi di configurazione di base è la seguente:

`az ml env setup [-c] --location [location of environment resources] --name[name of environment]`

Questo comando inizializza l'ambiente di Azure Machine Learning con un account di archiviazione, il registro del record di controllo di accesso e il servizio App Insights creato nella sottoscrizione. Per impostazione predefinita, l'ambiente viene inizializzato per distribuzioni locali (non ACS), se non viene specificato alcun contrassegno. Se è necessario ridimensionare il servizio, specificare il contrassegno `--cluster` (o `-c`) per creare un cluster ACS.

Dettagli del comando:

    --location -l        [Required]: Location for environment resources; an Azure region, e.g. eastus2.
    --name -n            [Required]: Name of environment to provision.
    --acr -r                       : ARM ID of ACR to associate with this environment.
    --agent-count -z               : Number of agents to provision in the ACS cluster. Default: 3.
    --cert-cname                   : CNAME of certificate.
    --cert-pem                     : Path to .pem file with certificate bytes.
    --cluster -c                   : Flag to provision ACS cluster. Off by default; specify this to force an ACS cluster deployment.
    --key-pem                      : Path to .pem file with certificate key.
    --master-count -m              : Number of master nodes to provision in the ACS cluster. Acceptable values: 1, 3, 5. Default: 1.
    --resource-group -g            : Resource group in which to create compute resource. Is created if it does not exist.
                                     If not provided, resource group is created with 'rg' appended to 'name.'.
    --service-principal-app-id -a  : App ID of service principal to use for configuring ML compute.
    --service-principal-password -p: Password associated with service principal.
    --storage -s                   : ARM ID of storage account to associate with this environment.
    --yes -y                       : Flag to answer 'yes' to any prompts. Command fails if user is not logged in.

Argomenti globali
```
    --debug                        : Increase logging verbosity to show all debug logs.
    --help -h                      : Show this help message and exit.
    --output -o                    : Output format.  Allowed values: json, jsonc, table, tsv. Default: json.
    --query                        : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose                      : Increase logging verbosity. Use --debug for full debug logs.
```
## <a name="model-commands"></a>Comandi di modello

    list
    register
    show

**Registrare un modello**

Comando per registrare il modello.

`az ml model register --model [path to model file] --name [model name]`

Dettagli del comando:

    --model -m [Required]: Model to register.
    --name -n  [Required]: Name of model to register.
    --description -d     : Description of the model.
    --tag -t             : Tags for the model. Multiple tags can be specified with additional -t arguments.
    -v                   : Verbosity flag.

Argomenti globali

    --debug              : Increase logging verbosity to show all debug logs.
    --help -h            : Show this help message and exit.
    --output -o          : Output format.  Allowed values: json, jsonc, table, tsv.  Default: json.
    --query              : JMESPath query string. See http://jmespath.org/ for more information and
                           examples.
    --verbose            : Increase logging verbosity. Use --debug for full debug logs.

## <a name="manifest-commands"></a>Comandi di manifesto

    create: Create an Operationalization Manifest. This command has two different
            sets of required arguments, depending on if you want to use previously registered
            model/s.
    list
    show

**Creare un manifesto**

Il comando seguente crea un file manifesto per il modello. 

`az ml manifest create --manifest-name [your new manifest name] -f [path to code file] -r [runtime for the image, e.g. spark-py]`

Dettagli del comando:

    --manifest-name -n [Required]: Name of the manifest to create.
    -f                 [Required]: The code file to be deployed.
    -r                 [Required]: Runtime of the web service. Valid runtimes are spark-py|python.
    --conda-file -c              : Path to Conda Environment file.
    --dependency -d              : Files and directories required by the service. Multiple
                                   dependencies can be specified with additional -d arguments.
    --manifest-description       : Description of the manifest.
    --schema-file -s             : Schema file to add to the manifest.
    -p                           : A pip requirements.txt file needed by the code file.
    -v                           : Verbosity flag.

Argomenti di modello registrati

    --model-id -i                : [Required] Id of previously registered model to add to manifest.
                                   Multiple models can be specified with additional -i arguments.

Argomenti di modello non registrati

    --model-file -m              : [Required] Model file to register. If used, must be combined with
                                   model name.

Argomenti globali

    --debug                      : Increase logging verbosity to show all debug logs.
    --help -h                    : Show this help message and exit.
    --output -o                  : Output format.  Allowed values: json, jsonc, table, tsv.
                                   Default: json.
    --query                      : JMESPath query string. See http://jmespath.org/ for more
                                   information and examples.
    --verbose                    : Increase logging verbosity. Use --debug for full debug logs.


## <a name="image-commands"></a>Comandi di immagine

    create: Creates a docker image with the model and its dependencies. This command has two different sets of
            required arguments, depending on if you want to use a previously created manifest.
    list
    show
    usage

**Creare un'immagine**

È possibile creare un'immagine di cui prima è stato creato il relativo manifesto. 

`az ml image create -n [image name] --manifest-id [the manifest ID]`

Oppure è possibile creare il manifesto e l'immagine con un unico comando. 

`az ml image create -n [image name] --model-file [model file or folder path] -f [code file, e.g. the score.py file] -r [the runtime eg.g. spark-py which is the Docker container image base]`

Dettagli del comando:

    --image-name -n [Required]: The name of the image being created.
    --image-description       : Description of the image.
    --image-type              : The image type to create. Defaults to "Docker".
    -v                        : Verbosity flag.

Argomenti di manifesto registrati

    --manifest-id             : [Required] Id of previously registered manifest to use in image creation.

Argomenti di manifesto non registrati

    --conda-file -c           : Path to Conda Environment file.
    --dependency -d           : Files and directories required by the service. Multiple dependencies can
                                be specified with additional -d arguments.
    --model-file -m           : [Required] Model file to register.
    --schema-file -s          : Schema file to add to the manifest.
    -f                        : [Required] The code file to be deployed.
    -p                        : A pip requirements.txt file needed by the code file.
    -r                        : [Required] Runtime of the web service. Valid runtimes are python|spark-py.


## <a name="service-commands"></a>Comandi di servizio
Il servizio supporta i comandi seguenti. Per visualizzare i parametri di ogni comando, usare l'opzione -h. Ad esempio, usare `az ml service create realtime -h` per vedere i dettagli del comando create.

    create
    delete
    keys
    list
    logs
    run
    show
    update
    usage

**Creare un servizio**

Per creare un servizio con un'immagine creata in precedenza, usare il comando seguente:

`az ml service create realtime --image-id [image to deploy] -n [service name]`

Per creare un servizio, un manifesto e un'immagine con un unico comando, usare il comando seguente:

`az ml service create realtime --model-file [path to model file(s)] -f [path to model scoring file, e.g. score.py] -n [service name] -r [run time included in the image, e.g. spark-py]`

Dettagli di comandi:

    -n                                : [Required] Webservice name.
    --autoscale-enabled               : Enable automatic scaling of service replicas based on request demand.
                                        Allowed values: true, false. False if omitted.  Default: false.
    --autoscale-max-replicas          : If autoscale is enabled - sets the maximum number of replicas.
    --autoscale-min-replicas          : If autoscale is enabled - sets the minimum number of replicas.
    --autoscale-refresh-period-seconds: If autoscale is enabled - the interval of evaluating scaling demand.
    --autoscale-target-utilization    : If autoscale is enabled - target utilization of replicas time.
    --collect-model-data              : Enable model data collection. Allowed values: true, false. False if omitted.  Default: false.
    --cpu                             : Reserved number of CPU cores per service replica (can be fraction).
    --enable-app-insights -l          : Enable app insights. Allowed values: true, false. False if omitted.  Default: false.
    --memory                          : Reserved amount of memory per service replica, in M or G. (ex. 1G, 300M).
    --replica-max-concurrent-requests : Maximum number of concurrent requests that can be routed to a service replica.
    -v                                : Verbosity flag.
    -z                                : Number of replicas for a Kubernetes service.  Default: 1.

Argomenti di immagine registrati

    --image-id                        : [Required] Image to deploy to the service.

Argomenti di immagine non registrati

    --conda-file -c                   : Path to Conda Environment file.
    --image-type                      : The image type to create. Defaults to "Docker".
    --model-file -m                   : [Required] The model to be deployed.
    -d                                : Files and directories required by the service. Multiple dependencies can be specified
                                        with additional -d arguments.
    -f                                : [Required] The code file to be deployed.
    -p                                : A pip requirements.txt file of package needed by the code file.
    -r                                : [Required] Runtime of the web service. Valid runtimes are python|spark-py.
    -s                                : Input and output schema of the web service.

Argomenti globali

    --debug                           : Increase logging verbosity to show all debug logs.
    --help -h                         : Show this help message and exit.
    --output -o                       : Output format.  Allowed values: json, jsonc, table, tsv. Default: json.
    --query                           : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose                         : Increase logging verbosity. Use --debug for full debug logs.


Nota sul contrassegno `-d` per allegare dipendenze: se si passa il nome di una directory che non sia già in bundle (con estensione ZIP, TAR, e così via), tale directory viene compressa con estensione TAR e fatta passare automaticamente quindi separata dal bundle dall'altra parte. 

Se si passa una directory che è già in bundle, la consideriamo come un file e la passiamo come tale. Non viene separata dal bundle automaticamente; è previsto che l'utente la gestisca nel codice.

**Ottenere i dettagli di servizio**

Ottenere i dettagli di servizio, tra cui l'URL e l'uso (inclusi i dati di esempio, se è stato creato uno schema).

`az ml service show realtime --name [service name]`

Dettagli del comando:

    --id -i    : The service id to show.
    --name -n  : Webservice name.
    -v         : Verbosity flag.

Argomenti globali

    --debug    : Increase logging verbosity to show all debug logs.
    --help -h  : Show this help message and exit.
    --output -o: Output format.  Allowed values: json, jsonc, table, tsv.  Default: json.
    --query    : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose  : Increase logging verbosity. Use --debug for full debug logs.

**Eseguire il servizio**

`az ml service run realtime -n [service name] -d [input_data]`

Dettagli del comando:

    --id -i    : The service id to show.
    --name -n  : Webservice name.
    -v         : Verbosity flag.

Argomenti globali

    --debug    : Increase logging verbosity to show all debug logs.
    --help -h  : Show this help message and exit.
    --output -o: Output format.  Allowed values: json, jsonc, table, tsv.  Default: json.
    --query    : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose  : Increase logging verbosity. Use --debug for full debug logs.

Comando

    az ml service run realtime

Argomenti - id -i: [obbligatorio] l'ID del servizio di cui segnare il punteggio.
-d         : i dati da usare per chiamare il servizio Web.
-v: contrassegno del livello di dettaglio.

Argomenti globali

    --debug    : Increase logging verbosity to show all debug logs.
    --help -h  : Show this help message and exit.
    --output -o: Output format.  Allowed values: json, jsonc, table, tsv. Default: json.
    --query    : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose  : Increase logging verbosity. Use --debug for full debug logs.
