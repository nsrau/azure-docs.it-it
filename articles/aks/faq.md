---
title: Domande frequenti relative al servizio Azure Kubernetes
description: Questo articolo include le risposte ad alcune domande frequenti sul servizio Azure Kubernetes.
ms.topic: conceptual
ms.date: 07/31/2020
ms.openlocfilehash: 6d30bc1faa6a896233d09f9247fe8c56f82371fa
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87485627"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Domande frequenti relative al servizio Azure Kubernetes

Questo articolo tratta alcune domande frequenti relative al servizio Azure Kubernetes.

## <a name="which-azure-regions-currently-provide-aks"></a>In quali aree di Azure è attualmente disponibile il servizio Azure Kubernetes?

Per un elenco completo delle aree disponibili, vedere [Aree e disponibilità del servizio Azure Kubernetes e disponibilità][aks-regions].

## <a name="can-i-spread-an-aks-cluster-across-regions"></a>È possibile distribuire un cluster del servizio Azure Kubernetes tra più aree?

No. I cluster del servizio Azure Kubernetes sono risorse a livello di area e non possono estendersi in più aree. Per le istruzioni su come creare un'architettura che includa più aree, vedere le [procedure consigliate per la continuità aziendale e il ripristino di emergenza][bcdr-bestpractices].

## <a name="can-i-spread-an-aks-cluster-across-availability-zones"></a>È possibile distribuire un cluster del servizio Azure Kubernetes tra zone di disponibilità?

Sì. È possibile distribuire un cluster del servizio Azure Kubernetes in una o più [zone di disponibilità][availability-zones] nelle [aree][az-regions] che le supportano.

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>È possibile limitare chi ha accesso al server API Kubernetes?

Sì. Sono disponibili due opzioni per limitare l'accesso al server API:

- Usare gli [intervalli IP autorizzati del server API][api-server-authorized-ip-ranges] per mantenere un endpoint pubblico per il server API ma limitare l'accesso a un set di indirizzi IP attendibili.
- Usare un [cluster privato][private-clusters] per limitare il server API in modo che sia accessibile *solo* dall'interno della rete virtuale.

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>È possibile avere dimensioni diverse delle VM in un singolo cluster?

Sì, è possibile usare dimensioni diverse delle macchine virtuali nel cluster del servizio Azure Kubernetes creando [più pool di nodi][multi-node-pools].

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Gli aggiornamenti della sicurezza vengono applicati ai nodi agente servizio Azure Kubernetes?

Azure applica automaticamente le patch di sicurezza ai nodi Linux del cluster in base a una pianificazione notturna. Tuttavia l'utente è responsabile di assicurarsi che i nodi Linux vengano riavviati come richiesto. Sono disponibili diverse opzioni per il riavvio dei nodi:

