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
ms.openlocfilehash: f21b440ee0e2c53d9824300e85b683629c1575da
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "78252539"
---
# <a name="quickstart-create-a-private-link-service-by-using-the-azure-portal"></a>Guida introduttiva: Creare un servizio Collegamento privato usando il portale di Azure

Un servizio Collegamento privato di Azure si riferisce a un servizio personalizzato gestito da Collegamento privato. È possibile concedere l'accesso a Collegamento privato al servizio o alla risorsa che opera dietro Azure Load Balancer Standard. I consumer del servizio possono accedervi privatamente dalle proprie reti virtuali. Questo argomento di avvio rapido illustra come creare un servizio Collegamento privato con il portale di Azure.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-an-internal-load-balancer"></a>Creare un bilanciamento del carico interno

Prima di tutto, creare una rete virtuale. Quindi, creare un'istanza di Load Balancer interna da usare con il servizio Collegamento privato.

## <a name="virtual-network-and-parameters"></a>Rete virtuale e parametri

In questa sezione si crea una rete virtuale. Si crea anche la subnet che ospiterà l'istanza di Load Balancer che accede al servizio Collegamento privato.

In questa sezione è necessario sostituire i parametri seguenti delle procedure con le informazioni riportate di seguito:

| Parametro                   | valore                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupLB |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | Stati Uniti orientali 2      |
| **\<IPv4-address-space>**   | 10.3.0.0\16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.3.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-a-standard-load-balancer"></a>Creare un'istanza di Load Balancer Standard

Usare il portale per creare un servizio Load Balancer Standard interno. Il nome e l'indirizzo IP creati vengono configurati automaticamente come front-end del servizio di bilanciamento del carico.

1. In alto a sinistra nel portale selezionare **Crea una risorsa** > **Rete** > **Load Balancer**.

1. Nella scheda **Informazioni di base** della pagina **Crea servizio di bilanciamento del carico** immettere o selezionare le informazioni seguenti:

    | Impostazione                 | valore                                              |
    | ---                     | ---                                                |
    | **Sottoscrizione**               | Selezionare la propria sottoscrizione.    |
    | **Gruppo di risorse**         | Selezionare **MyResourceGroupLB** dalla casella.|
    | **Nome**                   | Immettere **myLoadBalancer**.                                   |
    | **Area**         | Selezionare **Stati Uniti orientali 2**.                                        |
    | **Tipo**          | Selezionare **Interno**.                                        |
    | **SKU**           | Selezionare **Standard**.                          |
    | **Rete virtuale**           | Selezionare **myVNET**.                          |
    | **Assegnazione indirizzi IP**              | Selezionare **Statico**.   |
    | **Indirizzo IP privato**|Immettere un indirizzo nello spazio degli indirizzi della rete virtuale e della subnet, per esempio 10.3.0.7.  |

1. Accettare i valori predefiniti per le impostazioni rimanenti e quindi selezionare **Rivedi e crea**

1. Nella scheda **Rivedi e crea** selezionare **Crea**.

### <a name="create-standard-load-balancer-resources"></a>Creare le risorse di Load Balancer Standard

In questa sezione vengono configurate le impostazioni del servizio di bilanciamento del carico per un pool di indirizzi back-end e un probe di integrità. Vengono anche specificate le regole di bilanciamento del carico.

#### <a name="create-a-back-end-pool"></a>Creare un pool back-end

Un pool di indirizzi back-end contiene gli indirizzi IP delle schede di interfaccia di rete virtuale connesse al bilanciamento del carico. Questo pool consente di distribuire il traffico alle risorse. Creare il pool di indirizzi back-end denominato **myBackendPool** per includere le risorse per il bilanciamento del carico del traffico.

1. Selezionare **Tutti i servizi** dal menu a sinistra.
1. Selezionare **Tutte le risorse** e quindi selezionare **MyLoadBalancer** dall'elenco delle risorse.
1. In **Impostazioni** selezionare **Pool di back-end**, quindi selezionare **Aggiungi**.
1. Nella pagina **Aggiungi pool back-end** digitare **myBackEndPool** come nome per il pool back-end e quindi selezionare **Aggiungi**.

#### <a name="create-a-health-probe"></a>Creare un probe di integrità

Usare un probe di integrità per consentire al servizio Load Balancer di monitorare lo stato delle risorse. In base alla risposta ai controlli di integrità, il probe di integrità aggiunge o rimuove in modo dinamico le risorse nella rotazione dell'istanza di Load Balancer.

Per creare un probe di integrità per monitorare l'integrità delle risorse:

1. Selezionare **Tutte le risorse** dal menu a sinistra e quindi selezionare **myLoadBalancer** dall'elenco delle risorse.

1. In **Impostazioni** selezionare **Probe integrità**, quindi selezionare **Aggiungi**.

1. Nella pagina **Aggiungi probe integrità** immettere o selezionare i valori seguenti:

   - **Name**: Immettere **myHealthProbe**.
   - **Protocollo**: selezionare **TCP**.
   - **Porta**: Immettere **80**.
   - **Intervallo**: Immettere **15**. Questo valore specifica il numero di secondi tra i tentativi del probe.
   - **Soglia non integra**: Immettere **2**. Questo valore corrisponde al numero di errori di probe consecutivi che si verificano prima che una macchina virtuale venga considerata non integra.

