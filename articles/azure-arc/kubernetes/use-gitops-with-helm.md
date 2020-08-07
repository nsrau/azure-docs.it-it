---
title: Distribuire i grafici Helm con GitOps nel cluster Kubernetes abilitato per Arc (anteprima)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Usare GitOps con Helm per la configurazione di cluster abilitata per Azure Arc (anteprima)
keywords: GitOps, Kubernetes, K8s, Azure, Helm, Arc, servizio Azure Container, servizio Azure Kubernetes, contenitori
ms.openlocfilehash: 44803338a27fc492f4dc896a0edb398b2ce486ea
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2020
ms.locfileid: "87926128"
---
# <a name="deploy-helm-charts-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>Distribuire i grafici Helm con GitOps nel cluster Kubernetes abilitato per Arc (anteprima)

Helm è uno strumento open source per la creazione di pacchetti che consente di installare e gestire il ciclo di vita delle applicazioni Kubernetes. Analogamente agli strumenti di gestione pacchetti di Linux, come APT e Yum, Helm viene usato per gestire i grafici per Kubernetes, che sono pacchetti di risorse Kubernetes preconfigurate.

Questo articolo illustra come configurare e usare Helm con Kubernetes con abilitazione di Azure Arc.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che sia già disponibile un cluster connesso Kubernetes con abilitazione di Azure Arc. Se è necessario un cluster connesso, vedere l'[avvio rapido di connessione di un cluster](./connect-cluster.md).

Per prima cosa è necessario impostare le variabili di ambiente da usare in questa esercitazione. Per il cluster connesso sono necessari il nome del gruppo di risorse e il nome del cluster.

```bash
export RESOURCE_GROUP=<Resource_Group_Name>
export CLUSTER_NAME=<ClusterName>
```

## <a name="verify-your-cluster-is-enabled-with-arc"></a>Verificare che il cluster sia abilitato con Arc

```bash
az connectedk8s list -g $RESOURCE_GROUP -o table
```

Output:
```bash
Name           Location    ResourceGroup
-------------  ----------  ---------------
arc-helm-demo  eastus      k8s-clusters
```

## <a name="overview-of-using-gitops-and-helm-with-azure-arc-enabled-kubernetes"></a>Panoramica dell'uso di GitOps e Helm con Azure Arc Enabled Kubernetes

 L'operatore Helm fornisce un'estensione a Flux che consente di automatizzare le versioni dei grafici Helm. Una versione del grafico viene descritta tramite una risorsa personalizzata di Kubernetes denominata HelmRelease. Flux sincronizza queste risorse da Git al cluster e l'operatore Helm garantisce che i grafici Helm vengano rilasciati come specificato nelle risorse.

 Di seguito è riportata una struttura di repository Git di esempio che verrà usata in questa esercitazione:

```bash
├── charts
│   └── azure-vote
│       ├── Chart.yaml
│       ├── templates
│       │   ├── NOTES.txt
│       │   ├── deployment.yaml
│       │   └── service.yaml
│       └── values.yaml
└── releases
    └── vote-app.yaml
```

Nel repository git sono disponibili due directory, una contenente un grafico Helm e una contenente la configurazione releases. Nella `releases` directory `vote-app.yaml` contiene la configurazione HelmRelease illustrata di seguito:

```bash
apiVersion: helm.fluxcd.io/v1
kind: HelmRelease
metadata:
  name: vote-app
  namespace: arc-k8s-demo
spec:
  releaseName: arc-k8s-demo
  chart:
    git: https://github.com/Azure/arc-helm-demo
    ref: master
    path: charts/azure-vote
  values:
    frontendServiceName: arc-k8s-demo-vote-front
```

La configurazione delle versioni Helm contiene i campi seguenti:

- `metadata.name` è obbligatorio ed è necessario seguire le convenzioni di denominazione Kubernetes
- `metadata.namespace` è facoltativo e determina la posizione in cui viene creata la versione
- `spec.releaseName` è facoltativo e, se non viene specificato, il nome della versione sarà $namespace-$name
- `spec.chart.path` è la directory che contiene il grafico, relativa alla radice del repository
- `spec.values` sono personalizzazioni utente dei valori di parametro predefiniti del grafico

Le opzioni specificate nell'elemento spec.values di HelmRelease sostituiranno le opzioni specificate in values.yaml dall'origine del grafico.

Per altre informazioni su HelmRelease, vedere la [documentazione ufficiale dell'operatore Helm](https://docs.fluxcd.io/projects/helm-operator/en/stable/)

## <a name="create-a-configuration"></a>Creare una configurazione

Usando l'estensione dell'interfaccia della riga di comando di Azure per `k8sconfiguration`, collegare il cluster connesso al repository Git di esempio. A questa configurazione si assegnerà un nome `azure-voting-app` e si distribuirà l'operatore Flux nello spazio dei nomi `arc-k8s-demo`.

```bash
az k8sconfiguration create --name azure-voting-app \
  --resource-group $RESOURCE_GROUP --cluster-name $CLUSTER_NAME \
  --operator-instance-name flux --operator-namespace arc-k8s-demo \
  --operator-params='--git-readonly --git-path=releases' \
  --enable-helm-operator --helm-operator-version='0.6.0' \
  --helm-operator-params='--set helm.versions=v3' \
  --repository-url https://github.com/Azure/arc-helm-demo.git  \
  --scope namespace --cluster-type connectedClusters
```

### <a name="configuration-parameters"></a>Parametri di configurazione

Per personalizzare la creazione della configurazione, [vedere i parametri aggiuntivi che è possibile usare](./use-gitops-connected-cluster.md#additional-parameters).

## <a name="validate-the-configuration"></a>Convalidare la configurazione

Usando l'interfaccia della riga di comando di Azure, verificare che `sourceControlConfiguration` sia stato creato correttamente.

```console
az k8sconfiguration show --resource-group $RESOURCE_GROUP --name azure-voting-app --cluster-name $CLUSTER_NAME --cluster-type connectedClusters
```

La `sourceControlConfiguration` risorsa viene aggiornata con lo stato di conformità, i messaggi e le informazioni di debug.

**Output:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2019-12-05T05:34:41.481000",
    "message": "{\"OperatorMessage\":null,\"ClusterState\":null}",
    "messageLevel": "3"
  },
  "enableHelmOperator": "True",
  "helmOperatorProperties": {
    "chartValues": "--set helm.versions=v3",
    "chartVersion": "0.6.0"
  },
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/azure-voting-app",
  "name": "azure-voting-app",
  "operatorInstanceName": "flux",
  "operatorNamespace": "arc-k8s-demo",
  "operatorParams": "--git-readonly --git-path=releases",
  "operatorScope": "namespace",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "",
  "repositoryUrl": "https://github.com/Azure/arc-helm-demo.git",
  "resourceGroup": "AzureArcTest",
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

## <a name="validate-application"></a>Convalidare l'applicazione

Eseguire il comando seguente e passare a `localhost:3000` nel browser per verificare che l'applicazione sia in esecuzione.

```bash
kubectl port-forward -n arc-k8s-demo svc/arc-k8s-demo-vote-front 3000:80
```

## <a name="next-steps"></a>Passaggi successivi

- [Usare Criteri di Azure per gestire la configurazione del cluster](./use-azure-policy.md)
