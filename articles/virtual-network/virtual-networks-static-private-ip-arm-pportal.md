---
title: Configurare gli indirizzi IP privati per le macchine virtuali - Portale di AzureConfigure private IP addresses for VMs - Azure portal
description: Informazioni su come configurare indirizzi IP privati per le VM mediante il portale di Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
tags: azure-resource-manager
ms.assetid: 11245645-357d-4358-9a14-dd78e367b494
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2020
ms.author: kumud
ms.openlocfilehash: 946926a8a805ec3c53ea3c57dc3eded2462f7673
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461549"
---
# <a name="configure-a-private-ip-address-for-a-vm-using-the-azure-portal"></a>Configurare un indirizzo IP privato per una macchina virtuale usando il portale di AzureConfigure a private IP address for a VM using the Azure portal

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

I passaggi di esempio seguenti prevedono che sia già stato creato un ambiente semplice. Se si desidera eseguire i passaggi così come vengono visualizzati in questo documento, [creare innanzitutto una rete virtuale.](quick-create-portal.md#create-a-virtual-network) Tuttavia, nel passaggio 3, utilizzare invece questi valori:However, in step 3, use these values instead:

| Impostazione | valore |
| ------- | ----- |
| Nome | *TestVNet (informazioni in base alla proprietà)* |
| Spazio degli indirizzi | *192.168.0.0/16* |
| Resource group | **TestRG** (se necessario, selezionare **Crea nuovo** per crearlo) |
| Subnet - Nome | *Frontend* |
| Subnet - Intervallo di indirizzi | *192.168.1.0/24* |

## <a name="create-a-vm-for-testing-static-private-ip-addresses"></a>Creare una macchina virtuale per il test di indirizzi IP privati staticiCreate a VM for testing static private IP addresses
Quando si crea una macchina virtuale in modalità di distribuzione di Resource Manager, non è possibile impostare un indirizzo IP privato statico usando il portale di Azure.When you create a VM in Resource Manager deployment mode, you can't set a static private IP address using the Azure portal. Al contrario, creare prima la macchina virtuale. Quindi è possibile impostare il relativo IP privato in modo che sia statico.

Per creare una macchina virtuale denominata *DNS01* nella subnet *front-end* di una rete virtuale denominata *TestVNet*, attenersi alla seguente procedura:

1. Nel menu del portale di [Azure](https://portal.azure.com) selezionare **Crea una risorsa.**

    ![Creare una risorsa, portale di AzureCreate a resource, Azure portal](./media/virtual-networks-static-ip-arm-pportal/create-a-resource.png)
2. Selezionare **Calcola** > **macchina virtuale**.

    ![Creare VM, portale di AzureCreate VM, Azure portal](./media/virtual-networks-static-ip-arm-pportal/compute-virtual-machine.png)
3. In **Nozioni di base**specificare i valori per gli elementi come descritto nella tabella seguente. Quindi selezionare **&nbsp;Avanti :&nbsp;Dischi** e quindi Avanti **&nbsp;:&nbsp;Rete**.

    | Elemento | valore |
    | --- | --- |
    | **Sottoscrizione** | L'abbonamento corrente |
    | **Gruppo di risorse** | **TestRG** (selezionare dall'elenco a discesa) |
    | **Nome macchina virtuale** | *DNS01* |
    | **Area** | **(Stati Uniti) Stati Uniti orientali** |
    | **Immagine** | **Windows Server 2019 Datacenter** |
    | **Dimensione** | **VM Dimensioni** di **B1ls**, **Offerta** di **Standard** |
    | **Nome utente** | Il nome utente dell'account amministratore |
    | **Password** | La password per il nome utente dell'account amministratore |
    | **Conferma password** | La password di nuovo |

    ![Scheda Nozioni di base, Creare una macchina virtuale, portale di Azure](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-basics.png)
4. In **Rete**specificare i valori per gli elementi come descritto nella tabella seguente e quindi selezionare **Avanti**.

    | Elemento | valore |
    | --- | --- |
    | **Rete virtuale** | **TestVNet (informazioni in base alla proprietà)** |
    | **Subnet** | **Frontend** |

    ![Scheda Rete, Crea una macchina virtuale, Portale di Azure](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-networking.png)
5. In **Gestione**, in **Account di archiviazione di diagnostica**scegliere **vnetstorage**. Se l'account di archiviazione non viene visualizzato nell'elenco, selezionare **Crea nuovo**, specificare un **Nome** di *vnetstorage*e scegliere **OK**. Infine, selezionare **&nbsp;+&nbsp;Rivedi crea**.

    ![Scheda Gestione, Creare una macchina virtuale, portale di Azure](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-management.png)
6. In **Revisione e creazione**esaminare le informazioni di panoramica e quindi selezionare **Crea**.

    ![Esaminare la scheda Creazione, Creare una macchina virtuale, Portale di AzureReview - create tab, Create a virtual machine, Azure portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-review-create.png)

Una volta creata la macchina virtuale, viene visualizzato il messaggio seguente.

![Messaggio di completamento della distribuzione, Creare una macchina virtuale, portale di AzureDeployment completion message, Create a virtual machine, Azure portal](./media/virtual-networks-static-ip-arm-pportal/deployment-is-complete.png)

## <a name="retrieve-private-ip-address-information-for-a-vm"></a>Recuperare informazioni sull'indirizzo IP privato per una macchina virtualeRetrieve private IP address information for a VM
Per visualizzare le informazioni sull'indirizzo IP privato per la nuova macchina virtuale:To view the private IP address information for your new VM:

1. Passare al portale di [Azure](https://portal.azure.com) per trovare la macchina virtuale. Cercare e selezionare **Macchine virtuali**.

    ![Macchine virtuali, casella di ricerca, portale di AzureVirtual machines, Search box, Azure portal](./media/virtual-networks-static-ip-arm-pportal/search-box-virtual-machines.png)

2. Selezionare il nome della nuova macchina virtuale (**DNS01**).

    ![Elenco macchine virtuali, portale di AzureVirtual machine list, Azure portal](./media/virtual-networks-static-ip-arm-pportal/virtual-machine-list.png)

3. Scegliere **Rete**e selezionare l'unica interfaccia di rete elencata.

    ![Interfaccia di rete, rete, macchina virtuale, portale di AzureNetwork interface, networking, virtual machine, Azure portal](./media/virtual-networks-static-ip-arm-pportal/networking-network-interface.png)

4. Scegliere **Configurazioni IP**e selezionare la configurazione IP elencata nella tabella.

    ![Configurazione IP, interfaccia di rete, rete, macchina virtuale, portale di Azure](./media/virtual-networks-static-ip-arm-pportal/network-interface-ip-configurations.png)

5. In **Impostazioni indirizzo IP privato**, nella rete **virtuale/subnet TestVNet/FrontEnd** prendere nota del valore **di assegnazione** (**Dynamic** o **Static**) e dell'indirizzo **IP**.

    ![Assegnazione dinamica o statica, impostazioni dell'indirizzo IP privato precedenti, configurazione IP, interfaccia di rete, rete, macchina virtuale, portale di Azure](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-old.png)

## <a name="add-a-static-private-ip-address-to-an-existing-vm"></a>Aggiungere un indirizzo IP statico privato a una macchina virtuale esistente
To add a static private IP address to your new VM:

1. Nella pagina Configurazione IP impostare l'assegnazione per l'indirizzo IP privato su **Statico**.
2. Modificare **l'indirizzo IP** privato in *192.168.1.101*, quindi selezionare **Salva**.
   
    ![Assegnazione dinamica o statica, nuove impostazioni dell'indirizzo IP privato, configurazione IP, interfaccia di rete, rete, macchina virtuale, portale di Azure](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-new.png)

> [!NOTE]
> Se dopo aver selezionato **Salva,** si noterà che l'assegnazione è ancora impostata su **Dinamico**, l'indirizzo IP digitato è già in uso. Provare con un altro indirizzo IP.

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>Rimuovere un indirizzo IP statico privato da una macchina virtuale
Per rimuovere l'indirizzo IP privato statico dalla macchina virtuale:To remove the static private IP address from your VM:

Nella pagina Configurazione IP impostare l'assegnazione per l'indirizzo IP privato su **Dinamico**, quindi selezionare **Salva**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Impostare gli indirizzi IP all'interno del sistema operativo

Dall'interno del sistema operativo di una macchina virtuale, non è necessario assegnare in modo statico l'indirizzo IP privato assegnato alla macchina virtuale di Azure.From within the operating system of a VM, you shouldn't statically assign the *private* IP that's assigned to the Azure VM. Eseguire l'assegnazione statica di un indirizzo IP privato solo quando è necessario, ad esempio quando si [assegnano molti indirizzi IP alle macchine virtuali](virtual-network-multiple-ip-addresses-portal.md). Se si imposta manualmente l'indirizzo IP privato all'interno del sistema operativo, assicurarsi che corrisponda all'indirizzo IP privato assegnato [all'interfaccia](virtual-network-network-interface-addresses.md#change-ip-address-settings)di rete di Azure. In caso contrario, è possibile perdere la connettività alla macchina virtuale. Altre informazioni sulle impostazioni dell'[indirizzo IP privato](virtual-network-network-interface-addresses.md#private).

Inoltre, non assegnare mai manualmente l'indirizzo IP *pubblico* assegnato a una macchina virtuale di Azure all'interno del sistema operativo della macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come gestire le [impostazioni dell'indirizzo IP](virtual-network-network-interface-addresses.md).
