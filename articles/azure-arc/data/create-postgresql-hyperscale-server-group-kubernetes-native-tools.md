---
title: Creare un gruppo di server con iperscalabilità PostgreSQL usando gli strumenti di Kubernetes
description: Creare un gruppo di server con iperscalabilità PostgreSQL usando gli strumenti di Kubernetes
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: f447c6028b1750aa96e531a97e7b0861f66a5749
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91761669"
---
# <a name="create-a-postgresql-hyperscale-server-group-using-kubernetes-tools"></a>Creare un gruppo di server con iperscalabilità PostgreSQL usando gli strumenti di Kubernetes

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Prerequisiti

È necessario avere già creato un [controller di dati di Azure Arc](./create-data-controller.md).

Per creare un gruppo di server con iperscalabilità PostgreSQL usando gli strumenti di Kubernetes, è necessario che siano installati gli strumenti di Kubernetes.  Gli esempi in questo articolo useranno `kubectl` , ma approcci simili potrebbero essere usati con altri strumenti Kubernetes, ad esempio il dashboard di Kubernetes, `oc` o `helm` se si ha familiarità con questi strumenti e Kubernetes YAML/JSON.

[Installare lo strumento kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

## <a name="overview"></a>Panoramica

Per creare un gruppo di server con iperscalabilità PostgreSQL, è necessario creare un segreto Kubernetes per archiviare in modo sicuro l'accesso e la password dell'amministratore di Postgres e una risorsa personalizzata del gruppo di server di scalabilità PostgreSQL basata sulle definizioni di risorse personalizzate PostgreSQL-12 o PostgreSQL-11.

## <a name="create-a-yaml-file"></a>Creare un file YAML

È possibile usare il file [YAML del modello](https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/yaml/postsgresql.yaml) come punto di partenza per creare il file YAML del gruppo di server di scalabilità PostgreSQL personalizzato.  Scaricare questo file nel computer locale e aprirlo in un editor di testo.  È utile usare un editor di testo, ad esempio [vs code](https://code.visualstudio.com/download) che supporta l'evidenziazione della sintassi e la sfilacciatura per i file YAML.

Questo è un esempio di file YAML:

```yaml
apiVersion: v1
data:
  password: <your base64 encoded password>
kind: Secret
metadata:
  name: example-login-secret
type: Opaque
---
apiVersion: arcdata.microsoft.com/v1alpha1
kind: postgresql-12
metadata:
  generation: 1
  name: example
spec:
  engine:
    extensions:
    - name: citus
  scale:
    shards: 3
  scheduling:
    default:
      resources:
        limits:
          cpu: "4"
          memory: 4Gi
        requests:
          cpu: "1"
          memory: 2Gi
  service:
    type: LoadBalancer
  storage:
    backups:
      className: default
      size: 5Gi
    data:
      className: default
      size: 5Gi
    logs:
      className: default
      size: 1Gi
```

### <a name="customizing-the-login-and-password"></a>Personalizzazione dell'account di accesso e della password.
Un segreto Kubernetes viene archiviato come stringa con codifica Base64, uno per il nome utente e uno per la password.  È necessario codificare con Base64 un account di accesso e una password di amministratore e inserirli nella posizione segnaposto in `data.password` e `data.username` .  Non includere i `<` simboli e `>` specificati nel modello.

È possibile usare uno strumento online per codificare con Base64 il nome utente e la password desiderati oppure è possibile usare gli strumenti dell'interfaccia della riga di comando predefiniti a seconda della piattaforma.

PowerShell

```console
[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('<your string to encode here>'))

#Example
#[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('example'))

```

Linux/macOS

```console
echo '<your string to encode here>' | base64

#Example
# echo 'example' | base64
```

### <a name="customizing-the-name"></a>Personalizzazione del nome

Il valore del modello è' example ' per l'attributo Name.  Questa operazione può essere modificata, ma deve essere costituita da caratteri che seguono gli standard di denominazione DNS.  È anche necessario modificare il nome del segreto in modo che corrisponda.  Ad esempio, se si modifica il nome del gruppo di server di iperscala PostgreSQL in ' postgres1', è necessario modificare il nome del segreto da' Example-Login-Secret ' a' postgres1-login-Secret '

### <a name="customizing-the-engine-version"></a>Personalizzazione della versione del motore

È possibile modificare la versione del motore in modo che sia PostgreSQL-11 o PostgreSQL-12 modificando l' `kind` attributo.

### <a name="customizing-the-resource-requirements"></a>Personalizzazione dei requisiti delle risorse

È possibile modificare i requisiti delle risorse, ovvero i limiti di RAM e di base e le richieste, in base alle esigenze.  

> [!NOTE]
> Altre informazioni sulla [governance delle risorse di Kubernetes](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes).

Requisiti per i limiti delle risorse e le richieste:
- Il valore del limite di core è **necessario** ai fini della fatturazione.
- Il resto delle richieste e dei limiti delle risorse è facoltativo.
- Il limite di core e la richiesta devono essere un valore intero positivo, se specificato.
- Il valore minimo di 1 Core è obbligatorio per la richiesta di core, se specificato.
- Il formato del valore di memoria segue la notazione Kubernetes.  

### <a name="customizing-service-type"></a>Personalizzazione del tipo di servizio

Se lo si desidera, è possibile modificare il tipo di servizio in norimpatri.  Verrà assegnato un numero di porta casuale.

### <a name="customizing-storage"></a>Personalizzazione dell'archiviazione

È possibile personalizzare le classi di archiviazione per l'archiviazione in base all'ambiente in uso.  Se non si è certi delle classi di archiviazione disponibili, è possibile eseguire il comando `kubectl get storageclass` per visualizzarle.  Il valore predefinito del modello è' default '.  Ciò significa che è presente una classe di archiviazione _denominata_ ' default ', che non è una _classe di archiviazione_ predefinita.  Facoltativamente, è anche possibile modificare le dimensioni della risorsa di archiviazione.  Per altre informazioni, vedere [configurazione dell'archiviazione](./storage-configuration.md).

## <a name="creating-the-postgresql-hyperscale-server-group"></a>Creazione del gruppo di server di iperscala PostgreSQL

Ora che è stato personalizzato il file YAML del gruppo di server iperscala PostgreSQL, è possibile creare il gruppo di server di iperscala PostgreSQL eseguendo il comando seguente:

```console
kubectl create -n <your target namespace> -f <path to your yaml file>

#Example
#kubectl create -n arc -f C:\arc-data-services\postgres.yaml
```


## <a name="monitoring-the-creation-status"></a>Monitoraggio dello stato di creazione

Il completamento della creazione del gruppo di server per l'iperscala PostgreSQL potrebbe richiedere alcuni minuti. È possibile monitorare lo stato di avanzamento in un'altra finestra del terminale con i comandi seguenti:

> [!NOTE]
>  I comandi di esempio seguenti presuppongono che sia stato creato un gruppo di server di iperscala PostgreSQL denominato ' postgres1' e uno spazio dei nomi Kubernetes con il nome ' Arc '.  Se è stato usato un nome di gruppo di server con iperscalabilità di PostgreSQL o spazio dei nomi diverso, è possibile sostituire ' Arc ' è postgres1' con i nomi.

```console
kubectl get postgresql-12/postgres1 --namespace arc
```

```console
kubectl get pods --namespace arc
```

È anche possibile controllare lo stato di creazione di un particolare pod eseguendo un comando come riportato di seguito.  Questa operazione è particolarmente utile per la risoluzione di eventuali problemi.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/postgres1-0 --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Risoluzione dei problemi di creazione

Se si verificano problemi durante la creazione, vedere la [Guida alla risoluzione dei problemi](troubleshoot-guide.md).