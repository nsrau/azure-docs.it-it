---
title: 'Esercitazione: configurare il port forwarding in Azure Load Balancer tramite il portale di Azure'
titlesuffix: Azure Load Balancer
description: Questa esercitazione mostra come configurare il port forwarding con Azure Load Balancer per creare connessioni alle macchine virtuali in una rete virtuale di Azure.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: As an IT administrator, I want to configure port forwarding in Azure Load Balancer to remotely connect to VMs in an Azure virtual network.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: kumud
ms.custom: seodec18
ms.openlocfilehash: 44b9e592d60c541e6b1e7f0e8c639b641af414e4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58118358"
---
# <a name="tutorial-configure-port-forwarding-in-azure-load-balancer-using-the-portal"></a>Esercitazione: configurare il port forwarding in Azure Load Balancer tramite il portale

Il port forwarding permette di connettersi a macchine virtuali (VM) in una rete virtuale di Azure tramite un numero di porta e indirizzo IP pubblico di Azure Load Balancer. 

In questa esercitazione si eseguirà la configurazione del port forwarding in Azure Load Balancer. Si apprenderà come:

> [!div class="checklist"]
> * Creare un servizio di bilanciamento del carico standard pubblico per bilanciare il traffico nelle macchine virtuali. 
> * Creare una rete virtuale e macchine virtuali con una regole di gruppo di sicurezza di rete (NSG). 
> * Aggiungere le macchine virtuali al pool di indirizzi back-end del bilanciamento del carico.
> * Creare un probe di integrità per il servizio di bilanciamento del carico e regole del traffico.
> * Creare regole NAT di port forwarding in ingresso di bilanciamento del carico.
> * Installare e configurare IIS nelle macchine virtuali per visualizzare il bilanciamento del carico e il port forwarding in azione.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 

Per tutti i passaggi in questa esercitazione, accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-standard-load-balancer"></a>Creare un'istanza di Load Balancer Standard

Per prima cosa, creare un servizio di bilanciamento del carico standard pubblico in grado di bilanciare il traffico nelle macchine virtuali. Un servizio di bilanciamento del carico standard supporta solo indirizzi IP pubblici standard. Quando si crea un servizio di bilanciamento del carico standard, si crea anche un nuovo indirizzo IP pubblico standard configurato come front-end del bilanciamento del carico, denominato **LoadBalancerFrontEnd** per impostazione predefinita.  

