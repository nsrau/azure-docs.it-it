---
title: Eseguire la migrazione dal servizio contenitore di Azure (ACS) per Azure Kubernetes Service (AKS)
description: Eseguire la migrazione dal servizio contenitore di Azure (ACS) per Azure Kubernetes Service (AKS).
services: container-service
author: noelbundick
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/13/2018
ms.author: nobun
ms.custom: mvc
ms.openlocfilehash: dcee8da943603fb0978caf9992be76347ca197d6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65977706"
---
# <a name="migrate-from-azure-container-service-acs-to-azure-kubernetes-service-aks"></a>Eseguire la migrazione dal servizio contenitore di Azure (ACS) per Azure Kubernetes Service (AKS)

Questo articolo consente di pianificare ed eseguire correttamente una migrazione tra servizio contenitore di Azure (ACS) con Kubernetes e Azure Kubernetes Service (AKS). Per consentire di prendere decisioni chiave, questa guida illustra in dettaglio le differenze tra ACS e servizio contenitore di AZURE e viene fornita una panoramica del processo di migrazione.

## <a name="differences-between-acs-and-aks"></a>Differenze tra ACS e servizio Azure Kubernetes

Servizio contenitore di AZURE e servizio contenitore di AZURE si differenziano per alcune aree chiave che influiscono sulla migrazione. Prima di qualsiasi migrazione, è necessario rivedere e prevede di risolvere le differenze seguenti:

* Usano nodi AKS [dischi gestiti](../virtual-machines/windows/managed-disks-overview.md).
    * I dischi non gestiti devono essere convertiti prima che vengano collegati ai nodi AKS.
    * Custom `StorageClass` gli oggetti per i dischi di Azure devono essere modificati da `unmanaged` a `managed`.
    * Eventuali `PersistentVolumes` utilizzino `kind: Managed`.
