---
title: "Guida introduttiva: Creare un'istanza di Load Balancer Standard - Portale di Azure"
titlesuffix: Azure Load Balancer
description: Questo argomento di avvio rapido mostra come creare un'istanza di Load Balancer Standard con il portale di Azure.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a Standard Load Balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/11/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: c8df0daac25a79bbbd67577c30b0a2da62d037da
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68273833"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Guida introduttiva: Creare un'istanza di Load Balancer Standard per bilanciare il carico delle macchine virtuali con il portale di Azure

Il bilanciamento del carico offre un livello più elevato di disponibilità e scalabilità distribuendo le richieste in ingresso tra più macchine virtuali. È possibile usare il portale di Azure per creare un servizio di bilanciamento del carico di macchine virtuali. Questa guida introduttiva illustra come bilanciare il carico delle macchine virtuali con un'istanza di Load Balancer Standard.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-standard-load-balancer"></a>Creare un'istanza di Load Balancer Standard

In questa sezione viene creata un'istanza di Load Balancer Standard che consente di bilanciare il carico delle macchine virtuali. Load Balancer Standard supporta solo un indirizzo IP pubblico standard. Quando si crea un'istanza di Load Balancer Standard, è necessario creare anche un nuovo indirizzo IP pubblico standard che viene configurato come il front-end (denominato come *LoadBalancerFrontend* per impostazione predefinita) per Load Balancer Standard. 

1. In alto a sinistra nella schermata selezionare **Crea una risorsa** > **Rete** > **Load Balancer**.
2. Nella scheda **Generale** della pagina **Crea servizio di bilanciamento del carico** immettere o selezionare le informazioni seguenti, accettare le impostazioni predefinite per le opzioni rimanenti e quindi selezionare **Rivedi e crea**:

    | Impostazione                 | Valore                                              |
    | ---                     | ---                                                |
    | Subscription               | Selezionare la propria sottoscrizione.    |    
    | Resource group         | Selezionare **Crea nuovo** e digitare *myResourceGroupSLB* nella casella di testo.|
    | NOME                   | *myLoadBalancer*                                   |
    | Region         | Selezionare **Europa occidentale**.                                        |
    | Type          | Selezionare **Pubblica**.                                        |
    | SKU           | Selezionare **Standard**.                          |
    | Indirizzo IP pubblico | Selezionare **Crea nuovo**. |
    | Nome dell'indirizzo IP pubblico              | Digitare *myPublicIP* nella casella di testo.   |
    |Zona di disponibilità| Selezionare **Con ridondanza della zona**.    |
