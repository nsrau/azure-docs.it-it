---
title: Instradare il traffico di rete - Esercitazione - Portale di Azure
titlesuffix: Azure Virtual Network
description: In questa esercitazione si apprenderà come instradare il traffico di rete con una tabella di route usando il portale di Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/12/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 7e7a01b7fdc1a508fa19397900f8fd4f52d49c53
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73164012"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Esercitazione: Instradare il traffico di rete con una tabella di route usando il portale di Azure

Per impostazione predefinita, Azure instrada il traffico tra tutte le subnet di una rete virtuale. È possibile creare le proprie route per eseguire l'override del routing predefinito di Azure. La possibilità di creare route personalizzate è utile se, ad esempio, si vuole indirizzare il traffico tra subnet attraverso un'appliance virtuale di rete. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una tabella di route
> * Creare una route
> * Creare una rete virtuale con più subnet
> * Associare una route a una subnet
> * Creare un'appliance virtuale di rete che indirizza il traffico
> * Distribuire le macchine virtuali in subnet diverse
> * Indirizzare il traffico da una subnet a un'altra attraverso un'appliance virtuale di rete

Se si preferisce, è possibile completare questa esercitazione usando l'[interfaccia della riga di comando di Azure](tutorial-create-route-table-cli.md) oppure [Azure PowerShell](tutorial-create-route-table-powershell.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="create-a-route-table"></a>Creare una tabella di route

1. In alto a sinistra nella schermata selezionare **Crea una risorsa** > **Rete** > **Tabella di route**.

1. In **Crea tabella di route** immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    | NOME | Immettere *myRouteTablePublic*. |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Selezionare **Crea nuovo**, immettere *myResourceGroup* e selezionare *OK*. |
    | Location | Lasciare il valore **Stati Uniti orientali** predefinito.
    | Propagazione route BGP | Lasciare il valore predefinito, **Abilitata**. |

1. Selezionare **Create** (Crea).

## <a name="create-a-route"></a>Creare una route

1. Nella barra di ricerca del portale, immettere *myRouteTablePublic*.

1. Selezionare **myRouteTablePublic** quando viene visualizzato nei risultati della ricerca.

1. Nella pagina **myRouteTablePublic**, in **Impostazioni** selezionare **Route** >  **+ Aggiungi**.

    ![Aggiungere la route](./media/tutorial-create-route-table-portal/add-route.png)

1. In **Aggiungi route** immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    | Nome route | Immettere *ToPrivateSubnet*. |
    | Prefisso indirizzo | Immettere *10.0.1.0/24*. |
    | Tipo hop successivo | Selezionare **Appliance virtuale**. |
    | Indirizzo hop successivo | Immettere *10.0.2.4*. |

1. Selezionare **OK**.

## <a name="associate-a-route-table-to-a-subnet"></a>Associare una route a una subnet

Prima di poter associare una tabella di route a una subnet, è necessario creare una rete virtuale e una subnet.

### <a name="create-a-virtual-network"></a>Crea rete virtuale

1. Nella parte superiore sinistra della schermata, selezionare **Crea una risorsa** > **Rete** > **Rete virtuale**.

1. In **Crea rete virtuale** immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    | NOME | Immettere *myVirtualNetwork*. |
    | Spazio degli indirizzi | Immettere *10.0.0.0/16*. |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Selezionare ***Seleziona esistente*** > **myResourceGroup**. |
    | Location | Lasciare il valore **Stati Uniti orientali** predefinito. |
    | Subnet - Nome | Immettere *Pubblica*. |
    | Subnet - Intervallo di indirizzi | Immettere *10.0.0.0/24*. |

1. Accettare tutte le impostazioni predefinite e selezionare **Crea**.

### <a name="add-subnets-to-the-virtual-network"></a>Aggiungere subnet alla rete virtuale

1. Nella barra di ricerca del portale, immettere *myVirtualNetwork*.

1. Selezionare **myVirtualNetwork** quando viene visualizzato nei risultati della ricerca.

1. In **myVirtualNetwork**, in **Impostazioni** selezionare **Subnet** >  **+ Subnet**.

    ![Aggiungi subnet](./media/tutorial-create-route-table-portal/add-subnet.png)

1. In **Aggiungi subnet**, immettere queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    | NOME | Immettere *Privata*. |
    | Spazio degli indirizzi | Immettere *10.0.1.0/24*. |

1. Accettare tutte le impostazioni predefinite e selezionare **OK**.

1. Selezionare nuovamente **+ Subnet**. Questa volta, immettere le informazioni seguenti:

    | Impostazione | Valore |
    | ------- | ----- |
    | NOME | Immettere *DMZ*. |
    | Spazio degli indirizzi | Immettere *10.0.2.0/24*. |

