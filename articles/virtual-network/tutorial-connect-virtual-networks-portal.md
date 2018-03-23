---
title: Connettere reti virtuali con il peering reti virtuali - Portale di Azure | Microsoft Docs
description: Informazioni su come connettere reti virtuali con il peering reti virtuali.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: ''
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/06/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: ac0c1033546758a77b43298a5fa8cba5f5204650
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-portal"></a>Connettere reti virtuali con il peering reti virtuali usando il portale di Azure

È possibile connettere due reti virtuali tra loro con il peering reti virtuali. Dopo che è stato eseguito il peering, le risorse delle due reti virtuali possono comunicare tra loro con la stessa larghezza di banda e la stessa latenza che sarebbero disponibili se si trovassero nella stessa rete virtuale. Questo articolo illustra la creazione e il peering di due reti virtuali. Si apprenderà come:

> [!div class="checklist"]
> * Creare due reti virtuali
> * Creare un peering tra le reti virtuali
> * Testare il peering

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="log-in-to-azure"></a>Accedere ad Azure 

Accedere al portale di Azure all'indirizzo http://portal.azure.com.

## <a name="create-virtual-networks"></a>Creare reti virtuali

1. Selezionare **+ Crea una risorsa** nell'angolo in alto a sinistra del portale di Azure.
2. Selezionare **Rete** e quindi **Rete virtuale**.
3. Come illustrato nell'immagine seguente, immettere *myVirtualNetwork1* come **Nome**, *10.0.0.0/16* come **Spazio indirizzi**, **myResourceGroup** come **Gruppo di risorse**, *Subnet1* come **Nome** della subnet e 10.0.0.0/24 come **Intervallo di indirizzi** della subnet, selezionare **Località** e **Sottoscrizione**, accettare le impostazioni predefinite rimanenti e quindi selezionare **Crea**:

    ![Crea rete virtuale](./media/tutorial-connect-virtual-networks-portal/create-virtual-network.png)

4. Ripetere i passaggi da 1 a 3, con le modifiche seguenti.
    - **Nome**: *myVirtualNetwork2*
    - **Gruppo di risorse**: selezionare **Usa esistente** e quindi **myResourceGroup**.
    - **Spazio indirizzi**: *10.1.0.0/16*
    - **Intervallo di indirizzi subnet**: *10.1.0.0/24*

    Il prefisso di indirizzo per la rete virtuale *myVirtualNetwork2* non si sovrappone allo spazio indirizzi della rete virtuale *myVirtualNetwork1*. Non è possibile eseguire il peering di reti virtuali i cui spazi indirizzi si sovrappongono.

## <a name="peer-virtual-networks"></a>Eseguire il peering delle reti virtuali

1. Nella casella di ricerca nella parte superiore del portale di Azure iniziare a digitare *MyVirtualNetwork1*. Selezionare **myVirtualNetwork1** quando viene visualizzato nei risultati della ricerca.
2. Selezionare **Peer** in **IMPOSTAZIONI** e quindi **+ Aggiungi**, come illustrato nell'immagine seguente:

    ![Creare un peering](./media/tutorial-connect-virtual-networks-portal/create-peering.png)

3. Immettere o selezionare le informazioni riportate nell'immagine seguente e quindi selezionare **OK**. Per selezionare la rete virtuale *myVirtualNetwork2*, selezionare **Rete virtuale** e quindi *myVirtualNetwork2*.

    ![Impostazioni del peering](./media/tutorial-connect-virtual-networks-portal/peering-settings.png)

    Lo stato riportato sotto **STATO PEERING** è *Avviato*, come illustrato nell'immagine seguente:

    ![Stato peering](./media/tutorial-connect-virtual-networks-portal/peering-status.png)

    Se lo stato non viene visualizzato, aggiornare il browser.

4. Cercare la rete virtuale *myVirtualNetwork2*. Quando viene restituita nei risultati della ricerca, selezionarla.
5. Ripetere i passaggi da 1 a 3, con le modifiche seguenti, quindi selezionare **OK**.
    - **Nome**: *myVirtualNetwork2-myVirtualNetwork1*
    - **Rete virtuale**: *myVirtualNetwork1*

    Lo stato riportato sotto **STATO PEERING** è *Connesso*. Azure avrà modificato anche lo stato del peering *myVirtualNetwork2-myVirtualNetwork1* da *Avviato* a *Connesso*. Il peering reti virtuali non è completamente stabilito finché lo stato del peering per entrambe le reti virtuali non è *Connesso*. 

