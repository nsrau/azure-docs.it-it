---
title: Creare un servizio pubblico di bilanciamento del carico di base - Portale di Azure | Microsoft Docs
description: Informazioni su come creare un servizio pubblico di bilanciamento del carico di base con il portale di Azure.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: aa9d26ca-3d8a-4a99-83b7-c410dd20b9d0
ms.service: load-balancer
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.openlocfilehash: c646b0b1ab0ec62cffb4f7cf7474b48c68dfabb4
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2018
---
# <a name="create-a-public-basic-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Creare un servizio pubblico di bilanciamento del carico di base tramite il portale di Azure

Il bilanciamento del carico offre un livello più elevato di disponibilità e scalabilità distribuendo le richieste in ingresso tra più macchine virtuali. È possibile usare il portale di Azure per creare un servizio di bilanciamento del carico di macchine virtuali. In questa guida introduttiva viene illustrato come creare risorse di rete, server back-end e un servizio pubblico di bilanciamento del carico di base.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al portale di Azure all'indirizzo [http://portal.azure.com](http://portal.azure.com).

## <a name="create-a-basic-load-balancer"></a>Creare un servizio di bilanciamento del carico di base

In questa sezione si crea un servizio pubblico di bilanciamento del carico di base tramite il portale. L'indirizzo IP pubblico viene configurato automaticamente come front-end del bilanciamento del carico, denominato *LoadBalancerFrontend* quando si crea l'indirizzo IP pubblico quando si crea la risorsa di bilanciamento del carico tramite il portale.

1. Nella parte superiore sinistra dello schermo fare clic su **Crea una risorsa** > **Rete** > **Load Balancer**.
2. Nella pagina **Creare un servizio di bilanciamento del carico** immettere questi valori per il bilanciamento del carico:
    - *myLoadBalancer* come nome del servizio di bilanciamento del carico.
    - **Pubblico** come tipo di bilanciamento del carico.
    - *myPublicIP* come indirizzo IP pubblico da creare con SKU impostato su **Basic** e **Assegnazione** impostato su **Dinamico**.
    - *myResourceGroupLB* come nome del nuovo gruppo di risorse creato.
3. Fare clic su **Crea** per creare il servizio di bilanciamento del carico.
   
    ![Creare un servizio di bilanciamento del carico](./media/load-balancer-get-started-internet-portal/1-load-balancer.png)


## <a name="create-backend-servers"></a>Creare i server back-end

In questa sezione si crea una rete virtuale, si creano due macchine virtuali per il pool back-end del servizio di bilanciamento del carico di base e quindi si installa IIS nelle macchine virtuali per testare il servizio di bilanciamento del carico.

### <a name="create-a-virtual-network"></a>Crea rete virtuale
1. Nella parte superiore sinistra della schermata fare clic su **Nuovo** > **Rete** > **Rete virtuale** e immettere i valori seguenti per la rete virtuale:
    - *myVnet* come nome della rete virtuale.
    - *myResourceGroupLB* come nome del gruppo di risorse esistente.
    - *myBackendSubnet* come nome della subnet.
