---
title: 'Esercitazione: Creare un servizio pubblico di bilanciamento del carico di base - Portale di Azure | Microsoft Docs'
description: Questa esercitazione illustra come creare un servizio di bilanciamento del carico di base interno tramite il portale di Azure.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internet traffic to virtual machines within a specific zone in a region.
ms.assetid: aa9d26ca-3d8a-4a99-83b7-c410dd20b9d0
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/28/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: a5b6ae833fcd340a639c068156940e6b9ad469ca
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2018
ms.locfileid: "51711991"
---
# <a name="tutorial-load-balance-internal-traffic-with-basic-load-balancer-to-vms-using-the-azure-portal"></a>Esercitazione: Bilanciare il carico del traffico interno con il servizio di bilanciamento del carico di base sulle VM tramite il portale di Azure

Il bilanciamento del carico offre un livello più elevato di disponibilità e scalabilità distribuendo le richieste in ingresso tra più macchine virtuali. È possibile usare il portale di Azure per bilanciare il carico del traffico interno sulle macchine virtuali con un servizio di bilanciamento del carico di base. In questa esercitazione viene illustrato come creare risorse di rete, server back-end e un servizio di bilanciamento del carico di base interno.

Se si preferisce, è possibile completare questa esercitazione usando l'[interfaccia della riga di comando di Azure](load-balancer-get-started-ilb-arm-cli.md) oppure [Azure PowerShell](load-balancer-get-started-ilb-arm-ps.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Creare una rete virtuale
1. Nella parte superiore sinistra della schermata fare clic su **Nuovo** > **Rete** > **Rete virtuale** e immettere i valori seguenti per la rete virtuale:
    - *myVnet* come nome della rete virtuale.
    - *myResourceGroupILB* come nome del gruppo di risorse esistente
    - *myBackendSubnet* come nome della subnet.
2. Fare clic su **Crea** per creare la rete virtuale.

![Creare un servizio di bilanciamento del carico](./media/tutorial-load-balancer-basic-internal-portal/1-load-balancer.png)

## <a name="create-a-basic-load-balancer"></a>Creare un servizio di bilanciamento del carico di base
Creare un servizio di bilanciamento del carico di base interno con il portale.

1. Nella parte superiore sinistra dello schermo fare clic su **Crea una risorsa** > **Rete** > **Servizio di bilanciamento del carico**.
2. Nella pagina **Crea servizio di bilanciamento del carico** immettere questi valori per il bilanciamento del carico:
    - *myLoadBalancer* come nome del servizio di bilanciamento del carico.
    - **Interno** come tipo di bilanciamento del carico.
    - **Di base** come versione SKU.
    - **10.1.0.7** come indirizzo IP privato statico.
    - *myVNet* come rete virtuale che si sceglie dall'elenco delle reti esistenti.
    - *mySubnet* come subnet che si sceglie dall'elenco delle subnet esistenti.
    - *myResourceGroupILB* come nome del nuovo gruppo di risorse creato.
3. Fare clic su **Crea** per creare il servizio di bilanciamento del carico.
   
    ## <a name="create-backend-servers"></a>Creare i server back-end

In questa sezione si creano due macchine virtuali per il pool back-end del servizio di bilanciamento del carico di base e quindi si installa IIS nelle macchine virtuali per testare il servizio di bilanciamento del carico.

### <a name="create-virtual-machines"></a>Creare macchine virtuali

1. Nella parte superiore sinistra dello schermo fare clic su **Crea una risorsa** > **Calcolo** > **Windows Server 2016 Datacenter** e immettere i valori seguenti per la macchina virtuale:
    - *myVM1* come nome della macchina virtuale.        
    - *azureuser* come nome utente dell'amministratore.   
    - *myResourceGroupILB* come **Gruppo di risorse**, selezionare **Usa esistente** e quindi selezionare *myResourceGroupILB*.
2. Fare clic su **OK**.
3. Selezionare **DS1_V2** come dimensioni per la macchina virtuale e fare clic su **Seleziona**.
4. Immettere i valori seguenti per le impostazioni della macchina virtuale:
    - *myAvailabilitySet* come nome del nuovo set di disponibilità creato.
    -  *myVNet*: assicurarsi che sia selezionato come rete virtuale.
    - *myBackendSubnet*: assicurarsi che sia selezionato come subnet.
