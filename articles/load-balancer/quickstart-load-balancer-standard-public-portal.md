---
title: 'Avvio rapido: Creare un servizio di bilanciamento del carico pubblico - Portale di Azure'
titleSuffix: Azure Load Balancer
description: Questa guida di avvio rapido mostra come creare un servizio di bilanciamento del carico con il portale di Azure.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/17/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: f9d736098e42bf5ca07eca0cb952275c5e39c2a9
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87125191"
---
# <a name="quickstart-create-a-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Avvio rapido: Creare un servizio di bilanciamento del carico per bilanciare il carico delle macchine virtuali con il portale di Azure

Iniziare a usare Azure Load Balancer con il portale di Azure per creare un servizio di bilanciamento del carico pubblico e tre macchine virtuali.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

---

# <a name="option-1-default-create-a-load-balancer-standard-sku"></a>[Opzione 1 (predefinita): Crea un servizio di bilanciamento del carico (SKU Standard)](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Il bilanciamento del carico di SKU Standard è l'impostazione consigliata per i carichi di lavoro di produzione.  Per altre informazioni sugli SKU, vedere **[SKU di Azure Load Balancer](skus.md)** .

In questa sezione verrà creato un servizio di bilanciamento del carico che bilancia il carico delle macchine virtuali. 

È possibile creare un servizio di bilanciamento del carico pubblico o interno. 

Quando si crea un servizio di bilanciamento del carico pubblico, viene creato un nuovo indirizzo IP pubblico che viene configurato come front-end (denominato **LoadBalancerFrontend** per impostazione predefinita).

1. In alto a sinistra nella schermata selezionare **Crea una risorsa** > **Rete** > **Load Balancer**.

