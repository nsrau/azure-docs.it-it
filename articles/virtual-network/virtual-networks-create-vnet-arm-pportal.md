---
title: Creare una rete virtuale di Azure con più subnet - Portale | Microsoft Docs
description: Informazioni su come creare una rete virtuale con più subnet usando il portale di Azure.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 898bdef779282d7312c76696f744b97ec2dfcded
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2018
---
# <a name="create-a-virtual-network-with-multiple-subnets-using-the-azure-portal"></a>Creare una rete virtuale con più subnet usando il portale di Azure

Una rete virtuale consente a vari tipi di risorse di Azure di comunicare con Internet e privatamente tra loro. La creazione di più subnet in una rete virtuale consente di segmentare la rete in modo da filtrare o controllare il flusso del traffico tra le subnet. In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Crea rete virtuale
> * Creare una subnet
> * Testare la comunicazione di rete tra le macchine virtuali

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="log-in-to-azure"></a>Accedere ad Azure 

Accedere al portale di Azure all'indirizzo http://portal.azure.com.

## <a name="create-a-virtual-network"></a>Crea rete virtuale

1. Selezionare **+ Crea una risorsa** nell'angolo in alto a sinistra del portale di Azure.
2. Selezionare **Rete** e quindi **Rete virtuale**.
3. Come illustrato nell'immagine seguente, immettere *myVirtualNetwork* come **Nome**, *10.0.0.0/16* come **Spazio di indirizzi** **myResourceGroup** come **Gruppo di risorse**, *Public* come **Nome** della subnet, 10.0.0.0/24 come **Intervallo indirizzi** della subnet, selezionare un valore per **Posizione** e la propria **Sottoscrizione**, accettare le impostazioni predefinite rimanenti e quindi selezionare **Crea**:

    ![Crea rete virtuale](./media/virtual-networks-create-vnet-arm-pportal/create-virtual-network.png)

    Lo **Spazio indirizzi** e l'**Intervallo indirizzi** sono specificati nella notazione CIDR. Lo **Spazio indirizzi** specificato include gli indirizzi IP 10.0.0.0-10.0.255.254. L'**Intervallo indirizzi** specificato per una subnet deve essere compreso nello **Spazio indirizzi** definito per la rete virtuale. Il server DHCP di Azure assegna gli indirizzi IP da un intervallo di indirizzi della subnet alle risorse distribuite in una subnet. Azure assegna solo gli indirizzi 10.0.0.4-10.0.0.254 alle risorse distribuite all'interno della subnet **Public**, poiché Azure riserva i primi quattro indirizzi (10.0.0.0-10.0.0.3 per la subnet, in questo esempio) e l'ultimo indirizzo (10.0.0.255 per la subnet, in questo esempio) in ogni subnet.

## <a name="create-a-subnet"></a>Creare una subnet

1. Nella casella **Cerca risorse, servizi e documentazione** nella parte superiore del portale iniziare a digitare *myVirtualNetwork*. Selezionare **myVirtualNetwork** quando viene visualizzato nei risultati della ricerca.
2. Selezionare **Subnet** e quindi selezionare **+ Subnet**, come illustrato nell'immagine seguente:

     ![Aggiungere una subnet](./media/virtual-networks-create-vnet-arm-pportal/add-subnet.png)
     
3. Nella casella **Aggiungi subnet** che viene visualizzata immettere *Private* come **Nome**, immettere *10.0.1.0/24* come **Intervallo indirizzi** e quindi scegliere **OK**.  Un intervallo di indirizzi della subnet non può sovrapporsi agli intervalli di indirizzi di altre subnet all'interno di una rete virtuale. 

