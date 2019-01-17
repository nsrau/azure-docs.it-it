---
title: Componente aggiuntivo Routing di applicazioni HTTP nel servizio Azure Kubernetes
description: Usare il componente aggiuntivo Routing di applicazioni HTTP nel servizio Azure Kubernetes.
services: container-service
author: lachie83
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/25/2018
ms.author: laevenso
ms.openlocfilehash: 0bca7281c390388bd860219fb6f2eacb96b99df0
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742389"
---
# <a name="http-application-routing"></a>Routing di applicazioni HTTP

La soluzione Routing di applicazioni HTTP semplifica l'accesso alle applicazioni distribuite nel cluster del servizio Azure Kubernetes. Quando è abilitata, la soluzione configura un controller di ingresso nel cluster servizio Azure Kubernetes. Man mano che le applicazioni vengono distribuite, la soluzione crea anche nomi DNS pubblicamente accessibili per gli endpoint applicazione.

Quando il componente aggiuntivo è abilitato, viene creata una zona DNS nella sottoscrizione. Per altre informazioni sul costo del DNS, vedere la pagina relativa ai [prezzi del DNS][dns-pricing].

> [!CAUTION]
> Il componente aggiuntivo di routing dell'applicazione HTTP consente di creare rapidamente un controller di ingresso e accedere alle applicazioni. Questo componente aggiuntivo non è consigliato per gli ambienti di produzione. Per distribuzioni di ingresso pronte per la produzione che includono più repliche e il supporto TLS, vedere [Creare un controller di ingresso HTTPS](https://docs.microsoft.com/azure/aks/ingress-tls).

## <a name="http-routing-solution-overview"></a>Panoramica della soluzione di routing HTTP

Il componente aggiuntivo distribuisce due componenti: un [controller di ingresso Kubernetes][ingress] e un controller [External-DNS][external-dns].

- **Controller di ingresso**: questo controller di ingresso è esposto a Internet tramite un servizio Kubernetes di tipo LoadBalancer. Controlla e implementa le [risorse di ingresso Kubernetes][ingress-resource], creando route agli endpoint applicazione.
- **Controller External-DNS**: controlla le risorse di ingresso Kubernetes e crea record DNS A nella zona DNS specifica del cluster.

## <a name="deploy-http-routing-cli"></a>Distribuire il routing HTTP: CLI

Il componente aggiuntivo Routing di applicazioni HTTP può essere abilitato tramite l'interfaccia della riga di comando di Azure al momento dell'implementazione di un cluster servizio Azure Kubernetes. A tale scopo, usare il comando [az servizio Azure Kubernetes create][az-aks-create] con l'argomento `--enable-addons`.

```azurecli
az aks create --resource-group myResourceGroup --name myAKSCluster --enable-addons http_application_routing
```

È anche possibile abilitare il routing HTTP su un cluster servizio Azure Kubernetes esistente usando il comando [az servizio Azure Kubernetes enable-addons][az-aks-enable-addons]. Per abilitare il routing HTTP in un cluster esistente, aggiungere il `--addons`parametro e specificare *http_application_routing* come illustrato nell'esempio seguente:

```azurecli
az aks enable-addons --resource-group myResourceGroup --name myAKSCluster --addons http_application_routing
```

Dopo aver implementato o aggiornato il cluster, usare il comando [az servizio Azure Kubernetes show][az-aks-show] per recuperare il nome della zona DNS. Questo nome è necessario per la distribuzione delle applicazioni nel cluster servizio Azure Kubernetes.

```azurecli
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query addonProfiles.httpApplicationRouting.config.HTTPApplicationRoutingZoneName -o table

Result
-----------------------------------------------------
9f9c1fe7-21a1-416d-99cd-3543bb92e4c3.eastus.aksapp.io
```

## <a name="deploy-http-routing-portal"></a>Distribuire il routing HTTP: Portale

Il componente aggiuntivo Routing di applicazioni HTTP può essere abilitato tramite il portale di Azure al momento dell'implementazione di un cluster servizio Azure Kubernetes.

![Abilitare la funzionalità di routing HTTP](media/http-routing/create.png)

Dopo aver implementato il cluster, passare al gruppo di risorse servizio Azure Kubernetes creato automaticamente e selezionare la zona DNS. Prendere nota del nome della zona DNS. Questo nome è necessario per la distribuzione delle applicazioni nel cluster servizio Azure Kubernetes.

![Ottenere il nome della zona DNS](media/http-routing/dns.png)

## <a name="use-http-routing"></a>Usare il routing HTTP

La soluzione Routing di applicazioni HTTP può essere attivata solo sulle risorse di ingresso annotate nel modo seguente:

```yaml
annotations:
  kubernetes.io/ingress.class: addon-http-application-routing
```

