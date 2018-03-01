---
title: "Bilanciamento del carico in più configurazioni IP in Azure | Microsoft Docs"
description: Bilanciamento del carico tra configurazioni IP primarie e secondarie.
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: na
ms.assetid: 244907cd-b275-4494-aaf7-dcfc4d93edfe
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 02388162ac4d2d86255c0a65d8b94253047f3983
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="load-balancing-on-multiple-ip-configurations-by-using-the-azure-portal"></a>Bilanciamento del carico in più configurazioni IP tramite il portale di Azure

> [!div class="op_single_selector"]
> * [di Microsoft Azure](load-balancer-multiple-ip.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)
> * [Interfaccia della riga di comando](load-balancer-multiple-ip-cli.md)

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Questo articolo illustra come usare Azure Load Balancer con più indirizzi IP in una scheda di interfaccia di rete (NIC, Network Interface Controller) secondaria. Il diagramma seguente illustra lo scenario:

![Scenario del bilanciamento del carico](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

In questo scenario viene usata la configurazione seguente:

- Due macchine virtuali (VM, Virtual Machine) che eseguono Windows.
- Ogni VM ha una scheda di interfaccia di rete primaria e una scheda di interfaccia di rete secondaria.
- Ogni scheda di interfaccia di rete secondaria ha due configurazioni IP.
- Ogni VM ospita due siti Web: contoso.com e fabrikam.com.
- Ogni sito Web è associato a una configurazione IP di una scheda di interfaccia di rete secondaria.
- Azure Load Balancer viene usato per esporre due indirizzi IP front-end, uno per ogni sito Web. Gli indirizzi front-end vengono usati per distribuire il traffico verso la rispettiva configurazione IP per ciascun sito Web.
- Lo stesso numero di porta viene usato per gli indirizzi IP front-end e gli indirizzi IP del pool back-end.

## <a name="prerequisites"></a>Prerequisiti

Questo scenario di esempio presuppone che sia disponibile un gruppo di risorse, denominato **contosofabrikam**, con la configurazione seguente:

- Il gruppo di risorse include una rete virtuale denominata **myVNet**.
- La rete **myVNet** include due macchine virtuali denominate **VM1** e **VM2**.
- VM1 e VM2 si trovano nello stesso set di disponibilità, denominato **myAvailset**. 
- VM1 e VM2 hanno ognuna una scheda di interfaccia di rete primaria, denominata rispettivamente **VM1NIC1** e **VM2NIC1**. 
- VM1 e VM2 hanno ognuna una scheda di interfaccia di rete secondaria, denominata rispettivamente **VM1NIC2** e **VM2NIC2**.

Per altre informazioni sulla creazione di macchine virtuali con più schede di interfaccia di rete, vedere [Creare una macchina virtuale con più schede di interfaccia di rete tramite PowerShell](../virtual-machines/windows/multiple-nics.md).

## <a name="perform-load-balancing-on-multiple-ip-configurations"></a>Eseguire il bilanciamento del carico in più configurazioni IP

Per ottenere lo scenario descritto in questo articolo, completare la procedura seguente.

### <a name="step-1-configure-the-secondary-nics"></a>Passaggio 1: Configurare le schede di interfaccia di rete secondarie

Per ogni VM nella rete virtuale aggiungere la configurazione IP impostata per la scheda di interfaccia di rete secondaria:  

1. Passare al portale di Azure all'indirizzo http://portal.azure.com. Accedere con l'account Azure.

2. Nella parte superiore sinistra della schermata selezionare l'icona **Gruppi di risorse**. Selezionare quindi il gruppo di risorse in cui si trovano le VM, ad esempio **contosofabrikam**. Il riquadro **Gruppi di risorse** visualizza tutte le risorse e le schede di interfaccia di rete per le VM.

3. Per la scheda di interfaccia di rete secondaria di ogni VM, aggiungere una configurazione IP:

    1. Selezionare la scheda di interfaccia di rete secondaria che si vuole configurare.
    
    2. Selezionare **Configurazioni IP**. Nella parte superiore del riquadro successivo selezionare **Aggiungi**.

    3. In **Aggiungi configurazione IP** aggiungere una seconda configurazione IP alla scheda di interfaccia di rete: 

        1. Immettere un nome per la configurazione IP secondaria. Per VM1 e VM2, ad esempio, denominare le configurazioni IP rispettivamente **VM1NIC2-ipconfig2** e **VM2NIC2-ipconfig2**.

        2. Per l'impostazione **Indirizzo IP privato**, **Allocazione** selezionare **Statico**.

        3. Selezionare **OK**.

Quando la seconda configurazione IP della scheda di interfaccia di rete secondaria è completata, la configurazione viene visualizzata tra le **Configurazioni IP** della scheda di interfaccia di rete in questione.

### <a name="step-2-create-the-load-balancer"></a>Passaggio 2: Creare il servizio di bilanciamento del carico

Creare il servizio di bilanciamento del carico per la configurazione:

1. Passare al portale di Azure all'indirizzo http://portal.azure.com. Accedere con l'account Azure.

2. In alto a sinistra nella schermata selezionare **Crea una risorsa** > **Rete** > **Load Balancer**. Quindi selezionare **Crea**.

3. In **Crea servizio di bilanciamento del carico** digitare un nome per il servizio. In questo scenario viene usato il nome **mylb**.

4. In **Indirizzo IP pubblico** creare un nuovo IP pubblico denominato **PublicIP1**.

5. In **Gruppo di risorse** selezionare il gruppo di risorse esistente per le VM, ad esempio, **contosofabrikam**. Selezionare la posizione in cui distribuire il servizio di bilanciamento del carico e quindi selezionare **OK**.

Verrà avviata la distribuzione del servizio di bilanciamento del carico. Il completamento della distribuzione può richiedere alcuni minuti. Al termine della distribuzione, il servizio di bilanciamento del carico viene visualizzato come risorsa nel gruppo di risorse.

### <a name="step-3-configure-the-front-end-ip-pool"></a>Passaggio 3: Configurare il pool di indirizzi IP front-end

Per ognuno dei siti Web (contoso.com e fabrikam.com) configurare il pool di indirizzi IP front-end nel servizio di bilanciamento del carico:

1. Nel portale selezionare **Altri servizi**. Nella casella del filtro digitare **Indirizzo IP pubblico** e quindi selezionare **Indirizzi IP pubblici**. Nella parte superiore del riquadro successivo selezionare **Aggiungi**.

2. Configurare due indirizzi IP pubblici (**PublicIP1** e **PublicIP2**) per entrambi i siti Web (contoso.com e fabrikam.com):

    1. Digitare un nome per l'indirizzo IP front-end.

    2. Per **Gruppo di risorse** selezionare il gruppo di risorse esistente per le VM, ad esempio, **contosofabrikam**.

    3. Per **Percorso** selezionare lo stesso percorso delle macchine virtuali.

    4. Selezionare **OK**.

    Dopo la creazione, i due indirizzi IP pubblici vengono visualizzati con gli indirizzi in **IP pubblico**.

3. <a name="step3-3"></a>Nel portale selezionare **Altri servizi**. Nella casella del filtro digitare **bilanciamento del carico** e quindi selezionare **Bilanciamento del carico**. 

4. Selezionare il servizio di bilanciamento del carico (**mylb**) in cui si vuole aggiungere il pool di indirizzi IP front-end.

5. In **Impostazioni** selezionare **Pool front-end**. Nella parte superiore del riquadro successivo selezionare **Aggiungi**.

6. Digitare un nome per l'indirizzo IP front-end, ad esempio **contosofe** o **fabrikamfe**.

7. <a name="step3-7"></a>Selezionare **Indirizzo IP**. In **Scegli indirizzo IP pubblico** selezionare gli indirizzi IP per il front-end (**PublicIP1** o **PublicIP2**).

8. Creare il secondo indirizzo IP front-end ripetendo la procedura dal <a href="#step3-3">passaggio 3</a> al <a href="#step3-7">passaggio 7</a> in questa sezione.

Dopo la configurazione del pool front-end, gli indirizzi IP vengono visualizzati nelle impostazioni **Pool di indirizzi IP front-end** del servizio di bilanciamento del carico. 
    
### <a name="step-4-configure-the-back-end-pool"></a>Passaggio 4: Configurare il pool back-end

Per ognuno dei siti Web (contoso.com e fabrikam.com) configurare il pool di indirizzi back-end nel servizio di bilanciamento del carico:
        
1. Nel portale selezionare **Altri servizi**. Nella casella del filtro digitare **bilanciamento del carico** e quindi selezionare **Bilanciamento del carico**.

2. Selezionare il bilanciamento del carico (**mylb**) in cui si vuole aggiungere il pool back-end.

3. In **Impostazioni** selezionare **Pool di back-end**. Digitare un nome per il pool back-end, ad esempio **contosopool** o **fabrikampool**. Nella parte superiore del riquadro successivo selezionare **Aggiungi**. 

4. Per **Associate a** selezionare **Set di disponibilità**.

5. Nella casella **Set di disponibilità** selezionare **myAvailset**.

6. Aggiungere le configurazioni IP della rete di destinazione per entrambe le VM: 

    ![Configurare i pool back-end per il servizio di bilanciamento del carico](./media/load-balancer-multiple-ip/lb-backendpool.PNG)
    
    1. Per **Macchina virtuale di destinazione** selezionare la VM che si vuole aggiungere al pool back-end, ad esempio **VM1** o **VM2**.

    2. Per **Configurazione IP della rete** selezionare la configurazione IP della scheda di interfaccia di rete della VM selezionata nel passaggio precedente, ad esempio **VM1NIC2-ipconfig2** o **VM2NIC2-ipconfig2**.

7. Selezionare **OK**.

Dopo la configurazione del pool back-end, gli indirizzi vengono visualizzati nelle impostazioni **Pool back-end** del servizio di bilanciamento del carico.

### <a name="step-5-configure-the-health-probe"></a>Passaggio 5: Configurare il probe di integrità

Configurare un probe di integrità per il servizio di bilanciamento del carico:

1. Nel portale selezionare **Altri servizi**. Nella casella del filtro digitare **bilanciamento del carico** e quindi selezionare **Bilanciamento del carico**.

2. Selezionare il servizio di bilanciamento del carico (**mylb**) a cui si vuole aggiungere il probe di integrità.

3. In **Impostazioni** selezionare **Probe di integrità**. Nella parte superiore del riquadro successivo selezionare **Aggiungi**. 

4. Digitare un nome per il probe di integrità, ad esempio **HTTP**. Selezionare **OK**.

### <a name="step-6-configure-load-balancing-rules"></a>Passaggio 6: Configurare le regole di bilanciamento del carico

Per ognuno dei siti Web (contoso.com e fabrikam.com) configurare le regole di bilanciamento del carico:
    
1. <a name="step6-1"></a>In **Impostazioni** selezionare **Probe integrità**. Nella parte superiore del riquadro successivo selezionare **Aggiungi**. 

2. In **Nome** digitare un nome per la regola di bilanciamento del carico, ad esempio **HTTPc** per contoso.com o **HTTPf** per fabrikam.com.

3. In **Indirizzo IP front-end** selezionare l'indirizzo IP front-end creato in precedenza, ad esempio **contosofe** o **fabrikamfe**.

4. Per la **porta** e la **porta back-end** mantenere il valore predefinito **80**.

5. In **IP mobile (Direct Server Return)** selezionare **Abilitato**.

6. <a name="step6-6"></a>Selezionare **OK**.

7. Creare la seconda regola di bilanciamento del carico ripetendo la procedura dal <a href="#step6-1">passaggio 1</a> al <a href="#step6-6">passaggio 6</a> in questa sezione.

Dopo la configurazione, le regole vengono visualizzate nelle impostazioni **Regole di bilanciamento del carico** del servizio di bilanciamento del carico.

### <a name="step-7-configure-dns-records"></a>Passaggio 7: Configurare i record DNS

L'ultimo passaggio prevede la configurazione dei record di risorse DNS in modo che puntino agli indirizzi IP front-end corrispondenti per il servizio di bilanciamento del carico. È possibile ospitare i domini nel servizio DNS di Azure. Per altre informazioni sull'uso del servizio DNS di Azure con Azure Load Balancer, vedere [Uso del servizio DNS di Azure con altri servizi di Azure](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni su come combinare i servizi di bilanciamento del carico di Azure sono disponibili in [Uso dei servizi di bilanciamento del carico in Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Le informazioni su come usare diversi tipi di log per gestire e risolvere i problemi di bilanciamento del carico sono disponibili in [Analisi dei log per il servizio di bilanciamento del carico di Azure](../load-balancer/load-balancer-monitor-log.md).
