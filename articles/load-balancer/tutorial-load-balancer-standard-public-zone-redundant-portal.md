---
title: 'Esercitazione: Macchine virtuali Load Balancer tra zone di disponibilità: portale di Azure'
titlesuffix: Azure Load Balancer
description: Questa esercitazione illustra come creare un'istanza di Load Balancer Standard con front-end con ridondanza della zona per bilanciare il carico delle macchine virtuali tra zone di disponibilità tramite il portale di Azure
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internet traffic to virtual machines across availability zones in a region, so that the customers can still access the web service if a datacenter is unavailable.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 5b024321a18c6dec4f56a7cbc12c5a8fa748f903
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68273485"
---
# <a name="tutorial-load-balance-vms-across-availability-zones-with-a-standard-load-balancer-using-the-azure-portal"></a>Esercitazione: Bilanciare il carico delle macchine virtuali tra zone di disponibilità con un servizio Load Balancer Standard tramite il portale di Azure

Il bilanciamento del carico offre un livello più elevato di disponibilità distribuendo le richieste in ingresso tra più macchine virtuali. Questa esercitazione illustra i passaggi necessari per creare un'istanza pubblica di Load Balancer Standard per il bilanciamento del carico delle macchine virtuali tra zone di disponibilità. Ciò facilita la protezione di app e dati da un poco probabile errore o perdita di un intero data center. Con la ridondanza della zona, in caso di errore di una o più zone di disponibilità, il percorso dati continua a funzionare, a condizione che una zona dell'area rimanga integra. Si apprenderà come:

> [!div class="checklist"]
> * Creare un'istanza di Load Balancer Standard
> * Creare gruppi di sicurezza di rete per definire le regole del traffico in ingresso
> * Creare macchine virtuali con ridondanza della zona su più zone e collegarle a un servizio di bilanciamento del carico
> * Creare un probe di integrità per il servizio di bilanciamento del carico
> * Creare regole del traffico di bilanciamento del carico
> * Creare un sito IIS di base
> * Visualizzare un bilanciamento del carico in azione

Per altre informazioni sull'uso delle zone di disponibilità con Load Balancer Standard, vedere [Load Balancer Standard e zone di disponibilità](load-balancer-standard-availability-zones.md).

Se si preferisce, è possibile completare questa esercitazione usando l'[interfaccia della riga di comando di Azure](load-balancer-standard-public-zone-redundant-cli.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-standard-load-balancer"></a>Creare un'istanza di Load Balancer Standard

Load Balancer Standard supporta solo un indirizzo IP pubblico standard. Quando si crea un nuovo indirizzo IP pubblico durante la creazione del servizio di bilanciamento del carico, l'indirizzo viene automaticamente configurato come versione SKU Standard e con ridondanza della zona.

1. Nella parte superiore sinistra dello schermo fare clic su **Crea una risorsa** > **Rete** > **Servizio di bilanciamento del carico**.
2. Nella scheda **Generale** della pagina **Crea servizio di bilanciamento del carico** immettere o selezionare le informazioni seguenti, accettare le impostazioni predefinite per le opzioni rimanenti e quindi selezionare **Rivedi e crea**:

    | Impostazione                 | Valore                                              |
    | ---                     | ---                                                |
    | Subscription               | Selezionare la propria sottoscrizione.    |    
    | Resource group         | Selezionare **Crea nuovo** e digitare *MyResourceGroupLBAZ* nella casella di testo.|
    | NOME                   | *myLoadBalancer*                                   |
    | Region         | Selezionare **Europa occidentale**.                                        |
    | Type          | Selezionare **Pubblica**.                                        |
    | SKU           | Selezionare **Standard**.                          |
    | Indirizzo IP pubblico | Selezionare **Crea nuovo**. |
    | Nome dell'indirizzo IP pubblico              | Digitare *myPublicIP* nella casella di testo.   |
    |Zona di disponibilità| Selezionare **Con ridondanza della zona**.    |
   

## <a name="create-backend-servers"></a>Creare i server back-end

