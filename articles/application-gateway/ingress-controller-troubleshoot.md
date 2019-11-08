---
title: Risoluzione dei problemi del controller di ingresso del gateway applicazione
description: Questo articolo fornisce la documentazione su come risolvere i problemi e/o le domande comuni con il controller di ingresso del gateway applicazione.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a64a9ce5e080308674893273e90a0e83686e339e
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795505"
---
# <a name="troubleshoot-common-questions-or-issues-with-ingress-controller"></a>Risolvere problemi comuni o problemi con il controller di ingresso

[Azure cloud Shell](https://shell.azure.com/) è il modo più pratico per risolvere eventuali problemi con l'installazione di AKS e AGIC. Avviare la shell da [Shell.Azure.com](https://shell.azure.com/) o facendo clic sul collegamento:

[![Incorpora avvio](https://shell.azure.com/images/launchcloudshell.png "Avviare Azure Cloud Shell")](https://shell.azure.com)


## <a name="test-with-a-simple-kubernetes-app"></a>Eseguire test con una semplice app Kubernetes

I passaggi seguenti presuppongono:
  - Si dispone di un cluster AKS con rete avanzata abilitata
  - AGIC è stato installato nel cluster AKS
  - Si dispone già di un gateway applicazione in una VNET condivisa con il cluster AKS

Per verificare che l'installazione del gateway applicazione + AKS + AGIC sia configurata correttamente, distribuire la più semplice app possibile:

```bash
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: test-agic-app-pod
  labels:
    app: test-agic-app
spec:
  containers:
  - image: "mcr.microsoft.com/dotnet/core/samples:aspnetapp"
    name: aspnetapp-image
    ports:
    - containerPort: 80
      protocol: TCP
---
apiVersion: v1
kind: Service
metadata:
  name: test-agic-app-service
spec:
  selector:
    app: test-agic-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: test-agic-app-ingress
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: test.agic.contoso.com
      http:
        paths:
          - path: /
            backend:
              serviceName: test-agic-app-service
              servicePort: 80
EOF
```

Copiare e incollare tutte le righe contemporaneamente dallo script precedente in un [Azure cloud Shell](https://shell.azure.com/). Assicurarsi che l'intero comando venga copiato, a partire da `cat` e includa l'ultimo `EOF`.

![apply](./media/application-gateway-ingress-controller-troubleshooting/tsg--apply-config.png)

Una volta completata la distribuzione dell'app sopra il cluster AKS, sarà presente un nuovo pod, un servizio e un ingresso.

Ottenere l'elenco di Pod con [cloud Shell](https://shell.azure.com/): `kubectl get pods -o wide`.
Si prevede che un pod denominato ' test-Agic-app-pod ' sia stato creato. Avrà un indirizzo IP. Questo indirizzo deve trovarsi all'interno della VNET del gateway applicazione, che viene usato con AKS.

![Baccelli](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-pods.png)

Ottenere l'elenco dei servizi: `kubectl get services -o wide`. Si prevede di visualizzare un servizio denominato "test-Agic-app-Service".

![Baccelli](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-services.png)

Ottiene l'elenco dei `kubectl get ingress`di ingresso. È prevista la creazione di una risorsa di ingresso denominata ' test-Agic-app-ingress '. La risorsa avrà un nome host ' test.agic.contoso.com '.

![Baccelli](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-ingress.png)

Uno dei pod sarà AGIC. in `kubectl get pods` sarà visualizzato un elenco di Pod, uno dei quali inizierà con "ingresso-Azure". Ottenere tutti i log del Pod con `kubectl logs <name-of-ingress-controller-pod>` per verificare che la distribuzione sia stata completata correttamente. Una distribuzione riuscita avrebbe aggiunto le righe seguenti al log:
```
I0927 22:34:51.281437       1 process.go:156] Applied Application Gateway config in 20.461335266s
I0927 22:34:51.281585       1 process.go:165] cache: Updated with latest applied config.
I0927 22:34:51.282342       1 process.go:171] END AppGateway deployment
```

In alternativa, da [cloud Shell](https://shell.azure.com/) è possibile recuperare solo le righe che indicano la corretta configurazione del gateway applicazione con `kubectl logs <ingress-azure-....> | grep 'Applied App Gateway config in'`, in cui `<ingress-azure....>` deve corrispondere al nome esatto del Pod AGIC.

Il gateway applicazione avrà la seguente configurazione applicata:

- Listener:](./media/application-gateway-ingress-controller-troubleshooting/tsg--listeners.png) listener ![

- Regola di routing: ![routing_rule](./media/application-gateway-ingress-controller-troubleshooting/tsg--rule.png)

- Pool back-end:
  - Nel pool di indirizzi back-end sarà presente un solo indirizzo IP, che corrisponderà all'indirizzo IP del Pod osservato in precedenza con `kubectl get pods -o wide`
![backend_pool](./media/application-gateway-ingress-controller-troubleshooting/tsg--backendpools.png)


Infine, è possibile usare il comando `cURL` dall'interno di [cloud Shell](https://shell.azure.com/) per stabilire una connessione HTTP all'app appena distribuita:

1. Usare `kubectl get ingress` per ottenere l'indirizzo IP pubblico del gateway applicazione
2. USA `curl -I -H 'test.agic.contoso.com' <publitc-ip-address-from-previous-command>`

![Baccelli](./media/application-gateway-ingress-controller-troubleshooting/tsg--curl.png)

Il risultato di `HTTP/1.1 200 OK` indica che il sistema gateway applicazione + AKS + AGIC funziona come previsto.


## <a name="inspect-kubernetes-installation"></a>Controllare l'installazione di Kubernetes

### <a name="pods-services-ingress"></a>Pod, servizi, ingresso
Il controller di ingresso del gateway applicazione (AGIC) monitora continuamente le risorse Kubernetes seguenti: [distribuzione](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#creating-a-deployment) o [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/#what-is-a-pod), [servizio](https://kubernetes.io/docs/concepts/services-networking/service/), [ingresso](https://kubernetes.io/docs/concepts/services-networking/ingress/)


Per il corretto funzionamento di AGIC è necessario quanto segue:
  1. AKS deve avere uno o più **Pod**integri.
     Verificare questa operazione da [cloud Shell](https://shell.azure.com/) con `kubectl get pods -o wide --show-labels` se si dispone di un pod con un `apsnetapp`, l'output potrebbe essere simile al seguente:
     ```bash
     delyan@Azure:~$ kubectl get pods -o wide --show-labels

     NAME                   READY   STATUS    RESTARTS   AGE   IP          NODE                       NOMINATED NODE   READINESS GATES   LABELS
     aspnetapp              1/1     Running   0          17h   10.0.0.6    aks-agentpool-35064155-1   <none>           <none>            app=aspnetapp
     ```

  2. Uno o più **Servizi**, che fanno riferimento ai pod precedenti tramite la corrispondenza di etichette `selector`.
     Verificare questa operazione da [cloud Shell](https://shell.azure.com/) con `kubectl get services -o wide`
     ```bash
     delyan@Azure:~$ kubectl get services -o wide --show-labels

     NAME                TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE   SELECTOR        LABELS
     aspnetapp           ClusterIP   10.2.63.254    <none>        80/TCP    17h   app=aspnetapp   <none>     
     ```

  3. Il traffico in **ingresso**, annotato con `kubernetes.io/ingress.class: azure/application-gateway`, che fa riferimento al servizio precedente, ne verifica l' [cloud Shell](https://shell.azure.com/) con `kubectl get ingress -o wide --show-labels`
     ```bash
     delyan@Azure:~$ kubectl get ingress -o wide --show-labels

     NAME        HOSTS   ADDRESS   PORTS   AGE   LABELS
     aspnetapp   *                 80      17h   <none>
     ```

  4. Visualizzare le annotazioni del traffico in ingresso precedente: `kubectl get ingress aspnetapp -o yaml` (sostituire `aspnetapp` con il nome del traffico in ingresso)
     ```bash
     delyan@Azure:~$ kubectl get ingress aspnetapp -o yaml

     apiVersion: extensions/v1beta1
     kind: Ingress
     metadata:
       annotations:
         kubernetes.io/ingress.class: azure/application-gateway
       name: aspnetapp
     spec:
       backend:
         serviceName: aspnetapp
         servicePort: 80
     ```

     La risorsa di ingresso deve essere annotata con `kubernetes.io/ingress.class: azure/application-gateway`.
 

### <a name="verify-observed-namespace"></a>Verificare lo spazio dei nomi osservato

* Ottenere gli spazi dei nomi esistenti nel cluster Kubernetes. Lo spazio dei nomi in cui viene eseguita l'app AGIC sta guardando lo spazio dei nomi? Per informazioni su come configurare correttamente gli spazi dei nomi osservati, vedere la documentazione del [supporto per più spazi dei nomi](./ingress-controller-multiple-namespace-support.md#enable-multiple-namespace-support) .

    ```bash
    # What namespaces exist on your cluster
    kubectl get namespaces
    
    # What pods are currently running
    kubectl get pods --all-namespaces -o wide
    ```


* Il Pod AGIC deve trovarsi nello spazio dei nomi `default` (vedere la colonna `NAMESPACE`). Un pod integro avrebbe `Running` nella colonna `STATUS`. Deve essere presente almeno un pod AGIC.

    ```bash
    # Get a list of the Application Gateway Ingress Controller pods
    kubectl get pods --all-namespaces --selector app=ingress-azure
    ```


* Se il Pod AGIC non è integro (`STATUS` colonna del comando precedente non è `Running`):
  - ottenere i log per comprendere perché: `kubectl logs <pod-name>`
  - per l'istanza precedente del Pod: `kubectl logs <pod-name> --previous`
  - descrivere il pod per ottenere più contesto: `kubectl describe pod <pod-name>`


* Si dispone di un [servizio](https://kubernetes.io/docs/concepts/services-networking/service/) Kubernetes e di risorse in [ingresso](https://kubernetes.io/docs/concepts/services-networking/ingress/) ?
    
    ```bash
    # Get all services across all namespaces
    kubectl get service --all-namespaces -o wide
    
    # Get all ingress resources across all namespaces
    kubectl get ingress --all-namespaces -o wide
    ```


* Il traffico in [ingresso](https://kubernetes.io/docs/concepts/services-networking/ingress/) viene annotato con: `kubernetes.io/ingress.class: azure/application-gateway`? AGIC controllerà solo per le risorse in ingresso Kubernetes con questa annotazione.
    
    ```bash
    # Get the YAML definition of a particular ingress resource
    kubectl get ingress --namespace  <which-namespace?>  <which-ingress?>  -o yaml
    ```


* AGIC genera eventi Kubernetes per determinati errori critici. È possibile visualizzare i seguenti:
  - nel terminale tramite `kubectl get events --sort-by=.metadata.creationTimestamp`
  - nel browser usando l' [interfaccia utente Web di Kubernetesri (Dashboard)](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)


## <a name="logging-levels"></a>Livello di registrazione

AGIC dispone di 3 livelli di registrazione. Il livello 1 è quello predefinito e Mostra un numero minimo di righe di log.
Il livello 5, d'altra parte, Visualizza tutti i log, incluso il contenuto purificato della configurazione applicata a ARM.

La community di Kubernetes ha stabilito 9 livelli di registrazione per lo strumento [kubectl](https://kubernetes.io/docs/reference/kubectl/cheatsheet/#kubectl-output-verbosity-and-debugging) . In questo repository si utilizzano tre di questi, con una semantica simile:


| Livello di dettaglio | Descrizione |
|-----------|-------------|
|  1        | Livello di registrazione predefinito; Mostra i dettagli dell'avvio, gli avvisi e gli errori |
|  3        | Informazioni estese su eventi e modifiche; elenchi di oggetti creati |
|  5        | Registra gli oggetti sottoposti a marshalling; Mostra la configurazione JSON purificata applicata a ARM |


I livelli di dettaglio sono regolabili tramite la variabile `verbosityLevel` nel file [Helm-config. YAML](#sample-helm-config-file) . Aumentare il livello di dettaglio per `5` per ottenere la configurazione JSON inviata a [ARM](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview):
  - aggiungere `verbosityLevel: 5` su una riga in [Helm-config. YAML](#sample-helm-config-file) e reinstallare
  - ottenere i log con `kubectl logs <pod-name>`

### <a name="sample-helm-config-file"></a>File di configurazione Helm di esempio
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

