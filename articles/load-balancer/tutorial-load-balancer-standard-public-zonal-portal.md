---
title: "Esercitazione: bilanciamento del carico delle macchine virtuali all'interno di una zona - Portale di Azure | Microsoft Docs"
description: Questa esercitazione dimostra come creare un servizio Load Balancer Standard con front-end di zona per bilanciare il carico delle macchine virtuali all'interno di una zona di disponibilità tramite il portale di Azure
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internet traffic to virtual machines within a specific zone in a region.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/17/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 52d0aeabab173caf4460827ca0d5984070688f0e
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/18/2018
ms.locfileid: "34304726"
---
# <a name="tutorialload-balance-vms-within-an-availability-zone-with-a-standard-load-balancer-using-the-azure-portal"></a>Esercitazione: bilanciare il carico delle macchine virtuali all'interno di una zona di disponibilità con un servizio Load Balancer Standard tramite il portale di Azure

Questa esercitazione illustra dettagliatamente la creazione di un servizio [Load Balancer Standard](https://aka.ms/azureloadbalancerstandard) pubblico con un front-end di zona tramite un indirizzo IP pubblico standard usando il portale di Azure. In questo scenario, specificare una zona particolare per le istanze front-end e back-end, per allineare il percorso dati e le risorse a una zona specifica. Si apprenderà come:

> [!div class="checklist"]
> * Creare un servizio Load Balancer Standard con un front-end di zona
> * Creare gruppi di sicurezza di rete per definire le regole del traffico in ingresso
> * Creare macchine virtuali di zona e collegarsi a un bilanciamento del carico
> * Creare un probe di integrità per il servizio di bilanciamento del carico
> * Creare regole del traffico di bilanciamento del carico
> * Creare un sito IIS di base
> * Visualizzare un bilanciamento del carico in azione

Per altre informazioni sull'uso delle zone di disponibilità con Load Balancer Standard, vedere [Load Balancer Standard e zone di disponibilità](load-balancer-standard-availability-zones.md).

Se si preferisce, è possibile completare questa esercitazione usando l'[interfaccia della riga di comando di Azure](load-balancer-standard-public-zonal-cli.md).

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [http://portal.azure.com](http://portal.azure.com).

## <a name="create-a-public-standard-load-balancer"></a>Creare un servizio Load Balancer Standard pubblico

Load Balancer Standard supporta solo un indirizzo IP pubblico standard. Quando si crea un nuovo indirizzo IP pubblico durante la creazione del servizio di bilanciamento del carico, l'indirizzo viene automaticamente configurato come versione SKU Standard e con ridondanza della zona.

1. Nella parte superiore sinistra dello schermo fare clic su **Crea una risorsa** > **Rete** > **Load Balancer**.
2. Nella pagina **Creare un servizio di bilanciamento del carico** immettere questi valori per il bilanciamento del carico:
    - *myLoadBalancer* come nome del servizio di bilanciamento del carico.
    - **Pubblico** come tipo di bilanciamento del carico.
     - *myPublicIPZonal* come nuovo indirizzo IP pubblico da creare. A tale scopo, fare clic su **Scegliere un indirizzo IP pubblico** e quindi su **Crea nuovo**. Per il tipo di nome *myPublicIP*, il valore di SKU è Standard per impostazione predefinita e seleziona **Zona 1** per **Zona di disponibilità**.
    - *myResourceGroupZLB* come nome del nuovo gruppo di risorse creato.
    - **westeurope** come posizione.
3. Fare clic su **Crea** per creare il servizio di bilanciamento del carico.
   
    ![Creare un servizio Load Balancer Standard di zona con il portale di Azure](./media/tutorial-load-balancer-standard-zonal-portal/create-load-balancer-zonal-frontend.png)


## <a name="create-backend-servers"></a>Creare i server back-end

In questa sezione si crea una rete virtuale, si creano due macchine virtuali nella stessa zona, ovvero la zona 1, per l'area da aggiungere al pool back-end del servizio di bilanciamento del carico e quindi si installa IIS nelle macchine virtuali per testare il servizio di bilanciamento del carico con ridondanza della zona. Di conseguenza, in caso di errore di una macchina virtuale, il probe di integrità per la macchina virtuale nella stessa zona ha esito negativo e il traffico continua a essere gestito dall'altra macchina virtuale all'interno della stessa zona.

### <a name="create-a-virtual-network"></a>Crea rete virtuale
1. Nella parte superiore sinistra dello schermo fare clic su **Crea una risorsa** > **Rete** > **Rete virtuale** e immettere i valori seguenti per la rete virtuale:
    - *myVnet* come nome della rete virtuale.
    - *myResourceGroupZLB* come nome del gruppo di risorse esistente.
    - *myBackendSubnet* come nome della subnet.
2. Fare clic su **Crea** per creare la rete virtuale.

    ![Crea rete virtuale](./media/tutorial-load-balancer-standard-zonal-portal/create-virtual-network.png)

## <a name="create-a-network-security-group"></a>Creare un gruppo di sicurezza di rete

1. Nella parte superiore sinistra dello schermo fare clic su **Crea una risorsa**, nella casella di ricerca digitare *Gruppo di sicurezza di rete* e nella pagina Gruppo di sicurezza di rete fare clic su **Crea**.
2. Nella pagina Crea gruppo di sicurezza di rete immettere i valori seguenti:
    - *myNetworkSecurityGroup* come nome del gruppo di sicurezza di rete.
    - *myResourceGroupLBAZ* come nome del gruppo di risorse esistente.
   
    ![Crea rete virtuale](./media/tutorial-load-balancer-standard-zonal-portal/create-network-security-group.png)

### <a name="create-nsg-rules"></a>Creare le regole NSG

In questa sezione vengono create regole NSG per consentire le connessioni in ingresso tramite HTTP e RDP usando il portale di Azure.

1. Nel portale di Azure scegliere **Tutte le risorse** dal menu a sinistra e quindi cercare e fare clic su **myNetworkSecurityGroup** nel gruppo di risorse **myResourceGroupZLB**.
2. In **Impostazioni** fare clic su **Regole di sicurezza in ingresso** e quindi su **Aggiungi**.
3. Immettere questi valori per la regola di sicurezza in ingresso denominata *myHTTPRule* per consentire connessioni HTTP in ingresso tramite la porta 80:
    - *Tag del servizio* come **Origine**
    - *Internet* come **Tag del servizio di origine**
    - *80* come **Intervalli di porte di destinazione**
    - *TCP* come **Protocollo**
    - *Consenti* come **Azione**
    - *100* come **Priorità**
    - *myHTTPRule* come **Nome**.
    - *Consenti HTTP* come **Descrizione**
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

    ![Creare una regola RDP](./media/tutorial-load-balancer-standard-zonal-portal/create-rdp-rule.png)

### <a name="create-virtual-machines"></a>Creare macchine virtuali

1. Nella parte superiore sinistra dello schermo fare clic su **Crea una risorsa** > **Calcolo** > **Windows Server 2016 Datacenter** e immettere i valori seguenti per la macchina virtuale:
    - *myVM1* come nome della macchina virtuale.        
    - *azureuser* come nome utente dell'amministratore.    
    - *myResourceGroupZLB* come **Gruppo di risorse**, selezionare **Usa esistente** e quindi selezionare *myResourceGroupZLB*.
2. Fare clic su **OK**.
3. Selezionare **DS1_V2** come dimensioni per la macchina virtuale e fare clic su **Seleziona**.
4. Immettere i valori seguenti per le impostazioni della macchina virtuale:
    - *Zona 1* per la zona di disponibilità in cui posizionare la macchina virtuale.
    -  *myVNet*: assicurarsi che sia stata fatta la selezione come rete virtuale.
    - *myVM1PIP* come indirizzo IP pubblico standard da creare. Fare clic su *Crea nuovo* e quindi digitare *myVM1PIP* per il nome e selezionare **1** per la zona. Lo SKU dell'indirizzo IP è Standard per impostazione predefinita.
    - *myBackendSubnet*: assicurarsi che sia stata fatta la selezione come subnet.
    - *myNetworkSecurityGroup* come nome del gruppo di sicurezza di rete (firewall) già esistente.
5. Fare clic su **Disabilitato** per disabilitare la diagnostica di avvio.
6. Fare clic su **OK**, verificare le impostazioni nella pagina di riepilogo e quindi fare clic su **Crea**.
7. Creare una seconda macchina virtuale denominata *myVM2* nella zona 1 con *myVnet* come rete virtuale, *myVM2PIP* come indirizzo IP pubblico standard, *myBackendSubnet* come subnet e **myNetworkSecurityGroup* come gruppo di sicurezza di rete, seguendo i passaggi da 1 a 6.

    ![Creare una regola RDP](./media/tutorial-load-balancer-standard-zonal-portal/create-virtual-machine.png) 

### <a name="install-iis-on-vms"></a>Installare IIS in macchine virtuali

1. Fare clic su **Tutte le risorse** nel menu a sinistra e quindi nell'elenco delle risorse fare clic su **myVM1**, che si trova nel gruppo di risorse *myResourceGroupZLB*.
2. Nella pagina **Panoramica** fare clic su **Connetti** per connettersi a RDP nella macchina virtuale.
3. Accedere alla macchina virtuale con il nome utente e la password specificati durante la creazione della macchina virtuale (potrebbe essere necessario selezionare **Altre opzioni**, quindi **Usa un account diverso** per specificare le credenziali immesse quando è stata creata la macchina virtuale), quindi selezionare **OK**. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Selezionare **Sì** per procedere con la connessione.
4. Nel desktop del server passare a **Strumenti di amministrazione Windows**>**Windows PowerShell**.
6. Nella finestra di PowerShell eseguire i comandi seguenti per installare il server IIS, rimuovere il file predefinito iisstart.htm e aggiungere un nuovo file iisstart.htm che visualizza il nome della macchina virtuale:

   ```azurepowershell-interactive
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from" + $env:computername)
   ```
7. Chiudere la sessione RDP con *myVM1*
8. Ripetere i passaggi da 1 a 7 per installare IIS in *myVM2*.

## <a name="create-load-balancer-resources"></a>Creare risorse di bilanciamento del carico

In questa sezione si configurano le impostazioni del servizio di bilanciamento del carico per un pool di indirizzi back-end e un probe di integrità e si specificano le regole NAT e di bilanciamento del carico.


### <a name="create-a-backend-address-pool"></a>Creare un pool di indirizzi back-end

Per distribuire il traffico alle macchine virtuali, è necessario che un pool di indirizzi back-end contenga gli indirizzi IP delle schede di interfaccia di rete virtuale connesse al bilanciamento del carico. Creare il pool di indirizzi back-end *myBackendPool* per includere *VM1* e *VM2*.

1. Fare clic su **Tutte le risorse** nel menu a sinistra e quindi fare clic su **myLoadBalancer* nell'elenco di risorse.
2. In **Impostazioni** fare clic su **Pool back-end** e quindi su **Aggiungi**.
3. Nella pagina **Add a backend pool** (Aggiungi pool back-end) seguire questa procedura:
    - Digitare *myBackEndPool* come nome del pool back-end.
    - Per **Rete virtuale** nel menu a discesa fare clic su *myVNet*.
    - Per **Macchina virtuale** e **Indirizzo IP** aggiungere *myVM1* e *myVM2* e gli indirizzi IP pubblici corrispondenti.
4. Fare clic su **Aggiungi**.
5. Verificare che l'impostazione del pool back-end di bilanciamento del carico visualizzi entrambe le macchine virtuali **myVM1** e **myVM2**.
 
    ![Creare un pool back-end](./media/tutorial-load-balancer-standard-zonal-portal/create-backend-pool.png) 

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
    
    ![Aggiunta di una regola di bilanciamento del carico](./media/tutorial-load-balancer-standard-zonal-portal/load-balancing-rule.png)

## <a name="test-the-load-balancer"></a>Testare il servizio di bilanciamento del carico
1. Trovare l'indirizzo IP pubblico del servizio di bilanciamento del carico nella schermata **Panoramica**. Fare clic su **Tutte le risorse** e quindi su **myPublicIP**. 

2. Copiare l'indirizzo IP pubblico e quindi incollarlo nella barra degli indirizzi del browser. La pagina predefinita che include il nome della pagina del server Web viene visualizzata nel browser.

      ![Server Web IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)
3. Per visualizzare il bilanciamento del carico in azione, è possibile forzare l'arresto della macchina virtuale visualizzata e aggiornare il browser per vedere l'altro nome di server visualizzato nel browser.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse, il servizio di bilanciamento del carico e tutte le risorse correlate. A questo scopo, selezionare il gruppo di risorse che contiene il servizio di bilanciamento del carico e fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Load Balancer Standard](load-balancer-standard-overview.md).
- [Bilanciare il carico delle macchine virtuali tra zone di disponibilità](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
