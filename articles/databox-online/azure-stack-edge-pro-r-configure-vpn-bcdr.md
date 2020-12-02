---
title: Configurare la continuità aziendale e il ripristino di emergenza (BCDR) nella rete privata virtuale (VPN) di Azure Stack Edge
description: Viene descritto come configurare BCDR in una VPN Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/17/2020
ms.author: alkohli
ms.openlocfilehash: 4d735c623a6dffe24108d06d00caa7fba987c7df
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467276"
---
# <a name="configure-business-continuity-and-disaster-recovery-for-azure-stack-edge-vpn"></a>Configurare la continuità aziendale e il ripristino di emergenza per VPN Azure Stack Edge

[!INCLUDE [applies-to-r-skus](../../includes/azure-stack-edge-applies-to-r-sku.md)]

Questo articolo descrive come configurare la continuità aziendale e il ripristino di emergenza (BCDR) in una rete privata virtuale (VPN) configurata in un dispositivo Azure Stack Edge.

Questo articolo si applica sia al dispositivo R Azure Stack Edge Pro che al dispositivo Mini R Azure Stack Edge.

## <a name="configure-failover-to-a-paired-region"></a>Configurare il failover in un'area abbinata

Il dispositivo Azure Stack Edge usa altri servizi di Azure, ad esempio archiviazione di Azure. È possibile configurare BCDR in qualsiasi servizio di Azure specifico usato dal dispositivo Azure Stack Edge. Se viene eseguito il failover di un servizio di Azure usato da Azure Stack Edge nell'area abbinata, il dispositivo Azure Stack Edge si connetterà ai nuovi indirizzi IP e la comunicazione non verrà crittografata doppiamente. 

Il dispositivo Azure Stack Edge usa lo split tunneling e tutti i dati e i servizi configurati nell'area Home (l'area associata al dispositivo Azure Stack Edge) passano attraverso il tunnel VPN. Se il failover dei servizi di Azure viene eseguito in un'area abbinata esterna all'area principale, i dati non verranno più inviati alla VPN e quindi non verranno crittografati in modo doppiato. 

In questo scenario, in genere sono interessati solo alcuni dei servizi di Azure. Per risolvere questo problema, è necessario apportare le modifiche seguenti nella configurazione VPN Azure Stack Edge:

1. Aggiungere gli intervalli di indirizzi IP del servizio di Azure di failover nelle route inclusive per VPN in Azure Stack Edge. I servizi inizieranno a essere instradati attraverso la VPN.

    Per aggiungere le route inclusive, è necessario scaricare il file JSON con le route specifiche del servizio. Assicurarsi di aggiornare il file con le nuove route.
2. Aggiungere gli intervalli di indirizzi IP dei servizi di Azure corrispondenti nella tabella di route di Azure.
3. Aggiungere le route al firewall.

> [!NOTE]
>
> 1. Il failover di un gateway VPN di Azure e di una rete virtuale di Azure (VNET) viene risolto nella sezione [ripristino da un'area di Azure non riuscita a causa di un'emergenza](#recover-from-a-failed-azure-region).
> 2. Gli intervalli IP aggiunti nella tabella di route di Azure potrebbero superare il limite di 400. In tal caso, è necessario seguire le istruzioni riportate nella sezione [passare da un'area di Azure a un'altra area di Azure](#move-from-an-azure-region-to-another).

## <a name="recover-from-a-failed-azure-region"></a>Ripristino da un'area di Azure non riuscita

Nel caso in cui venga eseguito il failover dell'intera area di Azure a causa di un evento irreversibile, ad esempio un terremoto, verranno eseguiti il failover di tutti i servizi di Azure in tale area, incluso il servizio Azure Stack Edge. Poiché sono presenti più servizi, le route Inclusive possono facilmente variare in poche centinaia. Azure prevede una limitazione di 400 Route. 

Quando viene eseguito il failover dell'area, viene eseguito il failover anche della rete virtuale (VNET) sulla nuova area, quindi il gateway di rete virtuale (gateway VPN). Per risolvere questa modifica, apportare le modifiche seguenti nella configurazione della VPN di Azure Stack Edge:

1. Spostare il VNET nell'area di destinazione. Per altre informazioni, vedere: [spostare una rete virtuale di Azure in un'altra area tramite il portale di Azure](../virtual-network/move-across-regions-vnet-portal.md).
2. Distribuire un nuovo gateway VPN di Azure nell'area di destinazione in cui è stato spostato il vnet. Per altre informazioni, vedere [creare un gateway di rete virtuale](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw).
3. Aggiornare la configurazione VPN Azure Stack Edge per usare il gateway VPN precedente nella connessione VPN e quindi selezionare l'area di destinazione per aggiungere le route che usano il gateway VPN.
4. Aggiornare la tabella di route di Azure in ingresso se viene modificato anche il pool di indirizzi client. 

## <a name="move-from-an-azure-region-to-another"></a>Passare da un'area di Azure a un'altra

È possibile spostare il dispositivo Azure Stack Edge da una posizione a un'altra. Per usare un'area più vicina alla posizione in cui viene distribuito il dispositivo, è necessario configurare il dispositivo per una nuova area principale. Apportare le modifiche seguenti:

1. È possibile aggiornare la configurazione VPN Azure Stack Edge per usare il gateway VPN di una nuova area e selezionare la nuova area per aggiungere route che usano il gateway VPN.

## <a name="next-steps"></a>Passaggi successivi

[Eseguire il backup del dispositivo Azure stack Edge](azure-stack-edge-gpu-prepare-device-failure.md).