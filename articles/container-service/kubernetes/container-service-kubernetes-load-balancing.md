---
title: Bilanciare il carico dei contenitori Kubernetes in Azure | Microsoft Docs
description: "Connettersi esternamente e bilanciare il carico tra più contenitori in un cluster Kubernetes nel servizio contenitore di Azure."
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Contenitori, microservizi, Kubernetes, Azure
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/17/2017
ms.author: danlep
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: e2ac0d65eea9cea25a2a9cb1f99a35c689eed976
ms.contentlocale: it-it
ms.lasthandoff: 07/25/2017

---
# <a name="load-balance-containers-in-a-kubernetes-cluster-in-azure-container-service"></a>Bilanciare il carico dei contenitori in un cluster Kubernetes nel servizio contenitore di Azure 
In questo articolo viene presentato come bilanciare il carico in un cluster Kubernetes nel servizio contenitore di Azure. Il bilanciamento del carico offre un indirizzo IP accessibile dall'esterno per il servizio e distribuisce il traffico di rete tra i pod in esecuzione nelle macchine virtuali dell'agente.

È possibile configurare un servizio Kubernetes in modo che usi [Azure Load Balancer](../../load-balancer/load-balancer-overview.md) per gestire il traffico di rete esterno, TCP. Con una configurazione aggiuntiva, è possibile eseguire il bilanciamento del carico e il routing del traffico HTTP o HTTPS o anche scenari più avanzati.

## <a name="prerequisites"></a>Prerequisiti
* [Distribuire un cluster Kubernetes](container-service-kubernetes-walkthrough.md) nel servizio contenitore di Azure
* [Connettere il client](../container-service-connect.md) al cluster

## <a name="azure-load-balancer"></a>Servizio di bilanciamento del carico di Azure

Per impostazione predefinita, un cluster Kubernetes distribuito nel servizio contenitore di Azure include un servizio di bilanciamento del carico di Azure con connessione Internet per le macchine virtuali dell'agente. Per le macchine virtuali master viene configurata una risorsa separata del servizio di bilanciamento del carico. Il servizio di bilanciamento del carico di Azure è di livello 4. Attualmente, il bilanciamento del carico supporta solo il traffico TCP in Kubernetes.

Quando si crea un servizio Kubernetes, è possibile configurare automaticamente il servizio di bilanciamento del carico di Azure per consentire l'accesso al servizio. Per configurare il servizio di bilanciamento del carico, impostare `type` per il servizio su `LoadBalancer`. Il servizio di bilanciamento del carico crea una regola per il mapping di un indirizzo IP pubblico e del numero di porta del traffico in ingresso agli indirizzi IP privati e ai numeri di porta dei pod delle macchine virtuali dell'agente (e viceversa per il traffico di risposta). 

 I due esempi seguenti spiegano come impostare `type` per il servizio Kubernetes su `LoadBalancer`. Dopo aver provato gli esempi, eliminare le distribuzioni che non servono più.

### <a name="example-use-the-kubectl-expose-command"></a>Esempio: uso del comando `kubectl expose` 
La [procedura dettagliata per Kubernetes](container-service-kubernetes-walkthrough.md) include un esempio su come esporre un servizio con il comando `kubectl expose` e il relativo flag `--type=LoadBalancer`. Di seguito sono riportati i passaggi necessari:

1. Avviare una nuova distribuzione di contenitori. Ad esempio, il comando seguente avvia una nuova distribuzione denominata `mynginx`. La distribuzione è costituita da tre contenitori in base all'immagine Docker per il server Web Nginx.

    ```console
    kubectl run mynginx --replicas=3 --image nginx
    ```
2. Verificare che i contenitori siano in esecuzione. Ad esempio, se si esegue una query per i contenitori con `kubectl get pods`, verrà visualizzato un output simile al seguente:

    ![Avviare i contenitori Nginx](./media/container-service-kubernetes-load-balancing/nginx-get-pods.png)

3. Per configurare il servizio di bilanciamento del carico in modo che accetti il traffico esterno verso la distribuzione, eseguire `kubectl expose` con `--type=LoadBalancer`. Il comando seguente espone il server Nginx sulla porta 80:

    ```console
    kubectl expose deployments mynginx --port=80 --type=LoadBalancer
    ```

4. Digitare `kubectl get svc` per visualizzare lo stato dei servizi del cluster. Mentre il servizio di bilanciamento del carico configura la regola, `EXTERNAL-IP` del servizio viene visualizzato come `<pending>`. Dopo alcuni minuti, viene configurato l'indirizzo IP esterno: 

    ![Configurare il servizio di bilanciamento del carico di Azure](./media/container-service-kubernetes-load-balancing/nginx-external-ip.png)

5. Verificare di poter accedere al servizio all'indirizzo IP esterno. Ad esempio, aprire un Web browser all'indirizzo IP indicato. Il browser visualizza il server Web Nginx in esecuzione in uno dei contenitori. In alternativa, eseguire il comando `curl` o `wget`. ad esempio:

    ```
    curl 13.82.93.130
    ```

    Verrà visualizzato un output simile al seguente:

    ![Accesso a Nginx con il comando curl](./media/container-service-kubernetes-load-balancing/curl-output.png)

