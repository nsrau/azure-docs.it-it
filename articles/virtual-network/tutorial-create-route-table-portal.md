---
title: Instradare il traffico di rete - Esercitazione - Portale di Azure
titlesuffix: Azure Virtual Network
description: In questa esercitazione si apprenderà come instradare il traffico di rete con una tabella di route usando il portale di Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: d630a41f9b83a852605ffad2a85ad6dd14bbac73
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86079650"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Esercitazione: Instradare il traffico di rete con una tabella di route usando il portale di Azure

Per impostazione predefinita, Azure instrada il traffico tra tutte le subnet di una rete virtuale. È possibile creare le proprie route per eseguire l'override del routing predefinito di Azure. Le route personalizzate sono utili quando, ad esempio, si vuole indirizzare il traffico tra subnet attraverso un'appliance virtuale di rete. In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un'appliance virtuale di rete che indirizza il traffico
> * Creare una tabella di route
> * Creare una route
> * Associare una route a una subnet
> * Distribuire le macchine virtuali in subnet diverse
> * Indirizzare il traffico da una subnet a un'altra attraverso un'appliance virtuale di rete

Questa esercitazione usa il [portale di Azure](https://portal.azure.com). È anche possibile usare l'[interfaccia della riga di comando di Azure](tutorial-create-route-table-cli.md) o [Azure PowerShell](tutorial-create-route-table-powershell.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-an-nva"></a>Creare un'appliance virtuale di rete

Le appliance virtuali di rete (appliance virtuali) sono macchine virtuali utili per le funzioni di rete, ad esempio l'ottimizzazione del routing e del firewall. Questa esercitazione presuppone che venga usato **Windows Server 2016 Datacenter**. Se si desidera, è possibile selezionare un altro sistema operativo.

1. Nel menu del [portale di Azure](https://portal.azure.com) o dalla pagina **Home** selezionare **Crea una risorsa**.

1. Scegliere **Sicurezza** > **Windows Server 2016 Datacenter**.

    ![Windows Server 2016 Datacenter, creare una macchina virtuale, portale di Azure](./media/tutorial-create-route-table-portal/vm-ws2016-datacenter.png)

1. Nella pagina **Crea una macchina virtuale** in **Informazioni di base** immettere o selezionare queste informazioni:

    | Sezione | Impostazione | Azione |
    | ------- | ------- | ----- |
    | **Dettagli del progetto** | Subscription | Scegliere la propria sottoscrizione. |
    | | Resource group | Selezionare **Crea nuovo**, immettere *myResourceGroup* e selezionare **OK**. |
    | **Dettagli istanza** | Nome macchina virtuale | Immettere *myVmNva*. |
    | | Region | Scegliere **(Stati Uniti) Stati Uniti orientali**. |
    | | Opzioni di disponibilità | Scegliere **La ridondanza dell'infrastruttura non è richiesta**. |
    | | Immagine | Scegliere **Windows Server 2016 Datacenter**. |
    | | Dimensione | Lasciare l'impostazione predefinita **DS1 Standard v2**. |
    | **Account amministratore** | Username | Immettere un nome utente a scelta. |
    | | Password | Immettere la password desiderata che deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm). |
    | | Confirm Password | Immettere di nuovo la password. |
    | **Regole porta in ingresso** | Porte in ingresso pubbliche | Selezionare **Nessuna**. |
    | **Risparmio sui costi** | Si dispone già di una licenza di Windows Server? | Selezionare **No**. |

    ![Informazioni di base, Creare una macchina virtuale, portale di Azure](./media/tutorial-create-route-table-portal/basics-create-virtual-machine.png)

    Selezionare quindi **Avanti: Dischi >** .

1. In **Dischi** selezionare le impostazioni adatte alle proprie esigenze, quindi fare clic su **Avanti: Rete >** .

1. In **Rete**:

    1. Per **Rete virtuale**, selezionare **Crea nuova**.
    
    1. Nel campo **Nome** della finestra di dialogo **Crea rete virtuale** immettere *myVirtualNetwork*.

    1. In **Spazio indirizzi** sostituire l'intervallo di indirizzi esistente con *10.0.0.0/16*.

    1. In **Subnet** selezionare l'icona **Elimina** per eliminare la subnet esistente, quindi immettere le combinazioni seguenti di **Nome subnet** e **Intervallo di indirizzi**. Una volta immesso un nome e un intervallo validi, viene visualizzata una nuova riga vuota.

        | Nome della subnet | Intervallo di indirizzi |
        | ----------- | ------------- |
        | *Pubblica* | *10.0.0.0/24* |
        | *Privata* | *10.0.1.0/24* |
        | *Rete perimetrale* | *10.0.2.0/24* |

    1. Fare clic su **OK** per chiudere la finestra di dialogo.

    1. In **Subnet** scegliere **Rete perimetrale (10.0.2.0/24)** .

    1. In **Indirizzo IP pubblico** scegliere **Nessuno** perché questa macchina virtuale non si connetterà a Internet.

    1. Selezionare **Avanti: Gestione >** .

1. In **Gestione**:

    1. In **Account di archiviazione di diagnostica** selezionare **Crea nuovo**.
    
    1. Nella finestra di dialogo **Crea account di archiviazione** immettere o selezionare queste informazioni:

        | Impostazione | valore |
        | ------- | ----- |
        | Nome | *mynvastorageaccount* |
        | Tipo di account | **Archiviazione (utilizzo generico v1)** |
        | Prestazioni | **Standard** |
        | Replica | **Archiviazione con ridondanza locale (LRS)** |
    
    1. Fare clic su **OK** per chiudere la finestra di dialogo.

    1. Selezionare **Rivedi e crea**. Si verrà reindirizzati alla pagina **Rivedi e crea** e Azure convaliderà la configurazione.

1. Quando viene visualizzato il messaggio **Convalida superata**, selezionare **Crea**.

    La creazione della VM richiede alcuni minuti. Attendere fino a quando Azure non termina la creazione della macchina virtuale. La pagina **La distribuzione è in corso** mostrerà i dettagli della distribuzione.

1. Quando la macchina virtuale è pronta, selezionare **Vai alla risorsa**.

## <a name="create-a-route-table"></a>Creare una tabella di route

1. Nel menu del [portale di Azure](https://portal.azure.com) o dalla pagina **Home** selezionare **Crea una risorsa**.

2. Nella casella di ricerca immettere *Tabella di route*. Selezionare **Tabella di route** quando viene visualizzata nei risultati della ricerca.

3. Nella pagina **Tabella di route** selezionare **Crea**.

4. In **Crea tabella di route** immettere o selezionare queste informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    | Nome | *myRouteTablePublic* |
    | Subscription | Sottoscrizione in uso |
    | Resource group | **myResourceGroup** |
    | Location | **(Stati Uniti) Stati Uniti orientali** |
    | Propagazione della route del gateway di rete virtuale | **Enabled** |

    ![Crea tabella di route, portale di Azure](./media/tutorial-create-route-table-portal/create-route-table.png)

5. Selezionare **Crea**.

## <a name="create-a-route"></a>Creare una route

1. Passare al [portale di Azure](https://portal.azure.com) per gestire la tabella di route. Cercare e selezionare **Tabelle di route**.

1. Selezionare il nome della tabella di route (**myRouteTablePublic**).

1. Scegliere **Route** > **Aggiungi**.

    ![Aggiungi route, tabella di route, portale di Azure](./media/tutorial-create-route-table-portal/add-route.png)

1. In **Aggiungi route** immettere o selezionare queste informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    | Nome route | *ToPrivateSubnet* |
    | Prefisso indirizzo | *10.0.1.0/24* (intervallo di indirizzi della subnet *privata* creata in precedenza) |
    | Tipo hop successivo | **Appliance virtuale** |
    | Indirizzo hop successivo | *10.0.2.4* (indirizzo compreso nell'intervallo di indirizzi della subnet di *rete perimetrale*) |

1. Selezionare **OK**.

## <a name="associate-a-route-table-to-a-subnet"></a>Associare una route a una subnet

1. Passare al [portale di Azure](https://portal.azure.com) per gestire la rete virtuale. Cercare e selezionare **Reti virtuali**.

1. Selezionare il nome della rete virtuale (**myVirtualNetwork**).

1. Nella barra dei menu della rete virtuale scegliere **Subnet**.

1. Nell'elenco delle subnet della rete virtuale scegliere **Pubblica**.

1. In **Tabella di route** scegliere la tabella di route creata (**myRouteTablePublic**), quindi selezionare **Salva** per associare la tabella di route alla subnet *Pubblica*.

    ![Associare la tabella di route, elenco di subnet, rete virtuale, portale di Azure](./media/tutorial-create-route-table-portal/associate-route-table.png)

## <a name="turn-on-ip-forwarding"></a>Abilitare l'inoltro IP

A questo punto, attivare l'inoltro IP per la nuova macchina virtuale appliance virtuale di rete, *myVmNva*. Quando Azure invia il traffico di rete verso *myVmNva*, se il traffico è destinato a un indirizzo IP diverso l'inoltro IP lo invierà alla posizione corretta.

1. Passare al [portale di Azure](https://portal.azure.com) per gestire la macchina virtuale. Cercare e selezionare **Macchine virtuali**.

1. Selezionare il nome della macchina virtuale (**myVmNva**).

1. Nella barra dei menu della macchina virtuale appliance virtuale di rete selezionare **Rete**.

1. Selezionare **myvmnva123**, ovvero l'interfaccia di rete creata da Azure per la macchina virtuale. Azure aggiunge alcuni numeri per garantire un nome univoco.

    ![Rete, macchina virtuale appliance virtuale di rete, portale di Azure](./media/tutorial-create-route-table-portal/virtual-machine-networking.png)

1. Nella barra dei menu dell'interfaccia di rete selezionare **Configurazioni IP**.

1. Nella pagina **Configurazioni IP** impostare **Inoltro IP** su **Abilitato** e quindi selezionare **Salva**.

    ![Abilitare l'inoltro IP, configurazioni IP, interfaccia di rete, macchina virtuale appliance virtuale di rete, portale di Azure](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png)

## <a name="create-public-and-private-virtual-machines"></a>Creare macchine virtuali pubbliche e private

Creare una macchina virtuale pubblica e una macchina virtuale privata nella rete virtuale. In un secondo momento, li si userà per verificare che Azure instradi il traffico della subnet *pubblica* a quella *privata* attraverso l'appliance virtuale di rete.

Per creare la macchina virtuale pubblica e la macchina virtuale privata, seguire la procedura illustrata in precedenza in [Creare un'appliance virtuale di rete](#create-an-nva). Non è necessario attendere il completamento della distribuzione o passare alla risorsa macchina virtuale. Verranno usate le stesse impostazioni, ad eccezione di quanto descritto di seguito.

Prima di selezionare **Crea** per creare la macchina virtuale pubblica o privata, passare alle due sottosezioni seguenti ([Macchina virtuale pubblica](#public-vm) e [Macchina virtuale privata](#private-vm)), che mostrano i valori che devono essere diversi. È possibile procedere alla sezione successiva ([Indirizzare il traffico attraverso un'appliance virtuale di rete](#route-traffic-through-an-nva)) dopo che Azure ha completato la distribuzione di entrambe le macchine virtuali.

### <a name="public-vm"></a>Macchina virtuale pubblica

| Scheda | Impostazione | valore |
| --- | ------- | ----- |
| Nozioni di base | Resource group | **myResourceGroup** |
| | Nome macchina virtuale | *myVmPublic* |
| | Porte in ingresso pubbliche | **Consenti porte selezionate** |
| | Selezionare le porte in ingresso | **RDP** |
| Rete | Rete virtuale | **myVirtualNetwork** |
| | Subnet | **Pubblica (10.0.0.0/24)** |
| | Indirizzo IP pubblico | Valore predefinito |
| Gestione | Account di archiviazione di diagnostica | **mynvastorageaccount** |

### <a name="private-vm"></a>Macchina virtuale privata

| Scheda | Impostazione | valore |
| --- | ------- | ----- |
| Nozioni di base | Resource group | **myResourceGroup** |
| | Nome macchina virtuale | *myVmPrivate* |
| | Porte in ingresso pubbliche | **Consenti porte selezionate** |
| | Selezionare le porte in ingresso | **RDP** |
| Rete | Rete virtuale | **myVirtualNetwork** |
| | Subnet | **Privata (10.0.1.0/24)** |
| | Indirizzo IP pubblico | Valore predefinito |
| Gestione | Account di archiviazione di diagnostica | **mynvastorageaccount** |

## <a name="route-traffic-through-an-nva"></a>Indirizzare il traffico attraverso un'appliance virtuale di rete

### <a name="sign-in-to-myvmprivate-over-remote-desktop"></a>Accedere a myVmPrivate tramite desktop remoto

1. Passare al [portale di Azure](https://portal.azure.com) per gestire la macchina virtuale privata. Cercare e selezionare **Macchine virtuali**.

1. Selezionare il nome della macchina virtuale privata (**myVmPrivate**).

1. Nella barra dei menu della macchina virtuale, selezionare **Connetti** per creare una connessione Desktop remoto per la macchina virtuale privata.

1. Nella pagina **Connetti tramite RDP** selezionare **Scarica file RDP**. Azure crea e scarica nel computer un file Remote Desktop Protocol con estensione *.rdp*.

1. Aprire il file con estensione *.rdp* scaricato. Quando richiesto, selezionare **Connetti**. Selezionare **Altre opzioni** > **Usa un account diverso**, quindi immettere il nome utente e la password specificati durante la creazione della macchina virtuale privata.

1. Selezionare **OK**.

1. Se viene visualizzato un avviso relativo al certificato durante il processo di accesso, selezionare **Sì** per connettersi alla macchina virtuale.

### <a name="enable-icmp-through-the-windows-firewall"></a>Abilitare ICPM in Windows Firewall

In un passaggio successivo viene usato lo strumento di tracciamento delle route per testare il routing. Il tracciamento delle route usa il protocollo ICMP (Internet Control Message Protocol), che viene rifiutato da Windows Firewall per impostazione predefinita. Abilitare ICPM in Windows Firewall.

1. Nel desktop remoto di *myVmPrivate*, aprire PowerShell.

1. Immettere questo comando:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    In questa esercitazione verrà usata la route di traccia per testare il routing. Per gli ambienti di produzione, non è consigliabile consentire il protocollo ICMP tramite Windows Firewall.

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>Attivare l'inoltro IP in myVmNva

Si è [attivato l'inoltro IP](#turn-on-ip-forwarding) per interfaccia di rete della macchina virtuale tramite Azure. Il sistema operativo della macchina virtuale deve anch'esso inoltrare il traffico di rete. Attivare l'inoltro IP per il sistema operativo della macchina virtuale *myVmNva* con questi comandi.

1. Da un prompt dei comandi nella macchina virtuale *myVmPrivate*, avviare una sessione desktop remoto con la macchina virtuale *myVmNva*:

    ```cmd
    mstsc /v:myvmnva
    ```

1. Da PowerShell nella macchina virtuale *myVmNva* immettere questo comando per attivare l'inoltro IP:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

1. Riavviare la macchina virtuale *myVmNva*: Nella barra delle applicazioni selezionare **Start** > **Arresta**, **Altro (pianificato)**  > **Continua**.

    Con questa operazione verrà anche disconnessa la sessione Desktop remoto.

1. Dopo il riavvio della macchina virtuale *myVmNva*, creare una sessione desktop remoto alla macchina virtuale *myVmPublic*. Mentre si è ancora connessi alla macchina virtuale *myVmPrivate*, aprire un prompt dei comandi ed eseguire questo comando:

    ```cmd
    mstsc /v:myVmPublic
    ```
1. Nel Desktop remoto di *myVmPublic* aprire PowerShell.

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

    Come si può notare, il primo hop è 10.0.2.4, cioè l'indirizzo IP privato dell'appliance virtuale di rete. Il secondo hop è rivolto all'indirizzo IP privato della macchina virtuale *myVmPrivate*: 10.0.1.4. In precedenza è stata aggiunta la route alla tabella di route *myRouteTablePublic* ed è stata associata alla subnet *Public* (pubblica). Di conseguenza, Azure ha invia il traffico attraverso l'appliance virtuale di rete e non direttamente alla subnet *privata*.

1. Chiudere la sessione Desktop remoto alla macchina virtuale *myVmPublic*. Si rimarrà tuttavia connessi alla macchina virtuale *myVmPrivate*.

1. Da un prompt dei comandi nella macchina virtuale *myVmPrivate*, immettere questo comando:

    ```cmd
    tracert myVmPublic
    ```

    Questo comando testa il routing del traffico di rete dalla macchina virtuale *myVmPrivate* alla macchina virtuale *myVmPublic*. La risposta restituita è simile all'esempio seguente:

    ```cmd
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

    1     1 ms     1 ms     1 ms  10.0.0.4

    Trace complete.
    ```

    Come si può osservare, Azure indirizza il traffico direttamente dalla macchina virtuale *myVmPrivate* alla macchina virtuale *myVmPublic*. Per impostazione predefinita, Azure instrada il traffico direttamente tra subnet.

1. Chiudere la sessione Desktop remoto alla macchina virtuale *myVmPrivate*.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il gruppo di risorse non è più necessario, eliminare *myResourceGroup* e tutte le risorse al suo interno:

1. Passare al [portale di Azure](https://portal.azure.com) per gestire il gruppo di risorse. Cercare e selezionare **Gruppi di risorse**.

1. Selezionare il nome del gruppo di risorse, **myResourceGroup**.

1. Selezionare **Elimina gruppo di risorse**.

1. Nella finestra di dialogo di conferma dell'eliminazione, nella casella *DIGITARE IL NOME DEL GRUPPO DI RISORSE* immettere **myResourceGroup** e quindi selezionare **Elimina**. Azure elimina *myResourceGroup* e tutte le risorse associate a tale gruppo di risorse, incluse le tabelle di route, gli account di archiviazione, le reti virtuali, le macchine virtuali, le interfacce di rete e gli indirizzi IP pubblici.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata creata una tabella di route per poi associarla a una subnet. È stata creata una semplice appliance virtuale di rete che ha indirizzato il traffico da una subnet pubblica a una subnet privata. A questo punto è possibile distribuire varie appliance virtuali di rete preconfigurate che offrono numerose funzioni utili da [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Per altre informazioni sul routing, vedere [Panoramica del routing](virtual-networks-udr-overview.md) e [Gestire una tabella di route](manage-route-table.md).

Benché sia possibile distribuire molte risorse di Azure all'interno di una rete virtuale, Azure non è in grado di distribuire le risorse per i servizi PaaS in una rete virtuale. È possibile limitare l'accesso alle risorse di alcuni servizi PaaS di Azure, sebbene la limitazione sia applicabile solo al traffico da una subnet di rete virtuale. Passare all'esercitazione successiva per informazioni su come limitare l'accesso di rete alle risorse PaaS di Azure.

> [!div class="nextstepaction"]
> [Limitare l'accesso alla rete alle risorse PaaS](tutorial-restrict-network-access-to-resources.md)

> [!NOTE] 
> I servizi di Azure comportano costi. Gestione costi di Azure consente di impostare budget e configurare avvisi per tenere sotto controllo la spesa. Analizzare, gestire e ottimizzare i costi di Azure con Gestione costi. Per altre informazioni, vedere l'argomento di [avvio rapido sull'analisi dei costi](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).