Prima di distribuire reti virtuali e subnet di Azure per la produzione, è consigliabile acquisire familiarità con le [considerazioni](manage-virtual-network.md#create-a-virtual-network) relative allo spazio degli indirizzi e i [limiti della rete virtuale](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Dopo che le risorse sono state distribuite nelle subnet, alcune modifiche a livello di rete virtuale e di subnet, come quelle degli intervalli di indirizzi, possono richiedere la ridistribuzione delle risorse di Azure esistenti distribuite all'interno delle subnet.

## <a name="test-network-communication"></a>Testare la comunicazione di rete

Una rete virtuale consente a vari tipi di risorse di Azure di comunicare con Internet e privatamente tra loro. Uno dei tipi di risorsa che è possibile distribuire in una rete virtuale è una macchina virtuale. Creare due macchine virtuali nella rete virtuale allo scopo di testare la comunicazione di rete tra di esse e con Internet in un passaggio successivo.

### <a name="create-virtual-machines"></a>Creare macchine virtuali

1. Selezionare **+ Crea una risorsa** nell'angolo in alto a sinistra del portale di Azure.
2. Selezionare **Calcolo** e quindi **Windows Server 2016 Datacenter**. È possibile selezionare un sistema operativo differente, ma i passaggi rimanenti presuppongono che sia stato selezionato **Windows Server 2016 Datacenter**. 
3. Selezionare o immettere le informazioni seguenti per **Informazioni di base** e quindi scegliere **OK**:
    - **Nome**: *myVmWeb*
    - **Gruppo di risorse**: selezionare **Usa esistente** e quindi *myResourceGroup*.
    - **Località**: selezionare *East US*.

    I valori immessi per **Nome utente** e **Password** verranno usati in un passaggio successivo. La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm). I valori selezionati per **Località** e **Sottoscrizione** devono corrispondere alla località e alla sottoscrizione in cui si trova la rete virtuale. Non è necessario selezionare lo stesso gruppo di risorse in cui è stata creata la rete virtuale, ma per questa esercitazione è stato selezionato lo stesso gruppo di risorse.
4. Selezionare le dimensioni della macchina virtuale in **Scegli una dimensione**.
5. Selezionare o immettere le informazioni seguenti per **Impostazioni** e quindi scegliere **OK**:
    - **Rete virtuale**: verificare che sia selezionato **myVirtualNetwork**. In caso contrario, selezionare **Rete virtuale** e quindi selezionare **myVirtualNetwork** in **Scegli rete virtuale**.
    - **Subnet**: verificare che sia selezionato **Public**. In caso contrario, selezionare **Subnet** e quindi selezionare **Public** in **Scegli subnet**, come illustrato nell'immagine seguente:
    
        ![Impostazioni della macchina virtuale](./media/virtual-networks-create-vnet-arm-pportal/virtual-machine-settings.png)
 
6. In **Crea** in **Riepilogo** selezionare **Crea** per avviare la distribuzione della macchina virtuale.
7. Completare nuovamente i passaggi da 1 a 6, immettendo però *myVmMgmt* come **Nome** della macchina virtuale e selezionando **Private** come **Subnet**.

La creazione delle macchine virtuali richiede alcuni minuti. Non procedere con i passaggi rimanenti finché non vengono create entrambe le macchine virtuali.

Le macchine virtuali create in questo articolo hanno un'[interfaccia di rete](virtual-network-network-interface.md) con un indirizzo IP assegnato dinamicamente a tale interfaccia. Dopo avere distribuito la macchina virtuale, è possibile [aggiungere più indirizzi IP pubblici e privati o modificare il metodo di assegnazione degli indirizzi IP impostandolo su statico](virtual-network-network-interface-addresses.md#add-ip-addresses). È possibile [aggiungere interfacce di rete](virtual-network-network-interface-vm.md#vm-add-nic) fino al limite supportato dalle [dimensioni della macchina virtuale](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) selezionate al momento della creazione della macchina virtuale. È anche possibile [abilitare Single Root I/O Virtualization (SR-IOV)](create-vm-accelerated-networking-powershell.md) per una macchina virtuale, ma solo quando si crea una macchina virtuale con dimensioni in grado di supportare questa funzionalità.

### <a name="communicate-between-virtual-machines-and-with-the-internet"></a>Comunicare tra macchine virtuali e con Internet

