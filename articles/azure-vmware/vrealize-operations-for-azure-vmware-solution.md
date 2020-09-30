---
title: Configurare operazioni vRealize per la soluzione VMware di Azure
description: Informazioni su come configurare le operazioni vRealize per il cloud privato della soluzione VMware di Azure.
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 9e512d107ddc4d9bca28323658d09f4b4b378dc3
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91580402"
---
# <a name="set-up-vrealize-operations-for-azure-vmware-solution"></a>Configurare operazioni vRealize per la soluzione VMware di Azure


vRealize Operations Manager è una piattaforma di gestione delle operazioni che consente agli amministratori dell'infrastruttura VMware di monitorare le risorse di sistema. Queste risorse di sistema possono essere oggetti a livello di applicazione o di infrastruttura (sia fisici che virtuali). In passato, la maggior parte degli amministratori VMware ha usato le operazioni vRealize per monitorare e gestire i componenti del cloud privato VMware, ovvero vCenter, ESXi, NSX-T, rete VSAN e l'estensione del cloud ibrido (HCX). Ogni cloud privato della soluzione VMware di Azure viene sottoposta a provisioning con una distribuzione di vCenter, NSX-T, rete VSAN e HCX dedicata. 

Prima di [iniziare](#before-you-begin) e prima dei [prerequisiti](#prerequisites) , esaminare attentamente. Verranno quindi illustrate le due topologie di distribuzione tipiche per vRealize Operations Manager con la soluzione VMware di Azure:

> [!div class="checklist"]
> * [Operazioni vRealize locali che gestiscono la distribuzione della soluzione VMware di Azure](#on-premises-vrealize-operations-managing-azure-vmware-solution-deployment)
> * [Operazioni vRealize in esecuzione nella distribuzione della soluzione VMware di Azure](#vrealize-operations-running-on-azure-vmware-solution-deployment)

## <a name="before-you-begin"></a>Prima di iniziare
* Vedere la [documentazione del prodotto vRealize Operations Manager](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) per altre informazioni sulla distribuzione delle operazioni di vRealize. 
* Esaminare la [serie di esercitazioni](tutorial-network-checklist.md)di base del software definito datacenter (SDDC) di Azure VMware Solution.
* Facoltativamente, consultare la documentazione del prodotto [controller remoto operazioni vRealize](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-263F9219-E801-4383-8A59-E84F3D01ED6B.html) per l'opzione di distribuzione delle soluzioni vRealize in locale gestione di Azure VMware. 



## <a name="prerequisites"></a>Prerequisiti
* VPN o Azure ExpressRoute devono essere configurati tra la soluzione SDDC locale e la soluzione VMware di Azure.
* Un cloud privato della soluzione VMware di Azure è stato distribuito in Azure.



## <a name="on-premises-vrealize-operations-managing-azure-vmware-solution-deployment"></a>Operazioni vRealize locali che gestiscono la distribuzione della soluzione VMware di Azure
La maggior parte dei clienti dispone di una distribuzione locale esistente di operazioni vRealize usate per gestire uno o più domini vCenter locali. Quando i clienti effettuano il provisioning di un nuovo cloud privato della soluzione VMware di Azure in Azure, in genere collegano l'ambiente locale con la soluzione VMware di Azure usando una ExpressRoute di Azure o una soluzione VPN di livello 3, come illustrato di seguito.   

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-1.png" alt-text="Operazioni vRealize locali che gestiscono la distribuzione della soluzione VMware di Azure"  border="false":::

Per estendere le funzionalità delle operazioni di vRealize al cloud privato della soluzione VMware di Azure, è possibile creare un'istanza di adapter [per le risorse del cloud privato della soluzione VMware di Azure](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.config.doc/GUID-640AD750-301E-4D36-8293-1BFEB67E2600.html) : per raccogliere dati dal cloud privato della soluzione VMware di Azure e inserirli in operazioni vRealize locali. L'istanza di vRealize Operations Manager locale può connettersi direttamente a vCenter e NSX-T Manager nella soluzione VMware di Azure oppure è possibile distribuire facoltativamente un agente di raccolta remota delle operazioni di vRealize nel cloud privato della soluzione VMware di Azure. Un agente di raccolta remota delle operazioni di vRealize comprime e crittografa i dati raccolti dal cloud privato della soluzione VMware di Azure prima di essere inviati tramite la rete VPN o ExpressRoute a vRealize Operations Manager in esecuzione in locale. 

> [!TIP]
> Per istruzioni dettagliate sull'installazione di vRealize Operations Manager, vedere la [documentazione di VMware](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) . 



## <a name="vrealize-operations-running-on-azure-vmware-solution-deployment"></a>Operazioni vRealize in esecuzione nella distribuzione della soluzione VMware di Azure

Un'altra opzione di distribuzione consiste nel distribuire un'istanza di vRealize Operations Manager in uno dei cluster vSphere nel cloud privato della soluzione VMware di Azure, come illustrato di seguito. 

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-2.png" alt-text="Operazioni vRealize locali che gestiscono la distribuzione della soluzione VMware di Azure" border="false":::

Dopo la distribuzione dell'istanza della soluzione VMware di Azure delle operazioni vRealize, è possibile configurare le operazioni di vRealize per la raccolta di dati da vCenter, ESXi, NSX-T, rete VSAN e HCX. 

> [!TIP]
> Per istruzioni dettagliate sull'installazione di vRealize Operations Manager, vedere la [documentazione di VMware](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) .


## <a name="known-limitations"></a>Limitazioni note

- L' **cloudadmin@vsphere.local** utente nella soluzione VMware di Azure dispone di [privilegi limitati](concepts-role-based-access-control.md). La raccolta di memoria in-Guest con strumenti VMware non è supportata con macchine virtuali (VM) in una soluzione VMware di Azure. In questo caso, l'utilizzo di memoria attivo e utilizzato continua a funzionare.
- L'ottimizzazione del carico di lavoro per finalità aziendali basate su host non funziona perché le soluzioni VMware di Azure gestiscono le configurazioni del cluster, incluse le impostazioni DRS.
- L'ottimizzazione del carico di lavoro per la selezione host tra cluster all'interno della SDDC usando lo scopo aziendale basato su cluster è completamente supportata con vRealize Operations Manager 8,0 e versioni successive. Tuttavia, l'ottimizzazione del carico di lavoro non è in grado di riconoscere i pool di risorse e di collocare le macchine virtuali a livello di cluster. Un utente può correggerlo manualmente nella soluzione VMware di Azure server vCenter interfaccia.
- Non è possibile accedere a vRealize Operations Manager usando la soluzione VMware di Azure server vCenter le credenziali. 
- La soluzione VMware di Azure non supporta il plug-in vRealize Operations Manager.

Quando si connette la soluzione VMware di Azure vCenter a vRealize Operations Manager usando un account server vCenter cloud, verrà visualizzato il seguente avviso:

:::image type="content" source="./media/vrealize-operations-manager/warning-adapter-instance-creation-succeeded.png" alt-text="Operazioni vRealize locali che gestiscono la distribuzione della soluzione VMware di Azure":::

L'avviso si verifica perché l' **cloudadmin@vsphere.local** utente nella soluzione VMware di Azure non dispone di privilegi sufficienti per eseguire tutte le azioni server vCenter necessarie per la registrazione. Tuttavia, i privilegi sono sufficienti per l'istanza dell'adapter per eseguire la raccolta dei dati, come illustrato di seguito:

:::image type="content" source="./media/vrealize-operations-manager/adapter-instance-to-perform-data-collection.png" alt-text="Operazioni vRealize locali che gestiscono la distribuzione della soluzione VMware di Azure":::

Per ulteriori informazioni, vedere [privilegi necessari per la configurazione di un'istanza di adapter vCenter](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.core.doc/GUID-3BFFC92A-9902-4CF2-945E-EA453733B426.html).

<!-- LINKS - external -->


<!-- LINKS - internal -->




