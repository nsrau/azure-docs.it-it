---
title: Creare Azure soluzione VMware da CloudSimple Private Cloud
description: Viene descritto come creare un Cloud privato CloudSimple per estendere carichi di lavoro VMware nel cloud con continuità aziendale e la flessibilità operativa
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e5c03c1d8a865b792ce79e3e2b576a629b71e02c
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67333118"
---
# <a name="create-a-cloudsimple-private-cloud"></a>Creare un Cloud privato CloudSimple

Creazione di un Cloud privato consente di rispondere a una varietà di esigenze comuni per l'infrastruttura di rete:

* **Crescita**. Se è stato raggiunto un punto di aggiornamento hardware per l'infrastruttura esistente, un Cloud privato consente di espandere senza investimenti hardware nuovo necessari.

* **Rapida espansione**. Se qualsiasi capacità temporanei o non pianificato deve verificarsi, un Cloud privato consente di creare la capacità aggiuntiva con alcun ritardo.

* **Una maggiore protezione**. Con un Cloud privato di tre o più nodi, puoi ottenere ridondanza automatica e protezione a disponibilità elevata.

* **Infrastruttura a lungo termine deve**. Se il Data Center sono in base alla capacità o che si desidera ristrutturare per ridurre i costi, un Cloud privato consente di ritirare i Data Center ed eseguire la migrazione a una soluzione cloud pur rimanendo compatibile con le operazioni dell'organizzazione.

Quando si crea un Cloud privato, si ottiene un cluster vSphere singolo e tutte le macchine virtuali che vengono creati in tale cluster di gestione.

## <a name="before-you-begin"></a>Prima di iniziare

Nodi devono essere eseguiti prima di poter creare il Cloud privato.  Per altre informazioni sul provisioning di nodi, vedere [effettuare il provisioning di nodi per la soluzione VMware da CloudSimple - Azure](create-nodes.md) articolo.

## <a name="sign-in-to-azure"></a>Accedi ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Accedere al portale di CloudSimple

Accesso di [CloudSimple portale](access-cloudsimple-portal.md).

## <a name="create-a-new-private-cloud"></a>Creare un nuovo Cloud privato

1. Nel **le risorse** pagina, fare clic su **nuovi Cloud privato**.

    ![Creare un Cloud privato - come iniziare](media/create-pc-button.png)

2. Selezionare il percorso per ospitare le risorse del Cloud privato.

3. Scegli tipo nodo CS28 o CS36 you'ev sottoposta a provisioning per il Cloud privato. L'opzione di quest'ultima include la capacità di calcolo e memoria massima.

4. Selezionare il numero di nodi per il Cloud privato. È possibile selezionare al massimo il numero di nodi disponibili tale you'ev [provisioning](create-nodes.md).

    ![Creare un Cloud privato - impostazioni di base](media/create-private-cloud-basic-info.png)

5. Fare clic su **Avanti: Opzioni avanzate**.

6. Immettere l'intervallo CIDR per la subnet di vSphere/rete vSAN. Assicurarsi che l'intervallo CIDR non si sovrapponga con uno qualsiasi di on-premises o ad altre subnet di Azure (reti virtuali) o con la subnet del gateway.  Non usare qualsiasi intervallo CIDR definito in reti virtuali di Azure.
    
    **Opzioni di intervallo CIDR:** da/24, /23, /22 o /21. Oggetto/24 intervallo CIDR supporta fino a nove nodi, / 23 intervallo CIDR supporta un massimo di 41 nodi e un /22 e /21 intervallo CIDR supporta un massimo di 64 nodi (vale a dire il numero massimo di nodi in un Cloud privato).

    > [!CAUTION]
    > Indirizzi IP nell'intervallo CIDR vSphere/vSAN è riservato per l'utilizzo dall'infrastruttura di Cloud privato.  Non utilizzare l'indirizzo IP in questo intervallo su qualsiasi macchina virtuale.

7. Fare clic su **Avanti: Esaminare e creare**.

8. Rivedere le impostazioni. Se è necessario modificare le impostazioni, fare clic su **Previous**.

9. Fare clic su **Create**(Crea).

Private Cloud il provisioning verrà avviato quando fa clic su Crea.  È possibile monitorare lo stato di avanzamento dal [attività](https://docs.azure.cloudsimple.com/activity/#tasks) pagina CloudSimple portale.  Il provisioning può richiedere 30 minuti a due ore.  Si riceverà un messaggio di posta elettronica al termine del provisioning.

## <a name="next-steps"></a>Passaggi successivi

* [Espandere il cloud privato](expand-private-cloud.md)