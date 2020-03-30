---
title: az nube di primavera
description: Gestire Azure Spring Cloud usando l'interfaccia della riga di comando di AzureManage Azure Spring Cloud using the Azure CLI
author: bmitchell287
ms.service: spring-cloud
ms.topic: reference
ms.date: 10/03/2019
ms.author: brendm
ms.openlocfilehash: 33d13d2d4fa9003ef041c4c96be83a69ac595a78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298785"
---
# <a name="az-spring-cloud"></a>az spring-cloud

## <a name="manage-azure-spring-cloud-using-the-azure-cli"></a>Gestire Azure Spring Cloud usando l'interfaccia della riga di comando di AzureManage Azure Spring Cloud using the Azure CLI

>[!Note]
> Azure Spring Cloud è attualmente disponibile in anteprima.  Questi comandi possono essere modificati o rimossi in una versione futura.

| az spring-cloud |  |
|------|------:|
| [az spring-cloud creare](#az-spring-cloud-create) | Creare un'istanza di Azure Spring Cloud.Create an Azure Spring Cloud instance. |
| [az spring-cloud delete](#az-spring-cloud-delete) | Eliminare un'istanza di Azure Spring Cloud.Delete an Azure Spring Cloud instance. |
| [az spring-cloud list](#az-spring-cloud-list) | Elencare tutte le istanze di Azure Spring Cloud nel gruppo di risorse specificato, altrimenti elencare gli ID di sottoscrizione. |
| [az primavera-cloud spettacolo](#az-spring-cloud-show) | Mostrare i dettagli per un cloud di Primavera di Azure.Show the details for an Azure Spring Cloud. |

| az spring-cloud app | Comandi per gestire le app in Azure Spring Cloud.  |
| ---- | ----: |
| [az spring-cloud app creare](#az-spring-cloud-app-create) | Creare una nuova app con una distribuzione predefinita in Azure Spring Cloud.Create a new app with a default deployment in the Azure Spring Cloud. |
| [az spring-cloud app delete](#az-spring-cloud-app-delete) | Eliminare un'app in Azure Spring Cloud.Delete an app in the Azure Spring Cloud. |
| [Az primavera-cloud app deploy](#az-spring-cloud-app-deploy) | Distribuisci dal codice sorgente o da un file binario predefinito a un'app e aggiorna le configurazioni correlate. |
| [az elenco di app a mezzastra](#az-spring-cloud-app-list) | Elencare tutte le app in Azure Spring Cloud.List all apps in the Azure Spring Cloud. |
| [az spring-cloud](#az-spring-cloud-app-restart) | Riavviare le istanze dell'app usando le impostazioni predefinite di distribuzione di produzione. |
| [az spring-cloud app scale](#az-spring-cloud-app-scale) | Ridimensionare manualmente un'app o le relative distribuzioni. |
| [az spring-cloud app set-deployment](#az-spring-cloud-app-set-deployment) | Impostare la distribuzione di produzione di un'app. |
| [Az spring-cloud app show](#az-spring-cloud-app-show) | Mostra i dettagli di un'app in Azure Spring Cloud. |
| [az spring-cloud app show-deploy-log](#az-spring-cloud-app-show-deploy-log) | Mostrare i log di compilazione per la distribuzione più recente dall'origine. Il valore predefinito è la distribuzione di produzione. |
| [Az spring-cloud](#az-spring-cloud-app-start) | Avviare le istanze dell'app usando le impostazioni predefinite di distribuzione di produzione. |
| [arresto dell'app az spring-cloud](#az-spring-cloud-app-stop) | Arrestare le istanze dell'app usando le impostazioni predefinite di distribuzione di produzione. |
| [az aggiornamento dell'app primavera-cloud](#az-spring-cloud-app-update) | Aggiornare la configurazione dell'app specificata. |

| az associazione app primavera-cloud | Comandi per gestire le associazioni con Azure Data Services.Commands to manage bindings with Azure Data Services.  L'app deve essere riavviata prima che queste impostazioni abbiano effetto. |
| --- | ---: |
| [az elenco di binding app a virgola mobile](#az-spring-cloud-app-binding-list) | Elencare tutte le associazioni di servizio in un'app. |
| [Az spring-cloud app binding rimuovere](#az-spring-cloud-app-binding-remove) | Rimuovere un'associazione al servizio dall'app. |
| [Az spring-cloud app binding show](#az-spring-cloud-app-binding-show) | Visualizzare i dettagli di un'associazione al servizio. |
| [az spring-cloud app binding cosmos aggiungere](#az-spring-cloud-app-binding-cosmos-add) | Associare un CosmosDB di Azure con l'app. |
| [az aggiornamento cosmo di associazione app primavera-cloud](#az-spring-cloud-app-binding-cosmos-update) | Aggiornare un'associazione del servizio CosmosDB di Azure.Update an Azure CosmosDB service binding. |
| [az spring-cloud app binding mysql aggiungere](#az-spring-cloud-app-binding-mysql-add) | Associare un database di Azure per MySQL con l'app. |
| [az spring-cloud app binding mysql update](#az-spring-cloud-app-binding-mysql-update) | Aggiornare un'associazione al servizio Database di Azure per MySQL.Update an Azure Database for MySQL service binding. |
| [az spring-cloud app binding redis aggiungere](#az-spring-cloud-app-binding-redis-add) | Associare una cache di Azure per Redis con l'app. |
| [az spring-cloud app binding redis update](#az-spring-cloud-app-binding-redis-update) | Aggiornare una cache di Azure per l'associazione al servizio Redis.Update an Azure Cache for Redis service binding. |

| distribuzione di app a lamilla-nuvola | Comandi per gestire il ciclo di vita della distribuzione di un'app in Azure Spring Cloud.Commands to manage the deployment life cycle of an app in Azure Spring Cloud. |
| --- | ---: |
| [az spring-cloud app deployment create](#az-spring-cloud-app-deployment-create) | Creare una distribuzione di gestione temporanea per l'app. |
| [az spring-cloud app distribuzione delete](#az-spring-cloud-app-deployment-delete) | Eliminare una distribuzione dell'app. |
| [az-spring-cloud app deployment list](#az-spring-cloud-app-deployment-list) | Elencare tutte le distribuzioni in un'app. |
| [Visualizzazione della distribuzione di app a questa vita](#az-spring-cloud-app-deployment-show) | Visualizzare i dettagli della distribuzione. |

| az spring-cloud config-server | Comandi per gestire Azure Spring Cloud Config Server. |
| --- | ---: |
| [az spring-cloud config-server chiaro](#az-spring-cloud-config-server-clear) | Cancellare tutte le impostazioni nel server di configurazione. |
| [az spring-cloud config-server set](#az-spring-cloud-config-server-set) | Definire il server di configurazione da un file YAML. |
| [az spring-cloud config-server show](#az-spring-cloud-config-server-show) | Mostra configurazione server di configurazione. |
| [az spring-cloud config server git set](#az-spring-cloud-config-server-git-set) | Definire le proprietà git per il server di configurazione.  I valori precedenti verranno sovrascritti. |
| [az spring-cloud config server git repo add](#az-spring-cloud-config-server-git-repo-add) | Aggiungere una nuova configurazione del repository git al server di configurazione. |
| [az spring-cloud config server git repo list](#az-spring-cloud-config-server-git-repo-list) | Elencare tutte le configurazioni del repository git per il server di configurazione. |
| [az spring-cloud config server git repo remove](#az-spring-cloud-config-server-git-repo-remove) | Rimuovere il repository git specificato dal server di configurazione. |

| az spring-cloud test-endpoint | Comandi per gestire il test degli endpoint in Azure Spring CloudCommands to manage endpoint testing in Azure Spring Cloud |
| --- | ---: |
| [az spring-cloud test-endpoint disable](#az-spring-cloud-test-endpoint-disable) | Disabilitare l'endpoint di test. |
| [az spring-cloud test-endpoint abilitare](#az-spring-cloud-test-endpoint-enable) | Abilitare l'endpoint di test. |
| [az spring-cloud test-endpoint elenco](#az-spring-cloud-test-endpoint-list) | Elencare le chiavi dell'endpoint di test. |
| [az spring-cloud test-endpoint renew-key](#az-spring-cloud-test-endpoint-renew-key) | Rigenerare una chiave dell'endpoint di test. |

## <a name="az-spring-cloud-create"></a>az spring-cloud creare

Creare una nuova app con una distribuzione predefinita in Azure Spring Cloud.Create a new app with a default deployment in the Azure Spring Cloud.

```azurecli
az spring-cloud create  --name -n
                        --resource-group -g
                        --location -l
                        --no-wait
```

| Parametri obbligatori | |
| --- | ---: |
| --name -n | Nome per questa istanza di Azure Spring Cloud.Name for this Azure Spring Cloud instance. |
| --resource-group -g | Specifica il gruppo di risorse per questa app.  Configurare il gruppo predefinito utilizzando`az configure --defaults group=<name>` |

| Parametri facoltativi | |
| --- | ---: |
| --location -l | Specifica il percorso del server per questa app.  Trovare posizioni valide utilizzando`az account list-locations` |
| --no-wait | Non per le operazioni a esecuzione prolungata per completare.

### <a name="examples"></a>Esempi

Creare un nuovo cloud di Azure Spring in WestUSCreate a new Azure Spring Cloud in WestUS

```azurecli
az spring-cloud create -n MyService -g MyResourceGroup -l westus
```

## <a name="az-spring-cloud-delete"></a>az spring-cloud delete

Eliminare un'istanza di Azure Spring Cloud.Delete an Azure Spring Cloud instance.

```azurecli
az spring cloud --name -n
                --resource-group -g
                --no-wait
```

| Parametri obbligatori | |
| --- | ---: |
| --name -n | Nome dell'istanza di Azure Spring Cloud da eliminare. |
| --resource-group -g | Nome del gruppo di risorse a cui appartiene Azure Spring Cloud. |

| Parametri facoltativi | |
| --- | ---: |
| -no-attesa | Non attendere il completamento delle operazioni a esecuzione prolungata. |

### <a name="example"></a>Esempio

Eliminare un'istanza di Azure Spring Cloud denominata 'MyService' da 'MyResourceGroup'.

```azurecli
az spring-cloud delete -n MyService -g MyResourceGroup
```

## <a name="az-spring-cloud-list"></a>az spring-cloud list

Elencare tutte le istanze di Azure Spring Cloud associate al gruppo di risorse specificato. Se non viene specificato alcun gruppo di risorse, elencare gli ID sottoscrizione.

```azurecli
az spring-cloud list --resource-group -g
```

| Parametri obbligatori | |
| --- | ---: |
| --resource-group -g | Nome del gruppo di risorse. |

## <a name="az-spring-cloud-show"></a>az primavera-cloud spettacolo

Visualizzare i dettagli per l'istanza di Azure Spring Cloud specificata.

```azurecli
az spring-cloud show --name -n
                     -- resource-group -g
```

| Parametri obbligatori | |
| --- | ---: |
| --name -n | Nome dell'istanza di Azure Spring Cloud. |
| --resource-group -g | Nome del gruppo di risorse a cui appartiene l'istanza di Azure Spring Cloud.

## <a name="az-spring-cloud-app-create"></a>az spring-cloud app creare

Creare una nuova app in un cloud di Azure Spring.Create a new app in an Azure Spring Cloud.

```azurecli
az spring-cloud app create --name -n
                            --resource-group -g
                            --service -s
                            --cpu
                            --enable-persistent-storage
                            --instance-count
                            --is-public
                            --memory
```

| Parametri obbligatori | |
| --- | ---: |
| --name -n | Nome dell'applicazione. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --service -s | Nome del cloud di Azure Spring.  È possibile configurare `az configure --defaults spring-cloud=<name>`il servizio predefinito utilizzando . |

| Parametri facoltativi | |
| --- | ---: |
| --cpu (informazioni in base al t | Numero di core virtuali per istanza.  Impostazione predefinita: 1. |
| --enable-persistent-storage | .  Se true, monta un disco da 50 GB con percorso predefinito. |
| --instance-count | Numero di istanza.  Impostazione predefinita: 1. |
| --is-public | .  Se true, assegna un dominio pubblico. |
| --memory | Numero di GB di memoria per istanza.  Impostazione predefinita: 1. |

### <a name="examples"></a>Esempi

Crea un'app con la configurazione predefinita.

```azurecli
az spring-cloud app create -n MyApp -s MyService
```

Crea un'app accessibile pubblicamente con 3 istanze.  Ogni istanza dispone di 3 GB di memoria e 2 core CPU.

```azurecli
az spring-cloud app create -n MyApp -s MyService --is-public true --cpu 2 --memory 3
```

## <a name="az-spring-cloud-app-delete"></a>az spring-cloud app delete

Elimina un'app nel cloud di primavera di Azure.Deletes an app in the Azure Spring Cloud.

```azurecli
az spring cloud app delete  --name -n
                            --resource-group -g
                            --service -s
```

| Parametri obbligatori | |
| --- | ---: |
| --name -n | Nome dell'applicazione. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --service -s | Nome del cloud di Azure Spring.  È possibile configurare `az configure --defaults spring-cloud=<name>`il servizio predefinito utilizzando . |

## <a name="az-spring-cloud-app-deploy"></a>Az primavera-cloud app deploy

Distribuire un'app in Azure Spring Cloud dal codice sorgente o da un file binario predefinito e aggiornare le configurazioni correlate.

```azurecli
az spring cloud app deploy  --name -n
                            --resource-group -g
                            --service -s
                            --cpu
                            --deployment -d
                            --env
                            --instance-count
                            --jar-path
                            --jvm-options
                            --memory
                            --no-wait
                            --runtime-version
                            --target-module
                            --version
```

| Parametri obbligatori | |
| --- | ---: |
| --name -n | Nome dell'applicazione. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --service -s | Nome del cloud di Azure Spring.  È possibile configurare `az configure --defaults spring-cloud=<name>`il servizio predefinito utilizzando . |

| Parametri facoltativi | |
| --- | ---: |
| --cpu (informazioni in base al t | Numero di core CPI virtuali per istanza. |
| --deployment -d | Nome di una distribuzione di app esistente.  Il valore predefinito è la distribuzione di produzione, se non specificato. |
| --env | Variabili di ambiente separate da spazi in formato 'chiave['valore]'. |
| --instance-count | Numero di istanze. |
| --jar-percorso | Se specificato, distribuire jar dal percorso specificato. In caso contrario, distribuire la cartella corrente come catrame. |
| --jvm-opzioni | Stringa contenente le opzioni JVM.  Per evitare errori di analisi della shell, utilizzare ' s ' invece di ' ' Ad esempio, `--jvm-options='-Xms1024m -Xmx2048m`. |
| --memory | Numero di GB di memoria per istanza. |
| --no-wait | Non attendere il completamento delle operazioni a esecuzione prolungata. |
| --runtime-versione | Versione runtime della lingua usata nell'app.  Valori consentiti: `Java_11`, `Java_8`. |
| --target-modulo | Modulo figlio da distribuire.  Obbligatorio quando più pacchetti jar vengono compilati dal codice sorgente. |
| --version | Versione di distribuzione.  Invariato se non impostato. |

### <a name="examples"></a>Esempi

Distribuire codice sorgente in un'app. In questo modo verrà compromessa la directory corrente, verrà creato un file binario utilizzando il servizio di compilazione Pivotal e quindi verrà distribuito all'app.

```azurecli
az spring-cloud app deploy -n MyApp -s MyService
```

Distribuisci un jar predefinito in un'app usando le opzioni JVM e le variabili di ambiente.

```azurecli
az spring-cloud app deploy -n MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseG1GC -XX:+UseStringDeduplication" --env foo=bar
```

Distribuire il codice sorgente in una distribuzione specifica di un'app.

```azurecli
az spring-cloud app deploy -n MyApp -s Myspring-cloud -d green-deployment
```

## <a name="az-spring-cloud-app-list"></a>az elenco di app a mezzastra

Elencare tutte le app nell'istanza di Azure Spring Cloud.List all apps in the Azure Spring Cloud instance.

```azurecli
az spring-cloud app list --resource-group -g
                         --service -s
```

|Parametri obbligatori | |
| --- | ---: |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --service -s | Nome del cloud di Azure Spring.  È possibile configurare `az configure --defaults spring-cloud=<name>`il servizio predefinito utilizzando . |

## <a name="az-spring-cloud-app-restart"></a>az spring-cloud

Riavviare le istanze dell'app.  Il valore predefinito è la distribuzione di produzione.

```azurecli
az spring-cloud app restart --name -n
                            --resource-group -g
                            --service -s
                            --deployment -d
                            --no-wait
```

| Parametri obbligatori | |
| --- | ---: |
| --name -n | Nome dell'applicazione. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --service -s | Nome del cloud di Azure Spring.  È possibile configurare `az configure --defaults spring-cloud=<name>`il servizio predefinito utilizzando . |

| Parametri facoltativi | |
| --- | ---: |
| --deployment -d | Nome della distribuzione esistente dell'app.  Il valore predefinito è la distribuzione di produzione, se non specificato. |
| --no-wait | Non attendere il completamento delle operazioni a esecuzione prolungata. |

## <a name="az-spring-cloud-app-scale"></a>az spring-cloud app scale

Ridimensionare manualmente un'app o le relative distribuzioni.

```azurecli
az spring-cloud app scale --name -n
                          --resource-group -g
                          --service -s
                          --cpu
                          --deployment -d
                          --instance-count
                          --memory
                          --no-wait
```

| Parametri obbligatori | |
| --- | ---: |
| --name -n | Nome dell'applicazione. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --service -s | Nome del cloud di Azure Spring.  È possibile configurare `az configure --defaults spring-cloud=<name>`il servizio predefinito utilizzando . |

| Parametri facoltativi | |
| --- | ---: |
| --cpu (informazioni in base al t | Numero di core CPU virtuali per istanza dell'app. |
| --deployment -d | Nome della distribuzione esistente dell'app.  Il valore predefinito è la distribuzione di produzione, se non specificato. |
| --instance-count | Numero di istanze di questa app. |
| --memory | Numero di GB di memoria per istanza dell'app. |
| --no-wait | Non attendere il completamento delle operazioni a esecuzione prolungata. |

### <a name="examples"></a>Esempi

Scalabilità verticale di un'app a 4 core CPU e 8 GB di memoria per istanza.

```azurecli
az spring-cloud app scale -n MyApp -s MyService --cpu 3 --memory 8
```

Scalabilità orizzontale di una distribuzione dell'app a 5 istanze.

```azurecli
az spring-cloud app scale -n MyApp -s MyService -d green-deployment --instance-count 5
```

## <a name="az-spring-cloud-app-set-deployment"></a>az spring-cloud app set-deployment

Impostare le opzioni di configurazione per la distribuzione di produzione dell'app.

```azurecli
az spring-cloud app set-deployment --deployment -d
                                   --name -n
                                   --resource-group -g
                                   --service -s
                                   --no-wait
```

| Parametri obbligatori | |
| --- | ---: |
| --deployment -d | Nome di una distribuzione esistente dell'app. |
| --name -n | Nome dell'applicazione. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --service -s | Nome del cloud di Azure Spring.  È possibile configurare `az configure --defaults spring-cloud=<name>`il servizio predefinito utilizzando . |

| Parametri facoltativi | |
| --- | ---: |
| --no-wait | Non attendere il completamento delle operazioni a esecuzione prolungata. |

### <a name="examples"></a>Esempi

Scambiare una distribuzione di gestione temporanea dell'app nell'ambiente di produzione.

```azurecli
az spring-cloud app set-deployment -d green-deployment -n MyApp -s MyService
```

## <a name="az-spring-cloud-app-show"></a>Az spring-cloud app show

Mostra i dettagli di un'app in Azure Spring Cloud.

```azurecli
az spring-cloud app show --name -n
                         --resource-group -g
                         --service -s
```

| Parametri obbligatori | |
| --- | ---: |
| --name -n | Nome dell'applicazione. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --service -s | Nome del cloud di Azure Spring.  È possibile configurare `az configure --defaults spring-cloud=<name>`il servizio predefinito utilizzando . |

## <a name="az-spring-cloud-app-show-deploy-log"></a>az spring-cloud app show-deploy-log

Visualizzare il log di compilazione dell'ultima distribuzione dal codice sorgente.  Il valore predefinito è l'ambiente di produzione.

```azurecli
az spring-cloud app show-deploy-log --name -n
                                    --resource-group -g
                                    --service -s
                                    --deployment -d
```

| Parametri obbligatori | |
| --- | ---: |
| --name -n | Nome dell'applicazione. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --service -s | Nome del cloud di Azure Spring.  È possibile configurare `az configure --defaults spring-cloud=<name>`il servizio predefinito utilizzando . |

| Parametri facoltativi | |
| --- | ---: |
| --deployment -d | Nome di una distribuzione esistente dell'app.  Il valore predefinito è l'ambiente di produzione. |

## <a name="az-spring-cloud-app-start"></a>Az spring-cloud

Avvia le istanze dell'app.  Il valore predefinito è l'ambiente di produzione.

```azurecli
az spring-cloud app start --name -n
                          --resource-group -g
                          --service -s
                          --deployment -d
                          --no-wait
```

| Parametri obbligatori | |
| --- | ---: |
| --name -n | Nome dell'applicazione. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --service -s | Nome del cloud di Azure Spring.  È possibile configurare `az configure --defaults spring-cloud=<name>`il servizio predefinito utilizzando . |

| Parametri facoltativi | |
| --- | ---: |
| --deployment -d | Nome di una distribuzione esistente dell'app.  Il valore predefinito è l'ambiente di produzione. |
| --no-wait | Non attendere il completamento delle operazioni a esecuzione prolungata. |

## <a name="az-spring-cloud-app-stop"></a>arresto dell'app az spring-cloud

Interrompere le istanze dell'app.  Il valore predefinito è l'ambiente di produzione.

```azurecli
az spring-cloud app stop --name -n
                         --resource-group -g
                         --service -s
                         --deployment -d
                         --no-wait
```

| Parametri obbligatori | |
| --- | ---: |
| --name -n | Nome dell'applicazione. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --service -s | Nome del cloud di Azure Spring.  È possibile configurare `az configure --defaults spring-cloud=<name>`il servizio predefinito utilizzando . |

| Parametri facoltativi | |
| --- | ---: |
| --deployment -d | Nome di una distribuzione esistente dell'app.  Il valore predefinito è l'ambiente di produzione. |
| --no-wait | Non attendere il completamento delle operazioni a esecuzione prolungata. |

## <a name="az-spring-cloud-app-update"></a>az aggiornamento dell'app primavera-cloud

Aggiornare la configurazione archiviata di un'app.

```azurecli
az spring-cloud app update --name -n
                           --resource-group -g
                           --service -s
                           --deployment -d
                           --enable-persistent-storage
                           --env
                           --is-public
                           --jvm-options
                           --no-wait
                           --runtime-version
```

| Parametri obbligatori | |
| --- | ---: |
| --name -n | Nome dell'applicazione. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --service -s | Nome del cloud di Azure Spring.  È possibile configurare `az configure --defaults spring-cloud=<name>`il servizio predefinito utilizzando . |

| Parametri facoltativi | |
| --- | ---: |
| --deployment -d | Nome di una distribuzione esistente dell'app.  Il valore predefinito è l'ambiente di produzione. |
| --enable-persistent-storage | Proprietà di tipo Boolean.  Se true, montare un disco da 50 GB con il percorso predefinito. |
| --env | Variabili di ambiente separate da spazi in formato 'chiave['valore]'. |
| --is-public | Proprietà di tipo Boolean.  Se true, assegna un dominio pubblico all'app. |
| --jvm-opzioni | Stringa contenente le opzioni JVM.  Per evitare errori di analisi della shell, utilizzare ' s ' invece di ' ' Ad esempio, `--jvm-options='-Xms1024m -Xmx2048m`. |
| --no-wait | Non attendere il completamento delle operazioni a esecuzione prolungata. |
| --runtime-versione | Versione runtime della lingua usata nell'app.  Valori consentiti: `Java_11`, `Java_8`. |

### <a name="example"></a>Esempio

Aggiungere una variabile di ambiente per l'app.

```azurecli
az spring-cloud app update --env foo=bar
```

## <a name="az-spring-cloud-app-binding-list"></a>az elenco di binding app a virgola mobile

Elencare tutte le associazioni di servizio in un'app.

```azurecli
az spring-cloud app binding list --app
                                 --resource-group -g
                                 --service -s
```

| Parametri obbligatori | |
| --- | ---: |
| --app | Nome dell'applicazione. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --service -s | Nome del cloud di Azure Spring.  È possibile configurare `az configure --defaults spring-cloud=<name>`il servizio predefinito utilizzando . |

## <a name="az-spring-cloud-app-binding-remove"></a>Az spring-cloud app binding rimuovere

Rimuovere un'associazione al servizio dall'app.

```azurecli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Parametri obbligatori | |
| --- | ---: |
| --app | Nome dell'applicazione. |
| --name | Nome dell'associazione al servizio da rimuovere. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --service -s | Nome del cloud di Azure Spring.  È possibile configurare `az configure --defaults spring-cloud=<name>`il servizio predefinito utilizzando . |

## <a name="az-spring-cloud-app-binding-show"></a>Az spring-cloud app binding show

Visualizzare i dettagli di un'associazione al servizio.

```azurecli
az spring-cloud app binding show --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Parametri obbligatori | |
| --- | ---: |
| --app | Nome dell'applicazione. |
| --name | Nome dell'associazione al servizio. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --service -s | Nome del cloud di Azure Spring.  È possibile configurare `az configure --defaults spring-cloud=<name>`il servizio predefinito utilizzando . |

## <a name="az-spring-cloud-app-binding-cosmos-add"></a>az spring-cloud app binding cosmos aggiungere

Associare un database Cosmos di Azure con l'app.

```azurecli
az spring-cloud app binding cosmos add --api-type
                                       --app
                                       --name -n
                                       --resource-group -g
                                       --resource-id
                                       --service -s
                                       --collection-name
                                       --database-name
                                       --key-space
```

```azurecli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Parametri obbligatori | |
| --- | ---: |
| --api-tipo | Specificare il tipo di API utilizzando uno dei valori seguenti: cassandra, gremlin, mongo, sql, table. |
| --app | Nome dell'applicazione. |
| --name | Nome dell'associazione al servizio. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --service -s | Nome del cloud di Azure Spring.  È possibile configurare `az configure --defaults spring-cloud=<name>`il servizio predefinito utilizzando . |

|Parametri facoltativi | |
| --- | ---: |
| --collection-name (nome-raccolta) | Nome della raccolta.  Obbligatorio quando si utilizza Gremlin. |
| --nome-database | Nome del database.  Obbligatorio quando si usano Mongo, SQL e Gremlin. |
| --key-space (spazio-chiave) | Cassandra key-space.  Obbligatorio quando si utilizza Cassandra. |

## <a name="az-spring-cloud-app-binding-cosmos-update"></a>az aggiornamento cosmo di associazione app primavera-cloud

```azurecli
az spring-cloud app binding cosmos update --app
                                          --name -n
                                          --resource-group -g
                                          --service -s
                                          --collection-name
                                          --database-name
                                          --key-space
```

| Parametri obbligatori | |
| --- | ---: |
| --app | Nome dell'applicazione. |
| --name | Nome dell'associazione al servizio. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --service -s | Nome del cloud di Azure Spring.  È possibile configurare `az configure --defaults spring-cloud=<name>`il servizio predefinito utilizzando . |

|Parametri facoltativi | |
| --- | ---: |
| --collection-name (nome-raccolta) | Nome della raccolta.  Obbligatorio quando si utilizza Gremlin. |
| --nome-database | Nome del database.  Obbligatorio quando si usano Mongo, SQL e Gremlin. |
| --key-space (spazio-chiave) | Cassandra key-space.  Obbligatorio quando si utilizza Cassandra. |

## <a name="az-spring-cloud-app-binding-mysql-add"></a>az spring-cloud app binding mysql aggiungere

```azurecli
az spring-cloud app binding mysql add --app
                                      --database-name
                                      --key
                                      --name -n
                                      --resource-group -g
                                      --service -s
                                      --username
```

| Parametri obbligatori | |
| --- | ---: |
| --app | Nome dell'applicazione. |
| --nome-database | Nome del database. |
| --key | chiave API del servizio. |
| --name | Nome dell'associazione al servizio. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --resource-id (id-risorsa) | ID risorsa di Azure del servizio con cui eseguire l'associazione. |
| --service -s | Nome del cloud di Azure Spring.  È possibile configurare `az configure --defaults spring-cloud=<name>`il servizio predefinito utilizzando . |
| --username | Nome utente per l'accesso al database. |

## <a name="az-spring-cloud-app-binding-mysql-update"></a>az spring-cloud app binding mysql update

Aggiornare la connessione di associazione del servizio dell'app a un database di Azure per MySQL.Update the service binding connection the app to an Azure Database for MySQL.

```azurecli
az spring-cloud app binding mysql update --add
                                         --name -n
                                         --resource-group -g
                                         --service -s
                                         --database-name
                                         --key
                                         --username
```

| Parametri obbligatori | |
| --- | ---: |
| --app | Nome dell'applicazione. |
| --name | Nome dell'associazione al servizio. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --service -s | Nome del cloud di Azure Spring.  È possibile configurare `az configure --defaults spring-cloud=<name>`il servizio predefinito utilizzando . |

| Parametri facoltativi | |
| --- | ---: |
| --nome-database | Nome del database. |
| --key | chiave API del servizio. |
| --username | Nome utente per l'accesso al database. |

## <a name="az-spring-cloud-app-binding-redis-add"></a>az spring-cloud app binding redis aggiungere

Associare una cache di Azure per Redis con l'app.

```azurecli
az spring-cloud app binding redis add --app
                                      --name -n
                                      --resource-group -g
                                      --resource-id
                                      --service -s
                                      --disable-ssl
```

| Parametri obbligatori | |
| --- | ---: |
| --app | Nome dell'applicazione. |
| --name | Nome dell'associazione al servizio. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --resource-id (id-risorsa) | ID risorsa di Azure del servizio con cui si vuole eseguire l'associazione. |
| --service -s | Nome del cloud di Azure Spring.  È possibile configurare `az configure --defaults spring-cloud=<name>`il servizio predefinito utilizzando . |

| Parametri facoltativi | |
| --- | ---: |
| --disable-ssl | Disabilitare TLS. |

## <a name="az-spring-cloud-app-binding-redis-update"></a>az spring-cloud app binding redis update

Aggiornare un'associazione di servizio per la cache di Azure per Redis.Update a service binding for Azure Cache for Redis.

| Parametri obbligatori | |
| --- | ---: |
| --app | Nome dell'applicazione. |
| --name | Nome dell'associazione al servizio. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --service -s | Nome del cloud di Azure Spring.  È possibile configurare `az configure --defaults spring-cloud=<name>`il servizio predefinito utilizzando . |

| Parametri facoltativi | |
| --- | ---: |
| --disable-ssl | Disabilitare TLS. |

## <a name="az-spring-cloud-app-deployment-create"></a>az spring-cloud app deployment create

Creare una distribuzione di gestione temporanea per l'app.

Per distribuire codice o aggiornare le `az spring-cloud app deploy --deployment <staging-deployment>` impostazioni a una distribuzione esistente, <staging deployment>usare o 'az aggiornamento dell'app spring-cloud --deployment .

| Parametri obbligatori | |
| --- | ---: |
| --app | Nome dell'applicazione. |
| --name | Nome dell'associazione al servizio. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --service -s | Nome del cloud di Azure Spring.  È possibile configurare `az configure --defaults spring-cloud=<name>`il servizio predefinito utilizzando . |

| Parametri facoltativi | |
| --- | ---: |
| --cpu (informazioni in base al t | Numero di core CPU virtuali per istanza.  Predefinito: 1 |
| --env | Variabili di ambiente separate da spazi in formato 'chiave['valore]'. |
| --instance-count | Numero di istanze. Impostazione predefinita: 1. |
| --jar-percorso | Se specificato, distribuire jar.  In caso contrario, distribuire la cartella corrente come catrame. |
| --jvm-opzioni | Stringa contenente le opzioni JVM.  Per evitare errori di analisi della shell, utilizzare ' s ' invece di ' ' Ad esempio, `--jvm-options='-Xms1024m -Xmx2048m`. |
| --memory | Numero di GB di memoria per istanza. |
| --no-wait | Non attendere il completamento delle operazioni a esecuzione prolungata. |
| --runtime-versione | Versione runtime della lingua usata nell'app.  Valori consentiti: `Java_11`, `Java_8`. |
| --skip-clone-impostazioni | Creare una distribuzione di gestione temporanea clonando le impostazioni di distribuzione di produzione correnti. |
| --target-modulo | Modulo figlio da distribuire.  Obbligatorio quando più pacchetti jar vengono compilati dal codice sorgente. |
| --version | Versione di distribuzione.  Invariato se non impostato. |

### <a name="examples"></a>Esempi

Distribuire il codice sorgente in una nuova distribuzione dell'app.  In questo modo verrà compromessa la directory corrente, verrà creato un file binario utilizzando il sistema di compilazione Pivotal e quindi verrà distribuito.

```azurecli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService
```

Distribuisci un jar predefinito in un'app con opzioni JVM e variabili di ambiente.

```azurecli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseStringDeDuplication" --env foo=bar
```

## <a name="az-spring-cloud-app-deployment-delete"></a>az spring-cloud app distribuzione delete

Eliminare una distribuzione dell'app.

```azurecli
az spring-cloud app deployment delete --app
                                      --name -n
                                      --resource-group -g
                                      --service -s
```

| Parametri obbligatori | |
| --- | ---: |
| --app | Nome dell'applicazione. |
| --name | Nome della distribuzione. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --service -s | Nome del cloud di Azure Spring.  È possibile configurare `az configure --defaults spring-cloud=<name>`il servizio predefinito utilizzando . |

## <a name="az-spring-cloud-app-deployment-list"></a>az-spring-cloud app deployment list

Elencare tutte le distribuzioni in un'app.

```azurecli
az spring-cloud app deployment list --app
                                    --resource-group -g
                                    --service -s
```

| Parametri obbligatori | |
| --- | ---: |
| --app | Nome dell'applicazione. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --service -s | Nome del cloud di Azure Spring.  È possibile configurare `az configure --defaults spring-cloud=<name>`il servizio predefinito utilizzando . |

## <a name="az-spring-cloud-app-deployment-show"></a>Visualizzazione della distribuzione di app a questa vita

Visualizzare i dettagli di una distribuzione.

```azurecli
az spring-cloud app deployment show --app
                                    --name -n
                                    --resource-group -g
                                    --service -s
```

| Parametri obbligatori | |
| --- | ---: |
| --app | Nome dell'applicazione. |
| --name | Nome della distribuzione. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --service -s | Nome del cloud di Azure Spring.  È possibile configurare `az configure --defaults spring-cloud=<name>`il servizio predefinito utilizzando . |

## <a name="az-spring-cloud-config-server-clear"></a>az spring-cloud config-server chiaro

Cancellare tutte le impostazioni di configurazione nel server di configurazione.

```azurecli
az spring-cloud config-server clear --name
                                        --resource-group -g
```

| Parametri obbligatori | |
| --- | ---: |
| --name | Nome del cloud di Azure Spring. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |

## <a name="az-spring-cloud-config-server-set"></a>az spring-cloud config-server set

Configurare le impostazioni di configurazione nel server di configurazione utilizzando un file YAML.

```azurecli
az spring-cloud config-server set --config-file
                                  --name -n
                                  --resource-group -g
                                  --no-wait
```

| Parametri obbligatori | |
| --- | ---: |
| --config-file | Percorso del file a un manifesto YAML per la configurazione del server di configurazione. |
| --name | Nome del cloud di Azure Spring. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |

| Parametri facoltativi | |
| --- | ---: |
| --no-wait | Non per le operazioni a esecuzione prolungata per completare.

## <a name="az-spring-cloud-config-server-show"></a>az spring-cloud config-server show

Visualizzare le impostazioni del server di configurazione.

```azurecli
az spring-cloud config-server show --name -n
                                   --resource-group -g
```

| Parametri obbligatori | |
| --- | ---: |
| --name | Nome del cloud di Azure Spring. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |

## <a name="az-spring-cloud-config-server-git-set"></a>az spring-cloud config-server git set

Impostare le proprietà git per il server di configurazione.  Questo sovrascriverà tutte le proprietà git esistenti.

```azurecli
az spring-cloud config-server git set --name -n
                                      --resource-group g
                                      --uri
                                      --defer
                                      --host-key
                                      --host-key-algorithm
                                      --label
                                      --password
                                      --private-key
                                      --search-paths
                                      --strict-host-key-checking
                                      --username
```

| Parametri obbligatori | |
| --- | ---: |
| --name | Nome del cloud di Azure Spring. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --uri (informazioni in base al nome | URI della configurazione aggiunta. |

| Parametri facoltativi | |
| --- | ---: |
| --defer | Archiviare temporaneamente l'oggetto nella cache locale anziché inviarlo ad Azure.  Utilizzare `az cache` per visualizzare / cancellare. |
| --host-key (chiave host) | Chiave host per la configurazione aggiunta. |
| --host-key-algorithm | Algoritmo della chiave host per la configurazione aggiunta. |
| --label (etichetta) | Etichetta della configurazione aggiunta. |
| --password | Password della configurazione aggiunta. |
| --chiave privata | Chiave privata della configurazione aggiunta. |
| --percorsi di ricerca | Percorsi di ricerca della configurazione aggiunta.  Utilizzare delimitatori di virgola per più percorsi. |
| --strict-host-key-checking | Abilita il controllo rigoroso della chiave host della configurazione aggiunta. |
| --username | Nome utente della configurazione aggiunta. |

## <a name="az-spring-cloud-config-server-git-repo-add"></a>az spring-cloud config-server git repo add

```azurecli
az spring-cloud config-server git repo add --name -n
                                           --repo-name
                                           --resource-group -g
                                           --uri
                                           --defer
                                           --host-key
                                           --host-key-algorithm
                                           --label
                                           --password
                                           --pattern
                                           --private-key
                                           --search-paths
                                           --strict-host-key-checking
                                           --username
```

| Parametri obbligatori | |
| --- | ---: |
| --name | Nome del cloud di Azure Spring. |
| --repo-name (nome repository) | URI del repository. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --uri (informazioni in base al nome | URI della configurazione aggiunta. |

| Parametri facoltativi | |
| --- | ---: |
| --defer | Archiviare temporaneamente l'oggetto nella cache locale anziché inviarlo ad Azure.  Utilizzare `az cache` per visualizzare / cancellare. |
| --host-key (chiave host) | Chiave host per la configurazione aggiunta. |
| --host-key-algorithm | Algoritmo della chiave host per la configurazione aggiunta. |
| --label (etichetta) | Etichetta della configurazione aggiunta. |
| --password | Password della configurazione aggiunta. |
| --modello | Modello per il repository.  Utilizzare delimitatori di virgola per più percorsi.|
| --chiave privata | Chiave privata della configurazione aggiunta. |
| --percorsi di ricerca | Percorsi di ricerca della configurazione aggiunta.  Utilizzare delimitatori di virgola per più percorsi. |
| --strict-host-key-checking | Abilita il controllo rigoroso della chiave host della configurazione aggiunta. |
| --username | Nome utente della configurazione aggiunta. |

## <a name="az-spring-cloud-config-server-git-repo-list"></a>az spring-cloud config-server git repo list

Elencare tutti i repository git definiti nel server di configurazioneList all git repos defined in the Config Server

```azurecli
az spring-cloud config-server git repo list --name -n
                                       --resource-group -g
                                       --defer
```

| Parametri obbligatori | |
| --- | ---: |
| --name | Nome del cloud di Azure Spring. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |

| Parametri facoltativi | |
| --- | ---: |
| --defer | Archiviare temporaneamente l'oggetto nella cache locale anziché inviarlo ad Azure.  Utilizzare `az cache` per visualizzare / cancellare. |

## <a name="az-spring-cloud-config-server-git-repo-remove"></a>az primavera-cloud config-server git repo remove

Rimuovere una configurazione di archivio git esistente dal server di configurazione.

```azurecli
az spring-cloud config-server git repo remove --name -n
                                         --repo-name
                                         --resource-group -g
                                         --defer
```

| Parametri obbligatori | |
| --- | ---: |
| --name | Nome del cloud di Azure Spring. |
| --repo-name (nome repository) | URI del repository. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |

| Parametri facoltativi | |
| --- | ---: |
| --defer | Archiviare temporaneamente l'oggetto nella cache locale anziché inviarlo ad Azure.  Utilizzare `az cache` per visualizzare / cancellare. |

## <a name="az-spring-cloud-test-endpoint-disable"></a>az spring-cloud test-endpoint disable

Disabilitare l'endpoint di test di Azure Spring CloudDisable test endpoint of the Azure Spring Cloud

```azurecli
az spring-cloud test-endpoint disable --name -n
                                      --resource-group -g
```

| Parametri obbligatori | |
| --- | ---: |
| --name | Nome del cloud di Azure Spring. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |

## <a name="az-spring-cloud-test-endpoint-enable"></a>az spring-cloud test-endpoint abilitare

Abilitare l'endpoint di test per Azure Spring Cloud.Enable test endpoint for the Azure Spring Cloud. 

```azurecli
az spring-cloud test-endpoint enable --name -n
                                     --resource-group -g
```

| Parametri obbligatori | |
| --- | ---: |
| --name | Nome del cloud di Azure Spring. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |

## <a name="az-spring-cloud-test-endpoint-list"></a>az spring-cloud test-endpoint elenco 

Elencare le chiavi dell'endpoint di test disponibili per Azure Spring Cloud.List the available test endpoint keys for the Azure Spring Cloud.

```azurecli
az spring-cloud test-endpoint list --name -n
                                   --resource-group -g
                                   --app
                                   --deployment -d
```

| Parametri obbligatori | |
| --- | ---: |
| --name | Nome del cloud di Azure Spring. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |

| Parametri facoltativi | |
| --- | ---: |
| --app | Nome dell'applicazione. |
| --deployment -d | Nome di una distribuzione esistente dell'app.  Il valore predefinito è production se non specificato. |

## <a name="az-spring-cloud-test-endpoint-renew-key"></a>az spring-cloud test-endpoint renew-key

Rigenerare una chiave dell'endpoint di test per Azure Spring Cloud.Regenerate a test-endpoint key for the Azure Spring Cloud.

```azurecli
az spring-cloud test-endpoint renew-key --name -n
                                        --resource-group -g
                                        --type
```

| Parametri obbligatori | |
| --- | ---: |
| --name | Nome del cloud di Azure Spring. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --tipo | Tipo di chiave dell'endpoint di test.  Valori consentiti: Primario, Secondario. |
