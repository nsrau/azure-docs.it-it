---
title: 'Avvio rapido: Creare un servizio di bilanciamento del carico Basic pubblico tramite il portale di Azure | Microsoft Docs'
description: In questo avvio rapido si apprende come creare un servizio di bilanciamento del carico Basic pubblico tramite il portale di Azure.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to create a Basic Load balancer so that I can load balance internet traffic to VMs.
ms.assetid: aa9d26ca-3d8a-4a99-83b7-c410dd20b9d0
ms.service: load-balancer
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 523f5eba632b15eaaf45f24be820f7b255aae7c0
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2018
ms.locfileid: "51616028"
---
# <a name="quickstart-create-a-public-basic-load-balancer-by-using-the-azure-portal"></a>Avvio rapido: Creare un servizio di bilanciamento del carico Basic pubblico tramite il portale di Azure

Il bilanciamento del carico offre un livello più elevato di disponibilità e scalabilità distribuendo le richieste in ingresso tra più macchine virtuali. È possibile usare il portale di Azure per creare un servizio di bilanciamento del carico dedicato alle macchine virtuali. In questo avvio rapido si apprende come creare risorse di rete, server back-end e un servizio di bilanciamento del carico con il piano tariffario Basic.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Per tutte le attività di questo avvio rapido, accedere al [portale di Azure](http://portal.azure.com).

## <a name="create-a-basic-load-balancer"></a>Creare un servizio di bilanciamento del carico Basic

In questa sezione viene creato un servizio di bilanciamento del carico Basic pubblico tramite il portale. L'indirizzo IP pubblico viene configurato automaticamente come front-end del servizio di bilanciamento del carico quando si creano l'indirizzo IP pubblico e la risorsa di bilanciamento del carico usando il portale. Il nome del front-end è **myLoadBalancer**.

1. In alto a sinistra nel portale selezionare **Crea una risorsa** > **Rete** > **Load Balancer**.
2. Nel riquadro **Crea servizio di bilanciamento del carico**, immettere i valori seguenti:
   - **myLoadBalancer** come nome del servizio di bilanciamento del carico.
   - **Pubblico** come tipo di bilanciamento del carico. 
   - **myPublicIP** come indirizzo IP pubblico da creare con **SKU** impostato su **Basic** e **Assegnazione** impostata su **Dinamico**.
   - **myResourceGroupLB** come nome del nuovo gruppo di risorse.
3. Selezionare **Create**.
   
![Creare un servizio di bilanciamento del carico](./media/load-balancer-get-started-internet-portal/1-load-balancer.png)


## <a name="create-back-end-servers"></a>Creare i server back-end

In questa sezione viene creata una rete virtuale e due macchine virtuali per il pool back-end del servizio di bilanciamento del carico Basic. Viene quindi installato Internet Information Services (IIS) nelle macchine virtuali per testare il servizio di bilanciamento del carico.

### <a name="create-a-virtual-network"></a>Crea rete virtuale
1. In alto a sinistra nel portale selezionare **Nuova** > **Rete** > **Rete virtuale**.
2. Nel riquadro **Crea rete virtuale** immettere i valori seguenti e quindi selezionare **Crea**:
   - **myVnet** come nome della rete virtuale.
   - **myResourceGroupLB** come nome del gruppo di risorse esistente.
   - **myBackendSubnet** come nome della subnet.

   ![Crea rete virtuale](./media/load-balancer-get-started-internet-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>Creare macchine virtuali

1. In alto a sinistra nel portale selezionare **Nuovo** > **Calcolo** > **Windows Server 2016 Datacenter**. 
2. Immettere i valori seguenti per la macchina virtuale, quindi selezionare **OK**:
   - **myVM1** come nome della macchina virtuale.        
   - **azureuser** come nome utente dell'amministratore.    
   - **myResourceGroupLB** come nome del gruppo di risorse. In **Gruppo di risorse** selezionare **Use existing** (Usa esistente) e quindi **myResourceGroupLB**.   
3. Selezionare **DS1_V2** come dimensioni per la macchina virtuale e fare clic su **Seleziona**.
4. Immettere i valori seguenti per le impostazioni della macchina virtuale:
   - **myAvailabilitySet** come nome del nuovo set di disponibilità creato.
   - **myVnet** come nome della rete virtuale. Verificare che sia selezionato.
   - **myBackendSubnet** come nome della subnet. Verificare che sia selezionato.
   - **myVM1-ip** come indirizzo IP pubblico.
   - **myNetworkSecurityGroup** come nome del nuovo gruppo di sicurezza di rete (NSG, un tipo di firewall) che è necessario creare.
5. Selezionare **Disabilitato** per disabilitare la diagnostica di avvio.
6. Fare clic su **OK**, verificare le impostazioni nella pagina di riepilogo e quindi selezionare **Crea**.
7. Seguendo i passaggi da 1 a 6, creare una seconda macchina virtuale denominata **VM2**, con:
   - **myAvailabilityset** come set di disponibilità.
   - **myVnet** come rete virtuale.
   - **myBackendSubnet** come subnet.
   - **myNetworkSecurityGroup** come gruppo di sicurezza di rete. 

### <a name="create-nsg-rules"></a>Creare le regole NSG

In questa sezione vengono create le regole NSG per consentire connessioni in ingresso che usano HTTP e RDP.

1. Nel menu a sinistra selezionare **Tutte le risorse**. Nell'elenco delle risorse selezionare **myNetworkSecurityGroup** nel gruppo di risorse**myResourceGroupLB**.
2. In **Impostazioni** selezionare **Regole di sicurezza in ingresso**, quindi selezionare **Aggiungi**.
3. Immettere i valori seguenti per la regola di sicurezza in ingresso denominata **myHTTPRule** che consente le connessioni HTTP in ingresso tramite la porta 80. Selezionare **OK**.
   - **Tag del servizio** come **Origine**.
   - **Internet** come **Tag del servizio di origine**.
   - **80** come **Intervalli di porte di destinazione**.
   - **TCP** come **Protocollo**.
   - **Consenti** come **Azione**.
   - **100** come **Priorità**
   - **myHTTPRule** come **Nome**.
   - **Consenti HTTP** come **Descrizione**.
 
   ![Creare una regola del gruppo di sicurezza di rete](./media/load-balancer-get-started-internet-portal/8-load-balancer-nsg-rules.png)
4. Ripetere i passaggi 2 e 3 per creare un'altra regola denominata **myRDPRule** per consentire una connessione RDP in ingresso tramite la porta 3389. Usare i valori seguenti:
   - **Tag del servizio** come **Origine**.
   - **Internet** come **Tag del servizio di origine**.
   - **3389** come **Intervalli di porte di destinazione**.
   - **TCP** come **Protocollo**.
   - **Consenti** come **Azione**.
   - **200** come **Priorità**
   - **myRDPRule** come **Nome**.
   - **Consenti RDP** come **Descrizione**.

   

### <a name="install-iis"></a>Installare IIS

1. Nel menu a sinistra selezionare **Tutte le risorse**. Dall'elenco delle risorse selezionare**myVM1** nel gruppo di risorse**myResourceGroupLB**.
2. Nella pagina **Panoramica** selezionare **Connetti** per connettersi a RDP nella macchina virtuale.
3. Accedere alla macchina virtuale con nome utente **azureuser** e password **Azure123456!**.
4. Nel desktop del server passare a **Strumenti di amministrazione Windows** > **Server Manager**.
5. In Server Manager selezionare **Gestione** e quindi **Aggiungi ruoli e funzionalità**.
   ![Aggiungere il ruolo di Server Manager](./media/load-balancer-get-started-internet-portal/servermanager.png)
6. In Aggiunta guidata ruoli e funzionalità usare i valori seguenti:
   - Nella pagina **Selezione tipo di installazione** fare clic su **Installazione basata su ruoli o basata su funzionalità**.
   - Nella pagina **Selezione server di destinazione** fare clic su **myVM1**.
   - Nella pagina **Selezionare un ruolo del server** selezionare **Server Web (IIS)**.
   - Seguire le istruzioni per completare il resto della procedura guidata. 
7. Ripetere i passaggi da 1 a 6 per la macchina virtuale **myVM2**.

## <a name="create-resources-for-the-basic-load-balancer"></a>Creare le risorse del servizio di bilanciamento del carico Basic

In questa sezione vengono configurate le impostazioni del servizio di bilanciamento del carico per un pool di indirizzi back-end e un probe di integrità. Vengono inoltre specificate le regole NAT e di bilanciamento del carico.


### <a name="create-a-back-end-address-pool"></a>Creare un pool di indirizzi back-end

Per distribuire il traffico alle macchine virtuali, è necessario che un pool di indirizzi back-end contenga gli indirizzi IP delle schede di interfaccia di rete virtuale connesse al bilanciamento del carico. Creare il pool di indirizzi back-end **myBackendPool** per includere **VM1** e **VM2**.

1. Nel menu a sinistra selezionare **Tutte le risorse** e quindi **myLoadBalancer** nell'elenco di risorse.
2. In **Impostazioni** selezionare **Pool di back-end**, quindi selezionare **Aggiungi**.
3. Nella pagina **Aggiungi pool back-end** eseguire le operazioni seguenti e quindi selezionare **OK**:
   - Come **Nome**, immettere **myBackEndPool**.
   - In **Associated to** (Associato a) selezionare **Set di disponibilità** dal menu a discesa.
   - Come **Set di disponibilità** selezionare **myAvailabilitySet**.
   - Selezionare **Aggiungi una configurazione IP della rete di destinazione** per aggiungere ogni macchina virtuale (**myVM1** e **myVM2**) creata nel pool back-end.

   ![Aggiunta al pool di indirizzi back-end](./media/load-balancer-get-started-internet-portal/3-load-balancer-backend-02.png)

3. Verificare che l'impostazione del pool back-end di bilanciamento del carico visualizzi entrambe le macchine virtuali **VM1** e **VM2**.

### <a name="create-a-health-probe"></a>Creare un probe di integrità

Per consentire al servizio di bilanciamento del carico Basic di monitorare lo stato dell'app, viene usato un probe di integrità. Il probe di integrità aggiunge o rimuove in modo dinamico le VM nella rotazione del servizio di bilanciamento del carico in base alla rispettiva risposta ai controlli di integrità. Creare un probe di integrità denominato **myHealthProbe** per monitorare l'integrità delle macchine virtuali.

1. Nel menu a sinistra selezionare **Tutte le risorse** e quindi **myLoadBalancer** nell'elenco di risorse.
2. In **Impostazioni** selezionare **Probe integrità**, quindi selezionare **Aggiungi**.
3. Usare questi valori e quindi selezionare **OK**:
   - **myHealthProbe** come nome del probe di integrità.
   - **HTTP** come tipo di protocollo.
   - **80** come numero della porta.
   - **Healthprobe.aspx** per il percorso dell'URI. È possibile sostituire questo valore con qualsiasi altro URI o mantenere il valore di percorso predefinito **"\\"** per ottenere l'URI predefinito.
   - **15** in **Intervallo** come numero di secondi tra i tentativi del probe.
   - **2** in **Soglia di non integrità** come numero di errori di probe consecutivi che devono verificarsi prima che una macchina virtuale venga considerata non integra.

   ![Aggiunta di un probe](./media/load-balancer-get-started-internet-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Creare una regola di bilanciamento del carico

Per definire il modo in cui il traffico viene distribuito alle macchine virtuali viene usata una regola di bilanciamento del carico. Definire la configurazione IP front-end per il traffico in ingresso e il pool IP back-end che riceve il traffico, insieme alle porte di origine e di destinazione necessarie. 

Creare una regola di bilanciamento del carico denominata **myLoadBalancerRuleWeb** per l'ascolto sulla porta 80 nel front-end **LoadBalancerFrontEnd**. La regola consente anche l'invio di traffico di rete con carico bilanciato al pool di indirizzi back-end **myBackEndPool**, usando sempre la porta 80. 

1. Nel menu a sinistra selezionare **Tutte le risorse** e quindi **myLoadBalancer** nell'elenco di risorse.
2. In **Impostazioni** selezionare **Regole di bilanciamento del carico**, quindi selezionare **Aggiungi**.
3. Usare questi valori e quindi selezionare **OK**:
   - **myHTTPRule** come nome della regola di bilanciamento del carico.
   - **TCP** come tipo di protocollo.
   - **80** come numero della porta.
   - **80** come porta back-end.
   - **myBackendPool** come nome del pool back-end.
   - **myHealthProbe** come nome del probe di integrità.
    
   ![Aggiungere una regola di bilanciamento del carico](./media/load-balancer-get-started-internet-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>Testare il servizio di bilanciamento del carico
1. Individuare l'indirizzo IP pubblico del servizio di bilanciamento del carico nella schermata **Panoramica**. Fare clic su **Tutte le risorse** e quindi selezionare **myPublicIP**.

2. Copiare l'indirizzo IP pubblico e quindi incollarlo nella barra degli indirizzi del browser. Nel browser viene visualizzata la pagina predefinita del server Web IIS.

   ![Server Web IIS](./media/load-balancer-get-started-internet-portal/9-load-balancer-test.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse, il servizio di bilanciamento del carico e tutte le risorse correlate. Selezionare il gruppo di risorse che contiene il servizio di bilanciamento del carico e fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questo avvio rapido si è appreso come creare un gruppo di risorse, le risorse di rete e i server back-end. Tali risorse sono state quindi usate per creare un'istanza di Load Balancer Basic. Per altre informazioni su Azure Load Balancer, passare alle esercitazioni su Azure Load Balancer.

> [!div class="nextstepaction"]
> [Esercitazioni su Azure Load Balancer](tutorial-load-balancer-basic-internal-portal.md)
