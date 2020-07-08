---
title: Configurare il bilanciamento del carico e le regole in uscita usando il portale di Azure
titleSuffix: Azure Load Balancer
description: Questo articolo illustra come configurare il bilanciamento del carico e le regole in uscita in Load Balancer Standard usando il portale di Azure.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: 2dff916bf005b307f27264ad7a17864fbba50872
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85367394"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-by-using-the-azure-portal"></a>Configurare il bilanciamento del carico e le regole in uscita in Load Balancer Standard usando il portale di Azure

Questo articolo illustra come configurare le regole in uscita in Load Balancer Standard usando il portale di Azure.  

La risorsa del servizio di bilanciamento del carico contiene due front-end e le relative regole associate. Si ha un front-end per il traffico in ingresso e un altro front-end per il traffico in uscita.  

Ogni front-end fa riferimento a un indirizzo IP pubblico. In questo scenario, l'indirizzo IP pubblico per il traffico in ingresso è diverso dall'indirizzo per il traffico in uscita.   La regola di bilanciamento del carico fornisce solo il bilanciamento del carico in ingresso. La regola in uscita controlla la Network Address Translation in uscita (NAT) per la macchina virtuale.  

Lo scenario usa due pool back-end: uno per il traffico in ingresso e uno per il traffico in uscita. Questi pool illustrano la funzionalità e offrono flessibilità per lo scenario.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-load-balancer"></a>Creare un servizio di bilanciamento del carico

In questa sezione viene creato un servizio di bilanciamento del carico per il bilanciamento del carico delle macchine virtuali. È possibile creare un servizio di bilanciamento del carico pubblico o un servizio di bilanciamento del carico interno. Quando si crea un servizio di bilanciamento del carico pubblico, si crea un nuovo indirizzo IP pubblico configurato come front-end per il servizio di bilanciamento del carico. Il front-end verrà denominato **LoadBalancerFrontEnd** per impostazione predefinita.

1. In alto a sinistra nella schermata selezionare **Crea una risorsa** > **Rete** > **Load Balancer**.
2. Nella scheda **nozioni di base** della pagina Crea servizio di **bilanciamento del carico** immettere o selezionare le informazioni seguenti:

    | Impostazione                 | valore                                              |
    | ---                     | ---                                                |
    | Subscription               | Selezionare la propria sottoscrizione.    |    
    | Resource group         | Selezionare **Crea nuovo** e digitare **myResourceGroupSLB** nella casella di testo.|
    | Nome                   | **myLoadBalancer**                                   |
    | Region         | Selezionare **Europa occidentale**.                                        |
    | Type          | Selezionare **Pubblica**.                                        |
    | SKU           | Selezionare **Standard**. |
    | Indirizzo IP pubblico | Selezionare **Crea nuovo**. Se si vuole usare un indirizzo IP pubblico esistente, selezionare **Usa esistente**.  L'IP pubblico esistente deve essere uno SKU **standard** .  Gli indirizzi IP pubblici di base non sono compatibili con il servizio di bilanciamento del carico SKU **standard** .  |
    | Nome dell'indirizzo IP pubblico              | Digitare **myPublicIP** nella casella di testo.|
    | Zona di disponibilità | Selezionare con **ridondanza della zona** per creare un Load Balancer resiliente. Per creare una risorsa Load Balancer di zona, selezionare una zona specifica tra 1, 2 o 3 |

3. Accettare le impostazioni predefinite per il resto della configurazione.
4. Selezionare **Verifica + crea**

    > [!IMPORTANT]
    > Il resto di questa guida di avvio rapido presuppone che durante il precedente processo di selezione dello SKU venga scelto lo SKU **Standard**.

