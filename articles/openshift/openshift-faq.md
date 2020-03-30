---
title: Domande frequenti per Azure Red Hat OpenShift
description: Ecco le risposte alle domande comuni su Microsoft Azure Red Hat OpenShift
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f468cb294d79c44f92ef95437c0d88639a78b9a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77619491"
---
# <a name="azure-red-hat-openshift-faq"></a>Azure Red Hat OpenShift FAQ

In questo articolo vengono illustrate le domande frequenti (FAQ) su Microsoft Azure Red Hat OpenShift.This article addresses frequently asked questions (FAQs) about Microsoft Azure Red Hat OpenShift.

## <a name="which-azure-regions-are-supported"></a>Quali aree di Azure sono supportate?

Vedere [Risorse supportate](supported-resources.md#azure-regions) per un elenco delle aree globali in cui è supportato Azure Red Hat OpenShift.See Supported resources for a list of global regions where Azure Red Hat OpenShift is supported.

## <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>È possibile distribuire un cluster in una rete virtuale esistente?

No. È tuttavia possibile connettere un cluster Azure Red Hat OpenShift a una rete virtuale esistente tramite peering. Per informazioni [dettagliate,](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) vedere Connettere la rete virtuale di un cluster a una rete virtuale esistente.

## <a name="what-cluster-operations-are-available"></a>Quali operazioni del cluster sono disponibili?

È possibile aumentare o ridurre solo il numero di nodi di calcolo. Non sono consentite altre `Microsoft.ContainerService/openShiftManagedClusters` modifiche alla risorsa dopo la creazione. Il numero massimo di nodi di calcolo è limitato a 20.The maximum number of compute nodes is limited to 20.

## <a name="what-virtual-machine-sizes-can-i-use"></a>Quali dimensioni di macchina virtuale è possibile utilizzare?

Vedere Dimensioni delle macchine virtuali Azure Red Hat OpenShift per un elenco delle dimensioni delle macchine virtuali che è possibile usare con un cluster OpenShift di Azure Red [Hat.See Azure Red Hat OpenShift virtual machine sizes](supported-resources.md#virtual-machine-sizes) for a list of virtual machine sizes you can use with an Azure Red Hat OpenShift cluster.

## <a name="is-data-on-my-cluster-encrypted"></a>I dati nel cluster sono crittografati?

Per impostazione predefinita, è presente la crittografia inattivi. La piattaforma Archiviazione di Azure crittografa automaticamente i dati prima di perseri li e decrittografa i dati prima del recupero. Per informazioni dettagliate, vedere [Crittografia del servizio di archiviazione di Azure per i dati inattivi.](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)

## <a name="can-i-use-prometheusgrafana-to-monitor-my-applications"></a>Posso usare Prometheus/Grafana per monitorare le mie applicazioni?

Sì, è possibile distribuire Prometheus nello spazio dei nomi e monitorare le applicazioni nello spazio dei nomi.

## <a name="can-i-use-prometheusgrafana-to-monitor-metrics-related-to-cluster-health-and-capacity"></a>È possibile utilizzare Prometheus/Grafana per monitorare le metriche relative all'integrità e alla capacità del cluster?

No, non all'ora attuale.

## <a name="is-the-docker-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>Il Registro di sistema Docker è disponibile esternamente in modo da poter utilizzare strumenti come Jenkins?

Il Registro di `https://docker-registry.apps.<clustername>.<region>.azmosa.io/` sistema Docker è disponibile da Tuttavia, non viene fornita una garanzia di durabilità di archiviazione forte. È anche possibile usare Registro di sistema [del contenitore](https://azure.microsoft.com/services/container-registry/)di Azure .You can also use Azure Container Registry .

## <a name="is-cross-namespace-networking-supported"></a>La rete tra spazi dei nomi è supportata?

Gli amministratori di clienti e singoli progetti possono personalizzare la rete tra `NetworkPolicy` spazi dei nomi (inclusa la negazione) per ogni progetto utilizzando gli oggetti.

## <a name="can-an-admin-manage-users-and-quotas"></a>Un amministratore può gestire utenti e quote?

Sì. Un amministratore di Azure Red Hat OpenShift può gestire utenti e quote oltre ad accedere a tutti i progetti creati dall'utente.

## <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>È possibile limitare un cluster solo a determinati utenti di Azure AD?

Sì. È possibile limitare gli utenti di Azure AD che possono accedere a un cluster configurando l'applicazione Azure AD. Per informazioni dettagliate, vedere [Procedura: Limitare l'app a un set di utenti](https://docs.microsoft.com/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users)

## <a name="can-i-restrict-users-from-creating-projects"></a>È possibile limitare la creazione di progetti agli utenti?

Sì. Accedere al cluster come amministratore di Azure Red Hat OpenShift ed eseguire questo comando:Log in to your cluster as an Azure Red Hat OpenShift administrator and execute this command:

```
oc adm policy \
    remove-cluster-role-from-group self-provisioner \
    system:authenticated:oauth
```

Per ulteriori informazioni, consultate la documentazione di OpenShift sulla [disabilitazione del provisioning automatico.](https://docs.openshift.com/container-platform/3.11/admin_guide/managing_projects.html#disabling-self-provisioning)

## <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>Un cluster può avere nodi di calcolo in più aree di Azure?

No. Tutti i nodi in un cluster Azure Red Hat OpenShift devono avere origine dalla stessa area di Azure.All nodes in an Azure Red Hat OpenShift cluster must originate from the same Azure region.

## <a name="are-master-and-infrastructure-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>I nodi master e dell'infrastruttura vengono astratti come con il servizio Azure Kubernetes (AKS)?

No. Tutte le risorse, incluso il master cluster, vengono eseguite nella sottoscrizione del cliente. Questi tipi di risorse vengono inseriti in un gruppo di risorse di sola lettura.

## <a name="is-open-service-broker-for-azure-osba-supported"></a>Open Service Broker per Azure (OSBA) è supportato?

Sì. È possibile usare OSBA con Azure Red Hat OpenShift.You can use OSBA with Azure Red Hat OpenShift. Per altre informazioni, vedere [Aprire Service Broker per Azure.See Open Service Broker for Azure](https://github.com/Azure/open-service-broker-azure#openshift-project-template) for more information.

## <a name="i-am-trying-to-peer-into-a-virtual-network-in-a-different-subscription-but-getting-failed-to-get-vnet-cidr-error"></a>Sto cercando di eseguire il peerina `Failed to get vnet CIDR` in una rete virtuale in una sottoscrizione diversa, ma viene visualizzato un errore.

Nella sottoscrizione che dispone della rete virtuale, assicurarsi di registrare `Microsoft.ContainerService` il provider con`az provider register -n Microsoft.ContainerService --wait` 

## <a name="what-is-the-azure-red-hat-openshift-aro-maintenance-process"></a>Che cos'è il processo di manutenzione di Azure Red Hat OpenShift (ARO)?

Esistono tre tipi di manutenzione per ARO: aggiornamenti, backup e ripristino dei dati etcD e manutenzione avviata dal provider di cloud.

+ Gli aggiornamenti includono aggiornamenti software e CME. La correzione di CVE `yum update` si verifica all'avvio eseguendo e fornisce una mitigazione immediata.  In parallelo verrà creata una nuova build dell'immagine per le future creazioni del cluster.

+ Il backup e la gestione dei dati etcd è un processo automatizzato che può richiedere tempi di inattività del cluster a seconda dell'azione. Se il database etcd viene ripristinato da un backup, si verificano tempi di inattività. Facciamo il backup eccd ogni ora e manteniamo le ultime 6 ore di backup.

+ La manutenzione avviata dal provider cloud include interruzioni di rete, archiviazione e regionali. La manutenzione dipende dal provider di cloud e si basa sugli aggiornamenti forniti dal provider.

## <a name="what-is-the-general-upgrade-process"></a>Qual è il processo di aggiornamento generale?

L'esecuzione di un aggiornamento deve essere un processo sicuro da eseguire e non deve interrompere i servizi cluster. SRE può attivare il processo di aggiornamento quando sono disponibili nuove versioni o CVEs sono in sospeso.

Gli aggiornamenti disponibili vengono testati in un ambiente di fase e quindi applicati ai cluster di produzione. Quando viene applicato, un nuovo nodo viene temporaneamente aggiunto e i nodi vengono aggiornati in modo rotante in modo che i pod mantengano i conteggi delle repliche. Le procedure consigliate seguenti contribuiscono a garantire tempi di inattività minimi o non minimi.

A seconda della gravità dell'aggiornamento o dell'aggiornamento in sospeso, il processo potrebbe differire in quanto gli aggiornamenti potrebbero essere applicati rapidamente per ridurre l'esposizione del servizio a un CVE. Una nuova immagine verrà compilata in modo asincrono, testato e implementato come aggiornamento del cluster. A parte questo, non vi è alcuna differenza tra la manutenzione di emergenza e quella pianificata. La manutenzione pianificata non è preprogrammata con il cliente.

Le notifiche possono essere inviate tramite ICM ed e-mail se è richiesta la comunicazione con il cliente.

## <a name="what-about-emergency-vs-planned-maintenance-windows"></a>Che dire di emergenza vs finestre di manutenzione pianificata?

Non distinguiamo tra i due tipi di manutenzione. I nostri team sono disponibili 24 ore su 24, 7/365 e non utilizzano le tradizionali finestre di manutenzione "fuori orario" programmate.

## <a name="how-will-host-operating-system-and-openshift-software-be-updated"></a>Come verranno aggiornati il sistema operativo host e il software OpenShift?

Il sistema operativo host e il software OpenShift vengono aggiornati tramite il processo generale di aggiornamento e creazione di immagini.

## <a name="whats-the-process-to-reboot-the-updated-node"></a>Qual è il processo per riavviare il nodo aggiornato?

Questo deve essere gestito come parte di un aggiornamento.

## <a name="is-data-stored-in-etcd-encrypted-on-aro"></a>I dati memorizzati in ecc. sono criptati su ARO?

Non è crittografato a livello etcd. L'opzione per attivarla non è attualmente supportata. OpenShift supporta questa funzionalità, ma sono necessari sforzi di progettazione per essere sulla tabella di marcia. I dati vengono crittografati a livello di disco. Per ulteriori informazioni, fare riferimento a [Crittografia dei dati a livello di archivio dati.](https://docs.openshift.com/container-platform/3.11/admin_guide/encrypting_data.html)

## <a name="can-logs-of-underlying-vms-be-streamed-out-to-a-customer-log-analysis-system"></a>È possibile trasmettere i log delle macchine virtuali sottostanti a un sistema di analisi dei log dei clienti?

Syslog, docker logs, journal e dmesg vengono gestiti dal servizio gestito e non sono esposti ai clienti.

## <a name="how-can-a-customer-get-access-to-metrics-like-cpumemory-at-the-node-level-to-take-action-to-scale-debug-issues-etc-i-cannot-seem-to-run-kubectl-top-on-an-aro-cluster"></a>In che modo un cliente può ottenere l'accesso a metriche come CPU/memoria a livello di nodo per intervenire per scalare, eseguire il debug dei problemi e così via. Non riesco a `kubectl top` funzionare su un cluster ARO.

I clienti possono accedere alle metriche di CPU/memoria a livello di nodo utilizzando il comando `oc adm top nodes` o `kubectl top nodes` con il clusterrole dell'amministratore del cliente.  I clienti possono anche accedere `pods` alle metriche CPU/Memoria di con il comando `oc adm top pods` o`kubectl top pods`

## <a name="what-is-the-default-pod-scheduler-configuration-for-aro"></a>Qual è la configurazione predefinita dell'utilità di pianificazione pod per ARO?

ARO utilizza l'utilità di pianificazione predefinita fornita in OpenShift. Esistono un paio di meccanismi aggiuntivi che non sono supportati in ARO. Per ulteriori dettagli, fare riferimento alla [documentazione dell'utilità di pianificazione predefinita](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/scheduler.html#generic-scheduler) e alla [documentazione dell'utilità di pianificazione master.](https://github.com/openshift/openshift-azure/blob/master/pkg/startup/v6/data/master/etc/origin/master/scheduler.json)

La pianificazione avanzata/personalizzata non è attualmente supportata. Fare riferimento alla [documentazione relativa](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/index.html) alla pianificazione per ulteriori dettagli.

## <a name="if-we-scale-up-the-deployment-how-do-azure-fault-domains-map-into-pod-placement-to-ensure-all-pods-for-a-service-do-not-get-knocked-out-by-a-failure-in-a-single-fault-domain"></a>Se si aumenta la scalabilità verticale della distribuzione, in che modo i domini di errore di Azure vengono mappati al posizionamento del pod per garantire che tutti i pod per un servizio non vengano eliminati da un errore in un singolo dominio di errore?

Esistono per impostazione predefinita cinque domini di errore quando si usano set di scalabilità di macchine virtuali in Azure.There are by default five fault domains when using virtual machine scale sets in Azure. Ogni istanza di macchina virtuale in un set di scalabilità verrà inserita in uno di questi domini di errore. In questo modo si garantisce che le applicazioni distribuite ai nodi di calcolo in un cluster vengano inserite in domini di errore separati.

Per ulteriori informazioni, vedere Scelta del numero corretto di domini di errore per la scalabilità delle [macchine virtuali.](https://docs.microsoft.com//azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-fault-domains)

## <a name="is-there-a-way-to-manage-pod-placement"></a>C'è un modo per gestire il posizionamento del pod?

I clienti hanno la possibilità di ottenere nodi e visualizzare le etichette come amministratore del cliente.  Ciò fornirà un modo per indirizzare qualsiasi macchina virtuale nel set di scalabilità.

Attenzione deve essere usato quando si utilizzano etichette specifiche:

- Il nome host non deve essere utilizzato. Hostname viene ruotato spesso con aggiornamenti e aggiornamenti ed è garantito che cambi.

- Se il cliente ha una richiesta per etichette specifiche o una strategia di distribuzione, questa operazione potrebbe essere eseguita ma richiederebbe sforzi di progettazione e non è attualmente supportata.

## <a name="what-is-the-maximum-number-of-pods-in-an-aro-cluster-what-is-the-maximum-number-of-pods-per-node-in-aro"></a>Qual è il numero massimo di pod in un cluster ARO?Qual è il numero massimo di pod per nodo in ARO?

 Azure Red Hat OpenShift 3.11 ha un limite di 50 pod per nodo con ARO con un limite di [nodo di calcolo di 20](https://docs.microsoft.com/azure/openshift/openshift-faq#what-cluster-operations-are-available), in modo che limiti il numero massimo di pod supportati in un cluster ARO a 50, 20 e 1000.

## <a name="can-we-specify-ip-ranges-for-deployment-on-the-private-vnet-avoiding-clashes-with-other-corporate-vnets-once-peered"></a>È possibile specificare gli intervalli IP per la distribuzione nella rete virtuale privata, evitando interferenze con altre reti virtuali aziendali una volta sottoposte a peering?

Azure Red Hat OpenShift supporta il peering VNET e consente al cliente di fornire una rete virtuale con cui eseguire il peering e una CIDR VNET in cui opererà la rete OpenShift.

Il disco rigido creato da ARO sarà protetto e non accetterà modifiche alla configurazione. La rete virtuale di cui viene eseguito il peered è controllata dal cliente e si trova nella sottoscrizione.

## <a name="does-the-cluster-reside-in-a-customer-subscription"></a>Il cluster si trova in una sottoscrizione cliente? 

L'applicazione gestita di Azure si trova in un gruppo di risorse bloccato con la sottoscrizione del cliente. Il cliente può visualizzare gli oggetti in tale RG ma non modificarli.

## <a name="is-the-sdn-module-configurable"></a>Il modulo SDN è configurabile?

SDN è openshift-ovs-networkpolicy e non è configurabile.

## <a name="which-unix-rights-in-iaas-are-available-for-mastersinfraapp-nodes"></a>Quali diritti UNIX (in IaaS) sono disponibili per i nodi Masters/Infra/App?

Non applicabile a questa offerta. Accesso al nodo non consentito.

## <a name="which-ocp-rights-do-we-have-cluster-admin-project-admin"></a>Quali diritti OCP abbiamo? Cluster-admin? Amministratore di progetto?

Per informazioni dettagliate, vedere [Panoramica dell'amministrazione cluster](https://docs.openshift.com/aro/admin_guide/index.html)di Azure Red Hat OpenShift .

## <a name="which-kind-of-federation-with-ldap"></a>Che tipo di federazione con LDAP?

Ciò si ottiene tramite l'integrazione di Azure AD. 

## <a name="is-there-any-element-in-aro-shared-with-other-customers-or-is-everything-independent"></a>C'è qualche elemento in ARO condiviso con altri clienti? O è tutto indipendente?

Ogni cluster Azure Red Hat OpenShift è dedicato a un determinato cliente e si trova all'interno della sottoscrizione del cliente. 

## <a name="can-we-choose-any-persistent-storage-solution-like-ocs"></a>Possiamo scegliere una soluzione di storage persistente, come OCS? 

Sono disponibili due classi di archiviazione: Disco di Azure e File di Azure.Two storage classes are available to select from: Azure Disk and Azure File.

## <a name="how-is-a-cluster-updated-including-majors-and-minors-due-to-vulnerabilities"></a>Come viene aggiornato un cluster (inclusi major e minori a causa di vulnerabilità)?

Vedere [Qual è il processo di aggiornamento generale?](https://docs.microsoft.com/azure/openshift/openshift-faq#what-is-the-general-upgrade-process)

## <a name="what-azure-load-balancer-is-used-by-aro-is-it-standard-or-basic-and-is-it-configurable"></a>Quale servizio di bilanciamento del carico di Azure viene usato da ARO?È Standard o Basic ed è configurabile?

ARO usa Il servizio di bilanciamento del carico di Azure standard e non è configurabile.

## <a name="can-aro-use-netapp-based-storage"></a>ARO è in grado di utilizzare lo storage basato su NetApp?

Al momento le uniche opzioni di archiviazione supportate sono le classi di archiviazione Disco e File di Azure.At the moment only supported storage options are Azure Disk and Azure File storage classes. 


