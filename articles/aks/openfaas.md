---
title: Usare OpenFaaS con il servizio contenitore di Azure (AKS)
description: Distribuire e usare OpenFaaS con il servizio contenitore di Azure (AKS)
services: container-service
author: justindavies
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/05/2018
ms.author: juda
ms.custom: mvc
ms.openlocfilehash: 06706450d8af6f571f002789815290f75da9623d
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="using-openfaas-on-aks"></a>Uso di OpenFaaS in AKS

[OpenFaaS][open-faas] è un framework per la compilazione di funzioni senza server su contenitori. Il fatto che si tratta di un progetto Open Source ne ha favorito l'adozione su larga scala all'interno della community. Questo documento descrive l'installazione l'uso di OpenFaaS in un cluster del servizio contenitore di Azure (AKS).

## <a name="prerequisites"></a>prerequisiti

Per completare la procedura descritta in questo articolo è necessario quanto segue.

* Conoscenza di base di Kubernetes.
* Un cluster del servizio contenitore di Azure (AKS) e le credenziali di AKS configurate nel sistema di sviluppo.
* Avere l'interfaccia della riga di comando di Azure installata nel sistema di sviluppo.
* Strumenti da riga di comando di GIT installati nel sistema.

## <a name="get-openfaas"></a>Ottenere OpenFaaS

Clonare il repository del progetto OpenFaaS nel sistema di sviluppo.

```azurecli-interactive
git clone https://github.com/openfaas/faas-netes
```

Passare alla directory del repository clonato.

```azurecli-interactive
cd faas-netes 
```

## <a name="deploy-openfaas"></a>Distribuire OpenFaaS

È consigliabile archiviare OpenFaaS e le funzioni di OpenFaaS in spazi dei nomi Kubernetes distinti.

Creare uno spazio dei nomi per il sistema OpenFaaS.

```azurecli-interactive
kubectl create namespace openfaas
```

Creare un secondo spazio dei nomi per le funzioni di OpenFaaS.

```azurecli-interactive 
kubectl create namespace openfaas-fn
```

Il repository clonato include un grafico Helm per OpenFaaS. Usare questo grafico per distribuire OpenFaaS nel cluster del servizio contenitore di Azure.

```azurecli-interactive
helm install --namespace openfaas -n openfaas \
  --set functionNamespace=openfaas-fn, \
  --set serviceType=LoadBalancer, \
  --set rbac=false chart/openfaas/ 
```

Output:

```
NAME:   openfaas
LAST DEPLOYED: Wed Feb 28 08:26:11 2018
NAMESPACE: openfaas
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                 DATA  AGE
prometheus-config    2     20s
alertmanager-config  1     20s

{snip}

NOTES:
To verify that openfaas has started, run:

  kubectl --namespace=openfaas get deployments -l "release=openfaas, app=openfaas"
```

Viene creato un indirizzo IP pubblico per l'accesso al gateway OpenFaaS. Per recuperare questo indirizzo IP, usare il comando [kubectl get service][kubectl-get]. L'assegnazione dell'indirizzo IP al servizio può richiedere qualche minuto.

```console
kubectl get service -l component=gateway --namespace openfaas
```

Output: 

```console
NAME               TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)          AGE
gateway            ClusterIP      10.0.156.194   <none>         8080/TCP         7m
gateway-external   LoadBalancer   10.0.28.18     52.186.64.52   8080:30800/TCP   7m
```

Per testare il sistema OpenFaaS, passare all'indirizzo IP esterno sulla porta 8080, in questo esempio `http://52.186.64.52:8080`.

![Interfaccia utente di OpenFaaS](media/container-service-serverless/openfaas.png)

Infine, installare l'interfaccia della riga di comando di OpenFaaS. In questo esempio viene usato brew, vedere la [documentazione dell'interfaccia della riga di comando di OpenFaaS] [ open-faas-cli] per altre opzioni.

```console
brew install faas-cli
```

## <a name="create-first-function"></a>Creare la prima funzione

Ora che OpenFaaS è operativo, creare una funzione usando il portale di OpenFaaS.

Fare clic su **Deploy New Function** (Distribuisci nuova funzione) e cercare **Figlet**. Selezionare la funzione Figlet e fare clic su **Deploy** (Distribuisci).

![Figlet](media/container-service-serverless/figlet.png)

Usare cURL per richiamare la funzione. Sostituire l'indirizzo IP nell'esempio seguente con quello del proprio gateway OpenFaaS.

```azurecli-interactive
curl -X POST http://52.186.64.52:8080/function/figlet -d "Hello Azure"
```