5. Nella scheda **Rivedi e crea** selezionare **Crea**.   

    ![Creare un'istanza di Load Balancer Standard](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Creare risorse di bilanciamento del carico

In questa sezione si configurano le impostazioni del servizio di bilanciamento del carico per un pool di indirizzi back-end e un probe di integrità e si specifica una regola di bilanciamento.

### <a name="create-a-backend-pool"></a>Creare un pool back-end

Un pool di indirizzi back-end contiene gli indirizzi IP delle schede di rete virtuali nel pool back-end. Creare il pool di indirizzi back-end **myBackendPool** per includere le macchine virtuali per il bilanciamento del carico del traffico Internet.

1. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myLoadBalancer** nell'elenco di risorse.
2. In **Impostazioni** selezionare **Pool back-end** e quindi selezionare **Aggiungi**.
3. Nella pagina **Aggiungi pool back-end** digitare **myBackEndPool** come nome per il pool back-end e quindi selezionare **Aggiungi**.

### <a name="create-a-health-probe"></a>Creare un probe di integrità

Viene usato un probe di integrità per monitorare lo stato dell'app. Il probe di integrità aggiunge o rimuove le VM dal servizio di bilanciamento del carico in base alla risposta ai controlli di integrità. Creare un probe di integrità **myHealthProbe** per monitorare l'integrità delle macchine virtuali.

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

Si definisce:
 - Configurazione IP front-end per il traffico in ingresso.
 - Pool di indirizzi IP back-end per ricevere il traffico.
 - La porta di origine e di destinazione richiesta. 

Nella sezione seguente viene creato un:
 - Regola di bilanciamento del carico **myhttprule come** per l'ascolto della porta 80.
 - **LoadBalancerFrontEnd**front-end.
 - Il pool di indirizzi back-end **myBackEndPool** anche usando la porta 80. 

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
    | Creare regole in uscita implicite | Selezionare **No**. In una sezione successiva si creeranno regole in uscita usando un indirizzo IP pubblico dedicato. |
4. Accettare tutte le impostazioni predefinite e quindi selezionare **OK**.

## <a name="create-outbound-rule-configuration"></a>Crea configurazione regola in uscita
Le regole in uscita del servizio di bilanciamento del carico configurano SNAT in uscita per le macchine virtuali nel pool back-end. 

### <a name="create-an-outbound-public-ip-address-and-frontend"></a>Creare un indirizzo IP pubblico in uscita e un front-end

1. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myLoadBalancer** nell'elenco di risorse.

2. In **Impostazioni**selezionare **configurazione IP**front-end, quindi selezionare **Aggiungi**.

3. Usare questi valori per configurare la configurazione IP front-end per il traffico in uscita:

    | Impostazione | valore |
    | ------- | ----- |
    | Nome | Immettere **LoadBalancerFrontEndOutbound**. |
    | Versione IP | Selezionare **IPv4**. |
    | Tipo IP | Selezionare **indirizzo IP**.|
    | Indirizzo IP pubblico | Selezionare **Crea nuovo**. Nell' **aggiungere un indirizzo IP pubblico**immettere **myPublicIPOutbound**.  Selezionare **OK**. |

4. Selezionare **Aggiungi**.

### <a name="create-an-outbound-backend-pool"></a>Creare un pool back-end in uscita

1. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myLoadBalancer** nell'elenco di risorse.

2. In **Impostazioni** selezionare **Pool back-end** e quindi selezionare **Aggiungi**.

3. Nella pagina **Aggiungi un pool back-end** digitare **myBackendPoolOutbound**come nome per il pool back-end, quindi selezionare **Aggiungi**.

### <a name="create-outbound-rule"></a>Creare una regola in uscita

1. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myLoadBalancer** nell'elenco di risorse.

2. In **Impostazioni**selezionare **regole in uscita**e quindi selezionare **Aggiungi**.

3. Usare questi valori per configurare le regole in uscita:

    | Impostazione | valore |
    | ------- | ----- |
    | Nome | Immettere **myOutboundRule**. |
    | Indirizzo IP front-end | Selezionare **LoadBalancerFrontEndOutbound**. |
    | Timeout di inattività (minuti) | Spostare il dispositivo di scorrimento su * * 15 minuti.|
    | Ripristino TCP | Selezionare **Abilitato**.|
    | Pool back-end | Seleziona **myBackendPoolOutbound** |
    | Allocazione porte-allocazione porta > | Selezionare **Scegli manualmente il numero di porte in uscita** |
    | Porte in uscita-> scegliere | Selezionare le **porte per istanza** |
    | Porte in uscita-Porte > per istanza | Immettere **10.000**. |

4. Selezionare **Aggiungi**.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse, il servizio di bilanciamento del carico e tutte le risorse correlate. Selezionare il gruppo di risorse **myResourceGroupSLB** che contiene il servizio di bilanciamento del carico e quindi selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

Contenuto dell'articolo:
 - È stato creato un servizio di bilanciamento del carico standard.
 - Sono state configurate le regole del traffico di bilanciamento del carico in ingresso e in uscita.
 - È stato configurato un probe di integrità per le macchine virtuali nel pool back-end. 

Per altre informazioni, continuare con le [esercitazioni per Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md).
