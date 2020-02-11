---
title: 'Avvio rapido: Creare un servizio Collegamento privato tramite il portale di Azure'
titlesuffix: Azure Private Link
description: Questa guida di avvio rapido illustra come creare un servizio Collegamento privato con il portale di Azure
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 02/03/2020
ms.author: allensu
ms.openlocfilehash: 4dd27d3fb4321c2eecf222136376bfd4b49790bb
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026204"
---
# <a name="quickstart-create-a-private-link-service-using-the-azure-portal"></a>Avvio rapido: Creare un servizio Collegamento privato tramite il portale di Azure

Il servizio Collegamento privato di Azure è il riferimento a un servizio personalizzato basato su Collegamento privato di Azure. Il servizio o la risorsa che opera dietro Azure Load Balancer possono essere abilitati per l'accesso a Collegamento privato. I consumer del servizio possono accedere al servizio privatamente dalle proprie reti virtuali. Questo argomento di avvio rapido illustra come creare un servizio Collegamento privato con il portale di Azure.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.


## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-an-internal-load-balancer"></a>Creare un'istanza di Load Balancer interna

Creare prima di tutto una rete virtuale, quindi un'istanza di Load Balancer interna da usare con il servizio Collegamento privato di Azure.

### <a name="create-the-virtual-network"></a>Creare la rete virtuale

In questa sezione si creeranno la rete virtuale e la subnet che ospiteranno l'istanza di Load Balancer usata per accedere al servizio Collegamento privato.


1. In alto a sinistra nel portale selezionare **Crea una risorsa** > **Rete** > **Rete virtuale**.
   
1. Nel riquadro **Crea rete virtuale** digitare o selezionare questi valori:
   
   - **Name**: Digitare **MyVNet**.
   - **Gruppo di risorse**: Selezionare **Crea nuovo**, immettere **MyResourceGroupLB**, quindi selezionare **OK**. 
   - **Subnet** > **Nome**: Digitare **MyBackendSubnet**.
   
1. Selezionare **Create** (Crea).

   ![Crea rete virtuale](../load-balancer/media/tutorial-load-balancer-basic-internal-portal/2-load-balancer-virtual-network.png)

### <a name="create-a-standard-load-balancer"></a>Creare un'istanza di Load Balancer Standard

Creare un servizio Load Balancer Standard interno tramite il portale. Il nome e l'indirizzo IP specificati vengono configurati automaticamente come front-end del servizio di bilanciamento del carico.

1. In alto a sinistra nel portale selezionare **Crea una risorsa** > **Rete** > **Load Balancer**.
   
2. Nella scheda **Generale** della pagina **Crea servizio di bilanciamento del carico** immettere o selezionare le informazioni seguenti, accettare le impostazioni predefinite per le opzioni rimanenti e quindi selezionare **Rivedi e crea**:

    | Impostazione                 | valore                                              |
    | ---                     | ---                                                |
    | Subscription               | Selezionare la propria sottoscrizione.    |    
    | Resource group         | Selezionare *MyResourceGroupLB* nel menu a discesa.|
    | Nome                   | *myLoadBalancer*                                   |
    | Region         | Selezionare **Stati Uniti orientali 2**.                                        |
    | Type          | Selezionare **Interno**.                                        |
    | SKU           | Selezionare **Standard**.                          |
    | Rete virtuale           | Selezionare *MyVNet*.                          |    
    | Assegnazione indirizzi IP              | Selezionare **Statico**.   |
    | Indirizzo IP privato|Digitare un indirizzo nello spazio degli indirizzi della rete virtuale e della subnet, per esempio *10.3.0.7*.  |

3. Nella scheda **Rivedi e crea** fare clic su **Crea**. 
   

### <a name="create-standard-load-balancer-resources"></a>Creare le risorse di Load Balancer Standard

In questa sezione si configurano le impostazioni del servizio di bilanciamento del carico per un pool di indirizzi back-end e un probe di integrità e si specificano le regole di bilanciamento del carico.

#### <a name="create-a-backend-pool"></a>Creare un pool back-end

Per distribuire il traffico alle risorse, è necessario che un pool di indirizzi back-end contenga gli indirizzi IP delle schede di interfaccia di rete virtuale connesse all'istanza di Load Balancer. Creare il pool di indirizzi back-end *myBackendPool* per includere le risorse per il bilanciamento del carico del traffico.

1. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myLoadBalancer** nell'elenco di risorse.
2. In **Impostazioni** selezionare **Pool back-end** e quindi selezionare **Aggiungi**.
3. Nella pagina **Aggiungi pool back-end** digitare *myBackEndPool* come nome per il pool back-end e quindi selezionare **Aggiungi**.



#### <a name="create-a-health-probe"></a>Creare un probe di integrità

Per consentire al servizio Load Balancer di monitorare lo stato delle risorse, si usa un probe di integrità. Il probe di integrità aggiunge o rimuove in modo dinamico le risorse nella rotazione dell'istanza di Load Balancer in base alla rispettiva risposta ai controlli di integrità. 

**Per creare un probe di integrità per monitorare l'integrità delle risorse:**

1. Selezionare **Tutte le risorse** dal menu a sinistra e quindi selezionare **MyLoadBalancer** dall'elenco delle risorse.
   
1. In **Impostazioni** selezionare **Probe integrità**, quindi selezionare **Aggiungi**.
   
