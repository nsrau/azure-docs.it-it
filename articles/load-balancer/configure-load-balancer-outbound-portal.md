---
title: Configurare il bilanciamento del carico e le regole in uscita tramite il portale di AzureConfigure load balancing and outbound rules by using the Azure portal
titleSuffix: Azure Load Balancer
description: Questo articolo illustra come configurare il bilanciamento del carico e le regole in uscita in Load Balancer Standard tramite il portale di Azure.This article shows how to configure load balancing and outbound rules in Standard Load Balancer by using the Azure portal.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: b75f49155991bfc71f788ad88f166c0bec281841
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77590013"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-by-using-the-azure-portal"></a>Configurare il bilanciamento del carico e le regole in uscita in Load Balancer Standard tramite il portale di AzureConfigure load balancing and outbound rules in Standard Load Balancer by using the Azure portal

Questo articolo illustra come configurare le regole in uscita in Load Balancer Standard tramite il portale di Azure.This article shows you how to configure outbound rules in Standard Load Balancer by using the Azure portal.  

La risorsa di bilanciamento del carico contiene due front-end e le regole associate. Si dispone di un front-end per il traffico in ingresso e un altro front-end per il traffico in uscita.  

Ogni front-end fa riferimento a un indirizzo IP pubblico. In questo scenario, l'indirizzo IP pubblico per il traffico in ingresso è diverso dall'indirizzo per il traffico in uscita.   La regola di bilanciamento del carico fornisce solo il bilanciamento del carico in ingresso. La regola in uscita controlla la conversione degli indirizzi di rete in uscita (NAT) per la macchina virtuale.  