1. Nella casella di *ricerca* nella parte superiore del portale iniziare a digitare *myVmMgmt*. Selezionare **myVmMgmt** quando viene visualizzato nei risultati della ricerca.
2. Creare una connessione Desktop remoto alla macchina virtuale *myVmMgmt* selezionando **Connetti**, come illustrato nell'immagine seguente:

    ![Connettersi alla macchina virtuale](./media/virtual-networks-create-vnet-arm-pportal/connect-to-virtual-machine.png)  

3. Per connettersi alla macchina virtuale, aprire il file con estensione rdp scaricato. Quando richiesto, selezionare **Connetti**.
4. Immettere il nome utente e la password specificati durante la creazione della macchina virtuale (potrebbe essere necessario selezionare **Altre opzioni**, quindi **Usa un account diverso** per specificare le credenziali immesse quando è stata creata la macchina virtuale) e quindi scegliere **OK**.
5. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Selezionare **Sì** per procedere con la connessione.
6. In un passaggio successivo viene usato ping per comunicare con la macchina virtuale *myVmMgmt* dalla macchina virtuale *myVmWeb*. Per impostazione predefinita, ping usa il protocollo ICMP, che non viene consentito tramite Windows Firewall. Abilitare il protocollo ICMP tramite Windows Firewall immettendo il comando seguente da un prompt dei comandi:

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

    Anche se in questo articolo viene usato ping, non è consigliabile consentire il protocollo ICMP tramite Windows Firewall per le distribuzioni di produzione.
7. Per motivi di sicurezza è comune limitare il numero di macchine virtuali a cui è possibile eseguire la connessione in remoto in una rete virtuale. In questa esercitazione viene usata la macchina virtuale *myVmMgmt* per gestire la macchina virtuale *myVmWeb* nella rete virtuale. Per eseguire una connessione Desktop remoto alla macchina virtuale *myVmWeb* dalla macchina virtuale *myVmMgmt*, immettere il comando seguente da un prompt dei comandi:

    ``` 
    mstsc /v:myVmWeb
    ```
8. Per comunicare con la macchina virtuale *myVmMgmt* dalla macchina virtuale *myVmWeb*, immettere il comando seguente da un prompt dei comandi:

    ```
    ping myvmmgmt
    ```

    L'output è simile all'output di esempio seguente:
    
    ```
    Pinging myvmmgmt.dar5p44cif3ulfq00wxznl3i3f.bx.internal.cloudapp.net [10.0.1.4] with 32 bytes of data:
    Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    
    Ping statistics for 10.0.1.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 0ms, Average = 0ms
    ```
      
    Si noti che l'indirizzo della macchina virtuale *myVmMgmt* è 10.0.1.4. 10.0.1.4 è il primo indirizzo IP disponibile nell'intervallo di indirizzi della subnet *Private* in cui è stata distribuita la macchina virtuale *myVmMgmt* in un passaggio precedente.  Il nome di dominio completo della macchina virtuale è *myvmmgmt.dar5p44cif3ulfq00wxznl3i3f.bx.internal.cloudapp.net*. Anche se la parte *dar5p44cif3ulfq00wxznl3i3f* del nome di dominio è diversa per la macchina virtuale che si sta usando, le parti restanti del nome di dominio sono le stesse. 

    Per impostazione predefinita, tutte le macchine virtuali di Azure usano il servizio DNS di Azure predefinito. Tutte le macchine virtuali all'interno di una rete virtuale possono risolvere i nomi di tutte le altre macchine virtuali nella stessa rete virtuale usando il servizio DNS predefinito di Azure. Invece di usare il servizio DNS predefinito di Azure, è possibile usare il proprio server DNS o la funzionalità di dominio privato del servizio DNS di Azure. Per maggiori dettagli, vedere [Risoluzione dei nomi usando il server DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) o [Uso di DNS di Azure per i domini privati](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

9. Per installare Internet Information Services (IIS) per Windows Server nella macchina virtuale *myVmWeb*, immettere il comando seguente da una sessione di PowerShell:

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

