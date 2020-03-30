---
title: Risoluzione dei problemi relativi al controller in ingresso del gateway applicazione
description: In questo articolo viene fornita la documentazione su come risolvere le domande comuni e/o problemi con il controller di ingresso del gateway applicazione.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a64a9ce5e080308674893273e90a0e83686e339e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795505"
---
# <a name="troubleshoot-common-questions-or-issues-with-ingress-controller"></a>Risolvere i problemi comuni relativi a domande o problemi relativi al controller in ingressoTroubleshoot common questions or issues with Ingress Controller

[Azure Cloud Shell](https://shell.azure.com/) è il modo più pratico per risolvere eventuali problemi con l'installazione di AKS e AGIC. Avviare la shell da [shell.azure.com](https://shell.azure.com/) o facendo clic sul collegamento:

[![Lancio dell'incorporamento](https://shell.azure.com/images/launchcloudshell.png "Avviare Azure Cloud Shell")](https://shell.azure.com)


## <a name="test-with-a-simple-kubernetes-app"></a>Esegui test con una semplice app Kubernetes

I passaggi seguenti presuppongono quanto segue:
  - Si dispone di un cluster AKS, con rete avanzata abilitata
  - AGIC è stato installato nel cluster AKS
  - Si dispone già di un gateway applicazione in una rete virtuale condivisa con il cluster AKS

Per verificare che l'installazione del gateway applicazione , AKS e AGIC sia configurata correttamente, distribuire l'app più semplice possibile:

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

Copiare e incollare tutte le righe contemporaneamente dallo script precedente in [Azure Cloud Shell.](https://shell.azure.com/) Si prega di assicurarsi che `cat` l'intero `EOF`comando viene copiato - a partire da e compreso l'ultimo .

![apply](./media/application-gateway-ingress-controller-troubleshooting/tsg--apply-config.png)

Dopo una corretta distribuzione dell'app sopra il cluster AKS avrà un nuovo Pod, Service e un Ingress.

Ottieni l'elenco dei pod `kubectl get pods -o wide`con [Cloud Shell:](https://shell.azure.com/).
Ci aspettiamo che sia stato creato un pod denominato 'test-agic-app-pod'. Avrà un indirizzo IP. Questo indirizzo deve trovarsi all'interno della rete virtuale del gateway applicazione, utilizzato con AKS.

![Baccelli](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-pods.png)

Ottenere l'elenco `kubectl get services -o wide`dei servizi: . Ci aspettiamo di vedere un servizio denominato 'test-agic-app-service'.

![Baccelli](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-services.png)

Ottenere l'elenco degli ingressioni: `kubectl get ingress`. Prevediamo che è stata creata una risorsa Ingress denominata 'test-agic-app-ingress'. La risorsa avrà un nome host 'test.agic.contoso.com'.

![Baccelli](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-ingress.png)

Uno dei baccelli sarà AGIC. `kubectl get pods`mostrerà un elenco di pod, uno dei quali inizierà con 'ingress-azure'. Ottenere tutti i log `kubectl logs <name-of-ingress-controller-pod>` di tale pod con per verificare che la distribuzione è stata eseguita correttamente. Una distribuzione corretta avrebbe aggiunto le seguenti righe al registro:
```
I0927 22:34:51.281437       1 process.go:156] Applied Application Gateway config in 20.461335266s
I0927 22:34:51.281585       1 process.go:165] cache: Updated with latest applied config.
I0927 22:34:51.282342       1 process.go:171] END AppGateway deployment
```

In alternativa, da [Cloud Shell](https://shell.azure.com/) è possibile recuperare solo `kubectl logs <ingress-azure-....> | grep 'Applied App Gateway config in'`le `<ingress-azure....>` righe che indicano la corretta configurazione del gateway applicazione con , dove deve essere il nome esatto del pod AGIC.

Al gateway applicazione verrà applicata la configurazione seguente:Application Gateway will have the following configuration applied:

- Listener: ![listener](./media/application-gateway-ingress-controller-troubleshooting/tsg--listeners.png)

- Regola di ![routing: routing_rule](./media/application-gateway-ingress-controller-troubleshooting/tsg--rule.png)

- Pool back-end:
  - Ci sarà un indirizzo IP nel pool di indirizzi back-end e corrisponderà `kubectl get pods -o wide` 
 ![all'indirizzo IP del Pod che abbiamo osservato in precedenza con backend_pool](./media/application-gateway-ingress-controller-troubleshooting/tsg--backendpools.png)


Infine possiamo usare `cURL` il comando dall'interno di [Cloud Shell](https://shell.azure.com/) per stabilire una connessione HTTP all'app appena distribuita:

1. Utilizzare `kubectl get ingress` per ottenere l'indirizzo IP pubblico del gateway applicazione
2. Utilizzare `curl -I -H 'test.agic.contoso.com' <publitc-ip-address-from-previous-command>`.

![Baccelli](./media/application-gateway-ingress-controller-troubleshooting/tsg--curl.png)

Il risultato indica `HTTP/1.1 200 OK` che il gateway dell'applicazione , il sistema AKS e AGIC funziona come previsto.


## <a name="inspect-kubernetes-installation"></a>Ispezionare l'installazione di Kubernetes

### <a name="pods-services-ingress"></a>Pod, Servizi, Ingresso
Application Gateway Ingress Controller (AGIC) monitora continuamente le seguenti risorse Kubernetes: [Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#creating-a-deployment) o [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/#what-is-a-pod), [Service](https://kubernetes.io/docs/concepts/services-networking/service/), [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/)


Affinché AGIC funzioni come previsto, è necessario che sia necessario quanto segue:
  1. AKS deve avere uno o più **baccelli**sani.
     Verificare questo da [Cloud Shell](https://shell.azure.com/) con `kubectl get pods -o wide --show-labels` `apsnetapp`Se si dispone di un Pod con un , l'output potrebbe essere simile al seguente:
     ```bash
     delyan@Azure:~$ kubectl get pods -o wide --show-labels

     NAME                   READY   STATUS    RESTARTS   AGE   IP          NODE                       NOMINATED NODE   READINESS GATES   LABELS
     aspnetapp              1/1     Running   0          17h   10.0.0.6    aks-agentpool-35064155-1   <none>           <none>            app=aspnetapp
     ```

  2. Uno o più **servizi**, che fanno `selector` riferimento ai pod precedenti tramite etichette corrispondenti.
     Verificare questo da [Cloud Shell](https://shell.azure.com/) con`kubectl get services -o wide`
     ```bash
     delyan@Azure:~$ kubectl get services -o wide --show-labels

     NAME                TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE   SELECTOR        LABELS
     aspnetapp           ClusterIP   10.2.63.254    <none>        80/TCP    17h   app=aspnetapp   <none>     
     ```

  3. **Ingress**, annotato `kubernetes.io/ingress.class: azure/application-gateway`con , che fa riferimento al servizio precedente Verificare da [Cloud Shell](https://shell.azure.com/) con`kubectl get ingress -o wide --show-labels`
     ```bash
     delyan@Azure:~$ kubectl get ingress -o wide --show-labels

     NAME        HOSTS   ADDRESS   PORTS   AGE   LABELS
     aspnetapp   *                 80      17h   <none>
     ```

  4. Visualizzare le annotazioni dell'ingresso `kubectl get ingress aspnetapp -o yaml` `aspnetapp` sopra: (sostituire con il nome dell'ingresso)
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

     La risorsa in ingresso deve essere `kubernetes.io/ingress.class: azure/application-gateway`annotata con .
 

### <a name="verify-observed-namespace"></a>Verifica dello spazio dei nomi osservatoVerify Observed Namespace

* Ottenere gli spazi dei nomi esistenti nel cluster Kubernetes.Get the existing namespaces in Kubernetes cluster. In quale spazio dei nomi è in esecuzione l'app? AGIC sta guardando quello spazio dei nomi? Fare riferimento alla documentazione [Supporto dello spazio dei nomi multipli](./ingress-controller-multiple-namespace-support.md#enable-multiple-namespace-support) su come configurare correttamente gli spazi dei nomi osservati.

    ```bash
    # What namespaces exist on your cluster
    kubectl get namespaces
    
    # What pods are currently running
    kubectl get pods --all-namespaces -o wide
    ```


* Il pod AGIC deve `default` trovarsi `NAMESPACE`nello spazio dei nomi (vedere la colonna ). Un baccello `Running` sano `STATUS` avrebbe nella colonna. Dovrebbe essere presente almeno un baccello AGIC.

    ```bash
    # Get a list of the Application Gateway Ingress Controller pods
    kubectl get pods --all-namespaces --selector app=ingress-azure
    ```


* Se il contenitore AGIC`STATUS` non è integro `Running`( la colonna del comando precedente non è ):
  - ottenere i registri per capire perché:`kubectl logs <pod-name>`
  - per l'istanza precedente del pod:`kubectl logs <pod-name> --previous`
  - descrivere il pod per ottenere più contesto:`kubectl describe pod <pod-name>`


* Hai un [servizio](https://kubernetes.io/docs/concepts/services-networking/service/) Kubernetes e le risorse [Ingress?](https://kubernetes.io/docs/concepts/services-networking/ingress/)
    
    ```bash
    # Get all services across all namespaces
    kubectl get service --all-namespaces -o wide
    
    # Get all ingress resources across all namespaces
    kubectl get ingress --all-namespaces -o wide
    ```


* Il tuo [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) è annotato con: `kubernetes.io/ingress.class: azure/application-gateway`? AGIC guarderà solo le risorse Kubernetes Ingress che hanno questa annotazione.
    
    ```bash
    # Get the YAML definition of a particular ingress resource
    kubectl get ingress --namespace  <which-namespace?>  <which-ingress?>  -o yaml
    ```


* AGIC genera eventi Kubernetes per determinati errori critici. È possibile visualizzare questi elementi:
  - nel terminale tramite`kubectl get events --sort-by=.metadata.creationTimestamp`
  - nel browser utilizzando [l'interfaccia utente Web Kubernetes (Dashboard)](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)


## <a name="logging-levels"></a>Livelli di registrazione

AGIC ha 3 livelli di registrazione. Livello 1 è quello predefinito e mostra il numero minimo di righe di registro.
Il livello 5, d'altra parte, visualizza tutti i log, incluso il contenuto sanitato della configurazione applicato a ARM.

La comunità di Kubernetes ha stabilito 9 livelli di registrazione per lo strumento [kubectl.](https://kubernetes.io/docs/reference/kubectl/cheatsheet/#kubectl-output-verbosity-and-debugging) In questo repository stiamo utilizzando 3 di questi, con una semantica simile:


| Livello di dettaglio | Descrizione |
|-----------|-------------|
|  1        | Livello di registrazione predefinito; mostra i dettagli di avvio, gli avvisi e gli errori |
|  3        | Informazioni estese su eventi e modifiche; elenchi di oggetti creati |
|  5        | Registra gli oggetti sottoposti a marshalling; mostra la configurazione JSON sanificata applicata ad ARM |


I livelli di dettaglio sono `verbosityLevel` regolabili tramite la variabile nel file [helm-config.yaml.](#sample-helm-config-file) Aumentare il livello `5` di dettaglio per inviare la configurazione JSON ad [ARM:](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)
  - aggiungere `verbosityLevel: 5` su una riga da solo in [helm-config.yaml](#sample-helm-config-file) e reinstallare
  - ottenere i registri con`kubectl logs <pod-name>`

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

