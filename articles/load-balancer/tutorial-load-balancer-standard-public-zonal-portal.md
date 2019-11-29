---
title: "Esercitazione: Bilanciamento del carico delle macchine virtuali all'interno di una zona - Portale di Azure"
titleSuffix: Azure Load Balancer
description: Questa esercitazione dimostra come creare un servizio Load Balancer Standard con front-end di zona per bilanciare il carico delle macchine virtuali all'interno di una zona di disponibilità tramite il portale di Azure
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internet traffic to virtual machines within a specific zone in a region.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 07d4b206c5651bb708ed8b56437a8769dff46557
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225160"
---
# <a name="tutorial-load-balance-vms-within-an-availability-zone-with-standard-load-balancer-by-using-the-azure-portal"></a>Esercitazione: Bilanciare il carico delle macchine virtuali all'interno di una zona di disponibilità con un servizio Load Balancer Standard tramite il portale di Azure

Questa esercitazione illustra la creazione di un'[istanza pubblica di Azure Load Balancer Standard](https://aka.ms/azureloadbalancerstandard) con un front-end di zona che usa un indirizzo IP pubblico standard tramite il portale di Azure. In questo scenario, specificare una zona particolare per le istanze front-end e back-end, per allineare il percorso dati e le risorse a una zona specifica. Si apprenderà come di eseguire le funzioni seguenti:

> [!div class="checklist"]
> * Creare un'istanza di Load Balancer Standard con un front-end di zona.
> * Creare gruppi di sicurezza di rete per definire le regole del traffico in ingresso.
> * Creare macchine virtuali e collegarle a servizio di bilanciamento del carico.
> * Creare un probe di integrità per il servizio di bilanciamento del carico.
> * Creare regole del traffico di bilanciamento del carico.
> * Creare un sito Internet Information Services (IIS) di base.
> * Visualizzare un servizio di bilanciamento del carico in azione.

Per altre informazioni sull'uso delle zone di disponibilità con Load Balancer Standard, vedere [Load Balancer Standard e zone di disponibilità](load-balancer-standard-availability-zones.md).

Se si preferisce, usare l'[interfaccia della riga di comando di Azure](load-balancer-standard-public-zonal-cli.md) per completare questa esercitazione.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-public-standard-load-balancer-instance"></a>Creare un'istanza pubblica di Load Balancer Standard

Load Balancer Standard supporta solo un indirizzo IP pubblico standard. Quando si crea un nuovo indirizzo IP pubblico durante la creazione del servizio di bilanciamento del carico, l'indirizzo viene automaticamente configurato come versione SKU Standard. È inoltre automaticamente con ridondanza della zona.

1. In alto a sinistra nella schermata selezionare **Crea una risorsa** > **Rete** > **Load Balancer**.
2. Nella scheda **Generale** della pagina **Crea servizio di bilanciamento del carico** immettere o selezionare le informazioni seguenti, accettare le impostazioni predefinite per le opzioni rimanenti e quindi selezionare **Rivedi e crea**:

    | Impostazione                 | Valore                                              |
    | ---                     | ---                                                |
    | Subscription               | Selezionare la propria sottoscrizione.    |    
    | Resource group         | Selezionare **Crea nuovo** e digitare *MyResourceGroupZLB* nella casella di testo.|
    | NOME                   | *myLoadBalancer*                                   |
    | Region         | Selezionare **Europa occidentale**.                                        |
    | Type          | Selezionare **Pubblica**.                                        |
    | SKU           | Selezionare **Standard**.                          |
    | Indirizzo IP pubblico | Selezionare **Crea nuovo**. |
    | Nome dell'indirizzo IP pubblico              | Digitare *myPublicIP* nella casella di testo.   |
    |Zona di disponibilità| Selezionare **1**.    |
3. Nella scheda **Rivedi e crea** fare clic su **Crea**.   

   ## <a name="create-backend-servers"></a>Creare i server back-end