In questa sezione viene creata una rete virtuale, macchine virtuali in diverse zone dell'area e quindi si installa IIS nelle macchine virtuali per testare il servizio di bilanciamento del carico con ridondanza della zona. Di conseguenza, in caso di errore di una zona, il probe di integrità per la macchina virtuale nella stessa zona ha esito negativo e il traffico continua a funzionare nelle macchine virtuali nelle altre zone.

### <a name="create-a-virtual-network"></a>Crea rete virtuale
Creare una rete virtuale per la distribuzione dei server back-end.

1. Nella parte superiore sinistra dello schermo fare clic su **Crea una risorsa** > **Rete** > **Rete virtuale** e immettere i valori seguenti per la rete virtuale:
    - *myVnet* come nome della rete virtuale.
    - *myResourceGroupLBAZ* come nome del gruppo di risorse esistente.
    - *myBackendSubnet* come nome della subnet.
2. Fare clic su **Crea** per creare la rete virtuale.

    ![Crea rete virtuale](./media/load-balancer-standard-public-availability-zones-portal/2-load-balancer-virtual-network.png)

## <a name="create-a-network-security-group"></a>Creare un gruppo di sicurezza di rete

Creare un gruppo di sicurezza di rete per definire le connessioni in ingresso alla rete virtuale.

1. Nella parte superiore sinistra dello schermo fare clic su **Crea una risorsa**, nella casella di ricerca digitare *Gruppo di sicurezza di rete* e nella pagina Gruppo di sicurezza di rete fare clic su **Crea**.
2. Nella pagina Crea gruppo di sicurezza di rete immettere i valori seguenti:
    - *myNetworkSecurityGroup* come nome del gruppo di sicurezza di rete.
    - *myResourceGroupLBAZ* come nome del gruppo di risorse esistente.
   
![Crea rete virtuale](./media/load-balancer-standard-public-availability-zones-portal/create-nsg.png)

### <a name="create-network-security-group-rules"></a>Creare regole del gruppo di sicurezza di rete

In questa sezione vengono create regole del gruppo di sicurezza di rete per consentire le connessioni in ingresso tramite HTTP e RDP usando il portale di Azure.

1. Nel portale di Azure scegliere **Tutte le risorse** dal menu a sinistra e quindi cercare e fare clic su **myNetworkSecurityGroup** nel gruppo di risorse **myResourceGroupLBAZ**.
2. In **Impostazioni** fare clic su **Regole di sicurezza in ingresso** e quindi su **Aggiungi**.
3. Immettere questi valori per la regola di sicurezza in ingresso denominata *myHTTPRule* per consentire connessioni HTTP in ingresso tramite la porta 80:
    - *Tag del servizio* come **Origine**
    - *Internet* come **Tag del servizio di origine**
    - *80* come **Intervalli di porte di destinazione**
    - *TCP* come **Protocollo**
    - *Consenti* come **Azione**
    - *100* come **Priorità**
    - *myHTTPRule* come nome della regola di bilanciamento del carico.
    - *Consenti HTTP* come descrizione della regola di bilanciamento del carico.
4. Fare clic su **OK**.
 
   ![Crea rete virtuale](./media/load-balancer-standard-public-availability-zones-portal/8-load-balancer-nsg-rules.png)
5. Ripetere i passaggi da 2 a 4 per creare un'altra regola denominata *myRDPRule* per consentire una connessione RDP in ingresso tramite la porta 3389 con i valori seguenti:
    - *Tag del servizio* come **Origine**
    - *Internet* come **Tag del servizio di origine**
    - *3389* come **Intervalli di porte di destinazione**
    - *TCP* come **Protocollo**
    - *Consenti* come **Azione**
    - *200* come **Priorità**
    - *myRDPRule* come nome
    - *Consenti RDP* come descrizione

### <a name="create-virtual-machines"></a>Creare macchine virtuali

Creare macchine virtuali in zone diverse (area 1, 2 e 3) per l'area che può fungere da server di back-end per il bilanciamento del carico.

