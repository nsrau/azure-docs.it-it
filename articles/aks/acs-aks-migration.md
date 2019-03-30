---
title: Eseguire la migrazione dal servizio Azure Container (ACS) al servizio Azure Kubernetes
description: Eseguire la migrazione dal servizio Azure Container (ACS) al servizio Azure Kubernetes
services: container-service
author: noelbundick
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/13/2018
ms.author: nobun
ms.custom: mvc
ms.openlocfilehash: 910c96988ec0a8b8aa7b6ac8ce287c4fdc59e177
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58649969"
---
# <a name="migrating-from-azure-container-service-acs-to-azure-kubernetes-service-aks"></a>Eseguire la migrazione dal servizio Azure Container (ACS) al servizio Azure Kubernetes

L'obiettivo di questo documento è di consentire la pianificazione e l'esecuzione corretta di una migrazione tra il servizio Azure Container con Kubernetes (ACS) e il servizio Azure Kubernetes. Questa guida illustra nel dettaglio le differenze tra ACS e servizio Azure Kubernetes, offre una panoramica del processo di migrazione e consente di prendere decisioni chiave.

## <a name="differences-between-acs-and-aks"></a>Differenze tra ACS e servizio Azure Kubernetes

ACS e servizio Azure Kubernetes differiscono in alcune aree chiave che influiscono sulla migrazione. È consigliabile analizzare e pianificare di risolvere le seguenti differenze prima di ogni operazione di migrazione.

* Nodi di servizio Azure Kubernetes usano [Usa dischi gestiti](../virtual-machines/windows/managed-disks-overview.md)
    * I dischi non gestiti sarà necessario convertirli prima di poterli collegare ai nodi servizio Azure Kubernetes
    * Gli oggetti personalizzati `StorageClass` per i dischi di Azure devono essere modificati da `unmanaged` a `managed`
    * Qualsiasi `PersistentVolumes` dovrà usare `kind: Managed`
