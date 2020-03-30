---
title: Personalizzare il servizio CoreDNS per Azure Kubernetes (AKS)Customize CoreDNS for Azure Kubernetes Service (AKS)
description: Informazioni su come personalizzare CoreDNS per aggiungere sottodomini o estendere endpoint DNS personalizzati usando il servizio Azure Kubernetes (AKS)
services: container-service
author: jnoller
ms.topic: article
ms.date: 03/15/2019
ms.author: jenoller
ms.openlocfilehash: 78132a53313f4a8ee5c10af340c8dab08c3e42c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595825"
---
# <a name="customize-coredns-with-azure-kubernetes-service"></a>Personalizzare CoreDNS con il servizio Azure Kubernetes

Il servizio Azure Kubernetes (AKS) usa il progetto [CoreDNS][coredns] per la gestione e la risoluzione DNS del cluster con tutti i cluster *1.12.x* e versioni successive. In precedenza, è stato utilizzato il progetto kube-dns. Questo progetto kube-dns è ora deprecato. Per ulteriori informazioni sulla personalizzazione CoreDNS e Kubernetes, vedere la [documentazione ufficiale a monte][corednsk8s].

Poiché AKS è un servizio gestito, non è possibile modificare la configurazione principale per CoreDNS (un *CoreFile*). Utilizzare invece un *ConfigMap* Kubernetes per eseguire l'override delle impostazioni predefinite. Per visualizzare le configMaps AKS CoreDNS predefinite, utilizzare il `kubectl get configmaps --namespace=kube-system coredns -o yaml` comando .

In questo articolo viene illustrato come utilizzare ConfigMaps per le opzioni di personalizzazione di base di CoreDNS in AKS. Questo approccio è diverso dalla configurazione di CoreDNS in altri contesti, ad esempio l'utilizzo di CoreFile.This approach differs from configuring CoreDNS in other contexts such as using the CoreFile. Verificare che la versione di CoreDNS in esecuzione poiché i valori di configurazione possono cambiare tra le versioni.

> [!NOTE]
> `kube-dns`offriva diverse opzioni di [personalizzazione][kubednsblog] tramite una mappa di configurazione Kubernetes. CoreDNS **non** è compatibile con le versioni precedenti di kube-dns. Tutte le personalizzazioni utilizzate in precedenza devono essere aggiornate per l'utilizzo con CoreDNS.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster del servizio Azure Kubernetes esistente. Se è necessario un cluster servizio Azure Kubernetes, vedere la Guida introduttiva su servizio Azure Kubernetes [Uso dell'interfaccia della riga di comando di Azure][aks-quickstart-cli] oppure [Uso del portale di Azure][aks-quickstart-portal].

## <a name="what-is-supportedunsupported"></a>Cosa è supportato/non supportatoWhat is supported/unsupported

Sono supportati tutti i plug-in CoreDNS incorporati. Non sono supportati plug-in/terze parti.

## <a name="rewrite-dns"></a>Riscrivere DNS

Uno scenario in questo scenario consiste nell'eseguire riscritture di nomi DNS in tempo reale. Nell'esempio seguente `<domain to be written>` sostituire con un nome di dominio completo. Creare un `corednsms.yaml` file denominato e incollare la seguente configurazione di esempio:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: |
    <domain to be rewritten>.com:53 {
        errors
        cache 30
        rewrite name substring <domain to be rewritten>.com default.svc.cluster.local
        forward .  /etc/resolv.conf # you can redirect this to a specific DNS server such as 10.0.0.10
    }
```

Creare ConfigMap utilizzando il comando [kubectl apply configmap][kubectl-apply] e specificare il nome del manifesto YAML:

```console
kubectl apply -f corednsms.yaml
```

Per verificare che le personalizzazioni siano state applicate, utilizzare [kubectl get configmaps][kubectl-get] e specificare la *ConfigMap coredns-custom:*

```
kubectl get configmaps --namespace=kube-system coredns-custom -o yaml
```

Forzare ora CoreDNS a ricaricare la ConfigMap. Il comando [del pod kubectl delete][kubectl delete] non è distruttivo e non causa tempi di inattività. I `kube-dns` pod vengono eliminati e l'Utilità di pianificazione Kubernetes li ricrea. Questi nuovi pod contengono la modifica del valore TTL.

```console
kubectl delete pod --namespace kube-system -l k8s-app=kube-dns
```

> [!Note]
> Il comando precedente è corretto. Mentre stiamo cambiando `coredns`, la distribuzione è sotto il nome **kube-dns.**

## <a name="custom-forward-server"></a>Server di inoltro personalizzato

Se è necessario specificare un server di inoltro per il traffico di rete, è possibile creare un ConfigMap per personalizzare DNS. Nell'esempio seguente aggiornare il `forward` nome e l'indirizzo con i valori per il proprio ambiente. Creare un `corednsms.yaml` file denominato e incollare la seguente configurazione di esempio:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    <domain to be rewritten>.com:53 {
        forward foo.com 1.1.1.1
    }
```

