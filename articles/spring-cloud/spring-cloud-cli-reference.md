---
title: AZ Spring cloud
description: Gestire Azure Spring cloud con l'interfaccia della riga di comando di Azure
author: jpconnock
ms.service: spring-cloud
ms.topic: reference
ms.date: 10/03/2019
ms.author: jeconnoc
ms.openlocfilehash: a1f82d4efa7756b44ca5ed9859aa872c1f55b565
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607834"
---
# <a name="az-spring-cloud"></a>AZ Spring-cloud

## <a name="manage-azure-spring-cloud-using-the-azure-cli"></a>Gestire Azure Spring cloud con l'interfaccia della riga di comando di Azure

>[!Note]
> Azure Spring cloud è attualmente disponibile in versione di anteprima.  Questi comandi possono essere modificati o rimossi in una versione futura.

| AZ Spring-cloud |  |
|------|------:|
| [AZ Spring-cloud create](#az-spring-cloud-create) | Creare un'istanza di Azure Spring cloud. |
| [AZ Spring-cloud Delete](#az-spring-cloud-delete) | Eliminare un'istanza di Azure Spring cloud. |
| [AZ Spring-cloud List](#az-spring-cloud-list) | Elencare tutte le istanze di Azure Spring cloud nel gruppo di risorse specificato. in caso contrario, elencare gli ID sottoscrizione. |
| [AZ Spring-cloud Show](#az-spring-cloud-show) | Mostra i dettagli per un cloud Spring di Azure. |

| AZ Spring-cloud app | Comandi per la gestione delle app nel cloud Spring di Azure.  |
| ---- | ----: |
| [AZ Spring-cloud app create](#az-spring-cloud-app-create) | Creare una nuova app con una distribuzione predefinita nel cloud Spring di Azure. |
| [AZ Spring-cloud app Delete](#az-spring-cloud-app-delete) | Eliminare un'app nel cloud Spring di Azure. |
| [AZ Spring-cloud app deploy](#az-spring-cloud-app-deploy) | Eseguire la distribuzione dal codice sorgente o da un file binario predefinito a un'app e aggiornare le configurazioni correlate. |
| [AZ Spring-cloud app list](#az-spring-cloud-app-list) | Elenca tutte le app nel cloud Spring di Azure. |
| [AZ Spring-cloud app restart](#az-spring-cloud-app-restart) | Riavviare le istanze dell'app usando le impostazioni predefinite della distribuzione di produzione. |
| [AZ Spring-cloud app scale](#az-spring-cloud-app-scale) | Ridimensionare manualmente un'app o le relative distribuzioni. |
| [AZ Spring-cloud app Set-Deployment](#az-spring-cloud-app-set-deployment) | Imposta la distribuzione di produzione di un'app. |
| [AZ Spring-cloud app Show](#az-spring-cloud-app-show) | Mostra i dettagli di un'app nel cloud Spring di Azure. |
| [AZ Spring-cloud app Show-deploy-log](#az-spring-cloud-app-show-deploy-log) | Mostra i log di compilazione per la distribuzione più recente dall'origine. Il valore predefinito è distribuzione di produzione. |
| [AZ Spring-cloud app Start](#az-spring-cloud-app-start) | Avviare le istanze dell'app usando le impostazioni predefinite della distribuzione di produzione. |
| [AZ Spring-cloud app stop](#az-spring-cloud-app-stop) | Arrestare le istanze dell'app usando le impostazioni predefinite della distribuzione di produzione. |
| [AZ Spring-cloud app Update](#az-spring-cloud-app-update) | Aggiorna la configurazione dell'app specificata. |

| AZ Spring-cloud app binding | Comandi per gestire le associazioni con i servizi dati di Azure.  Per rendere effettive le impostazioni, l'app deve essere riavviata. |
| --- | ---: |
| [AZ Spring-cloud app binding List](#az-spring-cloud-app-binding-list) | Elencare tutte le associazioni al servizio in un'app. |
| [AZ Spring-cloud app binding Rimuovi](#az-spring-cloud-app-binding-remove) | Rimuovere un'associazione al servizio dall'app. |
| [AZ Spring-cloud app binding Show](#az-spring-cloud-app-binding-show) | Mostra i dettagli di un'associazione al servizio. |
| [AZ Spring-cloud app binding Cosmos Add](#az-spring-cloud-app-binding-cosmos-add) | Associare una CosmosDB di Azure all'app. |
| [AZ Spring-cloud app binding Cosmos Update](#az-spring-cloud-app-binding-cosmos-update) | Aggiornare un'associazione al servizio Azure CosmosDB. |
| [AZ Spring-cloud app binding MySQL Add](#az-spring-cloud-app-binding-mysql-add) | Associare un database di Azure per MySQL con l'app. |
| [AZ Spring-cloud app binding MySQL Update](#az-spring-cloud-app-binding-mysql-update) | Aggiornare un'associazione al servizio database di Azure per MySQL. |
| [AZ Spring-cloud app binding Redis Add](#az-spring-cloud-app-binding-redis-add) | Associare una cache di Azure per Redis con l'app. |
| [AZ Spring-cloud app binding Redis Update](#az-spring-cloud-app-binding-redis-update) | Aggiornare una cache di Azure per l'associazione del servizio Redis. |

| AZ Spring-cloud App Deployment | Comandi per gestire il ciclo di vita della distribuzione di un'app nel cloud Spring di Azure. |
| --- | ---: |
| [AZ Spring-cloud App Deployment crea](#az-spring-cloud-app-deployment-create) | Creare una distribuzione di staging per l'app. |
| [AZ Spring-cloud App Deployment Delete](#az-spring-cloud-app-deployment-delete) | Eliminare una distribuzione dell'app. |
| [AZ Spring-cloud App Deployment List](#az-spring-cloud-app-deployment-list) | Elencare tutte le distribuzioni in un'app. |
| [AZ Spring-cloud App Deployment Show](#az-spring-cloud-app-deployment-show) | Mostra i dettagli della distribuzione. |

| AZ Spring-cloud config-server | Comandi per gestire il server di configurazione cloud Spring di Azure. |
| --- | ---: |
| [AZ Spring-cloud config-server Clear](#az-spring-cloud-config-server-clear) | Cancellare tutte le impostazioni nel server di configurazione. |
| [AZ Spring-cloud config-server set](#az-spring-cloud-config-server-set) | Definire il server di configurazione da un file YAML. |
| [AZ Spring-cloud config-server show](#az-spring-cloud-config-server-show) | Mostra la configurazione del server di configurazione. |
| [AZ Spring-cloud config server Git set](#az-spring-cloud-config-server-git-set) | Definire le proprietà git per il server di configurazione.  I valori precedenti verranno sovrascritti. |
| [AZ Spring-cloud config server Git repository Add](#az-spring-cloud-config-server-git-repo-add) | Aggiungere una nuova configurazione del repository git al server di configurazione. |
| [AZ Spring-cloud config server Git repository list](#az-spring-cloud-config-server-git-repo-list) | Elencare tutte le configurazioni del repository Git per il server di configurazione. |
| [AZ Spring-cloud config server Git repository Remove](#az-spring-cloud-config-server-git-repo-remove) | Rimuovere il repository git specificato dal server di configurazione. |

| AZ Spring-cloud test-endpoint | Comandi per gestire i test degli endpoint nel cloud Spring di Azure |
| --- | ---: |
| [AZ Spring-cloud test-endpoint Disable](#az-spring-cloud-test-endpoint-disable) | Disabilitare l'endpoint di test. |
| [AZ Spring-cloud test-endpoint Enable](#az-spring-cloud-test-endpoint-enable) | Abilitare l'endpoint di test. |
| [AZ Spring-cloud test-endpoint List](#az-spring-cloud-test-endpoint-list) | Elencare le chiavi dell'endpoint di test. |
| [AZ Spring-cloud test-endpoint Renew-Key](#az-spring-cloud-test-endpoint-renew-key) | Rigenerare una chiave test-endpoint. |

## <a name="az-spring-cloud-create"></a>AZ Spring-cloud create

Creare una nuova app con una distribuzione predefinita nel cloud Spring di Azure.

```cli
az spring-cloud create  --name -n
                        --resource-group -g
                        --location -l
                        --no-wait
```

| Parametri obbligatori | |
| --- | ---: |
| --Name-n | Nome per questa istanza di Azure Spring cloud. |
| --resource-group -g | Specifica il gruppo di risorse per l'app.  Configurare il gruppo predefinito usando `az configure --defaults group=<name>` |

| Parametri facoltativi | |
| --- | ---: |
| --location -l | Specifica il percorso del server per l'app.  Trovare percorsi validi con `az account list-locations` |
| --no-wait | Non per il completamento delle operazioni a esecuzione prolungata.

### <a name="examples"></a>Esempi

Crea un nuovo Cloud Spring di Azure in Westus

```cli
az spring-cloud create -n MyService -g MyResourceGroup -l westus
```

## <a name="az-spring-cloud-delete"></a>AZ Spring-cloud Delete

Eliminare un'istanza di Azure Spring cloud.

```cli
az spring cloud --name -n
                --resource-group -g
                --no-wait
```

| Parametri obbligatori | |
| --- | ---: |
| --Name-n | Nome dell'istanza del cloud Spring di Azure da eliminare. |
| --resource-group -g | Nome del gruppo di risorse a cui appartiene il cloud Spring di Azure. |

| Parametri facoltativi | |
| --- | ---: |
| -No-Wait | Non attendere il completamento delle operazioni a esecuzione prolungata. |

### <a name="example"></a>Esempio

Eliminare un'istanza di Azure Spring cloud denominata ' servizio ' da' MyResourceGroup '.

```cli
az spring-cloud delete -n MyService -g MyResourceGroup
```

## <a name="az-spring-cloud-list"></a>AZ Spring-cloud List

Elenca tutte le istanze di Azure Spring cloud associate al gruppo di risorse specificato. Se non viene specificato alcun gruppo di risorse, elencare gli ID sottoscrizione.

```cli
az spring-cloud list --resource-group -g
```

| Parametri obbligatori | |
| --- | ---: |
| --resource-group -g | Nome del gruppo di risorse. |

## <a name="az-spring-cloud-show"></a>AZ Spring-cloud Show

Mostra i dettagli per l'istanza di Azure Spring cloud specificata.

```cli
az spring-cloud show --name -n
                     -- resource-group -g
```

| Parametri obbligatori | |
| --- | ---: |
| --Name-n | Nome dell'istanza di Azure Spring cloud. |
| --resource-group -g | Nome del gruppo di risorse a cui appartiene l'istanza di Azure Spring cloud.

## <a name="az-spring-cloud-app-create"></a>AZ Spring-cloud app create

Crea una nuova app in un cloud Spring di Azure.

```cli
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
| --Name-n | Nome dell'app. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --Service-s | Nome del cloud Spring di Azure.  È possibile configurare il servizio predefinito utilizzando `az configure --defaults spring-cloud=<name>`. |

| Parametri facoltativi | |
| --- | ---: |
| --CPU | Numero di core virtuali per istanza.  Impostazione predefinita: 1. |
| --Enable-persistent-storage | Valore booleano.  Se true, monta un disco da 50 GB con il percorso predefinito. |
| --instance-count | Numero di istanze.  Impostazione predefinita: 1. |
| --is-public | Valore booleano.  Se true, assegna un dominio pubblico. |
| -memoria | Numero di GB di memoria per ogni istanza.  Impostazione predefinita: 1. |

### <a name="examples"></a>Esempi

Creare un'app con la configurazione predefinita.

```cli
az spring-cloud app create -n MyApp -s MyService
```

Creare un'app accessibile pubblicamente con 3 istanze.  Ogni istanza ha 3 GB di memoria e 2 core CPU.

```cli
az spring-cloud app create -n MyApp -s MyService --is-public true --cpu 2 --memory 3
```

## <a name="az-spring-cloud-app-delete"></a>AZ Spring-cloud app Delete

Elimina un'app nel cloud Spring di Azure.

```cli
az spring cloud app delete  --name -n
                            --resource-group -g
                            --service -s
```

| Parametri obbligatori | |
| --- | ---: |
| --Name-n | Nome dell'app. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --Service-s | Nome del cloud Spring di Azure.  È possibile configurare il servizio predefinito utilizzando `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-deploy"></a>AZ Spring-cloud app deploy

Distribuire un'app nel cloud Spring di Azure dal codice sorgente o da un file binario predefinito e aggiornare le configurazioni correlate.

```cli
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
| --Name-n | Nome dell'app. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --Service-s | Nome del cloud Spring di Azure.  È possibile configurare il servizio predefinito utilizzando `az configure --defaults spring-cloud=<name>`. |

| Parametri facoltativi | |
| --- | ---: |
| --CPU | Numero di Core CPI virtuali per istanza. |
| --Deployment-d | Nome di una distribuzione di app esistente.  Se non è specificato, il valore predefinito è la distribuzione di produzione. |
| --ENV | Variabili di ambiente separate da spazi nel formato ' chiave [= valore]'. |
| --instance-count | Numero di istanze. |
| --jar-Path | Se specificato, distribuire jar dal percorso specificato. In caso contrario, distribuire la cartella corrente come tar. |
| --JVM-Options | Stringa contenente le opzioni JVM.  Usare ' =' invece di '' per evitare errori di analisi della shell. Ad esempio, `--jvm-options='-Xms1024m -Xmx2048m`. |
| -memoria | Numero di GB di memoria per ogni istanza. |
| --no-wait | Non attendere il completamento delle operazioni a esecuzione prolungata. |
| --Runtime-Version | Versione di runtime del linguaggio usato nell'app.  Valori consentiti: `Java_11`, `Java_8`. |
| --target-Module | Modulo figlio da distribuire.  Obbligatorio quando vengono compilati più pacchetti jar dal codice sorgente. |
| --version | Versione della distribuzione.  Non modificato se non impostato. |

### <a name="examples"></a>Esempi

Distribuire il codice sorgente in un'app. Questa operazione configurerà la directory corrente, creerà un file binario usando il servizio di compilazione Pivotal e quindi verrà distribuito nell'app.

```cli
az spring-cloud app deploy -n MyApp -s MyService
```

Consente di distribuire un file jar predefinito in un'app usando le opzioni JVM e le variabili di ambiente.

```cli
az spring-cloud app deploy -n MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseG1GC -XX:+UseStringDeduplication" --env foo=bar
```

Distribuire il codice sorgente in una distribuzione specifica di un'app.

```cli
az spring-cloud app deploy -n MyApp -s Myspring-cloud -d green-deployment
```

## <a name="az-spring-cloud-app-list"></a>AZ Spring-cloud app list

Elencare tutte le app nell'istanza di Azure Spring cloud.

```cli
az spring-cloud app list --resource-group -g
                         --service -s
```

|Parametri obbligatori | |
| --- | ---: |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --Service-s | Nome del cloud Spring di Azure.  È possibile configurare il servizio predefinito utilizzando `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-restart"></a>AZ Spring-cloud app restart

Riavviare le istanze dell'app.  Il valore predefinito è la distribuzione di produzione.

```cli
az spring-cloud app restart --name -n
                            --resource-group -g
                            --service -s
                            --deployment -d
                            --no-wait
```

| Parametri obbligatori | |
| --- | ---: |
| --Name-n | Nome dell'app. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --Service-s | Nome del cloud Spring di Azure.  È possibile configurare il servizio predefinito utilizzando `az configure --defaults spring-cloud=<name>`. |

| Parametri facoltativi | |
| --- | ---: |
| --Deployment-d | Nome della distribuzione esistente dell'app.  Se non è specificato, il valore predefinito è la distribuzione di produzione. |
| --no-wait | Non attendere il completamento delle operazioni a esecuzione prolungata. |

## <a name="az-spring-cloud-app-scale"></a>AZ Spring-cloud app scale

Ridimensionare manualmente un'app o le relative distribuzioni.

```cli
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
| --Name-n | Nome dell'app. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --Service-s | Nome del cloud Spring di Azure.  È possibile configurare il servizio predefinito utilizzando `az configure --defaults spring-cloud=<name>`. |

| Parametri facoltativi | |
| --- | ---: |
| --CPU | Numero di core della CPU virtuale per ogni istanza dell'app. |
| --Deployment-d | Nome della distribuzione esistente dell'app.  Se non è specificato, il valore predefinito è la distribuzione di produzione. |
| --instance-count | Numero di istanze dell'app. |
| -memoria | Numero di GB di memoria per ogni istanza dell'app. |
| --no-wait | Non attendere il completamento delle operazioni a esecuzione prolungata. |

### <a name="examples"></a>Esempi

Ridimensionare un'app a 4 core CPU e 8 GB di memoria per ogni istanza.

```cli
az spring-cloud app scale -n MyApp -s MyService --cpu 3 --memory 8
```

Scalare in orizzontale una distribuzione dell'app in 5 istanze.

```cli
az spring-cloud app scale -n MyApp -s MyService -d green-deployment --instance-count 5
```

## <a name="az-spring-cloud-app-set-deployment"></a>AZ Spring-cloud app Set-Deployment

Impostare le opzioni di configurazione per la distribuzione di produzione dell'app.

```cli
az spring-cloud app set-deployment --deployment -d
                                   --name -n
                                   --resource-group -g
                                   --service -s
                                   --no-wait
```

| Parametri obbligatori | |
| --- | ---: |
| --Deployment-d | Nome di una distribuzione esistente dell'app. |
| --Name-n | Nome dell'app. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --Service-s | Nome del cloud Spring di Azure.  È possibile configurare il servizio predefinito utilizzando `az configure --defaults spring-cloud=<name>`. |

| Parametri facoltativi | |
| --- | ---: |
| --no-wait | Non attendere il completamento delle operazioni a esecuzione prolungata. |

### <a name="examples"></a>Esempi

Scambiare una distribuzione di staging dell'app in produzione.

```cli
az spring-cloud app set-deployment -d green-deployment -n MyApp -s MyService
```

## <a name="az-spring-cloud-app-show"></a>AZ Spring-cloud app Show

Mostra i dettagli di un'app nel cloud Spring di Azure.

```cli
az spring-cloud app show --name -n
                         --resource-group -g
                         --service -s
```

| Parametri obbligatori | |
| --- | ---: |
| --Name-n | Nome dell'app. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --Service-s | Nome del cloud Spring di Azure.  È possibile configurare il servizio predefinito utilizzando `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-show-deploy-log"></a>AZ Spring-cloud app Show-deploy-log

Mostra il log di compilazione dell'ultima distribuzione dal codice sorgente.  Il valore predefinito è ambiente di produzione.

```cli
az spring-cloud app show-deploy-log --name -n
                                    --resource-group -g
                                    --service -s
                                    --deployment -d
```

| Parametri obbligatori | |
| --- | ---: |
| --Name-n | Nome dell'app. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --Service-s | Nome del cloud Spring di Azure.  È possibile configurare il servizio predefinito utilizzando `az configure --defaults spring-cloud=<name>`. |

| Parametri facoltativi | |
| --- | ---: |
| --Deployment-d | Nome di una distribuzione esistente dell'app.  Il valore predefinito è l'ambiente di produzione. |

## <a name="az-spring-cloud-app-start"></a>AZ Spring-cloud app Start

Avvia le istanze dell'app.  Il valore predefinito è ambiente di produzione.

```cli
az spring-cloud app start --name -n
                          --resource-group -g
                          --service -s
                          --deployment -d
                          --no-wait
```

| Parametri obbligatori | |
| --- | ---: |
| --Name-n | Nome dell'app. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --Service-s | Nome del cloud Spring di Azure.  È possibile configurare il servizio predefinito utilizzando `az configure --defaults spring-cloud=<name>`. |

| Parametri facoltativi | |
| --- | ---: |
| --Deployment-d | Nome di una distribuzione esistente dell'app.  Il valore predefinito è l'ambiente di produzione. |
| --no-wait | Non attendere il completamento delle operazioni a esecuzione prolungata. |

## <a name="az-spring-cloud-app-stop"></a>AZ Spring-cloud app stop

Arrestare le istanze dell'app.  Il valore predefinito è l'ambiente di produzione.

```cli
az spring-cloud app stop --name -n
                         --resource-group -g
                         --service -s
                         --deployment -d
                         --no-wait
```

| Parametri obbligatori | |
| --- | ---: |
| --Name-n | Nome dell'app. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --Service-s | Nome del cloud Spring di Azure.  È possibile configurare il servizio predefinito utilizzando `az configure --defaults spring-cloud=<name>`. |

| Parametri facoltativi | |
| --- | ---: |
| --Deployment-d | Nome di una distribuzione esistente dell'app.  Il valore predefinito è l'ambiente di produzione. |
| --no-wait | Non attendere il completamento delle operazioni a esecuzione prolungata. |

## <a name="az-spring-cloud-app-update"></a>AZ Spring-cloud app Update

Aggiornare la configurazione archiviata di un'app.

```cli
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
| --Name-n | Nome dell'app. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --Service-s | Nome del cloud Spring di Azure.  È possibile configurare il servizio predefinito utilizzando `az configure --defaults spring-cloud=<name>`. |

| Parametri facoltativi | |
| --- | ---: |
| --Deployment-d | Nome di una distribuzione esistente dell'app.  Il valore predefinito è l'ambiente di produzione. |
| --Enable-persistent-storage | Booleano.  Se true, montare un disco da 50 GB con il percorso predefinito. |
| --ENV | Variabili di ambiente separate da spazi nel formato ' chiave [= valore]'. |
| --is-public | Booleano.  Se true, assegnare un dominio pubblico all'app. |
| --JVM-Options | Stringa contenente le opzioni JVM.  Usare ' =' invece di '' per evitare errori di analisi della shell. Ad esempio, `--jvm-options='-Xms1024m -Xmx2048m`. |
| --no-wait | Non attendere il completamento delle operazioni a esecuzione prolungata. |
| --Runtime-Version | Versione di runtime del linguaggio usato nell'app.  Valori consentiti: `Java_11`, `Java_8`. |

### <a name="example"></a>Esempio

Aggiungere una variabile di ambiente per l'app.

```cli
az spring-cloud app update --env foo=bar
```

## <a name="az-spring-cloud-app-binding-list"></a>AZ Spring-cloud app binding List

Elencare tutte le associazioni al servizio in un'app.

```cli
az spring-cloud app binding list --app
                                 --resource-group -g
                                 --service -s
```

| Parametri obbligatori | |
| --- | ---: |
| --app | Nome dell'app. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --Service-s | Nome del cloud Spring di Azure.  È possibile configurare il servizio predefinito utilizzando `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-binding-remove"></a>AZ Spring-cloud app binding Rimuovi

Rimuovere un'associazione al servizio dall'app.

```cli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Parametri obbligatori | |
| --- | ---: |
| --app | Nome dell'app. |
| --name | Nome dell'associazione al servizio da rimuovere. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --Service-s | Nome del cloud Spring di Azure.  È possibile configurare il servizio predefinito utilizzando `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-binding-show"></a>AZ Spring-cloud app binding Show

Mostra i dettagli di un'associazione al servizio.

```cli
az spring-cloud app binding show --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Parametri obbligatori | |
| --- | ---: |
| --app | Nome dell'app. |
| --name | Nome dell'associazione al servizio. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --Service-s | Nome del cloud Spring di Azure.  È possibile configurare il servizio predefinito utilizzando `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-binding-cosmos-add"></a>AZ Spring-cloud app binding Cosmos Add

Associare un Azure Cosmos DB all'app.

```cli
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

```cli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Parametri obbligatori | |
| --- | ---: |
| --API-Type | Specificare il tipo di API usando uno dei valori seguenti: Cassandra, Gremlin, Mongo, SQL, tabella. |
| --app | Nome dell'app. |
| --name | Nome dell'associazione al servizio. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --Service-s | Nome del cloud Spring di Azure.  È possibile configurare il servizio predefinito utilizzando `az configure --defaults spring-cloud=<name>`. |

|Parametri facoltativi | |
| --- | ---: |
| --Collection-Name | Nome della raccolta.  Obbligatorio quando si usa Gremlin. |
| --database-name | Nome del database.  Obbligatorio quando si usa Mongo, SQL e Gremlin. |
| --chiave-spazio | Spazio delle chiavi di Cassandra.  Obbligatorio quando si usa Cassandra. |

## <a name="az-spring-cloud-app-binding-cosmos-update"></a>AZ Spring-cloud app binding Cosmos Update

```cli
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
| --app | Nome dell'app. |
| --name | Nome dell'associazione al servizio. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --Service-s | Nome del cloud Spring di Azure.  È possibile configurare il servizio predefinito utilizzando `az configure --defaults spring-cloud=<name>`. |

|Parametri facoltativi | |
| --- | ---: |
| --Collection-Name | Nome della raccolta.  Obbligatorio quando si usa Gremlin. |
| --database-name | Nome del database.  Obbligatorio quando si usa Mongo, SQL e Gremlin. |
| --chiave-spazio | Spazio delle chiavi di Cassandra.  Obbligatorio quando si usa Cassandra. |

## <a name="az-spring-cloud-app-binding-mysql-add"></a>AZ Spring-cloud app binding MySQL Add

```cli
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
| --app | Nome dell'app. |
| --database-name | Nome del database. |
| --key | Chiave API del servizio. |
| --name | Nome dell'associazione al servizio. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --Resource-ID | ID risorsa di Azure del servizio con cui eseguire l'associazione. |
| --Service-s | Nome del cloud Spring di Azure.  È possibile configurare il servizio predefinito utilizzando `az configure --defaults spring-cloud=<name>`. |
| --username | Nome utente per l'accesso al database. |

## <a name="az-spring-cloud-app-binding-mysql-update"></a>AZ Spring-cloud app binding MySQL Update

Aggiornare l'associazione del servizio per l'app a un database di Azure per MySQL.

```cli
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
| --app | Nome dell'app. |
| --name | Nome dell'associazione al servizio. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --Service-s | Nome del cloud Spring di Azure.  È possibile configurare il servizio predefinito utilizzando `az configure --defaults spring-cloud=<name>`. |

| Parametri facoltativi | |
| --- | ---: |
| --database-name | Nome del database. |
| --key | Chiave API del servizio. |
| --username | Nome utente per l'accesso al database. |

## <a name="az-spring-cloud-app-binding-redis-add"></a>AZ Spring-cloud app binding Redis Add

Associare una cache di Azure per Redis con l'app.

```cli
az spring-cloud app binding redis add --app
                                      --name -n
                                      --resource-group -g
                                      --resource-id
                                      --service -s
                                      --disable-ssl
```

| Parametri obbligatori | |
| --- | ---: |
| --app | Nome dell'app. |
| --name | Nome dell'associazione al servizio. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --Resource-ID | ID risorsa di Azure del servizio con cui si vuole eseguire l'associazione. |
| --Service-s | Nome del cloud Spring di Azure.  È possibile configurare il servizio predefinito utilizzando `az configure --defaults spring-cloud=<name>`. |

| Parametri facoltativi | |
| --- | ---: |
| --Disable-SSL | Disabilitare SSL. |

## <a name="az-spring-cloud-app-binding-redis-update"></a>AZ Spring-cloud app binding Redis Update

Aggiornare un'associazione al servizio per cache di Azure per Redis.

| Parametri obbligatori | |
| --- | ---: |
| --app | Nome dell'app. |
| --name | Nome dell'associazione al servizio. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --Service-s | Nome del cloud Spring di Azure.  È possibile configurare il servizio predefinito utilizzando `az configure --defaults spring-cloud=<name>`. |

| Parametri facoltativi | |
| --- | ---: |
| --Disable-SSL | Disabilitare SSL. |

## <a name="az-spring-cloud-app-deployment-create"></a>AZ Spring-cloud App Deployment crea

Creare una distribuzione di staging per l'app.

Per distribuire il codice o per aggiornare le impostazioni a una distribuzione esistente, usare `az spring-cloud app deploy --deployment <staging-deployment>` o ' AZ Spring-cloud app Update--Deployment <staging deployment>.

| Parametri obbligatori | |
| --- | ---: |
| --app | Nome dell'app. |
| --name | Nome dell'associazione al servizio. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --Service-s | Nome del cloud Spring di Azure.  È possibile configurare il servizio predefinito utilizzando `az configure --defaults spring-cloud=<name>`. |

| Parametri facoltativi | |
| --- | ---: |
| --CPU | Numero di core CPU virtuali per istanza.  Predefinito: 1 |
| --ENV | Variabili di ambiente separate da spazi nel formato ' chiave [= valore]'. |
| --instance-count | Numero di istanze. Impostazione predefinita: 1. |
| --jar-Path | Se specificato, distribuire jar.  In caso contrario, distribuire la cartella corrente come tar. |
| --JVM-Options | Stringa contenente le opzioni JVM.  Usare ' =' invece di '' per evitare errori di analisi della shell. Ad esempio, `--jvm-options='-Xms1024m -Xmx2048m`. |
| -memoria | Numero di GB di memoria per ogni istanza. |
| --no-wait | Non attendere il completamento delle operazioni a esecuzione prolungata. |
| --Runtime-Version | Versione di runtime del linguaggio usato nell'app.  Valori consentiti: `Java_11`, `Java_8`. |
| --Skip-clone-impostazioni | Creare una distribuzione di staging clonando le impostazioni di distribuzione di produzione correnti. |
| --target-Module | Modulo figlio da distribuire.  Obbligatorio quando vengono compilati più pacchetti jar dal codice sorgente. |
| --version | Versione della distribuzione.  Non modificato se non impostato. |

### <a name="examples"></a>Esempi

Distribuire il codice sorgente in una nuova distribuzione dell'app.  Questa operazione compilerà la directory corrente, creerà un file binario usando il sistema di compilazione pivot, quindi eseguirà la distribuzione.

```cli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService
```

Consente di distribuire un file jar predefinito in un'app con le opzioni JVM e le variabili di ambiente.

```cli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseStringDeDuplication" --env foo=bar
```

## <a name="az-spring-cloud-app-deployment-delete"></a>AZ Spring-cloud App Deployment Delete

Eliminare una distribuzione dell'app.

```cli
az spring-cloud app deployment delete --app
                                      --name -n
                                      --resource-group -g
                                      --service -s
```

| Parametri obbligatori | |
| --- | ---: |
| --app | Nome dell'app. |
| --name | Nome della distribuzione. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --Service-s | Nome del cloud Spring di Azure.  È possibile configurare il servizio predefinito utilizzando `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-deployment-list"></a>AZ Spring-cloud App Deployment List

Elencare tutte le distribuzioni in un'app.

```cli
az spring-cloud app deployment list --app
                                    --resource-group -g
                                    --service -s
```

| Parametri obbligatori | |
| --- | ---: |
| --app | Nome dell'app. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --Service-s | Nome del cloud Spring di Azure.  È possibile configurare il servizio predefinito utilizzando `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-deployment-show"></a>AZ Spring-cloud App Deployment Show

Mostra i dettagli di una distribuzione.

```cli
az spring-cloud app deployment show --app
                                    --name -n
                                    --resource-group -g
                                    --service -s
```

| Parametri obbligatori | |
| --- | ---: |
| --app | Nome dell'app. |
| --name | Nome della distribuzione. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --Service-s | Nome del cloud Spring di Azure.  È possibile configurare il servizio predefinito utilizzando `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-config-server-clear"></a>AZ Spring-cloud config-server Clear

Cancellare tutte le impostazioni di configurazione nel server di configurazione.

```cli
az spring-cloud config-server clear --name
                                        --resource-group -g
```

| Parametri obbligatori | |
| --- | ---: |
| --name | Nome del cloud Spring di Azure. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |

## <a name="az-spring-cloud-config-server-set"></a>AZ Spring-cloud config-server set

Impostare le impostazioni di configurazione nel server di configurazione utilizzando un file YAML.

```cli
az spring-cloud config-server set --config-file
                                  --name -n
                                  --resource-group -g
                                  --no-wait
```

| Parametri obbligatori | |
| --- | ---: |
| --config-file | Percorso del file di un manifesto YAML per la configurazione del server di configurazione. |
| --name | Nome del cloud Spring di Azure. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |

| Parametri facoltativi | |
| --- | ---: |
| --no-wait | Non per il completamento delle operazioni a esecuzione prolungata.

## <a name="az-spring-cloud-config-server-show"></a>AZ Spring-cloud config-server show

Mostra le impostazioni del server di configurazione.

```cli
az spring-cloud config-server show --name -n
                                   --resource-group -g
```

| Parametri obbligatori | |
| --- | ---: |
| --name | Nome del cloud Spring di Azure. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |

## <a name="az-spring-cloud-config-server-git-set"></a>AZ Spring-cloud config-server Git set

Impostare le proprietà git per il server di configurazione.  Tutte le proprietà git esistenti vengono sovrascritte.

```cli
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
| --name | Nome del cloud Spring di Azure. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --URI | URI della configurazione aggiunta. |

| Parametri facoltativi | |
| --- | ---: |
| --rinvia | Archiviare temporaneamente l'oggetto nella cache locale anziché inviarlo ad Azure.  Utilizzare `az cache` per visualizzare/deselezionare. |
| --Host-Key | Chiave host per la configurazione aggiunta. |
| --host-key-algorithm | Algoritmo di chiave host per la configurazione aggiunta. |
| --etichetta | Etichetta della configurazione aggiunta. |
| --password | Password della configurazione aggiunta. |
| --chiave privata | Chiave privata della configurazione aggiunta. |
| --Search-percorsi | Percorsi di ricerca della configurazione aggiunta.  Usare delimitatori di virgola per più percorsi. |
| --Strict-host-key-checking | Abilita il controllo della chiave host Strict della configurazione aggiunta. |
| --username | Nome utente della configurazione aggiunta. |

## <a name="az-spring-cloud-config-server-git-repo-add"></a>AZ Spring-cloud config-server Git repository Add

```cli
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
| --name | Nome del cloud Spring di Azure. |
| --repository-Name | URI del repository. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --URI | URI della configurazione aggiunta. |

| Parametri facoltativi | |
| --- | ---: |
| --rinvia | Archiviare temporaneamente l'oggetto nella cache locale anziché inviarlo ad Azure.  Utilizzare `az cache` per visualizzare/deselezionare. |
| --Host-Key | Chiave host per la configurazione aggiunta. |
| --host-key-algorithm | Algoritmo di chiave host per la configurazione aggiunta. |
| --etichetta | Etichetta della configurazione aggiunta. |
| --password | Password della configurazione aggiunta. |
| --pattern | Modello per il repository.  Usare delimitatori di virgola per più percorsi.|
| --chiave privata | Chiave privata della configurazione aggiunta. |
| --Search-percorsi | Percorsi di ricerca della configurazione aggiunta.  Usare delimitatori di virgola per più percorsi. |
| --Strict-host-key-checking | Abilita il controllo della chiave host Strict della configurazione aggiunta. |
| --username | Nome utente della configurazione aggiunta. |

## <a name="az-spring-cloud-config-server-git-repo-list"></a>AZ Spring-cloud config-elenco di repository git server

Elencare tutti i repository git definiti nel server di configurazione

```cli
az spring-cloud config-server git repo list --name -n
                                       --resource-group -g
                                       --defer
```

| Parametri obbligatori | |
| --- | ---: |
| --name | Nome del cloud Spring di Azure. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |

| Parametri facoltativi | |
| --- | ---: |
| --rinvia | Archiviare temporaneamente l'oggetto nella cache locale anziché inviarlo ad Azure.  Utilizzare `az cache` per visualizzare/deselezionare. |

## <a name="az-spring-cloud-config-server-git-repo-remove"></a>AZ Spring-cloud config-server Git repository Remove

Rimuovere una configurazione del repository Git esistente dal server di configurazione.

```cli
az spring-cloud config-server git repo remove --name -n
                                         --repo-name
                                         --resource-group -g
                                         --defer
```

| Parametri obbligatori | |
| --- | ---: |
| --name | Nome del cloud Spring di Azure. |
| --repository-Name | URI del repository. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |

| Parametri facoltativi | |
| --- | ---: |
| --rinvia | Archiviare temporaneamente l'oggetto nella cache locale anziché inviarlo ad Azure.  Utilizzare `az cache` per visualizzare/deselezionare. |

## <a name="az-spring-cloud-test-endpoint-disable"></a>AZ Spring-cloud test-endpoint Disable

Disabilitare l'endpoint di test del cloud Spring di Azure

```cli
az spring-cloud test-endpoint disable --name -n
                                      --resource-group -g
```

| Parametri obbligatori | |
| --- | ---: |
| --name | Nome del cloud Spring di Azure. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |

## <a name="az-spring-cloud-test-endpoint-enable"></a>AZ Spring-cloud test-endpoint Enable

Abilitare l'endpoint di test per il cloud Spring di Azure. 

```cli 
az spring-cloud test-endpoint enable --name -n
                                     --resource-group -g
```

| Parametri obbligatori | |
| --- | ---: |
| --name | Nome del cloud Spring di Azure. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |

## <a name="az-spring-cloud-test-endpoint-list"></a>AZ Spring-cloud test-endpoint List 

Elencare le chiavi dell'endpoint di test disponibili per il cloud Spring di Azure.

```cli
az spring-cloud test-endpoint list --name -n
                                   --resource-group -g
                                   --app
                                   --deployment -d
```

| Parametri obbligatori | |
| --- | ---: |
| --name | Nome del cloud Spring di Azure. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |

| Parametri facoltativi | |
| --- | ---: |
| --app | Nome dell'app. |
| --Deployment-d | Nome di una distribuzione esistente dell'app.  Se non è specificato, il valore predefinito è Production. |

## <a name="az-spring-cloud-test-endpoint-renew-key"></a>AZ Spring-cloud test-endpoint Renew-Key

Rigenerare una chiave test-endpoint per il cloud Spring di Azure.

```cli
az spring-cloud test-endpoint renew-key --name -n
                                        --resource-group -g
                                        --type
```

| Parametri obbligatori | |
| --- | ---: |
| --name | Nome del cloud Spring di Azure. |
| --resource-group -g | Nome del gruppo di risorse.  È possibile configurare il gruppo predefinito con `az configure --defaults group=<name>`. |
| --tipo | Tipo di chiave dell'endpoint di test.  Valori consentiti: primario, secondario. |