3. Nella scheda **Rivedi e crea** selezionare **Crea**.   

    ![Creare un'istanza di Load Balancer Standard](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Creare risorse di Load Balancer

In questa sezione si configurano le impostazioni di Load Balancer per un pool di indirizzi back-end e un probe di integrità e si specifica una regola di bilanciamento.

### <a name="create-a-backend-address-pool"></a>Creare un pool di indirizzi back-end

Per distribuire il traffico alle macchine virtuali, è necessario che un pool di indirizzi back-end contenga gli indirizzi IP delle schede di interfaccia di rete virtuale connesse all'istanza di Load Balancer. Creare il pool di indirizzi back-end *myBackendPool* per includere le macchine virtuali per il bilanciamento del carico del traffico Internet.

1. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myLoadBalancer** nell'elenco di risorse.
2. In **Impostazioni** selezionare **Pool back-end** e quindi selezionare **Aggiungi**.
3. Nella pagina **Aggiungi pool back-end** digitare *myBackEndPool* come nome per il pool back-end e quindi selezionare **Aggiungi**.

### <a name="create-a-health-probe"></a>Creare un probe di integrità

Per consentire all'istanza di Load Balancer di monitorare lo stato dell'app, si usa un probe di integrità. Il probe di integrità aggiunge o rimuove in modo dinamico le macchine virtuali nella rotazione dell'istanza di Load Balancer in base alla rispettiva risposta ai controlli di integrità. Creare un probe di integrità *myHealthProbe* per monitorare l'integrità delle macchine virtuali.

1. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myLoadBalancer** nell'elenco di risorse.
2. In **Impostazioni** selezionare **Probe integrità** e quindi selezionare **Aggiungi**.
    
    | Impostazione | Valore |
    | ------- | ----- |
    | NOME | Immettere *myHealthProbe*. |
    | Protocollo | Selezionare **HTTP**. |
    | Porta | Immettere *80*.|
    | Interval | Immettere *15* in **Intervallo** come numero di secondi tra i tentativi del probe. |
    | Soglia non integra | Selezionare **2** per **Soglia di non integrità** come numero di errori di probe consecutivi che devono verificarsi prima che una macchina virtuale venga considerata non integra.|
    | | |
4. Selezionare **OK**.

### <a name="create-a-load-balancer-rule"></a>Creare una regola di bilanciamento del carico
Una regola di bilanciamento del carico consente di definire come il traffico verrà distribuito alle VM. Definire la configurazione IP front-end per il traffico in ingresso e il pool IP back-end che riceve il traffico, insieme alle porte di origine e di destinazione necessarie. Creare una regola di Load Balancer *myLoadBalancerRuleWeb* per l'ascolto sulla porta 80 nel front-end *FrontendLoadBalancer* e l'invio del traffico di rete con bilanciamento del carico al pool di indirizzi back-end *myBackEndPool*, sempre tramite la porta 80. 

1. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myLoadBalancer** nell'elenco di risorse.
2. In **Impostazioni** selezionare **Regole di bilanciamento del carico** e quindi selezionare **Aggiungi**.
3. Usare questi valori per configurare la regola di bilanciamento del carico:
    
    | Impostazione | Valore |
    | ------- | ----- |
    | NOME | Immettere *myHTTPRule*. |
    | Protocollo | selezionare **TCP**. |
    | Porta | Immettere *80*.|
    | Porta back-end | Immettere *80*. |
    | Pool back-end | Selezionare *myBackendPool*.|
    | Probe di integrità | Selezionare *myHealthProbe*. |
4. Accettare tutte le impostazioni predefinite e quindi selezionare **OK**.


## <a name="create-backend-servers"></a>Creare i server back-end

In questa sezione si crea una rete virtuale, si creano tre macchine virtuali per il pool back-end al servizio di bilanciamento del carico e quindi si installa IIS nelle macchine virtuali per testare il servizio di bilanciamento del carico.

### <a name="create-a-virtual-network"></a>Crea rete virtuale
1. Nella parte superiore sinistra della schermata, selezionare **Crea una risorsa** > **Rete** > **Rete virtuale**.

1. In **Crea rete virtuale** immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    | NOME | Immettere *myVNet*. |
    | Spazio degli indirizzi | Immettere *10.1.0.0/16*. |
    | Subscription | Selezionare la propria sottoscrizione.|
    | Resource group | Selezionare la risorsa esistente: *myResourceGroupSLB*. |
    | Location | Selezionare **Europa occidentale**.|
    | Subnet - Nome | Immettere *myBackendSubnet*. |
    | Subnet - Intervallo di indirizzi | Immettere *10.1.0.0/24*. |
1. Lasciare tutte le impostazioni predefinite e selezionare **Crea**.

### <a name="create-virtual-machines"></a>Creare macchine virtuali
Load Balancer Standard supporta solo le macchine virtuali con indirizzi IP standard nel pool back-end. In questa sezione si creeranno tre macchine virtuali (*myVM1*, *myVM2* e *myVM3*) con un indirizzo IP pubblico standard in tre aree diverse (*Zona 1*, *Zona 2* e *Zona 3*) che vengono aggiunte in seguito al pool di back-end dell'istanza di Load Balancer Standard che è stata creata in precedenza.

1. In altro a sinistra nel portale selezionare **Crea una risorsa** > **Calcolo** > **Windows Server 2019 Datacenter**. 
   
1. In **Crea macchina virtuale** digitare o selezionare i valori seguenti nella scheda **Nozioni di base**:
   - **Sottoscrizione** > **Gruppo di risorse**: Selezionare **myResourceGroupSLB**.
   - **Dettagli istanza** > **Nome macchina virtuale**: Digitare *myVM1*.
   - **Dettagli istanza** > **Area** > selezionare **Europa occidentale**.
   - **Dettagli istanza** > **Opzioni di disponibilità** > selezionare **Zone di disponibilità**. 
   - **Dettagli istanza** > **Zona di disponibilità** > selezionare **1**.
   - **Account amministratore**> Immettere le informazioni **Nome utente**, **Password** e **Conferma password**.
   - Selezionare la scheda **Rete**, oppure selezionare **Avanti: Dischi**, quindi **Avanti: Rete**.
  
1. Nella scheda **Rete** verificare che siano selezionate le opzioni seguenti:
   - **Rete virtuale**: *myVnet*
   - **Subnet**: *myBackendSubnet*
   - **IP pubblico** > selezionare **Crea nuovo** e nella finestra **Crea indirizzo IP pubblico** selezionare **Standard** per **SKU** e **Con ridondanza della zona** per **Zona di disponibilità**, quindi selezionare **OK**.
   - Per creare un nuovo gruppo di sicurezza di rete (NSG), un tipo di firewall, in **Gruppo di sicurezza di rete** selezionare **Avanzate**. 
       1. Nel campo **Configura gruppo di sicurezza di rete** selezionare **Crea nuovo**. 
       1. Digitare *myNetworkSecurityGroup* e selezionare **OK**.
   - Per includere la macchina virtuale nel pool back-end dell'istanza di Load Balancer, completare i passaggi seguenti:
        - In **Bilanciamento del carico**, per **Posizionare questa macchina virtuale prima di una soluzione di bilanciamento del carico esistente?** selezionare **Sì**.
        - In **Impostazioni di bilanciamento del carico**, per **Opzioni di bilanciamento del carico**, selezionare **Azure Load Balancer**.
        - Per **Selezionare un servizio di bilanciamento del carico**, specificare *myLoadBalancer*.
        - Selezionare la scheda **Gestione** oppure **Avanti** > **Gestione**.
2. Nella scheda **Gestione**, in **Monitoraggio**, impostare **Diagnostica di avvio** su **Off**. 
1. Selezionare **Rivedi e crea**.   
1. Rivedere le impostazioni e quindi selezionare **Crea**.
1. Seguire i passaggi da 2 a 6 per creare due macchine virtuali aggiuntive con i valori seguenti e tutte le altre impostazioni identiche a *myVM1*:

    | Impostazione | VM 2| VM 3|
    | ------- | ----- |---|
    | NOME |  *myVM2* |*myVM3*|
    | Zona di disponibilità | 2 |3|
    |IP pubblico| SKU **Standard**|SKU **Standard**|
    | IP pubblico: Zona di disponibilità| **Con ridondanza della zona** |**Con ridondanza della zona**|
    | Gruppo di sicurezza di rete | Selezionare il gruppo esistente *myNetworkSecurity*| Selezionare il gruppo esistente *myNetworkSecurity*|

 ### <a name="create-nsg-rule"></a>Creare una regola del gruppo di sicurezza di rete

In questa sezione si crea una regola del gruppo di sicurezza di rete per consentire connessioni in ingresso tramite HTTP.

1. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine nell'elenco di risorse selezionare **myNetworkSecurityGroup**, che si trova nel gruppo di risorse **myResourceGroupSLB**.
2. In **Impostazioni** selezionare **Regole di sicurezza in ingresso**, quindi selezionare **Aggiungi**.
3. Immettere questi valori per la regola di sicurezza in ingresso denominata *myHTTPRule* per consentire connessioni HTTP in ingresso tramite la porta 80:
    - *Tag del servizio* come **Origine**
    - *Internet* come **Tag del servizio di origine**
    - *80* come **Intervalli di porte di destinazione**
    - *TCP* come **Protocollo**
    - *Consenti* come **Azione**
    - *100* come **Priorità**
    - *myHTTPRule* come nome
    - *Consenti HTTP* come descrizione
4. Selezionare **Aggiungi**.
 
### <a name="install-iis"></a>Installare IIS

1. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine nell'elenco di risorse selezionare **myVM1**, che si trova nel gruppo di risorse *myResourceGroupSLB*.
2. Nella pagina **Panoramica** selezionare **Connetti** per connettersi a RDP nella macchina virtuale.
5. Accedere alla VM con le credenziali fornite durante la creazione di questa VM. Verrà avviata una sessione Desktop remoto con la macchina virtuale *myVM1*.
6. Nel desktop del server passare a **Strumenti di amministrazione Windows**>**Windows PowerShell**.
7. Nella finestra di PowerShell eseguire i comandi seguenti per installare il server IIS, rimuovere il file predefinito iisstart.htm e aggiungere un nuovo file iisstart.htm che visualizza il nome della macchina virtuale:

   ```azurepowershell-interactive
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
6. Chiudere la sessione RDP con *myVM1*.
7. Ripetere i passaggi da 1 a 6 per installare IIS e il file iisstart.htm aggiornato in *myVM2* e *myVM3*.

## <a name="test-the-load-balancer"></a>Testare il servizio di bilanciamento del carico
1. Trovare l'indirizzo IP pubblico del servizio di bilanciamento del carico nella schermata **Panoramica**. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myPublicIP**.

2. Copiare l'indirizzo IP pubblico e quindi incollarlo nella barra degli indirizzi del browser. Nel browser verrà visualizzata la pagina predefinita del server Web IIS.

   ![Server Web IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Per visualizzare l'istanza di Load Balancer distribuire il traffico tra tutte e tre macchine virtuali, è possibile personalizzare la pagina predefinita del server Web IIS di ciascuna macchina virtuale e quindi forzare l'aggiornamento del Web browser dal computer client.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse, l'istanza di Load Balancer e tutte le risorse correlate. A questo scopo, selezionare il gruppo di risorse (*myResouceGroupSLB*) che contiene l'istanza di Load Balancer e quindi selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è creata un'istanza di Load Balancer Standard, si sono collegate macchine virtuali a tale istanza, si è configurata la regola del traffico di Load Balancer, si è definito il probe di integrità e quindi si è testata l'istanza di Load Balancer. Per altre informazioni su Azure Load Balancer, passare alle esercitazioni su Azure Load Balancer.

> [!div class="nextstepaction"]
> [Esercitazioni su Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
