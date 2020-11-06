---
title: Ridimensionare automaticamente i pod AKS con applicazione Azure metriche del gateway
description: Questo articolo fornisce istruzioni su come ridimensionare i pod back-end AKS usando le metriche del gateway applicazione e la scheda metrica di Azure Kubernetes
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a8f015085baa8fffa6f208e9d8dd749e397c76c3
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397434"
---
# <a name="autoscale-your-aks-pods-using-application-gateway-metrics-beta"></a>Ridimensionare automaticamente i pod AKS usando le metriche del gateway applicazione (beta)

Man mano che aumenta il traffico in ingresso, diventa fondamentale scalare le applicazioni in base alla domanda.

Nell'esercitazione seguente viene illustrato come è possibile usare la metrica del gateway applicazione `AvgRequestCountPerHealthyHost` per aumentare le prestazioni dell'applicazione. `AvgRequestCountPerHealthyHost` misura le richieste medie inviate a un pool back-end specifico e una combinazione di impostazioni HTTP back-end.

Verranno usati i due componenti seguenti:

* [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter) -Si userà la metrica Adapter per esporre le metriche del gateway applicazione tramite il server della metrica. Azure Kubernetes Metric Adapter è un progetto open source in Azure, simile al controller di ingresso del gateway applicazione. 
* [`Horizontal Pod Autoscaler`](../aks/concepts-scale.md#horizontal-pod-autoscaler) -Si userà HPA per usare le metriche del gateway applicazione e come destinazione una distribuzione per la scalabilità.

## <a name="setting-up-azure-kubernetes-metric-adapter"></a>Impostazione della scheda metrica di Azure Kubernetes

1. Si creerà prima un'entità servizio di Azure AAD e si assegnerà `Monitoring Reader` l'accesso al gruppo di risorse del gateway applicazione. 

    ```azurecli
        applicationGatewayGroupName="<application-gateway-group-id>"
        applicationGatewayGroupId=$(az group show -g $applicationGatewayGroupName -o tsv --query "id")
        az ad sp create-for-rbac -n "azure-k8s-metric-adapter-sp" --role "Monitoring Reader" --scopes applicationGatewayGroupId
    ```

1. A questo punto, verrà distribuito [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter) usando l'entità servizio AAD creata in precedenza.

    ```bash
    kubectl create namespace custom-metrics
    # use values from service principal created above to create secret
    kubectl create secret generic azure-k8s-metrics-adapter -n custom-metrics \
        --from-literal=azure-tenant-id=<tenantid> \
        --from-literal=azure-client-id=<clientid> \
        --from-literal=azure-client-secret=<secret>
    kubectl apply -f kubectl apply -f https://raw.githubusercontent.com/Azure/azure-k8s-metrics-adapter/master/deploy/adapter.yaml -n custom-metrics
    ```

1. Si creerà una `ExternalMetric` risorsa con nome `appgw-request-count-metric` . Questa risorsa indicherà all'adattatore della metrica di esporre la `AvgRequestCountPerHealthyHost` metrica per la `myApplicationGateway` risorsa nel `myResourceGroup` gruppo di risorse. È possibile usare il `filter` campo per fare riferimento a un pool back-end specifico e a un'impostazione http back-end nel gateway applicazione.

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

È ora possibile effettuare una richiesta al server della metrica per verificare se la nuova metrica è stata esposta:
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

## <a name="using-the-new-metric-to-scale-up-the-deployment"></a>Uso della nuova metrica per la scalabilità verticale della distribuzione

Una volta che è possibile esporre `appgw-request-count-metric` tramite il server delle metriche, è possibile usare [`Horizontal Pod Autoscaler`](../aks/concepts-scale.md#horizontal-pod-autoscaler) per aumentare il livello di distribuzione della destinazione.

Nell'esempio seguente viene scelta la destinazione di una distribuzione di esempio `aspnet` . Si aumenteranno i pod quando `appgw-request-count-metric` > 200 per ogni pod fino a un massimo di `10` pod.

Sostituire il nome della distribuzione di destinazione e applicare la configurazione di scalabilità automatica seguente:
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

Testare la configurazione usando uno strumento di test di carico come Apache BENCH:
```bash
ab -n10000 http://<applicaiton-gateway-ip-address>/
```

## <a name="next-steps"></a>Passaggi successivi
- [**Risolvere**](ingress-controller-troubleshoot.md)i problemi del controller di ingresso: risolvere eventuali problemi del controller di ingresso.