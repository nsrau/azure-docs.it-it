---
title: "Bilanciamento del carico in più configurazioni IP in Azure | Documentazione Microsoft"
description: Bilanciamento del carico tra configurazioni IP primarie e secondarie.
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: na
ms.assetid: 244907cd-b275-4494-aaf7-dcfc4d93edfe
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: cf1e68c7b37b2506de007bdf24eea63a27187a33
ms.lasthandoff: 03/22/2017

---

# <a name="load-balancing-on-multiple-ip-configurations-using-the-azure-portal"></a>Bilanciamento del carico in più configurazioni IP usando il portale di Azure

> [!div class="op_single_selector"]
> * [Portale](load-balancer-multiple-ip.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)
> * [CLI](load-balancer-multiple-ip-cli.md)

Questo articolo illustra l'uso di Azure Load Balancer con più indirizzi IP su un'interfaccia di interfaccia di rete secondaria (NIC). Per questo scenario sono disponibili due macchine virtuali che eseguono Windows, ognuna con una scheda di interfaccia di rete primaria e secondaria. Ogni scheda di interfaccia di rete secondaria dispone di due configurazioni di IP. Ogni macchina virtuale ospita entrambi i siti Web: contoso.com e fabrikam.com. Ogni sito Web è associato a una delle configurazioni IP della scheda di interfaccia di rete secondaria. Azure Load Balancer viene usato per esporre due indirizzi IP front-end, uno per ogni sito Web, per distribuire il traffico alla rispettiva configurazione IP per il sito Web. Questo scenario usa lo stesso numero di porta per entrambi i front-end, nonché per entrambi gli indirizzi IP del pool back-end.