Come negli esempi precedenti, creare ConfigMap usando il comando [kubectl apply configmap][kubectl-apply] e specificare il nome del manifesto YAML. Quindi, forzate CoreDNS a ricaricare ConfigMap usando il [contenitore kubectl delete][kubectl delete] per l'Utilità di pianificazione Kubernetes per ricrearli:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="use-custom-domains"></a>Utilizzare domini personalizzati

È possibile configurare domini personalizzati che possono essere risolti solo internamente. Ad esempio, è possibile risolvere il dominio personalizzato *puglife.local*, che non è un dominio di primo livello valido. Senza un dominio personalizzato ConfigMap, il cluster AKS non è in grado di risolvere l'indirizzo.

Nell'esempio seguente aggiornare il dominio personalizzato e l'indirizzo IP per indirizzare il traffico con i valori per il proprio ambiente. Creare un `corednsms.yaml` file denominato e incollare la seguente configurazione di esempio:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  puglife.server: |
    puglife.local:53 {
        errors
        cache 30
        forward . 192.11.0.1  # this is my test/dev DNS server
    }
```

Come negli esempi precedenti, creare ConfigMap usando il comando [kubectl apply configmap][kubectl-apply] e specificare il nome del manifesto YAML. Quindi, forzate CoreDNS a ricaricare ConfigMap usando il [contenitore kubectl delete][kubectl delete] per l'Utilità di pianificazione Kubernetes per ricrearli:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="stub-domains"></a>Domini Stub

CoreDNS può essere utilizzato anche per configurare i domini stub. Nell'esempio seguente aggiornare i domini e gli indirizzi IP personalizzati con i valori per il proprio ambiente. Creare un `corednsms.yaml` file denominato e incollare la seguente configurazione di esempio:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: |
    abc.com:53 {
        errors
        cache 30
        forward . 1.2.3.4
    }
    my.cluster.local:53 {
        errors
        cache 30
        forward . 2.3.4.5
    }

```

Come negli esempi precedenti, creare ConfigMap usando il comando [kubectl apply configmap][kubectl-apply] e specificare il nome del manifesto YAML. Quindi, forzate CoreDNS a ricaricare ConfigMap usando il [contenitore kubectl delete][kubectl delete] per l'Utilità di pianificazione Kubernetes per ricrearli:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="hosts-plugin"></a>Plug-in degli host

Poiché tutti i plugin incorporati sono supportati, questo significa che il plug-in CoreDNS [Hosts][coredns hosts] è disponibile per la personalizzazione:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom # this is the name of the configmap you can overwrite with your changes
  namespace: kube-system
data:
    test.override: |
          hosts example.hosts example.org { # example.hosts must be a file
              10.0.0.1 example.org
              fallthrough
          }
```

## <a name="enable-logging-for-dns-query-debugging"></a>Abilitare la registrazione per il debug delle query DNSEnable logging for DNS query debugging 

Per abilitare la registrazione delle query DNS, applicare la configurazione seguente nella configMap coredns-custom:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  log.override: |
        log
```

## <a name="next-steps"></a>Passaggi successivi

Questo articolo mostrava alcuni scenari di esempio per la personalizzazione di CoreDNS.This article showed some example scenarios for CoreDNS customization. Per informazioni sul progetto CoreDNS, vedere [la pagina del progetto upstream CoreDNS][coredns].

Per ulteriori informazioni sui concetti di base della rete, consultate Concetti di rete per le [applicazioni in AKS][concepts-network].

<!-- LINKS - external -->
[kubednsblog]: https://www.danielstechblog.io/using-custom-dns-server-for-domain-specific-name-resolution-with-azure-kubernetes-service/
[coredns]: https://coredns.io/
[corednsk8s]: https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/#coredns
[dnscache]: https://coredns.io/plugins/cache/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[coredns hosts]: https://coredns.io/plugins/hosts/

<!-- LINKS - internal -->
[concepts-network]: concepts-network.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
