---
title: Eseguire la migrazione da un Azure Red Hat OpenShift 3,11 ad Azure Red Hat OpenShift 4
description: Eseguire la migrazione da un Azure Red Hat OpenShift 3,11 ad Azure Red Hat OpenShift 4
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 08/13/2020
keywords: migrazione, Aro, OpenShift, Red Hat
ms.openlocfilehash: 68e8be24273dfab490d2423b76c372f2ff9f2b38
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88513188"
---
# <a name="migrate-from-azure-red-hat-openshift-311-to-azure-red-hat-openshift-4"></a>Eseguire la migrazione da Azure Red Hat OpenShift 3,11 ad Azure Red Hat OpenShift 4

Azure Red Hat OpenShift su OpenShift 4 offre Kubernetes 1,16 sul sistema operativo Red Hat core, i cluster privati, il supporto per la rete virtuale e il ruolo di amministratore del cluster completo. Inoltre, sono ora disponibili molte nuove funzionalità, ad esempio il supporto per operator Framework, l'hub operatore e la mesh del servizio OpenShift.

Per eseguire correttamente la transizione da Azure Red Hat OpenShift 3,11 ad Azure Red Hat OpenShift 4, assicurarsi di esaminare le [differenze in archiviazione, rete, registrazione, sicurezza e monitoraggio](https://docs.openshift.com/container-platform/4.4/migration/migrating_3_4/planning-migration-3-to-4.html).

Questo articolo illustra come eseguire la migrazione da un cluster Azure Red Hat OpenShift 3,11 a un cluster di Azure Red Hat 4.

> [!NOTE]
> Gli strumenti di migrazione di Red Hat OpenShift, ad esempio lo strumento di assistenza per la migrazione del piano di controllo e lo strumento di migrazione dell'applicazione cluster (CAM), non possono essere usati con i cluster Azure Red Hat OpenShift 3,11.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che sia presente un cluster Azure Red Hat OpenShift 3,11 esistente.

## <a name="create-a-target-azure-red-hat-openshift-4-cluster"></a>Creare un cluster Azure Red Hat OpenShift 4 di destinazione

Per prima cosa, [creare il cluster Azure Red Hat OpenShift 4](tutorial-create-cluster.md) che si vuole usare come cluster di destinazione. Qui verrà usata la configurazione di base. Se si è interessati a impostazioni diverse, vedere l' [esercitazione creare un cluster di Azure Red Hat OpenShift 4](tutorial-create-cluster.md).

Creare una rete virtuale con due subnet vuote per i nodi master e worker.

```azurecli-interactive
    az network vnet create \
    --resource-group $RESOURCEGROUP \
    --name aro-vnet \
    --address-prefixes 10.0.0.0/22

    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name master-subnet \
    --address-prefixes 10.0.0.0/23 \
    --service-endpoints Microsoft.ContainerRegistry

    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name worker-subnet \
    --address-prefixes 10.0.2.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
```

Usare quindi il comando seguente per creare il cluster.

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet \
  # --domain foo.example.com # [OPTIONAL] custom domain
  # --pull-secret @pull-secret.txt # [OPTIONAL]
```

## <a name="configure-the-target-openshift-4-cluster"></a>Configurare il cluster OpenShift 4 di destinazione

### <a name="authentication"></a>Authentication

Per consentire agli utenti di interagire con Azure Red Hat OpenShift, è necessario prima eseguire l'autenticazione al cluster. Il livello di autenticazione identifica l'utente associato alle richieste all'API OpenShift di Azure Red Hat. Il livello di autorizzazione Usa quindi le informazioni relative all'utente richiedente per determinare se la richiesta è consentita.

Quando viene creato un cluster Azure Red Hat OpenShift 4, viene creato un utente amministratore temporaneo. [Connettersi al cluster](tutorial-connect-cluster.md), aggiungere utenti e gruppi e [configurare le autorizzazioni appropriate](https://docs.openshift.com/aro/4/authentication/understanding-authentication.html) per entrambe.

### <a name="networking"></a>Funzionalità di rete

Azure Red Hat OpenShift 4 usa alcuni operatori diversi per configurare la rete nel cluster: [operatore di rete cluster](https://docs.openshift.com/aro/4/networking/cluster-network-operator.html#nw-cluster-network-operator_cluster-network-operator), [operatore DNS](https://docs.openshift.com/aro/4/networking/dns-operator.html)e [operatore di ingresso](https://docs.openshift.com/aro/4/networking/ingress-operator.html). Per altre informazioni sulla configurazione della rete in un cluster Azure Red Hat OpenShift 4, vedere il [diagramma di rete](concepts-networking.md) e [informazioni sulla rete](https://docs.openshift.com/aro/4/networking/understanding-networking.html).

### <a name="storage"></a>Archiviazione
Azure Red Hat OpenShift 4 supporta i seguenti plug-in PersistentVolume:

- Archivio del blocco elastico AWS (EBS)
- Disco di Azure
- File di Azure
- Disco persistente GCE
- HostPath
- iSCSI
- Volume locale
- NFS
- Archiviazione contenitore Red Hat OpenShift

Per informazioni sulla configurazione di questi tipi di archiviazione, vedere [configurazione dell'archiviazione permanente](https://access.redhat.com/documentation/azure_red_hat_openshift/4/html/storage/configuring-persistent-storage).

### <a name="registry"></a>Registro

Azure Red Hat OpenShift 4 può creare immagini dal codice sorgente, distribuirle e gestirne il ciclo di vita. Per abilitare questa funzionalità, Azure Red Hat OpenShift fornisce 4 un [registro interno integrato di immagini del contenitore](https://docs.openshift.com/aro/4/registry/registry-options.html) che può essere distribuito nell'ambiente Azure Red Hat OpenShift per gestire le immagini in locale.

Se si usano registri esterni come [Azure container Registry](https://docs.microsoft.com/azure/container-registry/), [registri Red Hat Quay](https://docs.openshift.com/aro/4/registry/registry-options.html#registry-quay-overview_registry-options)o un [Registro Red Hat abilitato](https://docs.openshift.com/aro/4/registry/registry-options.html#registry-authentication-enabled-registry-overview_registry-options)per l'autenticazione, seguire la procedura per fornire le credenziali al cluster per consentire al cluster di accedere ai repository.

### <a name="monitoring"></a>Monitoraggio

Azure Red Hat OpenShift include uno stack di monitoraggio preconfigurato, pre-installato e con aggiornamento automatico basato sul progetto Prometheus open source e sul rispettivo eco-sistema più ampio. Fornisce il monitoraggio dei componenti cluster e include un set di avvisi per notificare immediatamente all'amministratore del cluster eventuali problemi che si verificano e un set di dashboard Grafana. Lo stack di monitoraggio del cluster è supportato solo per il monitoraggio dei cluster OpenShift di Azure Red Hat. Per altre informazioni, vedere [monitoraggio del cluster per Azure Red Hat OpenShift](https://docs.openshift.com/aro/4/monitoring/cluster_monitoring/about-cluster-monitoring.html).

Se si usa [monitoraggio di Azure per i contenitori per Azure Red Hat OpenShift 3,11](../azure-monitor/insights/container-insights-azure-redhat-setup.md), è anche possibile abilitare monitoraggio di Azure per i contenitori per i [cluster di Azure Red Hat OpenShift 4](../azure-monitor/insights/container-insights-azure-redhat4-setup.md) e continuare a usare la stessa area di lavoro log Analytics.

## <a name="move-your-dns-or-load-balancer-configuration-to-the-new-cluster"></a>Spostare il DNS o la configurazione del servizio di bilanciamento del carico nel nuovo cluster

Se si usa Gestione traffico di Azure, aggiungere gli endpoint per fare riferimento al cluster di destinazione e assegnare la priorità a questi endpoint.

## <a name="deploy-application-to-your-target-cluster"></a>Distribuire un'applicazione nel cluster di destinazione

Dopo aver configurato correttamente il cluster di destinazione per il carico di lavoro, [connettersi al cluster](tutorial-connect-cluster.md) e creare le applicazioni, i componenti o i servizi necessari per i progetti. Azure Red Hat OpenShift consente di creare questi file da git, immagini del contenitore, Red Hat Developer Catalog, un Dockerfile, una definizione YAML/JSON o selezionando un servizio di database dal catalogo.

## <a name="delete-your-source-cluster"></a>Eliminare il cluster di origine
Dopo aver verificato che il cluster Azure Red Hat OpenShift 4 è stato configurato correttamente, eliminare il cluster Azure Red Hat OpenShift 3,11.

```
az openshift delete --name $CLUSTER_NAME
                    --resource-group $RESOURCE_GROUP
                    [--no-wait]
                    [--subscription]
                    [--yes]
```
## <a name="next-steps"></a>Passaggi successivi
Vedere la documentazione di Azure Red Hat OpenShift fornita da Red Hat [qui](https://docs.openshift.com/aro/4/welcome/index.html).