In questa sezione si crea una rete virtuale. Si creano inoltre due macchine virtuali nella stessa zona ,ovvero la zona 1, per l'area da aggiungere al pool back-end del servizio di bilanciamento del carico. Quindi si installa IIS nelle macchine virtuali per testare il servizio di bilanciamento del carico con ridondanza della zona. In caso di errore di una macchina virtuale, il probe di integrità per la macchina virtuale nella stessa zona ha esito negativo. Il traffico continua a essere gestito dalle altre macchine virtuali all'interno della stessa zona.

### <a name="create-a-virtual-network"></a>Crea rete virtuale
1. In alto a sinistra nella schermata selezionare **Crea una risorsa** > **Rete** > **Rete virtuale**.  Immettere i valori seguenti per la rete virtuale:
    - **myVnet** come nome della rete virtuale.
    - **myResourceGroupZLB** come nome del gruppo di risorse esistente.
    - **myBackendSubnet** come nome della subnet.
2. Fare clic su **Crea** per creare la rete virtuale.

    ![Crea rete virtuale](./media/tutorial-load-balancer-standard-zonal-portal/create-virtual-network.png)

## <a name="create-a-network-security-group"></a>Creare un gruppo di sicurezza di rete

1. In alto a sinistra nella schermata selezionare **Crea una risorsa**. Nella casella di ricerca immettere **Gruppo di sicurezza di rete**. Nella pagina del gruppo di sicurezza di rete selezionare **Crea**.
2. Nella pagina **Crea gruppo di sicurezza di rete** immettere i valori seguenti:
   - **myNetworkSecurityGroup** come nome del gruppo di sicurezza di rete.
   - **myResourceGroupLBAZ** come nome del gruppo di risorse esistente.
   
     ![Creare un gruppo di sicurezza di rete](./media/tutorial-load-balancer-standard-zonal-portal/create-network-security-group.png)

### <a name="create-nsg-rules"></a>Creare le regole NSG

In questa sezione vengono create regole NSG per consentire le connessioni in ingresso che usano HTTP e il protocollo RDP (Remote Desktop Protocol) Microsoft mediante il portale di Azure.

1. Nel portale di Azure scegliere **Tutte le risorse** dal menu a sinistra. Quindi cercare e selezionare **myNetworkSecurityGroup**. Si trova nel gruppo di risorse **myResourceGroupZLB**.
2. In **Impostazioni** selezionare **Regole di sicurezza in ingresso**. Quindi selezionare **Aggiungi**.
3. Immettere questi valori per la regola di sicurezza in ingresso denominata **myHTTPRule** per consentire le connessioni HTTP in ingresso tramite la porta 80:
    - **Tag del servizio** come **Origine**.
    - **Internet** come **Tag del servizio di origine**.
    - **80** come **Intervalli di porte di destinazione**.
    - **TCP** come **Protocollo**.
    - **Consenti** come **Azione**.
    - **100** come **Priorità**.
    - **myHTTPRule** come **Nome**.
    - **Consenti HTTP** come **Descrizione**.
4. Selezionare **OK**.
 
   ![Creare le regole NSG](./media/load-balancer-standard-public-availability-zones-portal/8-load-balancer-nsg-rules.png)

5. Ripetere i passaggi da 2 a 4 per creare un'altra regola denominata **myRDPRule**. Questa regola consente una connessione RDP in entrata che usa la porta 3389, con i valori seguenti:
    - **Tag del servizio** come **Origine**.
    - **Internet** come **Tag del servizio di origine**.
    - **3389** come **Intervalli di porte di destinazione**.
    - **TCP** come **Protocollo**.
    - **Consenti** come **Azione**.
    - **200** come **Priorità**.
    - **myRDPRule** come **Nome**.
    - **Consenti RDP** come **Descrizione**.

      ![Creare una regola RDP](./media/tutorial-load-balancer-standard-zonal-portal/create-rdp-rule.png)

### <a name="create-virtual-machines"></a>Creare macchine virtuali

1. In altro a sinistra nella schermata selezionare **Crea una risorsa** > **Calcolo** > **Windows Server 2016 Datacenter**. Immettere i valori seguenti per la macchina virtuale:
    - **myVM1** come nome della macchina virtuale.        
    - **azureuser** come nome utente dell'amministratore.    
    - **myResourceGroupZLB** come **Gruppo di risorse**. Selezionare **Usa esistente** e quindi **myResourceGroupZLB**.
