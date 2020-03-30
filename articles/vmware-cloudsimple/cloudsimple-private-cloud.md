---
title: Soluzione Azure VMware di CloudSimple - Cloud privati
description: Scopri di più sui cloud privati e sui concetti di CloudSimple.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4fb930603455ed1a5df5d357fcab669f41a0c28c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024950"
---
# <a name="cloudsimple-private-cloud-overview"></a>Panoramica del cloud privato di CloudSimple

CloudSimple trasforma ed estende i carichi di lavoro VMware ai cloud pubblici in pochi minuti. Usando il servizio CloudSimple, è possibile distribuire VMware in modo nativo nell'infrastruttura bare metal di Azure.Using the CloudSimple service, you can deploy VMware natively on Azure bare metal infrastructure. La distribuzione si trova in posizioni di Azure e si integra completamente con il resto del cloud di Azure.Your deployment lives on Azure locations and fully integrates with the rest of the Azure cloud.

La soluzione CloudSimple garantisce una continuità operativa Completa VMware. Questa soluzione offre i vantaggi del cloud pubblico di:This solution gives you the public cloud benefits of:

* Elasticità
* Innovazione
* Efficienza

Con CloudSimple, puoi beneficiare di un modello di consumo cloud che riduce il costo totale di proprietà. Offre inoltre provisioning su richiesta, con pagamento in base al tuo aumento e ottimizzazione della capacità.

CloudSimple è completamente compatibile con:

* Strumenti esistenti
* Competenze
* Processi

Questa compatibilità consente ai team di gestire i carichi di lavoro nel cloud di Azure, senza interrompere questi tipi di criteri:This compatibility enables your teams to manage workloads on the Azure cloud, without disrupting these types of policies:

* Rete
* Security  
* Protezione dei dati  
* Audit

CloudSimple gestisce l'infrastruttura e tutti i servizi di networking e gestione necessari. Il servizio CloudSimple consente al team di concentrarsi su:

* Valore aziendale
* Provisioning di applicazioni
* Continuità aziendale
* Supporto
* Imposizione dei criteri

## <a name="private-cloud-environment-overview"></a>Panoramica dell'ambiente Cloud privato

Un cloud privato è uno stack VMware isolato che supporta:

