---
title: 'Creare una rete virtuale: guida introduttiva - Portale di Azure'
titleSuffix: Azure Virtual Network
description: 'Guida introduttiva: creare una rete virtuale nel portale di Azure. Queste reti consentono alle risorse di Azure, come le macchine virtuali, di comunicare in modo sicuro tra loro e con Internet.'
author: KumudD
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/05/2020
ms.author: kumud
ms.openlocfilehash: 1a1593566b8bdb72f322d64c1ee99c7018f49329
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128654"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Guida introduttiva: Creare una rete virtuale con il portale di Azure

In questa Guida introduttiva si apprenderà come creare una rete virtuale usando il portale di Azure. Si distribuiscono due macchine virtuali (VM). A questo punto, è possibile comunicare in modo sicuro tra le macchine virtuali e connettersi alle macchine virtuali da Internet. Una rete virtuale è il blocco costitutivo fondamentale per una rete privata in Azure. Consente alle risorse di Azure, come le macchine virtuali, di comunicare in modo sicuro tra loro e con Internet.

## <a name="prerequisites"></a>Prerequisites

* Un account Azure con una sottoscrizione attiva. [È possibile crearne uno gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Crea rete virtuale

1. Nel menu del portale di Azure selezionare **Crea una risorsa**. In Azure Marketplace selezionare **Rete** > **Rete virtuale**.

1. In **Crea rete virtuale** immettere o selezionare queste informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    | Subscription | Selezionare la propria sottoscrizione.|
    | Resource group | Selezionare **Crea nuovo**, immettere *myResourceGroup* e selezionare **OK**. |
    | Nome | Immettere *myVirtualNetwork*. |
    | Location | Selezionare **Stati Uniti orientali**.|

1. Selezionare **Avanti: indirizzi IP**e per **spazio indirizzi IPv4**immettere *10.1.0.0/16*.

1. Selezionare **Aggiungi subnet**, quindi immettere *MyVirtualSubnet* per **nome subnet** e *10.1.0.0/24* per **intervallo di indirizzi subnet**.

1. Selezionare **Aggiungi**, quindi selezionare **Verifica + crea**. Lasciare le altre impostazioni sui valori predefiniti e selezionare **Crea**.

1. In **Crea rete virtuale**selezionare **Crea**.

## <a name="create-virtual-machines"></a>Creare macchine virtuali

Creare due VM nella rete virtuale:

### <a name="create-the-first-vm"></a>Creare la prima VM

1. Nel menu del portale di Azure selezionare **Crea una risorsa**.

1. In Azure Marketplace selezionare **Calcolo** > **Windows Server 2019 Datacenter**. Selezionare **Create** (Crea).

1. In **Creare una macchina virtuale - Informazioni di base**, immettere o selezionare queste informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    | **Dettagli del progetto** | |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Selezionare **myResourceGroup**. Questo gruppo di risorse è stato creato nella sezione precedente. |
    | **Dettagli istanza** |  |
    | Nome macchina virtuale | Immettere *myVm1*. |
    | Region | Selezionare **Stati Uniti orientali**. |
    | Opzioni di disponibilità | Per impostazione predefinita, **non è necessaria alcuna ridondanza dell'infrastruttura**. |
    | Image | Il valore predefinito è **Windows Server 2019 datacenter**. |
    | Dimensione | Per impostazione predefinita, **DS1 V2 standard**. |
    | **Account amministratore** |  |
    | Username | Immettere un nome utente a scelta. |
    | Password | Immettere una password a scelta. La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirm Password | Immettere nuovamente la password. |
    | **Regole porta in ingresso** |  |
    | Porte in ingresso pubbliche | Selezionare **Consenti porte selezionate**. |
    | Selezionare le porte in ingresso | Immettere *http (80)* e *RDP (3389)* . |
    | **Risparmia denaro** |  |
    | Già in possesso di una licenza di Windows? | Il valore predefinito è **No**. |

1. Selezionare **Avanti: dischi**.

1. In **creare una macchina virtuale-dischi**, Mantieni le impostazioni predefinite e seleziona **Avanti: rete**.

1. In **Creare una macchina virtuale - Rete**, selezionare queste informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    | Rete virtuale | Il valore predefinito è **myVirtualNetwork**. |
    | Subnet | Il valore predefinito è **myVirtualSubnet (10.1.0.0/24)** . |
    | IP pubblico | Il valore predefinito è **(nuovo) myVm-IP**. |
    | Gruppo di sicurezza di rete della scheda di interfaccia di rete | Il valore predefinito è **Basic**. |
    | Porte in ingresso pubbliche | Impostazione predefinita per **consentire le porte selezionate**. |
    | Selezionare le porte in ingresso | Impostazione predefinita su **http** e **RDP**.

1. Selezionare **Avanti: gestione**.

1. In **Creare una macchina virtuale - Gestione**, per **Account di archiviazione di diagnostica**, selezionare **Crea nuovo**.

1. In **Crea account di archiviazione**, immettere o selezionare queste informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    | Nome | Immettere *myvmstorageaccount*. Se il nome è già usato, creare un nome univoco.|
    | Tipo di account | Il valore predefinito è **archiviazione (utilizzo generico V1)** . |
    | Prestazioni | Il valore predefinito è **standard**. |
    | Replica | Il valore predefinito è **archiviazione con ridondanza locale (con ridondanza locale)** . |

1. Selezionare **OK**, quindi selezionare **Verifica + crea**. Si viene reindirizzati alla pagina **Rivedi e crea** dove Azure convalida la configurazione.

1. Quando viene visualizzato il messaggio **Convalida superata**, selezionare **Crea**.

### <a name="create-the-second-vm"></a>Creare la seconda VM

Ripetere la procedura descritta nella sezione precedente per creare un'altra macchina virtuale.

> [!IMPORTANT]
> Per **nome macchina virtuale**immettere *myVm2*.
>
> Per l' **account di archiviazione diagnosi**, assicurarsi di selezionare **myvmstorageaccount**, invece di crearne uno.

## <a name="connect-to-a-vm-from-the-internet"></a>Connettersi a una VM da Internet

Dopo aver creato *myVm1*, connettersi a Internet.

1. Nella portale di Azure cercare e selezionare *myVm1*.

1. Selezionare **Connetti**, quindi **RDP**.

    ![Connettersi a una macchina virtuale](./media/quick-create-portal/connect-to-virtual-machine.png)

    Verrà visualizzata la pagina **Connetti** .

1. Selezionare **Scarica file RDP**. Azure crea e scarica nel computer un file Remote Desktop Protocol con estensione *.rdp*.

1. Aprire il file RDP. Quando richiesto, selezionare **Connetti**.

1. Immettere il nome utente e la password specificati al momento della creazione della macchina virtuale.

    > [!NOTE]
    > Potrebbe essere necessario selezionare **Altre opzioni** > **Usa un altro account** per specificare le credenziali immesse al momento della creazione della macchina virtuale.

1. Selezionare **OK**.

1. Quando si accede, è possibile che venga visualizzato un avviso di certificato. Se si riceve un avviso relativo al certificato, selezionare **Sì** oppure **Continua**.

1. Quando viene visualizzato il desktop della macchina virtuale, ridurlo a icona per tornare al desktop locale.

## <a name="communicate-between-vms"></a>Stabilire la comunicazione tra le macchine virtuali

1. Nel desktop remoto di *myVm1*, aprire PowerShell.

1. Immettere `ping myVm2`.

    Verrà visualizzato un messaggio simile all'output seguente:

    ```output
    Pinging myVm2.0v0zze1s0uiedpvtxz5z0r0cxg.bx.internal.clouda
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.1.0.5:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

    Il `ping` ha esito negativo perché `ping` usa il protocollo ICMP (Internet Control Message Protocol). Per impostazione predefinita, il protocollo ICMP non dispone dell'autorizzazione del firewall di Windows.

1. Per consentire a *myVm2* di eseguire il ping su *myVm1* in un passaggio successivo, immettere questo comando:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Questo comando permette il passaggio di dati in ingresso da parte del protocollo ICMP attraverso il firewall di Windows:

1. Chiudere la connessione Desktop remoto a *myVm1*.

1. Completare nuovamente i passaggi descritti in [Connettersi a una VM da Internet](#connect-to-a-vm-from-the-internet), ma questa volta connettersi a *myVm2*.

1. Da un prompt dei comandi immettere `ping myvm1`.

    Si otterrà un messaggio simile al seguente:

    ```output
    Pinging myVm1.0v0zze1s0uiedpvtxz5z0r0cxg.bx.internal.cloudapp.net [10.1.0.4] with 32 bytes of data:
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128

    Ping statistics for 10.1.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 1ms, Average = 0ms
    ```

    Si ricevono risposte da *myVm1*, poiché il protocollo ICMP è stato consentito in Windows Firewall nella macchina virtuale *myVm1* nel passaggio 3.

1. Chiudere la connessione Desktop remoto a *myVm2*.

## <a name="clean-up-resources"></a>Pulire le risorse

In questa guida introduttiva sono state create una rete virtuale predefinita e due VM. È stata effettuata la connessione a una macchina virtuale da Internet ed è stata stabilita una comunicazione sicura tra le due macchine virtuali.

Quando non si ha più bisogno della rete e delle macchine virtuali, rimuovere il gruppo di risorse e tutte le risorse che contiene:

1. Cercare e selezionare *myResourceGroup*.

1. Selezionare **Elimina gruppo di risorse**.

1. Immettere *myResourceGroup* in **DIGITARE IL NOME DEL GRUPPO DI RISORSE** e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle impostazioni della rete virtuale, vedere [creare, modificare o eliminare una rete virtuale](manage-virtual-network.md).

Per impostazione predefinita, Azure consente la comunicazione sicura tra le macchine virtuali. Azure consente solo connessioni Desktop remoto in ingresso alle VM Windows da Internet. Per altre informazioni sui tipi di comunicazioni di rete VM, vedere [filtrare il traffico di rete](tutorial-filter-network-traffic.md).