1. Nella pagina **Aggiungi probe integrità** digitare o selezionare i valori seguenti:
   
   - **Name**: digitare **MyHealthProbe**.
   - **Protocollo**: nell'elenco a discesa selezionare **TCP**. 
   - **Porta**: Digitare **80**. 
   - **Intervallo**: Digitare **15**. L'intervallo specifica il numero di secondi tra i tentativi del probe.
   - **Soglia non integra**: Digitare **2**. Questa impostazione specifica il numero di errori di probe consecutivi che si verificano prima che una macchina virtuale venga considerata non integra.
   
1. Selezionare **OK**.

#### <a name="create-a-load-balancer-rule"></a>Creare una regola di bilanciamento del carico

Una regola di bilanciamento del carico definisce come verrà distribuito il traffico alle risorse. La regola definisce la configurazione IP front-end per il traffico in ingresso, il pool di indirizzi IP back-end per ricevere il traffico e le porte di origine e di destinazione necessarie. 

La regola di bilanciamento del carico denominata **MyLoadBalancerRule** rimane in ascolto sulla porta 80 nel front-end **LoadBalancerFrontEnd**. La regola consente l'invio del traffico di rete al pool di indirizzi back-end **MyBackendPool**, sempre sulla porta 80. 

**Per creare la regola di bilanciamento del carico:**

1. Selezionare **Tutte le risorse** dal menu a sinistra e quindi selezionare **MyLoadBalancer** dall'elenco delle risorse.
   
1. In **Impostazioni** selezionare **Regole di bilanciamento del carico**, quindi selezionare **Aggiungi**.
   
1. Nella pagina **Aggiungi regola di bilanciamento del carico** digitare o selezionare i valori seguenti, se non sono già presenti:
   
   - **Name**: Digitare **MyLoadBalancerRule**.
   - **Indirizzo IP front-end:** Digitare **LoadBalancerFrontEnd** se non presente.
   - **Protocollo**: selezionare **TCP**.
   - **Porta**: Digitare **80**.
   - **Porta back-end**: Digitare **80**.
   - **Pool back-end**: Selezionare **MyBackendPool**.
   - **Probe di integrità**: Selezionare **MyHealthProbe**. 
   
1. Selezionare **OK**.
   
## <a name="create-a-private-link-service"></a>Creare un servizio Collegamento privato

In questa sezione verrà creato un servizio Collegamento privato dietro un'istanza di Load Balancer Standard.

1. Nella parte superiore sinistra della schermata del portale di Azure selezionare **Crea una risorsa** > **Rete** > **Centro collegamento privato (anteprima)** . È anche possibile cercare Collegamento privato tramite la ricerca nel portale.

2. In **Centro collegamento privato - Informazioni generali** selezionare Avvia per l'opzione **"Esporre il servizio personalizzato per consentire ad altri utenti di connettersi"** .

3. In **Crea un servizio Collegamento privato - Informazioni di base** immettere o selezionare queste informazioni:

    | Impostazione           | valore                                                                        |
    |-------------------|------------------------------------------------------------------------------|
    | **Dettagli del progetto:**  |                                                                              |
    | Subscription      | Selezionare la propria sottoscrizione                                                     |
    | Gruppo di risorse    | Selezionare *MyResourceGroupLB*                                                     |
    | **DETTAGLI DELL'ISTANZA:** |                                                                              |
    | Nome              | Immettere *myPrivateLinkService* |
    | Region            | Selezionare *Stati Uniti orientali 2*                                                         |

4. Selezionare **Avanti: Impostazioni in uscita**.

5. In **Crea un servizio Collegamento privato - Impostazioni in uscita** immettere o selezionare queste informazioni:


    | Impostazione                           | valore                                                                           |
    |-----------------------------------|---------------------------------------------------------------------------------|
    | Load Balancer                     | Selezionare *MyLoadBalancer*                                                           |
    | Indirizzo IP front-end di Load Balancer | Selezionare l'indirizzo IP front-end di MyLoadBalancer                                |
    | Rete virtuale NAT di origine        | Selezionare *myVNET*                                                                   |
    | Subnet NAT di origine                 | Selezionare *myBackendSubnet*                                                          |
    | Abilita proxy TCP V2               | Selezionare Sì/NO a seconda che nell'applicazione sia prevista l'intestazione proxy TCP V2 |
    | Impostazioni dell'indirizzo IP privato       | Configurare il metodo di allocazione e l'indirizzo IP per ogni IP NAT                  |

6. Selezionare **Avanti: Sicurezza di accesso**.

7. In **Crea un servizio Collegamento privato - Sicurezza di accesso** immettere o selezionare queste informazioni:

    | Impostazione                                     | valore                                                             |
    |---------------------------------------------|-------------------------------------------------------------------|
    | Visibilità                                  | Scegliere *Solo controllo degli accessi in base al ruolo*         |
  
8. Selezionare **Avanti: Tag**, quindi **Rivedi e crea** oppure scegliere la scheda **Rivedi e crea** nella parte superiore della pagina.

9. Esaminare le informazioni e selezionare **Crea**.

## <a name="clean-up-resources"></a>Pulire le risorse 
Quando non si ha più bisogno del servizio Collegamento privato, eliminare il gruppo di risorse per pulire le risorse usate in questo argomento di avvio rapido:

1. Immettere *myResourceGroupLB* nella casella **Cerca** nella parte superiore del portale e selezionare *myResourceGroupLB* nei risultati della ricerca. 
2. Selezionare **Elimina gruppo di risorse**. 
3. Immettere myResourceGroup in **DIGITARE IL NOME DEL GRUPPO DI RISORSE** e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido sono stati creati un'istanza di Azure Load Balancer interna e un servizio Collegamento privato. Per altre informazioni su come creare endpoint privati, vedere [Creare endpoint privati con il portale di Azure](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal).