Lo scenario utilizza due pool back-end: uno per il traffico in ingresso e uno per il traffico in uscita. Questi pool illustrano la funzionalità e offrono flessibilità per lo scenario.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di [https://portal.azure.com](https://portal.azure.com)Azure all'indirizzo .

## <a name="create-a-load-balancer"></a>Creare un servizio di bilanciamento del carico

In questa sezione viene creato un servizio di bilanciamento del carico che consente di bilanciare il carico delle macchine virtuali. È possibile creare un servizio di bilanciamento del carico pubblico o un servizio di bilanciamento del carico interno. Quando si crea un servizio di bilanciamento del carico pubblico, si crea un nuovo indirizzo IP pubblico configurato come front-end per il servizio di bilanciamento del carico. Il frontend sarà denominato **LoadBalancerFrontEnd** per impostazione predefinita.

1. Nella parte superiore sinistra dello schermo selezionare **Crea una risorsa** > **di bilanciamento del carico**di**rete** > .
2. Nella scheda **Nozioni di base** della pagina **Crea bilanciamento del carico** immettere o selezionare le informazioni seguenti:

    | Impostazione                 | valore                                              |
    | ---                     | ---                                                |
    | Subscription               | Selezionare la propria sottoscrizione.    |    
    | Resource group         | Selezionare **Crea nuovo** e digitare **myResourceGroupSLB** nella casella di testo.|
    | Nome                   | **myLoadBalancer**                                   |
    | Region         | Selezionare **Europa occidentale**.                                        |
    | Type          | Selezionare **Pubblico**.                                        |
    | SKU           | Selezionare **Standard** o **Basic**. Microsoft consiglia di scegliere Standard per i carichi di lavoro di produzione. |
    | Indirizzo IP pubblico | Selezionare **Crea nuovo**. Se si desidera utilizzare un IP pubblico esistente, selezionare **Usa esistente**.  L'indirizzo IP pubblico esistente deve essere **SKU Standard.Existing** public IP must be Standard SKU.  Gli indirizzi IP pubblici di base non sono compatibili con il servizio di bilanciamento del carico SKU **Standard.Basic** public IPs aren't compatible with Standard SKU load balancer.  |
    | Nome dell'indirizzo IP pubblico              | Digitare **myPublicIP** nella casella di testo.|
    | Zona di disponibilità | Selezionare **Ridondante** di zona per creare un servizio di bilanciamento del carico resiliente. Per creare una risorsa Load Balancer di zona, selezionare una zona specifica tra 1, 2 o 3 |

3. Accettare le impostazioni predefinite per il resto della configurazione.
4. Selezionare **Revisione e creazione**

    > [!IMPORTANT]
    > Il resto di questa guida di avvio rapido presuppone che durante il precedente processo di selezione dello SKU venga scelto lo SKU **Standard**.

5. Nella scheda **Rivedi e crea** selezionare **Crea**.   

    ![Creare un'istanza di Load Balancer Standard](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Creare risorse di bilanciamento del carico

In questa sezione si configurano le impostazioni del servizio di bilanciamento del carico per un pool di indirizzi back-end e un probe di integrità e si specifica una regola di bilanciamento.

### <a name="create-a-backend-pool"></a>Creare un pool back-end

Un pool di indirizzi back-end contiene gli indirizzi IP delle schede di interfaccia di rete virtuali nel pool back-end. Creare il pool di indirizzi back-end **myBackendPool** per includere le macchine virtuali per il bilanciamento del carico del traffico Internet.

1. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myLoadBalancer** nell'elenco di risorse.
2. In **Impostazioni** selezionare **Pool back-end** e quindi selezionare **Aggiungi**.
3. Nella pagina **Aggiungi pool back-end** digitare **myBackEndPool** come nome per il pool back-end e quindi selezionare **Aggiungi**.

### <a name="create-a-health-probe"></a>Creare un probe di integrità

Un probe di integrità viene usato per monitorare lo stato dell'app. Il probe di integrità aggiunge o rimuove le macchine virtuali dal servizio di bilanciamento del carico in base alla risposta ai controlli di integrità. Creare un probe di integrità **myHealthProbe** per monitorare l'integrità delle macchine virtuali.

1. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myLoadBalancer** nell'elenco di risorse.
2. In **Impostazioni** selezionare **Probe integrità** e quindi selezionare **Aggiungi**.
    
    | Impostazione | valore |
    | ------- | ----- |
    | Nome | Immettere **myHealthProbe**. |
    | Protocollo | Selezionare **HTTP**. |
    | Porta | Immettere **80**.|
    | Interval | Immettere **15** in **Intervallo** come numero di secondi tra i tentativi del probe. |
    | Soglia non integra | Selezionare **2** per **Soglia di non integrità** come numero di errori di probe consecutivi che devono verificarsi prima che una macchina virtuale venga considerata non integra.|
    | | |
4. Selezionare **OK**.

### <a name="create-a-load-balancer-rule"></a>Creare una regola di bilanciamento del carico
Una regola di bilanciamento del carico consente di definire come il traffico verrà distribuito alle VM. 

È possibile definire:
 - Configurazione IP front-end per il traffico in ingresso.
 - Pool IP back-end per ricevere il traffico.
 - La porta di origine e di destinazione richiesta. 

Nella sezione seguente viene creata una:
 - Regola di bilanciamento del carico myHTTPRule per l'ascolto della porta 80.Load balancer rule **myHTTPRule** for listening to port 80.
 - **LoadBalancerFrontEnd**front-end .
 - Anche il pool di indirizzi back-end myBackEndPool utilizza la porta 80.Backend address pool **myBackEndPool** also using port 80. 

1. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myLoadBalancer** nell'elenco di risorse.
2. In **Impostazioni** selezionare **Regole di bilanciamento del carico** e quindi selezionare **Aggiungi**.
3. Usare questi valori per configurare la regola di bilanciamento del carico:
    
    | Impostazione | valore |
    | ------- | ----- |
    | Nome | Immettere **myHTTPRule**. |
    | Protocollo | selezionare **TCP**. |
    | Porta | Immettere **80**.|
    | Porta back-end | Immettere **80**. |
    | Pool back-end | Selezionare **myBackendPool**.|
    | Probe di integrità | Selezionare **myHealthProbe**. |
    | Creare regole in uscita implicite | Selezionare **No**. Creeremo regole in uscita in una sezione successiva usando un ip pubblico dedicato. |
4. Accettare tutte le impostazioni predefinite e quindi selezionare **OK**.

## <a name="create-outbound-rule-configuration"></a>Creare la configurazione delle regole in uscitaCreate outbound rule configuration
Le regole in uscita del servizio di bilanciamento del carico configurano SNAT in uscita per le macchine virtuali nel pool back-end. 

### <a name="create-an-outbound-public-ip-address-and-frontend"></a>Creare un indirizzo IP pubblico in uscita e un front-endCreate an outbound public IP address and frontend

1. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myLoadBalancer** nell'elenco di risorse.

2. In **Impostazioni**selezionare **Configurazione IP front-end**, quindi selezionare **Aggiungi**.

3. Utilizzare questi valori per configurare la configurazione IP front-end per l'uscita:Use these values to configure the frontend IP configuration for outbound:

    | Impostazione | valore |
    | ------- | ----- |
    | Nome | Immettere **LoadBalancerFrontEndOutbound**. |
    | Versione IP | Selezionare **IPv4**. |
    | Tipo IP | Selezionare **Indirizzo IP**.|
    | Indirizzo IP pubblico | Selezionare **Crea nuovo**. Nella finestra di dialogo **Aggiungi un indirizzo IP pubblico**immettere **myPublicIPOutbound**.  Selezionare **OK**. |

4. Selezionare **Aggiungi**.

### <a name="create-an-outbound-backend-pool"></a>Creare un pool di back-end in uscitaCreate an outbound backend pool

1. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myLoadBalancer** nell'elenco di risorse.

2. In **Impostazioni** selezionare **Pool back-end** e quindi selezionare **Aggiungi**.

3. Nella pagina **Aggiungi pool back-end** digitare **myBackendPoolOutbound**, come nome del pool back-end e quindi scegliere **Aggiungi**.

### <a name="create-outbound-rule"></a>Creare una regola in uscita

1. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myLoadBalancer** nell'elenco di risorse.

2. In **Impostazioni**selezionare **Regole in uscita**, quindi Aggiungi . **Add**

3. Utilizzare questi valori per configurare le regole in uscita:Use these values to configure the outbound rules:

    | Impostazione | valore |
    | ------- | ----- |
    | Nome | Immettere **myOutboundRule**. |
    | Indirizzo IP front-end | Selezionare **LoadBalancerFrontEndOutbound**. |
    | Timeout di inattività (minuti) | Spostare il dispositivo di scorrimento a 15 minuti.|
    | Reimpostazione TCP | Selezionare **Abilitato**.|
    | Pool back-end | Selezionare **myBackendPoolOutbound** |
    | Allocazione porta ->allocazione delle porte | Selezionare **Scegli manualmente il numero di porte in uscita** |
    | Porte in uscita -> Scegli per | Seleziona **porte per istanza** |
    | Porte in uscita -> porte per istanzaOutbound ports -> Ports per instance | Immettere **10.000**. |

4. Selezionare **Aggiungi**.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse, il servizio di bilanciamento del carico e tutte le risorse correlate. Selezionare il gruppo di risorse **myResourceGroupSLB** contenente il servizio di bilanciamento del carico e quindi selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

Contenuto dell'articolo:
 - È stato creato un servizio di bilanciamento del carico standard.
 - Configurate le regole del traffico del servizio di bilanciamento del carico in ingresso e in uscita.
 - Configurato un probe di integrità per le macchine virtuali nel pool back-end. 

Per altre informazioni, continuare con [le esercitazioni per Azure Load Balancer.](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
