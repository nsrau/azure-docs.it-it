---
title: Instradare il traffico di rete - Portale di Azure | Microsoft Docs
description: Informazioni su come instradare il traffico di rete con una tabella di route usando il portale di Azure.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 45b07c6ca86802d0cc3e773234e1122ba7bd9ea7
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Instradare il traffico di rete con una tabella di route usando il portale di Azure

Per impostazione predefinita, Azure instrada automaticamente il traffico tra tutte le subnet di una rete virtuale. È possibile creare le proprie route per eseguire l'override del routing predefinito di Azure. La possibilità di creare route personalizzate è utile se, ad esempio, si vuole indirizzare il traffico tra subnet attraverso un'appliance virtuale di rete. In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Creare una tabella di route
> * Creare una route
> * Creare una rete virtuale con più subnet
> * Associare una route a una subnet
> * Creare un'appliance virtuale di rete che indirizza il traffico
> * Distribuire le macchine virtuali in subnet diverse
> * Indirizzare il traffico da una subnet a un'altra attraverso un'appliance virtuale di rete

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="log-in-to-azure"></a>Accedere ad Azure 

Accedere al portale di Azure all'indirizzo http://portal.azure.com.

## <a name="create-a-route-table"></a>Creare una tabella di route

1. Selezionare **+ Crea una risorsa** nell'angolo in alto a sinistra del portale di Azure.
2. Selezionare **Rete** e quindi **Tabella di route**.
3. Immettere o selezionare le informazioni seguenti, accettare le impostazioni predefinite rimanenti e quindi selezionare **Crea**:

    |Impostazione|Valore|
    |---|---|
    |NOME|myRouteTablePublic|
    |Sottoscrizione| Selezionare la propria sottoscrizione.|
    |Gruppo di risorse | Selezionare **Crea nuovo** e immettere *myResourceGroup*.|
    |Località|Stati Uniti orientali|
 
    ![Creare una tabella di route](./media/tutorial-create-route-table-portal/create-route-table.png) 

## <a name="create-a-route"></a>Creare una route

1. Nella casella *Cerca risorse, servizi e documentazione* nella parte superiore del portale iniziare a digitare *myRouteTablePublic*. Selezionare **myRouteTablePublic** quando viene visualizzato nei risultati della ricerca.
2. In **IMPOSTAZIONI** selezionare **Route** e quindi selezionare **+ Aggiungi**, come illustrato nell'immagine seguente:

    ![Aggiungere la route](./media/tutorial-create-route-table-portal/add-route.png) 
 
3. In **Aggiungi route** immettere o selezionare le informazioni seguenti, accettare le impostazioni predefinite rimanenti e quindi selezionare **Crea**:

    |Impostazione|Valore|
    |---|---|
    |Nome route|ToPrivateSubnet|
    |Prefisso indirizzo| 10.0.1.0/24|
    |Tipo hop successivo | Selezionare **Appliance virtuale**.|
    |Indirizzo hop successivo| 10.0.2.4|

## <a name="associate-a-route-table-to-a-subnet"></a>Associare una route a una subnet

Per poter associare una tabella di route a una subnet, è necessario creare una rete virtuale e una subnet, in modo che si possa poi associare la tabella di route alla subnet:

1. Selezionare **+ Crea una risorsa** nell'angolo in alto a sinistra del portale di Azure.
2. Selezionare **Rete** e quindi **Rete virtuale**.
3. In **Crea rete virtuale** immettere o selezionare le informazioni seguenti, accettare le impostazioni predefinite rimanenti e quindi selezionare **Crea**:

    |Impostazione|Valore|
    |---|---|
    |NOME|myVirtualNetwork|
    |Spazio degli indirizzi| 10.0.0.0/16|
    |Sottoscrizione | Selezionare la propria sottoscrizione.|
    |Gruppo di risorse|Selezionare **Usa esistente** e quindi **myResourceGroup**.|
    |Località|Selezionare *Stati Uniti orientali*.|
    |Nome della subnet|Pubblico|
    |Intervallo di indirizzi|10.0.0.0/24|
    
