---
title: 'Esercitazione: Creare un servizio di bilanciamento del carico interno: portale di Azure'
titlesuffix: Azure Load Balancer
description: Questa esercitazione illustra come creare un servizio di bilanciamento del carico interno Basic tramite il portale di Azure.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internal traffic to virtual machines within a specific zone in a region.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 09a73b5d97e46797dd880f64383fdf03fd591f3d
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68273487"
---
# <a name="tutorial-balance-internal-traffic-load-with-a-basic-load-balancer-in-the-azure-portal"></a>Esercitazione: Bilanciare il carico del traffico interno con un servizio di bilanciamento del carico Basic nel portale di Azure

Il bilanciamento del carico offre un livello più elevato di disponibilità e scalabilità distribuendo le richieste in ingresso tra le macchine virtuali. È possibile usare il portale di Azure per creare un servizio di bilanciamento del carico Basic e bilanciare il traffico interno tra le macchine virtuali. Questa esercitazione illustra come creare e configurare un servizio di bilanciamento del carico interno, i server back-end e le risorse di rete con il piano tariffario Basic.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 

Se si preferisce, è possibile eseguire questi passaggi usando l'[interfaccia della riga di comando di Azure](load-balancer-get-started-ilb-arm-cli.md) o [Azure PowerShell](load-balancer-get-started-ilb-arm-ps.md) in alternativa al portale.

Per eseguire i passaggi in questa esercitazione, accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-vnet-back-end-servers-and-a-test-vm"></a>Creare una rete virtuale, i server back-end e una macchina virtuale di test

Prima di tutto, creare una rete virtuale. Nella rete virtuale creare due macchine virtuali da usare per il pool back-end del servizio di bilanciamento del carico Basic e una terza macchina virtuale da usare per testare il servizio di bilanciamento del carico. 

### <a name="create-a-virtual-network"></a>Crea rete virtuale

1. In alto a sinistra nel portale selezionare **Crea una risorsa** > **Rete** > **Rete virtuale**.
   
1. Nel riquadro **Crea rete virtuale** digitare o selezionare questi valori:
   
   - **Nome**: Digitare *MyVNet*.
   - **Gruppo di risorse**: Selezionare **Crea nuovo**, immettere *MyResourceGroupLB*, quindi selezionare **OK**. 
   - **Subnet** > **Nome**: Digitare *MyBackendSubnet*.
   
