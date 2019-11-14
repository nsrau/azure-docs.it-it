---
title: 'Guida introduttiva: creare un Load Balancer di base pubblico-portale di Azure'
titleSuffix: Azure Load Balancer
description: In questo avvio rapido si apprende come creare un servizio di bilanciamento del carico Basic pubblico tramite il portale di Azure.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a Basic Load balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 3cbb4271909cf739dc3ce13712e388f2fc8e20a5
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048694"
---
# <a name="quickstart-create-a-basic-load-balancer-by-using-the-azure-portal"></a>Guida introduttiva: creare un Load Balancer di base usando il portale di Azure

Il bilanciamento del carico offre un livello più elevato di disponibilità e scalabilità distribuendo le richieste in ingresso tra le macchine virtuali. È possibile usare il portale di Azure per creare un servizio di bilanciamento del carico e bilanciare il traffico tra le macchine virtuali. Questo argomento di avvio rapido illustra come creare e configurare un servizio di bilanciamento del carico, i server back-end e le risorse di rete con il piano tariffario Basic.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 

Per eseguire le attività di questo avvio rapido, accedere al [portale di Azure](https://portal.azure.com).

## <a name="create-a-basic-load-balancer"></a>Creare un servizio di bilanciamento del carico di base

Creare prima di tutto un servizio Load Balancer Basic pubblico usando il portale. Il nome e l'indirizzo IP pubblico specificati vengono configurati automaticamente come front-end del servizio di bilanciamento del carico.

1. Nella parte superiore sinistra dello schermo fare clic su **Crea una risorsa** > **Rete** > **Servizio di bilanciamento del carico**.
2. Nella scheda **Generale** della pagina **Crea servizio di bilanciamento del carico** immettere o selezionare le informazioni seguenti, accettare le impostazioni predefinite per le opzioni rimanenti e quindi selezionare **Rivedi e crea**:

    | Impostazione                 | Valore                                              |
    | ---                     | ---                                                |
    | sottoscrizione               | Selezionare la propria sottoscrizione.    |    
    | Resource group         | Selezionare **Crea nuovo** e digitare *MyResourceGroupLB* nella casella di testo.|
    | Nome                   | *myLoadBalancer*                                   |
    | Area         | Selezionare **Europa occidentale**.                                        |
    | digitare          | Selezionare **Pubblico**.                                        |
    | Sku           | Selezionare **Basic**.                          |
    | Indirizzo IP pubblico | Selezionare **Crea nuovo**. |
    | Nome dell'indirizzo IP pubblico              | *MyPublicIP*   |
    | Assegnazione| Static|

3. Nella scheda **Rivedi e crea** fare clic su **Crea**.   


## <a name="create-back-end-servers"></a>Creare i server back-end

Creare successivamente una rete virtuale e due macchine virtuali per il pool back-end del servizio di bilanciamento del carico Basic. 

### <a name="create-a-virtual-network"></a>Crea rete virtuale

1. In alto a sinistra nel portale selezionare **Crea una risorsa** > **Rete** > **Rete virtuale**.
   
1. Nel riquadro **Crea rete virtuale** digitare o selezionare questi valori:
   
   - **Nome**: digitare *MyVnet*.
   - **Gruppo di risorse**: selezionare l'elenco a discesa **Seleziona esistente** e quindi **MyResourceGroupLB**. 
   - **Subnet** > **Nome**: digitare *MyBackendSubnet*.
   
1. Selezionare **Create**.

   ![Crea rete virtuale](./media/load-balancer-get-started-internet-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>Creare macchine virtuali

1. In altro a sinistra nel portale selezionare **Crea una risorsa** > **Calcolo** > **Windows Server 2016 Datacenter**. 
   
1. In **Crea macchina virtuale** digitare o selezionare i valori seguenti nella scheda **Nozioni di base**:
   - **Sottoscrizione** > **Gruppo di risorse**: selezionare **MyResourceGroupLB** dall'elenco a discesa.
   - **Dettagli dell'istanza** > **Nome macchina virtuale**: digitare *MyVM1*.
   - **Dettagli dell'istanza** > **Opzioni di disponibilità**: 
     1. Selezionare **Set di disponibilità** dall'elenco a discesa. 
     2. Selezionare **Crea nuovo**, digitare *MyAvailabilitySet* e quindi selezionare **OK**.
  
1. Selezionare la scheda **Rete** oppure selezionare **Avanti: Dischi** e quindi **Avanti: Rete**. 
   
   Verificare che siano selezionate le opzioni seguenti:
   - **Rete virtuale**: **MyVnet**
   - **Subnet**: **MyBackendSubnet**
   - **IP pubblico**: **MyVM1-ip**
   
   Per creare un nuovo gruppo di sicurezza di rete (NSG), un tipo di firewall, in **Gruppo di sicurezza di rete** selezionare **Avanzate**. 
   1. Nel campo **Configura gruppo di sicurezza di rete** selezionare **Crea nuovo**. 
   1. Digitare *MyNetworkSecurityGroup* e selezionare **OK**. 
   
1. Selezionare la scheda **Gestione** oppure **Avanti** > **Gestione**. In **Monitoraggio** impostare **Diagnostica di avvio** su **Off**.
   
1. Selezionare **Rivedi e crea**.
   
1. Rivedere le impostazioni e quindi selezionare **Crea**. 

1. Seguire la procedura per creare una seconda macchina virtuale denominata *MyVM2*, con l'indirizzo **IP pubblico** *MyVM2-ip* e tutte le altre impostazioni identiche a quelle di MyVM1. 

### <a name="create-nsg-rules-for-the-vms"></a>Creare regole NSG per le macchine virtuali

In questa sezione si creano le regole del gruppo di sicurezza di rete (NSG) per le macchine virtuali, per consentire le connessioni HTTP (traffico Internet in ingresso) e RDP (desktop remoto).

1. Nel menu a sinistra selezionare **Tutte le risorse**. Nell'elenco delle risorse selezionare **MyNetworkSecurityGroup** nel gruppo di risorse**MyResourceGroupLB**.
   
1. In **Impostazioni** selezionare **Regole di sicurezza in ingresso**, quindi selezionare **Aggiungi**.
   
1. Nella finestra di dialogo **Aggiungi regola di sicurezza in ingresso**, per la regola HTTP, digitare o selezionare le impostazioni seguenti:
   
   - **Origine**: selezionare **Service Tag** (Tag del servizio).  
   - **Tag del servizio di origine**: selezionare **Internet**. 
   - **Intervalli di porte di destinazione**: digitare *80*.
   - **Protocollo**: selezionare **TCP**. 
   - **Azione**: selezionare **Consenti**.  
   - **Priorità**: digitare *100*. 
   - **Nome**: digitare *MyHTTPRule*. 
   - **Descrizione**: digitare *Allow HTTP*. 
   
1. Selezionare **Aggiungi**. 
   
   ![Creare una regola del gruppo di sicurezza di rete](./media/load-balancer-get-started-internet-portal/8-load-balancer-nsg-rules.png)
   
1. Ripetere i passaggi per la regola RDP in ingresso, con i valori seguenti diversi:
   - **Intervalli di porte di destinazione**: digitare *3389*.
   - **Priorità**: digitare *200*. 
   - **Nome**: digitare *MyRDPRule*. 
   - **Descrizione**: digitare *Allow RDP*. 

## <a name="create-resources-for-the-load-balancer"></a>Creare le risorse del servizio di bilanciamento del carico

In questa sezione si configurano le impostazioni del servizio di bilanciamento del carico per un pool di indirizzi back-end, un probe di integrità e una regola di bilanciamento del carico.

### <a name="create-a-backend-address-pool"></a>Creare un pool di indirizzi back-end

Per distribuire il traffico alle macchine virtuali, il servizio di bilanciamento del carico usa un pool di indirizzi back-end che include gli indirizzi IP delle schede di interfaccia di rete virtuale connesse al bilanciamento del carico. 

**Per creare un pool di indirizzi back-end che include VM1 e VM2:**

1. Selezionare **Tutte le risorse** dal menu a sinistra e quindi selezionare **MyLoadBalancer** dall'elenco delle risorse.
   
1. In **Impostazioni** selezionare **Pool di back-end**, quindi selezionare **Aggiungi**.
   
1. Nella pagina **Aggiungi un pool back-end** digitare o selezionare i valori seguenti:
   
   - **Nome**: digitare *MyBackEndPool*.
   - **Associato a**: selezionare **Set di disponibilità** dall'elenco a discesa.
   - **Set di disponibilità**: selezionare **MyAvailabilitySet**.
   
1. Selezionare **Aggiungi una configurazione IP della rete di destinazione**. 
   1. Aggiungere ogni macchina virtuale (**MyVM1** e **MyVM2**) creata al pool back-end.
   2. Dopo avere aggiunto ogni macchina, selezionare l'opzione **Configurazione IP della rete** corrispondente dall'elenco a discesa. 
   
1. Selezionare **OK**.
   
   ![Aggiungere il pool di indirizzi back-end](./media/load-balancer-get-started-internet-portal/3-load-balancer-backend-02.png)
   
1. Nella pagina **Pool back-end** espandere **MyBackendPool** e verificare che siano elencate entrambe le macchine virtuali **VM1** e **VM2**.

### <a name="create-a-health-probe"></a>Creare un probe di integrità

Per consentire al servizio di bilanciamento del carico di monitorare lo stato delle macchine virtuali, si usa un probe di integrità. Il probe di integrità aggiunge o rimuove in modo dinamico le VM nella rotazione del servizio di bilanciamento del carico in base alla rispettiva risposta ai controlli di integrità. 

**Per creare un probe di integrità per monitorare l'integrità delle macchine virtuali:**

1. Selezionare **Tutte le risorse** dal menu a sinistra e quindi selezionare **MyLoadBalancer** dall'elenco delle risorse.
   
1. In **Impostazioni** selezionare **Probe integrità**, quindi selezionare **Aggiungi**.
   
1. Nella pagina **Aggiungi probe integrità** digitare o selezionare i valori seguenti:
   
   - **Nome**: digitare *MyHealthProbe*.
   - **Protocollo**: selezionare **HTTP** dall'elenco a discesa. 
   - **Porta**: digitare *80*. 
   - **Percorso**: accettare */* come URI predefinito. È possibile sostituire questo valore con qualsiasi altro URI. 
   - **Intervallo**: digitare *15*. L'intervallo specifica il numero di secondi tra i tentativi del probe.
   - **Soglia di non integrità**: digitare *2*. Questa impostazione specifica il numero di errori di probe consecutivi che si verificano prima che una macchina virtuale venga considerata non integra.
   
1. Selezionare **OK**.
   
   ![Aggiungere un probe](./media/load-balancer-get-started-internet-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Creare una regola di bilanciamento del carico

Una regola di bilanciamento del carico definisce come verrà distribuito il traffico alle macchine virtuali. La regola definisce la configurazione IP front-end per il traffico in ingresso, il pool di indirizzi IP back-end per ricevere il traffico e le porte di origine e di destinazione necessarie. 

La regola di bilanciamento del carico denominata **MyLoadBalancerRule** rimane in ascolto sulla porta 80 nel front-end **LoadBalancerFrontEnd**. La regola consente l'invio del traffico di rete al pool di indirizzi back-end **MyBackEndPool**, sempre sulla porta 80. 

**Per creare la regola di bilanciamento del carico:**


1. Selezionare **Tutte le risorse** dal menu a sinistra e quindi selezionare **MyLoadBalancer** dall'elenco delle risorse.
   
1. In **Impostazioni** selezionare **Regole di bilanciamento del carico**, quindi selezionare **Aggiungi**.
   
1. Nella pagina **Aggiungi regola di bilanciamento del carico** digitare o selezionare i valori seguenti:
   
   - **Nome**: digitare *MyLoadBalancerRule*.
   - **Indirizzo IP front-end**: digitare *LoadBalancerFrontend*.
   - **Protocollo**: selezionare **TCP**.
   - **Porta**: digitare *80*.
   - **Porta back-end**: digitare *80*.
   - **Pool back-end**: selezionare **MyBackendPool**.
   - **Probe integrità**: selezionare **MyHealthProbe**. 
   
1. Selezionare **OK**.
   
   ![Aggiungere una regola di bilanciamento del carico](./media/load-balancer-get-started-internet-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>Testare il servizio di bilanciamento del carico

Si userà l'indirizzo IP pubblico per testare il servizio di bilanciamento del carico sulle macchine virtuali. 

Nel portale, nella pagina **Panoramica** per **MyLoadBalancer**, trovare il relativo indirizzo IP pubblico in **Indirizzo IP pubblico**. Passare il puntatore sull'indirizzo e selezionare l'icona **Copia** per copiarlo. 

### <a name="install-iis-on-the-vms"></a>Installare IIS nelle macchine virtuali

Installare Internet Information Services (IIS) nelle macchine virtuali per testare il servizio di bilanciamento del carico.

**Per stabilire una connessione RDP alla macchina virtuale:**

1. Nel portale selezionare **Tutte le risorse** dal menu a sinistra. Dall'elenco delle risorse selezionare**MyVM1** nel gruppo di risorse **MyResourceGroupLB**.
   
1. Nella pagina **Panoramica** selezionare **Connetti** e quindi **Scarica file RDP**. 
   
1. Aprire il file RDP scaricato e selezionare **Connetti**.
   
1. Nella schermata Sicurezza di Windows selezionare **Altre opzioni** e quindi **Usa un altro account**. 
   
   Immettere nome utente e password e selezionare **OK**.
   
1. Rispondere **Sì** all'eventuale messaggio di richiesta di certificato. 
   
   Il desktop della macchina virtuale viene aperto in una nuova finestra. 
   
**Per installare IIS**

1. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine nell'elenco di risorse selezionare **myVM1**, che si trova nel gruppo di risorse *myResourceGroupSLB*.
2. Nella pagina **Panoramica** selezionare **Connetti** per connettersi a RDP nella macchina virtuale.
5. Accedere alla VM con le credenziali fornite durante la creazione di questa VM. Verrà avviata una sessione Desktop remoto con la macchina virtuale *myVM1*.
6. Nel desktop del server passare a **Strumenti di amministrazione Windows**>**Windows PowerShell**.
7. Nella finestra di PowerShell eseguire i comandi seguenti per installare il server IIS, rimuovere il file predefinito iisstart.htm e aggiungere un nuovo file iisstart.htm che visualizza il nome della macchina virtuale:

   ```azurepowershell
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
    remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
6. Chiudere la sessione RDP con *myVM1*.
7. Ripetere i passaggi da 1 a 6 per installare IIS e il file iisstart.htm aggiornato in *myVM2*.
   
1. Ripetere i passaggi per la macchina virtuale **MyVM2**, impostando però il server di destinazione su **MyVM2**.

### <a name="test-the-load-balancer"></a>Testare il servizio di bilanciamento del carico

Aprire un Web browser e incollare l'indirizzo IP pubblico del servizio di bilanciamento del carico nella barra degli indirizzi del browser. Nel browser verrà visualizzata la pagina predefinita del server Web IIS.

![Server Web IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Per verificare la distribuzione del traffico tra entrambe le VM che eseguono l'app da parte del servizio di bilanciamento del carico, forzare l'aggiornamento del Web browser.
## <a name="clean-up-resources"></a>Pulire le risorse

Per eliminare il servizio di bilanciamento del carico e tutte le risorse correlate quando non sono più necessari, aprire il gruppo di risorse **MyResourceGroupLB** e selezionare **Elimina gruppo di risorse**.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stato creato un servizio di bilanciamento del carico di livello Basic. Si è appreso come creare e configurare un gruppo di risorse, le risorse di rete, i server back-end, un probe di integrità e le regole da usare con il servizio di bilanciamento del carico. Si è installato IIS nelle macchine virtuali e lo si è usato per testare il servizio di bilanciamento del carico. 

Per altre informazioni su Azure Load Balancer, passare alle esercitazioni.

> [!div class="nextstepaction"]
> [Esercitazioni su Azure Load Balancer](tutorial-load-balancer-basic-internal-portal.md)