2. Fare clic su **Crea** per creare la rete virtuale.

    ![Crea rete virtuale](./media/load-balancer-get-started-internet-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>Creare macchine virtuali

1. Nella parte superiore sinistra della schermata fare clic su **Nuovo** > **Calcolo** > **Windows Server 2016 Datacenter** e immettere i valori seguenti per la macchina virtuale:
    - *myVM1* come nome della macchina virtuale.        
    - *azureuser* come nome utente dell'amministratore. -    
    - *myResourceGroupLB* - per **Resource group** (Gruppo di risorse) selezionare **Use existing** (Usa esistente) e quindi selezionare *myResourceGroupLB*.
2. Fare clic su **OK**.
3. Selezionare **DS1_V2** come dimensioni per la macchina virtuale e fare clic su **Seleziona**.
4. Immettere i valori seguenti per le impostazioni della macchina virtuale:
    - *myAvailabilitySet* come nome del nuovo set di disponibilità creato.
    -  *myVNet* - assicurarsi che sia selezionato come rete virtuale.
    - *myBackendSubnet* - assicurarsi che sia selezionato come subnet.
    - *myVM1-ip* come indirizzo IP pubblico.
    - *myNetworkSecurityGroup* come nome del nuovo gruppo di sicurezza di rete (firewall) che è necessario creare.
5. Fare clic su **Disabilitato** per disabilitare la diagnostica di avvio.
6. Fare clic su **OK**, verificare le impostazioni nella pagina di riepilogo e quindi fare clic su **Crea**.
7. Seguendo i passaggi da 1 a 6, creare una seconda macchina virtuale denominata *VM2* con *myAvailabilityset* come set di disponibilità, *myVnet* come rete virtuale,  *myBackendSubnet* come subnet e *myNetworkSecurityGroup* come gruppo di sicurezza di rete. 

### <a name="create-nsg-rules"></a>Creare le regole NSG

In questa sezione vengono create regole NSG per consentire connessioni in ingresso tramite HTTP ed RDP.

1. Fare clic su **Tutte le risorse** nel menu a sinistra e quindi, nell'elenco delle risorse, fare clic su **myNetworkSecurityGroup**, che si trova nel gruppo di risorse **myResourceGroupLB**.
2. In **Impostazioni** fare clic su **Regole di sicurezza in ingresso** e quindi su **Aggiungi**.
3. Immettere questi valori per la regola di sicurezza in ingresso denominata *myHTTPRule* per consentire connessioni HTTP in ingresso tramite la porta 80:
    - *Tag del servizio* come **Origine**
    - *Internet* come **Tag del servizio di origine**
    - *80* come **Intervalli di porte di destinazione**
    - *TCP* come **Protocollo**
    - *Consenti* come **Azione**
    - *100* come **Priorità**
    - *myHTTPRule* come nome
    - *Consenti HTTP* come descrizione
4. Fare clic su **OK**.
 
 ![Crea rete virtuale](./media/load-balancer-get-started-internet-portal/8-load-balancer-nsg-rules.png)
5. Ripetere i passaggi da 2 a 4 per creare un'altra regola denominata *myRDPRule* per consentire una connessione RDP in ingresso tramite la porta 3389 con i valori seguenti:
    - *Tag del servizio* come **Origine**
    - *Internet* come **Tag del servizio di origine**
    - *3389* come **Intervalli di porte di destinazione**
    - *TCP* come **Protocollo**
    - *Consenti* come **Azione**
    - *200* come **Priorità**
    - *myRDPRule* come nome
    - *Consenti RDP* come descrizione

   

### <a name="install-iis"></a>Installare IIS

1. Fare clic su **Tutte le risorse** nel menu a sinistra e quindi nell'elenco delle risorse fare clic su **myVM1**, che si trova nel gruppo di risorse *myResourceGroupLB*.
2. Nella pagina **Panoramica** fare clic su **Connect to RDP** (Connetti a RDP) nella macchina virtuale.
3. Accedere alla macchina virtuale con nome utente *azureuser* e password *Azure123456!*
4. Nel desktop del server passare a **Strumenti di amministrazione Windows**>**Server Manager**.
5. In Server Manager fare clic su Gestione e quindi su **Aggiungi ruoli e funzionalità**.
 ![Aggiungere il ruolo di Server Manager](./media/load-balancer-get-started-internet-portal/servermanager.png)
6. Nella procedura guidata **Aggiungi ruoli e funzionalità** usare i valori seguenti:
    - Nella pagina **Selezione tipo di installazione** fare clic su **Installazione basata su ruoli o basata su funzionalità**.
    - Nella pagina **Selezione server di destinazione** fare clic su **myVM1**.
    - Nella pagina **Selezionare un ruolo server** fare clic su **Web Server (IIS)**.
    - Seguire le istruzioni per completare il resto della procedura guidata. 
7. Ripetere i passaggi da 1 a 6 per la macchina virtuale *myVM2*.

## <a name="create-basic-load-balancer-resources"></a>Creare le risorse del servizio di bilanciamento del carico di base

In questa sezione si configurano le impostazioni del servizio di bilanciamento del carico per un pool di indirizzi back-end e un probe di integrità e si specificano le regole NAT e di bilanciamento del carico.


### <a name="create-a-backend-address-pool"></a>Creare un pool di indirizzi back-end

Per distribuire il traffico alle macchine virtuali, è necessario che un pool di indirizzi back-end contenga gli indirizzi IP delle schede di interfaccia di rete virtuale connesse al bilanciamento del carico. Creare il pool di indirizzi back-end *myBackendPool* per includere *VM1* e *VM2*.

1. Fare clic su **Tutte le risorse** nel menu a sinistra e quindi fare clic su **myLoadBalancer** nell'elenco di risorse.
2. In **Impostazioni** fare clic su **Pool back-end** e quindi su **Aggiungi**.
3. Nella pagina **Add a backend pool** (Aggiungi pool back-end) seguire questa procedura:
    - Digitare *myBackEndPool come nome del pool back-end.
    - In **Associated to** (Associato a) selezionare **Set di disponibilità** dal menu a discesa.
    - In **Set di disponibilità** fare clic su **myAvailabilitySet**.
    - Fare clic su **Aggiungi una configurazione IP della rete di destinazione** per aggiungere ogni macchina virtuale (*myVM1* & *myVM2*) creata nel pool back-end.
    - Fare clic su **OK**.

    ![Aggiunta al pool di indirizzi back-end ](./media/load-balancer-get-started-internet-portal/3-load-balancer-backend-02.png)

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

   ![Aggiunta di un probe](./media/load-balancer-get-started-internet-portal/4-load-balancer-probes.png)

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
    
    ![Aggiunta di una regola di bilanciamento del carico](./media/load-balancer-get-started-internet-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>Testare il servizio di bilanciamento del carico
1. Trovare l'indirizzo IP pubblico del servizio di bilanciamento del carico nella schermata **Panoramica**. Fare clic su **Tutte le risorse** e quindi su **myPublicIP**.

2. Copiare l'indirizzo IP pubblico e quindi incollarlo nella barra degli indirizzi del browser. Nel browser verrà visualizzata la pagina predefinita del server Web IIS.

  ![Server Web IIS](./media/load-balancer-get-started-internet-portal/9-load-balancer-test.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse, il servizio di bilanciamento del carico e tutte le risorse correlate. A questo scopo, selezionare il gruppo di risorse che contiene il servizio di bilanciamento del carico e fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva sono stati creati un gruppo di risorse, le risorse di rete e i server back-end. Tali risorse sono state quindi usate per creare un servizio di bilanciamento del carico. Per altre informazioni sui servizi di bilanciamento del carico e sulle risorse associate, passare agli articoli dell'esercitazione.