![Immagine dello scenario LB](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

##<a name="prerequisites"></a>Prerequisiti
Questo esempio presuppone che sia disponibile un gruppo di risorse, denominato *contosofabrikam*, con la seguente configurazione:
 -  include una rete virtuale denominata *myVNet*, due macchine virtuali denominate rispettivamente *VM1* e *VM2* all'interno dello stesso set di disponibilità, denominato *myAvailset*. 
 - ogni macchina virtuale dispone di una scheda di interfaccia di rete primaria e una scheda di interfaccia di rete secondaria. Le schede di interfaccia di rete primarie sono denominate *VM1NIC1* e *VM2NIC1* e quelle secondarie sono denominate *VM1NIC2* e *VM2NIC2*. Per altre informazioni sulla creazione di macchine virtuali con più schede di interfacce di rete, vedere [Creare una macchina virtuale con più schede di interfaccia di rete usando PowerShell](../virtual-network/virtual-network-deploy-multinic-arm-ps.md).

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Procedura per il bilanciamento del carico in più configurazioni IP

Per ottenere lo scenario descritto in questo articolo, seguire questa procedura:

### <a name="step-1-configure-the-secondary-nics-for-each-vm"></a>PASSAGGIO 1: Configurare le schede di interfaccia di rete secondarie per ogni macchina virtuale

Per ogni macchina virtuale nella rete virtuale aggiungere la configurazione IP impostata per la scheda di interfaccia di rete secondaria nel modo seguente:  

1. Usare un browser per accedere al portale di Azure (http://portal.azure.com) con il proprio account di Azure.
2. Sul lato superiore sinistro della schermata fare clic sull'icona del gruppo di risorse e quindi sul gruppo di risorse in cui si trovano le macchine virtuali, ad esempio *contosofabrikam*. Verrà ora visualizzato il pannello **Gruppi di risorse** che elenca tutte le risorse, insieme alle interfacce di rete per le macchine virtuali.
3. Per la scheda di interfaccia di rete secondaria di ogni macchina virtuale, aggiungere una configurazione IP come segue:
    1. Selezionare l'interfaccia di rete a cui si desidera aggiungere la configurazione IP.
    2. Nel pannello visualizzato per la scheda di interfaccia di rete selezionata fare clic su **Configurazioni IP**. Successivamente fare clic su **Aggiungi** nella parte superiore del pannello visualizzato.
    3. Nel pannello **Aggiungi configurazioni IP** aggiungere una seconda configurazione IP per la scheda di interfaccia di rete nel modo seguente: 
        1. Digitare un nome per la configurazione IP secondaria, ad esempio per VM1 e VM2 denominare le configurazioni IP rispettivamente come *VM1NIC2-ipconfig2* e *VM2NIC2-ipconfig2*.
        2. Per **Indirizzo IP privato** in **Allocazione**selezionare **Statico**.
        3. Fare clic su **OK**.
        4. Al completamento della seconda configurazione IP della scheda di interfaccia di rete secondaria, questa viene visualizzata nel pannello delle impostazioni **Configurazioni IP** per la scheda di interfaccia di rete specificata.

### <a name="step-2-create-a-load-balancer"></a>PASSAGGIO 2: Creare un servizio di bilanciamento del carico

Creare un servizio di bilanciamento del carico nel modo seguente:

1. Usare un browser per accedere al portale di Azure (http://portal.azure.com) con il proprio account di Azure.
2. Sul lato superiore sinistro della schermata fare clic su **Nuovo** > **Rete** > **Servizio di bilanciamento del carico**. Quindi fare clic su **Crea**.
3. Nel pannello **Crea bilanciamento del carico** digitare un nome per il servizio di bilanciamento del carico. Qui viene chiamato *mylb*.
4. In Indirizzo IP pubblico creare un nuovo indirizzo IP pubblico denominato **PublicIP1**.
5. Nel gruppo di risorse selezionare il gruppo di risorse esistente delle proprie macchine virtuali, ad esempio, *contosofabrikam*. Selezionare quindi un percorso appropriato e fare clic su **OK**. Il bilanciamento del carico verrà avviato per la distribuzione e richiederà alcuni minuti per completare correttamente la distribuzione.
6. Una volta distribuito, il bilanciamento del carico viene visualizzato come risorsa nel gruppo di risorse.

### <a name="step-3-configure-the-frontend-ip-pool"></a>PASSAGGIO 3: Configurare il pool di indirizzi IP front-end

Configurare il pool di indirizzi IP front-end per ogni sito Web (Contoso e Fabrikam) nel modo seguente:

1. Nel portale fare clic su **Altri servizi** > digitare **Indirizzo IP pubblico** nella casella del filtro e quindi fare clic su **Indirizzi IP pubblici**. Fare clic su **Aggiungi** nella parte superiore del pannello visualizzato.
2. Configurare due indirizzi IP pubblici (*PublicIP1* e *PublicIP2*) per entrambi i siti Web (contoso e fabrikam) nel modo seguente:
    1. Digitare un nome per l'indirizzo IP front-end.
    2. Per **Gruppo di risorse** selezionare il gruppo di risorse esistente delle macchine virtuali, ad esempio, *contosofabrikam*.
    3. Per **Percorso** selezionare lo stesso percorso delle macchine virtuali.
    4. Fare clic su **OK**.
    5. Una volta creati, i due indirizzi IP pubblici vengono visualizzati nel pannello relativo agli indirizzi **IP pubblici**.
3. Nel portale fare clic su **Altri servizi** > digitare **bilanciamento del carico** nella casella del filtro e quindi fare clic su **Servizio di bilanciamento del carico**.  
4. Selezionare il bilanciamento del carico (*mylb*) in cui si vuole aggiungere il pool di indirizzi IP front-end.
5. In **Impostazioni** selezionare **Pool front-end**. Successivamente fare clic su **Aggiungi** nella parte superiore del pannello visualizzato.
6. Digitare un nome per l'indirizzo IP front-end (*farbikamfe* o **contosofe*).
7. Fare clic su **Indirizzo IP** e nel pannello **Scegli indirizzo IP pubblico** selezionare gli indirizzi IP per il front-end (*PublicIP1* o *PublicIP2*).
8. Ripetere i passaggi da 3 a 7 all'interno di questa sezione per creare il secondo indirizzo IP front-end.
9. Una volta completata la configurazione del pool di indirizzi IP front-end, entrambi gli indirizzi IP front-end vengono visualizzati nel pannello **Pool di indirizzi IP front-end** del bilanciamento del carico. 
    
### <a name="step-4-configure-the-backend-pool"></a>PASSAGGIO 4: Configurare il pool back-end   
Configurare i pool di indirizzi back-end per il bilanciamento del carico per ogni sito Web (Contoso e Fabrikam) nel modo seguente:
        
1. Nel portale fare clic su **Altri servizi** > digitare bilanciamento del carico nella casella del filtro e quindi fare clic su **Servizio di bilanciamento del carico**.  
2. Selezionare il bilanciamento del carico (*mylb*) a cui si vuole aggiungere i pool di back-end.
3. In **Impostazioni** selezionare **Pool di back-end**. Digitare un nome per il pool di back-end, ad esempio *contosopool* o *fabrikampool*. Successivamente fare clic sul pulsante **Aggiungi** nella parte superiore del pannello visualizzato. 
4. Per **Associate a** selezionare **Set di disponibilità**.
5. Nella casella **Set di disponibilità** selezionare **myAvailset**.
6. Aggiungere le configurazioni IP della rete di destinazione per entrambe le macchine virtuali nel modo seguente (vedere la figura 2):  
    1. Per **Macchina virtuale di destinazione** selezionare la macchina virtuale che si vuole aggiungere al pool back-end, ad esempio VM1 o VM2.
    2. Per **Configurazione IP di rete** selezionare la configurazione IP delle schede di interfaccia di rete secondarie per questa VM, ad esempio VM1NIC2-ipconfig2 o VM2NIC2-ipconfig2.
    ![Immagine dello scenario di bilanciamento del carico](./media/load-balancer-multiple-ip/lb-backendpool.PNG)
            
        **Figura 2**: configurazione del bilanciamento del carico con pool back-end  
7. Fare clic su **OK**.
8. Una volta completata la configurazione del pool di indirizzi IP back-end, entrambi gli indirizzi IP back-end vengono visualizzati nel pannello **Pool di indirizzi IP back-end** del servizio di bilanciamento del carico.

### <a name="step-5-configure-a-health-probe-for-your-load-balancer"></a>PASSAGGIO 5: Configurare un probe di integrità per il bilanciamento del carico
Configurare un probe di integrità per il bilanciamento del carico nel modo seguente:
    1. Nel portale fare clic su Altri servizi > digitare bilanciamento del carico nella casella del filtro e quindi fare clic su **Servizio di bilanciamento del carico**.  
    2. Selezionare il bilanciamento del carico a cui si vuole aggiungere i pool di back-end.
    3. In **Impostazioni** selezionare **Probe di integrità**. Successivamente fare clic su **Aggiungi** nella parte superiore del pannello visualizzato.
    4. Digitare un nome per il probe di integrità, ad esempio HTTP, e fare clic su **OK**.

### <a name="step-6-configure-load-balancing-rules"></a>PASSAGGIO 6: Configurare le regole del servizio di bilanciamento del carico
Configurare le regole di bilanciamento del carico (*HTTPc* e *HTTPf*) per ogni sito Web nel modo seguente:
    
1. In **Impostazioni** selezionare **Probe di integrità**. Successivamente fare clic su **Aggiungi** nella parte superiore del pannello visualizzato.
2. Per il **nome** digitare un nome per la regola di bilanciamento del carico, ad esempio, *HTTPc* per Contoso o *HTTPf* per Fabrikam
3. Per l'indirizzo IP front-end selezionare l'indirizzo IP front-end, ad esempio *Contosofe* o *Fabrikamfe*
4. Per la **porta** e la **porta back-end** mantenere il valore predefinito **80**.
5. Per **IP mobile (Direct Server Return)** fare clic su **Abilitato**.
6. Fare clic su **OK**.
7. Ripetere i passaggi da 1 a 6 all'interno di questa sezione per creare la seconda regola di bilanciamento del carico.
8. Quando la configurazione delle regole di bilanciamento del carico è stata completata, entrambe le regole (*HTTPc* e *HTTPf*) vengono visualizzate nel pannello **Regole di bilanciamento del carico** del servizio di bilanciamento del carico.

### <a name="step-7-configure-dns-records"></a>PASSAGGIO 7: Configurare i record DNS
Infine è necessario configurare i record risorsa DNS in modo che puntino all'indirizzo IP front-end corrispondente del bilanciamento del carico. È possibile ospitare i domini nel servizio DNS di Azure. Per altre informazioni sull'uso del servizio DNS di Azure con Azure Load Balancer, vedere [Uso del servizio DNS di Azure con altri servizi di Azure](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni su come combinare i servizi di bilanciamento del carico di Azure sono disponibili in [Uso dei servizi di bilanciamento del carico in Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Informazioni su come è possibile usare diversi tipi di log in Azure per gestire e risolvere i problemi di bilanciamento del carico in [Analisi dei log per Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md).

