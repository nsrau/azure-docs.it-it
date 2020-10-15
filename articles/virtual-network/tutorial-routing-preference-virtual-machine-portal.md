---
title: Configurare la preferenza di routing per una macchina virtuale - Portale di Azure
description: Informazioni su come creare una macchina virtuale con un indirizzo IP pubblico scegliendo il tipo di routing con il portale di Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2020
ms.author: mnayak
ms.openlocfilehash: af3d9e9fcf0dad6a5e51a3db87b63567d701970e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84687991"
---
# <a name="configure-routing-preference-for-a-vm-using-the-azure-portal"></a>Configurare la preferenza di routing per una macchina virtuale con il portale di Azure

Questo articolo illustra come configurare la preferenza di routing per una macchina virtuale. Quando si sceglie **Internet** come preferenza di routing, il traffico associato a Internet proveniente dalla macchina virtuale viene instradato tramite la rete dell'ISP. L'opzione di routing predefinita è tramite la rete globale Microsoft.

Questo articolo illustra come creare una macchina virtuale con un indirizzo IP pubblico impostato per instradare il traffico tramite Internet pubblico con il portale di Azure.

> [!IMPORTANT]
> La preferenza di routing è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-feature-for-your-subscription"></a>Registrare la funzionalità per la propria sottoscrizione
La funzionalità Preferenza di routing è attualmente disponibile in anteprima. È necessario registrare la funzionalità per la propria sottoscrizione usando Azure PowerShell come indicato di seguito:
```azurepowershell
Register-AzProviderFeature -FeatureName AllowRoutingPreferenceFeature ProviderNamespace Microsoft.Network
```

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://preview.portal.azure.com/).

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

1. Selezionare **+ Crea una risorsa** visualizzato nell'angolo in alto a sinistra del portale di Azure.
2. Selezionare **Calcolo** e quindi **Windows Server 2016 VM** o un altro sistema operativo a scelta.
3. Immettere o selezionare le informazioni seguenti, accettare le impostazioni predefinite rimanenti e quindi scegliere **OK**:

    |Impostazione|valore|
    |---|---|
    |Nome|myVM|
    |Nome utente| Immettere un nome utente a scelta.|
    |Password| Immettere una password a scelta. La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Subscription| Selezionare la propria sottoscrizione.|
    |Resource group| Selezionare **Usa esistente** e selezionare **myResourceGroup**.|
    |Location| Selezionare **Stati Uniti orientali**.|

4. Selezionare una dimensione per la VM e quindi selezionare **Seleziona**.
5. Nella scheda **Rete** fare clic su **Crea nuovo** per **Indirizzo IP pubblico**.
6. Immettere *myPublicIpAddress*, selezionare **Standard** come SKU, quindi selezionare la preferenza di routing **Internet** e infine fare clic su **OK**, come illustrato nell'immagine seguente:

   ![Selezionare Statico](./media/tutorial-routing-preference-virtual-machine-portal/routing-preference-internet-new.png)

6. In **Selezionare le porte in ingresso pubbliche** selezionare una porta o nessuna porta. La porta 3389 è selezionata per consentire l'accesso remoto alla macchina virtuale Windows Server da Internet. Non è consigliabile aprire la porta 3389 da Internet per i carichi di lavoro di produzione.

   ![Selezionare una porta](./media/tutorial-routing-preference-virtual-machine-portal/pip-ports-new.png)

7. Accettare le impostazioni predefinite rimanenti e fare clic su **OK**.
8. Nella pagina **Riepilogo** selezionare **Crea**. La distribuzione della macchina virtuale richiede alcuni minuti.
9. Al termine della distribuzione della macchina virtuale, immettere *myPublicIpAddress* nella casella di ricerca nella parte superiore del portale. Selezionare **myPublicIpAddress** quando viene visualizzato nei risultati della ricerca.
10. È possibile visualizzare l'indirizzo IP pubblico assegnato e verificare che l'indirizzo sia assegnato alla macchina virtuale **myVM**, come illustrato nell'immagine seguente:

    ![Visualizzare l'indirizzo IP pubblico](./media/tutorial-routing-preference-virtual-machine-portal/pip-properties-new.png)

11. Selezionare **Rete**, quindi fare clic sulla scheda di interfaccia di rete **mynic** e infine selezionare l'indirizzo IP pubblico per confermare che la preferenza di routing venga assegnata come **Internet**.
    ![Visualizzare l'indirizzo IP pubblico](./media/tutorial-routing-preference-virtual-machine-portal/pip-routing-internet-new.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse e tutte le risorse in esso contenute:

1. Immettere *myResourceGroup* nella casella di **ricerca** nella parte superiore del portale. Selezionare **myResourceGroup** quando viene visualizzato nei risultati della ricerca.
2. Selezionare **Elimina gruppo di risorse**.
3. Immettere *myResourceGroup* in **DIGITARE IL NOME DEL GRUPPO DI RISORSE** e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sugli [indirizzi IP pubblici con preferenza di routing](routing-preference-overview.md).
- Altre informazioni sugli [indirizzi IP pubblici](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) in Azure.
- Altre informazioni su tutte le [impostazioni relative agli indirizzi IP pubblici](virtual-network-public-ip-address.md#create-a-public-ip-address).
