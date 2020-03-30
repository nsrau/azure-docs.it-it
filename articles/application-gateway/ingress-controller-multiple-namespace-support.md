---
title: Abilitare più supporti dello spazio dei nomi per il controller di ingresso del gateway applicazioneEnable multiple namespace supports for Application Gateway Ingress Controller
description: In questo articolo vengono fornite informazioni su come abilitare il supporto di più spazi dei nomi in un cluster Kubernetes con un controller di ingresso del gateway applicazione.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 83650e7cf46ec1dede5f25e32114d6469bab24be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279923"
---
# <a name="enable-multiple-namespace-support-in-an-aks-cluster-with-application-gateway-ingress-controller"></a>Abilitare il supporto di più spazi dei nomi in un cluster AKS con il controller di ingresso del gateway applicazioneEnable multiple Namespace support in an AKS cluster with Application Gateway Ingress Controller

## <a name="motivation"></a>Motivazione
Gli spazi dei [nomi](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) Kubernetes consentono di partizionare e allocare un cluster Kubernetes ai sottogruppi di un team più ampio. Questi sottoteam possono quindi distribuire e gestire l'infrastruttura con controlli più precisi di risorse, sicurezza, configurazione e così via. Kubernetes consente di definire una o più risorse in ingresso in modo indipendente all'interno di ogni spazio dei nomi.

A partire dalla versione 0.7 [Azure Application Gateway Kubernetes IngressController](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/README.md) (AGIC) può ingerire eventi da e osservare più spazi dei nomi. Se l'amministratore di AKS decide di usare [app Gateway](https://azure.microsoft.com/services/application-gateway/) come ingresso, tutti gli spazi dei nomi utilizzeranno la stessa istanza del gateway applicazione. Una singola installazione di Ingress Controller monitorerà gli spazi dei nomi accessibili e configurerà il gateway applicazione a cui è associato.

La versione 0.7 di AGIC continuerà a osservare esclusivamente lo `default` spazio dei nomi, a meno che non venga esplicitamente modificato in uno o più spazi dei nomi diversi nella configurazione Helm (vedere la sezione seguente).