2. Selezionare **OK**.
3. Selezionare **DS1_V2** come dimensioni per la macchina virtuale. Scegliere **Seleziona**.
4. Immettere i valori seguenti per le impostazioni della macchina virtuale:
    - **Zona 1** per la zona di disponibilità in cui posizionare la macchina virtuale.
    -  **myVNet**. Assicurarsi che sia stata fatta la selezione come rete virtuale.
    - **myVM1PIP** come indirizzo IP pubblico standard da creare. Selezionare **Crea nuovo**. Per il tipo di nome, selezionare **myVM1PIP**. Per **Zona**, selezionare **1**. Lo SKU dell'indirizzo IP è Standard per impostazione predefinita.
    - **myBackendSubnet**. Verificare che sia selezionato come subnet.
    - **myNetworkSecurityGroup** come nome del gruppo di sicurezza di rete (firewall) già esistente.
5. Selezionare **Disabilitato** per disabilitare la diagnostica di avvio.
6. Selezionare **OK**. Verificare le impostazioni nella pagina di riepilogo. Selezionare quindi **Crea**.
7. Ripetere i passaggi da 1 a 6 per creare una seconda macchina virtuale denominata **myVM2** nella Zona 1. Impostare **myVnet** come rete virtuale. Impostare **myVM2PIP** come indirizzo IP pubblico standard. Impostare **myBackendSubnet** come subnet. Impostare **myNetworkSecurityGroup** come gruppo di sicurezza di rete.

    ![Creare macchine virtuali](./media/tutorial-load-balancer-standard-zonal-portal/create-virtual-machine.png) 

### <a name="install-iis-on-vms"></a>Installare IIS in macchine virtuali

1. Scegliere **Tutte le risorse** nel menu a sinistra. Quindi nell'elenco di risorse selezionare **myVM1**. Si trova nel gruppo di risorse **myResourceGroupZLB**.
2. Nella pagina **Panoramica** selezionare **Connetti** per usare RDP per passare alla macchina virtuale.
3. Accedere alla macchina virtuale con il nome utente e la password specificati durante la creazione. Per specificare le credenziali immesse al momento della creazione della VM potrebbe essere necessario selezionare **Altre opzioni**. Selezionare **Usa un account diverso**. Quindi selezionare **OK**. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Selezionare **Sì** per procedere con la connessione.
4. Nel desktop del server passare a **Strumenti di amministrazione Windows** > **Windows PowerShell**.
6. Nella finestra di **PowerShell** eseguire i comandi seguenti per installare il server IIS. Questi comandi consentono inoltre di rimuovere il file predefinito iisstart.htm e aggiungere un nuovo file iisstart.htm che visualizza il nome della macchina virtuale:

   ```azurepowershell-interactive
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from" + $env:computername)
   ```
7. Chiudere la sessione RDP con **myVM1**.
8. Ripetere i passaggi da 1 a 7 per installare IIS in **myVM2**.

## <a name="create-load-balancer-resources"></a>Creare risorse di bilanciamento del carico

In questa sezione vengono configurate le impostazioni del servizio di bilanciamento del carico per un pool di indirizzi back-end e un probe di integrità. Inoltre, si specificano le regole NAT e di bilanciamento del carico.


### <a name="create-a-backend-address-pool"></a>Creare un pool di indirizzi back-end

Per distribuire il traffico alle macchine virtuali, è necessario che un pool di indirizzi back-end contenga gli indirizzi IP delle schede di interfaccia di rete virtuale connesse al bilanciamento del carico. Creare il pool di indirizzi back-end **myBackendPool** per includere **VM1** e **VM2**.

1. Scegliere **Tutte le risorse** nel menu a sinistra. Quindi selezionare **myLoadBalancer** dall'elenco di risorse.
2. In **Impostazioni** selezionare **Pool back-end**. Quindi selezionare **Aggiungi**.
3. Nella pagina **Aggiungi pool back-end** seguire questa procedura:
    - Immettere **myBackEndPool** come nome del pool back-end.
    - Per **Rete virtuale** nel menu a discesa fare clic selezionare **myVNet**. 
    - Per **Macchina virtuale** e **Indirizzo IP** aggiungere **myVM1** e **myVM2** e gli indirizzi IP pubblici corrispondenti.
