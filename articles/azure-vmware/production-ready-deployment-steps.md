---
title: Pianificazione della distribuzione della soluzione Azure VMware
description: Questo articolo illustra un flusso di lavoro di distribuzione della soluzione Azure VMware.  Il risultato finale è un ambiente pronto per la creazione e la migrazione di macchine virtuali.
ms.topic: tutorial
ms.date: 10/16/2020
ms.openlocfilehash: 7914176174a38fef2336fc52eae7501780057452
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92147984"
---
# <a name="planning-the-azure-vmware-solution-deployment"></a>Pianificazione della distribuzione della soluzione Azure VMware

In questo articolo viene illustrato il processo di pianificazione per identificare e raccogliere i dati usati durante la distribuzione. Nell'ambito della pianificazione della distribuzione, assicurarsi di documentare le informazioni raccolte per potervi fare riferimento facilmente durante la distribuzione.

I processi di questo avvio rapido consentono di ottenere un ambiente pronto per la produzione per la creazione di macchine virtuali e la migrazione. 

>[!IMPORTANT]
>Prima di creare la risorsa della soluzione Azure VMware, seguire l'articolo [Come abilitare la risorsa della soluzione Azure VMware](enable-azure-vmware-solution.md) per inviare un ticket di supporto per ottenere l'allocazione dei nodi. Dopo la ricezione della richiesta da parte del team di supporto, sono necessari fino a cinque giorni per completare la richiesta e allocare i nodi. Se è disponibile un cloud privato della soluzione Azure VMware ed è necessaria l'allocazione di altri nodi, sarà necessario seguire lo stesso processo. 


## <a name="subscription"></a>Subscription

Identificare la sottoscrizione che si vuole usare per distribuire la soluzione Azure VMware.  È possibile creare una nuova sottoscrizione o riutilizzare una sottoscrizione esistente.

>[!NOTE]
>La sottoscrizione deve essere associata a un Contratto Enterprise Microsoft.

## <a name="resource-group"></a>Resource group

Identificare il gruppo di risorse da usare per la soluzione Azure VMware.  Viene in genere creato un gruppo di risorse specifico per la soluzione Azure VMware, ma è possibile usare un gruppo di risorse esistente.

## <a name="region"></a>Area

Identificare l'area in cui si vuole distribuire la soluzione Azure VMware.  Per altre informazioni, vedere [Prodotti di Azure disponibili in base all'area](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=azure-vmware).

## <a name="resource-name"></a>Nome risorsa

Definire il nome della risorsa da usare durante la distribuzione.  Il nome della risorsa è un nome descrittivo usato per il cloud privato della soluzione Azure VMware.

## <a name="size-nodes"></a>Nodi dimensioni

