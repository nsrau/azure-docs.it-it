---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/27/2020
ms.author: alkohli
ms.openlocfilehash: efa9e996cbfbc8954c294b4da8900b1d5bcbeeed
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467384"
---
Per verificare la VPN, è possibile creare un account di archiviazione a cui è possibile accedere solo tramite la rete virtuale creata. Seguire questa procedura per creare e associare l'account di archiviazione alla rete virtuale creata:

1. Creare un account di archiviazione. È possibile usare l'account di archiviazione che si prevede di usare con il dispositivo Azure Stack Edge. Provare ad accedere all'account di archiviazione da una rete esterna (all'esterno della rete selezionata). L'account di archiviazione deve essere accessibile.
2. Nella portale di Azure passare all'account di archiviazione. 
3. Passare a **firewall e reti virtuali**. Nel riquadro destro per **Consenti accesso da** scegliere **reti selezionate**. Nell' **account di archiviazione sicuro con reti virtuali** scegliere **+ Aggiungi rete virtuale esistente.**

    ![Verifica VPN 1](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-1.png)

4. Nel pannello **Aggiungi reti** :

    - Selezionare la sottoscrizione. Si tratta della stessa sottoscrizione associata alla risorsa Azure Stack Edge/Data Box Gateway. 
    - Dall'elenco a discesa scegliere la rete virtuale da associare a questo account di archiviazione. Selezionare la rete virtuale creata nel passaggio precedente.
    - In **subnet** scegliere la **_impostazione predefinita_* _ e la _GatewaySubnet *. Gli endpoint del servizio verranno abilitati per le combinazioni di rete virtuale/subnet. Il completamento dell'abilitazione dell'accesso richiede fino a 15 minuti.
    - Selezionare **Abilita**.

    ![Verifica VPN 2](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-2.png)
    
4. Salvare **le impostazioni**.

    ![Verifica VPN 3](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-3.png)

5. Dopo aver salvato le impostazioni, è possibile visualizzare le subnet per le quali è abilitata la rete virtuale.

    ![Verifica VPN 4](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-4.png)

5. Per verificare che i dati vengano ora inviati solo tramite VPN, attenersi alla procedura seguente: 
    - Provare ad accedere all'account di archiviazione da una rete esterna (all'esterno della rete selezionata). L'account di archiviazione non deve essere accessibile. 
    - Provare ad accedere all'account di archiviazione dalla rete virtuale/subnet abilitata nelle reti selezionate. L'account di archiviazione deve essere accessibile. 
 
È possibile accedere a questo account di archiviazione solo se la VPN è abilitata. Se si disabilita la VPN, sarà anche necessario modificare le impostazioni di rete dell'account di archiviazione. 

Per altre informazioni, vedere [configurare i firewall e le reti virtuali di archiviazione di Azure](../articles/storage/common/storage-network-security.md). 

