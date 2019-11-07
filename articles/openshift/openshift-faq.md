---
title: Domande frequenti su Azure Red Hat OpenShift | Microsoft Docs
description: Di seguito sono riportate le risposte alle domande più comuni su Microsoft Azure Red Hat OpenShift
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 11/04/2019
ms.openlocfilehash: d8707e2edccf144cbe58a530bcfe2c176e656915
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582407"
---
# <a name="azure-red-hat-openshift-faq"></a>Domande frequenti su Azure Red Hat OpenShift

Questo articolo risponde alle domande frequenti su Microsoft Azure Red Hat OpenShift.

## <a name="which-azure-regions-are-supported"></a>Quali aree di Azure sono supportate?

Per un elenco delle aree globali in cui è supportato Azure Red Hat OpenShift, vedere [risorse supportate](supported-resources.md#azure-regions) .

## <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>È possibile distribuire un cluster in una rete virtuale esistente?

No. È tuttavia possibile connettere un cluster Azure Red Hat OpenShift a un VNET esistente tramite il peering. Per informazioni dettagliate, vedere [connettere la rete virtuale di un cluster a una rete virtuale esistente](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) .

## <a name="what-cluster-operations-are-available"></a>Quali sono le operazioni del cluster disponibili?

È possibile aumentare o ridurre il numero di nodi di calcolo. Non sono consentite altre modifiche alla risorsa `Microsoft.ContainerService/openShiftManagedClusters` dopo la creazione. Il numero massimo di nodi di calcolo è limitato a 20.

## <a name="what-virtual-machine-sizes-can-i-use"></a>Quali dimensioni della macchina virtuale è possibile usare?

Per un elenco delle dimensioni delle macchine virtuali che è possibile usare con un cluster Azure Red Hat OpenShift, vedere la pagina relativa alle [dimensioni delle macchine virtuali OpenShift di Azure Red Hat](supported-resources.md#virtual-machine-sizes) .

## <a name="is-data-on-my-cluster-encrypted"></a>I dati nel cluster sono crittografati?

Per impostazione predefinita, la crittografia è inattiva. La piattaforma di archiviazione di Azure crittografa automaticamente i dati prima di renderli permanente ed esegue la decrittografia dei dati prima del recupero. Per informazioni dettagliate, vedere [crittografia del servizio di archiviazione di Azure per i dati](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) inattivi.

## <a name="can-i-use-prometheusgrafana-to-monitor-my-applications"></a>È possibile utilizzare Prometeo/Grafana per monitorare le applicazioni?

Sì, è possibile distribuire Prometeo nello spazio dei nomi e monitorare le applicazioni nello spazio dei nomi.

## <a name="can-i-use-prometheusgrafana-to-monitor-metrics-related-to-cluster-health-and-capacity"></a>È possibile usare Prometeo/Grafana per monitorare le metriche correlate all'integrità e alla capacità del cluster?

No, non all'ora corrente.

## <a name="is-the-docker-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>Il registro Docker è disponibile esternamente per poter usare strumenti come Jenkins?

Il registro Docker è disponibile da `https://docker-registry.apps.<clustername>.<region>.azmosa.io/`, tuttavia, non è stata fornita una garanzia di durabilità di archiviazione avanzata. È anche possibile usare [container Registry di Azure](https://azure.microsoft.com/services/container-registry/).

## <a name="is-cross-namespace-networking-supported"></a>La rete tra gli spazi dei nomi è supportata?

Gli amministratori di singoli progetti e clienti possono personalizzare la rete tra spazi dei nomi (inclusa la negazione) per ogni progetto usando oggetti `NetworkPolicy`.

## <a name="can-an-admin-manage-users-and-quotas"></a>Un amministratore può gestire gli utenti e le quote?

Sì. Un amministratore di Azure Red Hat OpenShift può gestire gli utenti e le quote oltre ad accedere a tutti i progetti creati dall'utente.

## <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>È possibile limitare un cluster solo a determinati utenti di Azure AD?

Sì. È possibile limitare il Azure AD gli utenti possono accedere a un cluster configurando l'applicazione Azure AD. Per informazioni dettagliate, vedere [procedura: limitare l'app a un set di utenti](https://docs.microsoft.com/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users)

## <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>Un cluster può avere nodi di calcolo in più aree di Azure?

No. Tutti i nodi in un cluster Azure Red Hat OpenShift devono avere origine dalla stessa area di Azure.

## <a name="are-master-and-infrastructure-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>I nodi master e infrastruttura sono astratti, così come sono con il servizio Azure Kubernetes (AKS)?

No. Tutte le risorse, incluso il master del cluster, vengono eseguite nella sottoscrizione del cliente. Questi tipi di risorse vengono inseriti in un gruppo di risorse di sola lettura.

## <a name="is-open-service-broker-for-azure-osba-supported"></a>È possibile aprire Service Broker per Azure (OSBA)?

Sì. È possibile usare OSBA con Azure Red Hat OpenShift. Per altre informazioni, vedere [aprire Service Broker per Azure](https://github.com/Azure/open-service-broker-azure#openshift-project-template) .

## <a name="i-am-trying-to-peer-into-a-virtual-network-in-a-different-subscription-but-getting-failed-to-get-vnet-cidr-error"></a>Si sta tentando di eseguire il peering in una rete virtuale in una sottoscrizione diversa ma viene `Failed to get vnet CIDR` errore.

Nella sottoscrizione in cui è presente la rete virtuale, assicurarsi di registrare il provider `Microsoft.ContainerService` con `az provider register -n Microsoft.ContainerService --wait` 

## <a name="what-is-the-azure-red-hat-openshift-aro-maintenance-process"></a>Che cos'è il processo di manutenzione di Azure Red Hat OpenShift (ARO)?

Sono disponibili tre tipi di manutenzione per ARO: aggiornamenti, backup e ripristino dei dati di ETCD e manutenzione avviata dal provider di servizi cloud.

+ Gli aggiornamenti includono aggiornamenti software e CVEs. La correzione CVE si verifica all'avvio eseguendo `yum update` e fornisce la mitigazione immediata.  In parallelo, viene creata una nuova compilazione di immagine per creare cluster futuri.

+ Il backup e la gestione dei dati di ETCD sono processi automatici che possono richiedere tempi di inattività del cluster a seconda dell'azione. Se il database ETCD viene ripristinato da un backup, si verifica un tempo di inattività. Si esegue il backup di ETCD ogni ora e si mantengono le ultime 6 ore di backup.

+ La manutenzione avviata dal provider di servizi cloud include le interruzioni di rete, di archiviazione e a livello di area. La manutenzione dipende dal provider di servizi cloud e si basa sugli aggiornamenti forniti dal provider.

## <a name="what-is-the-general-upgrade-process"></a>Qual è il processo di aggiornamento generale?

L'esecuzione di un aggiornamento deve essere un processo sicuro da eseguire e non deve compromettere i servizi cluster. Il SRE può attivare il processo di aggiornamento quando sono disponibili nuove versioni o CVEs in attesa.

Gli aggiornamenti disponibili vengono testati in un ambiente di gestione temporanea e quindi applicati ai cluster di produzione. Quando applicato, un nuovo nodo viene aggiunto temporaneamente e i nodi vengono aggiornati in modo rotante in modo che i pod mantengano i conteggi delle repliche. Le procedure consigliate seguenti consentono di evitare tempi di inattività minimi.

A seconda della gravità dell'aggiornamento o dell'aggiornamento in sospeso, il processo potrebbe variare in modo che gli aggiornamenti vengano applicati rapidamente per attenuare l'esposizione del servizio a un CVE. Una nuova immagine verrà compilata in modo asincrono, testato e implementata come aggiornamento del cluster. A parte questo, non esiste alcuna differenza tra l'emergenza e la manutenzione pianificata. La manutenzione pianificata non è prepianificata con il cliente.

Se è richiesta la comunicazione con il cliente, le notifiche possono essere inviate tramite ICM e tramite posta elettronica.

## <a name="what-about-emergency-vs-planned-maintenance-windows"></a>Che cosa si intende per le finestre di manutenzione di emergenza e pianificate?

Non viene fatta distinzione tra i due tipi di manutenzione. I nostri team sono disponibili 24/7/365 e non usano le finestre di manutenzione pianificate "fuori orario" tradizionali.

## <a name="how-will-host-operating-system-and-openshift-software-be-updated"></a>In che modo verranno aggiornati i sistemi operativi host e il software OpenShift?

Il sistema operativo host e il software OpenShift vengono aggiornati tramite il processo generale di compilazione dell'aggiornamento e dell'immagine.

## <a name="whats-the-process-to-reboot-the-updated-node"></a>Qual è il processo di riavvio del nodo aggiornato?

Questa operazione deve essere gestita come parte di un aggiornamento.

## <a name="is-data-stored-in-etcd-encrypted-on-aro"></a>I dati vengono archiviati in etcd crittografati su ARO?

Non è crittografato a livello di ETCD. L'opzione per attivarla non è attualmente supportata. OpenShift supporta questa funzionalità, ma sono necessari sforzi di progettazione per crearla sulla mappa stradale. I dati vengono crittografati a livello di disco. Per ulteriori informazioni, vedere [crittografia dei dati a livello di archivio dati](https://docs.openshift.com/container-platform/3.11/admin_guide/encrypting_data.html) .

## <a name="can-logs-of-underlying-vms-be-streamed-out-to-a-customer-log-analysis-system"></a>È possibile eseguire lo streaming di log delle macchine virtuali sottostanti in un sistema di analisi dei log dei clienti?

Syslog, i log di Docker, Journal e dmesg vengono gestiti dal servizio gestito e non vengono esposti ai clienti.

## <a name="how-can-a-customer-get-access-to-metrics-like-cpumemory-at-the-node-level-to-take-action-to-scale-debug-issues-etc-i-cannot-seem-to-run-kubectl-top-on-an-aro-cluster"></a>In che modo un cliente può accedere a metriche come CPU/memoria a livello di nodo per intervenire su scalabilità, problemi di debug e così via. Non posso sembrare di eseguire `kubectl top` in un cluster ARO.

`kubectl top` non è disponibile in Red Hat OpenShift. Richiede un'origine metrica di backup, Heapster (deprecata) o Metrics-Server (incubating o Alpha), nessuno dei quali è incluso nello stack di monitoraggio OpenShift.

## <a name="what-is-the-default-pod-scheduler-configuration-for-aro"></a>Qual è la configurazione dell'utilità di pianificazione Pod predefinita per ARO?

ARO usa l'utilità di pianificazione predefinita fornita in OpenShift. Sono disponibili un paio di meccanismi aggiuntivi che non sono supportati in ARO. Per altri dettagli, vedere la [documentazione dell'utilità di pianificazione predefinita](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/scheduler.html#generic-scheduler) e la [documentazione dell'utilità di pianificazione master](https://github.com/openshift/openshift-azure/blob/master/pkg/startup/v6/data/master/etc/origin/master/scheduler.json) .

La pianificazione avanzata/personalizzata non è attualmente supportata. Per altri dettagli, vedere la [documentazione relativa alla pianificazione](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/index.html) .

## <a name="if-we-scale-up-the-deployment-how-do-azure-fault-domains-map-into-pod-placement-to-ensure-all-pods-for-a-service-do-not-get-knocked-out-by-a-failure-in-a-single-fault-domain"></a>Se viene eseguita la scalabilità verticale della distribuzione, in che modo i domini di errore di Azure eseguono il mapping alla selezione host di pod per assicurarsi che tutti i pod per un servizio non vengano eliminati da un errore in un singolo dominio di errore?

Quando si usano i set di scalabilità di macchine virtuali in Azure, sono disponibili per impostazione predefinita cinque domini di errore. Ogni istanza di macchina virtuale in un set di scalabilità viene inserita in uno di questi domini di errore. Ciò garantisce che le applicazioni distribuite nei nodi di calcolo in un cluster vengano inserite in domini di errore distinti.

Per altri dettagli, vedere [scelta del numero corretto di domini di errore per il set di scalabilità di macchine virtuali](https://docs.microsoft.com//azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-fault-domains) .

## <a name="is-there-a-way-to-manage-pod-placement"></a>Esiste un modo per gestire la selezione host di Pod?

Con l'imminente aggiornamento dell'amministratore del cliente, i clienti avranno la possibilità di ottenere i nodi e visualizzare le etichette.  Questo consentirà di specificare come destinazione qualsiasi macchina virtuale nel set di scalabilità.

Quando si usano etichette specifiche, è necessario prestare attenzione:

- Il nome host non deve essere usato. Il nome host viene ruotato spesso con aggiornamenti e aggiornamenti e ne viene garantita la modifica.

- Se il cliente dispone di una richiesta di etichette specifiche o di una strategia di distribuzione, questa operazione può essere eseguita, ma richiede sforzi di progettazione e non è attualmente supportata.

## <a name="what-is-the-maximum-number-of-pods-in-an-aro-cluster-what-is-the-maximum-number-of-pods-per-node-in-aro"></a>Qual è il numero massimo di pod in un cluster ARO?  Qual è il numero massimo di pod per nodo in ARO?

Per altri dettagli, vedere la [documentazione di upstream OpenShift](https://docs.openshift.com/container-platform/3.11/scaling_performance/cluster_limits.html#scaling-performance-current-cluster-limits) . Red Hat OpenShift 3,11 ha un limite di 250 Pod/nodo, mentre [Aro ha un limite di 20 nodi di calcolo](https://docs.microsoft.com/azure/openshift/openshift-faq#what-cluster-operations-are-available), in modo che il numero massimo di Pod supportati in un cluster Aro sia pari a 250 * 20 = 5000.

## <a name="can-we-specify-ip-ranges-for-deployment-on-the-private-vnet-avoiding-clashes-with-other-corporate-vnets-once-peered"></a>È possibile specificare gli intervalli IP per la distribuzione nel VNET privato, evitando conflitti con altri reti virtuali aziendali una volta eseguito il peering?

Azure Red Hat OpenShift supporta il peering VNET e consente al cliente di fornire un VNET a peer con e un CIDR VNET in cui la rete OpenShift funzionerà.

Il VNET creato da ARO verrà protetto e non accetterà modifiche di configurazione. Il VNET di cui è stato utilizzato il peering è controllato dal cliente e risiede nella sottoscrizione.

## <a name="does-the-cluster-reside-in-a-customer-subscription"></a>Il cluster si trova in una sottoscrizione del cliente? 

L'applicazione gestita di Azure si trova in un gruppo di risorse bloccato con la sottoscrizione del cliente. Il cliente può visualizzare gli oggetti in tale RG ma non modificarli.

## <a name="is-the-sdn-module-configurable"></a>Il modulo SDN è configurabile?

SDN è OpenShift-OVS-networkpolicy e non è configurabile.

## <a name="which-unix-rights-in-iaas-are-available-for-mastersinfraapp-nodes"></a>Quali diritti UNIX (in IaaS) sono disponibili per i nodi master/infra/app?

Non applicabile a questa offerta. Accesso al nodo non consentito.

## <a name="which-ocp-rights-do-we-have-cluster-admin-project-admin"></a>Quali sono i diritti di OCP? Amministrazione cluster Project-amministratore?

Per informazioni dettagliate, vedere [Cenni preliminari sull'amministrazione del cluster](https://docs.openshift.com/aro/admin_guide/index.html)Red Hat OpenShift di Azure.

## <a name="which-kind-of-federation-with-ldap"></a>Quale tipo di federazione con LDAP?

Questa operazione viene eseguita tramite l'integrazione Azure AD. 

## <a name="is-there-any-element-in-aro-shared-with-other-customers-or-is-everything-independent"></a>È presente un elemento in ARO condiviso con altri clienti? O è tutto indipendente?

Ogni cluster Azure Red Hat OpenShift è dedicato a un determinato cliente e si trova all'interno della sottoscrizione del cliente. 

## <a name="can-we-choose-any-persistent-storage-solution-like-ocs"></a>È possibile scegliere qualsiasi soluzione di archiviazione persistente, ad esempio OCS? 

È possibile scegliere tra due classi di archiviazione: disco di Azure e file di Azure.

## <a name="how-is-a-cluster-updated-including-majors-and-minors-due-to-vulnerabilities"></a>In che modo viene aggiornato un cluster, inclusi i principali e i minorenni a causa di vulnerabilità?

Vedere [Qual è il processo di aggiornamento generale?](https://docs.microsoft.com/azure/openshift/openshift-faq#what-is-the-general-upgrade-process)

## <a name="what-azure-load-balancer-is-used-by-aro-is-it-standard-or-basic-and-is-it-configurable"></a>Quale servizio di bilanciamento del carico di Azure viene usato da ARO?  È standard o Basic ed è configurabile?

ARO USA Azure Load Balancer standard e non è configurabile.

## <a name="can-aro-use-netapp-based-storage"></a>Gli ARO possono usare l'archiviazione basata su NetApp?

Al momento le uniche opzioni di archiviazione supportate sono le classi del disco di Azure e di archiviazione file di Azure. 


