---
title: Personalizzare CoreDNS per Azure Kubernetes Service (AKS)
description: Informazioni su come personalizzare CoreDNS per aggiungere sottodomini o estendere endpoint DNS personalizzati tramite Azure Kubernetes Service (AKS)
services: container-service
author: jnoller
ms.service: container-service
ms.topic: article
ms.date: 03/15/2019
ms.author: jenoller
ms.openlocfilehash: e42e017730ebee6b9b0f06f700a33e499d7eba51
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67615702"
---
# <a name="customize-coredns-with-azure-kubernetes-service"></a>Personalizzare CoreDNS con Azure Kubernetes Service

Azure Kubernetes Service (AKS) usa il [CoreDNS][coredns] progetto per la gestione DNS del cluster e la risoluzione con tutte *1.12.x* e cluster superiore. In precedenza, il progetto di kube-dns è stato usato. Questo progetto di kube-dns è deprecato. Per altre informazioni sulla personalizzazione CoreDNS e Kubernetes, vedere la [ufficiali upstream][corednsk8s].

Servizio contenitore di AZURE è un servizio gestito, non è possibile modificare la configurazione principale per CoreDNS (un *CoreFile*). Usare invece un Kubernetes *ConfigMap* per sostituire le impostazioni predefinite. Per visualizzare il valore predefinito CoreDNS ConfigMaps servizio contenitore di AZURE, usare il `kubectl get configmaps coredns -o yaml` comando.

Questo articolo illustra come usare ConfigMaps per le opzioni di personalizzazione di base di CoreDNS nel servizio contenitore di AZURE.

> [!NOTE]
> `kube-dns` offerte differenti [opzioni di personalizzazione][kubednsblog] tramite una mappa di configurazione di Kubernetes. È CoreDNS **non** garantisce la compatibilità con kube-dns. Le eventuali personalizzazioni usato in precedenza devono essere aggiornate per l'uso con CoreDNS.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster AKS esistente. Se è necessario un cluster del servizio contenitore di AZURE, vedere la Guida introduttiva AKS [tramite la CLI di Azure][aks-quickstart-cli] or [using the Azure portal][aks-quickstart-portal].

## <a name="what-is-supportedunsupported"></a>Che cos'è supportato/non supportato

Tutti i plug-in CoreDNS incorporati sono supportati. Nessun plug-in componenti pratici/terze parti sono supportati.

## <a name="rewrite-dns"></a>Riscrivere DNS

Uno scenario che richiede consiste nell'eseguire in tempo reale di riscrittura di nome DNS. Nell'esempio seguente, sostituire `<domain to be written>` con il proprio nome di dominio completo. Creare un file denominato `corednsms.json` e incollare la configurazione di esempio seguente:

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
        proxy .  /etc/resolv.conf # you can redirect this to a specific DNS server such as 10.0.0.10
    }
```

Creare l'oggetto ConfigMap usando il [kubectl applicare configmap][kubectl-apply] comando e specificare il nome del manifesto YAML:

```console
kubectl apply -f corednsms.json
```

Per verificare le personalizzazioni sono state applicate, usare il [kubectl get configmaps][kubectl-get] e specificare le *coredns-custom* ConfigMap:

```
kubectl get configmaps --namespace=kube-system coredns-custom -o yaml
```

A questo punto impone CoreDNS ricaricare ConfigMap. Il [kubectl eliminare pod][kubectl delete] comando non è distruttivo e non causa tempi di inattività. Il `kube-dns` POD vengono eliminati e quindi ricrea l'utilità di pianificazione di Kubernetes. Questi nuovi POD contengono la modifica del valore di durata (TTL).

```console
kubectl delete pod --namespace kube-system -l k8s-app=kube-dns
```

> [!Note]
> Il comando precedente è corretto. Anche se stiamo cambiando `coredns`, la distribuzione è sotto la **kube-dns** nome.

## <a name="custom-proxy-server"></a>Server proxy personalizzato

Se è necessario specificare un server proxy per il traffico di rete, è possibile creare un oggetto ConfigMap per personalizzare DNS. Nell'esempio seguente aggiorna il `proxy` nome e l'indirizzo con i valori per il proprio ambiente. Creare un file denominato `corednsms.json` e incollare la configurazione di esempio seguente:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    <domain to be rewritten>.com:53 {
        proxy foo.com 1.1.1.1
    }
```

Come negli esempi precedenti, creare l'oggetto ConfigMap usando il [kubectl applicare configmap][kubectl-apply] command and specify the name of your YAML manifest. Then, force CoreDNS to reload the ConfigMap using the [kubectl delete pod][kubectl delete] per crearli di nuovo l'utilità di pianificazione Kubernetes:

```console
kubectl apply -f corednsms.json
kubectl delete pod --namespace kube-system --label k8s-app=kube-dns
```

## <a name="use-custom-domains"></a>Usare i domini personalizzati

È possibile configurare domini personalizzati possono essere risolti solo internamente. Ad esempio, è possibile risolvere il dominio personalizzato *puglife.local*, che non è un dominio di primo livello valido. Senza un oggetto ConfigMap di dominio personalizzato, il cluster AKS non è possibile risolvere l'indirizzo.

Nell'esempio seguente, aggiornare il dominio personalizzato e l'indirizzo IP per indirizzare il traffico per i valori per il proprio ambiente. Creare un file denominato `corednsms.json` e incollare la configurazione di esempio seguente:

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
        proxy . 192.11.0.1  # this is my test/dev DNS server
    }
```

Come negli esempi precedenti, creare l'oggetto ConfigMap usando il [kubectl applicare configmap][kubectl-apply] command and specify the name of your YAML manifest. Then, force CoreDNS to reload the ConfigMap using the [kubectl delete pod][kubectl delete] per crearli di nuovo l'utilità di pianificazione Kubernetes:

```console
kubectl apply -f corednsms.json
kubectl delete pod --namespace kube-system --label k8s-app=kube-dns
```

## <a name="stub-domains"></a>Domini di stub

CoreDNS può essere usato anche per configurare domini a stub. Nell'esempio seguente, aggiornare gli indirizzi IP e domini personalizzati con i valori per il proprio ambiente. Creare un file denominato `corednsms.json` e incollare la configurazione di esempio seguente:

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
        proxy . 1.2.3.4
    }
    my.cluster.local:53 {
        errors
        cache 30
        proxy . 2.3.4.5
    }

```

Come negli esempi precedenti, creare l'oggetto ConfigMap usando il [kubectl applicare configmap][kubectl-apply] command and specify the name of your YAML manifest. Then, force CoreDNS to reload the ConfigMap using the [kubectl delete pod][kubectl delete] per crearli di nuovo l'utilità di pianificazione Kubernetes:

```console
kubectl apply -f corednsms.json
kubectl delete pod --namespace kube-system --label k8s-app=kube-dns
```

## <a name="hosts-plugin"></a>Plug-in di host

Poiché sono supportati tutti i plug-in predefiniti ciò significa che il CoreDNS [host][coredns hosts] plug-in è disponibile per personalizzare anche:

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

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato alcuni scenari di esempio per la personalizzazione CoreDNS. Per informazioni sul progetto CoreDNS, vedere [page del progetto upstream CoreDNS][coredns].

Per altre informazioni sui concetti di rete principali, vedere [concetti per le applicazioni nel servizio contenitore di AZURE di rete][concepts-network].

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