1. Selezionare **OK**.

#### <a name="create-a-load-balancer-rule"></a>Creare una regola di bilanciamento del carico

Una regola di bilanciamento del carico definisce come verrà distribuito il traffico alle risorse. La regola definisce:

- La configurazione dell'indirizzo IP front-end per il traffico in ingresso.
- Il pool di indirizzi IP back-end in cui ricevere il traffico.
- Le porte di origine e destinazione richieste.

La regola di bilanciamento del carico denominata **myLoadBalancerRule** rimane in ascolto sulla porta 80 nel front-end **LoadBalancerFrontEnd**. La regola consente l'invio del traffico di rete al pool di indirizzi back-end **myBackEndPool** sulla stessa porta 80.

Per creare una regola di bilanciamento del carico:

1. Selezionare **Tutte le risorse** dal menu a sinistra e quindi selezionare **myLoadBalancer** dall'elenco delle risorse.

1. In **Impostazioni** selezionare **Regole di bilanciamento del carico**, quindi selezionare **Aggiungi**.

1. Nella pagina **Aggiungi regola di bilanciamento del carico** immettere o selezionare i valori seguenti, se non sono già presenti:

   - **Name**: Immettere **MyLoadBalancerRule**.
   - **Indirizzo IP front-end:** Immettere **LoadBalancerFrontend**.
   - **Protocollo**: selezionare **TCP**.
   - **Porta**: Immettere **80**.
   - **Porta back-end**: Immettere **80**.
   - **Pool back-end**: Selezionare **myBackendPool**.
   - **Probe di integrità**: Selezionare **myHealthProbe**. 

1. Selezionare **OK**.

## <a name="create-a-private-link-service"></a>Creare un servizio Collegamento privato

In questa sezione verrà creato un servizio Collegamento privato dietro un'istanza di Load Balancer Standard.

1. Nella parte superiore sinistra della pagina del portale di Azure selezionare **Crea una risorsa** > **Rete** > **Centro collegamento privato (anteprima)** . È anche possibile usare la barra di ricerca del portale per cercare Collegamento privato.

1. In **Centro collegamento privato - Informazioni generali** > **Esporre il servizio personalizzato per consentire ad altri utenti di connettersi**, selezionare **Avvia**.

1. In **Crea un servizio Collegamento privato - Informazioni di base** immettere o selezionare queste informazioni:

    | Impostazione           | valore                                                                        |
    |-------------------|------------------------------------------------------------------------------|
    | Dettagli del progetto:  |                                                                              |
    | **Sottoscrizione**      | Selezionare la propria sottoscrizione.                                                     |
    | **Gruppo di risorse**    | Selezionare **myResourceGroupLB**.                                                    |
    | Dettagli dell'istanza: |                                                                              |
    | **Nome**              | Immettere **myPrivateLinkService**. |
    | **Area**            | Selezionare **Stati Uniti orientali 2**.                                                        |

1. Selezionare **Avanti: Impostazioni in uscita**.

1. In **Crea un servizio Collegamento privato - Impostazioni in uscita** immettere o selezionare queste informazioni:

    | Impostazione                           | valore                                                                           |
    |-----------------------------------|---------------------------------------------------------------------------------|
    | **Bilanciamento del carico**                     | Selezionare **myLoadBalancer**.                                                           |
    | **Indirizzo IP front-end di Load Balancer** | Selezionare l'indirizzo IP front-end di **myLoadBalancer**.                                |
    | **Rete virtuale NAT di origine**        | Selezionare **myVNET**.                                                                   |
    | **Subnet NAT di origine**                 | Selezionare **myBackendSubnet**.                                                          |
    | **Abilita proxy TCP V2**               | Selezionare **SÌ** o **NO** a seconda che nell'applicazione sia prevista l'intestazione proxy TCP V2. |
    | **Impostazioni dell'indirizzo IP privato**       | Configurare il metodo di allocazione e l'indirizzo IP per ogni IP NAT.                  |

1. Selezionare **Avanti: Sicurezza di accesso**.

1. In **Creare un servizio Collegamento privato - Sicurezza dell'accesso** selezionare **Visibilità**, quindi selezionare **Solo controllo degli accessi in base al ruolo**.
  
1. Selezionare **Avanti: Tag** > **Rivedi e crea** oppure scegliere la scheda **Rivedi e crea** nella parte superiore della pagina.

1. Esaminare le informazioni e selezionare **Crea**.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non si ha più bisogno del servizio Collegamento privato, eliminare il gruppo di risorse per pulire le risorse usate in questo argomento di avvio rapido.

1. Immettere **myResourceGroupLB** nella casella Cerca nella parte superiore del portale e selezionare **myResourceGroupLB** nei risultati della ricerca.
1. Selezionare **Elimina gruppo di risorse**.
1. In **DIGITARE IL NOME DEL GRUPPO DI RISORSE** immettere **myResourceGroup**.
1. Selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido sono stati creati un'istanza di Azure Load Balancer interna e un servizio Collegamento privato. È anche possibile apprendere come [creare un endpoint privato con il portale di Azure](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal).
