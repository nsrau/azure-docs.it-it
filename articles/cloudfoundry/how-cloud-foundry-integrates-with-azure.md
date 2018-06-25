---
title: Modalità di integrazione di Cloud Foundry con Azure | Microsoft Docs
description: Viene descritto in che modo Cloud Foundry può utilizzare i servizi di Azure per migliorare l'esperienza aziendale
services: virtual-machines-linux
documentationcenter: ''
author: ningk
manager: jeconnoc
editor: ''
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: ningk
ms.openlocfilehash: 1f4afbe1849210c55c392d014449224f2fe97b04
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34659395"
---
# <a name="integrate-cloud-foundry-with-azure"></a>Integrare Cloud Foundry con Azure

[Cloud Foundry](https://docs.cloudfoundry.org/) è una piattaforma PaaS in esecuzione sulla piattaforma IaaS dei provider di servizi cloud. Offre un'esperienza coerente di distribuzione delle applicazioni tra i diversi provider di servizi cloud. Si integra inoltre con diversi servizi di Azure, per offrire disponibilità elevata di livello aziendale, scalabilità e risparmio sui costi.
Ci sono [6 sottosistemi di Cloud Foundry](https://docs.cloudfoundry.org/concepts/architecture/) che consentono scalabilità flessibile online, relativi a routing, autenticazione, gestione del ciclo di vita delle applicazioni, gestione dei servizi, messaggistica e monitoraggio. Per ognuno dei sottosistemi, è possibile configurare Cloud Foundry per utilizzare il servizio di Azure corrispondente. 

![Architettura dell'integrazione di Cloud Foundry in Azure](media/CFOnAzureEcosystem-colored.png)

## <a name="1-high-availability-and-scalability"></a>1. Disponibilità e scalabilità elevate
### <a name="managed-disk"></a>Managed Disks
Bosh utilizza l'interfaccia del provider di servizi cloud Azure per la creazione di dischi e l'eliminazione di routine. Per impostazione predefinita, vengono usati dischi non gestiti. Il cliente deve creare manualmente gli account di archiviazione e quindi configurare gli account nei file manifesto CF. Ciò è dovuto al limite previsto per il numero di dischi per ogni account di archiviazione.
Ora è disponibile il servizio [Managed Disks](https://azure.microsoft.com/services/managed-disks/), che offre archiviazione su disco gestita, sicura e affidabile per le macchine virtuali. I clienti non devono più gestire l'account di archiviazione per la scalabilità e la disponibilità elevata. Azure organizza automaticamente i dischi. Che si tratti di una distribuzione nuova o esistente, l'interfaccia del provider di servizi cloud Azure gestisce la creazione o la migrazione del disco gestito durante una distribuzione di CF. Questo comportamento è supportato con PCF 1.11. Per informazioni, è anche possibile vedere le [indicazioni sull'uso di Managed Disks](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/managed-disks) con Cloud Foundry open source. 
### <a name="availability-zone-"></a>Zona di disponibilità*
In quanto piattaforma applicativa nativa del cloud, la progettazione di Cloud Foundry prevede [quattro livelli di disponibilità elevata](https://docs.pivotal.io/pivotalcf/2-1/concepts/high-availability.html). Mentre i primi tre livelli di errori software possono essere gestiti dal sistema CF stesso, la tolleranza di errore della piattaforma viene fornita dai provider di servizi cloud. I componenti principali di CF devono essere protetti con una soluzione a disponibilità elevata della piattaforma del provider di servizi cloud. Sono inclusi Gorouter, Diego Brain e pacchetti di servizi e database CF. Per impostazione predefinita, viene usato il [set di disponibilità di Azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/deploy-cloudfoundry-with-availability-sets) per la tolleranza di errore tra i cluster in un data center.
È ora disponibile la [zona di disponibilità di Azure](https://docs.microsoft.com/azure/availability-zones/az-overview ), che offre un nuovo livello di tolleranza di errore, con ridondanza a bassa latenza tra data center.
La zona di disponibilità di Azure consente di ottenere la disponibilità elevata inserendo un set di macchine virtuali in due o più data center, con ogni set di macchine virtuali ridondante per gli altri set. Se una zona non è disponibile, gli altri set sono ancora attivi e isolati dal problema.
> [!NOTE] 
> La zona di disponibilità di Azure non viene offerta ancora in tutte le aree. Vedere il più recente [annuncio per l'elenco delle aree supportate](https://docs.microsoft.com/azure/availability-zones/az-overview). Per Cloud Foundry open source, vedere [Azure Availability Zone for open source Cloud Foundry guidance](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/availability-zone) (Informazioni sulla zona di disponibilità di Azure per Cloud Foundry open source).

## <a name="2-network-routing"></a>2. Routing di rete
Per impostazione predefinita, viene usato il servizio Azure Load Balancer Basic per le richieste di API/app CF in ingresso, che vengono inoltrate a Gorouter. I componenti di CF come Diego Brain, MySQL, ERT, possono anche usare il servizio Load Balancer per bilanciare il traffico per la disponibilità elevata. Azure offre inoltre una gamma di soluzioni di bilanciamento del carico completamente gestite. Se si desidera la terminazione TLS ("offload SSL") o l'elaborazione a livello di applicazione per ogni richiesta HTTP/HTTPS, prendere in considerazione il gateway applicazione. Per il bilanciamento del carico di livello 4 per la disponibilità elevata e la scalabilità, prendere in considerazione il servizio Load Balancer Standard.
### <a name="azure-application-gateway-"></a>Gateway applicazione di Azure*
Il [gateway applicazione di Azure](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) offre diverse funzionalità di bilanciamento del carico di livello 7, tra cui offload SSL, SSL end-to-end, web application firewall, affinità di sessione basata su cookie e molto altro. È possibile [configurare il gateway applicazione in Cloud Foundry open source](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/application-gateway). Per PCF, vedere le [note sulla versione di PCF 2.1](https://docs.pivotal.io/pivotalcf/2-1/pcf-release-notes/opsmanager-rn.html#azure-application-gateway) per il test POC.

### <a name="azure-standard-load-balancer-"></a>Azure Load Balancer Standard*
Azure Load Balancer è un servizio di bilanciamento del carico di livello 4. Viene usato per distribuire il traffico tra istanze dei servizi in un set con carico bilanciato. La versione Standard fornisce [funzionalità avanzate](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) in aggiunta a quanto disponibile nella versione Basic. Ad esempio: 1. Il limite massimo per il pool back-end passa da 100 a 1000 macchine virtuali.  2. Gli endpoint supportano ora più set di disponibilità invece di un unico set di disponibilità.  3. Sono disponibili funzionalità aggiuntive come porte a disponibilità elevata, dati di monitoraggio più completi e così via. Se si sta passando alla zona di disponibilità di Azure, è richiesto il servizio Load Balancer Standard. Per una nuova distribuzione, è consigliabile iniziare con il servizio Load Balancer Standard. 

## <a name="3-authentication"></a>3. Authentication 
Il server [Cloud Foundry UAA (User Account and Authentication)](https://docs.cloudfoundry.org/concepts/architecture/uaa.html) rappresenta il servizio di gestione delle identità centrale per CF e i suoi diversi componenti. [Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) è il servizio Microsoft di gestione di identità e directory multi-tenant basato sul cloud. Per impostazione predefinita, per l'autenticazione di Cloud Foundry viene usato UAA. Come opzione avanzata, UAA supporta anche Azure AD come archivio utenti esterno. Gli utenti di Azure AD possono accedere a Cloud Foundry usando la propria identità LDAP, senza che sia necessario un account Cloud Foundry. Seguire questa procedura per [configurare Azure AD per UAA in PCF](http://docs.pivotal.io/p-identity/1-6/azure/index.html).

## <a name="4-data-storage-for-cloud-foundry-runtime-system"></a>4. Archiviazione dei dati per il sistema di runtime di Cloud Foundry
Cloud Foundry offre un'eccezionale estendibilità per l'uso dell'archivio BLOB di Azure o dei servizi Azure MySQL/PostgreSQL per l'archiviazione del sistema di runtime dell'applicazione.
### <a name="azure-blobstore-for-cloud-foundry-cloud-controller-blobstore"></a>Archivio BLOB di Azure per l'archivio BLOB Cloud Controller di Cloud Foundry
L'archivio BLOB Cloud Controller è un archivio dati molto importante per buildpack, droplet, pacchetti e pool di risorse. Per impostazione predefinita, viene usato il server NFS per l'archivio BLOB Cloud Controller. Per evitare un singolo punto di guasto, usare Archiviazione BLOB di Azure come archivio esterno. Vedere la [documentazione di Cloud Foundry](https://docs.cloudfoundry.org/deploying/common/cc-blobstore-config.html) per informazioni sul background e le [opzioni in Pivotal Cloud Foundry](https://docs.pivotal.io/pivotalcf/2-0/customizing/azure.html).

### <a name="mysqlpostgresql-as-cloud-foundry-elastic-run-time-database-"></a>MySQL/PostgreSQL come database Cloud Foundry Elastic Runtime*
CF Elastic Runtime richiede due database di sistema principali:
#### <a name="ccdb"></a>CCDB 
Il database Cloud Controller.  Cloud Controller fornisce endpoint API REST per consentire ai client di accedere al sistema. Il database CCDB archivia le tabelle per organizzazioni, spazi, servizi, ruoli utente e molto altro per Cloud Controller.
#### <a name="uaadb"></a>UAADB 
Il database per l'autenticazione e gli account utente. Archivia i dati correlati all'autenticazione degli utenti, ad esempio password e nomi utente crittografati.

Per impostazione predefinita, è possibile usare un database di sistema locale (MySQL). Per la disponibilità elevata e la scalabilità, usare i servizi Azure PostgreSQL o MySQL gestiti. Leggere le istruzioni per [abilitare Azure MySQL/PostgreSQL per CCDB, UAADB e altri database di sistema con Cloud Foundry open source](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/configure-cf-external-databases-using-azure-mysql-postgres-service).

## <a name="5-open-service-broker"></a>5. Open Service Broker
La tecnologia Service Broker di Azure offre un'interfaccia coerente per la gestione dell'accesso delle applicazioni ai servizi di Azure. Il nuovo [progetto Open Service Broker per Azure](https://github.com/Azure/open-service-broker-azure) offre un modo semplice e unico per fornire i servizi alle applicazioni in Cloud Foundry, OpenShift e Kubernetes. Vedere il [pacchetto Azure Open Service Broker for PCF](https://network.pivotal.io/products/azure-open-service-broker-pcf/) per istruzioni per la distribuzione in PCF.

## <a name="6-metrics-and-logging"></a>6. Metriche e registrazione
Azure Log Analytics Nozzle è un componente di Cloud Foundry che inoltra le metriche dal [Firehose Cloud Foundry Loggregator](https://docs.cloudfoundry.org/loggregator/architecture.html) ad [Azure Log Analytics](https://azure.microsoft.com/services/log-analytics/). Questo strumento consente di raccogliere, visualizzare e analizzare le metriche relative a prestazioni e integrità del sistema CF tra più distribuzioni.
Fare clic [qui](https://docs.microsoft.com/azure/cloudfoundry/cloudfoundry-oms-nozzle) per informazioni su come distribuire Azure Log Analytics Nozzle negli ambienti Cloud Foundry open source e Pivotal Cloud Foundry e quindi accedere ai dati dalla console OMS di Azure Log Analytics. 
> [!NOTE]
> A partire da PCF 2.0, le metriche sull'integrità BOSH per le macchine virtuali vengono inoltrate al Firehose Loggregator per impostazione predefinita e sono integrate nella console OMS di Azure Log Analytics.

## <a name="7-cost-saving"></a>7. Risparmio sui costi
### <a name="cost-saving-for-devtest-environments"></a>Risparmio sui costi per gli ambienti di sviluppo/test
#### <a name="b-series-"></a>Serie B*:
Mentre le macchine virtuali serie F e D erano comunemente consigliate per l'ambiente di produzione Pivotal Cloud Foundry, la nuova [serie B](https://azure.microsoft.com/blog/introducing-b-series-our-new-burstable-vm-size/) con possibilità di burst offre nuove opzioni. Le VM della serie B che prevedono burst sono ideali per carichi di lavoro che non necessitano in modo continuativo delle prestazioni complete della CPU, ad esempio server Web, database di piccole dimensioni e ambienti di sviluppo e test. Questi carichi di lavoro hanno in genere requisiti relativi alle prestazioni che prevedono burst. Il costo è di € 0,011/ora (B1) rispetto a € 0,043/ora (F1). Vedere l'elenco completo di [dimensioni di macchine virtuali](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/sizes-general) e [prezzi](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) per informazioni dettagliate. 
#### <a name="managed-standard-disk"></a>Managed Disks Standard: 
Per prestazioni affidabili nell'ambiente di produzione erano consigliati i dischi Premium.  Con [Managed Disks](https://azure.microsoft.com/services/managed-disks/), l'archiviazione Standard può offrire un livello di affidabilità simile, con prestazioni diverse. Per i carichi di lavoro per cui le prestazioni non sono importanti, ad esempio per un ambiente non critico o di sviluppo/test, il servizio Managed Disks Standard offre un'opzione alternativa a un costo inferiore.  
### <a name="cost-saving-in-general"></a>Risparmio sui costi in generale 
#### <a name="significant-vm-cost-saving-with-reserved-instances"></a>Risparmio significativo sui costi delle macchine virtuali con le istanze riservate: 
Oggi tutte le macchine virtuali CF vengono fatturate in base ai prezzi "on demand", anche se gli ambienti in genere rimangono attivi all'infinito. Ora è possibile riservare la capacità delle macchine virtuali per un periodo di 1 o 3 anni, ottenendo sconti dal 45 al 65%. Gli sconti vengono applicati nel sistema di fatturazione, senza alcuna modifica all'ambiente. Per informazioni dettagliate, vedere l'articolo su [come funzionano le istanze riservate](https://azure.microsoft.com/pricing/reserved-vm-instances/). 
#### <a name="managed-premium-disk-with-smaller-sizes"></a>Managed Disks Premium con dimensioni più piccole: 
Il servizio Managed Disks supporta dimensioni dei dischi più piccole, ad esempio P4 (32 GB) e P6 (64 GB) sia per i dischi Premium che per quelli Standard. Se si hanno carichi di lavoro di piccole dimensioni, è possibile risparmiare sui costi eseguendo la migrazione dai dischi Premium standard ai dischi Premium gestiti.
#### <a name="utilizing-azure-first-party-services"></a>Utilizzo dei servizi di Azure proprietari: 
Sfruttando i servizi di Azure proprietari, è possibile ridurre i costi di amministrazione a lungo termine, oltre che disporre di disponibilità elevata e affidabilità, come illustrato nelle sezioni precedenti. 

Pivotal ha lanciato [Small Footprint ERT](https://docs.pivotal.io/pivotalcf/2-0/customizing/small-footprint.html) per i clienti PCF, i cui componenti si trovano in sole 4 macchine virtuali, che eseguono fino a 2500 istanze delle applicazioni. La versione di valutazione è ora disponibile tramite [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).

## <a name="next-steps"></a>Passaggi successivi
Le funzionalità di integrazione di Azure sono disponibili con [Cloud Foundry open source](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/) prima di essere disponibili in Pivotal Cloud Foundry. Le funzionalità contrassegnate con * non son ancora disponibili tramite PCF. L'integrazione di Cloud Foundry con Azure Stack non viene trattata in questo documento.
Per il supporto di PCF per le funzionalità contrassegnate con * o per l'integrazione di Cloud Foundry con Azure Stack, contattare l'account manager Pivotal e Microsoft per informazioni aggiornate. 

