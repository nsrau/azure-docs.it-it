---
title: Cloud privati nella soluzione di VMware da CloudSimple - Azure
description: Informazioni sui concetti e i cloud privati CloudSimple.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: dc07b4eea553e6cb3d9b522826e860ddbfbc1513
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577046"
---
# <a name="cloudsimple-private-cloud-overview"></a>Panoramica del cloud privato CloudSimple

CloudSimple Trasforma ed estende i carichi di lavoro VMware ai cloud pubblici in pochi minuti. Usa il servizio CloudSimple, è possibile distribuire VMware in modalità nativa in Azure infrastruttura bare metal bare. La distribuzione si trova in posizioni di Azure e si integra completamente con il resto del cloud di Azure.

* La soluzione CloudSimple fornisce la continuità operativa VMware completata. Questa soluzione offre i vantaggi di cloud pubblico di:
  * Elasticità
  * Innovazione
  * Efficienza
* Con CloudSimple, trarre vantaggio da un modello di consumo cloud che consente di ridurre il costo totale di proprietà. Offre anche on demand il provisioning, pagamento come-di-aumento delle dimensioni e ottimizzazione della capacità.
* CloudSimple è completamente compatibile con:
  * Strumenti esistenti
  * Competenze
  * Processi
* Questa compatibilità consente ai team di gestire carichi di lavoro nel cloud di Azure, senza interrompere i criteri:
  * Rete
  * Security  
  * Protezione dati  
  * Audit
* CloudSimple gestisce l'infrastruttura e tutte le funzionalità di rete e gestione dei servizi necessari. Il servizio CloudSimple consente al team di concentrarsi su:
  * Valore di Business
  * Il provisioning dell'applicazione
  * Continuità aziendale
  * Supporto
  * Imposizione dei criteri

## <a name="private-cloud-environment-overview"></a>Panoramica sull'ambiente di cloud privato

Un cloud privato è uno stack VMware isolato, ad esempio in questi ambienti:

* Host ESXi
* vCenter
* vSAN
* NSX

I cloud privati gestiti da un server vCenter nel proprio dominio di gestione.

Lo stack di esecuzione in:

* Nodi dedicati
* Nodi di tipo isolato hardware bare metal

Gli utenti utilizzano lo stack di tramite native strumenti VMware, tra cui:

* vCenter
* NSX Manager

È possibile distribuire i nodi dedicati nella località di Azure. È quindi possibile gestirli con Azure e CloudSimple. Un cloud privato è costituito da uno o più cluster vSphere e ogni cluster contiene 3 a 16 nodi.

È possibile creare un cloud privato tramite acquistato i nodi:

* Nodi con pagamento a consumo
* Nodi riservati e prenotati

È possibile connettersi al cloud privato per l'ambiente locale e rete di Azure Usa le connessioni seguenti:

* Proteggere
* VPN privato
* Azure ExpressRoute

L'ambiente cloud privato è progettato per eliminare un singolo punto di errore:

* I cluster ESXi sono configurati con la disponibilità elevata di vSphere e vengono ridimensionati e hanno almeno un nodo di riserva per garantire la resilienza.
* rete vSAN fornisce archiviazione primaria ridondanti. rete vSan richiede almeno tre nodi per garantire la protezione contro un singolo errore. È possibile configurare una rete vSAN per fornire maggiore resilienza per cluster di maggiori dimensioni.
* È possibile configurare vCenter PSC e le macchine virtuali NSX Manager con i criteri di archiviazione RAID 10 per proteggere dagli errori di archiviazione. Quindi, che sono protette da vSphere a disponibilità elevata in caso di errori di nodo e di rete.

## <a name="scenarios-for-deploying-a-private-cloud"></a>Scenari per la distribuzione di un cloud privato

* **Ritiro di centro dati o la migrazione**

  * Ottenere capacità aggiuntiva quando si raggiungono i limiti del tuo Data Center esistente o l'aggiornamento dell'hardware.
  * Aggiungere la capacità necessaria in the cloud ed eliminare le difficoltà legate alla gestione di aggiornamento dei componenti hardware.
  * Ridurre i rischi e costi di migrazioni cloud, rispetto alle conversioni che richiedono molto tempo o rielaborazione.
  * Usa familiari strumenti di VMware e le competenze per accelerare le migrazioni cloud. Nel cloud, utilizzare servizi di Azure per modernizzare le applicazioni in base alle esigenze.

* **Espandere su richiesta**

  * Espandere il cloud per soddisfare le esigenze di impreviste, ad esempio nuovi ambienti di sviluppo o picchi stagionali di capacità.
  * Creare la nuova capacità su richiesta e mantenerla solo fino a quando ti serve.
  * Ridurre l'investimento iniziale, accelerare la velocità di provisioning e ridurre la complessità con la stessa architettura e i criteri sia in locale e nel cloud.

* **Ripristino di emergenza e desktop virtuali nel cloud di Azure**

  * Stabilire l'accesso remoto ai dati, App e i desktop nel cloud di Azure. Con connessioni a banda larga, caricare / scaricare i dati di velocità per il ripristino da eventi imprevisti. Assegnare le reti a bassa latenza veloce risposta volte per cui gli utenti si aspettano da un'app desktop.

  * Replicare tutti i criteri e la rete nel cloud usando il portale di CloudSimple e familiari strumenti VMware. La replica consente di ridurre il lavoro richiesto e il rischio di creare e gestire le implementazioni di ripristino di emergenza e VDI.

* **I database e applicazioni a prestazioni elevate**

  * Eseguire i carichi di lavoro più impegnativi, con l'architettura iperconvergente fornita da CloudSimple.
  * Eseguire Oracle, Microsoft SQL server, i sistemi di middleware e i database ad alte prestazioni. no-SQL.

  * Esperienza cloud come il proprio data center con connessioni di rete ad alta velocità da 25 Gbps. Connessioni ad alta velocità che consentono di eseguire App ibride che risiedono in locale, VMware in Azure, e i carichi di lavoro della privata Azure, senza compromettere le prestazioni.

* **Ibrido davvero**

  * Unificare DevOps in tutti i servizi di Azure e VMware.
  * Ottimizzare l'amministrazione di VMware per le soluzioni che possono essere applicate a tutti i tuoi carichi di lavoro e servizi di Azure.
  * Accedere ai servizi di cloud pubblico senza la necessità di espandere il data center o ridefinizione dell'architettura delle applicazioni.
  * Centralizzare le identità e criteri di controllo di accesso, registrazione e monitoraggio per le applicazioni di VMware in Azure.

## <a name="limits"></a>Limiti

Nella tabella seguente mostra i limiti di nodo per le risorse di un cloud privato.

| Risorsa | Limite |
|----------|-------|
| Numero minimo di nodi per creare un cloud privato | 3 |
| Numero massimo di nodi in un cluster in un cloud privato | 16 |
| Numero massimo di nodi in un cloud privato | 64 |
| Numero minimo di nodi in un nuovo cluster | 3 |

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [creare un cloud privato](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Informazioni su come [configurare un ambiente cloud privato](quickstart-create-private-cloud.md)