Identificare i nodi dimensioni da usare durante la distribuzione della soluzione Azure VMware.  Per un elenco completo, vedere la documentazione dei [cloud privati e dei cluster della soluzione Azure VMware](concepts-private-clouds-clusters.md#hosts).

## <a name="number-of-hosts"></a>Numero di host

Definire il numero di host da distribuire nel cloud privato della soluzione Azure VMware.  Il numero minimo di nodi è tre e il numero massimo è 16 nodi per cluster.  Per altre informazioni, vedere la documentazione dei [cloud privati e dei cluster della soluzione Azure VMware](concepts-private-clouds-clusters.md#clusters).

È sempre possibile estendere il cluster in un secondo momento se è necessario incrementare il numero di distribuzione iniziale.

## <a name="vcenter-admin-password"></a>Password dell'amministratore di vCenter
Definire la password dell'amministratore di vCenter.  Durante la distribuzione verrà creata una password per l'amministratore di vCenter. La password è assegnata a cloudadmin@vsphere.local nell'account dell'amministratore durante la compilazione di vCenter. Verrà usata per accedere a vCenter.

## <a name="nsx-t-admin-password"></a>Password dell'amministratore di NSX-T
Definire la password dell'amministratore di NSX-T.  Durante la distribuzione verrà creata una password per l'amministratore di NSX-T. La password è assegnata all'utente amministratore nell'account NSX durante la compilazione di NSX. Verrà usata per accedere a NSX-T Manager.

## <a name="ip-address-segment"></a>Segmento di indirizzo IP

Il primo passaggio della pianificazione della distribuzione consiste nel pianificare la segmentazione IP.  La soluzione Azure VMware inserisce una rete /22 fornita dall'utente, quindi la suddivide in segmenti più piccoli e usa tali segmenti IP per vCenter, VMware HCX, NSX-T e vMotion.

La soluzione Azure VMware si connette alla Rete virtuale di Microsoft Azure tramite un circuito ExpressRoute interno. Nella maggior parte dei casi si connette al data center tramite Copertura globale ExpressRoute. 

La soluzione Azure VMware, l'ambiente di Azure esistente e l'ambiente locale scambiano in genere route. Il blocco di indirizzi di rete CIDR /22 definito in questo passaggio non dovrebbe quindi sovrapporsi a eventuali elementi già presenti in locale o in Azure.

**Esempio:** 10.0.0.0/22

Per altre informazioni, vedere l'[Elenco di controllo di pianificazione della rete](tutorial-network-checklist.md#routing-and-subnet-considerations).

:::image type="content" source="media/pre-deployment/management-vmotion-vsan-network-ip-diagram.png" alt-text="Identificazione - Segmento dell'indirizzo IP" border="false":::  

## <a name="ip-address-segment-for-virtual-machine-workloads"></a>Segmento dell'indirizzo IP per carichi di lavoro di macchine virtuali

Identificare un segmento IP per creare la prima rete (segmento NSX) nel cloud privato.  In altre parole, si vuole creare un segmento di rete nella soluzione Azure VMware per consentire la distribuzione di VM nella soluzione Azure VMware.   

Anche se si prevede di estendere le reti L2, creare un segmento di rete che risulterà utile per convalidare l'ambiente.

Occorre ricordare che eventuali segmenti IP creati devono essere univoci in Azure e nel footprint locale.  

**Esempio:** 10.0.4.0/24

:::image type="content" source="media/pre-deployment/nsx-segment-diagram.png" alt-text="Identificazione - Segmento dell'indirizzo IP" border="false":::     

## <a name="optional-extend-networks"></a>(Facoltativo) Estendere le reti

È possibile estendere segmenti di rete da locale alla soluzione Azure VMware. Se si esegue questa operazione, è necessario identificare adesso queste reti.  

Occorre ricordare che:

- Se si prevede di estendere le reti da locale, tali reti devono connettersi a un [commutatore distribuito di vSphere](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-B15C6A13-797E-4BCB-B9D9-5CBC5A60C3A6.html) nell'ambiente VMware locale.  
- Se la rete o le reti da estendere si trovano in un [commutatore standard di vSphere](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-350344DE-483A-42ED-B0E2-C811EE927D59.html), non sarà possibile estenderle.

## <a name="expressroute-global-reach-peering-network"></a>Rete di peering di Copertura globale ExpressRoute

Identificare un blocco di indirizzi di rete CIDR `/29`, necessario per il peering di Copertura globale ExpressRoute. Occorre ricordare che eventuali segmenti IP creati devono essere univoci nella soluzione Azure VMware e nel footprint locale. Gli IP in questo segmento vengono usati a ogni estremità della connessione di Copertura globale ExpressRoute per connettere il circuito ExpressRoute della soluzione Azure VMware con il circuito ExpressRoute locale. 

**Esempio:** 10.1.0.0/29

:::image type="content" source="media/pre-deployment/expressroute-global-reach-ip-diagram.png" alt-text="Identificazione - Segmento dell'indirizzo IP" border="false":::

## <a name="azure-virtual-network-to-attach-azure-vmware-solution"></a>Rete virtuale di Azure da collegare alla soluzione Azure VMware

Per accedere al cloud privato della soluzione Azure VMware, il circuito ExpressRoute, incluso nella soluzione Azure VMware, deve essere collegato a una Rete virtuale di Azure.  Durante la distribuzione è possibile definire una nuova rete virtuale o sceglierne una esistente.

Il circuito ExpressRoute dalla soluzione Azure VMware si connette a un gateway ExpressRoute nella Rete virtuale di Azure definita in questo passaggio.  

>[!IMPORTANT]
>È possibile usare un gateway ExpressRoute esistente per connettersi alla soluzione Azure VMware, purché non si superi il limite di quattro circuiti ExpressRoute per rete virtuale.  Tuttavia, per accedere alla soluzione Azure VMware dall'ambiente locale tramite ExpressRoute è necessario avere Copertura globale ExpressRoute, in quanto il gateway ExpressRoute non fornisce il routing transitivo tra i circuiti connessi.  

Se si vuole connettere il circuito ExpressRoute dalla soluzione Azure VMware a un gateway ExpressRoute esistente, è possibile eseguire questa operazione dopo la distribuzione.  

Per riepilogare, si vuole connettere la soluzione Azure VMware a un gateway ExpressRoute esistente?  

* **Sì** : identificare la rete virtuale che non viene usata durante la distribuzione.
* **No** : identificare una rete virtuale esistente o crearne una nuova durante la distribuzione.

In entrambi i casi, è necessario documentare le operazioni da eseguire in questo passaggio.

>[!NOTE]
>Questa rete virtuale viene visualizzata dall'ambiente locale e dalla soluzione Azure VMware, quindi è necessario assicurarsi che non siano presenti sovrapposizioni per il segmento IP usato in questa rete virtuale e nelle subnet.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-expressroute-diagram.png" alt-text="Identificazione - Segmento dell'indirizzo IP" border="false":::

## <a name="vmware-hcx-network-segments"></a>Segmenti di rete di VMware HCX

VMware HCX è una tecnologia inclusa nella soluzione Azure VMware. I casi d'uso principali per VMware HCX sono costituiti da migrazioni dei carichi di lavoro e ripristino di emergenza. Se si prevede di eseguire una di queste operazioni, è consigliabile pianificare ora le risorse di rete.   In caso contrario, è possibile ignorare questo passaggio e procedere al passaggio successivo.

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]

## <a name="next-steps"></a>Passaggi successivi
Dopo avere raccolto e documentato le informazioni necessarie, passare alla sezione successiva per creare il cloud privato della soluzione Azure VMware.

> [!div class="nextstepaction"]
> [Distribuire la soluzione Azure VMware](deploy-azure-vmware-solution.md)
