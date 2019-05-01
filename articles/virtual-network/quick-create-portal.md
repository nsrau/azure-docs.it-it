---
title: 'Creare una rete virtuale: guida introduttiva - Portale di Azure'
titlesuffix: Azure Virtual Network
description: Questa guida introduttiva illustra come creare una rete virtuale usando il portale di Azure. Una rete virtuale consente alle risorse di Azure, come le macchine virtuali, di comunicare privatamente tra loro e con Internet.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.service: virtual-network
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 11/30/2018
ms.author: kumud
ms.openlocfilehash: 995bc8e7b2eb4e9160b2b625067f20324df2cbfd
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64717002"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Guida introduttiva: Creare una rete virtuale usando il portale di Azure

Una rete virtuale consente alle risorse di Azure, come le macchine virtuali (VM), di comunicare privatamente tra loro e con Internet. Questa guida introduttiva illustra come creare una rete virtuale. Dopo avere creato una rete virtuale, si distribuiscono due VM nella rete virtuale. Quindi si effettua la connessione alle macchine virtuali da Internet e si stabilisce una comunicazione privata tra le due.

Se non si ha una sottoscrizione di Azure, creare ora un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Crea rete virtuale

1. Nella parte superiore sinistra della schermata, selezionare **Crea una risorsa** > **Rete** > **Rete virtuale**.

1. In **Crea rete virtuale** immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    | NOME | Immettere *myVirtualNetwork*. |
    | Spazio degli indirizzi | Immettere *10.1.0.0/16*. |
    | Sottoscrizione | Selezionare la propria sottoscrizione.|
    | Gruppo di risorse | Selezionare **Crea nuovo**, immettere *myResourceGroup* e selezionare **OK**. |
    | Località | Selezionare **Stati Uniti orientali**.|
    | Subnet - Nome | Immettere *myVirtualSubnet*. |
    | Subnet - Intervallo di indirizzi | Immettere *10.1.0.0/24*. |

1. Lasciare tutte le impostazioni predefinite e selezionare **Crea**.

## <a name="create-virtual-machines"></a>Creare macchine virtuali

Creare due VM nella rete virtuale:

### <a name="create-the-first-vm"></a>Creare la prima VM

1. Nella parte superiore sinistra della schermata, selezionare **Crea una risorsa** > **Calcolo** > **Windows Server 2016 Datacenter**.

1. In **Creare una macchina virtuale - Informazioni di base**, immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    | **DETTAGLI DEL PROGETTO** | |
    | Sottoscrizione | Selezionare la propria sottoscrizione. |
    | Gruppo di risorse | Selezionare **MyResourceGroup**. È stato creato nella sezione precedente. |
    | **DETTAGLI DELL'ISTANZA** |  |
    | Nome macchina virtuale | Immettere *myVm1*. |
    | Region | Selezionare **Stati Uniti orientali**. |
    | Opzioni di disponibilità | Lasciare l'impostazione predefinita **Nessuna ridondanza dell'infrastruttura necessaria**. |
    | Image | Lasciare l'impostazione predefinita **Windows Server 2016 Datacenter**. |
    | Dimensione | Lasciare l'impostazione predefinita **DS1 Standard v2**. |
    | **ACCOUNT AMMINISTRATORE** |  |
    | Username | Immettere un nome utente a scelta. |
    | Password | Immettere una password a scelta. La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirm Password | Reimmettere la password. |
    | **REGOLE PORTA IN INGRESSO** |  |
    | Porte in ingresso pubbliche | Lasciare il valore predefinito **Nessuna**. |
    | **RISPARMIA** |  |
    | Già in possesso di una licenza di Windows? | Lasciare il valore predefinito **No**. |

1. Selezionare **Avanti: Dischi**.

1. In **Creare una macchina virtuale - Dischi**, lasciare i valori predefiniti e selezionare **Avanti: Rete**.

1. In **Creare una macchina virtuale - Rete**, selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    | Rete virtuale | Lasciare l'impostazione predefinita **myVirtualNetwork**. |
    | Subnet | Lasciare l'impostazione predefinita **myVirtualSubnet (10.1.0.0/24)**. |
    | IP pubblico | Lasciare l'impostazione predefinita **(nuovo) myVm-ip**. |
    | Porte di sicurezza di rete | Selezionare **Consenti porte selezionate**. |
    | Selezionare le porte in ingresso | Selezionare **HTTP** e **RDP**.

1. Selezionare **Avanti: Gestione**.

1. In **Creare una macchina virtuale - Gestione**, per **Account di archiviazione di diagnostica**, selezionare **Crea nuovo**.

