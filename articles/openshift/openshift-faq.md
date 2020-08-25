---
title: Domande frequenti su Azure Red Hat OpenShift
description: Di seguito sono riportate le risposte alle domande più comuni su Microsoft Azure Red Hat OpenShift
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 07/31/2020
ms.openlocfilehash: a8b5ec48b64341ad9eabd087d7ee20bb703198c6
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816236"
---
# <a name="azure-red-hat-openshift-faq"></a>Domande frequenti su Azure Red Hat OpenShift

Questo articolo risponde alle domande frequenti su Microsoft Azure Red Hat OpenShift.

## <a name="installation-and-upgrade"></a>Installazione e aggiornamento

### <a name="which-azure-regions-are-supported"></a>Quali aree di Azure sono supportate?

Per un elenco delle aree supportate per Azure Red Hat OpenShift 4. x, vedere [aree disponibili](https://docs.openshift.com/aro/4/welcome/index.html#available-regions).

Per un elenco delle aree supportate per Azure Red Hat OpenShift 3,11, vedere [prodotti disponibili in base all'area](supported-resources.md#azure-regions).

### <a name="what-virtual-machine-sizes-can-i-use"></a>Quali dimensioni della macchina virtuale è possibile usare?

Per un elenco delle dimensioni delle macchine virtuali supportate per Azure Red Hat OpenShift 4, vedere [risorse supportate per Azure Red Hat OpenShift 4](support-policies-v4.md).

Per un elenco delle dimensioni delle macchine virtuali supportate per Azure Red Hat OpenShift 3,11, vedere [risorse supportate per Azure Red Hat OpenShift 3,11](supported-resources.md).

### <a name="what-is-the-maximum-number-of-pods-in-an-azure-red-hat-openshift-cluster--what-is-the-maximum-number-of-pods-per-node-in-azure-red-hat-openshift"></a>Qual è il numero massimo di pod in un cluster Azure Red Hat OpenShift?  Qual è il numero massimo di pod per nodo in Azure Red Hat OpenShift?

Il numero effettivo di Pod supportati dipende dai requisiti di memoria, CPU e archiviazione di un'applicazione.

Azure Red Hat OpenShift 4. x ha un limite di 250 pod per nodo e un limite di nodi di calcolo 100. Il numero massimo di Pod supportati in un cluster viene limitato a 250 &times; 100 = 25.000.

Azure Red Hat OpenShift 3,11 ha un limite di 50 pod per nodo e un limite di 20 nodi di calcolo. Il numero massimo di Pod supportati in un cluster viene limitato a 50 &times; 20 = 1.000.

### <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>Un cluster può avere nodi di calcolo in più aree di Azure?

No. Tutti i nodi in un cluster Azure Red Hat OpenShift devono avere origine nella stessa area di Azure.

### <a name="can-a-cluster-be-deployed-across-multiple-availability-zones"></a>Un cluster può essere distribuito in più zone di disponibilità?

Sì. Questa operazione viene eseguita automaticamente se il cluster viene distribuito in un'area di Azure che supporta le zone di disponibilità. Per altre informazioni, vedere [Zone di disponibilità](../availability-zones/az-overview.md#availability-zones).

### <a name="are-control-plane-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>I nodi del piano di controllo sono astratti, così come sono con il servizio Azure Kubernetes (AKS)?

No. Tutte le risorse, inclusi i nodi master del cluster, vengono eseguite nella sottoscrizione del cliente. Questi tipi di risorse vengono inseriti in un gruppo di risorse di sola lettura.

### <a name="does-the-cluster-reside-in-a-customer-subscription"></a>Il cluster si trova in una sottoscrizione del cliente? 

L'applicazione gestita di Azure si trova in un gruppo di risorse bloccato con la sottoscrizione del cliente. I clienti possono visualizzare gli oggetti in tale gruppo di risorse, ma non modificarli.

### <a name="is-there-any-element-in-azure-red-hat-openshift-shared-with-other-customers-or-is-everything-independent"></a>È presente un elemento in Azure Red Hat OpenShift condiviso con altri clienti? O è tutto indipendente?

Ogni cluster Azure Red Hat OpenShift è dedicato a un determinato cliente e si trova all'interno della sottoscrizione del cliente. 

### <a name="are-infrastructure-nodes-available"></a>I nodi dell'infrastruttura sono disponibili?

Nei cluster OpenShift 4. x di Azure Red Hat, i nodi dell'infrastruttura non sono attualmente disponibili.

Nei cluster Azure Red Hat OpenShift 3,11 i nodi dell'infrastruttura sono inclusi per impostazione predefinita.

## <a name="how-do-i-handle-cluster-upgrades"></a>Ricerca per categorie gestire gli aggiornamenti del cluster?

Per informazioni sugli aggiornamenti, sulla manutenzione e sulle versioni supportate, vedere la [Guida del ciclo](support-lifecycle.md)di vita del supporto.

### <a name="how-will-the-host-operating-system-and-openshift-software-be-updated"></a>Come verrà aggiornato il sistema operativo host e il software OpenShift?

I sistemi operativi host e il software OpenShift vengono aggiornati poiché Azure Red Hat OpenShift usa le versioni di rilascio e le patch secondarie della piattaforma di contenitori OpenShift upstream.

### <a name="whats-the-process-to-reboot-the-updated-node"></a>Qual è il processo di riavvio del nodo aggiornato?

I nodi vengono riavviati come parte di un aggiornamento.

## <a name="cluster-operations"></a>Operazioni del cluster

### <a name="can-i-use-prometheus-to-monitor-my-applications"></a>È possibile utilizzare Prometeo per monitorare le applicazioni?

Prometeo è preinstallato e configurato per i cluster Azure Red Hat OpenShift 4. x. Scopri di più sul [monitoraggio del cluster](https://docs.openshift.com/container-platform/3.11/install_config/prometheus_cluster_monitoring.html).

Per i cluster Azure Red Hat OpenShift 3,11, è possibile distribuire Prometeo nello spazio dei nomi e monitorare le applicazioni nello spazio dei nomi. Per altre informazioni, vedere [deploy Prometheus instance in Azure Red Hat OpenShift cluster](howto-deploy-prometheus.md).

### <a name="can-i-use-prometheus-to-monitor-metrics-related-to-cluster-health-and-capacity"></a>È possibile usare Prometeo per monitorare le metriche correlate all'integrità e alla capacità del cluster?

In Azure Red Hat OpenShift 4. x: Sì.

In Azure Red Hat OpenShift 3,11: No.

### <a name="can-logs-of-underlying-vms-be-streamed-out-to-a-customer-log-analysis-system"></a>È possibile eseguire lo streaming di log delle macchine virtuali sottostanti in un sistema di analisi dei log dei clienti?

I log delle macchine virtuali sottostanti sono gestiti dal servizio gestito e non sono esposti ai clienti.

### <a name="how-can-a-customer-get-access-to-metrics-like-cpumemory-at-the-node-level-to-take-action-to-scale-debug-issues-etc-i-cannot-seem-to-run-kubectl-top-on-an-azure-red-hat-openshift-cluster"></a>In che modo un cliente può accedere a metriche come CPU/memoria a livello di nodo per intervenire su scalabilità, problemi di debug e così via? Non posso sembrare di eseguire kubectl top in un cluster Azure Red Hat OpenShift.

Per i cluster Azure Red Hat OpenShift 4. x, la console Web OpenShift contiene tutte le metriche a livello di nodo. Per ulteriori informazioni, vedere la documentazione di Red Hat sulla [visualizzazione delle informazioni sul cluster](https://docs.openshift.com/aro/4/web_console/using-dashboard-to-get-cluster-information.html).

Per i cluster Azure Red Hat OpenShift 3,11, i clienti possono accedere alle metriche della CPU/memoria a livello di nodo usando il comando `oc adm top nodes` o `kubectl top nodes` con il ruolo del cluster Customer-admin. I clienti possono anche accedere alle metriche della CPU/memoria di `pods` con il comando `oc adm top pods` o `kubectl top pods` .

### <a name="if-we-scale-up-the-deployment-how-do-azure-fault-domains-map-into-pod-placement-to-ensure-all-pods-for-a-service-do-not-get-knocked-out-by-a-failure-in-a-single-fault-domain"></a>Se viene eseguita la scalabilità verticale della distribuzione, in che modo i domini di errore di Azure eseguono il mapping alla selezione host di pod per assicurarsi che tutti i pod per un servizio non vengano eliminati da un errore in un singolo dominio di errore?

Quando si usano i set di scalabilità di macchine virtuali in Azure, sono disponibili per impostazione predefinita cinque domini di errore. Ogni istanza di macchina virtuale in un set di scalabilità viene inserita in uno di questi domini di errore. Ciò garantisce che le applicazioni distribuite nei nodi di calcolo in un cluster vengano inserite in domini di errore distinti.

Per altre informazioni, vedere [scelta del numero corretto di domini di errore per il set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-manage-fault-domains.md).

### <a name="is-there-a-way-to-manage-pod-placement"></a>Esiste un modo per gestire la selezione host di Pod?

I clienti hanno la possibilità di ottenere i nodi e visualizzare le etichette come amministratore del cliente. Questo consentirà di specificare come destinazione qualsiasi macchina virtuale nel set di scalabilità.

Quando si usano etichette specifiche, è necessario prestare attenzione:

- Il nome host non deve essere usato. Il nome host viene ruotato spesso con aggiornamenti e aggiornamenti e ne viene garantita la modifica.
- Se il cliente dispone di una richiesta di etichette specifiche o di una strategia di distribuzione, questa operazione può essere eseguita, ma richiede sforzi di progettazione e non è attualmente supportata.

Per altre informazioni, vedere [controllo della selezione host per pod](https://docs.openshift.com/aro/4/nodes/scheduling/nodes-scheduler-about.html).

### <a name="is-the-image-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>Il registro immagini è disponibile esternamente per poter usare strumenti come Jenkins?

Per i cluster 4. x, è necessario esporre un registro di sistema sicuro e configurare l'autenticazione. Per ulteriori informazioni, vedere la seguente documentazione di Red Hat:

- [Esposizione di un registro](https://docs.openshift.com/aro/4/registry/securing-exposing-registry.html)
- [Accesso al registro di sistema](https://docs.openshift.com/aro/4/registry/accessing-the-registry.html)

Per i cluster 3,11, il registro immagini Docker è disponibile. Il registro Docker è disponibile da `https://docker-registry.apps.<clustername>.<region>.azmosa.io/` . È anche possibile usare Container Registry di Azure.

## <a name="networking"></a>Rete

### <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>È possibile distribuire un cluster in una rete virtuale esistente?

Nei cluster 4. x è possibile distribuire un cluster in un VNet esistente.

Nei cluster 3,11 non è possibile distribuire un cluster in un VNet esistente. È possibile connettere un cluster Azure Red Hat OpenShift 3,11 a un VNet esistente tramite il peering.

### <a name="is-cross-namespace-networking-supported"></a>La rete tra gli spazi dei nomi è supportata?

Gli amministratori di singoli progetti e clienti possono personalizzare la rete tra gli spazi dei nomi (inclusa la negazione) per ogni progetto usando `NetworkPolicy` oggetti.

### <a name="i-am-trying-to-peer-into-a-virtual-network-in-a-different-subscription-but-getting-failed-to-get-vnet-cidr-error"></a>Si sta tentando di eseguire il peering in una rete virtuale in una sottoscrizione diversa, ma non è stato possibile ottenere l'errore CIDR VNet.

Nella sottoscrizione in cui è presente la rete virtuale, assicurarsi di registrare `Microsoft.ContainerService` il provider con il comando seguente: `az provider register -n Microsoft.ContainerService --wait`

### <a name="can-we-specify-ip-ranges-for-deployment-on-the-private-vnet-avoiding-clashes-with-other-corporate-vnets-once-peered"></a>È possibile specificare gli intervalli IP per la distribuzione nel VNet privato, evitando conflitti con altri reti virtuali aziendali una volta eseguito il peering?

Nei cluster 4. x è possibile specificare intervalli di indirizzi IP personalizzati.

Nei cluster 3,11, Azure Red Hat OpenShift supporta il peering VNet e consente al cliente di fornire un VNet a peer con e un CIDR VNet in cui la rete OpenShift funzionerà.

Il VNet creato da Azure Red Hat OpenShift verrà protetto e non accetterà modifiche di configurazione. Il VNet di cui è stato utilizzato il peering è controllato dal cliente e risiede nella sottoscrizione.

### <a name="is-the-software-defined-network-module-configurable"></a>Il modulo software defined Network è configurabile?

La rete definita dal software è `openshift-ovs-networkpolicy` e non è configurabile.

### <a name="what-azure-load-balancer-is-used-by-azure-red-hat-openshift--is-it-standard-or-basic-and-is-it-configurable"></a>Quale Azure Load Balancer viene usato da Azure Red Hat OpenShift?  È standard o Basic ed è configurabile?

Azure Red Hat OpenShift USA Azure Load Balancer standard e non è configurabile.

## <a name="permissions"></a>Autorizzazioni

### <a name="can-an-admin-manage-users-and-quotas"></a>Un amministratore può gestire gli utenti e le quote?

Sì. Un amministratore di Azure Red Hat OpenShift può gestire gli utenti e le quote oltre ad accedere a tutti i progetti creati dall'utente.

### <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>È possibile limitare un cluster solo a determinati utenti di Azure AD?

Sì. È possibile limitare il Azure AD gli utenti possono accedere a un cluster configurando l'applicazione Azure AD. Per informazioni dettagliate, vedere [procedura: limitare l'app a un set di utenti](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md).

### <a name="can-i-restrict-users-from-creating-projects"></a>È possibile impedire agli utenti di creare progetti?

Sì. Accedere al cluster come amministratore ed eseguire questo comando:

```
oc adm policy \
    remove-cluster-role-from-group self-provisioner \
    system:authenticated:oauth
```

Per ulteriori informazioni, vedere la documentazione di OpenShift sulla disabilitazione del provisioning automatico per la versione del cluster:

- [Disabilitazione del provisioning automatico in cluster 4,3](https://docs.openshift.com/aro/4/applications/projects/configuring-project-creation.html#disabling-project-self-provisioning_configuring-project-creation)
- [Disabilitazione del provisioning automatico in cluster 3,11](https://docs.openshift.com/container-platform/3.11/admin_guide/managing_projects.html#disabling-self-provisioning)

### <a name="which-unix-rights-in-iaas-are-available-for-mastersinfraapp-nodes"></a>Quali diritti UNIX (in IaaS) sono disponibili per i nodi master/infra/app?

Per i cluster 4. x, l'accesso al nodo è disponibile tramite il ruolo di amministratore del cluster. Per altre informazioni, vedere [Cenni preliminari sui RBAC](https://docs.openshift.com/container-platform/4.3/authentication/using-rbac.html).

Per i cluster 3,11, l'accesso ai nodi non è consentito.

### <a name="which-ocp-rights-do-we-have-cluster-admin-project-admin"></a>Quali sono i diritti di OCP? Amministrazione cluster Project-amministratore?

Per i cluster 4. x, è disponibile il ruolo di amministratore del cluster. Per altre informazioni, vedere [Cenni preliminari sui RBAC](https://docs.openshift.com/container-platform/4.3/authentication/using-rbac.html).

Per i cluster 3,11, vedere [Cenni preliminari sull'amministrazione del cluster](https://docs.openshift.com/aro/admin_guide/index.html) .

### <a name="which-identity-providers-are-available"></a>Quali provider di identità sono disponibili?

Per i cluster 4. x, è possibile configurare un provider di identità personalizzato. Per altre informazioni, vedere la documentazione di Red Hat sulla [configurazione dei ProDiver delle identità](https://docs.openshift.com/aro/4/authentication/identity_providers/configuring-ldap-identity-provider.html).

Per i cluster 3,11, è possibile usare l'integrazione Azure AD. 

## <a name="storage"></a>Archiviazione

### <a name="is-data-on-my-cluster-encrypted"></a>I dati nel cluster sono crittografati?

Per impostazione predefinita, i dati vengono crittografati a riposo. La piattaforma di archiviazione di Azure crittografa automaticamente i dati prima di renderli permanente ed esegue la decrittografia dei dati prima del recupero. Per altre informazioni, vedere [crittografia del servizio di archiviazione di Azure per i dati](../storage/common/storage-service-encryption.md)inattivi.

### <a name="is-data-stored-in-etcd-encrypted-on-azure-red-hat-openshift"></a>I dati vengono archiviati in etcd crittografati in Azure Red Hat OpenShift?

Per i cluster Azure Red Hat OpenShift 4, i dati non vengono crittografati per impostazione predefinita, ma è possibile abilitare la crittografia. Per ulteriori informazioni, vedere la guida alla [crittografia di ETCD](https://docs.openshift.com/container-platform/4.3/authentication/encrypting-etcd.html).

Per i cluster 3,11, i dati non vengono crittografati a livello di ETCD. L'opzione per attivare la crittografia non è attualmente supportata. OpenShift supporta questa funzionalità, ma sono necessari sforzi di progettazione per crearla sulla mappa stradale. I dati vengono crittografati a livello di disco. Per ulteriori informazioni, vedere [crittografia dei dati a livello di archivio dati](https://docs.openshift.com/container-platform/3.11/admin_guide/encrypting_data.html) .

### <a name="can-we-choose-any-persistent-storage-solution-like-ocs"></a>È possibile scegliere qualsiasi soluzione di archiviazione persistente, ad esempio OCS? 

Per i cluster 4. x, il disco di Azure (Premium_LRS) viene configurato come classe di archiviazione predefinita. Per i provider di archiviazione aggiuntivi e per i dettagli di configurazione (incluso file di Azure), vedere la documentazione di Red Hat sull' [archivio permanente](https://docs.openshift.com/aro/4/storage/understanding-persistent-storage.html).

Per i cluster 3,11, per impostazione predefinita vengono fornite due classi di archiviazione: una per i dischi di Azure (Premium_LRS) e una per file di Azure.

## <a name="does-aro-store-any-customer-data-outside-of-the-clusters-region"></a>ARO archivia i dati dei clienti al di fuori dell'area del cluster?

No. Tutti i dati creati in un cluster ARO vengono mantenuti all'interno dell'area del cluster.