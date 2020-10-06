---
title: Cluster gestiti di Service Fabric (anteprima)
description: I cluster gestiti di Service Fabric sono un'evoluzione del modello di risorse cluster di Azure Service Fabric che semplifica la gestione della distribuzione e del cluster.
ms.topic: overview
ms.date: 09/28/2020
ms.openlocfilehash: 3d26a92126491662d5c51b3c4e8900ffa547f830
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/28/2020
ms.locfileid: "91410398"
---
# <a name="service-fabric-managed-clusters-preview"></a>Cluster gestiti di Service Fabric (anteprima)

I cluster gestiti di Service Fabric sono un'evoluzione del modello di risorse cluster di Azure Service Fabric che semplifica l'esperienza di gestione della distribuzione e dei cluster.

Il modello ARM (Azure Resource Manager) per i cluster di Service Fabric tradizionali richiede la definizione di una risorsa cluster insieme a una serie di risorse di supporto, che devono tutte essere "collegate" correttamente (durante la distribuzione e per tutto il ciclo di vita del cluster) affinché il cluster e i servizi funzionino correttamente. Al contrario, il modello di incapsulamento per i cluster gestiti di Service Fabric è costituito da una singola risorsa *cluster gestito di Service Fabric*. Tutte le risorse sottostanti relative al cluster sono astratte e gestite da Azure per conto dell'utente.

**Modello di cluster tradizionale di Service Fabric**
![Modello di cluster tradizionale di Service Fabric][sf-composition]

**Modello di cluster gestito di Service Fabric**
![Modello cluster incapsulato di Service Fabric][sf-encapsulation]

In termini di dimensioni e complessità, il modello ARM per un cluster gestito di Service Fabric è costituito da circa 100 righe di codice JSON, rispetto al numero approssimativo di 1000 righe che sono necessarie per definire un tipico cluster di Service Fabric:

| Risorse di Service Fabric | Risorse del cluster gestito di Service Fabric |
|----------|-----------|
| Cluster di Service Fabric | Cluster gestito di Service Fabric |
| Set di scalabilità di macchine virtuali | |
| Bilanciamento del carico | |
| Indirizzo IP pubblico | |
| Account di archiviazione | |
| Rete virtuale | |

I cluster gestiti di Service Fabric offrono numerosi vantaggi rispetto ai cluster tradizionali:

**Distribuzione e gestione del cluster semplificate**
- Distribuzione e gestione di una singola risorsa di Azure
- Gestione e rotazione automatica dei certificati
- Operazioni di ridimensionamento semplificate

**Prevenzione dagli errori operativi**
- Prevenzione da errori di corrispondenza della configurazione con le risorse sottostanti
- Blocco delle operazioni non sicure (ad esempio l'eliminazione di un nodo di inizializzazione)

**Procedure consigliate predefinite**
- Impostazioni consigliate per affidabilità e durabilità

Non sono previsti costi aggiuntivi per i cluster gestiti di Service Fabric oltre al costo delle risorse sottostanti necessarie per il cluster.

## <a name="service-fabric-managed-cluster-skus"></a>SKU dei cluster gestiti di Service Fabric

I cluster gestiti di Service Fabric sono disponibili negli SKU Basic e Standard.

| Feature | Basic | Standard |
| ------- | ----- | -------- |
| Risorsa di rete (SKU per [Load Balancer](../load-balancer/skus.md), [IP pubblico](../virtual-network/public-ip-addresses.md)) | Basic | Standard |
| Numero minimo di nodi (istanza VM) | 3 | 5 |
| Numero massimo di nodi per tipo | 100 | 100 |
| Numero massimo di tipi di nodo | 1 | 20 |
| Aggiunta/Rimozione di tipi di nodo | No | Sì |
| Ridondanza della zona | No | Sì |

## <a name="service-fabric-managed-cluster-feature-roadmap"></a>Roadmap delle funzionalità dei cluster gestiti di Service Fabric
Poiché si tratta di una versione di anteprima anticipata dei cluster gestiti di Service Fabric, è necessario tenere presente che alcune funzionalità non sono ancora implementate. Le funzionalità seguenti diventeranno disponibili nelle versioni future. 

* Pubblicazione di applicazioni in cluster direttamente da Visual Studio
* Identità gestite 
* Distribuzioni di applicazioni ARM 
* Zone di disponibilità 
* Proxy inverso 
* Ridimensionamento automatico 
* Aggiornamento delle regole NSG 
* Aggiornamenti automatici del sistema operativo

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare i cluster gestiti di Service Fabric, provare l'avvio rapido:

> [!div class="nextstepaction"]
> [Creare un cluster gestito di Service Fabric (anteprima)](quickstart-managed-cluster-template.md)


[sf-composition]: ./media/overview-managed-cluster/sfrp-composition-resource.png
[sf-encapsulation]: ./media/overview-managed-cluster/sfrp-encapsulated-resource.png