I peering vengono stabiliti tra due reti virtuali, ma non sono transitivi. Di conseguenza, se si vuole eseguire il peering da *myVirtualNetwork2* a *myVirtualNetwork3*, ad esempio, è necessario creare un peering aggiuntivo tra le reti virtuali *myVirtualNetwork2* e *myVirtualNetwork3*. Anche se *myVirtualNetwork1* è connessa tramite peering a *myVirtualNetwork2*, le risorse in *myVirtualNetwork1* potrebbero accedere alle risorse di *myVirtualNetwork3* solo se *myVirtualNetwork1* fosse connessa tramite peering anche con *myVirtualNetwork3*. 

Prima di eseguire il peering di reti virtuali di produzione, è consigliabile acquisire familiarità con la [panoramica del peering](virtual-network-peering-overview.md), la [gestione del peering](virtual-network-manage-peering.md) e i [limiti delle reti virtuali](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Questo articolo illustra un peering tra due reti virtuali nella stessa sottoscrizione e località, ma è possibile eseguire il peering anche di reti virtuali in [aree diverse](#register) e in [sottoscrizioni di Azure diverse](create-peering-different-subscriptions.md#portal). Si possono anche creare [progettazioni di rete di tipo hub-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) con peering.

## <a name="test-peering"></a>Testare il peering

Per testare la comunicazione di rete tra macchine virtuali in reti virtuali diverse tramite un peering, distribuire una macchina virtuale in ogni subnet e quindi avviare la comunicazione tra le macchine virtuali. 

### <a name="create-virtual-machines"></a>Creare macchine virtuali

Creare una macchina virtuale in ogni rete virtuale per poter convalidare la comunicazione reciproca in un passaggio successivo.

### <a name="create-virtual-machines"></a>Creare macchine virtuali

1. Selezionare **+ Crea una risorsa** nell'angolo in alto a sinistra del portale di Azure.
2. Selezionare **Calcolo** e quindi **Windows Server 2016 Datacenter**. È possibile selezionare un sistema operativo differente, ma i passaggi rimanenti presuppongono che sia stato selezionato **Windows Server 2016 Datacenter**. 
3. Selezionare o immettere le informazioni seguenti per **Informazioni di base** e quindi scegliere **OK**:
    - **Nome**: *myVm1*
    - **Gruppo di risorse**: selezionare **Usa esistente** e quindi *myResourceGroup*.
    - **Località**: selezionare *East US*.

    I valori immessi per **Nome utente** e **Password** verranno usati in un passaggio successivo. La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm). I valori selezionati per **Località** e **Sottoscrizione** devono corrispondere alla località e alla sottoscrizione in cui si trova la rete virtuale. Non è necessario selezionare lo stesso gruppo di risorse in cui è stata creata la rete virtuale, ma per questo articolo è stato selezionato lo stesso gruppo di risorse.
4. Selezionare le dimensioni della macchina virtuale in **Scegli una dimensione**.
5. Selezionare o immettere le informazioni seguenti per **Impostazioni** e quindi scegliere **OK**:
    - **Rete virtuale**: verificare che sia selezionata **myVirtualNetwork1**. In caso contrario, selezionare **Rete virtuale** e quindi **myVirtualNetwork1** in **Scegli rete virtuale**.
    - **Subnet**: verificare che sia selezionata **Subnet1**. In caso contrario, selezionare **Subnet** e quindi **Subnet1** in **Scegli subnet**, come illustrato nell'immagine seguente:
    
        ![Impostazioni della macchina virtuale](./media/tutorial-connect-virtual-networks-portal/virtual-machine-settings.png)
 
6. In **Crea** in **Riepilogo** selezionare **Crea** per avviare la distribuzione della macchina virtuale.
7. Ripetere i passaggi da 1 a 6, immettendo però *myVm2* come **Nome** della macchina virtuale e selezionando *myVirtualNetwork2* come **Rete virtuale**.

Azure ha assegnato *10.0.0.4* come indirizzo IP privato della macchina virtuale *myVm1* e 10.1.0.4 alla macchina virtuale *myVm2* perché sono i primi indirizzi IP disponibili nella subnet *Subnet1* rispettivamente delle reti virtuali *myVirtualNetwork1* e *myVirtualNetwork2*.

La creazione delle macchine virtuali richiede alcuni minuti. Non procedere con i passaggi rimanenti finché non vengono create entrambe le macchine virtuali.

### <a name="test-virtual-machine-communication"></a>Testare la comunicazione tra le macchine virtuali

1. Nella casella *Cerca* nella parte superiore del portale iniziare a digitare *myVm1*. Selezionare **myVm1** quando viene visualizzato nei risultati della ricerca.
2. Creare una connessione Desktop remoto alla macchina virtuale *myVm1* selezionando **Connetti**, come illustrato nell'immagine seguente:

    ![Connettersi alla macchina virtuale](./media/tutorial-connect-virtual-networks-portal/connect-to-virtual-machine.png)  

3. Per connettersi alla macchina virtuale, aprire il file con estensione rdp scaricato. Quando richiesto, selezionare **Connetti**.
4. Immettere il nome utente e la password specificati durante la creazione della macchina virtuale (potrebbe essere necessario selezionare **Altre opzioni**, quindi **Usa un account diverso** per specificare le credenziali immesse quando è stata creata la macchina virtuale) e quindi scegliere **OK**.
5. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Selezionare **Sì** per procedere con la connessione.
6. In un passaggio successivo verrà usato il ping per comunicare con la macchina virtuale *myVm2* dalla macchina virtuale *myVmWeb*. Per impostazione predefinita, ping usa il protocollo ICMP, che non viene consentito tramite Windows Firewall. Abilitare il protocollo ICMP tramite Windows Firewall immettendo il comando seguente da un prompt dei comandi:

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

    Anche se in questo articolo viene usato il ping a scopo di test, non è consigliabile consentire il protocollo ICMP tramite Windows Firewall per le distribuzioni di produzione.

7. Per connettersi alla macchina virtuale *myVm2*, immettere il comando seguente al prompt dei comandi nella macchina virtuale *myVm1*:

    ```
    mstsc /v:10.1.0.4
    ```
    
8. Dato che è stato abilitato il ping in *myVm1*, è ora possibile effettuare il ping di tale macchina virtuale in base all'indirizzo IP:

    ```
    ping 10.0.0.4
    ```
    
    Si riceveranno quattro risposte. Se si effettua il ping in base al nome della macchina virtuale (*myVm1*) anziché in base all'indirizzo IP, il ping non riesce perché *myVm1* è un nome host sconosciuto. La risoluzione dei nomi predefinita di Azure funziona tra macchine virtuali all'interno della stessa rete virtuale, ma non tra macchine virtuali in reti virtuali diverse. Per risolvere i nomi tra reti virtuali, è necessario [distribuire un server DNS personalizzato](virtual-networks-name-resolution-for-vms-and-role-instances.md) oppure usare [domini privati di DNS di Azure](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

9. Disconnettere le sessioni RDP con *myVm1* e *myVm2*.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse e tutte le risorse in esso contenute: 

1. Immettere *myResourceGroup* nella casella di **ricerca** nella parte superiore del portale. Selezionare **myResourceGroup** quando viene visualizzato nei risultati della ricerca.
2. Selezionare **Elimina gruppo di risorse**.
3. Immettere *myResourceGroup* in **DIGITARE IL NOME DEL GRUPPO DI RISORSE** e selezionare **Elimina**.

**<a name="register"></a>Registrarsi per l'anteprima del peering reti virtuali globale**

Il peering reti virtuali nella stessa area è disponibile a livello generale. Il peering di reti virtuali in aree diverse è attualmente disponibile in anteprima. Vedere gli [aggiornamenti delle reti virtuali](https://azure.microsoft.com/updates/?product=virtual-network) per le aree disponibili. Per eseguire il peering di reti virtuali tra aree diverse, è prima necessario registrarsi per l'anteprima. Non si può effettuare la registrazione con il portale, ma è possibile eseguire questa operazione con [PowerShell](tutorial-connect-virtual-networks-powershell.md#register) o l'[interfaccia della riga di comando di Azure](tutorial-connect-virtual-networks-cli.md#register). Se si tenta di eseguire il peering di reti virtuali in aree diverse prima di registrarsi per questa funzionalità, il peering non riesce.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come connettere due reti virtuali con il peering reti virtuali.

Proseguire connettendo il proprio computer a una rete virtuale tramite una VPN e interagendo con le risorse in una rete virtuale o in reti virtuali associate tramite peering.

> [!div class="nextstepaction"]
> [Connettere un computer a una rete virtuale](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
