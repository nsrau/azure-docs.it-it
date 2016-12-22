---
title: "Più indirizzi IP per le macchine virtuali - Interfaccia della riga di comando di Azure | Documentazione Microsoft"
description: "Informazioni su come assegnare più indirizzi IP a una macchina virtuale usando l&quot;interfaccia della riga di comando di Azure | Resource Manager."
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: annahar
translationtype: Human Translation
ms.sourcegitcommit: bf92cdc3f6adf1bfaffbcae4e8a0257d0682e33d
ms.openlocfilehash: 0bb3e5f74cb6d013a2b14b4e4c81f0936f90004a


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-azure-cli"></a>Assegnare più indirizzi IP alle macchine virtuali usando l'interfaccia della riga di comando di Azure

> [!div class="op_single_selector"]
> * [portale di Azure](virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
> * [CLI](virtual-network-multiple-ip-addresses-cli.md)

Una macchina virtuale di Azure può essere associata a una o più interfacce di rete. A ogni scheda di interfaccia di rete possono essere assegnati uno o più indirizzi IP pubblici o privati, statici e dinamici. L'assegnazione di più indirizzi IP a una VM consente di:

* Ospitare più siti Web o servizi con indirizzi IP e certificati SSL diversi in un singolo server.
* Fungere da appliance virtuale di rete, ad esempio un firewall o un servizio di bilanciamento del carico.
* Aggiungere qualsiasi indirizzo IP per qualsiasi scheda di interfaccia di rete a un pool back-end di Azure Load Balancer. In passato, era possibile aggiungere a un pool di back-end solo gli indirizzi IP primari per la scheda di interfaccia di rete primaria. Per altre informazioni su come bilanciare il carico di più configurazioni IP, leggere l'articolo [Load balancing multiple IP configurations](../load-balancer/load-balancer-multiple-ip.md) (Bilanciamento del carico di più configurazioni IP).

Ogni scheda di interfaccia di rete collegata a una macchina virtuale dispone di una o più configurazioni IP associate. A ogni configurazione viene assegnato un indirizzo IP privato statico o dinamico. Ogni configurazione può anche avere una risorsa di indirizzo IP pubblico associata. Una risorsa indirizzo IP pubblico dispone di un indirizzo IP dinamico o statico assegnato. Se non si ha familiarità con gli indirizzi IP in Azure, leggere l'articolo [Indirizzi IP in Azure](virtual-network-ip-addresses-overview-arm.md) per ottenere altre informazioni.

In questo articolo viene illustrato come usare il portale di Azure per assegnare più indirizzi IP a una VM creata tramite il modello di distribuzione Azure Resource Manager. Non è possibile a assegnare più indirizzi IP alle risorse create tramite il modello di distribuzione classica. Per altre informazioni sui modelli di distribuzione di Azure, leggere l'articolo [Understand Azure deployment models](../resource-manager-deployment-model.md) (Informazioni sui modelli di distribuzione di Azure).

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

## <a name="scenario"></a>Scenario
Una macchina virtuale con una singola scheda di interfaccia di rete viene creata e collegata a una rete virtuale. La macchina virtuale richiede tre diversi indirizzi IP *privati* e due indirizzi IP *pubblici*. Gli indirizzi IP vengono assegnati alle configurazioni IP seguenti:

* **IPConfig-1:** assegna un indirizzo IP privato *dinamico* (predefinito) e un indirizzo IP pubblico *statico*.
* **IPConfig-2:** assegna un indirizzo IP privato *statico* e un indirizzo IP pubblico *statico*.
* **IPConfig-3:** assegna un indirizzo IP privato *dinamico* e nessun indirizzo IP pubblico.
  
    ![Più indirizzi IP](./media/virtual-network-multiple-ip-addresses-powershell/OneNIC-3IP.png)

Le configurazioni IP vengono associate alla scheda di interfaccia di rete al momento della creazione della stessa, mentre la scheda di interfaccia di rete viene collegata alla macchina virtuale al momento della creazione della macchina virtuale. I tipi di indirizzi IP usati per lo scenario sono a scopo illustrativo. È possibile assegnare qualsiasi tipo di assegnazione e indirizzo IP desiderato.

## <a name="a-name--createacreate-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Creare una macchina virtuale con più indirizzi IP

La procedura seguente illustra come creare una macchina virtuale di esempio con più indirizzi IP, come descritto nello scenario. Modificare i nomi delle variabili e i tipi di indirizzi IP come richiesto per l'implementazione.

1. Per installare e configurare l'interfaccia della riga di comando di Azure, seguire la procedura riportata nell'articolo [Installare e configurare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md) e accedere all'account Azure.

2. Registrarsi all'anteprima inviando un messaggio di posta elettronica a [IP multipli](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) con l'ID sottoscrizione e l'uso previsto. Non completare i passaggi rimanenti:
    - Finché non si riceve un messaggio di posta elettronica che comunica che si è stati accettati nell'anteprima.
    - Senza seguire le istruzioni nel messaggio di posta elettronica ricevuto.
3. [Creare un gruppo di risorse](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-resource-groups-and-choose-deployment-locations) seguito da una [rete virtuale e subnet](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-network-and-subnet). Modificare i campi ``` --address-prefixes ``` e ```--address-prefix``` sui valori seguenti per rappresentare lo scenario esatto descritto in questo articolo:

    ```azurecli
    --address-prefixes 10.0.0.0/16
    --address-prefix 10.0.0.0/24
    ```
    >[!NOTE] 
    >L'articolo di riferimento precedente usa l'Europa occidentale come posizione per creare risorse, mentre questo articolo usa gli Stati Uniti centro-occidentali. Apportare in modo accurato le modifiche alla posizione.

4. [Creare un account di archiviazione](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-storage-account) per la macchina virtuale.


5. Creare la scheda di interfaccia di rete e le configurazioni IP da assegnare alla scheda di interfaccia di rete. È possibile aggiungere, rimuovere o modificare le configurazioni in base alle esigenze. Nello scenario vengono descritte le configurazioni seguenti:

    **IPConfig-1**

    Immettere i comandi seguenti per creare:

    - Una risorsa indirizzo IP pubblico con un indirizzo IP pubblico statico
    - Una configurazione IP con la risorsa indirizzo IP pubblico e un indirizzo IP privato dinamico

    ```azurecli
    azure network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP --domain-name-label mypublicdns --allocation-method Static
    ```
    > [!NOTE]
    > Per gli indirizzi IP pubblici è prevista una tariffa nominale. Per altre informazioni sui prezzi degli indirizzi IP, vedere la pagina [Prezzi per gli indirizzi IP](https://azure.microsoft.com/pricing/details/ip-addresses) . È previsto un limite per il numero di indirizzi IP pubblici che possono essere usati in una sottoscrizione. Per altre informazioni sui limiti, vedere l'articolo [Limiti di Azure](../azure-subscription-service-limits.md#networking-limits).

    ```azurecli
    azure network nic create --resource-group myResourceGroup --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic1 --public-ip-name myPublicIP
    ```

    **IPConfig-2**

     Immettere i comandi seguenti per creare una nuova risorsa indirizzo IP pubblico e una nuova configurazione IP con un indirizzo IP pubblico e un indirizzo IP privato statico:
    
    ```azurecli
    azure network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP2 --domain-name-label mypublicdns2 --allocation-method Static

    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-2 --private-ip-address 10.0.0.5 --public-ip-name myPublicIP2
    ```

    **IPConfig-3**

    Immettere i comandi seguenti per creare una configurazione IP con un indirizzo IP privato dinamico e nessun indirizzo IP pubblico:

    ```azurecli
    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-3
    ```

    >[!NOTE] 
    >Sebbene questo articolo assegna tutte le configurazioni IP a una singola scheda di interfaccia di rete, è possibile anche assegnare più configurazioni IP a qualsiasi scheda di interfaccia di rete in una macchina virtuale. Per informazioni su come creare una VM con più interfacce di rete, leggere l'articolo Creare una macchina virtuale con più schede di interfaccia di rete usando PowerShell.

6. Articolo [Creare una macchina virtuale Linux](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-the-linux-vms). Assicurarsi di rimuovere la proprietà ```  --availset-name myAvailabilitySet \ ```, non è necessaria per questo scenario. Usare il percorso appropriato in base allo scenario in uso. 

    >[!WARNING] 
    > Il passaggio 6 nell'articolo sulla creazione di macchine virtuali ha esito negativo se la dimensione della macchina virtuale non è supportata nella posizione selezionata. Eseguire il comando seguente per ottenere un elenco completo delle macchine virtuali, ad esempio negli Stati Uniti centro-occidentali. Questo nome di percorso può essere modificato in base allo scenario.
    > 
    >       azure vm sizes --location westcentralus

    Per modificare le dimensioni della macchina virtuale alla versione 2 DS2 Standard, ad esempio, è sufficiente aggiungere la seguente proprietà ```  --vm-size Standard_DS3_v2``` al comando ``` azure vm create ``` nel passaggio 6.

7. Immettere il comando seguente per visualizzare la scheda di interfaccia di rete e le configurazioni IP associate:

    ```azurecli
    azure network nic show --resource-group myResourceGroup --name myNic1
    ```

## <a name="a-nameosconfigaadd-ip-addresses-to-a-vm-operating-system"></a><a name="OsConfig"></a>Aggiungere indirizzi IP a un sistema operativo VM

Connettersi e accedere alla VM creata con più indirizzi IP privati. È necessario aggiungere manualmente tutti gli indirizzi IP privati aggiunti alla VM, incluso l'indirizzo primario. Completare i passaggi seguenti per il sistema operativo VM:

### <a name="windows"></a>Windows

1. Da un prompt dei comandi digitare *ipconfig /all*.  Viene visualizzato solo l'indirizzo IP privato *Primary* , tramite DHCP.
2. Digitare *ncpa.cpl* nel prompt dei comandi per aprire la finestra **Connessioni di rete**.
3. Aprire le proprietà per**Connessione alla rete locale (LAN)**.
4. Fare doppio clic su Protocollo Intenret versione 4 (IPv4).
5. Selezionare **Utilizza il seguente indirizzo IP** e immettere i valori seguenti:

    * **Indirizzo IP**: immettere l'indirizzo IP privato *Primary* .
    * **Subnet mask**: configurare questo valore in base alla subnet. Se, ad esempio, la subnet è di tipo /24, la subnet mask è 255.255.255.0.
    * **Gateway predefinito**: primo indirizzo IP nella subnet. Se la subnet è 10.0.0.0/24, l'indirizzo IP del gateway è 10.0.0.1.
    * Fare clic su **Utilizza i seguenti indirizzi server DNS** e immettere i valori seguenti:
        * **Server DNS preferito**: immettere 168.63.129.16 se non si usa il proprio server DNS.  Se si usa il proprio server DNS, immettere il relativo indirizzo IP.
    * Fare clic sul pulsante **Avanzate** e aggiungere altri indirizzi IP. Aggiungere ogni indirizzo IP privato secondario elencato nel passaggio 8 all'interfaccia di rete con la stessa subnet specificata per l'indirizzo IP primario.
    * Fare clic su **OK** per chiudere le impostazioni TCP/IP e quindi di nuovo su **OK** per chiudere le impostazioni della scheda. Viene ristabilita la connessione RDP.
6. Da un prompt dei comandi digitare *ipconfig /all*. Tutti gli indirizzi IP aggiunti vengono visualizzati e DHCP viene disattivato.
    
### <a name="linux-ubuntu"></a>Linux (Ubuntu)

1. Aprire una finestra del terminale.
2. Assicurarsi di essere l'utente ROOT. In caso contrario, immettere il comando seguente:

    ```bash
    sudo -i
    ```

3. Aggiornare il file di configurazione dell'interfaccia di rete, presupponendo 'eth0'.

    * Mantenere la voce esistente per dhcp. L'indirizzo IP primario conserva la configurazione precedente.
    * Aggiungere una configurazione per un indirizzo IP statico aggiuntivo con i comandi seguenti:

        ```bash
        cd /etc/network/interfaces.d/
        ls
        ```

    Dovrebbe essere visualizzato un file con estensione cfg.
4. Aprire il file: vi *filename*.

    Dovrebbero essere visualizzate le righe seguenti alla fine del file:

    ```bash
    auto eth0
    iface eth0 inet dhcp
    ```

5. Aggiungere le righe seguenti dopo le righe esistenti nel file:

    ```bash
    iface eth0 inet static
    address <your private IP address here>
    ```

6. Salvare il file usando il comando seguente:

    ```bash
    :wq
    ```

7. Reimpostare l'interfaccia di rete con il comando seguente:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

    > [!IMPORTANT]
    > Eseguire ifdown e ifup nella stessa riga se si usa una connessione remota.
    >

8. Verificare che l'indirizzo IP venga aggiunto all'interfaccia di rete con il comando seguente:

    ```bash
    Ip addr list eth0
    ```

    L'indirizzo IP aggiunto dovrebbe essere incluso nell'elenco.
    
### <a name="linux-redhat-centos-and-others"></a>Linux (Redhat, CentOS e altro)

1. Aprire una finestra del terminale.
2. Assicurarsi di essere l'utente ROOT. In caso contrario, immettere il comando seguente:

    ```bash
    sudo -i
    ```

3. Immettere la password e seguire le istruzioni visualizzate. Quando si è l'utente ROOT, passare alla cartella degli script di rete con il comando seguente:

    ```bash
    cd /etc/sysconfig/network-scripts
    ```

4. Elencare i file ifcfg correlati usando il comando seguente:

    ```bash
    ls ifcfg-*
    ```

    Uno dei file visualizzati dovrebbe essere *ifcfg-eth0* .

5. Copiare il file *ifcfg-eth0* e denominarlo *ifcfg-eth0:0* con il comando seguente:

    ```bash
    cp ifcfg-eth0 ifcfg-eth0:0
    ```

6. Modificare il file *ifcfg-eth0:0* con il comando seguente:

    ```bash
    vi ifcfg-eth1
    ```

7. Cambiare il dispositivo specificando il nome appropriato nel file, in questo caso *eth0:0* , con il comando seguente:

    ```bash
    DEVICE=eth0:0
    ```

8. Cambiare la riga *IPADDR = YourPrivateIPAddress* in modo che rispecchi l'indirizzo IP.
9. Salvare il file usando il comando seguente:

    ```bash
    :wq
    ```

10. Riavviare i servizi di rete e assicurarsi che le modifiche siano riuscite eseguendo i comandi seguenti:

    ```bash
    /etc/init.d/network restart
    Ipconfig
    ```

    L'indirizzo IP aggiunto, *eth0:0*, dovrebbe essere incluso nell'elenco restituito.

## <a name="a-nameaddaadd-ip-addresses-to-a-vm"></a><a name="add"></a>Aggiungere indirizzi IP a una macchina virtuale

È possibile aggiungere indirizzi IP privati e pubblici a una scheda di interfaccia di rete esistente completando la procedura seguente. Gli esempi si basano sullo [scenario](#Scenario) descritto in questo articolo.

1. Aprire l'interfaccia della riga di comando di Azure e completare i passaggi rimanenti in questa sezione all'interno di una singola sessione dell'interfaccia della riga di comando. Se l'interfaccia della riga di comando di Azure non è installata e configurata, completare la procedura riportata nell'articolo [Installare e configurare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md) e accedere all'account Azure.

2. Registrarsi all'anteprima inviando un messaggio di posta elettronica a [IP multipli](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) con l'ID sottoscrizione e l'uso previsto. Non completare i passaggi rimanenti:
    - Finché non si riceve un messaggio di posta elettronica che comunica che si è stati accettati nell'anteprima.
    - Senza seguire le istruzioni nel messaggio di posta elettronica ricevuto.


3. Completare i passaggi in una delle sezioni seguenti, a seconda delle esigenze:

    **Aggiungere un indirizzo IP privato**
    
    Per aggiungere un indirizzo IP privato a una scheda di interfaccia di rete, è necessario creare una configurazione IP tramite il comando seguente.  Se si desidera aggiungere un indirizzo IP privato dinamico, rimuovere ```-PrivateIpAddress 10.0.0.7``` prima di immettere il comando. Quando si specifica un indirizzo IP statico, deve essere un indirizzo non usato per la subnet.

    ```azurecli
    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 --private-ip-address 10.0.0.7 --name IPConfig-4
    ```
    Creare tutte le configurazioni usando nomi di configurazione univoci e indirizzi IP privati (per le configurazioni con indirizzi IP statici).

    **Aggiungere un indirizzo IP pubblico**
    
    L'indirizzo IP pubblico viene aggiunto associandolo a una nuova configurazione IP o a una configurazione IP esistente. Completare i passaggi in una delle sezioni che seguono, a seconda del caso.

    > [!NOTE]
    > Per gli indirizzi IP pubblici è prevista una tariffa nominale. Per altre informazioni sui prezzi degli indirizzi IP, vedere la pagina [Prezzi per gli indirizzi IP](https://azure.microsoft.com/pricing/details/ip-addresses) . È previsto un limite per il numero di indirizzi IP pubblici che possono essere usati in una sottoscrizione. Per altre informazioni sui limiti, vedere l'articolo [Limiti di Azure](../azure-subscription-service-limits.md#networking-limits).
    >

    **Associare la risorsa a una nuova configurazione IP**
    
    Ogni volta che si aggiunge un indirizzo IP pubblico a una nuova configurazione IP, è necessario aggiungere anche un indirizzo IP privato, perché tutte le configurazioni IP devono avere un indirizzo IP privato. È possibile aggiungere una risorsa indirizzo IP pubblico esistente o crearne una nuova. Per crearne una nuova, usare il comando seguente:
    
    ```azurecli
    azure network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP3 --domain-name-label mypublicdns3
    ```

    Per creare una nuova configurazione IP con un indirizzo IP privato dinamico e la risorsa indirizzo IP pubblico *myPublicIP3* associata, inserire il comando seguente:

    ```azurecli
    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic --name IPConfig-4 --public-ip-name myPublicIP3
    ```

    **Associare la risorsa a una configurazione IP esistente**
    Una risorsa indirizzo IP pubblico può essere associata a una configurazione IP che non dispone ancora di una risorsa associata. È possibile stabilire se una configurazione IP dispone di un indirizzo IP pubblico associato immettendo il comando seguente:

    ```azurecli
    azure network nic ip-config list --resource-group myResourceGroup --nic-name myNic1
    ```

    Cercare una riga simile a quella che segue l'output restituito:
    
        Name               Provisioning state  Primary  Private IP allocation  Private IP version  Private IP address  Subnet    Public IP
        -----------------  ------------------  -------  ---------------------  ------------------  ------------------  --------  -----------
        default-ip-config  Succeeded           true     Dynamic                IPv4                10.0.0.4            mySubnet  myPublicIP
        IPConfig-2         Succeeded           false    Static                 IPv4                10.0.0.5            mySubnet  myPublicIP2
        IPConfig-3         Succeeded           false    Dynamic                IPv4                10.0.0.6            mySubnet
     
    Poiché la colonna **IP pubblico** per *IpConfig-3* è vuota, nessuna risorsa di indirizzo IP pubblico è attualmente associata. È possibile aggiungere una risorsa indirizzo IP pubblico esistente a IpConfig-3 o immettere il comando seguente per crearne una:

    ```azurecli
    azure network public-ip create --resource-group  myResourceGroup --location westcentralus --name myPublicIP3 --domain-name-label mypublicdns3 --allocation-method Static
    ```
    
    Immettere il comando seguente per associare la risorsa indirizzo IP pubblico alla configurazione IP esistente denominata *IPConfig-3*:
    
    ```azurecli
    azure network nic ip-config set --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-3 --public-ip-name myPublicIP3
    ```


7. Visualizzare le risorse indirizzo IP privato e indirizzo IP pubblico assegnate alla scheda di interfaccia di rete immettendo il comando seguente:

    ```azurecli
    azure network nic ip-config list --resource-group myResourceGroup --nic-name myNic1
    ```
    L'output dovrebbe essere simile al seguente: 
    
        Name               Provisioning state  Primary  Private IP allocation  Private IP version  Private IP address  Subnet    Public IP
        -----------------  ------------------  -------  ---------------------  ------------------  ------------------  --------  -----------
        default-ip-config  Succeeded           true     Dynamic                IPv4                10.0.0.4            mySubnet  myPublicIP
        IPConfig-2         Succeeded           false    Static                 IPv4                10.0.0.5            mySubnet  myPublicIP2
        IPConfig-3         Succeeded           false    Dynamic                IPv4                10.0.0.6            mySubnet  myPublicIP3
     
9. Aggiungere gli indirizzi IP aggiunti alla scheda di interfaccia di rete al sistema operativo della macchina virtuale seguendo le istruzioni disponibili nella sezione [Aggiungere indirizzi IP a un sistema operativo VM](#OsConfig) in questo articolo.


<!--HONumber=Nov16_HO3-->