1. Selezionare **Create** (Crea).

   ![Crea rete virtuale](./media/tutorial-load-balancer-basic-internal-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>Creare macchine virtuali

1. In altro a sinistra nel portale selezionare **Crea una risorsa** > **Calcolo** > **Windows Server 2016 Datacenter**. 
   
1. In **Crea macchina virtuale** digitare o selezionare i valori seguenti nella scheda **Nozioni di base**:
   - **Sottoscrizione** > **Gruppo di risorse**: Nell'elenco a discesa selezionare **MyResourceGroupLB**.
   - **Dettagli istanza** > **Nome macchina virtuale**: Digitare *MyVM1*.
   - **Dettagli dell'istanza** > **Opzioni di disponibilità**: 
     1. Selezionare **Set di disponibilità** dall'elenco a discesa. 
     2. Selezionare **Crea nuovo**, digitare *MyAvailabilitySet* e quindi selezionare **OK**.
   
1. Selezionare la scheda **Rete**, oppure selezionare **Avanti: Dischi**, quindi **Avanti: Rete**. 
   
   Verificare che siano selezionate le opzioni seguenti:
   - **Rete virtuale**: **MyVNet**
   - **Subnet**: **MyBackendSubnet**
   
   In **Gruppo di sicurezza di rete**:
   1. Selezionare **Advanced** (Avanzate). 
   1. Selezionare l'elenco a discesa **Configura gruppo di sicurezza di rete** e quindi **Nessuno**. 
   
1. Selezionare la scheda **Gestione** oppure **Avanti** > **Gestione**. In **Monitoraggio** impostare **Diagnostica di avvio** su **Off**.
   
1. Selezionare **Rivedi e crea**.
   
1. Rivedere le impostazioni e quindi selezionare **Crea**. 

1. Seguire la procedura per creare una seconda macchina virtuale denominata *MyVM2*, con tutte le altre impostazioni identiche a quelle di MyVM1. 

1. Seguire nuovamente la procedura per creare una terza macchina virtuale denominata *MyTestVM*. 

## <a name="create-a-basic-load-balancer"></a>Creare un servizio di bilanciamento del carico Basic

Creare un servizio di bilanciamento del carico interno Basic usando il portale. Il nome e l'indirizzo IP specificati vengono configurati automaticamente come front-end del servizio di bilanciamento del carico.

1. In alto a sinistra nel portale selezionare **Crea una risorsa** > **Rete** > **Load Balancer**.
   
2. Nella scheda **Generale** della pagina **Crea servizio di bilanciamento del carico** immettere o selezionare le informazioni seguenti, accettare le impostazioni predefinite per le opzioni rimanenti e quindi selezionare **Rivedi e crea**:

    | Impostazione                 | Valore                                              |
    | ---                     | ---                                                |
    | Subscription               | Selezionare la propria sottoscrizione.    |    
    | Resource group         | Selezionare **Crea nuovo** e digitare *MyResourceGroupLB* nella casella di testo.|
    | NOME                   | *myLoadBalancer*                                   |
    | Region         | Selezionare **Europa occidentale**.                                        |
    | Type          | Selezionare **Interno**.                                        |
    | SKU           | Selezionare **Basic**.                          |
    | Rete virtuale           | Selezionare *MyVNet*.                          |    
    | Assegnazione indirizzi IP              | Selezionare **Statico**.   |
    | Indirizzo IP privato|Digitare un indirizzo nello spazio degli indirizzi della rete virtuale e della subnet, per esempio *10.3.0.7*.  |

3. Nella scheda **Rivedi e crea** fare clic su **Crea**. 
   

## <a name="create-basic-load-balancer-resources"></a>Creare le risorse del servizio di bilanciamento del carico Basic

In questa sezione si configurano le impostazioni del servizio di bilanciamento del carico per un pool di indirizzi back-end e un probe di integrità e si specificano le regole di bilanciamento del carico.

### <a name="create-a-back-end-address-pool"></a>Creare un pool di indirizzi back-end

Per distribuire il traffico alle macchine virtuali, il servizio di bilanciamento del carico usa un pool di indirizzi back-end che include gli indirizzi IP delle schede di interfaccia di rete virtuale connesse al bilanciamento del carico. 

**Per creare un pool di indirizzi back-end che include VM1 e VM2:**

1. Selezionare **Tutte le risorse** dal menu a sinistra e quindi selezionare **MyLoadBalancer** dall'elenco delle risorse.
   
1. In **Impostazioni** selezionare **Pool di back-end**, quindi selezionare **Aggiungi**.
   
1. Nella pagina **Aggiungi un pool back-end** digitare o selezionare i valori seguenti:
   
   - **Nome**: Digitare *MyBackEndPool*.
   - **Associato a**: Selezionare **Set di disponibilità** dall'elenco a discesa.
   - **Set di disponibilità**: Selezionare **MyAvailabilitySet**.
   
1. Selezionare **Aggiungi una configurazione IP della rete di destinazione**. 
   1. Aggiungere **MyVM1** e **MyVM2** al pool back-end.
   2. Dopo avere aggiunto ogni macchina, selezionare l'opzione **Configurazione IP della rete** corrispondente dall'elenco a discesa. 
   
   >[!NOTE]
   >Non aggiungere **MyTestVM** al pool. 
   
1. Selezionare **OK**.
   
   ![Aggiungere il pool di indirizzi back-end](./media/tutorial-load-balancer-basic-internal-portal/3-load-balancer-backend-02.png)
   
1. Nella pagina **Pool back-end** espandere **MyBackendPool** e verificare che siano elencate entrambe le macchine virtuali **VM1** e **VM2**.

### <a name="create-a-health-probe"></a>Creare un probe di integrità

Per consentire al servizio di bilanciamento del carico di monitorare lo stato delle macchine virtuali, si usa un probe di integrità. Il probe di integrità aggiunge o rimuove in modo dinamico le VM nella rotazione del servizio di bilanciamento del carico in base alla rispettiva risposta ai controlli di integrità. 

**Per creare un probe di integrità per monitorare l'integrità delle macchine virtuali:**

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
   
   ![Aggiungere un probe](./media/tutorial-load-balancer-basic-internal-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Creare una regola di bilanciamento del carico

Una regola di bilanciamento del carico definisce come verrà distribuito il traffico alle macchine virtuali. La regola definisce la configurazione IP front-end per il traffico in ingresso, il pool di indirizzi IP back-end per ricevere il traffico e le porte di origine e di destinazione necessarie. 

La regola di bilanciamento del carico denominata **MyLoadBalancerRule** rimane in ascolto sulla porta 80 nel front-end **LoadBalancerFrontEnd**. La regola consente l'invio del traffico di rete al pool di indirizzi back-end **MyBackendPool**, sempre sulla porta 80. 

**Per creare la regola di bilanciamento del carico:**

1. Selezionare **Tutte le risorse** dal menu a sinistra e quindi selezionare **MyLoadBalancer** dall'elenco delle risorse.
   
1. In **Impostazioni** selezionare **Regole di bilanciamento del carico**, quindi selezionare **Aggiungi**.
   
1. Nella pagina **Aggiungi regola di bilanciamento del carico** digitare o selezionare i valori seguenti, se non sono già presenti:
   
   - **Nome**: Digitare *MyLoadBalancerRule*.
   - **Indirizzo IP front-end:** Digitare *LoadBalancerFrontEnd* se non presente.
   - **Protocollo**: selezionare **TCP**.
   - **Porta**: Digitare *80*.
   - **Porta back-end**: Digitare *80*.
   - **Pool back-end**: Selezionare **MyBackendPool**.
   - **Probe di integrità**: Selezionare **MyHealthProbe**. 
   
1. Selezionare **OK**.
   
   ![Aggiungere una regola di bilanciamento del carico](./media/tutorial-load-balancer-basic-internal-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>Testare il servizio di bilanciamento del carico

Installare Internet Information Services (IIS) nei server back-end e quindi usare MyTestVM per testare il servizio di bilanciamento del carico usando il relativo indirizzo IP privato. Ogni macchina virtuale back-end viene usata per una versione diversa della pagina Web predefinita di IIS, consentendo così di visualizzare le richieste di distribuzione di bilanciamento del carico tra le due macchine virtuali.

Nel portale, nella pagina **Panoramica** per **MyLoadBalancer**, trovare il relativo indirizzo IP in **Indirizzo IP privato**. Passare il puntatore sull'indirizzo e selezionare l'icona **Copia** per copiarlo. In questo esempio l'indirizzo è **10.3.0.7**. 

### <a name="connect-to-the-vms-with-rdp"></a>Stabilire una connessione RDP alle macchine virtuali

Prima di tutto, connettersi a tutte e tre le macchine virtuali con Desktop remoto (RDP). 

>[!NOTE]
>Per impostazione predefinita, le macchine virtuali hanno già la porta **RDP** aperta per consentire l'accesso tramite Desktop remoto. 

**Per stabilire una connessione RDP alle macchine virtuali:**

1. Nel portale selezionare **Tutte le risorse** dal menu a sinistra. Dall'elenco delle risorse selezionare ogni macchina virtuale nel gruppo di risorse **MyResourceGroupLB**.
   
1. Nella pagina **Panoramica** selezionare **Connetti** e quindi **Scarica file RDP**. 
   
1. Aprire il file RDP scaricato e selezionare **Connetti**.
   
1. Nella schermata Sicurezza di Windows selezionare **Altre opzioni** e quindi **Usa un altro account**. 
   
   Immettere nome utente e password e selezionare **OK**.
   
1. Rispondere **Sì** all'eventuale messaggio di richiesta di certificato. 
   
   Il desktop della macchina virtuale viene aperto in una nuova finestra. 

### <a name="install-iis-and-replace-the-default-iis-page-on-the-back-end-vms"></a>Installare IIS e sostituire la pagina predefinita di IIS nelle macchine virtuali back-end

In ogni server back-end usare PowerShell per installare IIS e sostituire la pagina Web predefinita di IIS con una pagina personalizzata.

>[!NOTE]
>Per installare IIS è possibile usare anche l'**Aggiunta guidata ruoli e funzionalità** in **Server Manager**. 

**Per installare IIS e aggiornare la pagina Web predefinita con PowerShell:**

1. In MyVM1 e MyVM2 avviare **Windows PowerShell** da menu **Start**. 

2. Eseguire i comandi seguenti per installare IIS e sostituire la pagina Web predefinita di IIS:
   
   ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```
1. Chiudere le connessioni RDP con MyVM1 e MyVM2 selezionando **Disconnetti**. Non arrestare le macchine virtuali.

### <a name="test-the-load-balancer"></a>Testare il servizio di bilanciamento del carico

1. In MyTestVM aprire **Internet Explorer** e rispondere **OK** ai messaggi di richiesta di configurazione. 
   
1. Incollare o digitare l'indirizzo IP privato del servizio di bilanciamento del carico (*10.3.0.7*) nella barra degli indirizzi del browser. 
   
   Nel browser viene visualizzata la pagina predefinita del server Web IIS che è stata personalizzata. Il testo del messaggio è **Hello World from MyVM1** o **Hello World from MyVM2**.
   
1. Aggiornare il browser per visualizzare la distribuzione del traffico tra le macchine virtuali eseguita dal servizio di bilanciamento del carico. Può anche essere necessario cancellare la cache del browser tra i vari tentativi.

   Mentre il servizio di bilanciamento del carico distribuisce le richieste a ogni macchina virtuale back-end, alcune volte viene visualizzata la pagina di **MyVM1** e altre volte quella di **MyVM2**. 

   ![Nuova pagina predefinita di IIS](./media/tutorial-load-balancer-basic-internal-portal/9-load-balancer-test.png) 
   
## <a name="clean-up-resources"></a>Pulire le risorse

Per eliminare il servizio di bilanciamento del carico e tutte le risorse correlate quando non sono più necessari, aprire il gruppo di risorse **MyResourceGroupLB** e selezionare **Elimina gruppo di risorse**.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione ha illustrato come creare un servizio di bilanciamento del carico interno di livello Basic. Si sono eseguite le procedure per creare e configurare le risorse di rete, i server back-end, un probe di integrità e le regole di bilanciamento del carico. Si è inoltre installato IIS nelle macchine virtuali back-end e si è usata una macchina virtuale di test per testare il servizio di bilanciamento del carico nel browser. 

È ora possibile apprendere come bilanciare il carico delle macchine virtuali tra zone di disponibilità.

> [!div class="nextstepaction"]
> [Bilanciare il carico delle macchine virtuali tra zone di disponibilità](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