Output:

```console
 _   _      _ _            _                        
| | | | ___| | | ___      / \    _____   _ _ __ ___ 
| |_| |/ _ \ | |/ _ \    / _ \  |_  / | | | '__/ _ \
|  _  |  __/ | | (_) |  / ___ \  / /| |_| | | |  __/
|_| |_|\___|_|_|\___/  /_/   \_\/___|\__,_|_|  \___|

```

## <a name="create-second-function"></a>Creare la seconda funzione

Ora creare una seconda funzione. Questo esempio verrà distribuito mediante l'interfaccia della riga di comando di OpenFaaS e include un'immagine del contenitore personalizzata e il recupero dei dati da un database Cosmos DB. Prima di creare la funzione occorre configurare diversi elementi. 

Prima di tutto, creare un nuovo gruppo di risorse per il database Cosmos DB.

```azurecli-interactive
az group create --name serverless-backing --location eastus
```

Distribuire un'istanza di Cosmos DB di tipo `MongoDB`. Per l'istanza è necessario un nome univoco, aggiornare `openfaas-cosmos` in un valore univoco per l'ambiente. 

```azurecli-interactive
az cosmosdb create --resource-group serverless-backing --name openfaas-cosmos --kind MongoDB
```

Ottenere la stringa di connessione al database Cosmos DB e archiviarla in una variabile. 

Aggiornare il valore per l'argomento `--resource-group` con il nome del gruppo di risorse e l'argomento `--name` con il nome del database Cosmos DB.

```azurecli-interactive
COSMOS=$(az cosmosdb list-connection-strings \
  --resource-group serverless-backing \
  --name openfaas-cosmos \
  --query connectionStrings[0].connectionString \
  --output tsv)
```

Popolare il database Cosmos DB con dati di test. Creare un file denominato `plans.json` e copiare al suo interno il codice JSON seguente.

```json
{
    "name" : "two_person",
    "friendlyName" : "Two Person Plan",
    "portionSize" : "1-2 Person",
    "mealsPerWeek" : "3 Unique meals per week",
    "price" : 72,
    "description" : "Our basic plan, delivering 3 meals per week, which will feed 1-2 people.",
    "__v" : 0
}
```

Usare lo strumento *mongoimport* per caricare i dati nell'istanza di Cosmos DB. 

Se necessario, installare gli strumenti MongoDB. L'esempio seguente illustra come installare questi strumenti usando brew, per altre opzioni vedere la [documentazione di MongoDB][install-mongo].

```azurecli-interactive
brew install mongodb
```

Caricare i dati nel database.

```azurecli-interactive
mongoimport --uri=$COSMOS -c plans < plans.json
```

Output:

```console
2018-02-19T14:42:14.313+0000    connected to: localhost
2018-02-19T14:42:14.918+0000    imported 1 document
```

Eseguire il comando seguente per creare la funzione. Aggiornare il valore dell'argomento `-g` con l'indirizzo del gateway OpenFaaS.

```azurecli-interctive
faas-cli deploy -g http://52.186.64.52:8080 --image=shanepeckham/openfaascosmos --name=cosmos-query --env=NODE_ENV=$COSMOS
```

Dopo la distribuzione, dovrebbe essere visualizzato l'endpoint OpenFaaS appena creato per la funzione.

```console
Deployed. 202 Accepted.
URL: http://52.186.64.52:8080/function/cosmos-query
```

Testare la funzione usando cURL. Aggiornare l'indirizzo IP con l'indirizzo del gateway OpenFaaS.

```console
curl -s http://52.186.64.52:8080/function/cosmos-query
```

Output:

```json
[{"ID":"","Name":"two_person","FriendlyName":"","PortionSize":"","MealsPerWeek":"","Price":72,"Description":"Our basic plan, delivering 3 meals per week, which will feed 1-2 people."}]
```

È anche possibile testare la funzione all'interno dell'interfaccia utente di OpenFaaS.

![testo alternativo](media/container-service-serverless/OpenFaaSUI.png)

# <a name="next-steps"></a>Passaggi successivi

La distribuzione predefinita di OpenFaaS deve essere bloccata, sia per quanto riguarda il gateway OpenFaaS, sia per le funzioni. [Questo post di blog di Alex Ellis](https://blog.alexellis.io/lock-down-openfaas/) contiene informazioni dettagliate sulle opzioni di configurazione sicura. 

<!-- LINKS - external -->
[install-mongo]: https://docs.mongodb.com/manual/installation/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[open-faas]: https://www.openfaas.com/
[open-faas-cli]: https://github.com/openfaas/faas-cli