* Host ESXi
* vCenter
* VSAN (informazioni in stato ine
* Nsx

I cloud privati vengono gestiti tramite il portale CloudSimple. Hanno il proprio server vCenter nel proprio dominio di gestione.

Lo stack viene eseguito su:

* Nodi dedicati
* Nodi hardware bare metal isolati

Gli utenti utilizzano lo stack tramite strumenti VMware nativi, tra cui:

* vCenter
* Responsabile NSX

È possibile distribuire nodi dedicati in posizioni di Azure.You can deploy dedicated nodes in Azure locations. È quindi possibile gestirli con Azure e CloudSimple.Then you can manage them with Azure and CloudSimple. Un cloud privato è costituito da uno o più cluster vSphere e ogni cluster contiene da 3 a 16 nodi.

È possibile creare un cloud privato utilizzando nodi acquistati, con pagamento in base al cliente o nodi dedicati riservati.

È possibile connettere il cloud privato all'ambiente locale e alla rete di Azure usando le connessioni seguenti:You can connect the Private Cloud to your on-premises environment and the Azure network using the following connections:

* Sicurezza
* VPN privata
* Azure ExpressRoute

L'ambiente Private Cloud è progettato per eliminare singoli punti di errore:The Private Cloud environment is designed to eliminate single points of failure:

* I cluster ESXi sono configurati con disponibilità elevata di vSphere e sono dimensionati in modo da avere almeno un nodo di riserva per la resilienza.
* vSAN fornisce un'archiviazione primaria ridondante. vSan richiede almeno tre nodi per fornire protezione da un singolo errore. È possibile configurare vSAN per fornire una maggiore resilienza per i cluster di grandi dimensioni.
* È possibile configurare vCenter, PSC e LE macchine virtuali di NSX Manager con criteri di archiviazione RAID-10 per la protezione da errori di archiviazione. vSphere HA protegge da errori di nodo e di rete.

## <a name="scenarios-for-deploying-a-private-cloud"></a>Scenari per la distribuzione di un cloud privatoScenarios for deploying a Private Cloud

Ecco alcuni casi d'uso di esempio per la distribuzione nel cloud privato.

### <a name="data-center-retirement-or-migration"></a>Ritiro o migrazione del data center

* Ottenere capacità aggiuntiva quando si raggiungono i limiti del data center esistente o si aggiorna l'hardware.
* Aggiungi la capacità necessaria nel cloud ed elimina i problemi di gestione degli aggiornamenti hardware.
* Riduci i rischi e i costi delle migrazioni cloud rispetto alle conversioni o alla riarchitettura che richiedono molto tempo.
* Utilizza strumenti e competenze VMware familiari per accelerare le migrazioni cloud. Nel cloud usare i servizi di Azure per modernizzare le applicazioni in base alle proprie esigenze.

### <a name="expand-on-demand"></a>Espandi su richiesta

* Espandere nel cloud per soddisfare esigenze impreviste, ad esempio nuovi ambienti di sviluppo o picchi di capacità stagionali.
* Crea nuova capacità su richiesta e mantienila solo per tutto il tempo necessario.
* Riduci l'investimento iniziale, accelera la velocità del provisioning e complessità con la stessa architettura e i criteri sia in locale che nel cloud.

### <a name="disaster-recovery-and-virtual-desktops-in-the-azure-cloud"></a>Ripristino di emergenza e desktop virtuali nel cloud di AzureDisaster recovery and virtual desktops in the Azure cloud

* Stabilisci l'accesso remoto a dati, app e desktop nel cloud di Azure.Establish remote access to data, apps, and desktops in the Azure cloud. Con le connessioni ad alta larghezza di banda, si caricano / scaricano rapidamente i dati per il recupero da incidenti. Le reti a bassa latenza offrono tempi di risposta rapidi che gli utenti si aspettano da un'app desktop.

* Replica tutte le policy e la rete nel cloud utilizzando il portale CloudSimple e gli strumenti VMware familiari. La replica riduce lo sforzo e il rischio di creare e gestire implementazioni DR e VDI.

### <a name="high-performance-applications-and-databases"></a>Applicazioni e database ad alte prestazioni

* Esegui i tuoi carichi di lavoro più impegnativi con l'architettura iperconvergente fornita da CloudSimple.
* Eseguire Oracle, Microsoft SQL Server, sistemi middleware e database no-SQL ad alte prestazioni.
* Sperimenta il cloud come il tuo data center con connessioni di rete ad alta velocità da 25 Gbps. Le connessioni ad alta velocità consentono di eseguire app ibride che si estendono su carichi di lavoro locali, VMware in Azure e carichi di lavoro privati di Azure, senza compromettere le prestazioni.

### <a name="true-hybrid"></a>Vero ibrido

* Unificare DevOps tra i servizi VMware e Azure.Unify DevOps across VMware and Azure services.
* Ottimizzare l'amministrazione di VMware per i servizi e le soluzioni di Azure che possono essere applicati a tutti i carichi di lavoro.
* Accedi ai servizi cloud pubblici senza dover espandere il tuo data center o riprogettare le tue applicazioni.
* Centralizza le identità, i criteri di controllo degli accessi, la registrazione e il monitoraggio per le applicazioni VMware in Azure.

## <a name="limits"></a>Limiti

Nella tabella seguente sono elencati i limiti dei nodi sulle risorse di un cloud privato.

| Risorsa | Limite |
|----------|-------|
| Numero minimo di nodi per creare un cloud privato | 3 |
| Numero massimo di nodi in un cluster in un cloud privatoMaximum number of nodes in a cluster on a Private Cloud | 16 |
| Numero massimo di nodi in un cloud privatoMaximum number of nodes in a Private Cloud | 64 |
| Numero minimo di nodi in un nuovo cluster | 3 |

## <a name="next-steps"></a>Passaggi successivi

* Scopri come [creare un cloud privato](create-private-cloud.md)
* Informazioni su come [configurare un ambiente cloud privato](quickstart-create-private-cloud.md)
