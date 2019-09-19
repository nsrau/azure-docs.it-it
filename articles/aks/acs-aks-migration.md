---
title: Eseguire la migrazione dal servizio contenitore di Azure (ACS) al servizio Azure Kubernetes (AKS)
description: Eseguire la migrazione dal servizio contenitore di Azure (ACS) al servizio Azure Kubernetes (AKS).
services: container-service
author: noelbundick
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/13/2018
ms.author: nobun
ms.custom: mvc
ms.openlocfilehash: 66f76a8a706f60df786786cbd1ce00b7eafd8d7e
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097893"
---
# <a name="migrate-from-azure-container-service-acs-to-azure-kubernetes-service-aks"></a>Eseguire la migrazione dal servizio contenitore di Azure (ACS) al servizio Azure Kubernetes (AKS)

Questo articolo consente di pianificare ed eseguire una migrazione corretta tra il servizio contenitore di Azure (ACS) con Kubernetes e il servizio Azure Kubernetes (AKS). Per prendere decisioni chiave, in questa guida vengono descritte in dettaglio le differenze tra ACS e AKS e viene fornita una panoramica del processo di migrazione.

## <a name="differences-between-acs-and-aks"></a>Differenze tra ACS e servizio Azure Kubernetes

ACS e AKS differiscono in alcune aree chiave che influiscono sulla migrazione. Prima di qualsiasi migrazione, è necessario rivedere e pianificare le seguenti differenze:

* I nodi AKS usano [Managed disks](../virtual-machines/windows/managed-disks-overview.md).
    * I dischi non gestiti devono essere convertiti prima di poterli aggiungere ai nodi AKS.
    * Gli `StorageClass` oggetti personalizzati per i dischi di Azure devono `unmanaged` essere `managed`modificati da a.
    * Qualsiasi `PersistentVolumes` deve utilizzare `kind: Managed`.