6. Per visualizzare la configurazione del servizio di bilanciamento del carico di Azure, accedere al [portale di Azure](https://portal.azure.com).

7. Cercare il gruppo di risorse per il cluster del servizio contenitore e selezionare il servizio di bilanciamento del carico per le macchine virtuali dell'agente. Il nome deve essere corrispondere a quello del servizio contenitore. Non scegliere il servizio di bilanciamento del carico per i nodi master, cioè quello il cui nome contiene **master-lb**. 

    ![Servizio di bilanciamento del carico nel gruppo di risorse](./media/container-service-kubernetes-load-balancing/container-resource-group-portal.png)

8. Per visualizzare i dettagli della configurazione del servizio di bilanciamento del carico, fare clic su **Regole di bilanciamento del carico** e sul nome della regola che è stata configurata.

    ![Regole del servizio di bilanciamento del carico](./media/container-service-kubernetes-load-balancing/load-balancing-rules.png) 

### <a name="example-specify-type-loadbalancer-in-the-service-configuration-file"></a>Esempio: specificare `type: LoadBalancer` nel file di configurazione del servizio

Se si distribuisce un'app di un contenitore Kubernetes da un [file di configurazione del servizio](https://kubernetes.io/docs/user-guide/services/operations/#service-configuration-file) YAML o JSON, specificare un servizio di bilanciamento del carico esterno aggiungendo la riga seguente alla specifica del servizio:

```YAML
 "type": "LoadBalancer"
``` 



La procedura seguente usa l'[esempio del guestbook](https://github.com/kubernetes/kubernetes/tree/master/examples/guestbook) di Kubernetes. Questo esempio è un'app Web multi-livello basata su immagini Redis e PHP Docker. È possibile specificare nel file di configurazione del servizio che il server PHP front-end usi il servizio di bilanciamento del carico di Azure.

1. Scaricare il file `guestbook-all-in-one.yaml` da [GitHub](https://github.com/kubernetes/kubernetes/tree/master/examples/guestbook/all-in-one). 
2. Cercare `spec` per il servizio `frontend`.
3. Rimuovere il commento dalla riga `type: LoadBalancer`.

    ![Servizio di bilanciamento durante la configurazione del servizio](./media/container-service-kubernetes-load-balancing/guestbook-frontend-loadbalance.png)

4. Salvare il file, quindi distribuire l'app eseguendo il comando seguente:

    ```
    kubectl create -f guestbook-all-in-one.yaml
    ```

5. Digitare `kubectl get svc` per visualizzare lo stato dei servizi del cluster. Mentre il servizio di bilanciamento del carico configura la regola, `EXTERNAL-IP` per il servizio `frontend` viene visualizzato come `<pending>`. Dopo alcuni minuti, viene configurato l'indirizzo IP esterno: 

    ![Configurare il servizio di bilanciamento del carico di Azure](./media/container-service-kubernetes-load-balancing/guestbook-external-ip.png)

6. Verificare di poter accedere al servizio all'indirizzo IP esterno. Ad esempio, è possibile aprire un Web browser per l'indirizzo IP esterno del servizio.

    ![Accedere al guestbook dall'esterno](./media/container-service-kubernetes-load-balancing/guestbook-web.png)

    È possibile aggiungere voci del guestbook.

7. Per visualizzare la configurazione del servizio di bilanciamento del carico di Azure, cercare la risorsa del servizio di bilanciamento del carico per il cluster nel [portale di Azure](https://portal.azure.com). Vedere i passaggi dell'esempio precedente.

### <a name="considerations"></a>Considerazioni

* La creazione della regola del servizio di bilanciamento del carico avviene in modo asincrono e le informazioni sul servizio di bilanciamento con provisioning vengono pubblicate nel campo `status.loadBalancer` del servizio.
* A ogni servizio viene assegnato automaticamente un proprio indirizzo IP virtuale nel servizio di bilanciamento del carico.
* Se si desidera raggiungere il servizio di bilanciamento del carico attraverso un nome DNS, usare il provider di servizi del proprio dominio per creare un nome DNS per l'indirizzo IP della regola.

## <a name="http-or-https-traffic"></a>Traffico HTTP o HTTPS

Per bilanciare il traffico HTTP o HTTPS sulle app Web del contenitore e gestire i certificati per Transport Layer Security (TLS), è possibile usare la risorsa [Ingress](https://kubernetes.io/docs/user-guide/ingress/) di Kubernetes. Una risorsa Ingress è un insieme di regole che consentono alle connessioni in ingresso di raggiungere i servizi del cluster. Per il funzionamento di una risorsa Ingress, è necessario che nel cluster Kubernetes sia in esecuzione un [controller di Ingress](https://kubernetes.io/docs/user-guide/ingress/#ingress-controllers).

Il servizio contenitore di Azure non implementa automaticamente un controller di Ingress di Kubernetes. Sono disponibili diverse implementazioni di controller. Attualmente, è consigliabile il [controller di Ingress Nginx](https://github.com/kubernetes/ingress/tree/master/examples/deployment/nginx) per configurare le regole di Ingress e bilanciare il carico del traffico HTTP e HTTPS. 

Per altre informazioni, vedere la [documentazione relativa al controller di Ingress Nginx](https://github.com/kubernetes/ingress/tree/master/controllers/nginx/README.md).

> [!IMPORTANT]
> Quando si usa il controller di Ingress Nginx nel servizio contenitore di Azure, è necessario esporre la distribuzione del controller come servizio con `type: LoadBalancer`. Ciò consente di configurare il bilanciamento del carico di Azure in modo che instradi il traffico al controller. Per altre informazioni, vedere la sezione precedente.


## <a name="next-steps"></a>Passaggi successivi

* Vedere la [documentazione sul servizio di bilanciamento del carico di Kubernetes](https://kubernetes.io/docs/user-guide/load-balancer/)
* Altre informazioni su [Ingress di Kubernetes e controller di Ingress](https://kubernetes.io/docs/user-guide/ingress/)
* Vedere [esempi di Kubernetes](https://github.com/kubernetes/kubernetes/tree/master/examples)