1. In **Crea account di archiviazione**, immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    | NOME | Immettere *myvmstorageaccount*. |
    | Tipo di account | Lasciare l'impostazione predefinita **Archiviazione (utilizzo generico v1)**. |
    | Prestazioni | Lasciare l'impostazione predefinita **Standard**. |
    | Replica | Lasciare l'impostazione predefinita **Archiviazione con ridondanza locale**. |

1. Selezionare **OK**.

1. Selezionare **Rivedi e crea**. Si verrà reindirizzati alla pagina **Rivedi e crea** e Azure convaliderà la configurazione.

1. Quando viene visualizzato il messaggio **Convalida superata**, selezionare **Crea**.

### <a name="create-the-second-vm"></a>Creare la seconda VM

1. Completare i passaggi 1 e 9 di cui sopra.

    > [!NOTE]
    > Nel passaggio 2, per il **nome della macchina virtuale**, immettere *myVm2*.
    >
    > Nel passaggio 7, per **account di archiviazione di diagnostica**, assicurarsi di selezionare **myvmstorageaccount**.

1. Selezionare **Rivedi e crea**. Si verrà reindirizzati alla pagina **Rivedi e crea** e Azure convaliderà la configurazione.

1. Quando viene visualizzato il messaggio **Convalida superata**, selezionare **Crea**.

## <a name="connect-to-a-vm-from-the-internet"></a>Connettersi a una VM da Internet

Dopo aver creato *myVm1*, connettersi alla macchina virtuale tramite Internet.

1. Nella barra di ricerca del portale, immettere *myVm1*.

1. Scegliere il pulsante **Connetti**.

    ![Connettersi a una macchina virtuale](./media/quick-create-portal/connect-to-virtual-machine.png)

    Dopo aver selezionato il pulsante **Connetti** viene aperta la finestra **Connetti alla macchina virtuale**.

1. Selezionare **Scarica file RDP**. Azure crea e scarica nel computer un file Remote Desktop Protocol con estensione *.rdp*.

1. Aprire il file con estensione *.rdp* scaricato.

    1. Quando richiesto, selezionare **Connetti**.

    1. Immettere il nome utente e la password specificati al momento della creazione della VM.

        > [!NOTE]
        > Potrebbe essere necessario selezionare **Altre opzioni** > **Usa un altro account** per specificare le credenziali immesse al momento della creazione della macchina virtuale.

1. Selezionare **OK**.

1. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Se si riceve un avviso relativo al certificato, selezionare **Sì** oppure **Continua**.

1. Quando viene visualizzato il desktop della macchina virtuale, ridurlo a icona per tornare al desktop locale.

## <a name="communicate-between-vms"></a>Stabilire la comunicazione tra le macchine virtuali

1. Nel desktop remoto di *myVm1*, aprire PowerShell.

1. Immettere `ping myVm2`.

    Si otterrà un messaggio simile al seguente:

    ```powershell
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
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

    Questo comando permette il passaggio di dati in ingresso da parte del protocollo ICMP attraverso il firewall di Windows:

1. Chiudere la connessione Desktop remoto a *myVm1*.

1. Completare nuovamente i passaggi descritti in [Connettersi a una VM da Internet](#connect-to-a-vm-from-the-internet), ma questa volta connettersi a *myVm2*.

1. Da un prompt dei comandi immettere `ping myvm1`.

    Si otterrà un messaggio simile al seguente:

    ```powershell
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

    Si ricevono risposte da *myVm1*, poiché il protocollo ICMP è stato consentito in Windows Firewall nella VM *myVm1* in un passaggio precedente.

1. Chiudere la connessione Desktop remoto a *myVm2*.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non si ha più bisogno della rete e delle macchine virtuali, rimuovere il gruppo di risorse e tutte le risorse che contiene:

1. Immettere *myResourceGroup* nella casella di **ricerca** nella parte superiore del portale.

1. Selezionare **myResourceGroup** quando viene visualizzato nei risultati della ricerca.

1. Selezionare **Elimina gruppo di risorse**.

1. Immettere *myResourceGroup* in **DIGITARE IL NOME DEL GRUPPO DI RISORSE** e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva sono state create una rete virtuale predefinita e due VM. È stata effettuata la connessione a una macchina virtuale da Internet ed è stata stabilita una comunicazione privata tra le due macchine virtuali. Per altre informazioni sulle impostazioni della rete virtuale, vedere [Manage a virtual network](manage-virtual-network.md) (Gestire una rete virtuale).

Per impostazione predefinita, Azure consente comunicazioni private senza restrizioni tra le macchine virtuali. Tuttavia, permette solo connessioni al desktop remoto in ingresso alle macchine virtuali Windows da Internet. Per altre informazioni sulla configurazione dei diversi tipi di comunicazioni della rete delle macchine virtuali, vedere l'esercitazione [Filtrare il traffico di rete](tutorial-filter-network-traffic.md).