1. Come in precedenza, accettare tutte le impostazioni predefinite e selezionare **OK**.

    Azure mostra tre subnet: **Pubblica**, **Privata** e **DMZ**.

### <a name="associate-myroutetablepublic-to-your-public-subnet"></a>Associare myRouteTablePublic alla subnet pubblica

1. Selezionare **Pubblica**.

1. In **Pubblica** selezionare **Tabella di route** > **MyRouteTablePublic** > **Salva**.

    ![Associare la tabella di route](./media/tutorial-create-route-table-portal/associate-route-table.png)

## <a name="create-an-nva"></a>Creare un'appliance virtuale di rete

Le appliance virtuali di rete sono macchine virtuali con funzioni di rete come l'ottimizzazione di routing e firewall. Se si desidera, è possibile selezionare un altro sistema operativo. Questa esercitazione presuppone che venga usato **Windows Server 2016 Datacenter**.

1. In altro a sinistra nella schermata selezionare **Crea una risorsa** > **Calcolo** > **Windows Server 2016 Datacenter**.

1. In **Creare una macchina virtuale - Informazioni di base**, immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    | **DETTAGLI DEL PROGETTO** | |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Selezionare **myResourceGroup**. |
    | **DETTAGLI DELL'ISTANZA** |  |
    | Nome macchina virtuale | Immettere *myVmNva*. |
    | Region | Selezionare **Stati Uniti orientali**. |
    | Opzioni di disponibilità | Lasciare l'impostazione predefinita **Nessuna ridondanza dell'infrastruttura necessaria**. |
    | Image | Lasciare l'impostazione predefinita **Windows Server 2016 Datacenter**. |
    | Dimensione | Lasciare l'impostazione predefinita **DS1 Standard v2**. |
    | **ACCOUNT AMMINISTRATORE** |  |
    | Username | Immettere un nome utente a scelta. |
    | Password | Immettere una password a scelta. La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirm Password | Reimmettere la password. |
    | **REGOLE PORTA IN INGRESSO** |  |
    | Porte in ingresso pubbliche | Lasciare il valore predefinito **Nessuna**.
    | **RISPARMIA** |  |
    | Già in possesso di una licenza di Windows? | Lasciare il valore predefinito **No**. |

1. Selezionare **Avanti: Dischi**.

1. In **Creare una macchina virtuale - Dischi** selezionare le impostazioni adatte alle proprie esigenze.

1. Selezionare **Avanti: Rete**.

1. In **Creare una macchina virtuale - Rete** immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    | Rete virtuale | Lasciare l'impostazione predefinita **myVirtualNetwork**. |
    | Subnet | Selezionare **DMZ (10.0.2.0/24)** . |
    | IP pubblico | Selezionare **Nessuno**. Non è necessario un indirizzo IP pubblico. La macchina virtuale, infatti, non si connetterà a Internet.|

1. Lasciare i valori predefiniti di tutte le altre impostazioni e selezionare **Avanti: Gestione**.

1. In **Creare una macchina virtuale - Gestione**, per **Account di archiviazione di diagnostica**, selezionare **Crea nuovo**.

1. In **Crea account di archiviazione**, immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    | NOME | Immettere *mynvastorageaccount*. |
    | Tipo di account | Lasciare l'impostazione predefinita **Archiviazione (utilizzo generico v1)** . |
    | Prestazioni | Lasciare l'impostazione predefinita **Standard**. |
    | Replica | Lasciare l'impostazione predefinita **Archiviazione con ridondanza locale**.

1. Selezionare **OK**.

1. Selezionare **Rivedi e crea**. Si verrà reindirizzati alla pagina **Rivedi e crea** e Azure convaliderà la configurazione.

1. Quando viene visualizzato il messaggio **Convalida superata**, selezionare **Crea**.

    La creazione della VM richiede alcuni minuti. Non continuare fino a quando Azure non termina la creazione della macchina virtuale. La pagina **La distribuzione è in corso** mostrerà i dettagli della distribuzione.

1. Quando la macchina virtuale è pronta, selezionare **Vai alla risorsa**.

## <a name="turn-on-ip-forwarding"></a>Abilitare l'inoltro IP

Attivare l'inoltro IP per *myVmNva*. Quando Azure invia il traffico di rete verso *myVmNva*, se il traffico è destinato a un indirizzo IP diverso l'inoltro IP lo invierà nella posizione corretta.

1. In **myVmNva**, in **Impostazioni** selezionare **Rete**.

1. Selezionare **myvmnva123**, ovvero l'interfaccia di rete creata da Azure per la macchina virtuale. Disporrà di una stringa di numeri che la renderà univoca.

    ![Rete VM](./media/tutorial-create-route-table-portal/virtual-machine-networking.png)