4. Nella casella **Cerca risorse, servizi e documentazione** nella parte superiore del portale iniziare a digitare *myVirtualNetwork*. Selezionare **myVirtualNetwork** quando viene visualizzato nei risultati della ricerca.
5. In **IMPOSTAZIONI** selezionare **Subnet** e quindi selezionare **+ Subnet**, come illustrato nell'immagine seguente:

    ![Aggiungi subnet](./media/tutorial-create-route-table-portal/add-subnet.png) 

6. Selezionare o immettere le informazioni seguenti, quindi fare clic su **OK**:

    |Impostazione|Valore|
    |---|---|
    |NOME|Privato|
    |Spazio degli indirizzi| 10.0.1.0/24|

7. Completare di nuovo i passaggi 5 e 6, specificando le informazioni seguenti:

    |Impostazione|Valore|
    |---|---|
    |NOME|Rete perimetrale|
    |Spazio degli indirizzi| 10.0.2.0/24|

8. La finestra di dialogo **myVirtualNetwork - Subnet** viene visualizzata dopo avere completato il passaggio precedente. In **IMPOSTAZIONI** selezionare **Subnet** e quindi selezionare **Public**.
9. Come illustrato nell'immagine seguente, selezionare **Tabella di route**, selezionare **MyRouteTablePublic** e quindi scegliere **Salva**:

    ![Associare la tabella di route](./media/tutorial-create-route-table-portal/associate-route-table.png) 

## <a name="create-an-nva"></a>Creare un'appliance virtuale di rete

Un'appliance virtuale di rete è una macchina virtuale che svolge una funzione di rete, ad esempio il routing, la protezione tramite firewall o l'ottimizzazione della WAN.

1. Selezionare **+ Crea una risorsa** nell'angolo in alto a sinistra del portale di Azure.
2. Selezionare **Calcolo** e quindi **Windows Server 2016 Datacenter**. È possibile selezionare un sistema operativo differente, ma i passaggi rimanenti presuppongono che sia stato selezionato **Windows Server 2016 Datacenter**. 
3. Selezionare o immettere le informazioni seguenti per **Informazioni di base** e quindi scegliere **OK**:

    |Impostazione|Valore|
    |---|---|
    |NOME|myVmNva|
    |Nome utente|Immettere un nome utente a scelta.|
    |Password|Immettere una password a scelta. La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Gruppo di risorse| Selezionare **Usa esistente** e quindi *myResourceGroup*.|
    |Località|Selezionare **Stati Uniti orientali**.|
4. Selezionare le dimensioni della macchina virtuale in **Scegli una dimensione**.
5. Selezionare o immettere le informazioni seguenti per **Impostazioni** e quindi scegliere **OK**:

    |Impostazione|Valore|
    |---|---|
    |Rete virtuale|myVirtualNetwork - se non è selezionato, selezionare **Rete virtuale**, quindi selezionare **myVirtualNetwork** in **Scegli rete virtuale**.|
    |Subnet|Selezionare **Subnet** e quindi selezionare **DMZ** in **Scegli subnet**. |
    |Indirizzo IP pubblico| Selezionare **Indirizzo IP pubblico** e quindi selezionare **Nessuno** in **Scegli indirizzo IP pubblico**. A questa macchina virtuale non verranno assegnati indirizzi IP pubblici perché non verranno stabilite connessioni alla macchina da Internet.
6. In **Crea** in **Riepilogo** selezionare **Crea** per avviare la distribuzione della macchina virtuale.

    La creazione della VM richiede alcuni minuti. Prima di continuare con il passaggio successivo, attendere che Azure finisca di creare la macchina virtuale e apra una finestra di dialogo con le informazioni sulla macchina virtuale.

7. Nella finestra di dialogo aperta dopo la creazione della macchina virtuale, in **IMPOSTAZIONI**, selezionare **Rete** e quindi selezionare **myvmnva158** (l'interfaccia di rete creata da Azure per la macchina virtuale ha un numero diverso dopo **myvmnva**), come illustrato nell'immagine seguente:

    ![Rete VM](./media/tutorial-create-route-table-portal/virtual-machine-networking.png) 

8. Perché un'interfaccia di rete possa inoltrare il traffico ricevuto, non destinato al rispettivo indirizzo IP, occorre abilitare l'inoltro IP per l'interfaccia di rete. In **IMPOSTAZIONI** selezionare **Configurazioni IP**, selezionare **Abilitato** per **Inoltro IP** e quindi selezionare **Salva**, come illustrato nell'immagine seguente:

    ![Abilitare l'inoltro IP](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png) 

## <a name="create-virtual-machines"></a>Creare macchine virtuali

Creare due macchine virtuali nella rete virtuale per poter verificare che il traffico dalla subnet *Public* venga indirizzato alla subnet *Private* attraverso l'appliance virtuale di rete in un passaggio successivo. Completare i passaggi da 1 a 6 di [Creare un'appliance virtuale di rete](#create-a-network-virtual-appliance). Usare le stesse impostazioni nei passaggi 3 e 5, fatta eccezione per le modifiche seguenti:

|Nome macchina virtuale      |Subnet      | Indirizzo IP pubblico     |
|--------- | -----------|---------              |
| myVmPublic  | Pubblico     | Accettare l'impostazione predefinita del portale |
| myVmPrivate | Privato    | Accettare l'impostazione predefinita del portale |

È possibile creare la macchina virtuale *myVmPrivate* mentre Azure crea la macchina virtuale *myVmPublic*. Prima di continuare con i passaggi successivi, attendere che Azure finisca di creare entrambe le macchine virtuali.

## <a name="route-traffic-through-an-nva"></a>Indirizzare il traffico attraverso un'appliance virtuale di rete

1. Nella *casella di ricerca* nella parte superiore del portale iniziare a digitare *myVmPrivate*. Selezionare la macchina virtuale **myVmPrivate** quando viene visualizzata nei risultati della ricerca.
2. Creare una connessione Desktop remoto alla macchina virtuale *myVmPrivate* selezionando **Connetti**, come illustrato nell'immagine seguente:

    ![Connettersi alla macchina virtuale ](./media/tutorial-create-route-table-portal/connect-to-virtual-machine.png)  

3. Per connettersi alla macchina virtuale, aprire il file con estensione rdp scaricato. Quando richiesto, selezionare **Connetti**.
4. Immettere il nome utente e la password specificati durante la creazione della macchina virtuale (potrebbe essere necessario selezionare **Altre opzioni**, quindi **Usa un account diverso** per specificare le credenziali immesse quando è stata creata la macchina virtuale), quindi scegliere **OK**.
5. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Selezionare **Sì** per procedere con la connessione.
6. In un passaggio successivo viene usato il comando tracert.exe per testare il routing. Tracert usa il protocollo ICMP (Internet Control Message Protocol), che viene rifiutato tramite Windows Firewall. Abilitare il protocollo ICMP tramite Windows Firewall immettendo il comando seguente da PowerShell:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

    Anche se in questo articolo viene usato tracert per testare il routing, non è consigliabile consentire il protocollo ICMP tramite Windows Firewall per le distribuzioni di produzione.
7. In [Enable IP forwarding](#enable-ip-forwarding) (Abilitare l'inoltro IP) è stato abilitato l'inoltro IP all'interno di Azure per l'interfaccia di rete della macchina virtuale. All'interno della macchina virtuale è necessario che il sistema operativo o un'applicazione in esecuzione nella macchina virtuale sia anche in grado di inoltrare il traffico di rete. Abilitare l'inoltro IP nel sistema operativo della macchina virtuale *myVmNva* completando i passaggi seguenti dalla macchina virtuale *myVmPrivate*:

    Creare una sessione Desktop remoto alla macchina virtuale *myVmNva* con il comando seguente da un prompt dei comandi:

    ``` 
    mstsc /v:myvmnva
    ```
    
    Per abilitare l'inoltro IP nel sistema operativo, immettere il comando seguente in PowerShell:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```
    
    Riavviare la macchina virtuale. Con questa operazione verrà anche disconnessa la sessione Desktop remoto.
8. Mentre si è ancora connessi alla macchina virtuale *myVmPrivate*, creare una sessione Desktop remoto alla macchina virtuale *myVmPublic* con il comando seguente dopo il riavvio della macchina virtuale *myVmNva*:

    ``` 
    mstsc /v:myVmPublic
    ```
    
    Abilitare il protocollo ICMP tramite Windows Firewall immettendo il comando seguente da PowerShell:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

9. Per testare il routing del traffico di rete alla macchina virtuale *myVmPrivate* dalla macchina virtuale *myVmPublic*, immettere il comando seguente da PowerShell:

    ```
    tracert myVmPrivate
    ```

    La risposta restituita è simile all'esempio seguente:
    
    ```
    Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:
        
    1    <1 ms     *        1 ms  10.0.2.4
    2     1 ms     1 ms     1 ms  10.0.1.4
        
    Trace complete.
    ```
      
    Come si può notare, il primo hop è 10.0.2.4, cioè l'indirizzo IP privato dell'appliance virtuale di rete. Il secondo hop è 10.0.1.4, ossia l'indirizzo IP privato della macchina virtuale *myVmPrivate*. La route aggiunta alla tabella di route *myRouteTablePublic* e associata alla subnet *Public* ha causato il routing del traffico di Azure attraverso l'appliance virtuale di rete, invece che direttamente alla subnet *Private*.
10.  Chiudere la sessione Desktop remoto alla macchina virtuale *myVmPublic*. Si rimarrà tuttavia connessi alla macchina virtuale *myVmPrivate*.
11. Per testare il routing del traffico di rete alla macchina virtuale *myVmPublic* dalla macchina virtuale *myVmPrivate*, immettere il comando seguente da un prompt dei comandi:

    ```
    tracert myVmPublic
    ```

    La risposta restituita è simile all'esempio seguente:

    ```
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:
    
    1     1 ms     1 ms     1 ms  10.0.0.4
    
    Trace complete.
    ```

    Come si può osservare, il traffico viene indirizzato direttamente dalla macchina virtuale *myVmPrivate* alla macchina virtuale *myVmPublic*. Per impostazione predefinita, Azure instrada il traffico direttamente tra subnet.
12. Chiudere la sessione Desktop remoto alla macchina virtuale *myVmPrivate*.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse e tutte le risorse in esso contenute: 

1. Immettere *myResourceGroup* nella casella di **ricerca** nella parte superiore del portale. Selezionare **myResourceGroup** quando viene visualizzato nei risultati della ricerca.
2. Selezionare **Elimina gruppo di risorse**.
3. Immettere *myResourceGroup* in **DIGITARE IL NOME DEL GRUPPO DI RISORSE** e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stata creata una tabella di route per poi associarla a una subnet. È stata creata una semplice appliance virtuale di rete che ha indirizzato il traffico da una subnet pubblica a una subnet privata. Distribuire una varietà di appliance virtuali di rete preconfigurate che svolgono funzioni di rete come la protezione tramite firewall e l'ottimizzazione della WAN da [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Prima di distribuire tabelle di route per l'uso in produzione, è consigliabile familiarizzare con il [routing in Azure](virtual-networks-udr-overview.md), la [gestione delle tabelle di route](manage-route-table.md) e i [limiti di Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).


Nonostante sia possibile implementare molte risorse di Azure all'interno di una rete virtuale, non è possibile implementare le risorse per alcuni servizi PaaS di Azure in una rete virtuale. È comunque possibile limitare l'accesso alle risorse di alcuni servizi PaaS di Azure al traffico da una sola subnet della rete virtuale. Passare all'esercitazione successiva per informazioni su come limitare l'accesso alla rete alle risorse PaaS di Azure.

> [!div class="nextstepaction"]
> [Limitare l'accesso alla rete alle risorse PaaS](virtual-network-service-endpoints-configure.md#azure-portal)
