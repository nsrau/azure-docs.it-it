---
title: Abilitare più supporti dello spazio dei nomi per il controller di ingresso del gateway applicazione
description: Questo articolo fornisce informazioni su come abilitare più supporto dello spazio dei nomi in un cluster Kubernetes con un controller di ingresso del gateway applicazione.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 10/23/2019
ms.author: caya
ms.openlocfilehash: 889cc603e339d40270d9c4585ed8f1a19c6dcf0b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513510"
---
# <a name="enable-multiple-namespace-support-in-an-aks-cluster-with-application-gateway-ingress-controller"></a>Abilitare più supporto dello spazio dei nomi in un cluster AKS con il controller di ingresso del gateway applicazione

## <a name="motivation"></a>Motivazione
Gli [spazi dei nomi](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) Kubernetes rendono possibile il partizionamento e l'allocazione di un cluster Kubernetes a sottogruppi di un team più grande. Questi sottoteam possono quindi distribuire e gestire l'infrastruttura con controlli più precisi delle risorse, della sicurezza, della configurazione e così via. Kubernetes consente di definire una o più risorse in ingresso in modo indipendente all'interno di ogni spazio dei nomi.

A partire dalla versione 0,7 [applicazione Azure gateway Kubernetes IngressController](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/README.md) (AGIC) può inserire eventi da e osservare più spazi dei nomi. Se l'amministratore AKS decide di usare il [gateway app](https://azure.microsoft.com/services/application-gateway/) come ingresso, tutti gli spazi dei nomi utilizzeranno la stessa istanza del gateway applicazione. Una singola installazione del controller di ingresso monitorerà gli spazi dei nomi accessibili e configurerà il gateway applicazione a cui è associato.

La versione 0,7 di AGIC continuerà a osservare esclusivamente lo spazio dei nomi `default`, a meno che questo non venga esplicitamente modificato in uno o più spazi dei nomi diversi nella configurazione Helm (vedere la sezione seguente).

## <a name="enable-multiple-namespace-support"></a>Abilita supporto per più spazi dei nomi
Per abilitare il supporto di più spazi dei nomi:
1. modificare il file [Helm-config. YAML](#sample-helm-config-file) in uno dei modi seguenti:
   - eliminare la chiave di `watchNamespace` interamente da [Helm-config. YAML](#sample-helm-config-file) -AGIC osserverà tutti gli spazi dei nomi
   - impostare `watchNamespace` su una stringa vuota-AGIC osserverà tutti gli spazi dei nomi
   - aggiungere più spazi dei nomi separati da una virgola (`watchNamespace: default,secondNamespace`)-AGIC osserverà questi spazi dei nomi in modo esclusivo
2. applicare le modifiche al modello Helm con: `helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure`

Una volta distribuita con la possibilità di osservare più spazi dei nomi, AGIC:
  - elencare le risorse in ingresso da tutti gli spazi dei nomi accessibili
  - filtrare per le risorse in ingresso annotate con `kubernetes.io/ingress.class: azure/application-gateway`
  - comporre la [configurazione del gateway applicazione](https://github.com/Azure/azure-sdk-for-go/blob/37f3f4162dfce955ef5225ead57216cf8c1b2c70/services/network/mgmt/2016-06-01/network/models.go#L1710-L1744) combinato
  - applicare la configurazione al gateway applicazione associato tramite [ARM](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)

## <a name="conflicting-configurations"></a>Configurazioni in conflitto
Più risorse di [ingresso](https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource) con spazio dei nomi possono indicare a AGIC di creare configurazioni in conflitto per un singolo gateway applicazione. (Due ingress che rivendicano lo stesso dominio per l'istanza).

Nella parte superiore dei **listener** della gerarchia (indirizzo IP, porta e host) e **le regole di routing** (listener di binding, pool back-end e impostazioni http) potrebbero essere create e condivise da più spazi dei nomi/Ingres.

Negli altri percorsi, i pool back-end, le impostazioni HTTP e i certificati TLS possono essere creati solo da uno spazio dei nomi e i duplicati verranno rimossi.

Si considerino, ad esempio, i seguenti spazi dei nomi definiti per le risorse di ingresso duplicate `staging` e `production` per `www.contoso.com`:
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

Nonostante le due risorse in ingresso che richiedono il traffico per `www.contoso.com` essere indirizzate ai rispettivi spazi dei nomi Kubernetes, solo un back-end può servire il traffico. AGIC creerebbe una configurazione in base a una delle risorse per la prima volta. Se vengono create contemporaneamente due risorse in ingresso, quella precedente nell'alfabeto avrà la precedenza. Dall'esempio precedente, sarà possibile creare le impostazioni per il `production` in ingresso. Il gateway applicazione verrà configurato con le risorse seguenti:

  - Listener: `fl-www.contoso.com-80`
  - Regola di routing: `rr-www.contoso.com-80`
  - Pool back-end: `pool-production-contoso-web-service-80-bp-80`
  - Impostazioni HTTP: `bp-production-contoso-web-service-80-80-websocket-ingress`
  - Probe di integrità: `pb-production-contoso-web-service-80-websocket-ingress`

Si noti che, ad eccezione del *listener* e della *regola di routing*, le risorse del gateway applicazione create includono il nome dello spazio dei nomi (`production`) per il quale sono state create.

Se le due risorse in ingresso vengono introdotte nel cluster AKS in momenti diversi, è probabile che AGIC si trovi in uno scenario in cui riconfigura il gateway applicazione e reindirizza il traffico da `namespace-B` a `namespace-A`.

Se ad esempio è stata aggiunta `staging` prima, AGIC configurerà il gateway applicazione per instradare il traffico al pool back-end di gestione temporanea. In una fase successiva, introducendo `production` in ingresso, AGIC riprogramma il gateway applicazione, che avvierà il routing del traffico al pool back-end `production`.

## <a name="restrict-access-to-namespaces"></a>Limitare l'accesso agli spazi dei nomi
Per impostazione predefinita, AGIC configurerà il gateway applicazione in base all'ingresso con annotazioni all'interno di qualsiasi spazio dei nomi. Se si desidera limitare questo comportamento, sono disponibili le opzioni seguenti:
  - limitare gli spazi dei nomi, definendo in modo esplicito gli spazi dei nomi AGIC dovrebbe osservare tramite il `watchNamespace` chiave YAML in [Helm-config. YAML](#sample-helm-config-file)
  - utilizzare [Role/Role](https://docs.microsoft.com/azure/aks/azure-ad-rbac) per limitare AGIC a spazi dei nomi specifici

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

