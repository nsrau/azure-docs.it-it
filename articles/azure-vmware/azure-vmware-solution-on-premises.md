---
title: Connettere la soluzione Azure VMware con l'ambiente locale
description: Informazioni su come connettere la soluzione Azure VMware con l'ambiente locale.
ms.topic: tutorial
ms.date: 10/02/2020
ms.openlocfilehash: 2a0cb641df00f3e580e87e38aff382d8e8101fc7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91578913"
---
# <a name="connect-azure-vmware-solution-to-your-on-premises-environment"></a>Connettere la soluzione Azure VMware con l'ambiente locale

In questo articolo si continueranno a usare le [informazioni raccolte durante la pianificazione](production-ready-deployment-steps.md) per connettere la soluzione Azure VMware con l'ambiente locale.

Prima di iniziare, sono necessari due prerequisiti per la connessione della soluzione Azure VMware con l'ambiente locale:

- Un circuito ExpressRoute dall'ambiente locale ad Azure.
- Un blocco di indirizzi di rete non sovrapposti /29 per il peering Copertura globale ExpressRoute, definito come parte della [fase di pianificazione](production-ready-deployment-steps.md).

>[!NOTE]
> È possibile stabilire la connessione tramite VPN, ma questa procedura non rientra nell'ambito di questo documento di avvio rapido.

## <a name="establish-an-expressroute-global-reach-connection"></a>Stabilire una connessione di Copertura globale ExpressRoute

Per stabilire la connettività locale al cloud privato della soluzione Azure VMware usando Copertura globale ExpressRoute, seguire l'esercitazione [Associare gli ambienti locali a un cloud privato](tutorial-expressroute-global-reach-private-cloud.md).



## <a name="verify-on-premises-network-connectivity"></a>Verificare la connettività alla rete locale

Nel **router perimetrale locale** dovrebbe essere ora possibile verificare la posizione in cui ExpressRoute si connette ai segmenti di rete NSX-T e ai segmenti di gestione della soluzione Azure VMware.

>[!NOTE]
>Ogni utente ha un ambiente diverso e in alcuni casi sarà necessario consentire la propagazione di tali route fino alla rete locale.  

Alcuni ambienti avranno firewall che proteggono il circuito ExpressRoute.  Se non sono presenti firewall e non si verifica l'eliminazione di route, è possibile effettuare il ping del server vCenter della soluzione Azure VMware o di una [VM](deploy-azure-vmware-solution.md#add-a-vm-on-the-nsx-t-network-segment) nel segmento NSX-T dall'ambiente locale.

Dalla VM nel segmento NSX-T è inoltre possibile raggiungere le risorse nell'ambiente locale.

## <a name="next-steps"></a>Passaggi successivi

Passare alla sezione successiva per distribuire e configurare VMware HCX

> [!div class="nextstepaction"]
> [Distribuire e configurare VMware HCX](tutorial-deploy-vmware-hcx.md)