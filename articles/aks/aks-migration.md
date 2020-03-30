---
title: Eseguire la migrazione al servizio Azure Kubernetes (AKS)Migrate to Azure Kubernetes Service (AKS)
description: Eseguire la migrazione al servizio Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 02/25/2020
ms.custom: mvc
ms.openlocfilehash: 8315560c679f9807715af14dc315fa3000be0472
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624813"
---
# <a name="migrate-to-azure-kubernetes-service-aks"></a>Eseguire la migrazione al servizio Azure Kubernetes (AKS)Migrate to Azure Kubernetes Service (AKS)

Questo articolo consente di pianificare ed eseguire una migrazione corretta al servizio Azure Kubernetes (AKS). Per facilitare le decisioni chiave, questa guida fornisce i dettagli per la configurazione consigliata corrente per AKS. In questo articolo non vengono illustrati tutti gli scenari e, se appropriato, l'articolo contiene collegamenti a informazioni più dettagliate per pianificare una migrazione corretta.

Questo documento può essere utilizzato per supportare gli scenari seguenti:This document can be used to help support the following scenarios:

* Migrazione di un cluster AKS supportato dai set di disponibilità in set di scalabilità di [macchine virtualiMigrating](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview) an AKS Cluster backed by [Availability Sets](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) to Virtual Machine Scale Sets
* Migrazione di un cluster AKS per l'utilizzo di un servizio di [bilanciamento del carico SKU StandardMigrating](https://docs.microsoft.com/azure/aks/load-balancer-standard) an AKS cluster to use a Standard SKU load balancer
* Migrazione dal servizio contenitore di [Azure (ACS) - ritiro dal 31 gennaio 2020 ad AKSMigrating from Azure Container Service (ACS) - pensionata January 31, 2020](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/) to AKS
* Migrazione dal [motore AKS](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908) ad AKS
* Migrazione da cluster Kubernetes non basati su Azure ad AKSMigrating from non-Azure based Kubernetes clusters to AKS

Durante la migrazione, assicurati che la versione di Kubernetes di destinazione sia all'interno della finestra supportata per AKS. Se si utilizza una versione precedente, potrebbe non essere compresa nell'intervallo supportato e richiedere l'aggiornamento delle versioni per essere supportato da AKS. Per ulteriori informazioni, vedere [Versioni di Kubernetes supportate da AKS.](https://docs.microsoft.com/azure/aks/supported-kubernetes-versions)

Se stai eseguendo la migrazione a una versione più recente di Kubernetes, consulta la versione di [Kubernetes e](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions)i criteri di supporto per l'inclinazione delle versioni .

Diversi strumenti open source possono essere utili per la migrazione, a seconda dello scenario:Several open-source tools can help with your migration, depending on your scenario:

* [Velero](https://velero.io/) (richiede Kubernetes 1.7 e)
* [Estensione CLI di Azure KubeAzure Kube CLI extension](https://github.com/yaron2/azure-kube-cli)
* [SpostamentoRe](https://github.com/mhausenblas/reshifter)

In questo articolo verranno riepilogati i dettagli della migrazione per:In this article we will summarize migration details for:

> [!div class="checklist"]
> * AKS con load Balancer standard e set di scalabilità di macchine virtuali
> * Servizi di Azure collegati esistenti
> * Garantire quote valideEnsure valid quotas
> * Disponibilità elevata e continuità aziendale
> * Considerazioni per le applicazioni senza statoConsiderations for stateless applications
> * Considerazioni per le applicazioni con statoConsiderations for stateful applications
> * Distribuzione della configurazione del cluster

## <a name="aks-with-standard-load-balancer-and-virtual-machine-scale-sets"></a>AKS con load Balancer standard e set di scalabilità di macchine virtuali

AKS è un servizio gestito che offre funzionalità esclusive con un overhead di gestione inferiore. Come risultato di essere un servizio gestito, è necessario selezionare da un set di [aree](https://docs.microsoft.com/azure/aks/quotas-skus-regions) che AKS supporta. La transizione dal cluster esistente ad AKS potrebbe richiedere la modifica delle applicazioni esistenti in modo che rimangano integre sul piano di controllo gestito AKS.

È consigliabile usare i cluster AKS supportati dai set di [scalabilità](https://docs.microsoft.com/azure/virtual-machine-scale-sets) di macchine virtuali e il servizio di [bilanciamento del](https://docs.microsoft.com/azure/aks/load-balancer-standard) carico standard di Azure per assicurarsi di ottenere funzionalità quali pool a [più nodi,](https://docs.microsoft.com/azure/aks/use-multiple-node-pools)zone [di disponibilità,](https://docs.microsoft.com/azure/availability-zones/az-overview) [intervalli IP autorizzati,](https://docs.microsoft.com/azure/aks/api-server-authorized-ip-ranges) [scalabilità automatica cluster,](https://docs.microsoft.com/azure/aks/cluster-autoscaler)criteri di [Azure per AKS](https://docs.microsoft.com/azure/governance/policy/concepts/rego-for-aks)e altre nuove funzionalità man mano che vengono rilasciate.

I cluster AKS supportati dai set di disponibilità delle [macchine virtuali](https://docs.microsoft.com/azure/virtual-machine-scale-sets/availability#availability-sets) non supportano molte di queste funzionalità.

Nell'esempio seguente viene creato un cluster AKS con pool a nodo singolo supportato da un set di scalabilità di macchine virtuali. Utilizza un servizio di bilanciamento del carico standard. Abilita inoltre il cluster autoscaler nel pool di nodi per il cluster e imposta un minimo di *1* e un massimo di *3* nodi:

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

Durante la migrazione dei cluster è possibile che siano stati collegati servizi di Azure esterni. Questi non richiedono la ricreazione delle risorse, ma richiedono l'aggiornamento delle connessioni dai cluster precedenti a i nuovi per mantenere la funzionalità.

* Registro Azure Container
* Log Analytics
* Application Insights
* Gestione traffico
* Account di archiviazione
* Database esterni

## <a name="ensure-valid-quotas"></a>Garantire quote valideEnsure valid quotas

Poiché le macchine virtuali aggiuntive verranno distribuite nella sottoscrizione durante la migrazione, è necessario verificare che i limiti e le quote siano sufficienti per tali risorse. Potrebbe essere necessario richiedere un aumento della [quota vCPU](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests).

Potrebbe essere necessario richiedere un aumento delle quote di rete per [assicurarsi](https://docs.microsoft.com/azure/azure-portal/supportability/networking-quota-requests) di non esaurire gli IP. Per ulteriori informazioni, vedere [Intervalli di rete e IP per AKS.](https://docs.microsoft.com/azure/aks/configure-kubenet)

Per altre informazioni, vedere Limiti della sottoscrizione e del servizio di [Azure.](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) Per controllare le quote correnti, nel portale di Azure passare al [pannello Sottoscrizioni,](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)selezionare la sottoscrizione e quindi selezionare **Utilizzo , quote**.

## <a name="high-availability-and-business-continuity"></a>Disponibilità elevata e continuità aziendale

Se l'applicazione non è in grado di gestire i tempi di inattività, sarà necessario seguire le procedure consigliate per gli scenari di migrazione a disponibilità elevata.  Le procedure consigliate per la pianificazione complessa della continuità aziendale, il ripristino di emergenza e l'ottimizzazione dei tempi di attività esulano dall'ambito di questo documento.  Per altre informazioni, vedere Procedure consigliate per la continuità aziendale e il [ripristino di emergenza in Azure Kubernetes Service (AKS).](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region)

Per le applicazioni complesse, in genere si esegue la migrazione nel corso del tempo anziché tutta in una volta. Ciò significa che gli ambienti vecchi e nuovi potrebbero dover comunicare in rete. Le applicazioni `ClusterIP` che in precedenza utilizzavano `LoadBalancer` servizi per comunicare potrebbe essere necessario esporre come tipo ed essere protette in modo appropriato.

Per completare la migrazione, è consigliabile puntare i client ai nuovi servizi in esecuzione su AKS. È consigliabile reindirizzare il traffico aggiornando DNS in modo che punti al servizio di bilanciamento del carico che si trova davanti al cluster AKS.

[Gestione traffico di Azure](https://docs.microsoft.com/azure/traffic-manager/) può indirizzare i clienti all'istanza dell'applicazione e del cluster Kubernetes desiderato.  Gestione traffico è un servizio di bilanciamento del carico basato su DNS in grado di distribuire il traffico di rete tra le aree.  Per ottenere prestazioni e ridondanza ottimali, indirizzare tutto il traffico delle applicazioni tramite Gestione traffico prima che venga indirizzato al cluster AKS.  In una distribuzione multicluster, i clienti devono connettersi a un nome DNS di Gestione traffico che punta ai servizi in ogni cluster AKS. Definire questi servizi usando gli endpoint di Gestione traffico. Ogni endpoint è l'IP del servizio di *bilanciamento del carico*del servizio. Usare questa configurazione per indirizzare il traffico di rete dall'endpoint di Gestione traffico in un'area all'endpoint in un'area diversa.

![AKS con Gestione traffico](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

[Il servizio porte frontale](https://docs.microsoft.com/azure/frontdoor/front-door-overview) di Azure è un'altra opzione per il routing del traffico per i cluster AKS.  Il servizio Frontdoor di Azure consente di definire, gestire e monitorare il routing globale del traffico Web, rendendo le prestazioni ottimali e il failover globale immediato per una disponibilità elevata. 

### <a name="considerations-for-stateless-applications"></a>Considerazioni per le applicazioni senza statoConsiderations for stateless applications

La migrazione delle applicazioni senza stato è il caso più semplice. Applicare le definizioni delle risorse (YAML o Helm) al nuovo cluster, assicurarsi che tutto funzioni come previsto e reindirizzare il traffico per attivare il nuovo cluster.

### <a name="considerations-for-stateful-applications"></a>Considerazioni per le applicazioni con statoConsiderations for stateful applications

Pianificare con attenzione la migrazione delle applicazioni con stato per evitare perdite di dati o tempi di inattività imprevisti.

Se si usa File di Azure, è possibile montare la condivisione file come volume nel nuovo cluster:If you use Azure Files, you can mount the file share as a volume into the new cluster:
* [Montaggio di file di Azure statici come volume](https://docs.microsoft.com/azure/aks/azure-files-volume#mount-the-file-share-as-a-volume)

If you use Azure Managed Disks, you can only mount the disk if unattached to any VM:
* [Montare il disco statico di Azure come volumeMount Static Azure Disk as a Volume](https://docs.microsoft.com/azure/aks/azure-disk-volume#mount-disk-as-volume)

Se nessuno di questi approcci funziona, è possibile utilizzare le opzioni di backup e ripristino:
* [Velero su Azure](https://github.com/heptio/velero/blob/master/site/docs/master/azure-config.md)

#### <a name="azure-files"></a>File di Azure

A differenza dei dischi, i file di Azure possono essere montati simultaneamente da più host. Nel cluster AKS, Azure e Kubernetes non impediscono di creare un pod che usa ancora il cluster ACS. Per evitare la perdita di dati e un comportamento imprevisto, assicurarsi che i cluster non scrivono negli stessi file contemporaneamente.

Se l'applicazione può ospitare più repliche che puntano alla stessa condivisione file, seguire i passaggi di migrazione senza stato e distribuire le definizioni YAML nel nuovo cluster. In caso contrario, un approccio di migrazione possibile prevede i passaggi seguenti:

* Verificare che l'applicazione funzioni correttamente.
* Puntare il traffico in tempo reale al nuovo cluster AKS.
* Scollegare il cluster precedente.

Se si desidera iniziare con una condivisione vuota ed eseguire una [`az storage file copy`](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) copia dei dati di origine, è possibile utilizzare i comandi per eseguire la migrazione dei dati.


#### <a name="migrating-persistent-volumes"></a>Migrazione di volumi persistenti

Se si esegue la migrazione di volumi persistenti esistenti ad AKS, in genere si esegue la procedura seguente:If you're migrating existing persistent volumes to AKS, you'll generally follow these steps:

* Disattivare le scritture nell'applicazione. Questo passaggio è facoltativo e richiede tempi di inattività.
* Creare istantanee dei dischi.
* Creare nuovi dischi gestiti dagli snapshot.
* Creare volumi persistenti in AKS.
* Aggiornare le specifiche del pod per [utilizzare i volumi esistenti](https://docs.microsoft.com/azure/aks/azure-disk-volume) anziché PersistentVolumeClaims (provisioning statico).
* Distribuire l'applicazione in AKS.
* Verificare che l'applicazione funzioni correttamente.
* Puntare il traffico in tempo reale al nuovo cluster AKS.

> [!IMPORTANT]
> Se si sceglie di non disattivare le scritture, sarà necessario replicare i dati nella nuova distribuzione. In caso contrario, si perderanno i dati scritti dopo lo scandito degli snapshot del disco.

Alcuni strumenti open source consentono di creare dischi gestiti ed eseguire la migrazione dei volumi tra i cluster Kubernetes:Some open-source tools can help you create managed disks and migrate volumes between Kubernetes clusters:

* [L'estensione Copia disco dell'interfaccia della riga](https://github.com/noelbundick/azure-cli-disk-copy-extension) di comando di Azure copia e converte i dischi tra gruppi di risorse e aree di Azure.Azure CLI Disk Copy extension copies and converts disks across resource groups and Azure regions.
* [L'estensione dell'interfaccia della riga](https://github.com/yaron2/azure-kube-cli) di comando di Azure Kube enumera i volumi di ACS Kubernetes e ne esegue la migrazione in un cluster AKS.


### <a name="deployment-of-your-cluster-configuration"></a>Distribuzione della configurazione del cluster

È consigliabile utilizzare la pipeline di integrazione continua (CI) e di recapito continuo (CD) esistente per distribuire una configurazione valida in AKS. È possibile usare le pipeline di Azure per [compilare e distribuire le applicazioni](https://docs.microsoft.com/azure/devops/pipelines/ecosystems/kubernetes/aks-template?view=azure-devops)in AKS . Clonare le attività di `kubeconfig` distribuzione esistenti e assicurarsi che punti al nuovo cluster AKS.

Se ciò non è possibile, esportare le definizioni delle risorse dal cluster Kubernetes esistente e quindi applicarle ad AKS. È possibile usare `kubectl` per esportare gli oggetti.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

### <a name="moving-existing-resources-to-another-region"></a>Spostamento delle risorse esistenti in un'altra area

È possibile spostare il cluster AKS in [un'area diversa supportata da AKS][region-availability]. È consigliabile creare un nuovo cluster nell'altra area, quindi distribuire le risorse e le applicazioni nel nuovo cluster. Inoltre, se nel cluster AKS sono in esecuzione servizi come [Azure Dev Spaces,][azure-dev-spaces] sarà necessario installare e configurare tali servizi nel cluster nella nuova area.


In questo articolo sono stati riepilogati i dettagli della migrazione per:In this article we summarized migration details for:

> [!div class="checklist"]
> * AKS con load Balancer standard e set di scalabilità di macchine virtuali
> * Servizi di Azure collegati esistenti
> * Garantire quote valideEnsure valid quotas
> * Disponibilità elevata e continuità aziendale
> * Considerazioni per le applicazioni senza statoConsiderations for stateless applications
> * Considerazioni per le applicazioni con statoConsiderations for stateful applications
> * Distribuzione della configurazione del cluster


[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/