* Servizio contenitore di AZURE supporta [più pool di nodi](https://docs.microsoft.com/azure/aks/use-multiple-node-pools) (attualmente in anteprima).
* Nodi basati su Windows Server sono attualmente nello [anteprima in AKS](https://azure.microsoft.com/blog/kubernetes-on-azure/).
* Servizio contenitore di AZURE supporta un set limitato di [aree](https://docs.microsoft.com/azure/aks/quotas-skus-regions).
* servizio Azure Kubernetes è un servizio gestito con un piano di controllo di Kubernetes ospitato. Si potrebbe essere necessario modificare le applicazioni se è stato precedentemente modificato la configurazione di master del servizio contenitore di AZURE.

## <a name="differences-between-kubernetes-versions"></a>Differenze tra le versioni di Kubernetes

Se si esegue la migrazione a una versione più recente di Kubernetes (ad esempio, da 1.7.x a 1.9.x), consultare le risorse seguenti per comprendere alcune modifiche all'API di Kubernetes:

* [La migrazione di un ThirdPartyResource a CustomResourceDefinition](https://kubernetes.io/docs/tasks/access-kubernetes-api/migrate-third-party-resource/)
* [Modifiche di carichi di lavoro API nelle versioni 1.8 e 1.9](https://kubernetes.io/docs/reference/workloads-18-19/)

## <a name="migration-considerations"></a>Considerazioni sulla migrazione

### <a name="agent-pools"></a>Pool di agenti

Sebbene il servizio contenitore di AZURE gestisce il piano di controllo di Kubernetes, è ancora possibile definire le dimensioni e numero di nodi da includere nel nuovo cluster. Supponendo che si voglia un mapping 1:1 tra ACS a servizio Azure Kubernetes, è opportuno acquisire le informazioni sul nodo di ACS esistente. Usare questi dati quando si crea il nuovo cluster di AKS.

Esempio:

| Name | Conteggio | Dimensioni macchina virtuale | Sistema operativo |
| --- | --- | --- | --- |
| agentpool0 | 3 | Standard_D8_v2 | Linux |
| agentpool1 | 1 | Standard_D2_v2 | Windows |

Poiché le macchine virtuali aggiuntive verranno distribuite nella sottoscrizione durante la migrazione, è necessario verificare che i limiti e le quote siano sufficienti per tali risorse. 

Per altre informazioni, vedere [sottoscrizione di Azure e limiti dei servizi](https://docs.microsoft.com/azure/azure-subscription-service-limits). Per controllare le quote correnti, nel portale di Azure, vedere la [pannello delle sottoscrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), selezionare la sottoscrizione e quindi selezionare **utilizzo + quote**.

### <a name="networking"></a>Rete

Per le applicazioni complesse, in genere si esegue la migrazione nel corso del tempo anziché tutta in una volta. Ciò significa che i vecchi e nuovi ambienti potrebbe essere necessario comunicare attraverso la rete. Le applicazioni che utilizzavano `ClusterIP` potrebbe essere necessario deve essere esposta come tipo di servizi per comunicare `LoadBalancer` ed essere protetto in modo appropriato.

Per completare la migrazione, è opportuno indirizzare i client per i nuovi servizi in esecuzione nel servizio contenitore di AZURE. È consigliabile reindirizzare il traffico tramite l'aggiornamento di DNS in modo che punti al servizio di bilanciamento del carico posizionato davanti ai cluster AKS.

### <a name="stateless-applications"></a>Applicazioni senza stato

La migrazione delle applicazioni senza stato è il caso più semplice. Si sarà applicare le definizioni di YAML al nuovo cluster, assicurarsi che tutto funzioni come previsto e reindirizzare il traffico per attivare il nuovo cluster.

### <a name="stateful-applications"></a>Applicazioni con stato

Pianificare con attenzione la migrazione delle applicazioni con state per evitare la perdita di dati o tempi di inattività imprevisti.

#### <a name="highly-available-applications"></a>Applicazioni a disponibilità elevata

È possibile distribuire alcune applicazioni con stati in una configurazione a disponibilità elevata. Queste applicazioni è possono copiare dati tra le repliche. Se attualmente si usa questo tipo di distribuzione, è possibile creare un nuovo membro nel nuovo cluster servizio contenitore di AZURE e quindi eseguire la migrazione con effetti minimi sui chiamanti downstream. In generale, i passaggi di migrazione per questo scenario sono:

1. Creare una nuova replica secondaria nel servizio contenitore di AZURE.
2. Per l'attesa di replica dei dati.
3. Esito negativo oltre a rendere il nuovo database primario di una replica secondaria.
4. Indirizzare il traffico verso il cluster AKS.

#### <a name="migrating-persistent-volumes"></a>La migrazione di volumi permanenti

Se si esegue la migrazione di volumi permanenti esistenti al servizio contenitore di AZURE, si seguiranno in genere questi passaggi:

1. Disattivazione scrive l'applicazione. (Questo passaggio è facoltativo e richiede tempi di inattività).
2. Acquisire snapshot dei dischi.
3. Creare nuovi dischi gestiti dagli snapshot.
4. Creare volumi permanenti nel servizio contenitore di AZURE.
5. Specifiche del pod per aggiornare [usare i volumi esistenti](https://docs.microsoft.com/azure/aks/azure-disk-volume) anziché PersistentVolumeClaims (provisioning statico).
6. Distribuire l'applicazione al servizio contenitore di AZURE.
7. La convalida.
8. Indirizzare il traffico verso il cluster AKS.

> [!IMPORTANT]
> Se sceglie di non disattivare scritture, è necessario replicare i dati alla nuova distribuzione. In caso contrario, non verrà trovato i dati scritti dopo che sono stati creati snapshot il disco.

Alcuni strumenti open source consentono di creare dischi gestiti e la migrazione dei volumi tra i cluster Kubernetes:

* [Estensione di copia del disco della riga di comando di Azure](https://github.com/noelbundick/azure-cli-disk-copy-extension) copia e converte i dischi in gruppi di risorse e aree di Azure.
* [L'estensione Azure CLI Kube](https://github.com/yaron2/azure-kube-cli) enumera volumi Kubernetes ACS e ne esegue la migrazione a un cluster del servizio contenitore di AZURE.

#### <a name="azure-files"></a>File di Azure

A differenza dei dischi, i file di Azure possono essere montati simultaneamente da più host. Nel cluster AKS, Azure e Kubernetes non impediscano la creazione di un pod che usa ancora nel cluster ACS. Per evitare la perdita di dati e un comportamento imprevisto, assicurarsi che il cluster non scrivere gli stessi file nello stesso momento.

Se l'applicazione può ospitare più repliche che puntano alla stessa condivisione di file, seguire i passaggi della migrazione senza stato e distribuire le definizioni di YAML nel nuovo cluster. In caso contrario, un approccio di migrazione possibile prevede i passaggi seguenti:

1. Distribuire l'applicazione in servizio contenitore di AZURE con un conteggio di replica pari a 0.
2. Scalabilità dell'applicazione nel servizio contenitore di AZURE su 0. (Questo passaggio richiede tempo di inattività).
3. Scalabilità dell'applicazione nel servizio contenitore di AZURE fino a 1.
4. La convalida.
5. Indirizzare il traffico verso il cluster AKS.

Se si vuole iniziare con una condivisione vuoto e apportare una copia dei dati di origine, è possibile usare la [ `az storage file copy` ](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) comandi per la migrazione dei dati.

### <a name="deployment-strategy"></a>Strategia di distribuzione

È consigliabile utilizzare la pipeline di integrazione continua/distribuzione esistente per distribuire una configurazione valida nota al servizio contenitore di AZURE. Clonare l'attività di distribuzione esistente e assicurarsi che `kubeconfig` fa riferimento al nuovo cluster AKS.

Se ciò non è possibile, esportare le definizioni delle risorse dal servizio contenitore di AZURE e quindi applicarle al servizio contenitore di AZURE. È possibile usare `kubectl` per esportare gli oggetti.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

Diversi strumenti open source può essere utile, a seconda delle esigenze di distribuzione:

* [Velero](https://github.com/heptio/ark) (questo strumento richiede 1.7 Kubernetes).
* [Estensione di Kube CLI di Azure](https://github.com/yaron2/azure-kube-cli)
* [ReShifter](https://github.com/mhausenblas/reshifter)

## <a name="migration-steps"></a>Passaggi della migrazione

1. [Creare un cluster AKS](https://docs.microsoft.com/azure/aks/create-cluster) tramite il portale di Azure, Azure CLI o modelli Azure Resource Manager.

   > [!NOTE]
   > Trovare modelli di Azure Resource Manager di esempio per AKS nel [servizio contenitore di AZURE/Azure](https://github.com/Azure/AKS/tree/master/examples/vnet) su GitHub.

2. Apportare le modifiche necessarie alle definizioni di YAML. Ad esempio, sostituire `apps/v1beta1` con `apps/v1` per `Deployments`.

3. [Eseguire la migrazione di volumi](#migrating-persistent-volumes) (facoltativo) dal cluster servizio contenitore di AZURE per il cluster AKS.

4. Usare il sistema di integrazione continua/recapito Continuo per distribuire le applicazioni al servizio contenitore di AZURE. Oppure usare kubectl per applicare le definizioni YAML.

5. La convalida. Assicurarsi che le applicazioni funzionino come previsto e che siano stati copiati tutti i dati migrati in.

6. Reindirizzare il traffico. Aggiornare il DNS per indirizzare i client per la distribuzione di servizio Azure Kubernetes.

7. Completare le attività di post-migrazione. Se si eseguire la migrazione di volumi e si sceglie di non disattivare scritture, copiare i dati nel nuovo cluster.
