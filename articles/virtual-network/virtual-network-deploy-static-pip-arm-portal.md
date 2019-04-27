---
title: Creare una VM con un indirizzo IP pubblico statico - Portale di Azure | Documentazione Microsoft
description: Informazioni su come creare una VM con un indirizzo IP pubblico statico mediante il portale di Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: e9546bcc-f300-428f-b94a-056c5bd29035
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: kumud
ms.openlocfilehash: 42e035b9dca6168fe77e6982505692cf18bfcb40
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743173"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-portal"></a>Creare una macchina virtuale con un indirizzo IP pubblico statico usando il portale di Azure

È possibile creare una macchina virtuale con un indirizzo IP pubblico statico. Un indirizzo IP pubblico consente di comunicare con una macchina virtuale da Internet. Assegnare un indirizzo IP pubblico statico, anziché un indirizzo dinamico, per assicurarsi che l'indirizzo non cambi mai. Altre informazioni sugli [indirizzi IP pubblici statici](virtual-network-ip-addresses-overview-arm.md#allocation-method). Per modificare da dinamico a statico un indirizzo IP pubblico assegnato a una macchina virtuale esistente o per usare indirizzi IP privati, vedere [Aggiungere, modificare o rimuovere indirizzi IP](virtual-network-network-interface-addresses.md). Gli indirizzi IP pubblici hanno un [costo nominale](https://azure.microsoft.com/pricing/details/ip-addresses)ed esiste un [limite](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) al numero di indirizzi IP pubblici che è possibile usare per ogni sottoscrizione.

## <a name="sign-in-to-azure"></a>Accedi ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

1. Selezionare **+ Crea una risorsa** visualizzato nell'angolo in alto a sinistra del portale di Azure.
2. Selezionare **Calcolo** e quindi **Windows Server 2016 VM** o un altro sistema operativo a scelta.
3. Immettere o selezionare le informazioni seguenti, accettare le impostazioni predefinite rimanenti e quindi scegliere **OK**:

    |Impostazione|Valore|
    |---|---|
    |NOME|myVM|
    |Nome utente| Immettere un nome utente a scelta.|
    |Password| Immettere una password a scelta. La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Sottoscrizione| Selezionare la propria sottoscrizione.|
    |Gruppo di risorse| Selezionare **Usa esistente** e selezionare **myResourceGroup**.|
    |Località| Selezionare **Stati Uniti orientali**.|

4. Selezionare una dimensione per la VM e quindi selezionare **Seleziona**.
5. In **Impostazioni** selezionare **Indirizzo IP pubblico**.
6. Immettere *myPublicIpAddress*, selezionare **Statico** e quindi fare clic su **OK**, come illustrato nell'immagine seguente:

   ![Selezionare Statico](./media/virtual-network-deploy-static-pip-arm-portal/select-static.png)

   Se l'indirizzo IP pubblico deve essere uno SKU standard, in **SKU** selezionare **Standard**. Altre informazioni sugli [SKU di indirizzi IP pubblici](virtual-network-ip-addresses-overview-arm.md#sku). Se la macchina virtuale verrà aggiunta al pool back-end di un Azure Load Balancer pubblico, lo SKU dell'indirizzo IP pubblico della macchina virtuale deve corrispondere allo SKU dell'indirizzo IP pubblico del bilanciamento del carico. Per informazioni dettagliate, vedere [Azure Load Balancer](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#skus).

6. In **Selezionare le porte in ingresso pubbliche** selezionare una porta o nessuna porta. La porta 3389 è selezionata per consentire l'accesso remoto alla macchina virtuale Windows Server da Internet. Non è consigliabile aprire la porta 3389 da Internet per i carichi di lavoro di produzione.

   ![Selezionare una porta](./media/virtual-network-deploy-static-pip-arm-portal/select-port.png)

7. Accettare le impostazioni predefinite rimanenti e fare clic su **OK**.
8. Nella pagina **Riepilogo** selezionare **Crea**. La distribuzione della macchina virtuale richiede alcuni minuti.
9. Al termine della distribuzione della macchina virtuale, immettere *myPublicIpAddress* nella casella di ricerca nella parte superiore del portale. Selezionare **myPublicIpAddress** quando viene visualizzato nei risultati della ricerca.
10. È possibile visualizzare l'indirizzo IP pubblico assegnato e verificare che l'indirizzo sia assegnato alla macchina virtuale **myVM**, come illustrato nell'immagine seguente:

    ![Visualizzare l'indirizzo IP pubblico](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-overview.png)

    Azure ha assegnato un indirizzo IP pubblico tra gli indirizzi usati nell'area in cui la macchina virtuale è stata creata. È possibile scaricare l'elenco degli intervalli (prefissi) per i cloud [Pubblico](https://www.microsoft.com/download/details.aspx?id=56519), [US Government](https://www.microsoft.com/download/details.aspx?id=57063), [Cina](https://www.microsoft.com/download/details.aspx?id=57062) e [Germania](https://www.microsoft.com/download/details.aspx?id=57064) di Azure.

11. Selezionare **Configurazione** per confermare che il valore dell'assegnazione è **Statico**.

    ![Visualizzare l'indirizzo IP pubblico](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-configuration.png)

> [!WARNING]
> Non modificare le impostazioni dell'indirizzo IP all'interno del sistema operativo della macchina virtuale. Il sistema operativo non rileva gli indirizzi IP pubblici di Azure. Anche se è possibile aggiungere al sistema operativo impostazioni relative a indirizzi IP privati, è consigliabile farlo solo se necessario e solo dopo aver letto [Aggiungere un indirizzo IP privato a un sistema operativo](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse e tutte le risorse in esso contenute:

1. Immettere *myResourceGroup* nella casella di **ricerca** nella parte superiore del portale. Selezionare **myResourceGroup** quando viene visualizzato nei risultati della ricerca.
2. Selezionare **Elimina gruppo di risorse**.
3. Immettere *myResourceGroup* in **DIGITARE IL NOME DEL GRUPPO DI RISORSE** e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sugli [indirizzi IP pubblici](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) in Azure
- Altre informazioni su tutte le [impostazioni relative agli indirizzi IP pubblici](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Altre informazioni sugli [indirizzi IP privati](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) e sull'assegnazione di un [indirizzo IP privato statico](virtual-network-network-interface-addresses.md#add-ip-addresses) a una macchina virtuale di Azure
- Altre informazioni sulla creazione di macchine virtuali [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)