---
title: Domande frequenti relative al servizio Azure Kubernetes
description: Risposte ad alcune delle domande comuni sul servizio Azure Kubernetes (AKS).
ms.topic: conceptual
ms.date: 10/02/2019
ms.openlocfilehash: c4bb4328af5df7f729967c7b249847b2ab098770
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79497762"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Domande frequenti relative al servizio Azure Kubernetes

Questo articolo tratta alcune domande frequenti relative al servizio Azure Kubernetes.

## <a name="which-azure-regions-currently-provide-aks"></a>Quali aree di Azure forniscono attualmente AKS?

Per un elenco completo delle aree disponibili, vedere [Aree Geografiche e disponibilità][aks-regions].

## <a name="does-aks-support-node-autoscaling"></a>servizio Azure Kubernetes supporta la scalabilità automatica dei nodi?

Sì, la possibilità di ridimensionare automaticamente i nodi agente orizzontalmente in AKS è attualmente disponibile in anteprima. Per istruzioni, vedere Scalabilità automatica di [un cluster per soddisfare le richieste delle applicazioni in AKS.][aks-cluster-autoscaler] La scalabilità automatica di AKS si basa sul [ridimensionamento automatico Kubernetes][auto-scaler].

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>È possibile distribuire servizio Azure Kubernetes nella rete virtuale esistente?

Sì, è possibile distribuire un cluster AKS in una rete virtuale esistente utilizzando la [funzionalità di rete avanzata.][aks-advanced-networking]

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Posso limitare chi ha accesso al server API Kubernetes?

Sì, è possibile limitare l'accesso al server API Kubernetes utilizzando [gli intervalli IP autorizzati][api-server-authorized-ip-ranges]al server API.

## <a name="can-i-make-the-kubernetes-api-server-accessible-only-within-my-virtual-network"></a>È possibile rendere il server API Kubernetes accessibile solo all'interno della rete virtuale?

Non in questo momento, ma questo è previsto. È possibile tenere traccia dello stato di avanzamento del [repository AKS GitHub][private-clusters-github-issue].

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>È possibile avere macchine virtuali diverse in un singolo cluster?

Sì, è possibile utilizzare macchine virtuali di dimensioni diverse nel cluster AKS creando [pool di più nodi.][multi-node-pools]

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Gli aggiornamenti della sicurezza vengono applicati ai nodi agente servizio Azure Kubernetes?

Azure applica automaticamente le patch di sicurezza ai nodi Linux nel cluster in base a una pianificazione notturna. Tuttavia, si è responsabili per garantire che i nodi Linux vengono riavviati in base alle esigenze. Sono disponibili diverse opzioni per il riavvio dei nodi:You have several options for rebooting nodes:

- Manualmente tramite il portale di Azure o l'interfaccia della riga di comando di Azure.
- Aggiornando il cluster servizio Azure Kubernetes. Il cluster aggiorna automaticamente i [nodi cordone e scarico][cordon-drain] e quindi porta in linea un nuovo nodo con l'immagine Ubuntu più recente e una nuova versione della patch o una versione secondaria di Kubernetes. Per altre informazioni, vedere [Aggiornare un cluster del servizio Azure Kubernetes][aks-upgrade].
- Utilizzando [Kured](https://github.com/weaveworks/kured), un daemon di riavvio open source per Kubernetes. Kured viene eseguito come [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) e monitora ogni nodo per la presenza di un file che indica che è necessario un riavvio. In tutto il cluster, i riavvii del sistema operativo sono gestiti dallo stesso [processo di cordone e svuotamento][cordon-drain] di un aggiornamento del cluster.

Per altre informazioni sull'utilizzo di Kured, vedere [Apply security and kernel updates to nodes in AKS][node-updates-kured] (Applicare aggiornamenti di sicurezza e del kernel ai nodi del servizio Azure Kubernetes).

### <a name="windows-server-nodes"></a>Nodi di Windows Server

Per i nodi di Windows Server (attualmente in anteprima in AKS), Windows Update non viene eseguito automaticamente e applica gli aggiornamenti più recenti. In base a una pianificazione regolare del ciclo di rilascio di Windows Update e del processo di convalida, è necessario eseguire un aggiornamento nel cluster e nei pool di nodi di Windows Server nel cluster AKS. Questo processo di aggiornamento crea nodi che eseguono l'immagine e le patch di Windows Server più recenti, quindi rimuove i nodi meno recenti. Per ulteriori informazioni su questo processo, vedere Aggiornare un pool di [nodi in AKS][nodepool-upgrade].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Perché vengono creati due gruppi di risorse con servizio Azure Kubernetes?

AKS si basa su una serie di risorse dell'infrastruttura di Azure, tra cui set di scalabilità di macchine virtuali, reti virtuali e dischi gestiti. In questo modo è possibile sfruttare molte delle funzionalità principali della piattaforma Azure all'interno dell'ambiente Kubernetes gestito fornito da AKS. Ad esempio, la maggior parte dei tipi di macchine virtuali di Azure può essere usata direttamente con AKS e le prenotazioni di Azure possono essere usate per ricevere automaticamente sconti su tali risorse.

Per abilitare questa architettura, ogni distribuzione AKS si estende su due gruppi di risorse:To enable this architecture, each AKS deployment spans two resource groups:

1. Creare il primo gruppo di risorse. Questo gruppo contiene solo la risorsa del servizio Kubernetes.This group contains only the Kubernetes service resource. Il provider di risorse AKS crea automaticamente il secondo gruppo di risorse durante la distribuzione. Un esempio del secondo gruppo *di risorse*è MC_myResourceGroup_myAKSCluster_eastus . Per informazioni su come specificare il nome di questo secondo gruppo di risorse, vedere la sezione successiva.
1. Il secondo gruppo di risorse, noto come gruppo di risorse del *nodo,* contiene tutte le risorse dell'infrastruttura associate al cluster. come ad esempio le macchine virtuali dei nodi Kubernetes, le risorse della rete virtuale e di archiviazione. Per impostazione predefinita, il gruppo di risorse del nodo ha un nome simile *a MC_myResourceGroup_myAKSCluster_eastus*. AKS elimina automaticamente la risorsa nodo ogni volta che il cluster viene eliminato, pertanto deve essere utilizzato solo per le risorse che condividono il ciclo di vita del cluster.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>È possibile specificare un nome personale per il gruppo di risorse del nodo AKS?

Sì. Per impostazione predefinita, AKS chiamerà il gruppo di risorse del nodo *MC_resourcegroupname_clustername_location*, ma è anche possibile fornire un nome personalizzato.

Per specificare il nome del gruppo di risorse, installare l'estensione [aks-preview][aks-preview-cli] dell'interfaccia della riga di comando di Azure versione *0.3.2* o successiva. Quando si crea un cluster AKS usando il comando [az aks create,][az-aks-create] usare il parametro *--node-resource-group* e specificare un nome per il gruppo di risorse. Se si usa un modello di Azure Resource Manager per distribuire un cluster AKS, è possibile definire il nome del gruppo di risorse usando la proprietà nodeResourceGroup.If you use [an Azure Resource Manager template][aks-rm-template] to deploy an AKS cluster, you can define the resource group name by using the *nodeResourceGroup* property.

* Il gruppo di risorse secondario viene creato automaticamente dal provider di risorse di Azure nella sottoscrizione.
* È possibile specificare un nome di gruppo di risorse personalizzato solo quando si crea il cluster.

Quando si lavora con il gruppo di risorse del nodo, tenere presente che non è possibile:As you work with the node resource group, keep in mind that you cannot:

* Specificare un gruppo di risorse esistente per il gruppo di risorse del nodo.
* Specificare una sottoscrizione diversa per il gruppo di risorse del nodo.
* Modificare il nome del gruppo di risorse del nodo dopo la creazione del cluster.
* Specificare i nomi per le risorse gestite all'interno del gruppo di risorse del nodo.
* Modificare o eliminare i tag delle risorse gestite all'interno del gruppo di risorse del nodo. (Vedere ulteriori informazioni nella sezione successiva.)

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>È possibile modificare i tag e altre proprietà delle risorse AKS nel gruppo di risorse del nodo?

Se si modificano o si eliminano tag creati da Azure e altre proprietà delle risorse nel gruppo di risorse del nodo, è possibile ottenere risultati imprevisti, ad esempio errori di ridimensionamento e aggiornamento. AKS consente di creare e modificare tag personalizzati. È possibile creare o modificare tag personalizzati, ad esempio per assegnare una Business Unit o un centro di costo. Modificando le risorse nel gruppo di risorse del nodo nel cluster AKS, si interrompe l'obiettivo del livello di servizio (SLO). Per ulteriori informazioni, vedere [AKS offre un contratto](#does-aks-offer-a-service-level-agreement) di servizio?

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Quali controller di ammissione Kubernetes supporta servizio Azure Kubernetes? È possibile aggiungere o rimuovere i controller di ammissione?

servizio Azure Kubernetes supporta i seguenti [controller di ammissione][admission-controllers]:

- *NamespaceLifecycle*
- *LimitRanger*
- *ServiceAccount*
- *DefaultStorageClass*
- *DefaultTolerationSeconds*
- *MutatingAdmissionWebhook*
- *ValidatingAdmissionWebhook*
- *ResourceQuota*

Attualmente, non è possibile modificare l'elenco dei controller di ammissione in AKS.

## <a name="is-azure-key-vault-integrated-with-aks"></a>Azure Key Vault è integrato in servizio Azure Kubernetes?

AKS isn't currently natively integrated with Azure Key Vault. Tuttavia, il progetto FlexVolume for Kubernetes di Azure Key Vault consente l'integrazione diretta dai pod Kubernetes ai segreti di Key Vault.However, the [Azure Key Vault FlexVolume for Kubernetes][keyvault-flexvolume] project enables direct integration from Kubernetes pods to Key Vault secrets.

## <a name="can-i-run-windows-server-containers-on-aks"></a>È possibile eseguire contenitori Windows Server in servizio Azure Kubernetes?

Sì, i contenitori di Windows Server sono disponibili in anteprima. Per eseguire i contenitori di Windows Server in AKS, creare un pool di nodi che esegue Windows Server come sistema operativo guest. I contenitori di Windows Server possono utilizzare solo Windows Server 2019.Windows Server containers can use only Windows Server 2019. Per iniziare, vedere [Creare un cluster AKS con un pool][aks-windows-cli]di nodi di Windows Server.

Il supporto di Windows Server per il pool di nodi include alcune limitazioni che fanno parte del progetto Upstream Windows Server in Kubernetes. Per ulteriori informazioni su queste limitazioni, vedere Contenitori di [Windows Server nelle limitazioni di AKS][aks-windows-limitations].

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS offre un contratto di servizio?

In un contratto di servizio, il provider accetta di rimborsare al cliente il costo del servizio se il livello di servizio pubblicato non viene soddisfatto. Dal momento che AKS è gratuito, non è disponibile alcun costo da rimborsare, quindi AKS non ha alcun sLA formale. Tuttavia, AKS cerca di mantenere la disponibilità di almeno il 99,5% per il server API Kubernetes.

È importante riconoscere la distinzione tra la disponibilità del servizio AKS che fa riferimento al tempo di attività del piano di controllo Kubernetes e la disponibilità del carico di lavoro specifico in esecuzione in Macchine virtuali di Azure.It is important to recognize the distinction between AKS service availability which refers to uptime of the Kubernetes control plane and the availability of your specific workload that is running on Azure Virtual Machines. Anche se il piano di controllo potrebbe non essere disponibile se il piano di controllo non è pronto, i carichi di lavoro del cluster in esecuzione nelle macchine virtuali di Azure possono comunque funzionare. Dato che le macchine virtuali di Azure sono risorse a pagamento, sono supportate da un servizio di integrazione finanziario. Leggere [qui per ulteriori dettagli](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) sul servizio di servizio della macchina virtuale di Azure e su come aumentare tale disponibilità con funzionalità come zone di [disponibilità][availability-zones].

## <a name="why-cant-i-set-maxpods-below-30"></a>Perché non posso impostare maxPods al di sotto di 30?

In AKS è possibile `maxPods` impostare il valore quando si crea il cluster usando i modelli dell'interfaccia della riga di comando di Azure e di Azure Resource Manager.In AKS, you can set the value when you create the cluster by using the Azure CLI and Azure Resource Manager templates. Tuttavia, sia Kubenet che Azure CNI richiedono un valore minimo (convalidato al momento della creazione):However, both Kubenet and Azure CNI require a *minimum value* (validated at creation time):

| Rete | Minima | Massimo |
| -- | :--: | :--: |
| CNI di AzureAzure CNI | 30 | 250 |
| Kubenet | 30 | 110 |

Poiché AKS è un servizio gestito, vengono distribuiti e gestiti componenti aggiuntivi e pod come parte del cluster. In passato, gli utenti `maxPods` potevano definire un valore inferiore al valore richiesto dai pod gestiti per l'esecuzione (ad esempio, 30). AKS ora calcola il numero minimo di pod utilizzando questa formula: ((maxPods o (maxPods - vm_count)) > minimo i contenitori gestiti add-on.

Gli utenti non possono `maxPods` ignorare la convalida minima.

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>È possibile applicare sconti di prenotazione di Azure ai nodi dell'agente AKS?

I nodi agente AKS vengono fatturati come macchine virtuali standard di Azure, pertanto se sono state acquistate [prenotazioni][reservation-discounts] di Azure per le dimensioni della macchina virtuale in uso in AKS, tali sconti vengono applicati automaticamente.

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>È possibile spostare/migrare il cluster tra tenant di Azure?

Il `az aks update-credentials` comando può essere usato per spostare un cluster AKS tra tenant di Azure.The command can be used to move an AKS cluster between Azure tenants. Seguire le istruzioni in [Scegliere per aggiornare o creare un'entità servizio](https://docs.microsoft.com/azure/aks/update-credentials) e quindi aggiornare il cluster [aks con nuove credenziali.](https://docs.microsoft.com/azure/aks/update-credentials#update-aks-cluster-with-new-service-principal-credentials)

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>È possibile spostare/migrare il cluster tra sottoscrizioni?

Lo spostamento di cluster tra sottoscrizioni non è attualmente supportato.

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>È possibile spostare i cluster AKS dalla sottoscrizione azure corrente a un'altra? 

Lo spostamento del cluster AKS e delle risorse associate tra sottoscrizioni di Azure non è supportato.

## <a name="why-is-my-cluster-delete-taking-so-long"></a>Perché l'eliminazione del cluster richiede così tanto tempo? 

La maggior parte dei cluster vengono eliminati su richiesta dell'utente; in alcuni casi, in particolare quando i clienti portano il proprio gruppo di risorse o l'eliminazione di attività cross-RG può richiedere più tempo o non riuscire. Se si verifica un problema con le eliminazioni, verificare di non disporre di blocchi su RG, che tutte le risorse esterne al RG siano dissociate dal RG e così via.

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>Se ho pod / distribuzioni nello stato 'NodeLost' o 'Unknown' posso comunque aggiornare il mio cluster?

È possibile, ma AKS non consiglia questo. Gli aggiornamenti dovrebbero essere eseguiti idealmente quando lo stato del cluster è noto e integro.

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>Se si dispone di un cluster con uno o più nodi in stato Non integro o arrestato, è possibile eseguire un aggiornamento?

No, eliminare/rimuovere tutti i nodi in stato di errore o altrimenti rimossi dal cluster prima dell'aggiornamento.

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>È stata eseguita un'eliminazione del cluster, ma viene visualizzato l'errore`[Errno 11001] getaddrinfo failed` 

In genere, ciò è causato da utenti con uno o più gruppi di sicurezza di rete (NSG) ancora in uso e associati al cluster.  Si prega di rimuoverli e riprovare a eliminare.

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>Ho eseguito un aggiornamento, ma ora i miei pod sono in loop di arresto anomalo, e le sonde di prontezza falliscono?

Verificare che l'entità servizio non sia scaduta.  Vedere: [AKS service principal](https://docs.microsoft.com/azure/aks/kubernetes-service-principal) e [aKS update credentials](https://docs.microsoft.com/azure/aks/update-credentials).

## <a name="my-cluster-was-working-but-suddenly-can-not-provision-loadbalancers-mount-pvcs-etc"></a>Il mio cluster funzionava, ma improvvisamente non è possibile eseguire il provisioning di LoadBalancers, montare PVC e così via? 

Verificare che l'entità servizio non sia scaduta.  Vedere: [AKS service principal](https://docs.microsoft.com/azure/aks/kubernetes-service-principal) e [aKS update credentials](https://docs.microsoft.com/azure/aks/update-credentials).

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>È possibile usare le API del set di scalabilità della macchina virtuale per la scalabilità manuale?

No, le operazioni di scalabilità usando le API del set di scalabilità delle macchine virtuali non sono supportate. Utilizzare le API AKS`az aks scale`( ).

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-0-nodes"></a>È possibile usare i set di scalabilità delle macchine virtuali per scalare manualmente a 0 nodi?

No, le operazioni di scalabilità usando le API del set di scalabilità delle macchine virtuali non sono supportate.

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>È possibile arrestare o deallocare tutte le macchine virtuali?

Sebbene AKS disponga di meccanismi di resilienza per resistere a tale configurazione e ripristinarne, questa non è una configurazione consigliata.

## <a name="can-i-use-custom-vm-extensions"></a>È possibile usare estensioni VM personalizzate?

Nessun AKS è un servizio gestito e la manipolazione delle risorse IaaS non è supportata. Per installare componenti personalizzati, ecc. si prega di sfruttare le API e i meccanismi Kubernetes. Ad esempio, sfruttare DaemonSets per installare i componenti necessari.

<!-- LINKS - internal -->

[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./autoscaler.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration.md
[node-updates-kured]: node-updates-kured.md
[aks-preview-cli]: /cli/azure/ext/aks-preview/aks
[az-aks-create]: /cli/azure/aks#az-aks-create
[aks-rm-template]: /azure/templates/microsoft.containerservice/2019-06-01/managedclusters
[aks-cluster-autoscaler]: cluster-autoscaler.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[aks-windows-cli]: windows-container-cli.md
[aks-windows-limitations]: windows-node-limitations.md
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
[api-server-authorized-ip-ranges]: ./api-server-authorized-ip-ranges.md
[multi-node-pools]: ./use-multiple-node-pools.md
[availability-zones]: ./availability-zones.md

<!-- LINKS - external -->
[aks-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948