## <a name="enable-multiple-namespace-support"></a>Abilitare il supporto per più spazi dei nomi
Per abilitare il supporto di più spazi dei nomi:
1. modificare il file [helm-config.yaml](#sample-helm-config-file) in uno dei seguenti modi:
   - eliminare `watchNamespace` completamente la chiave da [helm-config.yaml](#sample-helm-config-file) - AGIC osserverà tutti gli spazi dei nomi
   - impostato `watchNamespace` su una stringa vuota - AGIC osserverà tutti gli spazi dei nomi
   - aggiungere più spazi dei nomi`watchNamespace: default,secondNamespace`separati da una virgola ( ) - AGIC osserverà questi spazi dei nomi in modo esclusivo
2. applicare le modifiche al modello Helm con:`helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure`

Una volta distribuito con la possibilità di osservare più spazi dei nomi, AGIC:
  - elenca le risorse in ingresso da tutti gli spazi dei nomi accessibili
  - filtrare per entrare risorse annotato con`kubernetes.io/ingress.class: azure/application-gateway`
  - comporre la configurazione combinata [del gateway applicazione](https://github.com/Azure/azure-sdk-for-go/blob/37f3f4162dfce955ef5225ead57216cf8c1b2c70/services/network/mgmt/2016-06-01/network/models.go#L1710-L1744)
  - applicare la configurazione al gateway applicazione associato tramite [ARM](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)

## <a name="conflicting-configurations"></a>Configurazioni in conflitto
Più [risorse in ingresso](https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource) con spazi dei nomi possono indicare ad AGIC di creare configurazioni in conflitto per un singolo gateway applicazione. (Due in ingresso che rivendicano lo stesso dominio per esempio.)

Nella parte superiore della gerarchia: **i listener** (indirizzo IP, porta e host) e le regole di **routing** (listener di associazione, pool back-end e impostazioni HTTP) possono essere creati e condivisi da più spazi dei nomi/iningresso.

D'altra parte: i percorsi, i pool back-end, le impostazioni HTTP e i certificati TLS possono essere creati da un solo spazio dei nomi e i duplicati verranno rimossi.

Si considerino ad esempio le seguenti `staging` `production` risorse `www.contoso.com`in ingresso duplicate definite spazi dei nomi e per:For example, consider the following duplicate ingress resources defined namespaces and for :
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-ingress
  namespace: staging
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: www.contoso.com
      http:
        paths:
          - backend:
              serviceName: web-service
              servicePort: 80
```

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-ingress
  namespace: production
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: www.contoso.com
      http:
        paths:
          - backend:
              serviceName: web-service
              servicePort: 80
```

Nonostante le due risorse in `www.contoso.com` ingresso che richiedono il traffico per essere instradato ai rispettivi spazi dei nomi Kubernetes, solo un back-end può servire il traffico. AGIC creerebbe una configurazione su base "primo arrivato, primo servito" per una delle risorse. Se due risorse in ingresso vengono create contemporaneamente, quella precedente nell'alfabeto avrà la precedenza. Dall'esempio precedente saremo in grado di `production` creare solo le impostazioni per l'ingresso. Il gateway applicazione verrà configurato con le risorse seguenti:Application Gateway will be configured with the following resources:

  - Listener:`fl-www.contoso.com-80`
  - Regola di routing:`rr-www.contoso.com-80`
  - Pool back-end:`pool-production-contoso-web-service-80-bp-80`
  - Impostazioni HTTP:`bp-production-contoso-web-service-80-80-websocket-ingress`
  - Sonda di salute:`pb-production-contoso-web-service-80-websocket-ingress`

Si noti che, ad eccezione del *listener* e della`production`regola di *routing,* le risorse del gateway applicazione create includono il nome dello spazio dei nomi ( ) per il quale sono state create.

Se le due risorse in ingresso vengono introdotte nel cluster AKS in momenti diversi nel tempo, è probabile che AGIC `namespace-B` finisca in uno scenario in cui riconfigura il gateway applicazione e reindirizza il traffico da a `namespace-A`.

Ad esempio, `staging` se è stato aggiunto per primo, AGIC configurerà il gateway applicazione per instradare il traffico al pool back-end di gestione temporanea. In una fase `production` successiva, l'introduzione in ingresso causerà AGIC riprogrammare `production` il gateway applicazione, che avvierà il routing del traffico al pool back-end.

## <a name="restrict-access-to-namespaces"></a>Limitare l'accesso agli spazi dei nomi
Per impostazione predefinita, AGIC configurerà il gateway applicazione in base all'ingresso con annotato all'interno di qualsiasi spazio dei nomi. Se si desidera limitare questo comportamento sono disponibili le opzioni seguenti:
  - limitare gli spazi dei nomi, definendo in `watchNamespace` modo esplicito gli spazi dei nomi che AGIC deve osservare tramite la chiave YAML in [helm-config.yaml](#sample-helm-config-file)
  - utilizzare [Role/RoleBinding](https://docs.microsoft.com/azure/aks/azure-ad-rbac) per limitare AGIC a spazi dei nomi specifici

## <a name="sample-helm-config-file"></a>File di configurazione Helm di esempio
```yaml
    # This file contains the essential configs for the ingress controller helm chart

    # Verbosity level of the App Gateway Ingress Controller
    verbosityLevel: 3
    
    ################################################################################
    # Specify which application gateway the ingress controller will manage
    #
    appgw:
        subscriptionId: <subscriptionId>
        resourceGroup: <resourceGroupName>
        name: <applicationGatewayName>
    
        # Setting appgw.shared to "true" will create an AzureIngressProhibitedTarget CRD.
        # This prohibits AGIC from applying config for any host/path.
        # Use "kubectl get AzureIngressProhibitedTargets" to view and change this.
        shared: false
    
    ################################################################################
    # Specify which kubernetes namespace the ingress controller will watch
    # Default value is "default"
    # Leaving this variable out or setting it to blank or empty string would
    # result in Ingress Controller observing all acessible namespaces.
    #
    # kubernetes:
    #   watchNamespace: <namespace>
    
    ################################################################################
    # Specify the authentication with Azure Resource Manager
    #
    # Two authentication methods are available:
    # - Option 1: AAD-Pod-Identity (https://github.com/Azure/aad-pod-identity)
    armAuth:
        type: aadPodIdentity
        identityResourceID: <identityResourceId>
        identityClientID:  <identityClientId>
    
    ## Alternatively you can use Service Principal credentials
    # armAuth:
    #    type: servicePrincipal
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