Creare un file denominato **samples-http-application-routing.yaml** e copiarlo nel codice YAML seguente. Alla riga 43 aggiornare `<CLUSTER_SPECIFIC_DNS_ZONE>` con il nome della zona DNS raccolto nel passaggio precedente di questo articolo.


```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: party-clippy
spec:
  template:
    metadata:
      labels:
        app: party-clippy
    spec:
      containers:
      - image: r.j3ss.co/party-clippy
        name: party-clippy
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        tty: true
        command: ["party-clippy"]
        ports:
        - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: party-clippy
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 8080
  selector:
    app: party-clippy
  type: ClusterIP
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: party-clippy
  annotations:
    kubernetes.io/ingress.class: addon-http-application-routing
spec:
  rules:
  - host: party-clippy.<CLUSTER_SPECIFIC_DNS_ZONE>
    http:
      paths:
      - backend:
          serviceName: party-clippy
          servicePort: 80
        path: /
```

Usare il comando [kubectl apply][kubectl-apply] per creare le risorse.

```bash
$ kubectl apply -f samples-http-application-routing.yaml

deployment "party-clippy" created
service "party-clippy" created
ingress "party-clippy" created
```

Usare cURL o un browser per passare al nome host specificato nella sezione host del file samples-http-application-routing.yaml. Può trascorrere al massimo un minuto prima che l'applicazione sia disponibile tramite Internet.

```bash
$ curl party-clippy.471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io

 _________________________________
/ It looks like you're building a \
\ microservice.                   /
 ---------------------------------
 \
  \
     __
    /  \
    |  |
    @  @
    |  |
    || |/
    || ||
    |\_/|
    \___/

```

## <a name="remove-http-routing"></a>Rimuovere il routing HTTP

La soluzione di routing HTTP può essere rimossa tramite l'interfaccia della riga di comando di Azure. A tale scopo eseguire il comando seguente sostituendo il nome del cluster servizio Azure Kubernetes e del gruppo di risorse.

```azurecli
az aks disable-addons --addons http_application_routing --name myAKSCluster --resource-group myResourceGroup --no-wait
```

Quando il componente aggiuntivo di routing dell'applicazione HTTP è disabilitato, alcune risorse Kubernetes possono rimanere nel cluster. Queste risorse includono *configMaps* e *secrets* e vengono create nello spazio dei nomi *kube-system*. Per mantenere un cluster pulito, è possibile rimuovere queste risorse.

Cercare le risorse *addon-http-application-routing* con i comandi [kubectl get] [ kubectl-get] seguenti:

```console
kubectl get deployments --namespace kube-system
kubectl get services --namespace kube-system
kubectl get configmaps --namespace kube-system
kubectl get secrets --namespace kube-system
```

L'output di esempio seguente mostra risorse configMaps che devono essere eliminate:

```
$ kubectl get configmaps --namespace kube-system

NAMESPACE     NAME                                                       DATA   AGE
kube-system   addon-http-application-routing-nginx-configuration         0      9m7s
kube-system   addon-http-application-routing-tcp-services                0      9m7s
kube-system   addon-http-application-routing-udp-services                0      9m7s
```

Per eliminare le risorse, usare il comando [kubectl delete][kubectl-delete]. Specificare il tipo di risorsa, il nome della risorsa e lo spazio dei nomi. Nell'esempio seguente viene eliminata una delle risorse configmaps precedente:

```console
kubectl delete configmaps addon-http-application-routing-nginx-configuration --namespace kube-system
```

Ripetere il passaggio `kubectl delete` precedente per tutte le risorse *addon-http-application-routing* rimaste nel cluster.

## <a name="troubleshoot"></a>Risolvere problemi

Usare il comando [kubectl logs][kubectl-logs] per visualizzare i registri applicazioni dell'applicazione External-DNS. I registri dovrebbero confermare che sono stati creati correttamente un record DNS A e un record DNS TXT.

```
$ kubectl logs -f deploy/addon-http-application-routing-external-dns -n kube-system

time="2018-04-26T20:36:19Z" level=info msg="Updating A record named 'party-clippy' to '52.242.28.189' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
time="2018-04-26T20:36:21Z" level=info msg="Updating TXT record named 'party-clippy' to '"heritage=external-dns,external-dns/owner=default"' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
```

Questi record possono essere visualizzati anche nella risorsa di zona DNS nel portale di Azure.

![Ottenere i record DNS](media/http-routing/clippy.png)

Usare il comando [kubectl logs][kubectl-logs] per visualizzare i registri applicazioni del controller di ingresso NGINX. I registri dovrebbero confermare l'esecuzione dell'operazione `CREATE` per una risorsa di ingresso e il ricaricamento del controller. Vengono registrate tutte le attività HTTP.

