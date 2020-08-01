---
title: Configurare operazioni vRealize per la soluzione VMware di Azure (AVS)
description: ''
ms.topic: how-to
ms.date: 08/06/2020
ms.openlocfilehash: 57b513b681c4d2522b1c92946aab80fe8ba95746
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87476070"
---
# <a name="set-up-vrealize-operations-for-azure-vmware-solution-avs"></a>Configurare operazioni vRealize per la soluzione VMware di Azure (AVS)


vRealize Operations Manager è una piattaforma di gestione delle operazioni che consente agli amministratori dell'infrastruttura VMware di monitorare le risorse di sistema. Queste risorse di sistema possono essere oggetti a livello di applicazione o di infrastruttura (sia fisici che virtuali). In passato, la maggior parte degli amministratori VMware ha usato le operazioni vRealize per monitorare e gestire i componenti del cloud privato VMware, ovvero vCenter, ESXi, NSX-T, rete VSAN e l'estensione del cloud ibrido (HCX). Ogni cloud privato AVS viene sottoposta a provisioning con una distribuzione di vCenter, NSX-T, rete VSAN e HCX dedicata. 

Prima di [iniziare](#before-you-begin) e prima dei [prerequisiti](#prerequisites) , esaminare attentamente. Verranno quindi illustrate le due topologie di distribuzione tipiche per vRealize Operations Manager con AVS:

> [!div class="checklist"]
> * [Operazioni vRealize locali che gestiscono la distribuzione AVS](#on-premises-vrealize-operations-managing-avs-deployment)
> * [Operazioni di vRealize in esecuzione in una distribuzione AVS](#vrealize-operations-running-on-avs-deployment)

## <a name="before-you-begin"></a>Prima di iniziare
* Vedere la [documentazione del prodotto vRealize Operations Manager](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) per altre informazioni sulla distribuzione delle operazioni di vRealize. 
* Vedere la [serie di esercitazioni](tutorial-network-checklist.md)su AVS software defined datacenter (SDDC) di base.
* Facoltativamente, consultare la documentazione del prodotto [controller remoto delle operazioni di vRealize](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-263F9219-E801-4383-8A59-E84F3D01ED6B.html) per l'opzione di distribuzione delle operazioni di vRealize locale per la gestione di AVS. 



## <a name="prerequisites"></a>Prerequisites
* VPN o Azure ExpressRoute devono essere configurati tra l'ambiente locale e AVS SDDC.
* Un cloud privato AVS è stato distribuito in Azure.



## <a name="on-premises-vrealize-operations-managing-avs-deployment"></a>Operazioni vRealize locali che gestiscono la distribuzione AVS
La maggior parte dei clienti dispone di una distribuzione locale esistente di operazioni vRealize usate per gestire uno o più domini vCenter locali. Quando i clienti effettuano il provisioning di un nuovo cloud privato AVS in Azure, in genere collegano l'ambiente locale con AVS usando una ExpressRoute di Azure o una soluzione VPN di livello 3, come illustrato di seguito.   

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-1.png" alt-text="Operazioni vRealize locali che gestiscono la distribuzione AVS"  border="false":::

Per estendere le funzionalità delle operazioni di vRealize al cloud privato AVS, è possibile creare un'istanza di adapter[ per le risorse del cloud privato AVS](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.config.doc/GUID-640AD750-301E-4D36-8293-1BFEB67E2600.html) , per raccogliere dati dal cloud privato AVS e portarli in operazioni di vRealize locali. L'istanza di vRealize Operations Manager locale può connettersi direttamente a vCenter e NSX-T Manager in AVS oppure è possibile distribuire facoltativamente un agente di raccolta remota delle operazioni di vRealize nel cloud privato AVS. Un agente di raccolta remota delle operazioni di vRealize comprime e crittografa i dati raccolti dal cloud privato AVS prima che vengano inviati tramite la rete ExpressRoute o VPN a vRealize Operations Manager in esecuzione in locale. 

> [!TIP]
> Per istruzioni dettagliate sull'installazione di vRealize Operations Manager, vedere la [documentazione di VMware](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) . 



## <a name="vrealize-operations-running-on-avs-deployment"></a>Operazioni di vRealize in esecuzione in una distribuzione AVS

Un'altra opzione di distribuzione consiste nel distribuire un'istanza di vRealize Operations Manager in uno dei cluster vSphere nel cloud privato AVS, come illustrato di seguito. 

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-2.png" alt-text="Operazioni vRealize in esecuzione su AVS" border="false":::

Dopo la distribuzione dell'istanza di AVS delle operazioni vRealize, è possibile configurare le operazioni di vRealize per la raccolta di dati da vCenter, ESXi, NSX-T, rete VSAN e HCX. 

> [!TIP]
> Per istruzioni dettagliate sull'installazione di vRealize Operations Manager, vedere la [documentazione di VMware](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) .




<!-- LINKS - external -->


<!-- LINKS - internal -->