5. In **Gruppo di sicurezza di rete** selezionare **Avanzate**. Quindi, per **Gruppo di sicurezza di rete (firewall)**, selezionare **Nessuno**.
5. Fare clic su **Disabilitato** per disabilitare la diagnostica di avvio.
6. Fare clic su **OK**, verificare le impostazioni nella pagina di riepilogo e quindi fare clic su **Crea**.
7. Seguendo i passaggi da 1 a 6, creare una seconda macchina virtuale denominata *VM2* con *myAvailabilityset* come set di disponibilità, *myVnet* come rete virtuale, *myBackendSubnet* come subnet e selezionare **Nessuno** per **Gruppo di sicurezza di rete (firewall)**. 

### <a name="install-iis-and-customize-the-default-web-page"></a>Installare IIS e personalizzare la pagina Web predefinita

1. Fare clic su **Tutte le risorse** nel menu a sinistra e quindi nell'elenco delle risorse fare clic su **myVM1**, che si trova nel gruppo di risorse *myResourceGroupILB*.
2. Nella pagina **Panoramica** fare clic su **Connetti** per connettersi a RDP nella macchina virtuale.
3. Accedere alla macchina virtuale.
4. Nel desktop del server passare a **Strumenti di amministrazione Windows**>**Server Manager**.
5. Avviare Windows PowerShell su VM1 e usare i comandi seguenti per installare il server IIS e aggiornare il file con estensione htm predefinito.
    ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```
5. Chiudere la connessione RDP con *myVM1*.
6. Ripetere i passaggi da 1 a 5 in *myVM2* per installare IIS e personalizzare la pagina Web predefinita.

## <a name="create-basic-load-balancer-resources"></a>Creare le risorse del servizio di bilanciamento del carico di base

In questa sezione si configurano le impostazioni del servizio di bilanciamento del carico per un pool di indirizzi back-end e un probe di integrità e si specificano le regole di bilanciamento del carico.


### <a name="create-a-backend-address-pool"></a>Creare un pool di indirizzi back-end

Per distribuire il traffico alle macchine virtuali, è necessario che un pool di indirizzi back-end contenga gli indirizzi IP delle schede di interfaccia di rete virtuale connesse al bilanciamento del carico. Creare il pool di indirizzi back-end *myBackendPool* per includere *VM1* e *VM2*.

1. Fare clic su **Tutte le risorse** nel menu a sinistra e quindi fare clic su **myLoadBalancer** nell'elenco di risorse.
2. In **Impostazioni** fare clic su **Pool back-end** e quindi su **Aggiungi**.
3. Nella pagina **Aggiungi pool back-end** seguire questa procedura:
    - Digitare *myBackEndPool* come nome del pool back-end.
    - In **Associato a** selezionare **Set di disponibilità** dal menu a discesa.
    - In **Set di disponibilità** fare clic su **myAvailabilitySet**.
    - Fare clic su **Aggiungi una configurazione IP della rete di destinazione** per aggiungere ogni macchina virtuale (*myVM1* & *myVM2*) creata nel pool back-end.
    - Fare clic su **OK**.

        ![Aggiunta al pool di indirizzi back-end ](./media/tutorial-load-balancer-basic-internal-portal/3-load-balancer-backend-02.png)

3. Verificare che l'impostazione del pool back-end di bilanciamento del carico visualizzi entrambe le macchine virtuali **VM1** e **VM2**.

### <a name="create-a-health-probe"></a>Creare un probe di integrità

Per consentire al servizio di bilanciamento del carico di base di monitorare lo stato dell'app, si usa un probe di integrità. Il probe di integrità aggiunge o rimuove in modo dinamico le VM nella rotazione del servizio di bilanciamento del carico in base alla rispettiva risposta ai controlli di integrità. Creare un probe di integrità *myHealthProbe* per monitorare l'integrità delle macchine virtuali.

1. Fare clic su **Tutte le risorse** nel menu a sinistra e quindi fare clic su **myLoadBalancer** nell'elenco di risorse.
2. In **Impostazioni** fare clic su **Probe integrità** e quindi su **Aggiungi**.
3. Usare questi valori per creare il probe di integrità:
    - *myHealthProbe* come nome del probe di integrità.
    - **HTTP** come tipo di protocollo.
    - *80* come numero della porta.
    - *15* in **Intervallo** come numero di secondi tra i tentativi del probe.
    - *2* per **Soglia di non integrità** come numero di errori di probe consecutivi che devono verificarsi prima che una macchina virtuale venga considerata non integra.
4. Fare clic su **OK**.

   ![Aggiunta di un probe](./media/tutorial-load-balancer-basic-internal-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Creare una regola di bilanciamento del carico

Una regola di bilanciamento del carico consente di definire come il traffico verrà distribuito alle VM. Definire la configurazione IP front-end per il traffico in ingresso e il pool IP di back-end affinché riceva il traffico, insieme alla porta di origine e di destinazione necessaria. Creare una regola di bilanciamento del carico *myLoadBalancerRuleWeb* per l'ascolto sulla porta 80 nel front-end *LoadBalancerFrontEnd* e l'invio del traffico di rete con carico bilanciato al pool di indirizzi back-end *myBackEndPool* sempre tramite la porta 80. 

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
    
    ![Aggiunta di una regola di bilanciamento del carico](./media/tutorial-load-balancer-basic-internal-portal/5-load-balancing-rules.png)

## <a name="create-a-virtual-machine-to-test-the-load-balancer"></a>Creare una macchina virtuale per testare il servizio di bilanciamento del carico
Per testare il servizio di bilanciamento del carico interno, è necessario creare una macchina virtuale che si trova nella stessa rete virtuale delle VM del server back-end.
1. Nella parte superiore sinistra dello schermo fare clic su **Crea una risorsa** > **Calcolo** > **Windows Server 2016 Datacenter** e immettere i valori seguenti per la macchina virtuale:
    - *myVMTest* come nome della macchina virtuale.        
    - *myResourceGroupILB* come **Gruppo di risorse**, selezionare **Usa esistente** e quindi selezionare *myResourceGroupILB*.
2. Fare clic su **OK**.
3. Selezionare **DS1_V2** come dimensioni per la macchina virtuale e fare clic su **Seleziona**.
4. Immettere i valori seguenti per le impostazioni della macchina virtuale:
    -  *myVNet*: assicurarsi che sia selezionato come rete virtuale.
    - *myBackendSubnet*: assicurarsi che sia selezionato come subnet.
5. Fare clic su **Disabilitato** per disabilitare la diagnostica di avvio.
6. Fare clic su **OK**, verificare le impostazioni nella pagina di riepilogo e quindi fare clic su **Crea**.

## <a name="test-the-load-balancer"></a>Testare il servizio di bilanciamento del carico
1. Nel portale di Azure, individuare l'indirizzo IP pubblico del servizio di bilanciamento del carico nella schermata **Panoramica**. A tale scopo: a. Fare clic su **Tutte le risorse** nel menu a sinistra e quindi fare clic su **myLoadBalancer** nell'elenco di risorse.
    b. Nella pagina dei dettagli **Panoramica** copiare l'indirizzo IP privato (in questo esempio, è 10.1.0.7).

2. Creare una connessione remota a *myVMTest* come segue: a. Fare clic su **Tutte le risorse** nel menu a sinistra e quindi nell'elenco delle risorse fare clic su **myVMTest**, che si trova nel gruppo di risorse *myResourceGroupILB*.
2. Nella pagina **Panoramica** fare clic su **Connetti** per avviare una sessione remota con la macchina virtuale.
3. Accedere a *myVMTest*.
3. Incollare l'indirizzo IP privato nella barra degli indirizzi del browser in *myVMTest*. Nel browser verrà visualizzata la pagina predefinita del server Web IIS.

      ![Server Web IIS](./media/tutorial-load-balancer-basic-internal-portal/9-load-balancer-test.png)

Per verificare la distribuzione del traffico tra entrambe le VM che eseguono l'app da parte del servizio di bilanciamento del carico, forzare l'aggiornamento del Web browser.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse, il servizio di bilanciamento del carico e tutte le risorse correlate. A questo scopo, selezionare il gruppo di risorse che contiene il servizio di bilanciamento del carico e fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati creati un gruppo di risorse, le risorse di rete e i server back-end. Tali risorse sono state quindi usate per creare un sistema di bilanciamento del carico interno per bilanciare il carico del traffico interno sulle macchine virtuali. Successivamente, apprendere come [bilanciare il carico delle VM tra zone di disponibilità](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
