---
title: Eseguire la migrazione ad Azure Kubernetes Service (AKS)
description: Eseguire la migrazione ad Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 02/25/2020
ms.custom: mvc
ms.openlocfilehash: 9371feb527bbb2d94d43072bb8a44a6705b45055
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87280223"
---
# <a name="migrate-to-azure-kubernetes-service-aks"></a>Eseguire la migrazione ad Azure Kubernetes Service (AKS)

Questo articolo consente di pianificare ed eseguire una migrazione riuscita ad Azure Kubernetes Service (AKS). Per prendere decisioni chiave, questa guida fornisce i dettagli per la configurazione corrente consigliata per AKS. Questo articolo non copre tutti gli scenari e, laddove appropriato, l'articolo contiene collegamenti a informazioni più dettagliate per la pianificazione di una migrazione riuscita.

Questo documento può essere utilizzato per supportare gli scenari seguenti:

* Migrazione di un cluster AKS supportato da [set di disponibilità](../virtual-machines/windows/tutorial-availability-sets.md) a [set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/overview.md)
* Migrazione di un cluster AKS per l'uso di un servizio di [bilanciamento del carico con SKU standard](./load-balancer-standard.md)
* Migrazione dal [servizio contenitore di Azure (ACS)-ritiro del 31 gennaio 2020](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/) ad AKS
* Migrazione da [motore AKS](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908) ad AKS
* Migrazione da cluster Kubernetes non basati su Azure ad AKS
* Trasferimento di risorse esistenti in un'area diversa

Quando si esegue la migrazione, assicurarsi che la versione di Kubernetes di destinazione si trovi all'interno della finestra supportata per il servizio Se si usa una versione precedente, è possibile che non si trovi all'interno dell'intervallo supportato e che sia necessario aggiornare le versioni per essere supportate da AKS. Per ulteriori informazioni, vedere le [versioni di Kubernetes supportate da AKS](./supported-kubernetes-versions.md) .

Se si esegue la migrazione a una versione più recente di Kubernetes, verificare i [criteri di supporto](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions)della versione e della versione di Kubernetes.

Diversi strumenti open source possono essere utili per la migrazione, a seconda dello scenario:

* [Velero](https://velero.io/) (richiede Kubernetes 1.7 +)
* [Estensione CLI di Azure Kube](https://github.com/yaron2/azure-kube-cli)
* [Rispostamento](https://github.com/mhausenblas/reshifter)

In questo articolo vengono riepilogati i dettagli della migrazione per:

> [!div class="checklist"]
> * AKS con Load Balancer Standard e set di scalabilità di macchine virtuali
> * Servizi di Azure collegati esistenti
> * Verificare le quote valide
> * Disponibilità elevata e continuità aziendale
> * Considerazioni per le applicazioni senza stato
> * Considerazioni per le applicazioni con stato
> * Distribuzione della configurazione del cluster

## <a name="aks-with-standard-load-balancer-and-virtual-machine-scale-sets"></a>AKS con Load Balancer Standard e set di scalabilità di macchine virtuali

AKS è un servizio gestito che offre funzionalità esclusive con una riduzione del sovraccarico di gestione. Come risultato dell'esecuzione di un servizio gestito, è necessario selezionare un set di [aree](./quotas-skus-regions.md) supportate da AKS. La transizione dal cluster esistente ad AKS potrebbe richiedere la modifica delle applicazioni esistenti in modo che rimangano integri sul piano di controllo gestito di AKS.

Si consiglia di usare i cluster AKS supportati dai [set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/index.yml) e dal [Load Balancer standard di Azure](./load-balancer-standard.md) per assicurarsi di ottenere funzionalità come [pool di nodi multipli](./use-multiple-node-pools.md), [zone di disponibilità](../availability-zones/az-overview.md), [intervalli IP autorizzati](./api-server-authorized-ip-ranges.md), [scalabilità](./cluster-autoscaler.md)automatica del cluster, [criteri di Azure per AKS](../governance/policy/concepts/policy-for-kubernetes.md)e altre nuove funzionalità non appena vengono rilasciate.

I cluster AKS supportati dai [set di disponibilità delle macchine virtuali](../virtual-machines/availability.md#availability-sets) non sono supportati per molte di queste funzionalità.

L'esempio seguente crea un cluster AKS con un pool a nodo singolo supportato da un set di scalabilità di macchine virtuali. Usa un servizio di bilanciamento del carico standard. Abilita anche il componente di scalabilità automatica del cluster nel pool di nodi per il cluster e imposta un minimo di *1* e un massimo di *3* nodi:

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

## <a name="existing-attached-azure-services"></a>Servizi di Azure collegati esistenti

Quando si esegue la migrazione di cluster, è possibile che siano stati collegati servizi di Azure esterni. Questi elementi non richiedono la ricreazione delle risorse, ma richiedono l'aggiornamento delle connessioni dai precedenti ai nuovi cluster per mantenere la funzionalità.

* Registro Azure Container
* Log Analytics
* Application Insights
* Gestione traffico
* Account di archiviazione
* Database esterni

## <a name="ensure-valid-quotas"></a>Verificare le quote valide

Poiché le macchine virtuali aggiuntive verranno distribuite nella sottoscrizione durante la migrazione, è necessario verificare che i limiti e le quote siano sufficienti per tali risorse. Potrebbe essere necessario richiedere un aumento della [quota vCPU](../azure-portal/supportability/per-vm-quota-requests.md).

Potrebbe essere necessario richiedere un aumento delle [quote di rete](../azure-portal/supportability/networking-quota-requests.md) per assicurarsi di non esaurire gli indirizzi IP. Per ulteriori informazioni, vedere la pagina relativa agli [intervalli IP e di rete per AKS](./configure-kubenet.md) .

Per altre informazioni, vedere [sottoscrizione di Azure e limiti dei servizi](../azure-resource-manager/management/azure-subscription-service-limits.md). Per controllare le quote correnti, nella portale di Azure passare al pannello [sottoscrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), selezionare la sottoscrizione e quindi selezionare **utilizzo e quote**.

## <a name="high-availability-and-business-continuity"></a>Disponibilità elevata e continuità aziendale

Se l'applicazione non può gestire i tempi di inattività, è necessario seguire le procedure consigliate per gli scenari di migrazione a disponibilità elevata.  Le procedure consigliate per la pianificazione di continuità aziendale complessa, il ripristino di emergenza e l'ottimizzazione del tempo di attività esulano dall'ambito di questo documento.  Per altre informazioni, vedere [le procedure consigliate per la continuità aziendale e il ripristino di emergenza in Azure Kubernetes Service (AKS)](./operator-best-practices-multi-region.md) .

Per le applicazioni complesse, in genere si esegue la migrazione nel corso del tempo anziché tutta in una volta. Ciò significa che i vecchi e i nuovi ambienti potrebbero dover comunicare sulla rete. Le applicazioni che in precedenza usavano i `ClusterIP` Servizi per la comunicazione potrebbero dover essere esposte come tipo `LoadBalancer` e essere protette in modo appropriato.

Per completare la migrazione, è opportuno indirizzare i client ai nuovi servizi in esecuzione su AKS. Si consiglia di reindirizzare il traffico aggiornando DNS per puntare al Load Balancer che si trova davanti al cluster AKS.

[Gestione traffico di Azure](../traffic-manager/index.yml) può indirizzare i clienti al cluster Kubernetes e all'istanza dell'applicazione desiderati.  Gestione traffico è un servizio di bilanciamento del carico del traffico basato su DNS che consente di distribuire il traffico di rete tra le aree.  Per ottenere prestazioni e ridondanza ottimali, indirizzare tutto il traffico dell'applicazione attraverso gestione traffico prima di passare al cluster AKS.  In una distribuzione multicluster i clienti devono connettersi a un nome DNS di gestione traffico che punta ai servizi in ogni cluster AKS. Definire questi servizi usando gli endpoint di gestione traffico. Ogni endpoint è l' *IP del servizio di bilanciamento del carico del servizio*. Usare questa configurazione per indirizzare il traffico di rete dall'endpoint di gestione traffico in un'area all'endpoint in un'area diversa.

![AKS con gestione traffico](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

Il [servizio front door di Azure](../frontdoor/front-door-overview.md) è un'altra opzione per il routing del traffico per i cluster AKS.  Il servizio Frontdoor di Azure consente di definire, gestire e monitorare il routing globale del traffico Web, rendendo le prestazioni ottimali e il failover globale immediato per una disponibilità elevata. 

### <a name="considerations-for-stateless-applications"></a>Considerazioni per le applicazioni senza stato

La migrazione delle applicazioni senza stato è il caso più semplice. Applicare le definizioni delle risorse (YAML o Helm) al nuovo cluster, assicurarsi che tutto funzioni come previsto e reindirizzare il traffico per attivare il nuovo cluster.

### <a name="considerations-for-stateful-applications"></a>Considerazioni per le applicazioni con stato

Pianificare con attenzione la migrazione delle applicazioni con stato per evitare perdite di dati o tempi di inattività imprevisti.

Se si usa File di Azure, è possibile montare la condivisione file come volume nel nuovo cluster:
* [Montare File di Azure statici come volume](./azure-files-volume.md#mount-the-file-share-as-a-volume)

Se si usa Managed Disks di Azure, è possibile montare il disco solo se non è collegato ad alcuna macchina virtuale:
* [Montare il disco di Azure statico come volume](./azure-disk-volume.md#mount-disk-as-volume)

Se nessuno di questi approcci funziona, è possibile usare le opzioni di backup e ripristino:
* [Velero in Azure](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)

#### <a name="azure-files"></a>File di Azure

A differenza dei dischi, i file di Azure possono essere montati simultaneamente da più host. Nel cluster AKS, Azure e Kubernetes non impediscono la creazione di un pod che il cluster ACS usa ancora. Per evitare la perdita di dati e il comportamento imprevisto, assicurarsi che i cluster non scrivano nello stesso file nello stesso momento.

Se l'applicazione può ospitare più repliche che puntano alla stessa condivisione file, seguire i passaggi della migrazione senza stato e distribuire le definizioni YAML nel nuovo cluster. In caso contrario, un approccio di migrazione possibile prevede i passaggi seguenti:

* Verificare che l'applicazione funzioni correttamente.
* Puntare il traffico in tempo reale al nuovo cluster AKS.
* Disconnettere il vecchio cluster.

Se si vuole iniziare con una condivisione vuota e creare una copia dei dati di origine, è possibile usare i [`az storage file copy`](/cli/azure/storage/file/copy?view=azure-cli-latest) comandi per eseguire la migrazione dei dati.


#### <a name="migrating-persistent-volumes"></a>Migrazione di volumi permanenti

Se si esegue la migrazione di volumi persistenti esistenti a AKS, in genere si seguono i passaggi seguenti:

* Mettere in stato scrive nell'applicazione. Questo passaggio è facoltativo e richiede tempi di inattività.
* Eseguire snapshot dei dischi.
* Creare nuovi dischi gestiti dagli snapshot.
* Creare volumi permanenti in AKS.
* Aggiornare le specifiche pod in modo da [usare i volumi esistenti](./azure-disk-volume.md) anziché PersistentVolumeClaims (provisioning statico).
* Distribuire l'applicazione in AKS.
* Verificare che l'applicazione funzioni correttamente.
* Puntare il traffico in tempo reale al nuovo cluster AKS.

> [!IMPORTANT]
> Se si sceglie di non mettere in stato le Scritture, sarà necessario replicare i dati nella nuova distribuzione. In caso contrario, si perderanno i dati scritti dopo aver preso gli snapshot del disco.

Alcuni strumenti open source consentono di creare dischi gestiti ed eseguire la migrazione dei volumi tra cluster Kubernetes:

* [Estensione copia disco dell'interfaccia](https://github.com/noelbundick/azure-cli-disk-copy-extension) della riga di comando di Azure copia e converte i dischi tra gruppi di risorse e aree di Azure
* L'estensione dell'interfaccia della riga di comando di [Azure Kube](https://github.com/yaron2/azure-kube-cli) enumera i volumi Kubernetes ACS e ne esegue la migrazione a un cluster AKS.


### <a name="deployment-of-your-cluster-configuration"></a>Distribuzione della configurazione del cluster

Si consiglia di usare la pipeline di integrazione continua (CI) e recapito continuo (CD) esistente per distribuire una configurazione ben nota a AKS. È possibile usare Azure Pipelines per [compilare e distribuire le applicazioni in AKS](/azure/devops/pipelines/ecosystems/kubernetes/aks-template?view=azure-devops). Clonare le attività di distribuzione esistenti e assicurarsi che `kubeconfig` punti al nuovo cluster AKS.

Se non è possibile, esportare le definizioni delle risorse dal cluster Kubernetes esistente e quindi applicarle ad AKS. È possibile usare `kubectl` per esportare gli oggetti.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

### <a name="moving-existing-resources-to-another-region"></a>Trasferimento di risorse esistenti in un'altra area

Potrebbe essere necessario spostare il cluster AKS in un' [area diversa supportata da AKS][region-availability]. Si consiglia di creare un nuovo cluster nell'altra area, quindi distribuire le risorse e le applicazioni nel nuovo cluster. Inoltre, se si dispone di servizi come [Azure Dev Spaces][azure-dev-spaces] in esecuzione nel cluster AKS, sarà anche necessario installare e configurare tali servizi nel cluster nella nuova area.


In questo articolo sono stati riepilogati i dettagli della migrazione per:

> [!div class="checklist"]
> * AKS con Load Balancer Standard e set di scalabilità di macchine virtuali
> * Servizi di Azure collegati esistenti
> * Verificare le quote valide
> * Disponibilità elevata e continuità aziendale
> * Considerazioni per le applicazioni senza stato
> * Considerazioni per le applicazioni con stato
> * Distribuzione della configurazione del cluster


[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[azure-dev-spaces]: ../dev-spaces/index.yml