```bash
$ kubectl logs -f deploy/addon-http-application-routing-nginx-ingress-controller -n kube-system

-------------------------------------------------------------------------------
NGINX Ingress controller
  Release:    0.13.0
  Build:      git-4bc943a
  Repository: https://github.com/kubernetes/ingress-nginx
-------------------------------------------------------------------------------

I0426 20:30:12.212936       9 flags.go:162] Watching for ingress class: addon-http-application-routing
W0426 20:30:12.213041       9 flags.go:165] only Ingress with class "addon-http-application-routing" will be processed by this ingress controller
W0426 20:30:12.213505       9 client_config.go:533] Neither --kubeconfig nor --master was specified.  Using the inClusterConfig.  This might not work.
I0426 20:30:12.213752       9 main.go:181] Creating API client for https://10.0.0.1:443
I0426 20:30:12.287928       9 main.go:225] Running in Kubernetes Cluster version v1.8 (v1.8.11) - git (clean) commit 1df6a8381669a6c753f79cb31ca2e3d57ee7c8a3 - platform linux/amd64
I0426 20:30:12.290988       9 main.go:84] validated kube-system/addon-http-application-routing-default-http-backend as the default backend
I0426 20:30:12.294314       9 main.go:105] service kube-system/addon-http-application-routing-nginx-ingress validated as source of Ingress status
I0426 20:30:12.426443       9 stat_collector.go:77] starting new nginx stats collector for Ingress controller running in namespace  (class addon-http-application-routing)
I0426 20:30:12.426509       9 stat_collector.go:78] collector extracting information from port 18080
I0426 20:30:12.448779       9 nginx.go:281] starting Ingress controller
I0426 20:30:12.463585       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-nginx-configuration", UID:"2588536c-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"559", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-nginx-configuration
I0426 20:30:12.466945       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-tcp-services", UID:"258ca065-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"561", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-tcp-services
I0426 20:30:12.467053       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-udp-services", UID:"259023bc-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"562", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-udp-services
I0426 20:30:13.649195       9 nginx.go:302] starting NGINX process...
I0426 20:30:13.649347       9 leaderelection.go:175] attempting to acquire leader lease  kube-system/ingress-controller-leader-addon-http-application-routing...
I0426 20:30:13.649776       9 controller.go:170] backend reload required
I0426 20:30:13.649800       9 stat_collector.go:34] changing prometheus collector from  to default
I0426 20:30:13.662191       9 leaderelection.go:184] successfully acquired lease kube-system/ingress-controller-leader-addon-http-application-routing
I0426 20:30:13.662292       9 status.go:196] new leader elected: addon-http-application-routing-nginx-ingress-controller-5cxntd6
I0426 20:30:13.763362       9 controller.go:179] ingress backend successfully reloaded...
I0426 21:51:55.249327       9 event.go:218] Event(v1.ObjectReference{Kind:"Ingress", Namespace:"default", Name:"party-clippy", UID:"092c9599-499c-11e8-a5e1-0a58ac1f0ef2", APIVersion:"extensions", ResourceVersion:"7346", FieldPath:""}): type: 'Normal' reason: 'CREATE' Ingress default/party-clippy
W0426 21:51:57.908771       9 controller.go:775] service default/party-clippy does not have any active endpoints
I0426 21:51:57.908951       9 controller.go:170] backend reload required
I0426 21:51:58.042932       9 controller.go:179] ingress backend successfully reloaded...
167.220.24.46 - [167.220.24.46] - - [26/Apr/2018:21:53:20 +0000] "GET / HTTP/1.1" 200 234 "" "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Trident/5.0)" 197 0.001 [default-party-clippy-80] 10.244.0.13:8080 234 0.004 200
```

## <a name="clean-up"></a>Eseguire la pulizia

Rimuovere gli oggetti Kubernetes associati creati in questo articolo.

```bash
$ kubectl delete -f samples-http-application-routing.yaml

deployment "party-clippy" deleted
service "party-clippy" deleted
ingress "party-clippy" deleted
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come installare un controller di ingresso protetto con HTTPS nel servizio Azure Kubernetes, vedere [Ingresso HTTPS nel servizio Azure Kubernetes][ingress-https].

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-show]: /cli/azure/aks?view=azure-cli-latest#az-aks-show
[ingress-https]: ./ingress-tls.md
[az-aks-enable-addons]: /cli/azure/aks#az-aks-enable-addons


<!-- LINKS - external -->
[dns-pricing]: https://azure.microsoft.com/pricing/details/dns/
[external-dns]: https://github.com/kubernetes-incubator/external-dns
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[ingress]: https://kubernetes.io/docs/concepts/services-networking/ingress/
[ingress-resource]: https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource
