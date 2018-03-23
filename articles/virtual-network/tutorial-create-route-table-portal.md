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
ms.date: 03/05/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 4cc814e1fd3ee8979662695f9dec3dcce335dc2a
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Instradare il traffico di rete con una tabella di route usando il portale di Azure

Per impostazione predefinita, Azure instrada automaticamente il traffico tra tutte le subnet di una rete virtuale. È possibile creare le proprie route per eseguire l'override del routing predefinito di Azure. La possibilità di creare route personalizzate è utile se, ad esempio, si vuole instradare il traffico tra subnet attraverso un firewall. In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Creare una tabella di route
> * Creare una route
> * Associare una tabella di route a una subnet della rete virtuale
> * Testare il routing
> * Risolvere gli errori di routing

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="log-in-to-azure"></a>Accedere ad Azure 

Accedere al portale di Azure all'indirizzo http://portal.azure.com.

## <a name="create-a-route-table"></a>Creare una tabella di route

Per impostazione predefinita, Azure instrada il traffico tra tutte le subnet in una rete virtuale. Per altre informazioni sulle route predefinite di Azure, vedere [Route di sistema](virtual-networks-udr-overview.md). Per eseguire l'override del routing predefinito di Azure, si crea una tabella di route contenente route, quindi la si associa a una subnet della rete virtuale.

1. Selezionare **+ Crea una risorsa** nell'angolo in alto a sinistra del portale di Azure.
2. Selezionare **Rete** e quindi **Tabella di route**.
3. Selezionare la **sottoscrizione** e selezionare o immettere le informazioni seguenti e quindi selezionare **Crea**:
 
    ![Creare una tabella di route](./media/tutorial-create-route-table-portal/create-route-table.png) 

## <a name="create-a-route"></a>Creare una route

Una tabella di route può contenere nessuna o più route. 

1. Nella casella *Cerca risorse, servizi e documentazione* nella parte superiore del portale iniziare a digitare *myRouteTablePublic*. Selezionare **myRouteTablePublic** quando viene visualizzato nei risultati della ricerca.
2. In **IMPOSTAZIONI** selezionare **Route** e quindi selezionare **+ Aggiungi**, come illustrato nell'immagine seguente:

    ![Aggiungere la route](./media/tutorial-create-route-table-portal/add-route.png) 
 
4. In **Aggiungi route** selezionare o immettere le informazioni seguenti e quindi fare clic su **OK**:
    - **Nome route**: *ToPrivateSubnet*
    - **Prefisso indirizzo**: 10.0.1.0/24
    - **Tipo hop successivo**: selezionare **Appliance virtuale**.
    - **Indirizzo hop successivo**: 10.0.2.4

    La route indirizzerà tutto il traffico destinato al prefisso dell'indirizzo 10.0.1.0/24 attraverso un'appliance virtuale di rete con l'indirizzo IP 10.0.2.4. L'appliance virtuale di rete e la subnet con il prefisso dell'indirizzo specificato verranno create nei passaggi successivi. La route esegue l'override del routing predefinito di Azure, che instrada il traffico tra subnet direttamente. Ogni route specifica un tipo di hop successivo. Il tipo di hop successivo indica ad Azure come instradare il traffico. In questo esempio il tipo di hop successivo è *VirtualAppliance*. Per altre informazioni su tutti i tipi di hop successivi disponibili in Azure e quando usarli, vedere [Tipi di hop successivi](virtual-networks-udr-overview.md#custom-routes).

## <a name="associate-a-route-table-to-a-subnet"></a>Associare una route a una subnet

Prima di poter associare una tabella di route a una subnet, è necessario creare una rete virtuale e una subnet:

1. Selezionare **+ Crea una risorsa** nell'angolo in alto a sinistra del portale di Azure.
2. Selezionare **Rete** e quindi **Rete virtuale**.
3. In **Crea rete virtuale** selezionare o immettere le informazioni seguenti e quindi selezionare **Crea**:
    
    - **Nome**: *myVirtualNetwork*
    - **Spazio indirizzi**: *10.0.0.0/16*
    - **Sottoscrizione**: selezionare la propria sottoscrizione.
    - **Gruppo di risorse**: selezionare **Usa esistente** e quindi **myResourceGroup**.
    - **Località**: selezionare *Stati Uniti Orientali*
    - **Nome subnet**: *Public*
    - **Intervallo di indirizzi:** *10.0.0.0/24*

4. Nella casella **Cerca risorse, servizi e documentazione** nella parte superiore del portale iniziare a digitare *myVirtualNetwork*. Selezionare **myVirtualNetwork** quando viene visualizzato nei risultati della ricerca.
5. Aggiungere altre due subnet alla rete virtuale. In **IMPOSTAZIONI** selezionare **Subnet** e quindi selezionare **+ Subnet**, come illustrato nell'immagine seguente:

    ![Aggiungi subnet](./media/tutorial-create-route-table-portal/add-subnet.png) 

6. Selezionare o immettere le informazioni seguenti, quindi fare clic su **OK**:
    - **Nome**: Private
    - **Spazio indirizzi**: *10.0.1.0/24*

7. Completare di nuovo i passaggi 5 e 6, specificando le informazioni seguenti:
    - **Nome**: DMZ
    - **Spazio indirizzi**: *10.0.2.0/24*

È possibile associare una tabella di route a zero o più subnet. A una subnet può essere associata una o nessuna tabella di route. Il traffico in uscita da una subnet viene instradato in base alle route predefinite di Azure e alle eventuali route personalizzate che sono state aggiunte a una tabella di route associata a una subnet. Associare la tabella di route *myRouteTablePublic* alla subnet *Public*:

1. La finestra di dialogo **myVirtualNetwork - Subnet** viene visualizzata dopo avere completato il passaggio precedente. In **IMPOSTAZIONI** selezionare **Subnet** e quindi selezionare **Public**.
2. Come illustrato nell'immagine seguente, selezionare **Tabella di route**, quindi selezionare **MyRouteTablePublic**.

    ![Associare la tabella di route](./media/tutorial-create-route-table-portal/associate-route-table.png) 

3. Selezionare **Salva**.

## <a name="test-routing"></a>Testare il routing

Per testare il routing, si creerà una macchina virtuale che funge da appliance virtuale di rete attraverso la quale viene instradata la route creata in un passaggio precedente. Dopo aver creato l'appliance virtuale di rete, si distribuirà una macchina virtuale nelle subnet *Public* e *Private*. Verrà quindi instradato il traffico dalla subnet *Public* alla subnet *Private* attraverso l'appliance virtuale di rete.

### <a name="create-a-network-virtual-appliance"></a>Creare un'appliance virtuale di rete

In un passaggio precedente è stata creata una route che ha specificato un'appliance virtuale di rete come tipo di hop successivo. Una macchina virtuale che esegue un'applicazione di rete è spesso definita "appliance virtuale di rete". Negli ambienti di produzione l'appliance virtuale di rete distribuita è spesso una macchina virtuale configurata in precedenza. Sono disponibili diverse appliance virtuali di rete in [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1). In questo articolo viene creata una macchina virtuale di base.

1. Selezionare **+ Crea una risorsa** nell'angolo in alto a sinistra del portale di Azure.
2. Selezionare **Calcolo** e quindi **Windows Server 2016 Datacenter**. È possibile selezionare un sistema operativo differente, ma i passaggi rimanenti presuppongono che sia stato selezionato **Windows Server 2016 Datacenter**. 
3. Selezionare o immettere le informazioni seguenti per **Informazioni di base** e quindi scegliere **OK**:
    - **Nome**: *myVmNva*
    - **Gruppo di risorse**: selezionare **Usa esistente** e quindi *myResourceGroup*.
    - **Località**: selezionare *East US*.

    I valori immessi per **Nome utente** e **Password** verranno usati in un passaggio successivo. La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm). I valori selezionati per **Località** e **Sottoscrizione** devono corrispondere alla località e alla sottoscrizione in cui si trova la rete virtuale. Non è necessario selezionare lo stesso gruppo di risorse in cui è stata creata la rete virtuale, ma per questa esercitazione è stato selezionato lo stesso gruppo di risorse.
4. Selezionare le dimensioni della macchina virtuale in **Scegli una dimensione**.
5. Selezionare o immettere le informazioni seguenti per **Impostazioni** e quindi scegliere **OK**:
    - **Rete virtuale**: verificare che sia selezionato **myVirtualNetwork**. In caso contrario, selezionare **Rete virtuale**, quindi selezionare **myVirtualNetwork** in **Scegli rete virtuale**.
    - **Subnet**: selezionare **Subnet** e quindi selezionare **DMZ** in **Scegli subnet**.
    - **Indirizzo IP pubblico**: selezionare **Indirizzo IP pubblico** e selezionare **Nessuno** in **Scegli indirizzo IP pubblico**. A questa macchina virtuale non verranno assegnati indirizzi IP pubblici perché non verranno stabilite connessioni alla macchina da Internet.
6. In **Crea** in **Riepilogo** selezionare **Crea** per avviare la distribuzione della macchina virtuale.

La creazione della macchina virtuale richiede alcuni minuti. Prima di continuare con il passaggio successivo, attendere che Azure finisca di creare la macchina virtuale e apra una finestra di dialogo con le informazioni sulla macchina virtuale.

Quando Azure ha creato la macchina virtuale, ha creato anche un'[interfaccia di rete](virtual-network-network-interface.md) nella subnet *DMZ* e ha associato l'interfaccia di rete alla macchina virtuale. L'inoltro IP deve essere abilitato per ogni interfaccia di rete di Azure che inoltra il traffico destinato a indirizzi IP non assegnati all'interfaccia di rete.