2. Nella scheda **Informazioni di base** della pagina **Crea servizio di bilanciamento del carico** immettere o selezionare le informazioni seguenti: 

    | Impostazione                 | Valore                                              |
    | ---                     | ---                                                |
    | Subscription               | Selezionare la propria sottoscrizione.    |    
    | Resource group         | Selezionare **Crea nuovo** e immettere **myResourceGroupLB** nella casella di testo.|
    | Nome                   | Immettere **myLoadBalancer**                                   |
    | Region         | Selezionare **Europa occidentale**.                                        |
    | Type          | Selezionare **Pubblica**.                                        |
    | SKU           | selezionare **Standard** |
    | Indirizzo IP pubblico | Selezionare **Crea nuovo**. Se si vuole usare un indirizzo IP pubblico esistente, selezionare **Usa esistente**. |
    | Nome dell'indirizzo IP pubblico | Digitare **myPublicIP** nella casella di testo.|
    | Zona di disponibilità | Selezionare **Con ridondanza della zona** per creare un servizio di bilanciamento del carico resiliente. Per creare un servizio di bilanciamento del carico di zona, selezionare una zona specifica tra 1, 2 o 3 |
    | Aggiungi un indirizzo IPv6 pubblico | Selezionare **No**. </br> Per altre informazioni sugli indirizzi IPv6 e il servizio di bilanciamento del carico, vedere [Che cos'è IPv6 per la rete virtuale di Azure?](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)  |

3. Accettare i valori predefiniti per le impostazioni rimanenti e quindi selezionare **Rivedi e crea**.

4. Nella scheda **Rivedi e crea** selezionare **Crea**.   
    
    :::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png" alt-text="Creare un'istanza di Load Balancer Standard" border="true":::
 
## <a name="create-load-balancer-resources"></a>Creare risorse di bilanciamento del carico

In questa sezione vengono configurati:

* Le impostazioni del servizio di bilanciamento del carico per un pool di indirizzi back-end.
* Un probe di integrità.
* Una regola di bilanciamento del carico e una regola in uscita automatica.

### <a name="create-a-backend-pool"></a>Creare un pool back-end

Un pool di indirizzi back-end contiene gli indirizzi IP delle schede di interfaccia di rete virtuale connesse al bilanciamento del carico. 

Creare il pool di indirizzi back-end **myBackendPool** per includere le macchine virtuali per il bilanciamento del carico del traffico Internet.

1. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myLoadBalancer** nell'elenco di risorse.

2. In **Impostazioni** selezionare **Pool back-end** e quindi selezionare **Aggiungi**.

3. Nella pagina **Aggiungi pool back-end** digitare **myBackEndPool** come nome per il pool back-end e quindi selezionare **Aggiungi**.

### <a name="create-a-health-probe"></a>Creare un probe di integrità

Il servizio di bilanciamento del carico monitora lo stato dell'app con un probe di integrità. 

Il probe di integrità aggiunge o rimuove le macchine virtuali nel servizio di bilanciamento del carico in base alla rispettiva risposta ai controlli di integrità. 

Creare un probe di integrità denominato **myHealthProbe** per monitorare l'integrità delle macchine virtuali.

1. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myLoadBalancer** nell'elenco di risorse.

2. In **Impostazioni** selezionare **Probe integrità** e quindi selezionare **Aggiungi**.
    
    | Impostazione | Valore |
    | ------- | ----- |
    | Nome | Immettere **myHealthProbe**. |
    | Protocollo | Selezionare **HTTP**. |
    | Porta | Immettere **80**.|
    | Interval | Immettere **15** in **Intervallo** come numero di secondi tra i tentativi del probe. |
    | Soglia non integra | Selezionare **2** per **Soglia di non integrità** come numero di errori di probe consecutivi che devono verificarsi prima che una macchina virtuale venga considerata non integra.|
    | | |

3. Accettare tutte le impostazioni predefinite e selezionare **OK**.

### <a name="create-a-load-balancer-rule"></a>Creare una regola di bilanciamento del carico

Una regola di bilanciamento del carico consente di definire come il traffico verrà distribuito alle VM. È possibile definire la configurazione IP front-end per il traffico in ingresso e il pool IP back-end che riceve il traffico. La porta di origine e quella di destinazione vengono definite nella regola. 

In questa sezione verrà creata una regola di bilanciamento del carico:

* Denominata **myHTTPRule**.
* Nel front-end denominato **LoadBalancerFrontEnd**.
* In ascolto sulla **porta 80**.
* Che indirizza il traffico con carico bilanciato al back-end denominato **myBackendPool** sulla **porta 80**.

1. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myLoadBalancer** nell'elenco di risorse.

2. In **Impostazioni** selezionare **Regole di bilanciamento del carico** e quindi selezionare **Aggiungi**.

3. Usare questi valori per configurare la regola di bilanciamento del carico:
    
    | Impostazione | Valore |
    | ------- | ----- |
    | Nome | Immettere **myHTTPRule**. |
    | Versione indirizzo IP | Selezionare **IPv4** |
    | Indirizzo IP front-end | Selezionare **LoadBalancerFrontend** |
    | Protocollo | selezionare **TCP**. |
    | Porta | Immettere **80**.|
    | Porta back-end | Immettere **80**. |
    | Pool back-end | Selezionare **myBackendPool**.|
    | Probe di integrità | Selezionare **myHealthProbe**. |
    | Crea regole in uscita implicite | Selezionare **No**.

4. Accettare tutte le impostazioni predefinite e quindi selezionare **OK**.

## <a name="create-backend-servers"></a>Creare i server back-end

In questa sezione verrà illustrato come:

* Creare una rete virtuale.
* Creare tre macchine virtuali per il pool back-end del servizio di bilanciamento del carico.
* Installare IIS nelle macchine virtuali per testare il servizio di bilanciamento del carico.

## <a name="virtual-network-and-parameters"></a>Rete virtuale e parametri

In questa sezione è necessario sostituire i parametri delle procedure con le informazioni riportate di seguito:

| Parametro                   | valore                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupLB |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | Europa occidentale      |
| **\<IPv4-address-space>**   | 10.1.0.0\16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machines"></a>Creare macchine virtuali

Gli SKU degli indirizzi IP pubblici e gli SKU del bilanciamento del carico devono corrispondere. Per Load Balancer Standard usare macchine virtuali con indirizzi IP standard nel pool back-end. 

In questa sezione verranno create tre macchine virtuali (**myVM1**, **myVM2** e **myVM3**) con un indirizzo IP pubblico standard in tre zone diverse (**Zona 1**, **Zona 2**e **Zona 3**). 

Queste macchine virtuali vengono aggiunte al pool back-end del servizio di bilanciamento del carico creato in precedenza.

1. Nella parte superiore sinistra del portale selezionare **Crea una risorsa** > **Calcolo** > **Macchina virtuale**. 
   
2. In **Crea macchina virtuale** digitare o selezionare i valori nella scheda **Nozioni di base**:

    | Impostazione | valore                                          |
    |-----------------------|----------------------------------|
    | **Dettagli del progetto** |  |
    | Subscription | Selezionare la sottoscrizione ad Azure |
    | Gruppo di risorse | Selezionare **myResourceGroupLB** |
    | **Dettagli istanza** |  |
    | Nome macchina virtuale | Immettere **myVM1** |
    | Region | Selezionare **Europa occidentale** |
    | Opzioni di disponibilità | Selezionare **Zone di disponibilità** |
    | Zona di disponibilità | Selezionare **1** |
    | Immagine | Selezionare **Windows Server 2019 Datacenter** |
    | Istanza Spot di Azure | Selezionare **No** |
    | Dimensione | Scegliere le dimensioni della macchina virtuale o usare l'impostazione predefinita |
    | **Account amministratore** |  |
    | Username | Immettere un nome utente |
    | Password | Immettere una password |
    | Conferma password | Reimmettere la password |

3. Selezionare la scheda **Rete**, oppure selezionare **Avanti: Dischi**, quindi **Avanti: Rete**.
  
4. Nella scheda Rete selezionare o immettere:

    | Impostazione | valore |
    |-|-|
    | **Interfaccia di rete** |  |
    | Rete virtuale | **myVNet** |
    | Subnet | **myBackendSubnet** |
    | IP pubblico | Accettare l'impostazione predefinita **myVM-ip**. </br> IP sarà automaticamente un IP dello SKU standard nella Zona 1. |
    | Gruppo di sicurezza di rete della scheda di interfaccia di rete | Selezionare **Avanzato**|
    | Configura gruppo di sicurezza di rete | Selezionare **Crea nuovo**. </br> Nella pagina **Crea gruppo di sicurezza di rete** immettere **myNSG** in **Nome**. </br> In **Regole in ingresso** selezionare **+ Aggiungi una regola in ingresso**. </br> In **Intervalli di porte di destinazione** immettere **80**. </br> In **Priorità** immettere **100**. </br> In **Nome** immettere **myHTTPRule** </br> Selezionare **Aggiungi** </br> Selezionare **OK**. |
    | **Bilanciamento del carico**  |
    | Associare questa macchina virtuale a una soluzione di bilanciamento del carico esistente? | Selezionare **Sì** |
    | **Impostazioni di bilanciamento del carico** |
    | Opzioni di bilanciamento del carico | Selezionare **Bilanciamento del carico di Azure** |
    | Selezionare un servizio di bilanciamento del carico | Selezionare **myLoadBalancer**  |
    | Selezionare un pool back-end | Selezionare **myBackendPool** |

5. Selezionare la scheda **Gestione** oppure **Avanti** > **Gestione**.

6. Nella scheda **Gestione** selezionare o immettere:
    
    | Impostazione | valore |
    |-|-|
    | **Monitoring** |  |
    | Diagnostica di avvio | Selezionare **Disattivato** |
   
7. Selezionare **Rivedi e crea**. 
  
8. Rivedere le impostazioni e quindi selezionare **Crea**.

9. Seguire i passaggi da 1 a 8 per creare due macchine virtuali aggiuntive con i valori seguenti e tutte le altre impostazioni identiche a **myVM1**:

    | Impostazione | VM 2| VM 3|
    | ------- | ----- |---|
    | Nome |  **myVM2** |**myVM3**|
    | Zona di disponibilità | **2** |**3**|
    | Gruppo di sicurezza di rete | Selezionare il gruppo di sicurezza di rete **myNSG** esistente| Selezionare il gruppo di sicurezza di rete **myNSG** esistente|

## <a name="create-outbound-rule-configuration"></a>Creare la configurazione delle regole in uscita
Le regole in uscita del servizio di bilanciamento del carico configurano la conversione degli indirizzi di rete di origine in uscita per le macchine virtuali nel pool back-end. 

Per altre informazioni sulle connessioni in uscita, vedere [Connessioni in uscita in Azure](load-balancer-outbound-connections.md).

### <a name="create-outbound-rule"></a>Creare una regola in uscita

1. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myLoadBalancer** nell'elenco di risorse.

2. In **Impostazioni** selezionare **Regole in uscita** e quindi **Aggiungi**.

3. Usare questi valori per configurare le regole in uscita:

    | Impostazione | valore |
    | ------- | ----- |
    | Name (Nome) | Immettere **myOutboundRule**. |
    | Indirizzo IP front-end | Selezionare **Crea nuovo**. </br> In **Nome** immettere **LoadBalancerFrontEndOutbound**. </br> Selezionare **Indirizzo IP** o **Prefisso IP**. </br> Selezionare **Crea nuovo** in **Indirizzo IP pubblico**  o **Prefisso IP pubblico**. </br> In Nome immettere **myPublicIPOutbound** o **myPublicIPPrefixOutbound**. </br> Selezionare **OK**. </br> Selezionare **Aggiungi**.|
    | Timeout di inattività (minuti) | Spostare il dispositivo di scorrimento su **15 minuti**.|
    | Reimpostazione TCP | Selezionare **Enabled**.|
    | Pool back-end | Selezionare **Crea nuovo**. </br> In **Nome** immettere **myBackendPoolOutbound**. </br> Selezionare **Aggiungi**. |
    | Allocazione porte -> Allocazione porte | Selezionare **Scegliere manualmente il numero di porte in uscita** |
    | Porte in uscita -> Scegli per | Selezionare **Porte per istanza** |
    | Porte in uscita -> Porte per istanza | Immettere **10.000**. |

4. Selezionare **Aggiungi**.

### <a name="add-virtual-machines-to-outbound-pool"></a>Aggiungere le macchine virtuali al pool in uscita

1. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myLoadBalancer** nell'elenco di risorse.

2. In **Impostazioni** selezionare **Pool back-end**.

3. Selezionare **myBackendPoolOutbound**.

4. In **Rete virtuale** selezionare **myVNet**.

5. In **Macchine virtuali** selezionare **+ Aggiungi**.

6. Selezionare le caselle accanto a **myVM1**, **myVM2** e **myVM3**. 

7. Selezionare **Aggiungi**.

8. Selezionare **Salva**.

# <a name="option-2-create-a-load-balancer-basic-sku"></a>[Opzione 2: Creare un servizio di bilanciamento del carico (SKU Basic)](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Il bilanciamento del carico di SKU Standard è l'impostazione consigliata per i carichi di lavoro di produzione.  Per altre informazioni sugli SKU, vedere **[SKU di Azure Load Balancer](skus.md)** .

In questa sezione verrà creato un servizio di bilanciamento del carico che bilancia il carico delle macchine virtuali. 

È possibile creare un servizio di bilanciamento del carico pubblico o interno. 

Quando si crea un servizio di bilanciamento del carico pubblico, viene creato un nuovo indirizzo IP pubblico che viene configurato come front-end (denominato **LoadBalancerFrontend** per impostazione predefinita).

1. In alto a sinistra nella schermata selezionare **Crea una risorsa** > **Rete** > **Load Balancer**.

2. Nella scheda **Informazioni di base** della pagina **Crea servizio di bilanciamento del carico** immettere o selezionare le informazioni seguenti: 

    | Impostazione                 | valore                                              |
    | ---                     | ---                                                |
    | Subscription               | Selezionare la propria sottoscrizione.    |    
    | Resource group         | Selezionare **Crea nuovo** e digitare **myResourceGroupLB** nella casella di testo.|
    | Nome                   | Immettere **myLoadBalancer**                                   |
    | Region         | Selezionare **Europa occidentale**.                                        |
    | Type          | Selezionare **Pubblica**.                                        |
    | SKU           | Selezionare **Basic** |
    | Indirizzo IP pubblico | Selezionare **Crea nuovo**. Se si vuole usare un indirizzo IP pubblico esistente, selezionare **Usa esistente**. |
    | Nome dell'indirizzo IP pubblico | Digitare **myPublicIP** nella casella di testo.|
    | Assegnazione | Selezionare **Dinamica** |
    | Aggiungi un indirizzo IPv6 pubblico | Selezionare **No**. </br> Per altre informazioni sugli indirizzi IPv6 e il servizio di bilanciamento del carico, vedere [Che cos'è IPv6 per la rete virtuale di Azure?](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)  |

3. Accettare i valori predefiniti per le impostazioni rimanenti e quindi selezionare **Rivedi e crea**.

4. Nella scheda **Rivedi e crea** selezionare **Crea**.   

    :::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/create-basic-load-balancer.png" alt-text="Creare un servizio di bilanciamento del carico di base" border="true":::

## <a name="create-load-balancer-resources"></a>Creare risorse di bilanciamento del carico

In questa sezione vengono configurati:

* Creare una rete virtuale.
* Le impostazioni del servizio di bilanciamento del carico per un pool di indirizzi back-end.
* Un probe di integrità.
* Una regola di bilanciamento del carico.

## <a name="virtual-network-and-parameters"></a>Rete virtuale e parametri

In questa sezione è necessario sostituire i parametri delle procedure con le informazioni riportate di seguito:

| Parametro                   | Valore                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupLB |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | Europa occidentale      |
| **\<IPv4-address-space>**   | 10.1.0.0\16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-a-backend-pool"></a>Creare un pool back-end

Un pool di indirizzi back-end contiene gli indirizzi IP delle schede di interfaccia di rete virtuale connesse al bilanciamento del carico. 

Creare il pool di indirizzi back-end **myBackendPool** per includere le macchine virtuali per il bilanciamento del carico del traffico Internet.

1. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myLoadBalancer** nell'elenco di risorse.

2. In **Impostazioni** selezionare **Pool back-end** e quindi selezionare **Aggiungi**.

3. Nella pagina **Aggiungi pool back-end** immettere o selezionare:
    
    | Impostazione | valore |
    | ------- | ----- |
    | Nome | Immettere **myBackendPool**. |
    | Rete virtuale | Selezionare **myVNET**. |
    | Associato a | Selezionare **Macchine virtuali** |

### <a name="create-a-health-probe"></a>Creare un probe di integrità

Il servizio di bilanciamento del carico monitora lo stato dell'app con un probe di integrità. 

Il probe di integrità aggiunge o rimuove le macchine virtuali nel servizio di bilanciamento del carico in base alla rispettiva risposta ai controlli di integrità. 

Creare un probe di integrità denominato **myHealthProbe** per monitorare l'integrità delle macchine virtuali.

1. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myLoadBalancer** nell'elenco di risorse.

2. In **Impostazioni** selezionare **Probe integrità** e quindi selezionare **Aggiungi**.
    
    | Impostazione | valore |
    | ------- | ----- |
    | Name (Nome) | Immettere **myHealthProbe**. |
    | Protocollo | Selezionare **HTTP**. |
    | Porta | Immettere **80**.|
    | Path | Immettere **/** |
    | Interval | Immettere **15** in **Intervallo** come numero di secondi tra i tentativi del probe. |
    | Soglia non integra | Selezionare **2** per **Soglia di non integrità** come numero di errori di probe consecutivi che devono verificarsi prima che una macchina virtuale venga considerata non integra.|

3. Selezionare **OK**.

### <a name="create-a-load-balancer-rule"></a>Creare una regola di bilanciamento del carico

Una regola di bilanciamento del carico consente di definire come il traffico verrà distribuito alle VM. È possibile definire la configurazione IP front-end per il traffico in ingresso e il pool IP back-end che riceve il traffico. La porta di origine e quella di destinazione vengono definite nella regola. 

In questa sezione verrà creata una regola di bilanciamento del carico:

* Denominata **myHTTPRule**.
* Nel front-end denominato **LoadBalancerFrontEnd**.
* In ascolto sulla **porta 80**.
* Che indirizza il traffico con carico bilanciato al back-end denominato **myBackendPool** sulla **porta 80**.

1. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myLoadBalancer** nell'elenco di risorse.

2. In **Impostazioni** selezionare **Regole di bilanciamento del carico** e quindi selezionare **Aggiungi**.

3. Usare questi valori per configurare la regola di bilanciamento del carico:
    
    | Impostazione | valore |
    | ------- | ----- |
    | Nome | Immettere **myHTTPRule**. |
    | Versione indirizzo IP | Selezionare **IPv4** |
    | Indirizzo IP front-end | Selezionare **LoadBalancerFrontend** |
    | Protocollo | selezionare **TCP**. |
    | Porta | Immettere **80**.|
    | Porta back-end | Immettere **80**. |
    | Pool back-end | Selezionare **myBackendPool**.|
    | Probe di integrità | Selezionare **myHealthProbe**. |
 
4. Accettare tutte le impostazioni predefinite e quindi selezionare **OK**.

## <a name="create-backend-servers"></a>Creare i server back-end

In questa sezione verrà illustrato come:

* Creare tre macchine virtuali per il pool back-end del servizio di bilanciamento del carico.
* Creare un set di disponibilità per le macchine virtuali.
* Installare IIS nelle macchine virtuali per testare il servizio di bilanciamento del carico.

### <a name="create-virtual-machines"></a>Creare macchine virtuali

Gli SKU degli indirizzi IP pubblici e gli SKU del bilanciamento del carico devono corrispondere. Per un servizio di bilanciamento del carico di base usare macchine virtuali con indirizzi IP di base nel pool back-end. 

In questa sezione verranno create tre macchine virtuali (**myVM1**, **myVM2** e **myVM3**) con un indirizzo IP pubblico di base.  

Le tre macchine virtuali verranno aggiunte a un set di disponibilità denominato **myAvailabilitySet**.

Queste macchine virtuali vengono aggiunte al pool back-end del servizio di bilanciamento del carico creato in precedenza.

1. Nella parte superiore sinistra del portale selezionare **Crea una risorsa** > **Calcolo** > **Macchina virtuale**. 
   
2. In **Crea macchina virtuale** digitare o selezionare i valori nella scheda **Nozioni di base**:

    | Impostazione | valore                                          |
    |-----------------------|----------------------------------|
    | **Dettagli del progetto** |  |
    | Subscription | Selezionare la sottoscrizione ad Azure |
    | Gruppo di risorse | Selezionare **myResourceGroupLB** |
    | **Dettagli istanza** |  |
    | Nome macchina virtuale | Immettere **myVM1** |
    | Region | Selezionare **Europa occidentale** |
    | Opzioni di disponibilità | Selezionare **Set di disponibilità** |
    | Set di disponibilità | Selezionare **Crea nuovo**. </br> Immettere **myAvailabilitySet** in **Nome**. </br> Selezionare **OK**. |
    | Immagine | **Windows Server 2019 Datacenter** |
    | Istanza Spot di Azure | Selezionare **No** |
    | Dimensione | Scegliere le dimensioni della macchina virtuale o usare l'impostazione predefinita |
    | **Account amministratore** |  |
    | Username | Immettere un nome utente |
    | Password | Immettere una password |
    | Conferma password | Reimmettere la password |

3. Selezionare la scheda **Rete**, oppure selezionare **Avanti: Dischi**, quindi **Avanti: Rete**.
  
4. Nella scheda Rete selezionare o immettere:

    | Impostazione | valore |
    |-|-|
    | **Interfaccia di rete** |  |
    | Rete virtuale | Selezionare **myVNet** |
    | Subnet | Selezionare **myBackendSubnet** |
    | IP pubblico | Selezionare **Crea nuovo** </br> Immettere **myVM-ip** in Nome. </br> Selezionare **OK**. |
    | Gruppo di sicurezza di rete della scheda di interfaccia di rete | Selezionare **Avanzato**|
    | Configura gruppo di sicurezza di rete | Selezionare **Crea nuovo**. </br> Nella pagina **Crea gruppo di sicurezza di rete** immettere **myNSG** in **Nome**. </br> In **Regole in ingresso** selezionare **+ Aggiungi una regola in ingresso**. </br> In **Intervalli di porte di destinazione** immettere **80**. </br> In **Priorità** immettere **100**. </br> In **Nome** immettere **myHTTPRule** </br> Selezionare **Aggiungi** </br> Selezionare **OK**. |
    | **Bilanciamento del carico**  |
    | Associare questa macchina virtuale a una soluzione di bilanciamento del carico esistente? | Selezionare **No** |
 
5. Selezionare la scheda **Gestione** oppure **Avanti** > **Gestione**.

6. Nella scheda **Gestione** selezionare o immettere:
    | Impostazione | valore |
    |-|-|
    | **Monitoring** | |
    | Diagnostica di avvio | Selezionare **Disattivato** |

7. Selezionare **Rivedi e crea**. 
  
8. Rivedere le impostazioni e quindi selezionare **Crea**.

9. Seguire i passaggi da 1 a 8 per creare due macchine virtuali aggiuntive con i valori seguenti e tutte le altre impostazioni identiche a **myVM1**:

    | Impostazione | VM 2| VM 3|
    | ------- | ----- |---|
    | Nome |  **myVM2** |**myVM3**|
    | Set di disponibilità| Selezionare **myAvailabilitySet** | Selezionare **myAvailabilitySet**|
    | Gruppo di sicurezza di rete | Selezionare il gruppo di sicurezza di rete **myNSG** esistente| Selezionare il gruppo di sicurezza di rete **myNSG** esistente|

---

## <a name="install-iis"></a>Installare IIS

1. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine nell'elenco di risorse selezionare **myVM1**, che si trova nel gruppo di risorse **myResourceGroupLB**.

2. Nella pagina **Panoramica** selezionare **Connetti** per scaricare il file RDP per la macchina virtuale.

3. Aprire il file RDP.

4. Accedere alla VM con le credenziali fornite durante la creazione di questa VM.

5. Nel desktop del server passare a **Strumenti di amministrazione Windows**>**Windows PowerShell**.

6. Nella finestra di PowerShell eseguire i comandi seguenti per:

    * Installare il server IIS
    * Rimuovere il file iisstart.htm predefinito
    * Aggiungere un nuovo file iisstart.htm che visualizzi il nome della macchina virtuale:

   ```powershell
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
7. Chiudere la sessione RDP con **myVM1**.

8. Ripetere i passaggi da 1 a 6 per installare IIS e il file iisstart.htm aggiornato in **myVM2** e **myVM3**.

## <a name="test-the-load-balancer"></a>Testare il servizio di bilanciamento del carico

1. Individuare l'indirizzo IP pubblico del servizio di bilanciamento del carico nella schermata **Panoramica**. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myPublicIP**.

2. Copiare l'indirizzo IP pubblico e quindi incollarlo nella barra degli indirizzi del browser. Nel browser verrà visualizzata la pagina predefinita del server Web IIS.

   ![Server Web IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Per visualizzare il servizio di bilanciamento del carico distribuire il traffico tra tutte e tre macchine virtuali, è possibile personalizzare la pagina predefinita del server Web IIS di ciascuna macchina virtuale e quindi forzare l'aggiornamento del Web browser dal computer client.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse, il servizio di bilanciamento del carico e tutte le risorse correlate. A questo scopo, selezionare il gruppo di risorse **myResourceGroupLB** che contiene le risorse e quindi selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido:

* È stato creato un servizio di bilanciamento del carico Standard o Basic di Azure
* Sono state collegate tre macchine virtuali al servizio di bilanciamento del carico.
* È stata configurata la regola del traffico di bilanciamento del carico, il probe di integrità e quindi è stato testato il servizio di bilanciamento del carico. 

Per altre informazioni su Azure Load Balancer, vedere [Informazioni su Azure Load Balancer](load-balancer-overview.md) e le [domande frequenti su Load Balancer](load-balancer-faqs.md).

Altre informazioni su [Load Balancer e zone di disponibilità](load-balancer-standard-availability-zones.md).
