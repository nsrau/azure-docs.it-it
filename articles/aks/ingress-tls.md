---
title: Creare il traffico in ingresso con TLS automatico
titleSuffix: Azure Kubernetes Service
description: Informazioni su come installare e configurare un controller di ingresso NGINX che usa la crittografia per la generazione automatica di certificati TLS in un cluster Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 30fb0d000a64c7e460dd0ccf7e7eaf4b67957c8c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91335569"
---
# <a name="create-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>Creare un controller di ingresso HTTPS nel servizio Azure Kubernetes

Un controller di ingresso è un componente software che fornisce proxy inverso, routing del traffico configurabile e terminazione TLS per i servizi Kubernetes. Le risorse di ingresso Kubernetes vengono usate per configurare le regole di ingresso e le route per i singoli servizi Kubernetes. Usando un controller di ingresso e regole di ingresso è possibile servirsi di un singolo indirizzo IP per instradare il traffico a più servizi in un cluster Kubernetes.

Questo articolo illustra come distribuire il [controller di ingresso NGINX][nginx-ingress] in un cluster del servizio Azure Kubernetes. Il progetto [cert-manager][cert-manager] viene usato per generare e configurare automaticamente certificati [Let's Encrypt][lets-encrypt]. Infine, due applicazioni vengono eseguite nel cluster servizio Azure Kubernetes, ognuna delle quali è accessibile tramite un singolo indirizzo IP.

È anche possibile:

- [Creare un controller di ingresso di base con connettività di rete esterna][aks-ingress-basic]
- [Abilitare il componente aggiuntivo di routing dell'applicazione HTTP][aks-http-app-routing]
- [Creare un controller di ingresso che usa una rete privata interna e l'indirizzo IP][aks-ingress-internal]
- [Creare un controller di ingresso che usa i propri certificati TLS][aks-ingress-own-tls]
- [Creare un controller di ingresso che usa Let's Encrypt per generare automaticamente certificati TLS con un indirizzo IP pubblico statico][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster del servizio Azure Kubernetes esistente. Se è necessario un cluster del servizio Azure Kubernetes, vedere la guida di avvio rapido sul servizio Azure Kubernetes [Uso dell'interfaccia della riga di comando di Azure][aks-quickstart-cli] oppure [Uso del portale di Azure][aks-quickstart-portal].

Questo articolo presuppone anche che si disponga di [un dominio personalizzato][custom-domain] con una [zona DNS][dns-zone] nello stesso gruppo di risorse del cluster AKS.

Questo articolo usa [Helm 3][helm] per installare il controller di ingresso nginx e Cert-Manager. Assicurarsi di usare la versione più recente di Helm e di avere accesso ai repository di *ingresso-nginx* e *jetstack* Helm. Per istruzioni sull'aggiornamento, vedere la documentazione relativa all' [installazione di Helm][helm-install]. Per altre informazioni sulla configurazione e sull'uso di Helm, vedere [installare applicazioni con Helm in Azure Kubernetes Service (AKS)][use-helm].

Questo articolo richiede anche l'esecuzione dell'interfaccia della riga di comando di Azure versione 2.0.64 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Creare un controller di ingresso

Per creare il controller di ingresso, usare il `helm` comando per installare *nginx-ingress*. Per maggiore ridondanza, vengono distribuite due repliche dei controller di ingresso NGINX con il parametro `--set controller.replicaCount`. Per sfruttare appieno le repliche del controller di ingresso in esecuzione, assicurarsi che nel cluster servizio Azure Kubernetes siano presenti più nodi.

Il controller di ingresso deve anche essere pianificato in un nodo Linux. I nodi di Windows Server non devono eseguire il controller di ingresso. Un selettore di nodo viene specificato con il parametro `--set nodeSelector` per indicare all'utilità di pianificazione Kubernetes di eseguire il controller di ingresso NGINX in un nodo basato su Linux.

> [!TIP]
> L'esempio seguente crea uno spazio dei nomi Kubernetes per le risorse in ingresso denominate *ingress-Basic*. Specificare uno spazio dei nomi per il proprio ambiente in base alle esigenze.

> [!TIP]
> Per abilitare la [conservazione dell'indirizzo IP di origine client][client-source-ip] per le richieste ai contenitori nel cluster, aggiungere `--set controller.service.externalTrafficPolicy=Local` al comando Helm install. L'IP di origine del client viene archiviato nell'intestazione della richiesta sotto *X-inoltred-for*. Quando si usa un controller di ingresso con la conservazione IP dell'origine client abilitata, il pass-through TLS non funzionerà.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the ingress-nginx repository
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Durante l'installazione viene creato un indirizzo IP pubblico di Azure per il controller di ingresso. Questo indirizzo IP pubblico è statico per la durata di vita del controller di ingresso. Se si elimina il controller di ingresso, l'assegnazione dell'indirizzo IP pubblico viene persa. Se si crea quindi un controller di ingresso aggiuntivo, viene assegnato un nuovo indirizzo IP pubblico. Se si vuole mantenere l'uso dell'indirizzo IP pubblico, è possibile [creare un controller di ingresso con un indirizzo IP pubblico statico][aks-ingress-static-tls].

Per ottenere l'indirizzo IP pubblico, usare il comando `kubectl get service`. Per l'assegnazione dell'indirizzo IP al servizio, sono necessari pochi minuti.

```
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Non è ancora stata creata alcuna regola di ingresso. Se si passa all'indirizzo IP pubblico, viene visualizzata la pagina predefinita 404 del controller di ingresso NGINX.

## <a name="add-an-a-record-to-your-dns-zone"></a>Aggiungere un record a nella zona DNS

Aggiungere un *record a* nella zona DNS con l'indirizzo IP esterno del servizio nginx usando il comando [AZ network DNS record-set a Add-record][az-network-dns-record-set-a-add-record].

```console
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name * \
    --ipv4-address MY_EXTERNAL_IP
```

> [!NOTE]
> Facoltativamente, è possibile configurare un nome di dominio completo per l'indirizzo IP del controller di ingresso anziché un dominio personalizzato. Si noti che questo esempio è relativo a una shell bash.
> 
> ```azurecli-interactive
> # Public IP address of your ingress controller
> IP="MY_EXTERNAL_IP"
> 
> # Name to associate with public IP address
> DNSNAME="demo-aks-ingress"
> 
> # Get the resource-id of the public ip
> PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)
> 
> # Update public ip address with DNS name
> az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
> 
> # Display the FQDN
> az network public-ip show --ids $PUBLICIPID --query "[dnsSettings.fqdn]" --output tsv
> ```

## <a name="install-cert-manager"></a>Installare cert-manager

Il controller di ingresso NGINX supporta la terminazione TLS. Il recupero e la configurazione di certificati per HTTPS possono essere eseguiti in modi diversi. Questo articolo descrive l'uso di [cert-manager][cert-manager], che consente di generare automaticamente il certificato [Let's Encrypt][lets-encrypt] e che dispone di funzionalità di gestione.

Per installare il controller Cert-Manager:

```console
# Label the ingress-basic namespace to disable resource validation
kubectl label namespace ingress-basic cert-manager.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install \
  cert-manager \
  --namespace ingress-basic \
  --version v0.16.1 \
  --set installCRDs=true \
  --set nodeSelector."beta\.kubernetes\.io/os"=linux \
  jetstack/cert-manager
```

Per altre informazioni sulla configurazione di cert-manager, vedere [progetto cert-manager][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>Creare un'autorità di certificazione (CA) cluster

Per poter emettere i certificati, cert-manager richiede un [autorità di certificazione][cert-manager-issuer] o una risorsa [ClusterIssuer][cert-manager-cluster-issuer]. Queste risorse Kubernetes hanno funzionalità identiche, ma `Issuer` funziona in uno spazio dei nomi singolo, mentre `ClusterIssuer` funziona in tutti gli spazi dei nomi. Per altre informazioni, vedere la documentazione relativa all'[autorità di certificazione cert-manager][cert-manager-issuer].

Creare un'autorità di certificazione del cluster, ad esempio `cluster-issuer.yaml`, tramite il manifesto di esempio seguente. Aggiornare l'indirizzo di posta elettronica con un indirizzo valido della propria organizzazione:

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: ClusterIssuer
metadata:
  name: letsencrypt
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: MY_EMAIL_ADDRESS
    privateKeySecretRef:
      name: letsencrypt
    solvers:
    - http01:
        ingress:
          class: nginx
          podTemplate:
            spec:
              nodeSelector:
                "kubernetes.io/os": linux
```

Per creare l'autorità di certificazione, usare il comando `kubectl apply`.

```console
kubectl apply -f cluster-issuer.yaml
```

## <a name="run-demo-applications"></a>Eseguire applicazioni demo

Sono stati configurati un controller di ingresso e una soluzione di gestione dei certificati. A questo punto è possibile eseguire due applicazioni demo nel cluster servizio Azure Kubernetes. In questo esempio Helm viene usato per distribuire due istanze di una semplice applicazione *Hello World* .

Per visualizzare il controller di ingresso in azione, eseguire due applicazioni demo nel cluster AKS. In questo esempio si usa `kubectl apply` per distribuire due istanze di una semplice applicazione *Hello World* .

Creare un file *AKS-HelloWorld-One. YAML* e copiarlo nel seguente esempio YAML:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld-one
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld-one
  template:
    metadata:
      labels:
        app: aks-helloworld-one
    spec:
      containers:
      - name: aks-helloworld-one
        image: neilpeterson/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "Welcome to Azure Kubernetes Service (AKS)"
---
apiVersion: v1
kind: Service
metadata:
  name: aks-helloworld-one
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld-one
```

Creare un file *AKS-HelloWorld-Two. YAML* e copiarlo nel seguente esempio YAML:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld-two
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld-two
  template:
    metadata:
      labels:
        app: aks-helloworld-two
    spec:
      containers:
      - name: aks-helloworld-two
        image: neilpeterson/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "AKS Ingress Demo"
---
apiVersion: v1
kind: Service
metadata:
  name: aks-helloworld-two
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld-two
```

Eseguire le due applicazioni demo usando `kubectl apply` :

```console
kubectl apply -f aks-helloworld-one.yaml --namespace ingress-basic
kubectl apply -f aks-helloworld-two.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>Creare una route in ingresso

Entrambe le applicazioni sono in esecuzione nel cluster Kubernetes. Tuttavia, sono configurati con un servizio di tipo `ClusterIP` e non sono accessibili da Internet. Per renderle disponibili pubblicamente, creare una risorsa di ingresso Kubernetes. La risorsa di ingresso configura le regole che instradano il traffico a una delle due applicazioni.

Nell'esempio seguente, il traffico verso l'indirizzo *Hello-World-ingress. MY_CUSTOM_DOMAIN* viene indirizzato al servizio *AKS-HelloWorld* . Traffico verso l'indirizzo *Hello-World-ingress. MY_CUSTOM_DOMAIN/Hello-World-Two* viene indirizzato al servizio *AKS-HelloWorld-Two* . Traffico verso *Hello-World-ingress. MY_CUSTOM_DOMAIN/routing statico* viene indirizzato al servizio denominato *AKS-HelloWorld* per gli asset statici.

> [!NOTE]
> Se è stato configurato un FQDN per l'indirizzo IP del controller di ingresso anziché un dominio personalizzato, usare il nome di dominio completo anziché *Hello-World-ingress. MY_CUSTOM_DOMAIN*. Se, ad esempio, il nome di dominio completo è *demo-AKS-ingress.eastus.cloudapp.Azure.com*, sostituire *Hello-World-ingress. MY_CUSTOM_DOMAIN* con *demo-AKS-ingress.eastus.cloudapp.Azure.com* in `hello-world-ingress.yaml` .

Creare un file denominato `hello-world-ingress.yaml` usando l'esempio YAML. Aggiornare gli elementi *hosts* e *host* in base al nome DNS creato in un passaggio precedente.

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$1
    nginx.ingress.kubernetes.io/use-regex: "true"
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
    - hello-world-ingress.MY_CUSTOM_DOMAIN
    secretName: tls-secret
  rules:
  - host: hello-world-ingress.MY_CUSTOM_DOMAIN
    http:
      paths:
      - backend:
          serviceName: aks-helloworld-one
          servicePort: 80
        path: /hello-world-one(/|$)(.*)
      - backend:
          serviceName: aks-helloworld-two
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
      - backend:
          serviceName: aks-helloworld-one
          servicePort: 80
        path: /(.*)
---
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress-static
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /static/$2
    nginx.ingress.kubernetes.io/use-regex: "true"
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
    - hello-world-ingress.MY_CUSTOM_DOMAIN
    secretName: tls-secret
  rules:
  - host: hello-world-ingress.MY_CUSTOM_DOMAIN
    http:
      paths:
      - backend:
          serviceName: aks-helloworld-one
          servicePort: 80
        path: /static(/|$)(.*)
```

Creare la risorsa di ingresso con il comando `kubectl apply`.

```console
kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic
```

## <a name="verify-a-certificate-object-has-been-created"></a>Verificare che sia stato creato un oggetto certificato

Successivamente, è necessario creare una risorsa certificato. La risorsa certificato definisce il certificato X.509 desiderato. Per ulteriori informazioni, vedere [certificati Cert-Manager][cert-manager-certificates]. Cert-Manager ha creato automaticamente un oggetto certificato per l'utente tramite il traffico in ingresso-shim, che viene distribuito automaticamente con Cert-Manager da v 0.2.2. Per altre informazioni, consultare la [documentazione su ingress-shim][ingress-shim].

Per verificare che il certificato sia stato creato correttamente, usare il `kubectl get certificate --namespace ingress-basic` comando e verificare che *Ready* sia *true*, operazione che può richiedere alcuni minuti.

```
$ kubectl get certificate --namespace ingress-basic

NAME         READY   SECRET       AGE
tls-secret   True    tls-secret   11m
```

## <a name="test-the-ingress-configuration"></a>Testare la configurazione di ingresso

Aprire un Web browser per *Hello-World-ingress. MY_CUSTOM_DOMAIN* del controller di ingresso Kubernetes. Si noti che si è reindirizzati per l'uso di HTTPS e il certificato è attendibile e l'applicazione demo viene visualizzata nel Web browser. Aggiungere il percorso */Hello-World-Two* e notare che viene visualizzata la seconda applicazione demo con il titolo personalizzato.

## <a name="clean-up-resources"></a>Pulire le risorse

Questo articolo ha usato Helm per installare i componenti di ingresso, i certificati e le app di esempio. Quando si distribuisce un grafico Helm, viene creato un certo numero di risorse di Kubernetes. Queste risorse includono pod, distribuzioni e servizi. Per pulire queste risorse, è possibile eliminare l'intero spazio dei nomi di esempio o le singole risorse.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Eliminare lo spazio dei nomi di esempio e tutte le risorse

Per eliminare l'intero spazio dei nomi di esempio, usare il `kubectl delete` comando e specificare il nome dello spazio dei nomi. Vengono eliminate tutte le risorse nello spazio dei nomi.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Elimina risorse singolarmente

In alternativa, un approccio più granulare consiste nell'eliminare le singole risorse create. Rimuovere innanzitutto le risorse dell'autorità emittente del cluster:

```console
kubectl delete -f cluster-issuer.yaml --namespace ingress-basic
```

Elencare le versioni Helm con il `helm list` comando. Cercare i grafici denominati *nginx* e *Cert-Manager*, come illustrato nell'output di esempio seguente:

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
cert-manager            ingress-basic   1               2020-01-15 10:23:36.515514 -0600 CST    deployed        cert-manager-v0.13.0    v0.13.0    
nginx                   ingress-basic   1               2020-01-15 10:09:45.982693 -0600 CST    deployed        nginx-ingress-1.29.1    0.27.0  
```

Disinstallare le versioni con il `helm uninstall` comando. L'esempio seguente Disinstalla le distribuzioni NGINX in ingresso e Cert-Manager.

```
$ helm uninstall cert-manager nginx --namespace ingress-basic

release "cert-manager" uninstalled
release "nginx" uninstalled
```

Rimuovere quindi le due applicazioni di esempio:

```console
kubectl delete -f aks-helloworld-one.yaml --namespace ingress-basic
kubectl delete -f aks-helloworld-two.yaml --namespace ingress-basic
```

Rimuovere la route in ingresso che ha indirizzato il traffico verso le app di esempio:

```console
kubectl delete -f hello-world-ingress.yaml --namespace ingress-basic
```

Infine, è possibile eliminare lo spazio dei nomi stesso. Usare il `kubectl delete` comando e specificare il nome dello spazio dei nomi:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono stati inclusi alcuni componenti esterni ad servizio Azure Kubernetes. Per altre informazioni su questi componenti, vedere le pagine di progetto seguenti:

- [Interfaccia della riga di comando di Helm][helm-cli]
- [Controller di ingresso NGINX][nginx-ingress]
- [cert-manager][cert-manager]

È anche possibile:

- [Creare un controller di ingresso di base con connettività di rete esterna][aks-ingress-basic]
- [Abilitare il componente aggiuntivo di routing dell'applicazione HTTP][aks-http-app-routing]
- [Creare un controller di ingresso che usa una rete privata interna e l'indirizzo IP][aks-ingress-internal]
- [Creare un controller di ingresso che usa i propri certificati TLS][aks-ingress-own-tls]
- [Creare un controller di ingresso che usa Let's Encrypt per generare automaticamente certificati TLS con un indirizzo IP pubblico statico][aks-ingress-static-tls]

<!-- LINKS - external -->
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[custom-domain]: ../app-service/manage-custom-dns-buy-domain.md#buy-the-domain
[dns-zone]: ../dns/dns-getstarted-cli.md
[helm]: https://helm.sh/
[helm-cli]: ./kubernetes-helm.md
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[ingress-shim]: https://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
