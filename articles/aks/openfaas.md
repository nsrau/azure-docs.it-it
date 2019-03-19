---
title: Usare OpenFaaS con servizio Azure Kubernetes
description: Distribuire e usare OpenFaaS con il servizio Azure Kubernetes
services: container-service
author: justindavies
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/05/2018
ms.author: juda
ms.custom: mvc
ms.openlocfilehash: 5ed6e0b21b00ede3f78a102fd004e5706ae3cea5
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57571219"
---
# <a name="using-openfaas-on-aks"></a>Uso di OpenFaaS in servizio Azure Kubernetes

[OpenFaaS] [ open-faas] è un framework per la creazione di funzioni senza server tramite l'uso di contenitori. Il fatto che si tratti di un progetto Open Source ne ha favorito l'adozione su larga scala all'interno della community. Questo documento descrive l'installazione e l'uso di OpenFaaS in un cluster del servizio Azure Kubernetes.

## <a name="prerequisites"></a>Prerequisiti

Per completare la procedura descritta in questo articolo è necessario quanto segue.

* Conoscenza di base di Kubernetes.
* Un cluster del servizio Azure Kubernetes e le credenziali del servizio Azure Kubernetes configurate nel sistema di sviluppo.
* Avere l'interfaccia della riga di comando di Azure installata nel sistema di sviluppo.
* Strumenti da riga di comando di GIT installati nel sistema.

## <a name="add-the-openfaas-helm-chart-repo"></a>Aggiungere il repository di grafici di helm OpenFaaS

OpenFaaS mantiene i proprio grafici helm per mantenere aggiornate tutte le modifiche più recenti.

```azurecli-interactive
helm repo add openfaas https://openfaas.github.io/faas-netes/
helm repo update
```

## <a name="deploy-openfaas"></a>Distribuire OpenFaaS

È consigliabile archiviare OpenFaaS e le funzioni di OpenFaaS in spazi dei nomi Kubernetes distinti.

Creare uno spazio dei nomi per il sistema OpenFaaS e le funzioni:

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openfaas/faas-netes/master/namespaces.yml
```

Generare una password per il portale dell'interfaccia utente di OpenFaaS e API REST:

```azurecli-interactive
# generate a random password
PASSWORD=$(head -c 12 /dev/urandom | shasum| cut -d' ' -f1)

kubectl -n openfaas create secret generic basic-auth \
--from-literal=basic-auth-user=admin \
--from-literal=basic-auth-password="$PASSWORD"
```

È possibile ottenere il valore della chiave privata con `echo $PASSWORD`.

La password create qui verrà considerata dal grafico helm per abilitare l'autenticazione di base sul OpenFaaS Gateway, che viene esposto a Internet tramite un servizio Load Balancer di cloud.

Il repository clonato include un grafico Helm per OpenFaaS. Usare questo grafico per distribuire OpenFaaS nel cluster del servizio Azure Container.

```azurecli-interactive
helm upgrade openfaas --install openfaas/openfaas \
    --namespace openfaas  \
    --set basic_auth=true \
    --set functionNamespace=openfaas-fn \
    --set serviceType=LoadBalancer
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

Per testare il sistema OpenFaaS, passare all'indirizzo IP esterno sulla porta 8080, in questo esempio `http://52.186.64.52:8080`. Verrà richiesto di accedere. Per recuperare la password, immettere `echo $PASSWORD`.

![Interfaccia utente di OpenFaaS](media/container-service-serverless/openfaas.png)

Infine, installare l'interfaccia della riga di comando di OpenFaaS. In questo esempio viene usato brew, vedere la [documentazione dell'interfaccia della riga di comando di OpenFaaS][open-faas-cli] per altre opzioni.

```console
brew install faas-cli
```

Impostare `$OPENFAAS_URL` all'IP pubblico trovato sopra.

Accedere con la CLI di Azure:

```azurecli-interactive
export OPENFAAS_URL=http://52.186.64.52:8080
echo -n $PASSWORD | ./faas-cli login -g $OPENFAAS_URL -u admin --password-stdin
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

## <a name="next-steps"></a>Fasi successive

È possibile continuare ad apprendere con il workshop di OpenFaaS tramite una serie di esercitazioni pratiche che illustrano gli argomenti, ad esempio come creare i propri bot di GitHub, usando i segreti, visualizzazione delle metriche e la scalabilità automatica.

<!-- LINKS - external -->
[install-mongo]: https://docs.mongodb.com/manual/installation/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[open-faas]: https://www.openfaas.com/
[open-faas-cli]: https://github.com/openfaas/faas-cli
[openfaas-workshop]: https://github.com/openfaas/workshop