10. Al termine dell'installazione di IIS, disconnettere la sessione Desktop remoto *myVmWeb*, rimanendo nella sessione Desktop remoto *myVmMgmt*. Aprire un Web browser e passare a http://myvmweb. Verrà visualizzata la pagina iniziale di IIS.
11. Disconnettere la sessione Desktop remoto *myVmMgmt*.
12. Trovare l'indirizzo IP pubblico della macchina virtuale *myVmWeb*. Quando in Azure è stata creata la macchina virtuale *myVmWeb*, anche una risorsa di indirizzo IP pubblico denominata *myVmWeb* è stata creata e assegnata alla macchina virtuale. Si noti che 52.170.5.92 è stato assegnato come **Indirizzo IP pubblico** alla macchina virtuale *myVmMgmt* nell'immagine al passaggio 2. Per trovare l'indirizzo IP pubblico assegnato alla macchina virtuale *myVmWeb*, cercare *myVmWeb* nella casella di ricerca del portale e quindi selezionarlo quando viene visualizzato nei risultati della ricerca.

    Anche se non è richiesto che a una macchina virtuale sia assegnato un indirizzo IP pubblico, per impostazione predefinita Azure assegna un indirizzo IP pubblico a ogni macchina virtuale creata. Per comunicare da Internet a una macchina virtuale, alla macchina virtuale deve essere assegnato un indirizzo IP pubblico. Tutte le macchine virtuali possono comunicare in uscita con Internet, indipendentemente dal fatto che un indirizzo IP pubblico sia assegnato o meno alla macchina virtuale. Per altre informazioni sulle connessioni Internet in uscita in Azure, vedere [Connessioni in uscita in Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
13. Nel proprio computer selezionare l'indirizzo IP pubblico della macchina virtuale *myVmWeb*. Il tentativo di visualizzare la pagina iniziale di IIS dal proprio computer fallisce. Il tentativo non riesce perché quando le macchine virtuali sono state distribuite, per impostazione predefinita Azure ha creato un gruppo di sicurezza di rete per ogni macchina virtuale. 

     Un gruppo di sicurezza di rete contiene regole di sicurezza che consentono o impediscono il traffico di rete in ingresso e in uscita in base alla porta e all'indirizzo IP. Il gruppo di sicurezza di rete predefinito creato da Azure consente la comunicazione su tutte le porte tra le risorse nella stessa rete virtuale. Per le macchine virtuali Windows, il gruppo di sicurezza di rete predefinito impedisce tutto il traffico in ingresso da Internet su tutte le porte, fatta eccezione per la porta TCP 3389 (RDP). Di conseguenza, per impostazione predefinita è anche possibile connettersi tramite RDP direttamente alla macchina virtuale *myVmWeb* da Internet, anche se non si vuole che la porta 3389 sia aperta per un server Web. Dal momento che il Web browser comunica sulla porta 80, la comunicazione non riesce da Internet, poiché non esiste alcuna regola nel gruppo di sicurezza di rete predefinito che consenta il traffico sulla porta 80.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse e tutte le risorse in esso contenute: 

1. Immettere *myResourceGroup* nella casella di **ricerca** nella parte superiore del portale. Selezionare **myResourceGroup** quando viene visualizzato nei risultati della ricerca.
2. Selezionare **Elimina gruppo di risorse**.
3. Immettere *myResourceGroup* in **DIGITARE IL NOME DEL GRUPPO DI RISORSE** e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato descritto come distribuire una rete virtuale con più subnet. È stato inoltre spiegato che quando si crea una macchina virtuale Windows, Azure crea un'interfaccia di rete che viene collegata alla macchina virtuale e crea un gruppo di sicurezza di rete che consente solo il traffico sulla porta 3389 da Internet. Passare all'esercitazione successiva per informazioni su come filtrare il traffico di rete verso le subnet, anziché verso singole macchine virtuali.

> [!div class="nextstepaction"]
> [Filtrare il traffico di rete verso le subnet](./virtual-networks-create-nsg-arm-pportal.md)