1. Nella parte superiore sinistra dello schermo fare clic su **Crea una risorsa** > **Rete** > **Servizio di bilanciamento del carico**.
2. Nella scheda **Generale** della pagina **Crea servizio di bilanciamento del carico** immettere o selezionare le informazioni seguenti, accettare le impostazioni predefinite per le opzioni rimanenti e quindi selezionare **Rivedi e crea**:

    | Impostazione                 | Valore                                              |
    | ---                     | ---                                                |
    | Sottoscrizione               | Selezionare la propria sottoscrizione.    |    
    | Gruppo di risorse         | Selezionare **Crea nuovo** e digitare *MyResourceGroupLB* nella casella di testo.|
    | NOME                   | *myLoadBalancer*                                   |
    | Region         | Selezionare **Europa occidentale**.                                        |
    | Type          | Selezionare **Pubblica**.                                        |
    | SKU           | Selezionare **Standard**.                          |
    | Indirizzo IP pubblico | Selezionare **Crea nuovo**. |
    | Nome dell'indirizzo IP pubblico              | Digitare *myPublicIP* nella casella di testo.   |
    |Zona di disponibilità| Selezionare **Con ridondanza della zona**.    |
     
    >[!NOTE]
     >Assicurarsi di creare il bilanciamento del carico e tutte le relative risorse in una posizione che supporti le zone di disponibilità. Per altre informazioni, consultare [Aree che supportano la funzionalità Zone di disponibilità di Azure](../availability-zones/az-overview.md#regions-that-support-availability-zones). 

3. Nella scheda **Rivedi e crea** fare clic su **Crea**.  
  
## <a name="create-and-configure-back-end-servers"></a>Creare e configurare i server back-end

Creare una rete virtuale con due macchine virtuali, quindi aggiungere le VM al pool back-end del servizio di bilanciamento del carico. 

### <a name="create-a-virtual-network"></a>Crea rete virtuale

1. In alto a sinistra nel portale selezionare **Crea una risorsa** > **Rete** > **Rete virtuale**.
   
1. Nel riquadro **Crea rete virtuale** digitare o selezionare questi valori:
   
   - **Nome**: Digitare *MyVNet*.
   - **Gruppo di risorse**: selezionare nell'elenco a discesa **Seleziona esistente**, quindi **MyResourceGroupLB**. 
   - **Subnet** > **Nome**: Digitare *MyBackendSubnet*.
   
1. Selezionare **Create**.

   ![Crea rete virtuale](./media/tutorial-load-balancer-port-forwarding-portal/2-load-balancer-virtual-network.png)

### <a name="create-vms-and-add-them-to-the-load-balancer-back-end-pool"></a>Creare le macchine virtuali e aggiungerle al pool back-end del servizio di bilanciamento del carico

1. In altro a sinistra nel portale selezionare **Crea una risorsa** > **Calcolo** > **Windows Server 2016 Datacenter**. 
   
1. In **Crea macchina virtuale** digitare o selezionare i valori seguenti nella scheda **Nozioni di base**:
   - **Sottoscrizione** > **Gruppo di risorse**: Nell'elenco a discesa, selezionare **MyResourceGroupLB**.
   - **Nome macchina virtuale**: digitare *MyVM1*.
   - **Area**: Selezionare **Europa occidentale**. 
   - **Nome utente**: digitare *azureuser*.
   - **Password**: digitare *Azure1234567*. 
     Nel campo **Conferma password** digitare nuovamente la password.
   
1. Selezionare la scheda **Networking**, oppure selezionare **Avanti: Dischi**, quindi **Avanti: Rete**. 
   
   Verificare che siano selezionate le opzioni seguenti:
   - **Rete virtuale**: **MyVNet**
   - **Subnet**: **MyBackendSubnet**
   
1. Sotto **IP pubblico**, selezionare **Crea nuovo**, poi **Standard** nella pagina **Crea indirizzo IP pubblico**, quindi selezionare **OK**. 
   
1. In **Gruppo di sicurezza di rete**, selezionare **Avanzate** per creare un nuovo gruppo di sicurezza di rete (NSG), un tipo di firewall. 
   1. Nel campo **Configura gruppo di sicurezza di rete** selezionare **Crea nuovo**. 
   1. Digitare *MyNetworkSecurityGroup* e selezionare **OK**. 
   
   >[!NOTE]
   >Notare che, per impostazione predefinita, l'NSG dispone già di una regola in ingresso per aprire la porta 3389, la porta del desktop remoto (RDP).
   
1. Aggiungere la macchina virtuale a un pool back-end di bilanciamento del carico creato:
   
   1. In **BILANCIAMENTO DEL CARICO** > **Posizionare questa macchina virtuale prima di una soluzione di bilanciamento del carico esistente?**, selezionare **Sì**. 
   1. In **Opzioni di bilanciamento del carico**, selezionare **Bilanciamento del carico di Azure** nel menù a discesa. 
   1. In **Seleziona un servizio di bilanciamento del carico**, selezionare **MyLoadBalancer** nell'elenco a discesa. 
   1. In **Seleziona un pool back-end**, selezionare **Crea nuovo**, quindi digitare *MyBackendPool*, infine selezionare **Crea**. 
   
   ![Crea rete virtuale](./media/tutorial-load-balancer-port-forwarding-portal/create-vm-networking.png)
   
1. Selezionare la scheda **Gestione** oppure **Avanti** > **Gestione**. In **Monitoraggio** impostare **Diagnostica di avvio** su **Off**.
   
1. Selezionare **Rivedi e crea**.
   
1. Rivedere le impostazioni e, quando la convalida riesce, selezionare **Crea**. 

1. Seguire la procedura per creare una seconda macchina virtuale denominata *MyVM2*, con tutte le altre impostazioni identiche a quelle di MyVM1. 
   
   In **Gruppo di sicurezza di rete**, dopo aver selezionato **Avanzate**, selezionare il **MyNetworkSecurityGroup** già creato nell'elenco a discesa. 
   
   In **Seleziona un pool back-end**, assicurarsi che **MyBackendPool** sia selezionato. 

### <a name="create-an-nsg-rule-for-the-vms"></a>Creare una regola del gruppo di sicurezza di rete per le macchine virtuali

Creare una regola del gruppo di sicurezza di rete (NSG) per far sì che le macchine virtuali consentano le connessioni a internet in ingresso (HTTP).

>[!NOTE]
>Per impostazione predefinita, l'NSG dispone già di una regola per aprire la porta 3389 del desktop remoto (RDP).

1. Nel menu a sinistra selezionare **Tutte le risorse**. Nell'elenco delle risorse selezionare **MyNetworkSecurityGroup** nel gruppo di risorse**MyResourceGroupLB**.
   
1. In **Impostazioni** selezionare **Regole di sicurezza in ingresso**, quindi selezionare **Aggiungi**.
   
1. Nella finestra di dialogo **Aggiungi regola di sicurezza in ingresso**, digitare o selezionare le impostazioni seguenti:
   
   - **Origine**: selezionare **Tag del servizio**.  
   - **Tag del servizio di origine**: selezionare **Internet**. 
   - **Intervalli di porte di destinazione**: digitare *80*.
   - **Protocollo**: selezionare **TCP**. 
   - **Azione**: selezionare **Consenti**.  
   - **Priorità**: digitare *100*. 
   - **Nome**: digitare *MyHTTPRule*. 
   - **Descrizione**: digitare *Allow HTTP*. 
   
1. Selezionare **Aggiungi**. 
   
   ![Creare una regola del gruppo di sicurezza di rete](./media/tutorial-load-balancer-port-forwarding-portal/8-load-balancer-nsg-rules.png)
   
## <a name="create-load-balancer-resources"></a>Creare risorse di bilanciamento del carico

In questa sezione si esamina il pool back-end di bilanciamento del carico e si configurano un probe di integrità per il servizio di bilanciamento del carico e regole del traffico.

### <a name="view-the-back-end-address-pool"></a>Visualizzare il pool di indirizzi back-end

Per distribuire il traffico alle macchine virtuali, il bilanciamento del carico usa un pool di indirizzi back-end che contiene gli indirizzi IP delle schede di interfaccia di rete virtuale (NIC) connesse al bilanciamento del carico. 

È stato creato il pool di back-end di bilanciamento del carico, mentre le macchine virtuali sono state aggiunte al pool durante la loro creazione. È anche possibile creare pool back-end e aggiungere o rimuovere macchine virtuali dalla pagina **Pool back-end** del servizio di bilanciamento del carico. 

1. Selezionare **Tutte le risorse** dal menu a sinistra e quindi selezionare **MyLoadBalancer** dall'elenco delle risorse.
   
1. In **Impostazioni** selezionare **Pool back-end**.
   
1. Nella pagina **Pool back-end** espandere **MyBackendPool** e verificare che siano elencate entrambe le macchine virtuali **VM1** e **VM2**.

1. Selezionare **MyBackendPool**. 
   
   Nella pagina **MyBackendPool**, in **MACCHINA VIRTUALE** e **INDIRIZZO IP**, è possibile rimuovere o aggiungere le macchine virtuali disponibili al pool.

È possibile creare pool back-end nuovi selezionando **Aggiungi** nella pagina **Pool back-end**.

### <a name="create-a-health-probe"></a>Creare un probe di integrità

Per consentire al servizio di bilanciamento del carico di monitorare lo stato delle macchine virtuali, si usa un probe di integrità. Il probe di integrità aggiunge o rimuove in modo dinamico le VM nella rotazione del servizio di bilanciamento del carico in base alla rispettiva risposta ai controlli di integrità. 

1. Selezionare **Tutte le risorse** dal menu a sinistra e quindi selezionare **MyLoadBalancer** dall'elenco delle risorse.
   
1. In **Impostazioni** selezionare **Probe integrità**, quindi selezionare **Aggiungi**.
   
1. Nella pagina **Aggiungi probe integrità** digitare o selezionare i valori seguenti:
   
   - **Nome**: digitare *MyHealthProbe*.
   - **Protocollo**: Nell'elenco a discesa selezionare **HTTP**. 
   - **Porta**: digitare *80*. 
   - **Percorso**: Accettare */* come URI predefinito. È possibile sostituire questo valore con qualsiasi altro URI. 
   - **Intervallo**: Digitare *15*. L'intervallo specifica il numero di secondi tra i tentativi del probe.
   - **Soglia non integra**: Digitare *2*. Questa impostazione specifica il numero di errori di probe consecutivi che si verificano prima che una macchina virtuale venga considerata non integra.
   
1. Selezionare **OK**.
   
   ![Aggiungere un probe](./media/tutorial-load-balancer-port-forwarding-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Creare una regola di bilanciamento del carico

Una regola di bilanciamento del carico definisce come verrà distribuito il traffico alle macchine virtuali. La regola definisce la configurazione IP front-end per il traffico in ingresso, il pool di indirizzi IP back-end per ricevere il traffico e le porte di origine e di destinazione necessarie. 

La regola di bilanciamento del carico denominata **MyLoadBalancerRule** rimane in ascolto sulla porta 80 nel front-end **LoadBalancerFrontEnd**. La regola consente l'invio del traffico di rete al pool di indirizzi back-end **MyBackendPool**, sempre sulla porta 80. 

1. Selezionare **Tutte le risorse** dal menu a sinistra e quindi selezionare **MyLoadBalancer** dall'elenco delle risorse.
   
1. In **Impostazioni** selezionare **Regole di bilanciamento del carico**, quindi selezionare **Aggiungi**.
   
1. Nella pagina **Aggiungi regola di bilanciamento del carico** digitare o selezionare i valori seguenti:
   
   - **Nome**: digitare *MyLoadBalancerRule*.
   - **Protocollo**: selezionare **TCP**.
   - **Porta**: Digitare *80*.
   - **Porta back-end**: Digitare *80*.
   - **Pool back-end**: Selezionare **MyBackendPool**.
   - **Probe di integrità**: Selezionare **MyHealthProbe**. 
   
1. Selezionare **OK**.
   
   ![Aggiungere una regola di bilanciamento del carico](./media/tutorial-load-balancer-port-forwarding-portal/5-load-balancing-rules.png)

## <a name="create-an-inbound-nat-port-forwarding-rule"></a>Creare una regola NAT di port forwarding in ingresso

Creare una regola del Network Address Translation (NAT) in ingresso del bilanciamento del carico per trasferire il traffico da una porta specifica dell'indirizzo IP front-end a una porta specifica della macchina virtuale back-end.

1. Selezionare **Tutte le risorse** nel menu a sinistra, quindi selezionare **MyLoadBalancer** dall'elenco delle risorse.
   
1. In **Impostazioni**, selezionare **Regole NAT in ingresso**, quindi selezionare **Aggiungi**. 
   
1. Nella pagina **Aggiungi regola NAT in ingresso**, digitare o selezionare i valori seguenti:
   
   - **Nome**: digitare *MyNATRuleVM1*.
   - **Porta**: digitare *4221*.
   - **Macchina virtuale di destinazione**: Selezionare **MyVM1** nell'elenco a discesa.
   - **Mapping delle porte**: selezionare **Personalizzato**.
   - **Porta di destinazione**: digitare *3389*.
   
1. Selezionare **OK**.
   
1. Ripetere i passaggi per aggiungere una regola NAT in ingresso denominata *MyNATRuleVM2*, usando **Porta**: *4222* e **Macchina virtuale di destinazione**: **MyVM2**.

## <a name="test-the-load-balancer"></a>Testare il servizio di bilanciamento del carico

In questa sezione, si effettuerà l'installazione di Internet Information Services (IIS) nei server back-end, nonché la personalizzazione della pagina web predefinita per visualizzare il nome del computer. A questo punto, si effettuerà un test del bilanciamento del carico usando il suo indirizzo IP pubblico. 

Ogni macchina virtuale back-end viene usata per una versione diversa della pagina Web predefinita di IIS, consentendo così di visualizzare le richieste di distribuzione di bilanciamento del carico tra le due macchine virtuali.

### <a name="connect-to-the-vms-with-rdp"></a>Stabilire una connessione RDP alle macchine virtuali

Connettersi a ogni macchina virtuale tramite Desktop remoto (RDP). 

1. Nel portale selezionare **Tutte le risorse** dal menu a sinistra. Dall'elenco delle risorse selezionare ogni macchina virtuale nel gruppo di risorse **MyResourceGroupLB**.
   
1. Nella pagina **Panoramica** selezionare **Connetti** e quindi **Scarica file RDP**. 
   
1. Aprire il file RDP scaricato e selezionare **Connetti**.
   
1. Nella schermata Sicurezza di Windows selezionare **Altre opzioni** e quindi **Usa un altro account**. 
   
   Immettere il nome utente *azureuser* e la password *Azure1234567* e quindi selezionare **OK**.
   
1. Rispondere **Sì** all'eventuale messaggio di richiesta di certificato. 
   
   Il desktop della macchina virtuale viene aperto in una nuova finestra. 

### <a name="install-iis-and-replace-the-default-iis-web-page"></a>Installare IIS e sostituire la pagina web predefinita di IIS 

Usare PowerShell per installare IIS e sostituire la pagina web predefinita di IIS con una pagina in cui è visibile il nome della macchina virtuale.

1. In MyVM1 e MyVM2 avviare **Windows PowerShell** da menu **Start**. 

2. Eseguire i comandi seguenti per installare IIS e sostituire la pagina Web predefinita di IIS:
   
   ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    
   ```
   
1. Chiudere le connessioni RDP con MyVM1 e MyVM2 selezionando **Disconnetti**. Non arrestare le macchine virtuali.

### <a name="test-load-balancing"></a>Testare il bilanciamento del carico

1. Nel portale, nella pagina di **Panoramica** per **MyLoadBalancer**, copiare l'indirizzo IP pubblico in **Indirizzo IP pubblico**. Passare il puntatore sull'indirizzo e selezionare l'icona **Copia** per copiarlo. In questo esempio, l'indirizzo è **40.67.218.235**. 
   
1. Incollare o digitare l'indirizzo IP pubblico del servizio di bilanciamento del carico (*40.67.218.235*) nella barra degli indirizzi del browser internet. 
   
   Nel browser viene visualizzata la pagina predefinita del server Web IIS che è stata personalizzata. Il testo del messaggio è **Hello World from MyVM1** o **Hello World from MyVM2**.
   
   ![Nuova pagina predefinita di IIS](./media/tutorial-load-balancer-port-forwarding-portal/9-load-balancer-test.png) 
   
1. Aggiornare il browser per visualizzare la distribuzione del traffico tra le macchine virtuali eseguita dal servizio di bilanciamento del carico. Mentre il servizio di bilanciamento del carico distribuisce le richieste a ogni macchina virtuale back-end, alcune volte viene visualizzata la pagina di **MyVM1** e altre volte quella di **MyVM2**.
   
   >[!NOTE]
   >Tra un tentativo e l'altro, potrebbe essere necessario svuotare la cache o aprire una nuova finestra del browser.

## <a name="test-port-forwarding"></a>Verificare il port forwarding

Con il port forwarding, è possibile connettersi a una macchina virtuale back-end tramite desktop remoto usanto l'indirizzo IP del bilanciamento del carico e il valore della porta front-end definita nella regola NAT. 

1. Nel portale, nella pagina di **Panoramica** di **MyLoadBalancer**, copiare il relativo indirizzo IP pubblico. Passare il puntatore sull'indirizzo e selezionare l'icona **Copia** per copiarlo. In questo esempio, l'indirizzo è **40.67.218.235**. 
   
1. Aprire un prompt dei comandi e usare il comando seguente per creare una sessione di accesso in remoto con MyVM2, usando l'indirizzo IP pubblico del bilanciamento del carico e la porta front-end definita nella regola NAT della macchina virtuale. 
   
   ```
   mstsc /v:40.67.218.235:4222
   ```
  
1. Aprire il file con estensione RDP scaricato e selezionare **Connetti**.
   
1. Nella schermata Sicurezza di Windows selezionare **Altre opzioni** e quindi **Usa un altro account**. 
   
   Immettere il nome utente *azureuser* e la password *Azure1234567* e quindi selezionare **OK**.
   
1. Rispondere **Sì** all'eventuale messaggio di richiesta di certificato. 
   
   Il desktop MyVM2 si apre in una nuova finestra. 

La connessione RDP ha esito positivo, perché la regola NAT in ingresso **MyNATRuleVM2** indirizza il traffico dalla porta front-end 4222 del bilanciamento del carico alla porta 3389 di MyVM2 (porta RDP).

## <a name="clean-up-resources"></a>Pulire le risorse

Per eliminare il servizio di bilanciamento del carico e tutte le risorse correlate quando non sono più necessari, aprire il gruppo di risorse **MyResourceGroupLB** e selezionare **Elimina gruppo di risorse**.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione ha illustrato come creare un servizio di bilanciamento del carico pubblico standard. Si sono eseguite le procedure per creare e configurare le risorse di rete, i server back-end, un probe di integrità e le regole di bilanciamento del carico. È inoltre stato installato IIS nelle macchine virtuali back-end, mentre il bilanciamento del carico è stato testato usando il suo indirizzo IP pubblico. È stato configurato e testato il port forwarding da una porta specifica nel bilanciamento del carico a una porta in una macchina virtuale back-end. 

Per altre informazioni su Azure Load Balancer, proseguire con altre esercitazioni sul bilanciamento del carico.

> [!div class="nextstepaction"]
> [Esercitazioni su Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
