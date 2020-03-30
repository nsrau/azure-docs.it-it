---
title: Pod AKS con scalabilità automatica con metriche del gateway applicazione di AzureAutoscale AKS pods with Azure Application Gateway metrics
description: Questo articolo fornisce istruzioni su come ridimensionare i pod back-end AKS usando le metriche del gateway applicazione e l'adattatore metrico di Azure Kubernetes
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 1169ed0e9a2b970ee0e30d73ea20c87001b62786
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239440"
---
# <a name="autoscale-your-aks-pods-using-application-gateway-metrics-beta"></a>Scalabilità automatica dei pod AKS utilizzando le metriche del gateway applicazione (Beta)

Con l'aumentare del traffico in ingresso, diventa fondamentale aumentare le applicazioni in base alla domanda.

Nell'esercitazione seguente viene illustrato come usare `AvgRequestCountPerHealthyHost` la metrica del gateway applicazione per aumentare la scalabilità dell'applicazione. `AvgRequestCountPerHealthyHost`misura le richieste medie inviate a una specifica combinazione di impostazioni HTTP di pool back-end e back-end.

Utilizzeremo i seguenti due componenti:

* [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter)- Useremo l'adattatore metrico per esporre le metriche del gateway applicazione tramite il server delle metriche.- We will use the metric adapter to expose Application Gateway metrics through the metric server. L'adattatore metrico di Azure Kubernetes è un progetto open source in Azure, simile al controller di ingresso del gateway applicazione. 
* [`Horizontal Pod Autoscaler`](https://docs.microsoft.com/azure/aks/concepts-scale#horizontal-pod-autoscaler)- Useremo HPA per utilizzare le metriche del gateway applicazione e indirizzare una distribuzione per la scalabilità.- We will use HPA to use Application Gateway metrics and target a deployment for scaling.

## <a name="setting-up-azure-kubernetes-metric-adapter"></a>Configurazione dell'adattatore metrico di Azure Kubernetes

1. Verrà innanzitutto creata un'entità servizio `Monitoring Reader` di Azure AAD e assegnarle l'accesso tramite il gruppo di risorse del gateway applicazione. 

    ```azurecli
        applicationGatewayGroupName="<application-gateway-group-id>"
        applicationGatewayGroupId=$(az group show -g $applicationGatewayGroupName -o tsv --query "id")
        az ad sp create-for-rbac -n "azure-k8s-metric-adapter-sp" --role "Monitoring Reader" --scopes applicationGatewayGroupId
    ```

1. A questo punto, [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter) verrà distribuito l'utilizzo dell'entità servizio AAD creato in precedenza.

    ```bash
    kubectl create namespace custom-metrics
    # use values from service principle created above to create secret
    kubectl create secret generic azure-k8s-metrics-adapter -n custom-metrics \
        --from-literal=azure-tenant-id=<tenantid> \
        --from-literal=azure-client-id=<clientid> \
        --from-literal=azure-client-secret=<secret>
    kubectl apply -f kubectl apply -f https://raw.githubusercontent.com/Azure/azure-k8s-metrics-adapter/master/deploy/adapter.yaml -n custom-metrics
    ```

1. Verrà creata `ExternalMetric` una risorsa `appgw-request-count-metric`con nome . Questa risorsa indicherà all'adattatore di metrica di esporre `AvgRequestCountPerHealthyHost` la metrica per `myApplicationGateway` la risorsa nel `myResourceGroup` gruppo di risorse. È possibile `filter` utilizzare il campo per impostare HTTP e pool back-end specifico nel gateway applicazione.

    ```yaml
    apiVersion: azure.com/v1alpha2
    kind: ExternalMetric
    metadata:
    name: appgw-request-count-metric
    spec:
        type: azuremonitor
        azure:
            resourceGroup: myResourceGroup # replace with your application gateway's resource group name
            resourceName: myApplicationGateway # replace with your application gateway's name
            resourceProviderNamespace: Microsoft.Network
            resourceType: applicationGateways
        metric:
            metricName: AvgRequestCountPerHealthyHost
            aggregation: Average
            filter: BackendSettingsPool eq '<backend-pool-name>~<backend-http-setting-name>' # optional
    ```

Ora puoi effettuare una richiesta al server delle metriche per vedere se la nostra nuova metrica viene esposta:
```bash
kubectl get --raw "/apis/external.metrics.k8s.io/v1beta1/namespaces/default/appgw-request-count-metric"
# Sample Output
# {
#   "kind": "ExternalMetricValueList",
#   "apiVersion": "external.metrics.k8s.io/v1beta1",
#   "metadata":
#     {
#       "selfLink": "/apis/external.metrics.k8s.io/v1beta1/namespaces/default/appgw-request-count-metric",
#     },
#   "items":
#     [
#       {
#         "metricName": "appgw-request-count-metric",
#         "metricLabels": null,
#         "timestamp": "2019-11-05T00:18:51Z",
#         "value": "30",
#       },
#     ],
# }
```

## <a name="using-the-new-metric-to-scale-up-the-deployment"></a>Utilizzo della nuova metrica per aumentare la scalabilità verticale della distribuzioneUsing the new metric to scale up the deployment

Una volta che `appgw-request-count-metric` siamo in grado di esporre [`Horizontal Pod Autoscaler`](https://docs.microsoft.com/azure/aks/concepts-scale#horizontal-pod-autoscaler) attraverso il server metrico, siamo pronti per utilizzare per aumentare la nostra distribuzione di destinazione.

Nell'esempio seguente, verrà destinata `aspnet`a una distribuzione di esempio. Scaleremo i Pod `appgw-request-count-metric` quando > 200 per Pod `10` fino a un massimo di Pod.

Sostituire il nome della distribuzione di destinazione e applicare la seguente configurazione di scalabilità automatica:
```yaml
apiVersion: autoscaling/v2beta1
kind: HorizontalPodAutoscaler
metadata:
  name: deployment-scaler
spec:
  scaleTargetRef:
    apiVersion: extensions/v1beta1
    kind: Deployment
    name: aspnet # replace with your deployment's name
  minReplicas: 1
  maxReplicas: 10
  metrics:
  - type: External
    external:
      metricName: appgw-request-count-metric
      targetAverageValue: 200
```

Testare la configurazione utilizzando uno strumento di test di carico come apache bench:
```bash
ab -n10000 http://<applicaiton-gateway-ip-address>/
```

## <a name="next-steps"></a>Passaggi successivi
- [**Risolvere i problemi relativi**](ingress-controller-troubleshoot.md)a Ingress Controller : Risolvere eventuali problemi con il controller in ingresso.