1. Nella finestra di dialogo aperta dopo la creazione della macchina virtuale, in **IMPOSTAZIONI** selezionare **Rete** e quindi selezionare **myvmnva158** (l'interfaccia di rete creata da Azure per la macchina virtuale ha un numero diverso dopo **myvmnva**), come illustrato nell'immagine seguente:

    ![Rete delle macchine virtuali](./media/tutorial-create-route-table-portal/virtual-machine-networking.png) 

    Quando è stata creata l'appliance virtuale di rete nella subnet *DMZ*, Azure ha creato automaticamente l'interfaccia di rete, ha associato l'interfaccia di rete alla macchina virtuale e ha assegnato l'indirizzo IP privato *10.0.2.4* all'interfaccia di rete. 

2. In **IMPOSTAZIONI** selezionare **Configurazioni IP**, selezionare **Abilitato** per **Inoltro IP** e quindi selezionare **Salva**, come illustrato nell'immagine seguente:

    ![Abilitare l'inoltro IP](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png) 

### <a name="create-virtual-machines"></a>Creare macchine virtuali

Creare due macchine virtuali nella rete virtuale per poter verificare che il traffico dalla subnet *Public* venga indirizzato alla subnet *Private* attraverso l'appliance virtuale di rete in un passaggio successivo.

Completare i passaggi da 1 a 6 di [Creare un'appliance virtuale di rete](#create-a-network-virtual-appliance). Usare le stesse impostazioni nei passaggi 3 e 5, fatta eccezione per le modifiche seguenti:

|Macchina virtuale   |NOME      |Subnet      | Indirizzo IP pubblico     |
|---------         |--------- | -----------|---------              |
|Macchina virtuale 1 | myVmWeb  | Pubblico     | Accettare l'impostazione predefinita del portale |
|Macchina virtuale 2 | myVmMgmt | Privato    | Accettare l'impostazione predefinita del portale |

È possibile creare la macchina virtuale *myVmMgmt* mentre Azure crea la macchina virtuale *myVmWeb*. Prima di continuare con i passaggi successivi, attendere che Azure finisca di creare entrambe le macchine virtuali.

### <a name="route-traffic-through-a-network-virtual-appliance"></a>Instradare il traffico attraverso un'appliance virtuale di rete

1. Nella casella di *ricerca* nella parte superiore del portale iniziare a digitare *myVmMgmt*. Selezionare **myVmMgmt** quando viene visualizzato nei risultati della ricerca.
2. Creare una connessione Desktop remoto alla macchina virtuale *myVmMgmt* selezionando **Connetti**, come illustrato nell'immagine seguente:

    ![Connettersi alla macchina virtuale](./media/tutorial-create-route-table-portal/connect-to-virtual-machine.png)  

3. Per connettersi alla macchina virtuale, aprire il file con estensione rdp scaricato. Quando richiesto, selezionare **Connetti**.
4. Immettere il nome utente e la password specificati durante la creazione della macchina virtuale (potrebbe essere necessario selezionare **Altre opzioni**, quindi **Usa un account diverso** per specificare le credenziali immesse quando è stata creata la macchina virtuale) e quindi scegliere **OK**.
5. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Selezionare **Sì** per procedere con la connessione.
6. In un passaggio successivo viene usato il comando tracert.exe per testare il routing. Tracert usa il protocollo ICMP, che non viene consentito tramite Windows Firewall. Abilitare il protocollo ICMP tramite Windows Firewall immettendo il comando seguente da un prompt dei comandi:

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

    Anche se in questo articolo viene usato tracert per testare il routing, non è consigliabile consentire il protocollo ICMP tramite Windows Firewall per le distribuzioni di produzione.
7. In [Abilitare inoltro IP](#enable-ip-forwarding) è stato abilitato l'inoltro IP all'interno di Azure per l'interfaccia di rete della macchina virtuale. All'interno della macchina virtuale, il sistema operativo o un'applicazione in esecuzione all'interno della macchina virtuale deve anche poter trasferire il traffico. Quando si implementa un'appliance virtuale di rete in un ambiente di produzione, l'appliance in genere filtra, registra o esegue un'altra funzione prima di trasferire il traffico. In questo articolo, tuttavia, il sistema operativo trasferisce semplicemente tutto il traffico ricevuto. Abilitare l'inoltro IP nel sistema operativo di *myVmNva* completando i passaggi seguenti dalla macchina virtuale *myVmMgmt*:

    Creare una sessione Desktop remoto alla macchina virtuale *myVmNva* con il comando seguente da un prompt dei comandi:

    ``` 
    mstsc /v:myvmnva
    ```
    
    Per abilitare l'inoltro IP nel sistema operativo, immettere il comando seguente in PowerShell:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```
    
    Riavviare la macchina virtuale. Con questa operazione verrà anche disconnessa la sessione Desktop remoto.
8. Mentre si è ancora connessi alla macchina virtuale *myVmMgmt*, dopo il riavvio della macchina virtuale *myVmNva*, creare una sessione Desktop remoto alla macchina virtuale *myVmWeb* con il comando seguente:

    ``` 
    mstsc /v:myVmWeb
    ```
    
    Abilitare il protocollo ICMP tramite Windows Firewall immettendo il comando seguente da un prompt dei comandi:

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

9. Per testare il routing del traffico di rete alla macchina virtuale *myVmMgmt* dalla macchina virtuale *myVmWeb*, immettere il comando seguente da un prompt dei comandi:

    ```
    tracert myvmmgmt
    ```

    La risposta restituita è simile all'esempio seguente:
    
    ```
    Tracing route to myvmmgmt.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:
        
    1    <1 ms     *        1 ms  10.0.2.4
    2     1 ms     1 ms     1 ms  10.0.1.4
        
    Trace complete.
    ```
      
    È possibile notare che il primo hop è 10.0.2.4, cioè l'indirizzo IP privato dell'appliance virtuale di rete. Il secondo hop è 10.0.1.4, l'indirizzo IP della macchina virtuale *myVmMgmt*. La route aggiunta alla tabella di route *myRouteTablePublic* e associata alla subnet *Public* ha causato il routing del traffico di Azure attraverso l'appliance virtuale di rete, invece che direttamente alla subnet *Private*.
10.  Chiudere la sessione Desktop remoto alla macchina virtuale *myVmWeb*. Si rimarrà tuttavia connessi alla macchina virtuale *myVmMgmt*.
11. Per testare il routing del traffico di rete alla macchina virtuale *myVmWeb* dalla macchina virtuale *myVmMgmt*, immettere il comando seguente da un prompt dei comandi:

    ```
    tracert myvmweb
    ```

    La risposta restituita è simile all'esempio seguente:

    ```
    Tracing route to myvmweb.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:
    
    1     1 ms     1 ms     1 ms  10.0.0.4
    
    Trace complete.
    ```

    Come si può osservare, il traffico viene instradato direttamente dalla macchina virtuale *myVmMgmt* alla macchina virtuale *myVmWeb*. Per impostazione predefinita, Azure instrada il traffico direttamente tra subnet.
12. Chiudere la sessione Desktop remoto alla macchina virtuale *myVmMgmt*.

## <a name="troubleshoot-routing"></a>Risolvere gli errori di routing

Come illustrato nei passaggi precedenti, Azure applica route predefinite, di cui è possibile, facoltativamente, eseguire l'override con route personalizzate. In alcuni casi, il traffico non può essere instradato come previsto. È possibile usare la funzionalità [Hop successivo](../network-watcher/network-watcher-check-next-hop-portal.md) di Azure Network Watcher per determinare come Azure instrada il traffico tra due macchine virtuali. 

1. Nella casella di *ricerca* nella parte superiore del portale iniziare a digitare *Network Watcher*. Selezionare **Network Watcher** quando viene visualizzato tra i risultati della ricerca.
2. In **STRUMENTI DI DIAGNOSTICA DI RETE** selezionare **Hop successivo**.
3. Per testare il routing del traffico dalla macchina virtuale *myVmWeb* (10.0.0.4) alla macchina virtuale *myVmMgmt* (10.0.1.4), selezionare la sottoscrizione, immettere le informazioni visualizzate nell'immagine seguente (il nome dell'**interfaccia di rete** dell'utente è leggermente diverso) e quindi selezionare **Hop successivo**:

    ![Hop successivo](./media/tutorial-create-route-table-portal/next-hop.png)  

    L'output**Risultato** indica che l'indirizzo IP dell'hop successivo per il traffico proveniente dalla macchina virtuale *myVmWeb* alla macchina virtuale *myVmMgmt* è 10.0.2.4 (la macchina virtuale *myVmNva*), che il tipo di hop successivo è *VirtualAppliance* e che la tabella di route che determina il routing è *myRouteTablePublic*.

Le route valide per ogni interfaccia di rete sono una combinazione delle route predefinite di Azure e di qualsiasi route definita dall'utente. Per visualizzare tutte le route valide per un'interfaccia di rete in una macchina virtuale, completare i passaggi seguenti:

1. Nella casella di *ricerca* nella parte superiore del portale iniziare a digitare *myVmWeb*. Selezionare **myVmWeb** quando viene visualizzato nei risultati della ricerca.
2. In **IMPOSTAZIONI** selezionare **Rete** e quindi selezionare **myvmweb369**. L'interfaccia di rete creata da Azure per la macchina virtuale ha un numero diverso dopo **myvmweb**.
3. In **SUPPORTO E RISOLUZIONE DEI PROBLEMI** selezionare **Route valide**, come illustrato nell'immagine seguente:

    ![Route valide](./media/tutorial-create-route-table-portal/effective-routes.png) 

    Vengono visualizzate le route predefinite di Azure e la route aggiunta nella tabella di route *myRouteTablePublic*. Per altre informazioni su come Azure seleziona una route dall'elenco di route, vedere [Modalità di selezione di una route da parte di Azure](virtual-networks-udr-overview.md#how-azure-selects-a-route).

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse e tutte le risorse in esso contenute: 

1. Immettere *myResourceGroup* nella casella di **ricerca** nella parte superiore del portale. Selezionare **myResourceGroup** quando viene visualizzato nei risultati della ricerca.
2. Selezionare **Elimina gruppo di risorse**.
3. Immettere *myResourceGroup* in **DIGITARE IL NOME DEL GRUPPO DI RISORSE** e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stata creata una tabella di route per poi associarla a una subnet. È stata creata un'appliance virtuale di rete che ha instradato il traffico da una subnet pubblica a una subnet privata. Nonostante sia possibile implementare molte risorse di Azure all'interno di una rete virtuale, non è possibile implementare le risorse per alcuni servizi PaaS di Azure in una rete virtuale. È comunque possibile limitare l'accesso alle risorse di alcuni servizi PaaS di Azure al traffico da una sola subnet della rete virtuale. Passare all'esercitazione successiva per informazioni su come limitare l'accesso alla rete alle risorse PaaS di Azure.

> [!div class="nextstepaction"]
> [Limitare l'accesso alla rete alle risorse PaaS](virtual-network-service-endpoints-configure.md#azure-portal)