- Manualmente tramite il portale di Azure o l'interfaccia della riga di comando di Azure.
- Aggiornando il cluster servizio Azure Kubernetes. Gli aggiornamenti del cluster [bloccano e svuotano i nodi][cordon-drain], quindi portano un nuovo nodo online con l'immagine Ubuntu più recente e una nuova versione della patch o una versione secondaria di Kubernetes. Per altre informazioni, vedere [Aggiornare un cluster del servizio Azure Kubernetes][aks-upgrade].
- Usando [Kured](https://github.com/weaveworks/kured), un daemon di riavvio open source per Kubernetes. Kured, eseguito come [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/), esegue il monitoraggio di ogni nodo per verificare la presenza di un file che indica che è necessario un riavvio. All'interno del cluster i riavvii del sistema operativo vengono gestiti con lo stesso [processo di blocco e svuotamento][cordon-drain] dell'aggiornamento del cluster.

Per altre informazioni sull'uso di Kured, vedere [Applicare aggiornamenti di sicurezza e del kernel ai nodi nel servizio Azure Kubernetes][node-updates-kured].

### <a name="windows-server-nodes"></a>Nodi di Windows Server

Per i nodi di Windows Server, Windows Update non viene eseguito automaticamente per applicare gli aggiornamenti più recenti. In base a una pianificazione regolare secondo il ciclo di rilascio di Windows Update e per il processo di convalida interno, è consigliabile eseguire un aggiornamento nel cluster e nei pool del nodi di Windows Server nel servizio Azure Kubernetes. Questo processo di aggiornamento crea nodi che eseguono la versione più recente dell'immagine e delle patch di Windows Server e quindi rimuove i nodi precedenti. Per altre informazioni su questo processo, vedere [Aggiornare un pool di nodi nel servizio Azure Kubernetes][nodepool-upgrade].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Perché vengono creati due gruppi di risorse con servizio Azure Kubernetes?

Il servizio Azure Kubernetes si basa su una serie di risorse dell'infrastruttura di Azure, inclusi i set di scalabilità di macchine virtuali, le reti virtuali e i dischi gestiti. In questo modo è possibile sfruttare molte funzionalità essenziali della piattaforma Azure all'interno dell'ambiente Kubernetes gestito fornito dal servizio Azure Kubernetes. Ad esempio, la maggior parte dei tipi di macchine virtuali di Azure può essere usata direttamente con il servizio Azure Kubernetes ed è possibile usare le prenotazioni di Azure per ricevere automaticamente sconti su tali risorse.

Per rendere disponibile questa architettura, ogni distribuzione del servizio Azure Kubernetes include due gruppi di risorse:

1. Si crea il primo gruppo di risorse. Questo gruppo contiene solo la risorsa del servizio Kubernetes. Il provider di risorse del servizio Azure Kubernetes crea automaticamente il secondo gruppo di risorse durante la distribuzione. Un esempio del secondo gruppo di risorse è *MC_myResourceGroup_myAKSCluster_eastus*. Per informazioni su come specificare il nome di questo secondo gruppo di risorse, vedere la sezione successiva.
1. Il secondo gruppo di risorse, noto come *gruppo di risorse nodo*, contiene tutte le risorse di infrastruttura associate al cluster, come ad esempio le macchine virtuali dei nodi Kubernetes, le risorse della rete virtuale e di archiviazione. Per impostazione predefinita, il gruppo di risorse nodo ha un nome simile a *MC_myResourceGroup_myAKSCluster_eastus*. Il servizio Azure Kubernetes elimina automaticamente la risorsa nodo ogni volta che viene eliminato il cluster, quindi deve essere usato solo per le risorse che condividono il ciclo di vita del cluster.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>È possibile specificare un nome personalizzato per il gruppo di risorse nodo del servizio Azure Kubernetes?

Sì. Per impostazione predefinita, il servizio Azure Kubernetes assegna al gruppo di risorse nodo il nome *MC_resourcegroupname_clustername_location*, ma è anche possibile specificare un nome personalizzato.

Per specificare un nome personalizzato per il gruppo di risorse, installare l'estensione [aks-preview][aks-preview-cli] versione *0.3.2* o successiva dell'interfaccia della riga di comando di Azure. Quando si crea un cluster del servizio Azure Kubernetes con il comando [az aks create][az-aks-create], usare il parametro *--node-resource-group* e specificare un nome per il gruppo di risorse. Se si [usa un modello di Azure Resource Manager][aks-rm-template] per distribuire un cluster del servizio Azure Kubernetes, è possibile definire il nome del gruppo di risorse usando la proprietà *nodeResourceGroup*.

* Il gruppo di risorse secondario viene creato automaticamente dal provider di risorse di Azure nella propria sottoscrizione.
* È possibile specificare un nome di gruppo di risorse personalizzato solo quando si crea il cluster.

Quando si lavora con il gruppo di risorse nodo, tenere presente che non è possibile:

* Specificare un gruppo di risorse esistente come gruppo di risorse nodo.
* Specificare una sottoscrizione diversa per il gruppo di risorse nodo.
* Cambiare il nome del gruppo di risorse nodo dopo la creazione del cluster.
* Specificare i nomi delle risorse gestite nel gruppo di risorse del nodo.
* Modificare o eliminare i tag creati da Azure delle risorse gestite all'interno del gruppo di risorse del nodo. Per altre informazioni, vedere la sezione successiva.

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>È possibile modificare i tag e altre proprietà delle risorse del servizio Azure Kubernetes nel gruppo di risorse nodo?

La modifica o l'eliminazione di tag creati da Azure e di altre proprietà delle risorse nel gruppo di risorse nodo può generare risultati imprevisti, ad esempio errori di dimensionamento e di aggiornamento. AKS consente di creare e modificare i tag personalizzati creati dagli utenti finali ed è possibile aggiungere tali tag quando si [Crea un pool di nodi](use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool). Potrebbe essere necessario creare o modificare tag personalizzati da assegnare ad esempio a una business unit o a un centro di costo. Questa operazione può essere eseguita anche creando criteri di Azure con un ambito nel gruppo di risorse gestite.

Tuttavia, la modifica di eventuali **tag creati da Azure** sulle risorse nel gruppo di risorse nodo nel cluster AKS è un'azione non supportata che interrompe l'obiettivo del livello di servizio (SLO). Per altre informazioni, vedere [Il servizio Azure Kubernetes offre un contratto di servizio?](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Quali controller di ammissione Kubernetes supporta servizio Azure Kubernetes? È possibile aggiungere o rimuovere i controller di ammissione?

Il servizio Azure Kubernetes supporta i seguenti [controller di ammissione][admission-controllers]:

- *NamespaceLifecycle*
- *LimitRanger*
- *ServiceAccount*
- *DefaultStorageClass*
- *DefaultTolerationSeconds*
- *MutatingAdmissionWebhook*
- *ValidatingAdmissionWebhook*
- *ResourceQuota*

Attualmente non è possibile modificare l'elenco di controller di ammissione nel servizio Azure Kubernetes.

## <a name="can-i-use-admission-controller-webhooks-on-aks"></a>È possibile usare webhook dei controller di ammissione nel servizio Azure Kubernetes?

Sì, è possibile usare webhook dei controller di ammissione nel servizio Azure Kubernetes. È consigliabile escludere gli spazi dei nomi interni del servizio Azure Kubernetes contrassegnati con l'**etichetta del piano di controllo.** Ad esempio, aggiungere quanto segue alla configurazione del webhook:

```
namespaceSelector:
    matchExpressions:
    - key: control-plane
      operator: DoesNotExist
```

## <a name="can-admission-controller-webhooks-impact-kube-system-and-internal-aks-namespaces"></a>I webhook dei controller di ammissione possono influire sugli spazi dei nomi di kube-system e su quelli interni del servizio Azure Kubernetes?

Per proteggere la stabilità del sistema ed evitare che i controller di ammissione personalizzati influiscano sui servizi interni in kube-system, lo spazio dei nomi del servizio Azure Kubernetes include la funzionalità **Admissions Enforcer**, che esclude automaticamente gli spazi dei nomi di kube-system e quelli interni del servizio Azure Kubernetes. Questo servizio garantisce che i controller di ammissione personalizzati non influiscano sui servizi in esecuzione in kube-system.

Se si ha un caso d'uso critico per una distribuzione obbligatoria in kube-system (scelta non consigliata), che è necessario coprire con il webhook di ammissione personalizzato, è possibile aggiungere l'etichetta o l'annotazione seguente in modo che Admissions Enforcers lo ignori.

Etichetta ```"admissions.enforcer/disabled": "true"``` o annotazione ```"admissions.enforcer/disabled": true```

## <a name="is-azure-key-vault-integrated-with-aks"></a>Azure Key Vault è integrato in servizio Azure Kubernetes?

Il servizio Azure Kubernetes non è attualmente integrato in modalità nativa in Azure Key Vault. Tuttavia, il [provider di Azure Key Vault per l'archivio di segreti CSI][csi-driver] consente l'integrazione diretta dai pod di Kubernetes ai segreti di Key Vault.

## <a name="can-i-run-windows-server-containers-on-aks"></a>È possibile eseguire contenitori Windows Server in servizio Azure Kubernetes?

Sì, i contenitori di Windows Server sono disponibili nel servizio Azure Kubernetes. Per eseguire i contenitori di Windows Server nel servizio Azure Kubernetes, creare un pool di nodi che esegue Windows Server come sistema operativo guest. I contenitori di Windows Server possono usare solo Windows Server 2019. Per iniziare, vedere [Creare un cluster del servizio Azure Kubernetes con un pool di nodi di Windows Server][aks-windows-cli].

Il supporto di Windows Server per il pool di nodi include alcune limitazioni che fanno parte di Windows Server upstream nel progetto Kubernetes. Per altre informazioni su queste limitazioni, vedere [Limitazioni dei contenitori di Windows Server nel servizio Azure Kubernetes][aks-windows-limitations].

## <a name="does-aks-offer-a-service-level-agreement"></a>Il servizio Azure Kubernetes offre un contratto di servizio?

Il servizio Azure Kubernetes fornisce garanzie del contratto di servizio come funzionalità aggiuntiva facoltativa con il [contratto di servizio relativo al tempo di attività][uptime-sla].

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>È possibile applicare gli sconti per le prenotazioni di Azure ai nodi dell'agente del servizio Azure Kubernetes?

I nodi dell'agente del servizio Azure Kubernetes sono fatturati come macchine virtuali standard di Azure, pertanto se sono state acquistate [prenotazioni di Azure][reservation-discounts] per le dimensioni di VM in uso nel servizio Azure Kubernetes, gli sconti corrispondenti vengono applicati automaticamente.

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>È possibile spostare o eseguire la migrazione del cluster tra tenant di Azure?

Lo stato di trasferimento del cluster AKS tra i tenant non è al momento supportato.

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>È possibile spostare o eseguire la migrazione del cluster tra sottoscrizioni?

Lo spostamento di cluster tra sottoscrizioni non è al momento supportato.

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>È possibile spostare i cluster del servizio Azure Kubernetes dalla sottoscrizione di Azure corrente a un'altra? 

Lo stato di trasferimento del cluster AKS e delle risorse associate tra le sottoscrizioni di Azure non è supportato.

## <a name="can-i-move-my-aks-cluster-or-aks-infrastructure-resources-to-other-resource-groups-or-rename-them"></a>È possibile spostare il cluster AKS o le risorse dell'infrastruttura AKS in altri gruppi di risorse o rinominarli?

Lo stato di trasferimento o ridenominazione del cluster AKS e delle risorse associate non è supportato.

## <a name="why-is-my-cluster-delete-taking-so-long"></a>Perché l'eliminazione del cluster richiede molto tempo? 

La maggior parte dei cluster viene eliminata in seguito alla richiesta dell'utente. In alcuni casi, in particolare quando i clienti usano un gruppo di risorse personalizzato o eseguono l'eliminazione di attività tra più gruppi di risorse, l'operazione può richiedere più tempo oppure non riesce. In caso di problemi con le eliminazioni, verificare che non siano presenti blocchi sul gruppo di risorse, che le eventuali risorse esterne siano state disassociate dal gruppo di risorse e così via.

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>Nel caso di pod/distribuzioni con lo stato 'NodeLost' o 'Unknown', è comunque possibile aggiornare il cluster?

È possibile, ma questa operazione è sconsigliata nel servizio Azure Kubernetes. Gli aggiornamenti dovrebbero essere eseguiti idealmente quando lo stato del cluster è noto e integro.

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>Se un cluster include uno o più nodi arrestati o con uno stato non integro, è possibile eseguire un aggiornamento?

No, eliminare o rimuovere dal cluster tutti i nodi con lo stato di errore prima di procedere con l'aggiornamento.

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>Dopo l'eliminazione di un cluster viene visualizzato l'errore `[Errno 11001] getaddrinfo failed`. Perché? 

In genere, questo problema è dovuto al fatto che gli utenti hanno uno o più gruppi di sicurezza di rete ancora in uso e associati al cluster.  Rimuoverli e provare ripetere l'eliminazione.

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>Dopo un aggiornamento, si verificano arresti anomali ciclici dei pod e i probe di idoneità restituiscono un errore. Come si risolve il problema?

Verificare che l'entità servizio non sia scaduta.  Vedere [Entità servizio del servizio Azure Kubernetes](./kubernetes-service-principal.md) e [Credenziali per l'aggiornamento del servizio Azure Kubernetes](./update-credentials.md).

## <a name="my-cluster-was-working-but-suddenly-cannot-provision-loadbalancers-mount-pvcs-etc"></a>Il cluster funzionava ma improvvisamente non è possibile effettuare il provisioning di Load Balancer, montare PVC e così via. Come si risolve il problema? 

Verificare che l'entità servizio non sia scaduta.  Vedere [Entità servizio del servizio Azure Kubernetes](./kubernetes-service-principal.md) e [Credenziali per l'aggiornamento del servizio Azure Kubernetes](./update-credentials.md).

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>È possibile usare le API del set di scalabilità di macchine virtuali per il ridimensionamento manuale?

No, le operazioni di ridimensionamento con le API del set di scalabilità di macchine virtuali non sono supportate. Usare le API del servizio Azure Kubernetes (`az aks scale`).

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-0-nodes"></a>È possibile usare i set di scalabilità di macchine virtuali per il ridimensionamento manuale a 0 nodi?

No, le operazioni di ridimensionamento con le API del set di scalabilità di macchine virtuali non sono supportate.

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>È possibile arrestare o deallocare tutte le VM?

Anche se il servizio Azure Kubernetes include meccanismi di resilienza per tollerare una configurazione di questo tipo ed eseguire il successivo ripristino, questa scelta non è consigliata.

## <a name="can-i-use-custom-vm-extensions"></a>È possibile usare estensioni di VM personalizzate?

No. Il servizio Azure Kubernetes è un servizio gestito e la manipolazione delle risorse IaaS non è supportata. Per installare componenti personalizzati e così via, sfruttare le API e i meccanismi di Kubernetes. Ad esempio, usare DaemonSet per installare i componenti necessari.

## <a name="does-aks-store-any-customer-data-outside-of-the-clusters-region"></a>AKS archivia i dati dei clienti al di fuori dell'area del cluster?

No. Tutti i dati creati in un cluster AKS vengono mantenuti all'interno dell'area del cluster.

<!-- LINKS - internal -->

[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./cluster-autoscaler.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration-cli.md
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
[private-clusters]: ./private-clusters.md
[bcdr-bestpractices]: ./operator-best-practices-multi-region.md#plan-for-multiregion-deployment
[availability-zones]: ./availability-zones.md
[az-regions]: ../availability-zones/az-region.md
[uptime-sla]: ./uptime-sla.md

<!-- LINKS - external -->
[aks-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948
[csi-driver]: https://github.com/Azure/secrets-store-csi-driver-provider-azure
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines/
