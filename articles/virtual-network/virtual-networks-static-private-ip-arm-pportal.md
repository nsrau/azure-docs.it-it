---
title: Configurare indirizzi IP privati per le VM-portale di Azure
description: Informazioni su come configurare indirizzi IP privati per le VM mediante il portale di Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
tags: azure-resource-manager
ms.assetid: 11245645-357d-4358-9a14-dd78e367b494
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2020
ms.author: kumud
ms.openlocfilehash: c8fdba59a8d31c064745c7a1904204359b386a7f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015929"
---
# <a name="configure-a-private-ip-address-for-a-vm-using-the-azure-portal"></a>Configurare un indirizzo IP privato per una macchina virtuale usando il portale di Azure

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Nei passaggi di esempio seguenti si prevede che un ambiente semplice sia già stato creato. Se si desidera eseguire i passaggi così come sono visualizzati in questo documento, creare prima di tutto [una rete virtuale](quick-create-portal.md#create-a-virtual-network). Tuttavia, nel passaggio 3, usare invece questi valori:

| Impostazione | Valore |
| ------- | ----- |
| Nome | *TestVNet* |
| Spazio degli indirizzi | *192.168.0.0/16* |
| Resource group | **TestRG** (se necessario, selezionare **Crea nuovo** per crearlo) |
| Subnet - Nome | *FrontEnd* |
| Subnet - Intervallo di indirizzi | *192.168.1.0/24* |

## <a name="create-a-vm-for-testing-static-private-ip-addresses"></a>Creare una macchina virtuale per il test di indirizzi IP privati statici
Quando si crea una macchina virtuale in modalità di distribuzione Gestione risorse, non è possibile impostare un indirizzo IP privato statico usando il portale di Azure. In alternativa, si crea prima la VM. È quindi possibile impostare il relativo indirizzo IP privato come statico.

Per creare una VM denominata *DNS01* nella subnet *frontend* di una rete virtuale denominata *TestVNet*, seguire questa procedura:

1. Nel menu del [portale di Azure](https://portal.azure.com) selezionare **Crea una risorsa**.

    ![Creare una risorsa, portale di Azure](./media/virtual-networks-static-ip-arm-pportal/create-a-resource.png)
2. Selezionare **calcolo**  >  **macchina virtuale**.

    ![Creare una macchina virtuale, portale di Azure](./media/virtual-networks-static-ip-arm-pportal/compute-virtual-machine.png)
3. In **nozioni di base** specificare i valori per gli elementi, come descritto nella tabella seguente. Quindi selezionare **Avanti &nbsp; : &nbsp; dischi** e quindi **Avanti &nbsp; : &nbsp; rete**.

    | Elemento | valore |
    | --- | --- |
    | **Sottoscrizione** | Sottoscrizione corrente |
    | **Gruppo di risorse** | **TestRG** (Seleziona da elenco a discesa) |
    | **Nome macchina virtuale** | *DNS01* |
    | **Area** | **(Stati Uniti) Stati Uniti orientali** |
    | **Immagine** | **Windows Server 2019 Datacenter** |
    | **Size** | **Dimensioni della macchina virtuale** di **B1ls**, **offerta** **standard** |
    | **Nome utente** | Nome utente dell'account amministratore |
    | **Password** | Password per il nome utente dell'account amministratore |
    | **Conferma password** | Nuovamente la password |

    ![Scheda nozioni di base, creare una macchina virtuale, portale di Azure](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-basics.png)
4. In **rete** specificare i valori per gli elementi, come descritto nella tabella seguente, quindi selezionare **Avanti**.

    | Elemento | valore |
    | --- | --- |
    | **Rete virtuale** | **TestVNet** |
    | **Subnet** | **FrontEnd** |

    ![Scheda rete, creare una macchina virtuale, portale di Azure](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-networking.png)
5. In **gestione** in **account di archiviazione di diagnostica** scegliere **vnetstorage**. Se l'account di archiviazione non viene visualizzato nell'elenco, selezionare **Crea nuovo**, specificare il **nome** *Vnetstorage* e fare clic su **OK**. Infine, selezionare **verifica &nbsp; + &nbsp; creazione**.

    ![Scheda gestione, creare una macchina virtuale, portale di Azure](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-management.png)
6. In **Review + create** esaminare le informazioni generali e quindi selezionare Create ( **Crea**).

    ![Esaminare + creare la scheda, creare una macchina virtuale, portale di Azure](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-review-create.png)

Al termine della creazione della macchina virtuale, viene visualizzato il messaggio seguente.

![Messaggio di completamento della distribuzione, creare una macchina virtuale, portale di Azure](./media/virtual-networks-static-ip-arm-pportal/deployment-is-complete.png)

## <a name="retrieve-private-ip-address-information-for-a-vm"></a>Recuperare le informazioni sull'indirizzo IP privato per una macchina virtuale
Per visualizzare le informazioni sull'indirizzo IP privato per la nuova macchina virtuale:

1. Passare alla [portale di Azure](https://portal.azure.com) per trovare la macchina virtuale. Cercare e selezionare **Macchine virtuali**.

    ![Macchine virtuali, casella di ricerca, portale di Azure](./media/virtual-networks-static-ip-arm-pportal/search-box-virtual-machines.png)

2. Selezionare il nome della nuova VM (**DNS01**).

    ![Elenco di macchine virtuali, portale di Azure](./media/virtual-networks-static-ip-arm-pportal/virtual-machine-list.png)

3. Scegliere **rete** e selezionare l'unica interfaccia di rete elencata.

    ![Interfaccia di rete, rete, macchina virtuale, portale di Azure](./media/virtual-networks-static-ip-arm-pportal/networking-network-interface.png)

4. Scegliere **configurazioni IP** e selezionare la configurazione IP elencata nella tabella.

    ![Configurazione IP, interfaccia di rete, rete, macchina virtuale, portale di Azure](./media/virtual-networks-static-ip-arm-pportal/network-interface-ip-configurations.png)

5. In **Impostazioni indirizzo IP privato**, nella subnet/rete virtuale **TestVNet/** front-end, prendere nota del valore di **assegnazione** (**dinamico** o **statico**) e dell' **indirizzo IP**.

    ![Assegnazione dinamica o statica, impostazioni dell'indirizzo IP privato precedente, configurazione IP, interfaccia di rete, rete, macchina virtuale, portale di Azure](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-old.png)

## <a name="add-a-static-private-ip-address-to-an-existing-vm"></a>Aggiungere un indirizzo IP statico privato a una macchina virtuale esistente
Per aggiungere un indirizzo IP privato statico alla nuova VM:

1. Nella pagina Configurazione IP impostare l'assegnazione per l'indirizzo IP privato su **statico**.
2. Cambiare l' **indirizzo IP** privato in *192.168.1.101* e quindi selezionare **Save (Salva**).
   
    ![Assegnazione dinamica o statica, nuove impostazioni dell'indirizzo IP privato, configurazione IP, interfaccia di rete, rete, macchina virtuale, portale di Azure](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-new.png)

> [!NOTE]
> Se si nota dopo aver selezionato **Salva** che l'assegnazione è ancora impostata su **dinamico**, l'indirizzo IP digitato è già in uso. Provare con un altro indirizzo IP.

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>Rimuovere un indirizzo IP statico privato da una macchina virtuale
Per rimuovere l'indirizzo IP statico privato dalla macchina virtuale:

Nella pagina Configurazione IP impostare l'assegnazione per l'indirizzo IP privato su **dinamico** e quindi selezionare **Salva**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Impostare gli indirizzi IP all'interno del sistema operativo

Dall'interno del sistema operativo di una macchina virtuale, non è necessario assegnare staticamente l'indirizzo IP *privato* assegnato alla macchina virtuale di Azure. Eseguire l'assegnazione statica di un indirizzo IP privato solo quando necessario, ad esempio quando si [assegnano molti indirizzi IP alle macchine virtuali](virtual-network-multiple-ip-addresses-portal.md). Se si imposta manualmente l'indirizzo IP privato all'interno del sistema operativo, assicurarsi che corrisponda all'indirizzo IP privato assegnato all' [interfaccia di rete](virtual-network-network-interface-addresses.md#change-ip-address-settings)di Azure. In caso contrario, è possibile perdere la connettività alla macchina virtuale. Altre informazioni sulle impostazioni dell'[indirizzo IP privato](virtual-network-network-interface-addresses.md#private).

Inoltre, è consigliabile non assegnare mai manualmente l'indirizzo IP *pubblico* assegnato a una macchina virtuale di Azure all'interno del sistema operativo della macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come gestire le [impostazioni dell'indirizzo IP](virtual-network-network-interface-addresses.md).