1. Nella parte superiore sinistra dello schermo fare clic su **Crea una risorsa** > **Calcolo** > **Windows Server 2016 Datacenter** e immettere i valori seguenti per la macchina virtuale:
    - *myVM1* come nome della macchina virtuale.        
    - *azureuser* come nome utente dell'amministratore.    
    - *myResourceGroupLBAZ*: per **Gruppo di risorse** selezionare **Usa esistente** e quindi *myResourceGroupLBAZ*.
2. Fare clic su **OK**.
3. Selezionare **DS1_V2** come dimensioni per la macchina virtuale e fare clic su **Seleziona**.
4. Immettere i valori seguenti per le impostazioni della macchina virtuale:
    - *Zona 1* per la zona in cui posizionare la macchina virtuale.
    -  *myVNet*: assicurarsi che sia stata fatta la selezione come rete virtuale.
    - *myBackendSubnet*: assicurarsi che sia stata fatta la selezione come subnet.
    - *myNetworkSecurityGroup* come nome del gruppo di sicurezza di rete (firewall).
5. Fare clic su **Disabilitato** per disabilitare la diagnostica di avvio.
6. Fare clic su **OK**, verificare le impostazioni nella pagina di riepilogo e quindi fare clic su **Crea**.
  
   ![Creare una macchina virtuale](./media/load-balancer-standard-public-availability-zones-portal/create-vm-standard-ip.png)

7. Creare una seconda macchina virtuale denominata *VM2* nella zona 2 e una terza macchina virtuale nella zona 3, con *myVnet* come rete virtuale, *myBackendSubnet* come subnet e **myNetworkSecurityGroup* come gruppo di sicurezza di rete, seguendo i passaggi da 1 a 6.

### <a name="install-iis-on-vms"></a>Installare IIS in macchine virtuali

1. Scegliere **Tutte le risorse** dal menu a sinistra e quindi nell'elenco di risorse fare clic su **myVM1** nel gruppo di risorse *myResourceGroupLBAZ*.
2. Nella pagina **Panoramica** fare clic su **Connetti** per connettersi a RDP nella macchina virtuale.
3. Accedere alla macchina virtuale con nome utente *azureuser*.
4. Nel desktop del server passare a **Strumenti di amministrazione Windows**>**Windows PowerShell**.
5. Nella finestra di PowerShell eseguire i comandi seguenti per installare il server IIS, rimuovere il file predefinito iisstart.htm e aggiungere un nuovo file iisstart.htm che visualizza il nome della macchina virtuale:
   ```azurepowershell-interactive
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from" + $env:computername)
   ```
6. Chiudere la sessione RDP con *myVM1*.
7. Ripetere i passaggi da 1 a 6 per installare IIS e il file iisstart.htm aggiornato in *myVM2* e *myVM3*.

## <a name="create-load-balancer-resources"></a>Creare risorse di bilanciamento del carico

In questa sezione si configurano le impostazioni del servizio di bilanciamento del carico per un pool di indirizzi back-end e un probe di integrità e si specificano le regole NAT e di bilanciamento del carico.


### <a name="create-a-backend-address-pool"></a>Creare un pool di indirizzi back-end

Per distribuire il traffico alle macchine virtuali, è necessario che un pool di indirizzi back-end contenga gli indirizzi IP delle schede di interfaccia di rete virtuale connesse al bilanciamento del carico. Creare il pool di indirizzi back-end *myBackendPool* per includere *VM1*, *VM2* e *VM3*.

1. Fare clic su **Tutte le risorse** nel menu a sinistra e quindi fare clic su **myLoadBalancer** nell'elenco di risorse.
2. In **Impostazioni** fare clic su **Pool back-end** e quindi su **Aggiungi**.
3. Nella pagina **Aggiungi pool back-end** seguire questa procedura:
    - Digitare *myBackEndPool* come nome del pool back-end.
    - Per **Rete virtuale** nel menu a discesa fare clic su **myVNet**.
    - Per **Macchina virtuale** nel menu a discesa fare clic su **myVM1**.
    - Per **Indirizzo IP** nel menu a discesa fare clic sull'indirizzo IP di myVM1.