1. In **Impostazioni** selezionare **Configurazioni IP**.

1. In **myvmnva123 - Configurazioni IP**, per **Inoltro IP** selezionare **Abilitato** e quindi selezionare **Salva**.

    ![Abilitare l'inoltro IP](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png)

## <a name="create-public-and-private-virtual-machines"></a>Creare macchine virtuali pubbliche e private

Creare una macchina virtuale pubblica e una macchina virtuale privata nella rete virtuale. In un secondo momento, li si userà per verificare che Azure instradi il traffico della subnet *pubblica* a quella *privata* attraverso l'appliance virtuale di rete.

Completare i passaggi da 1 a 12 di [Creare un'appliance virtuale di rete](#create-an-nva). Lasciare invariata la maggior parte delle impostazioni e modificare invece questi valori:

| Impostazione | Valore |
| ------- | ----- |
| **MACCHINA VIRTUALE PUBBLICA** | |
| INFORMAZIONI DI BASE |  |
| Nome macchina virtuale | Immettere *myVmPublic*. |
| RETE | |
| Subnet | Selezionare **Pubblica (10.0.0.0/24)** . |
| Indirizzo IP pubblico | Accettare il valore predefinito. |
| Porte in ingresso pubbliche | Selezionare **Consenti porte selezionate**. |
| Selezionare le porte in ingresso | Selezionare **HTTP** e **RDP**. |
| GESTIONE | |
| Account di archiviazione di diagnostica | Lasciare l'impostazione predefinita **mynvastorageaccount**. |
| **MACCHINA VIRTUALE PRIVATA** | |
| INFORMAZIONI DI BASE |  |
| Nome macchina virtuale | Immettere *myVmPrivate*. |
| RETE | |
| Subnet | Selezionare **Privata (10.0.1.0/24)** . |
| Indirizzo IP pubblico | Accettare il valore predefinito. |
| Porte in ingresso pubbliche | Selezionare **Consenti porte selezionate**. |
| Selezionare le porte in ingresso | Selezionare **HTTP** e **RDP**. |
| GESTIONE | |
| Account di archiviazione di diagnostica | Lasciare l'impostazione predefinita **mynvastorageaccount**. |

È possibile creare la macchina virtuale *myVmPrivate* mentre Azure crea la macchina virtuale *myVmPublic*. Prima di continuare con i passaggi rimanenti, attendere che Azure finisca di creare entrambe le macchine virtuali.

## <a name="route-traffic-through-an-nva"></a>Indirizzare il traffico attraverso un'appliance virtuale di rete

### <a name="sign-in-to-myvmprivate-over-remote-desktop"></a>Accedere a myVmPrivate tramite desktop remoto

1. Nella barra di ricerca del portale, immettere *myVmPrivate*.

1. Selezionare la macchina virtuale **myVmPrivate** quando viene visualizzata nei risultati della ricerca.

1. Selezionare **Connetti** per creare una connessione desktop remoto per la macchina virtuale *myVmPrivate*.

1. In **Connetti alla macchina virtuale in corso** selezionare **Scarica file RDP**. Azure crea e scarica nel computer un file Remote Desktop Protocol con estensione *rdp*.

1. Aprire il file con estensione *.rdp* scaricato.

    1. Quando richiesto, selezionare **Connetti**.

    1. Immettere il nome utente e la password specificati al momento della creazione della macchina virtuale privata.

    1. Potrebbe essere necessario selezionare **Altre scelte** > **Usa un account diverso** per usare le credenziali della macchina virtuale privata.

1. Selezionare **OK**.

    Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato.

1. Selezionare **Sì** per connettersi alla macchina virtuale.

### <a name="enable-icmp-through-the-windows-firewall"></a>Abilitare ICPM in Windows Firewall

In un passaggio successivo viene usato lo strumento di tracciamento delle route per testare il routing. Il tracciamento delle route usa il protocollo ICMP (Internet Control Message Protocol), che viene rifiutato da Windows Firewall per impostazione predefinita. Abilitare ICPM in Windows Firewall.

1. Nel desktop remoto di *myVmPrivate*, aprire PowerShell.

1. Immettere questo comando:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    In questa esercitazione si usa il tracciamento delle route per testare il routing. Per gli ambienti di produzione, non è consigliabile consentire il protocollo ICMP tramite Windows Firewall.

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>Attivare l'inoltro IP in myVmNva

Si è [attivato l'inoltro IP](#turn-on-ip-forwarding) per interfaccia di rete della macchina virtuale tramite Azure. Il sistema operativo della macchina virtuale deve anch'esso inoltrare il traffico di rete. Attivare l'inoltro IP per il sistema operativo della macchina virtuale *myVmNva* con questi comandi.

1. Da un prompt dei comandi nella macchina virtuale *myVmPrivate*, avviare una sessione desktop remoto con la macchina virtuale *myVmNva*:

    ```cmd
    mstsc /v:myvmnva
    ```

1. Da PowerShell in *myVmNva*, immettere questo comando per attivare l'inoltro IP:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

1. Riavviare la macchina virtuale *myVmNva*. Nella barra delle applicazioni selezionare **Pulsante Start** > **Pulsante di alimentazione**, **Altro (pianificato)**  > **Continua**.

    Con questa operazione verrà anche disconnessa la sessione desktop remoto.

1. Dopo il riavvio della macchina virtuale *myVmNva*, creare una sessione desktop remoto alla macchina virtuale *myVmPublic*. Mentre si è ancora connessi alla macchina virtuale *myVmPrivate*, aprire un prompt dei comandi ed eseguire questo comando:

    ```cmd
    mstsc /v:myVmPublic
    ```
1. Nel desktop remoto di *myVmPublic* aprire PowerShell.

1. Abilitare il protocollo ICMP tramite Windows Firewall immettendo il comando seguente:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

## <a name="test-the-routing-of-network-traffic"></a>Testare il routing del traffico di rete

In primo luogo, testare il routing del traffico di rete dalla macchina virtuale *myVmPublic* alla macchina virtuale *myVmPrivate*.

1. Da PowerShell nella macchina virtuale *myVmPublic*, immettere questo comando:

    ```powershell
    tracert myVmPrivate
    ```

    La risposta restituita è simile all'esempio seguente:

    ```powershell
    Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:

    1    <1 ms     *        1 ms  10.0.2.4
    2     1 ms     1 ms     1 ms  10.0.1.4

    Trace complete.
    ```

    È possibile visualizzare che il primo hop è 10.0.2.4. Si tratta dell'indirizzo IP privato dell'appliance virtuale di rete. Il secondo hop è rivolto all'indirizzo IP privato della macchina virtuale *myVmPrivate*: 10.0.1.4. In precedenza è stata aggiunta la route alla tabella di route *myRouteTablePublic* ed è stata associata alla subnet *Public* (pubblica). Di conseguenza, Azure ha invia il traffico attraverso l'appliance virtuale di rete e non direttamente alla subnet *privata*.

1. Chiudere la sessione Desktop remoto alla macchina virtuale *myVmPublic*. Si rimarrà tuttavia connessi alla macchina virtuale *myVmPrivate*.

1. Da un prompt dei comandi nella macchina virtuale *myVmPrivate*, immettere questo comando:

    ```cmd
    tracert myVmPublic
    ```

    Testa il routing del traffico di rete dalla macchina virtuale *myVmPrivate* alla macchina virtuale *myVmPublic*. La risposta restituita è simile all'esempio seguente:

    ```cmd
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

    1     1 ms     1 ms     1 ms  10.0.0.4

    Trace complete.
    ```

    Come si può osservare, Azure indirizza il traffico direttamente dalla macchina virtuale *myVmPrivate* alla macchina virtuale *myVmPublic*. Per impostazione predefinita, Azure instrada il traffico direttamente tra subnet.

1. Chiudere la sessione Desktop remoto alla macchina virtuale *myVmPrivate*.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse e tutte le risorse in esso contenute:

1. Nella barra di ricerca del portale immettere *myResourceGroup*.

1. Selezionare **myResourceGroup** quando viene visualizzato nei risultati della ricerca.

1. Selezionare **Elimina gruppo di risorse**.

1. Immettere *myResourceGroup* in **DIGITARE IL NOME DEL GRUPPO DI RISORSE** e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata creata una tabella di route per poi associarla a una subnet. È stata creata una semplice appliance virtuale di rete che ha indirizzato il traffico da una subnet pubblica a una subnet privata. Ora che si è appreso come eseguire questa operazione, è possibile distribuire diverse appliance virtuali di rete configurate in precedenza da [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Effettuano numerose utili funzioni di rete. Per altre informazioni sul routing, vedere [Panoramica del routing](virtual-networks-udr-overview.md) e [Gestire una tabella di route](manage-route-table.md).

Benché sia possibile distribuire molte risorse di Azure all'interno di una rete virtuale, Azure non è in grado di distribuire le risorse per i servizi PaaS in una rete virtuale. È possibile limitare l'accesso alle risorse di alcuni servizi PaaS di Azure. La restrizione deve riguardare tuttavia solo il traffico dalla subnet di una rete virtuale. Passare all'esercitazione successiva per informazioni su come limitare l'accesso di rete alle risorse PaaS di Azure.

> [!div class="nextstepaction"]
> [Limitare l'accesso alla rete alle risorse PaaS](tutorial-restrict-network-access-to-resources.md)
