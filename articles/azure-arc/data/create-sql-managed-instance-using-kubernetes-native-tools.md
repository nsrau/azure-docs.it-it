---
title: Creare un'istanza gestita di SQL usando gli strumenti di Kubernetes
description: Creare un'istanza gestita di SQL usando gli strumenti di Kubernetes
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 80aa323dfb08ec1e8156099bd5d04e790732b3b0
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91343911"
---
# <a name="create-azure-sql-managed-instance-using-kubernetes-tools"></a>Creare un'istanza gestita di SQL di Azure con gli strumenti Kubernetes

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Prerequisiti

È necessario avere già creato un [controller di dati di Azure Arc](./create-data-controller.md).

Per creare un'istanza gestita di SQL usando gli strumenti di Kubernetes, è necessario che siano installati gli strumenti di Kubernetes.  Gli esempi in questo articolo useranno `kubectl` , ma approcci simili potrebbero essere usati con altri strumenti Kubernetes, ad esempio il dashboard di Kubernetes, `oc` o `helm` se si ha familiarità con questi strumenti e Kubernetes YAML/JSON.

[Installare lo strumento kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

## <a name="overview"></a>Panoramica

Per creare un'istanza gestita di SQL, è necessario creare un segreto Kubernetes per archiviare in modo sicuro l'account di accesso e la password dell'amministratore di sistema e una risorsa personalizzata dell'istanza gestita di SQL basata sulla definizione di risorsa personalizzata sqlmanagedinstance.

## <a name="create-a-yaml-file"></a>Creare un file YAML

È possibile usare il file [YAML del modello](https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/yaml/sqlmi.yaml) come punto di partenza per creare un file YAML di istanza gestita di SQL personalizzato.  Scaricare questo file nel computer locale e aprirlo in un editor di testo.  È utile usare un editor di testo, ad esempio [vs code](https://code.visualstudio.com/download) che supporta l'evidenziazione della sintassi e la sfilacciatura per i file YAML.

Questo è un esempio di file YAML:

```yaml
apiVersion: v1
data:
  password: <your base64 encoded password>
  username: <your base64 encoded user name. 'sa' is not allowed>
kind: Secret
metadata:
  name: example-login-secret
type: Opaque
---
apiVersion: sql.arcdata.microsoft.com/v1alpha1
kind: sqlmanagedinstance
metadata:
  name: example
spec:
  limits:
    memory: 4Gi
    vcores: "4"
  requests:
    memory: 2Gi
    vcores: "1"
  service:
    type: LoadBalancer
  storage:
    backups:
      className: default
      size: 5Gi
    data:
      className: default
      size: 5Gi
    datalogs:
      className: default
      size: 5Gi
    logs:
      className: default
      size: 1Gi
```

### <a name="customizing-the-login-and-password"></a>Personalizzazione dell'account di accesso e della password

Un segreto Kubernetes viene archiviato come stringa con codifica Base64, uno per il nome utente e uno per la password.  È necessario codificare con Base64 un account di accesso e una password dell'amministratore di sistema e inserirli nel percorso segnaposto in `data.password` e `data.username` .  Non includere i `<` simboli e `>` specificati nel modello.

> [!NOTE]
> Per una sicurezza ottimale, l'utilizzo del valore "sa" non è consentito per l'account di accesso.
> Seguire i [criteri di complessità delle password](/sql/relational-databases/security/password-policy#password-complexity).

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

Il valore del modello è' example ' per l'attributo Name.  Questa operazione può essere modificata, ma deve essere costituita da caratteri che seguono gli standard di denominazione DNS.  È anche necessario modificare il nome del segreto in modo che corrisponda.  Se ad esempio si modifica il nome dell'istanza gestita di SQL in ' SQL1', è necessario modificare il nome del segreto da' Example-Login-Secret ' a' SQL1-login-Secret '

### <a name="customizing-the-resource-requirements"></a>Personalizzazione dei requisiti delle risorse

È possibile modificare i requisiti delle risorse, ovvero i limiti di RAM e di base e le richieste, in base alle esigenze.  

> [!NOTE]
> Altre informazioni sulla [governance delle risorse di Kubernetes](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes).

Requisiti per i limiti delle risorse e le richieste:
- Il valore del limite di core è **necessario** ai fini della fatturazione.
- Il resto delle richieste e dei limiti delle risorse è facoltativo.
- Il limite di core e la richiesta devono essere un valore intero positivo, se specificato.
- Il valore minimo di 2 Core è obbligatorio per la richiesta di core, se specificato.
- Il formato del valore di memoria segue la notazione Kubernetes.  
- Se specificato, è necessario un minimo di 2Gi per la richiesta di memoria.
- Come linea guida generale, è necessario disporre di 4 GB di RAM per ogni core per i casi d'uso di produzione.

### <a name="customizing-service-type"></a>Personalizzazione del tipo di servizio

Se lo si desidera, è possibile modificare il tipo di servizio in norimpatri.  Verrà assegnato un numero di porta casuale.

### <a name="customizing-storage"></a>Personalizzazione dell'archiviazione

È possibile personalizzare le classi di archiviazione per l'archiviazione in base all'ambiente in uso.  Se non si è certi delle classi di archiviazione disponibili, è possibile eseguire il comando `kubectl get storageclass` per visualizzarle.  Il valore predefinito del modello è' default '.  Ciò significa che è presente una classe di archiviazione _denominata_ ' default ', che non è una _classe di archiviazione_ predefinita.  Facoltativamente, è anche possibile modificare le dimensioni della risorsa di archiviazione.  Per altre informazioni, vedere [configurazione dell'archiviazione](./storage-configuration.md).

## <a name="creating-the-sql-managed-instance"></a>Creazione dell'istanza gestita di SQL

Ora che è stato personalizzato il file YAML dell'istanza gestita di SQL, è possibile creare l'istanza gestita di SQL eseguendo il comando seguente:

```console
kubectl create -n <your target namespace> -f <path to your yaml file>

#Example
#kubectl create -n arc -f C:\arc-data-services\sqlmi.yaml
```


## <a name="monitoring-the-creation-status"></a>Monitoraggio dello stato di creazione

Il completamento della creazione dell'istanza gestita di SQL potrebbe richiedere alcuni minuti. È possibile monitorare lo stato di avanzamento in un'altra finestra del terminale con i comandi seguenti:

> [!NOTE]
>  I comandi di esempio seguenti presuppongono che sia stata creata un'istanza gestita di SQL denominata ' SQL1' e uno spazio dei nomi Kubernetes con il nome ' Arc '.  Se è stato utilizzato un nome diverso per lo spazio dei nomi o l'istanza gestita di SQL, è possibile sostituire ' Arc ' è sqlmi ' con i nomi.

```console
kubectl get sqlmi/sql1 --namespace arc
```

```console
kubectl get pods --namespace arc
```

È anche possibile controllare lo stato di creazione di un particolare pod eseguendo un comando come riportato di seguito.  Questa operazione è particolarmente utile per la risoluzione di eventuali problemi.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/sql1-0 --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Risoluzione dei problemi di creazione

Se si verificano problemi durante la creazione, vedere la [Guida alla risoluzione dei problemi](troubleshoot-guide.md).

## <a name="next-steps"></a>Passaggi successivi

[Connettersi ad Azure Arc Istanza gestita SQL abilitato](connect-managed-instance.md)