* servizio Azure Kubernetes supporta attualmente solo un pool di agenti
* I nodi basati su Windows Server sono attualmente in [anteprima privata](https://azure.microsoft.com/blog/kubernetes-on-azure/)
* Controllare l'elenco delle [aree supportate](https://docs.microsoft.com/azure/aks/container-service-quotas) di servizio Azure Kubernetes
* servizio Azure Kubernetes è un servizio gestito con un piano di controllo di Kubernetes ospitato. Potrebbe essere necessario modificare le applicazioni se è stata precedentemente modificata la configurazione di master ACS

### <a name="differences-between-kubernetes-versions"></a>Differenze tra le versioni di Kubernetes

Se si esegue la migrazione a una versione più recente di Kubernetes (ad esempio: 1.7.x a 1.9.x), esistono alcune modifiche all'API k8s che richiederà l'attenzione dell'utente.

* [Eseguire la migrazione di un ThirdPartyResource a CustomResourceDefinition](https://kubernetes.io/docs/tasks/access-kubernetes-api/migrate-third-party-resource/)
* [Modifiche ai carichi di lavoro API nella versione 1.8 e 1.9](https://kubernetes.io/docs/reference/workloads-18-19/).

## <a name="migration-considerations"></a>Considerazioni sulla migrazione

### <a name="agent-pools"></a>Pool di agenti

Mentre servizio Azure Kubernetes gestisce il piano di controllo di Kubernetes, è comunque possibile definire le dimensioni e il numero di nodi da includere nel nuovo cluster. Supponendo che si voglia un mapping 1:1 tra ACS a servizio Azure Kubernetes, è opportuno acquisire le informazioni sul nodo di ACS esistente. Si useranno questi dati quando si crea il nuovo cluster di servizio Azure Kubernetes.

Esempio:

| NOME | Conteggio | Dimensioni macchina virtuale | Sistema operativo |
| --- | --- | --- | --- |
| agentpool0 | 3 | Standard_D8_v2 | Linux |
| agentpool1 | 1 | Standard_D2_v2 | Windows |

Poiché le macchine virtuali aggiuntive verranno distribuite nella sottoscrizione durante la migrazione, è necessario verificare che i limiti e le quote siano sufficienti per tali risorse. È possibile ottenere più informazioni esaminando [Sottoscrizione di Azure e limiti dei servizi](https://docs.microsoft.com/azure/azure-subscription-service-limits). Per controllare le quote correnti, andare sul [Pannello delle sottoscrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) nel portale di Azure, selezionare la sottoscrizione, quindi selezionare `Usage + quotas`.

### <a name="networking"></a>Rete

Per le applicazioni complesse, in genere si esegue la migrazione nel corso del tempo anziché tutta in una volta. Ciò significa che i vecchi e nuovi ambienti potrebbero necessitare di comunicare attraverso la rete. Le applicazioni che in precedenza erano in grado di usare i servizi `ClusterIP` per comunicare potrebbero necessitare di essere esposte come tipo `LoadBalancer` e protette in modo appropriato.

Per completare la migrazione è opportuno indirizzare i client per i nuovi servizi in esecuzione in servizio Azure Kubernetes. È consigliabile reindirizzare il traffico tramite l'aggiornamento di DNS in modo che punti al servizio di bilanciamento del carico posizionato davanti ai cluster servizio Azure Kubernetes.

### <a name="stateless-applications"></a>Applicazioni senza stato

La migrazione delle applicazioni senza stato è il caso più semplice. Applicare le definizioni di YAML al nuovo cluster, verificare che tutto funzioni come previsto e reindirizzare il traffico per rendere attivo il nuovo cluster.

### <a name="stateful-applications"></a>Applicazioni con stato

La migrazione delle applicazioni con stato richiede un'attenta pianificazione per evitare la perdita di dati o tempi di inattività imprevisti.

#### <a name="highly-available-applications"></a>Applicazioni a disponibilità elevata

Alcune applicazioni con stato possono essere distribuite in una configurazione a disponibilità elevata e copiare i dati tra le repliche. Se l'oggetto descrive la distribuzione corrente, potrebbe essere possibile creare un nuovo membro nel nuovo cluster servizio Azure Kubernetes ed eseguire la migrazione con un impatto minimo per i chiamanti downstream. I passaggi della migrazione per questo scenario sono in genere:

1. Creare una nuova replica secondaria su servizio Azure Kubernetes
2. Attendere la replica dei dati
3. Non è più possibile rendere il nuovo database primario replica secondaria
4. Indirizzare il traffico verso il cluster servizio Azure Kubernetes

#### <a name="migrating-persistent-volumes"></a>Migrazione di volumi persistenti

Esistono molteplici fattori da considerare se si sta migrando volumi persistenti esistenti a servizio Azure Kubernetes. In generale, i passaggi necessari sono:

1. (Facoltativo) Scritture di disattivazione all'applicazione (richiede tempo di inattività)
2. Dischi di snapshot
3. Creare nuovi dischi gestiti da snapshot
4. Creare volumi persistenti in servizio Azure Kubernetes
5. Aggiornare le specifiche del Pod per [usare i volumi esistenti](https://docs.microsoft.com/azure/aks/azure-disk-volume) anziché PersistentVolumeClaims (provisioning statico)
6. Distribuire l'applicazione a servizio Azure Kubernetes
7. Convalida
8. Indirizzare il traffico verso il cluster servizio Azure Kubernetes

> **Importante**: Se sceglie di non disattivare scritture, è necessario replicare i dati alla nuova distribuzione, come si saranno manchino i dati scritti dopo lo snapshot del disco

Sono disponibili strumenti open source che consentono di creare dischi gestiti ed eseguire la migrazione dei volumi tra i cluster Kubernetes.

* [noelbundick/azure-cli-disco-extension](https://github.com/noelbundick/azure-cli-disk-copy-extension): copiare e convertire i dischi in gruppi di risorse e aree di Azure
* [yaron2/azure-kube-cli](https://github.com/yaron2/azure-kube-cli): enumerare i volumi Kubernetes ACS e eseguire la migrazione a un cluster servizio Azure Kubernetes

#### <a name="azure-files"></a>File di Azure

A differenza dei dischi, i file di Azure possono essere montati simultaneamente da più host. Né Azure né Kubernetes impedisce la creazione di un Pod nel cluster servizio Azure Kubernetes che è ancora in uso dal cluster ACS. Per evitare la perdita di dati e un comportamento imprevisto, è necessario assicurarsi che entrambi i cluster non scrivano gli stessi file nello stesso momento.

Se l'applicazione può ospitare più repliche che puntano alla stessa condivisione di file, è possibile seguire i passaggi della migrazione senza stato e distribuire le definizioni di YAML nel nuovo cluster.

In caso contrario, un approccio di migrazione possibile prevede i passaggi seguenti:

1. Distribuire l'applicazione a servizio Azure Kubernetes con un conteggio di replica pari a 0
2. Scalabilità dell'applicazione in ACS su 0 (richiede tempo di inattività)
3. Scalabilità dell'applicazione in servizio Azure Kubernetes fino a 1
4. Convalida
5. Indirizzare il traffico verso il cluster servizio Azure Kubernetes

Nei casi i cui si desidera iniziare con una condivisione vuota, per eseguire un copia dei dati di origine è possibile usare i comandi [`az storage file copy`](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) per effettuare la migrazione dei dati.

### <a name="deployment-strategy"></a>Strategia di distribuzione

Il metodo consigliato è usare la pipeline CI/CD esistente per distribuire una configurazione valida nota a servizio Azure Kubernetes. Verranno clonate le attività di distribuzione esistenti e assicurarsi che il `kubeconfig` faccia riferimento al nuovo cluster servizio Azure Kubernetes.

Nei casi in cui ciò non è possibile, è necessario esportare la definizione di risorsa da ACS e quindi applicarle a servizio Azure Kubernetes. È possibile usare `kubectl` per esportare gli oggetti.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

Esistono anche molteplici strumenti open source che possono essere utili, in base alle esigenze:

* [heptio/ark](https://github.com/heptio/ark) - richiede k8s 1.7
* [yaron2/azure-kube-cli](https://github.com/yaron2/azure-kube-cli)
* [mhausenblas/reshifter](https://github.com/mhausenblas/reshifter)

## <a name="migration-steps"></a>Passaggi della migrazione

### <a name="1-create-an-aks-cluster"></a>1. Creare un cluster del servizio Azure Container

È possibile seguire la documentazione per [Creare un cluster servizio Azure Kubernetes](https://docs.microsoft.com/azure/aks/create-cluster) tramite il portale di Azure, interfaccia della riga di comando di Azure o modello di Gestione risorse.

> È possibile trovare modelli di esempio di Gestione risorse di Azure per servizio Azure Kubernetes sui repository [AZURE/servizio Azure Kubernetes](https://github.com/Azure/AKS/tree/master/examples/vnet) su GitHub

### <a name="2-modify-applications"></a>2. Modificare le applicazioni

Apportare eventuali modifiche necessarie alle definizioni di YAML. Ad esempio: sostituzione `apps/v1beta1` con `apps/v1` per `Deployments`

### <a name="3-optional-migrate-volumes"></a>3. (Facoltativo) Eseguire la migrazione di volumi

Eseguire la migrazione di volumi dal cluster ACS nel cluster servizio Azure Kubernetes. Altre informazioni sono reperibili nella sezione [Migrazione di volumi persistenti](#migrating-persistent-volumes).

### <a name="4-deploy-applications"></a>4. Distribuire applicazioni

Usare il sistema CI/CD per distribuire le applicazioni a servizio Azure Kubernetes o usare kubectl per applicare le definizioni YAML.

### <a name="5-validate"></a>5. Convalida

Verificare che le applicazioni funzionino come previsto e che vi siano stati copiati tutti i dati migrati.

### <a name="6-redirect-traffic"></a>6. Reindirizzare il traffico

Aggiornare il DNS per indirizzare i client per la distribuzione di servizio Azure Kubernetes.

### <a name="7-post-migration-tasks"></a>7. Passaggi post-migrazione

Se si eseguire la migrazione di volumi e si sceglie di non disattivare le scritture, è necessario copiare i dati nel nuovo cluster.
