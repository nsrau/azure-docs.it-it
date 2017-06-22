---
title: Creare una macchina virtuale di Azure con rete accelerata | Microsoft Docs
description: Informazioni su come creare una macchina virtuale con rete accelerata.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/10/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 17c4dc6a72328b613f31407aff8b6c9eacd70d9a
ms.openlocfilehash: 43663ed8becfa69c06699709a18623652df28ed6
ms.contentlocale: it-it
ms.lasthandoff: 05/16/2017


---
# <a name="create-a-virtual-machine-with-accelerated-networking"></a>Creare una macchina virtuale con rete accelerata

Questa esercitazione spiega come creare una macchina virtuale (VM) di Azure con rete accelerata. La funzionalità rete accelerata abilita Single Root I/O Virtualization (SR-IOV) per le VM, migliorandone le prestazioni di rete. Questo percorso a prestazioni elevate ignora l'host del percorso dati riducendo la latenza, l'instabilità e l'utilizzo della CPU e può essere usato con i carichi di lavoro di rete più gravosi nei tipi di VM supportati. L'immagine seguente illustra le comunicazioni tra due macchine virtuali (VM), con e senza rete accelerata:

![Confronto](./media/virtual-network-create-vm-accelerated-networking/image1.png)

Senza rete accelerata, tutto il traffico di rete in ingresso e in uscita dalla VM deve attraversare l'host e il commutatore virtuale. Quest'ultimo è responsabile dell'applicazione di tutti i criteri al traffico di rete, ad esempio gruppi di sicurezza di rete, elenchi di controllo di accesso, isolamento e altri servizi di rete virtualizzati. Per altre informazioni sui commutatori virtuali, vedere l'articolo [Hyper-V Network Virtualization and Virtual Switch](https://technet.microsoft.com/library/jj945275.aspx) (Virtualizzazione rete Hyper-V e commutatore virtuale).

Con rete accelerata, il traffico di rete raggiunge la scheda di interfaccia di rete della VM e quindi viene inoltrato alla VM. Il carico di tutti i criteri di rete applicati dal commutatore virtuale senza rete accelerata viene ripartito e applicato all'hardware. L'applicazione dei criteri all'hardware permette alla scheda di rete di inoltrare il traffico di rete direttamente alla macchina virtuale ignorando l'host e il commutatore virtuale, pur mantenendo tutti i criteri applicati all'host.

I vantaggi della funzionalità rete accelerata si applicano solo alla VM in cui è abilitata. Per ottenere risultati ottimali, è consigliabile abilitarla in almeno due VM connesse alla stessa Rete virtuale di Azure. Nella comunicazione tra reti virtuali o nella connessione locale questa funzionalità ha un impatto minimo sulla latenza complessiva.

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

## <a name="benefits"></a>Vantaggi
* **Latenza più bassa e più pacchetti al secondo (pps):** rimuovendo il commutatore virtuale dal percorso dati viene eliminato il tempo di attesa dei pacchetti nell'host per l'elaborazione dei criteri, aumentando così il numero di pacchetti che possono essere elaborati all'interno della macchina virtuale.
* **Instabilità ridotta:** l'elaborazione del commutatore dipende dalla quantità di criteri da applicare e dal carico di lavoro della CPU che esegue l'elaborazione. La ripartizione del carico di applicazione dei criteri nell'hardware elimina tale variabilità recapitando i pacchetti direttamente alla macchina virtuale, rimuovendo l'host dalle comunicazioni con la macchina virtuale nonché tutte le interruzioni software e i cambi di contesto.
* **Utilizzo ridotto della CPU:** ignorando il commutatore virtuale nell'host si ottiene un minore utilizzo della CPU per l'elaborazione del traffico di rete.

## <a name="Limitations"></a>Limitazioni
L'uso della funzionalità Rete accelerata presenta le limitazioni seguenti:

* **Creazione di un'interfaccia di rete**: la funzionalità rete accelerata può essere abilitata solo per una nuova scheda di interfaccia di rete. Non può essere abilitata per una scheda di interfaccia di rete esistente.
* **Creazione di una VM**: una scheda di interfaccia di rete con rete accelerata abilitata può essere collegata a una VM solo durante la creazione della VM. Non è possibile collegare la scheda di interfaccia di rete a una VM esistente.
* **Aree**: le VM Windows con rete accelerata sono disponibili nella maggior parte delle aree di Azure. Le VM Linux con rete accelerata sono disponibili solo in due aree: Stati Uniti centro-meridionali e Stati Uniti occidentali 2. Le aree in cui questa funzionalità è disponibile saranno espanse in futuro.
* **Sistemi operativi supportati**: Windows: Microsoft Windows Server 2012 R2 Datacenter e Windows Server 2016. Linux: Ubuntu Server 16.04 LTS con kernel 4.4.0-77 e versioni successive. Verranno aggiunte altre distribuzioni a breve.
* **Dimensione della VM**: dimensioni di istanza per scopo generico e con ottimizzazione per il calcolo con otto o più memorie centrali. Per altre informazioni, vedere gli articoli sulle dimensioni delle VM di [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). In futuro il set di dimensioni delle istanze di macchina virtuale supportate verrà ampliato.

Le modifiche apportate a queste limitazioni sono annunciate nella pagina relativa agli [aggiornamenti sulla Rete virtuale di Azure](https://azure.microsoft.com/updates/accelerated-networking-in-preview).

## <a name="create-a-windows-vm"></a>Creare un'app Windows
Per creare la VM è possibile usare il portale di Azure o Azure [PowerShell](#windows-powershell).

### <a name="windows-portal"></a>Portale

1. In un browser Internet passare al [portale](https://portal.azure.com) di Azure ed eseguire l'accesso con l'[account](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) di Azure. Se non si ha un account, è possibile registrarsi per ottenere una [versione di prova gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Nel portale fare clic su **+ Nuovo** > **Calcolo** > **Windows Server 2016 Datacenter**. 
3. Nel pannello **Windows Server 2016 Datacenter** che viene visualizzato lasciare selezionato *Resource Manager* sotto **Selezionare un modello di distribuzione** e fare clic su **Crea**.
4. Nel pannello **Informazioni di base** che viene visualizzato immettere i valori seguenti, per le altre opzioni lasciare i valori predefiniti o immettere i valori desiderati e fare clic sul pulsante **OK**:

    |Impostazione|Valore|
    |---|---|
    |Nome|MyVm|
    |Gruppo di risorse|Lasciare selezionata l'opzione **Crea nuovo** e immettere *MyResourceGroup*|
    |Località|Stati Uniti occidentali 2|

    Se non si ha familiarità con Azure, vedere altre informazioni su [gruppi di risorse](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [sottoscrizioni](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) e [posizioni](https://azure.microsoft.com/regions) (anche chiamati aree).
5. Nel pannello **Scegli una dimensione** che viene visualizzato immettere *8* nella casella **Minimum cores** (Numero minimo di memorie centrali), quindi fare clic su **Visualizza tutto**.
6. Fare clic su **DS4_V2 Standard**, quindi fare clic sul pulsante **Seleziona**.
7. Nel pannello **Impostazioni** lasciare invariate tutte le impostazioni, fare clic solo su **Abilitato** sotto **Rete accelerata** e quindi fare clic sul pulsante **OK**. **Nota**: se nei passaggi precedenti si selezionano valori per la dimensione della VM, il sistema operativo o la posizione che non sono elencati nella sezione [Limitazioni](#Limitations) di questo articolo, **Rete accelerata** non è visibile.
8. Nel pannello **Riepilogo** che viene visualizzato fare clic sul pulsante **OK**. Azure inizia a creare la VM. La creazione della VM richiede alcuni minuti.
9. Per installare il driver di rete accelerata per Windows completare i passaggi della sezione [Configurare Windows](#configure-windows) di questo articolo.

### <a name="windows-powershell"></a>PowerShell
1. Installare la versione più recente del modulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) di Azure PowerShell. Se non si ha familiarità con Azure PowerShell, vedere l'articolo [Panoramica di Azure PowerShell](/powershell/azure/get-started-azureps?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Avviare una sessione di PowerShell facendo clic sul pulsante Start, digitando **powershell** e facendo clic su **PowerShell** fra i risultati della ricerca.
3. Nella finestra di PowerShell immettere il comando `login-azurermaccount` per eseguire l'accesso con l'[account](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) di Azure. Se non si ha un account, è possibile registrarsi per ottenere una [versione di prova gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
4. Nel browser copiare lo script seguente:
    ```powershell
    $RgName="MyResourceGroup"
    $Location="westus2"
    
    # Create a resource group
    New-AzureRmResourceGroup `
      -Name $RgName `
      -Location $Location
    
    # Create a subnet
    $Subnet = New-AzureRmVirtualNetworkSubnetConfig `
      -Name MySubnet `
      -AddressPrefix 10.0.0.0/24
    
    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName $RgName `
      -Location $Location `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet

    # Create a public IP address
    $Pip = New-AzureRmPublicIpAddress `
      -Name MyPublicIp `
      -ResourceGroupName $RgName `
      -Location $Location `
      -AllocationMethod Static

    # Create a virtual network interface and associate the public IP address to it
    $Nic = New-AzureRmNetworkInterface `
      -Name MyNic `
      -ResourceGroupName $RgName `
      -Location $Location `
      -SubnetId $Vnet.Subnets[0].Id `
      -PublicIpAddressId $Pip.Id `
      -EnableAcceleratedNetworking
     
    # Define a credential object for the VM. PowerShell prompts you for a username and password.
    $Cred = Get-Credential

    # Create a virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
      -VMName MyVM -VMSize Standard_DS4_v2 | `
      Set-AzureRmVMOperatingSystem `
      -Windows `
      -ComputerName myVM `
      -Credential $Cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface -Id $Nic.Id 

    # Create the virtual machine.    
    New-AzureRmVM `
      -ResourceGroupName $RgName `
      -Location $Location `
      -VM $VmConfig
    #
    ```
5. Nella finestra di PowerShell fare clic con il pulsante destro del mouse per incollare lo script e avviarne l'esecuzione. Vengono richiesti un nome utente e una password. Usare queste credenziali per accedere alla VM quando la si connette nel passaggio successivo. Se lo script non riesce e i valori dello script sono stati modificati prima dell'esecuzione, verificare che i valori utilizzati per la dimensione della VM, il sistema operativo e la posizione siano elencati nella sezione [Limitazioni](#Limitations) di questo articolo.
6. Per installare il driver di rete accelerata per Windows completare i passaggi della sezione [Configurare Windows](#configure-windows) di questo articolo.

### <a name="configure-windows"></a>Configurare Windows
Dopo aver creato la VM in Azure, è necessario installare il driver di rete accelerata per Windows. Prima di completare i passaggi seguenti occorre avere creato una VM Windows con rete accelerata mediante i passaggi relativi al [portale](#windows-portal) o a [PowerShell](#windows-powershell) indicati in questo articolo. 

1. In un browser Internet passare al [portale](https://portal.azure.com) di Azure ed eseguire l'accesso con l'[account](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) di Azure. Se non si ha un account, è possibile registrarsi per ottenere una [versione di prova gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Nella finestra che contiene il testo *Cerca risorse*, nella parte superiore del portale di Azure, digitare *MyVm*. Fare clic su **MyVm** quando viene visualizzato nei risultati della ricerca.
3. Nel pannello **MyVm** che viene visualizzato fare clic sul pulsante **Connetti** nell'angolo superiore sinistro. **Nota**: se **Creazione in corso** è visibile sotto il pulsante **Connetti**, Azure non ha ancora terminato la creazione della VM. Fare clic su **Connetti** solo dopo che **Creazione in corso** non è più visibile sotto il pulsante **Connetti**.
4. Consentire al browser di scaricare il file **MyVm.rdp**.  Dopo averlo scaricato, fare clic sul file per aprirlo. 
5. Fare clic sul pulsante **Connetti** nella finestra di dialogo **Connessione Desktop remoto** che viene visualizzata e riporta l'avviso che non è possibile identificare l'autore della connessione remota.
6. Nella finestra di dialogo  **Protezione di Windows**  che viene visualizzata fare clic su **Altre scelte** e quindi fare clic su **Usa un account diverso**. Immettere il nome utente e la password inseriti al passaggio 4 e fare clic sul pulsante **OK**.
7. Fare clic sul pulsante **Sì** nella finestra di dialogo Connessione desktop remoto che viene visualizzata e riporta l'avviso che l'identità del computer remoto non può essere verificata.
8. Nella VM MyVm fare clic sul pulsante Start di Windows e fare clic su **Internet Explorer**.
9. Nella finestra di dialogo **Internet Explorer 11** che viene visualizzata fare clic su **Usa impostazioni di sicurezza, privacy e compatibilità consigliate** e fare clic su **OK**.
10. Nella barra degli indirizzi di Internet Explorer immettere https://gallery.technet.microsoft.com/Azure-Accelerated-471b5d84 e premere INVIO.
11. Nella finestra di dialogo **Avviso di protezione** che viene visualizzata fare clic su **OK**.
12. Nella finestra di dialogo **Internet Explorer** che vene visualizzata fare clic su **Aggiungi**, fare clic sul pulsante **Aggiungi** nella casella **Siti attendibili** e fare clic sul pulsante **Chiudi**. Completare questi passaggi per le successive finestre visualizzate.
13. Per scaricare il file fare clic su esso.
14. Quando viene visualizzata la finestra di dialogo **Condizioni di licenza** fare clic su **Accetto**.
15. Consentire a Internet Explorer di salvare il file facendo clic su **Salva** nella finestra di dialogo visualizzata nella parte inferiore della schermata, quindi fare clic sul pulsante **Apri cartella**.
16. Per installare il driver di rete accelerata, fare doppio clic sul file. Durante l'installazione guidata accettare tutte le impostazioni predefinite e fare clic sul pulsante **Sì** alla fine della procedura guidata per riavviare la VM.
17. Dopo il riavvio della VM, completare i passaggi da 9 a 12 per connettersi alla VM stessa.
18. Fare clic con il pulsante destro del mouse sul pulsante Start di Windows e scegliere **Gestione dispositivi**. Espandere il nodo **Schede di rete**. Verificare che sia visualizzata la voce **Scheda Ethernet VF Mellanox ConnectX-3**, come illustra la figura seguente:
   
    ![Gestione dispositivi](./media/virtual-network-create-vm-accelerated-networking/image2.png)

## <a name="create-a-linux-vm"></a>Creare una macchina virtuale Linux
Per creare la VM è possibile usare il portale di Azure o Azure [PowerShell](#linux-powershell).

### <a name="linux-portal"></a>Portale
1. Registrarsi per l'anteprima della rete accelerata per Linux completando i passaggi 1-5 della sezione [Creare una VM Linux - PowerShell](#linux-powershell) di questo articolo.  Non è possibile registrarsi per l'anteprima nel portale.
2. Completare i passaggi 1-8 della sezione [Creare una VM Windows - portale](#windows-portal) di questo articolo. Nel passaggio 2 fare clic su **Ubuntu Server 16.04 LTS** anziché su **Windows Server 2016 Datacenter**. Per questa esercitazione scegliere di usare una password anziché una chiave SSH, anche se per le distribuzioni di produzione è possibile usare entrambe. Se l'opzione **Rete accelerata** non è visualizzata quando si completa il passaggio 7 della sezione [Creare una VM Windows - portale](#windows-portal) di questo articolo, è probabile che il motivo sia uno dei seguenti:
    - Non si è ancora registrati per l'anteprima. Verificare che lo stato della registrazione sia **Registered**, come illustrato nel passaggio 4 della sezione [Creare una VM Linux - PowerShell](#linux-powershell) di questo articolo. **Nota**: se si è partecipato all'anteprima della rete accelerata per VM Windows (non è più necessario registrarsi per l'uso della rete accelerata per VM Windows), non si viene automaticamente registrati anche per l'anteprima della rete accelerata per VM Linux. Per partecipare all'anteprima della rete accelerata per VM Linux è necessario registrarsi per tale anteprima.
    - Non è stata selezionata una dimensione di VM, un sistema operativo o una posizione nella sezione [Limitazioni](#simitations) di questo articolo.
3. Per installare il driver di rete accelerata per Linux completare i passaggi della sezione [Configurare Linux](#configure-linux) di questo articolo.

### <a name="linux-powershell"></a>PowerShell

>[!WARNING]
>Se si creano VM Linux con rete accelerata in una sottoscrizione e quindi si tenta di creare una VM Windows con rete accelerata nella stessa sottoscrizione, la creazione della VM Windows potrebbe non riuscire. Durante questa anteprima è consigliabile testare le VM Windows e Linux con rete accelerata in sottoscrizioni distinte.
>

1. Installare la versione più recente del modulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) di Azure PowerShell. Se non si ha familiarità con Azure PowerShell, vedere l'articolo [Panoramica di Azure PowerShell](/powershell/azure/get-started-azureps?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Avviare una sessione di PowerShell facendo clic sul pulsante Start, digitando **powershell** e facendo clic su **PowerShell** fra i risultati della ricerca.
3. Nella finestra di PowerShell immettere il comando `login-azurermaccount` per eseguire l'accesso con l'[account](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) di Azure. Se non si ha un account, è possibile registrarsi per ottenere una [versione di prova gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
4. Registrarsi per l'anteprima della rete accelerata per Azure completando i passaggi seguenti:
    - Immettere i comandi seguenti:

        ```powershell
        Register-AzureRmProviderFeature -FeatureName AllowAcceleratedNetworkingFeature -ProviderNamespace Microsoft.Network
        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
        ```
    - Inviare un messaggio di posta elettronica a [ axnpreview@microsoft.com ](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) con l'ID sottoscrizione di Azure e l'utilizzo previsto. 
    - Immettere il comando seguente per verificare di essere registrati per l'anteprima:
    
        `Get-AzureRmProviderFeature -FeatureName AllowAcceleratedNetworkingFeature -ProviderNamespace Microsoft.Network`

        Non proseguire con il passaggio 5 fino a quando nell'output appare **Registered** dopo aver immesso il comando precedente. L'output deve essere simile al seguente prima di continuare:
    
        ```
        FeatureName                       ProviderName      RegistrationState
        -----------                       ------------      -----------------
        AllowAcceleratedNetworkingFeature Microsoft.Network Registered
        ```
      >[!NOTE]
      >Se si è partecipato all'anteprima della rete accelerata per VM Windows (non è più necessario registrarsi per l'uso della rete accelerata per VM Windows), non si viene automaticamente registrati anche per l'anteprima della rete accelerata per VM Linux. Per partecipare all'anteprima della rete accelerata per VM Linux è necessario registrarsi per tale anteprima.
      >
5. Nel browser copiare lo script seguente:
    ```powershell
    $RgName="MyResourceGroup"
    $Location="westus2"
    
    # Create a resource group
    New-AzureRmResourceGroup `
      -Name $RgName `
      -Location $Location
    
    # Create a subnet
    $Subnet = New-AzureRmVirtualNetworkSubnetConfig `
      -Name MySubnet `
      -AddressPrefix 10.0.0.0/24
    
    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName $RgName `
      -Location $Location `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet

    # Create a public IP address
    $Pip = New-AzureRmPublicIpAddress `
      -Name MyPublicIp `
      -ResourceGroupName $RgName `
      -Location $Location `
      -AllocationMethod Static

    # Create a virtual network interface and associate the public IP address to it
    $Nic = New-AzureRmNetworkInterface `
      -Name MyNic `
      -ResourceGroupName $RgName `
      -Location $Location `
      -SubnetId $Vnet.Subnets[0].Id `
      -PublicIpAddressId $Pip.Id `
      -EnableAcceleratedNetworking
     
    # Define a credential object for the VM. PowerShell prompts you for a username and password.
    $Cred = Get-Credential

    # Create a virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
      -VMName MyVM -VMSize Standard_DS4_v2 | `
      Set-AzureRmVMOperatingSystem `
      -Linux `
      -ComputerName myVM `
      -Credential $Cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName Canonical `
      -Offer UbuntuServer `
      -Skus 16.04-LTS `
      -Version latest | `
    Add-AzureRmVMNetworkInterface -Id $Nic.Id 

    # Create the virtual machine.    
    New-AzureRmVM `
      -ResourceGroupName $RgName `
      -Location $Location `
      -VM $VmConfig
    #
    ```
6. Nella finestra di PowerShell fare clic con il pulsante destro del mouse per incollare lo script e avviarne l'esecuzione. Vengono richiesti un nome utente e una password. Usare queste credenziali per accedere alla VM quando la si connette nel passaggio successivo. Se lo script non riesce, verificare quanto segue:
    - Di essere registrati per l'anteprima, come descritto al passaggio 4
    - Se sono stati modificati i valori di dimensione della VM, tipo di sistema operativo o posizione nello script prima di eseguirlo, che i valori siano elencati nella sezione [Limitazioni](#Limitations) di questo articolo.
7. Per installare il driver di rete accelerata per Linux completare i passaggi della sezione [Configurare Linux](#configure-linux) di questo articolo.

### <a name="configure-linux"></a>Configurare Linux

Dopo aver creato la VM in Azure, è necessario installare il driver di rete accelerata per Linux. Prima di completare i passaggi seguenti è necessario avere creato una VM Linux con rete accelerata mediante i passaggi relativi al [portale](#linux-portal) o a [PowerShell](#linux-powershell) indicati in questo articolo. 

1. In un browser Internet passare al [portale](https://portal.azure.com) di Azure ed eseguire l'accesso con l'[account](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) di Azure. Se non si ha un account, è possibile registrarsi per ottenere una [versione di prova gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Nella parte superiore del portale, a destra della barra *Cerca risorse*, fare clic sull'icona **> _** per avviare una cloud shell Bash (anteprima). Il riquadro della cloud shell Bash viene visualizzato nella parte inferiore del portale e dopo alcuni secondi mostra il prompt **username@Azure:~$**. Anche se è possibile eseguire SSH alla VM dal computer, anziché dalla cloud shell, le istruzioni di questa esercitazione presuppongono che si usi la cloud shell.
3. Nella parte superiore del portale, nella finestra che contiene il testo *Cerca risorse*, digitare *MyVm*. Fare clic su **MyVm** quando viene visualizzato nei risultati della ricerca.
4. Nel pannello **MyVm** che viene visualizzato fare clic sul pulsante **Connetti** nell'angolo superiore sinistro. **Nota**: se **Creazione in corso** è visibile sotto il pulsante **Connetti**, Azure non ha ancora terminato la creazione della VM. Fare clic su **Connetti** solo dopo che **Creazione in corso** non è più visibile sotto il pulsante **Connetti**.
5. Azure apre una finestra di dialogo che richiede di immettere il `ssh adminuser@<ipaddress>`. Immettere il seguente comando nella cloud shell (o copiarlo nella finestra del passaggio 4 e incollarlo nella cloud shell) e premere INVIO.
6. Scegliere **Sì** come risposta alla domanda se si desidera continuare a connettersi, quindi premere INVIO.
7. Immettere la password specificata durante la creazione della VM. Dopo aver effettuato l'accesso alla VM, viene visualizzato il prompt adminuser@MyVm:~$. Ora si è connessi alla VM tramite la sessione cloud shell. **Nota**: le sessioni cloud shell vanno in timeout dopo 10 minuti di inattività.
8. Al prompt dei comandi immettere `uname -r` e verificare che l'output corrisponda alla versione seguente: "4.4.0-77-generic".
9.    Creare un legame tra la scheda di interfaccia di rete virtuale standard e la scheda di interfaccia di rete virtuale accelerata eseguendo i comandi che seguono. Il traffico di rete usa la scheda di interfaccia di rete virtuale accelerata, più efficiente, mentre il legame assicura che il traffico di rete non venga interrotto tra determinate modifiche di configurazione. 
    - `wget https://git.kernel.org/pub/scm/linux/kernel/git/next/linux-next.git/plain/tools/hv/bondvf.sh`
    - `chmod +x ./bondvf.sh`
    - `sudo ./bondvf.sh`
    - `sudo mv ~/bondvf.sh /etc/init.d`
    - `sudo update-rc.d bondvf.sh defaults`Nota: se si riceve un errore che dice *insserv: warning: script 'bondvf.sh' missing LSB tags and overrides*, è possibile ignorarlo.
    - `sudo nano /etc/network/interfaces.d/50-cloud-init.cfg` Apre il nano editor GNU per modificare il file.
    - Nell'editor trasformare in commenti le righe *auto etho0* e *iface eth0 inet dhcp* aggiungendo *#* all'inizio di ciascuna. Dopo l'aggiunta di  *#*  a ogni riga, le righe saranno simili all'esempio seguente:
        - #<a name="auto-eth0"></a>auto eth0
        - #<a name="iface-eth0-inet-dhcp"></a>iface eth0 inet dhcp
10. Tenere premuti i tasti **CTRL + X**, immettere **Y** e premere **INVIO** per salvare il file.
11. Riavviare la VM immettendo il comando `sudo shutdown -r now`.
12. Dopo il riavvio della VM riconnetterla completando di nuovo i passaggi 5-7.
13.    Eseguire il comando `ifconfig`, verificare che sia comparso bond0 e che per l'interfaccia ci sia l'indicazione UP. **Nota**: l'applicazione che usa la rete accelerata deve comunicare attraverso il legame *bond0* e non *eth0*.  Il nome di interfaccia può cambiare prima che la rete accelerata raggiunga la disponibilità generale.