* AKS supporta [più pool di nodi](https://docs.microsoft.com/azure/aks/use-multiple-node-pools) (attualmente in anteprima).
* I nodi basati su Windows Server sono attualmente in [anteprima in AKS](https://azure.microsoft.com/blog/kubernetes-on-azure/).
* AKS supporta un set limitato di [aree](https://docs.microsoft.com/azure/aks/quotas-skus-regions).
* servizio Azure Kubernetes è un servizio gestito con un piano di controllo di Kubernetes ospitato. Se in precedenza è stata modificata la configurazione dei Master ACS, potrebbe essere necessario modificare le applicazioni.

## <a name="differences-between-kubernetes-versions"></a>Differenze tra le versioni di Kubernetes

Se si sta eseguendo la migrazione a una versione più recente di Kubernetes, esaminare le risorse seguenti per comprendere le strategie di controllo delle versioni di Kubernetes:

* [Criteri di supporto della versione e dello sfasamento della versione di Kubernetes](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions)

## <a name="migration-considerations"></a>Considerazioni sulla migrazione

### <a name="agent-pools"></a>Pool di agenti

Anche se AKS gestisce il piano di controllo Kubernetes, si definiscono comunque le dimensioni e il numero di nodi da includere nel nuovo cluster. Supponendo che si voglia un mapping 1:1 tra ACS a servizio Azure Kubernetes, è opportuno acquisire le informazioni sul nodo di ACS esistente. Usare questi dati quando si crea il nuovo cluster AKS.

Esempio:

| Name | Conteggio | Dimensioni macchina virtuale | Sistema operativo |
| --- | --- | --- | --- |
| agentpool0 | 3 | Standard_D8_v2 | Linux |
| agentpool1 | 1 | Standard_D2_v2 | Windows |

Poiché le macchine virtuali aggiuntive verranno distribuite nella sottoscrizione durante la migrazione, è necessario verificare che i limiti e le quote siano sufficienti per tali risorse. 

Per altre informazioni, vedere [sottoscrizione di Azure e limiti dei servizi](https://docs.microsoft.com/azure/azure-subscription-service-limits). Per controllare le quote correnti, nella portale di Azure passare al pannello [sottoscrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), selezionare la sottoscrizione e quindi selezionare **utilizzo e quote**.

### <a name="networking"></a>Rete

Per le applicazioni complesse, in genere si esegue la migrazione nel corso del tempo anziché tutta in una volta. Ciò significa che i vecchi e i nuovi ambienti potrebbero dover comunicare sulla rete. Le applicazioni che in `ClusterIP` precedenza usavano i servizi per la comunicazione potrebbero dover essere `LoadBalancer` esposte come tipo e essere protette in modo appropriato.

Per completare la migrazione, è opportuno indirizzare i client ai nuovi servizi in esecuzione su AKS. Si consiglia di reindirizzare il traffico aggiornando DNS per puntare al Load Balancer che si trova davanti al cluster AKS.

### <a name="stateless-applications"></a>Applicazioni senza stato

La migrazione delle applicazioni senza stato è il caso più semplice. Le definizioni YAML verranno applicate al nuovo cluster, assicurarsi che tutto funzioni come previsto e reindirizzare il traffico per attivare il nuovo cluster.

### <a name="stateful-applications"></a>Applicazioni con stato

Pianificare con attenzione la migrazione delle applicazioni con stato per evitare perdite di dati o tempi di inattività imprevisti.

#### <a name="highly-available-applications"></a>Applicazioni a disponibilità elevata

È possibile distribuire alcune applicazioni con stato in una configurazione a disponibilità elevata. Queste applicazioni possono copiare i dati tra le repliche. Se attualmente si usa questo tipo di distribuzione, potrebbe essere possibile creare un nuovo membro nel nuovo cluster AKS e quindi eseguire la migrazione con un effetto minimo sui chiamanti downstream. In genere, i passaggi di migrazione per questo scenario sono:

1. Creare una nuova replica secondaria in AKS.
2. Attendere la replica dei dati.
3. Eseguire il failover per creare una replica secondaria come nuova replica primaria.
4. Indirizzare il traffico al cluster AKS.

#### <a name="migrating-persistent-volumes"></a>Migrazione di volumi permanenti

Se si esegue la migrazione di volumi persistenti esistenti a AKS, in genere si seguono i passaggi seguenti:

1. Mettere in stato scrive nell'applicazione. Questo passaggio è facoltativo e richiede tempi di inattività.
2. Eseguire snapshot dei dischi.
3. Creare nuovi dischi gestiti dagli snapshot.
4. Creare volumi permanenti in AKS.
5. Aggiornare le specifiche pod in modo da [usare i volumi esistenti](https://docs.microsoft.com/azure/aks/azure-disk-volume) anziché PersistentVolumeClaims (provisioning statico).
6. Distribuire l'applicazione in AKS.
7. Convalidare.
8. Indirizzare il traffico al cluster AKS.

> [!IMPORTANT]
> Se si sceglie di non mettere in stato le Scritture, sarà necessario replicare i dati nella nuova distribuzione. In caso contrario, si perderanno i dati scritti dopo aver preso gli snapshot del disco.

Alcuni strumenti open source consentono di creare dischi gestiti ed eseguire la migrazione dei volumi tra cluster Kubernetes:

* [Estensione copia disco dell'interfaccia](https://github.com/noelbundick/azure-cli-disk-copy-extension) della riga di comando di Azure copia e converte i dischi tra gruppi di risorse e aree di Azure
* L'estensione dell'interfaccia della riga di comando di [Azure Kube](https://github.com/yaron2/azure-kube-cli) enumera i volumi Kubernetes ACS e ne esegue la migrazione a un cluster AKS.

#### <a name="azure-files"></a>File di Azure

A differenza dei dischi, i file di Azure possono essere montati simultaneamente da più host. Nel cluster AKS, Azure e Kubernetes non impediscono la creazione di un pod che il cluster ACS usa ancora. Per evitare la perdita di dati e il comportamento imprevisto, assicurarsi che i cluster non scrivano nello stesso file nello stesso momento.

Se l'applicazione può ospitare più repliche che puntano alla stessa condivisione file, seguire i passaggi della migrazione senza stato e distribuire le definizioni YAML nel nuovo cluster. In caso contrario, un approccio di migrazione possibile prevede i passaggi seguenti:

1. Distribuire l'applicazione in AKS con un numero di repliche pari a 0.
2. Ridimensionare l'applicazione in ACS a 0. Questo passaggio richiede tempi di inattività.
3. Ridimensionare l'applicazione in AKS fino a 1.
4. Convalidare.
5. Indirizzare il traffico al cluster AKS.

Se si vuole iniziare con una condivisione vuota e creare una copia dei dati di origine, è possibile usare i comandi [`az storage file copy`](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) per eseguire la migrazione dei dati.

### <a name="deployment-strategy"></a>Strategia di distribuzione

È consigliabile usare la pipeline di integrazione continua/recapito continuo esistente per distribuire una configurazione ben nota a AKS. Clonare le attività di distribuzione esistenti e `kubeconfig` assicurarsi che punti al nuovo cluster AKS.

Se non è possibile, esportare le definizioni delle risorse da ACS e quindi applicarle ad AKS. È possibile usare `kubectl` per esportare gli oggetti.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

Diversi strumenti open source possono essere utili, a seconda delle esigenze di distribuzione:

* [Velero](https://github.com/heptio/ark) (Questo strumento richiede Kubernetes 1,7).
* [Estensione CLI di Azure Kube](https://github.com/yaron2/azure-kube-cli)
* [ReShifter](https://github.com/mhausenblas/reshifter)

## <a name="migration-steps"></a>Passaggi della migrazione

1. [Creare un cluster](https://docs.microsoft.com/azure/aks/create-cluster) del servizio contenitore di Azure tramite il portale di Azure, l'interfaccia della riga di comando di Azure o Azure Resource Manager modello

   > [!NOTE]
   > Trovare modelli di Azure Resource Manager di esempio per AKS nel repository [Azure/AKS](https://github.com/Azure/AKS/tree/master/examples/vnet) su GitHub.

2. Apportare le modifiche necessarie alle definizioni YAML. Ad esempio, sostituire `apps/v1beta1` con `apps/v1` per `Deployments`.

3. [Eseguire la migrazione di volumi](#migrating-persistent-volumes) (facoltativo) dal cluster ACS al cluster AKS.

4. Usare il sistema CI/CD per distribuire le applicazioni in AKS. In alternativa, usare kubectl per applicare le definizioni YAML.

5. Convalidare. Verificare che le applicazioni funzionino come previsto e che tutti i dati migrati siano stati copiati.

6. Reindirizza il traffico. Aggiornare il DNS per indirizzare i client per la distribuzione di servizio Azure Kubernetes.

7. Completare le attività post-migrazione. Se è stata eseguita la migrazione dei volumi e si è scelto di non mettere in stato le Scritture, copiare i dati nel nuovo cluster.