4. Selezionare **Aggiungi**.
5. Verificare che l'impostazione del pool back-end di bilanciamento del carico visualizzi entrambe le macchine virtuali **myVM1** e **myVM2**.
 
    ![Creare un pool back-end](./media/tutorial-load-balancer-standard-zonal-portal/create-backend-pool.png) 

### <a name="create-a-health-probe"></a>Creare un probe di integrità

Usare un probe di integrità per consentire al servizio di bilanciamento del carico di monitorare lo stato dell'app. Il probe di integrità aggiunge o rimuove in modo dinamico le VM nella rotazione del servizio di bilanciamento del carico in base alla rispettiva risposta ai controlli di integrità. Creare un probe di integrità **myHealthProbe** per monitorare l'integrità delle macchine virtuali.

1. Scegliere **Tutte le risorse** nel menu a sinistra. Quindi selezionare **myLoadBalancer** dall'elenco di risorse.
2. In **Impostazioni** selezionare **Probe integrità**. Quindi selezionare **Aggiungi**.
3. Usare questi valori per creare il probe di integrità:
    - **myHealthProbe** come nome del probe di integrità.
    - **HTTP** come tipo di protocollo.
    - **80** come numero della porta.
    - **15** in **Intervallo** come numero di secondi tra i tentativi del probe.
    - **2** per **Soglia di non integrità** come numero di errori di probe consecutivi che devono verificarsi prima che una macchina virtuale venga considerata non integra.
4. Selezionare **OK**.

   ![Aggiungere un probe di integrità](./media/load-balancer-standard-public-availability-zones-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Creare una regola di bilanciamento del carico

Una regola di bilanciamento del carico definisce come verrà distribuito il traffico alle macchine virtuali. Definire la configurazione IP front-end per il traffico in ingresso e il pool IP back-end che riceve il traffico, insieme alle porte di origine e di destinazione necessarie. Creare una regola di bilanciamento del carico denominata **myLoadBalancerRuleWeb** per l'ascolto sulla porta 80 nel front-end **FrontendLoadBalancer**. La regola consente l'invio di traffico di rete con carico bilanciato al pool di indirizzi back-end **myBackEndPool**, usando sempre la porta 80. 

1. Scegliere **Tutte le risorse** nel menu a sinistra. Quindi selezionare **myLoadBalancer** dall'elenco di risorse.
2. In **Impostazioni** selezionare **Regole di bilanciamento del carico**. Quindi selezionare **Aggiungi**.
3. Usare questi valori per configurare la regola di bilanciamento del carico:
    - **myHTTPRule** come nome della regola di bilanciamento del carico.
    - **TCP** come tipo di protocollo.
    - **80** come numero della porta.
    - **80** come porta back-end.
    - **myBackendPool** come nome del pool back-end.
    - **myHealthProbe** come nome del probe di integrità.
4. Selezionare **OK**.
    
    ![Aggiungere una regola di bilanciamento del carico](./media/tutorial-load-balancer-standard-zonal-portal/load-balancing-rule.png)

## <a name="test-the-load-balancer"></a>Testare il servizio di bilanciamento del carico
1. Individuare l'indirizzo IP pubblico del servizio di bilanciamento del carico nella schermata **Panoramica**. Selezionare **Tutte le risorse**. Quindi selezionare **myPublicIP**. 

2. Copiare l'indirizzo IP pubblico. Copiarlo quindi nella barra degli indirizzi del browser. La pagina predefinita che include il nome della pagina del server Web viene visualizzata nel browser.

      ![Server Web IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)
3. Per vedere il servizio di bilanciamento del carico in azione, forzare l'arresto della macchina virtuale visualizzata. Aggiornare il browser per vedere l'altro nome di server visualizzato nel browser.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse, il servizio di bilanciamento del carico e tutte le risorse correlate. Selezionare il gruppo di risorse che contiene il servizio di bilanciamento del carico. Selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Load Balancer Standard](load-balancer-standard-overview.md).
- [Bilanciare il carico delle VM tra zone di disponibilità](tutorial-load-balancer-standard-public-zone-redundant-portal.md).
