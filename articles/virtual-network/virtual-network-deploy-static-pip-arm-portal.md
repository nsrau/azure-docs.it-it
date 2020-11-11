---
title: Creare una VM con un indirizzo IP pubblico statico-portale di Azure
description: Informazioni su come creare una VM con un indirizzo IP pubblico statico mediante il portale di Azure.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/12/2020
ms.author: allensu
ms.openlocfilehash: 1ae0b869b24c4e05c88b936eceb1b9b1db3a9405
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94506312"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-portal"></a>Creare una macchina virtuale con un indirizzo IP pubblico statico usando il portale di Azure

Un indirizzo IP pubblico consente di comunicare con una macchina virtuale da Internet. 

Assegnare un indirizzo IP pubblico statico, anziché un indirizzo dinamico, per assicurarsi che l'indirizzo non cambi mai.   

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

1. Nell'angolo in alto a sinistra del portale selezionare **Crea una risorsa** > **Calcolo** > **Macchina virtuale** oppure cercare **Macchina virtuale** nella casella di ricerca.
   
2. In **Crea macchina virtuale** digitare o selezionare i valori nella scheda **Nozioni di base** :

    | Impostazione | Valore                                          |
    |-----------------------|----------------------------------|
    | **Dettagli del progetto** |  |
    | Subscription | Selezionare la sottoscrizione ad Azure |
    | Gruppo di risorse | Selezionare **Crea nuovo**. </br> In **nome** immettere **myResourceGroup**. </br> Selezionare **OK**. |
    | **Dettagli istanza** |  |
    | Nome macchina virtuale | Immettere **myVM** |
    | Area | Selezionare **Stati Uniti orientali**. |
    | Opzioni di disponibilità | Selezionare **La ridondanza dell'infrastruttura non è richiesta** |
    | Immagine | Selezionare **Windows Server 2019 Datacenter - Gen1** |
    | Istanza Spot di Azure | Selezionare **No** |
    | Dimensione | Scegliere le dimensioni della macchina virtuale o usare l'impostazione predefinita |
    | **Account amministratore** |  |
    | Username | Immettere un nome utente |
    | Password | Immettere una password |
    | Conferma password | Reimmettere la password |

3. Selezionare la scheda **Rete** , oppure selezionare **Avanti: Dischi** , quindi **Avanti: Rete**.
  
4. Nella scheda Rete selezionare o immettere:

    | Impostazione | Valore |
    |-|-|
    | **Interfaccia di rete** |  |
    | Rete virtuale | Accettare il nome di rete predefinito. |
    | Subnet | Accettare la configurazione della subnet predefinita. |
    | IP pubblico | Selezionare **Crea nuovo**. </br> In **Crea indirizzo IP pubblico** immettere **myPublicIP** in nome. </br> Per **SKU** selezionare **standard**. </br> **Assegnazione** selezionare **static**. </br> Selezionare **OK**.  |
    | Gruppo di sicurezza di rete della scheda di interfaccia di rete | Selezionare **Basic**|
    | Porte in ingresso pubbliche | Selezionare **Consenti porte selezionate**. |
    | Selezionare le porte in ingresso | Selezionare **RDP (3389)** |

    > [!WARNING]
    > La porta 3389 è selezionata per consentire l'accesso remoto alla macchina virtuale Windows Server da Internet. Non è consigliabile aprire la porta 3389 a Internet per gestire i carichi di lavoro di produzione. </br> Per un accesso sicuro alle macchine virtuali di Azure, vedere **[che cos'è Azure Bastion?](/azure/bastion/bastion-overview)**
   
5. Selezionare **Rivedi e crea**. 
  
6. Rivedere le impostazioni e quindi selezionare **Crea**.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse e tutte le risorse in esso contenute:

1. Immettere **myResourceGroup** nella casella di **ricerca** nella parte superiore del portale. Selezionare **myResourceGroup** quando viene visualizzato nei risultati della ricerca.
2. Selezionare **Elimina gruppo di risorse**.
3. Immettere **myResourceGroup** in **DIGITARE IL NOME DEL GRUPPO DI RISORSE** e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

Vedere [aggiungere, modificare o rimuovere indirizzi IP](virtual-network-network-interface-addresses.md):

* Per modificare un indirizzo IP pubblico da dinamico a statico.
* Usare indirizzi IP privati.

Per gli indirizzi IP pubblici è previsto un [addebito nominale](https://azure.microsoft.com/pricing/details/ip-addresses). È previsto un [limite](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) al numero di indirizzi IP pubblici che è possibile usare per ogni sottoscrizione.

Lo SKU dell'indirizzo IP pubblico della macchina virtuale deve corrispondere allo SKU IP pubblico di Azure Load Balancer quando viene aggiunto a un pool back-end. Per informazioni dettagliate, vedere [Azure Load Balancer](../load-balancer/skus.md).

È possibile scaricare l'elenco degli intervalli (prefissi) per i cloud [Pubblico](https://www.microsoft.com/download/details.aspx?id=56519), [US Government](https://www.microsoft.com/download/details.aspx?id=57063), [Cina](https://www.microsoft.com/download/details.aspx?id=57062) e [Germania](https://www.microsoft.com/download/details.aspx?id=57064) di Azure.

- Altre informazioni sugli [indirizzi IP pubblici statici](virtual-network-ip-addresses-overview-arm.md#allocation-method).
- Altre informazioni sugli [indirizzi IP pubblici](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) in Azure.
- Altre informazioni su tutte le [impostazioni relative agli indirizzi IP pubblici](virtual-network-public-ip-address.md#create-a-public-ip-address).
- Altre informazioni sugli [indirizzi IP privati](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) e sull'assegnazione di un [indirizzo IP privato statico](virtual-network-network-interface-addresses.md#add-ip-addresses) a una macchina virtuale di Azure.