4. Fare clic su **Aggiungi nuova risorsa back-end** per aggiungere ogni macchina virtuale (*myVM2* e *myVM3*) al pool back-end del servizio di bilanciamento del carico.
5. Fare clic su **Aggiungi**.

    ![Aggiunta al pool di indirizzi back-end](./media/load-balancer-standard-public-availability-zones-portal/add-backend-pool.png)

3. Verificare che l'impostazione del pool back-end di bilanciamento del carico visualizzi tutte le tre macchine virtuali: **myVM1**, **myVM2** e **myVM3**.

### <a name="create-a-health-probe"></a>Creare un probe di integrità

Per consentire al servizio di bilanciamento del carico di monitorare lo stato dell'app, si usa un probe di integrità. Il probe di integrità aggiunge o rimuove in modo dinamico le VM nella rotazione del servizio di bilanciamento del carico in base alla rispettiva risposta ai controlli di integrità. Creare un probe di integrità *myHealthProbe* per monitorare l'integrità delle macchine virtuali.

1. Fare clic su **Tutte le risorse** nel menu a sinistra e quindi fare clic su **myLoadBalancer** nell'elenco di risorse.
2. In **Impostazioni** fare clic su **Probe integrità** e quindi su **Aggiungi**.
3. Usare questi valori per creare il probe di integrità:
    - *myHealthProbe* come nome del probe di integrità.
    - **HTTP** come tipo di protocollo.
    - *80* come numero della porta.
    - *15* in **Intervallo** come numero di secondi tra i tentativi del probe.
    - *2* per **Soglia di non integrità** come numero di errori di probe consecutivi che devono verificarsi prima che una macchina virtuale venga considerata non integra.
4. Fare clic su **OK**.

   ![Aggiunta di un probe](./media/load-balancer-standard-public-availability-zones-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Creare una regola di bilanciamento del carico

Una regola di bilanciamento del carico consente di definire come il traffico verrà distribuito alle VM. Definire la configurazione IP front-end per il traffico in ingresso e il pool IP di back-end affinché riceva il traffico, insieme alla porta di origine e di destinazione necessaria. Creare una regola di bilanciamento del carico *myLoadBalancerRuleWeb* per l'ascolto sulla porta 80 nel front-end *FrontendLoadBalancer* e l'invio del traffico di rete con bilanciamento del carico al pool di indirizzi back-end *myBackEndPool* sempre tramite la porta 80. 

1. Fare clic su **Tutte le risorse** nel menu a sinistra e quindi fare clic su **myLoadBalancer** nell'elenco di risorse.
2. In **Impostazioni** fare clic su **Regole di bilanciamento del carico** e quindi su **Aggiungi**.
3. Usare questi valori per configurare la regola di bilanciamento del carico:
    - *myHTTPRule* come nome della regola di bilanciamento del carico.
    - **TCP** come tipo di protocollo.
    - *80* come numero della porta.
    - *80* come porta back-end.
    - *myBackendPool* come nome del pool back-end.
    - *myHealthProbe* come nome del probe di integrità.
4. Fare clic su **OK**.
    
    ![Aggiunta di una regola di bilanciamento del carico](./media/load-balancer-standard-public-availability-zones-portal/load-balancing-rule.png)

## <a name="test-the-load-balancer"></a>Testare il servizio di bilanciamento del carico
1. Trovare l'indirizzo IP pubblico del servizio di bilanciamento del carico nella schermata **Panoramica**. Fare clic su **Tutte le risorse** e quindi su **myPublicIP**.

2. Copiare l'indirizzo IP pubblico e quindi incollarlo nella barra degli indirizzi del browser. Nel browser verrà visualizzata la pagina predefinita del server Web IIS.

      ![Server Web IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Per vedere come il servizio di bilanciamento del carico distribuisce il traffico nelle due VM distribuite nell'area, forzare l'aggiornamento del Web browser.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse, il servizio di bilanciamento del carico e tutte le risorse correlate. A questo scopo, selezionare il gruppo di risorse che contiene il servizio di bilanciamento del carico e fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [Load Balancer Standard](load-balancer-standard-overview.md).
