---
title: Cloud privati nella soluzione VMware di CloudSimple-Azure
description: Informazioni sui cloud privati e i concetti relativi a CloudSimple.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9885366d5987870fe2739083ff47abaae9ef6ed1
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816200"
---
# <a name="cloudsimple-private-cloud-overview"></a>Panoramica del cloud privato CloudSimple

CloudSimple trasforma ed estende i carichi di lavoro VMware in cloud pubblici in pochi minuti. Con il servizio CloudSimple è possibile distribuire VMware in modo nativo nell'infrastruttura bare metal di Azure. La distribuzione si trova in località di Azure e si integra completamente con il resto del cloud di Azure.

* La soluzione CloudSimple fornisce la continuità operativa VMware completa. Questa soluzione offre i vantaggi del cloud pubblico di:
  * Elasticità
  * Innovazione
  * Efficienza
* Con CloudSimple è possibile trarre vantaggio da un modello di consumo cloud che riduce il costo totale di proprietà. Offre anche il provisioning su richiesta, il pagamento in base al consumo e l'ottimizzazione della capacità.
* CloudSimple è completamente compatibile con:
  * Strumenti esistenti
  * Competenze
  * Processi
* Questa compatibilità consente ai team di gestire i carichi di lavoro nel cloud di Azure, senza compromettere i criteri:
  * Rete
  * Security  
  * Protezione dati  
  * Audit
* CloudSimple gestisce l'infrastruttura e tutti i servizi di rete e gestione necessari. Il servizio CloudSimple consente al team di concentrarsi su:
  * Valore di business
  * Provisioning delle applicazioni
  * Continuità aziendale
  * Supporto
  * Applicazione dei criteri

## <a name="private-cloud-environment-overview"></a>Panoramica dell'ambiente del cloud privato

Un cloud privato è uno stack VMware isolato, ad esempio questi ambienti:

* Host ESXi
* vCenter
* vSAN
* NSX

I cloud privati vengono gestiti da un server vCenter nel proprio dominio di gestione.

Lo stack viene eseguito in:

* Nodi dedicati
* Nodi hardware bare metal isolati

Gli utenti utilizzano lo stack tramite strumenti VMware nativi, tra cui:

* vCenter
* Gestione NSX

È possibile distribuire nodi dedicati in località di Azure. È quindi possibile gestirli con Azure e CloudSimple. Un cloud privato è costituito da uno o più cluster vSphere e ogni cluster contiene da 3 a 16 nodi.

È possibile creare un cloud privato usando i nodi di cui è stato effettuato il provisioning:

* Nodi con pagamento in base al consumo
* Nodi dedicati riservati

È possibile connettere il cloud privato all'ambiente locale e alla rete di Azure usando le connessioni seguenti:

* Proteggere
* VPN privata
* Azure ExpressRoute

L'ambiente cloud privato è progettato per eliminare un singolo punto di errore:

* I cluster ESXi sono configurati con la disponibilità elevata di vSphere e sono dimensionati per avere almeno un nodo di riserva per la resilienza.
* Rete VSAN fornisce archiviazione primaria ridondante. Rete VSAN richiede almeno tre nodi per garantire la protezione da un singolo errore. È possibile configurare rete VSAN per fornire una resilienza maggiore per i cluster più grandi.
* È possibile configurare le VM vCenter, PSC e NSX Manager con i criteri di archiviazione RAID-10 per proteggersi da errori di archiviazione. Quindi, sono protetti da vSphere HA in caso di errori di nodo e di rete.

## <a name="scenarios-for-deploying-a-private-cloud"></a>Scenari per la distribuzione di un cloud privato

* **Ritiro o migrazione del Data Center**

  * Ottenere capacità aggiuntiva quando si raggiungono i limiti del data center esistente o si aggiorna l'hardware.
  * Aggiungere la capacità necessaria nel cloud ed eliminare i problemi di gestione degli aggiornamenti dell'hardware.
  * Riduci il rischio e i costi delle migrazioni cloud, rispetto alle conversioni o alla riarchitettura dispendiose in termini di tempo.
  * Usa strumenti e competenze VMware comuni per accelerare le migrazioni cloud. Nel cloud usare i servizi di Azure per modernizzare le applicazioni in modo più veloce.

* **Espandi su richiesta**

  * Espandersi nel cloud per soddisfare le esigenze impreviste, ad esempio nuovi ambienti di sviluppo o picchi di capacità stagionali.
  * È possibile creare una nuova capacità su richiesta e mantenerla solo se necessario.
  * Riduci gli investimenti iniziali, accelera la velocità di provisioning e Riduci la complessità con la stessa architettura e criteri sia in locale che nel cloud.

* **Ripristino di emergenza e desktop virtuali nel cloud di Azure**

  * Consente di stabilire l'accesso remoto ai dati, alle app e ai desktop nel cloud di Azure. Con le connessioni a larghezza di banda elevata, i dati vengono caricati o scaricati velocemente per il ripristino dagli eventi imprevisti. Le reti a bassa latenza offrono tempi di risposta rapidi che gli utenti si aspettano da un'app desktop.

  * Replicare tutti i criteri e le funzionalità di rete nel cloud usando il portale di CloudSimple e gli strumenti di VMware noti. Questa replica riduce l'impegno e il rischio di creare e gestire le implementazioni di ripristino di emergenza e di ripristino di emergenza.

* **Applicazioni e database ad alte prestazioni**

  * Esegui i tuoi carichi di lavoro più impegnativi con l'architettura iperconvergente fornita da CloudSimple.
  * Eseguire Oracle, Microsoft SQL Server, i sistemi middleware e i database no-SQL ad alte prestazioni.

  * Scopri il cloud come data center con connessioni di rete a 25 Gbps ad alta velocità. Le connessioni ad alta velocità consentono di eseguire app ibride che si estendono in locale, VMware in Azure e carichi di lavoro privati di Azure, senza compromettere le prestazioni.

* **Vero ibrido**

  * Unificare DevOps tra i servizi VMware e Azure.
  * Ottimizza l'amministrazione VMware per i servizi e le soluzioni di Azure che possono essere applicati a tutti i tuoi carichi di lavoro.
  * Accedi ai servizi cloud pubblici senza dover espandere il data center o riprogettare le tue applicazioni.
  * Centralizzare le identità, i criteri di controllo degli accessi, la registrazione e il monitoraggio per le applicazioni VMware in Azure.

## <a name="limits"></a>Limiti

La tabella seguente illustra i limiti dei nodi sulle risorse di un cloud privato.

| Risorsa | Limite |
|----------|-------|
| Numero minimo di nodi per la creazione di un cloud privato | 3 |
| Numero massimo di nodi in un cluster in un cloud privato | 16 |
| Numero massimo di nodi in un cloud privato | 64 |
| Numero minimo di nodi in un nuovo cluster | 3 |

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [creare un cloud privato](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Informazioni su come [configurare un ambiente cloud privato](quickstart-create-private-cloud.md)