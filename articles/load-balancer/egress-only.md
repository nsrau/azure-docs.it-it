---
title: Configurazione del servizio di bilanciamento del carico solo in uscita
titleSuffix: Azure Load Balancer
description: Questo articolo illustra come creare un servizio di bilanciamento del carico interno con NAT in uscita
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2020
ms.author: allensu
ms.openlocfilehash: ee264a22de5ce094e8a4c1335ace77cbbba49270
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94694994"
---
# <a name="outbound-only-load-balancer-configuration"></a>Configurazione del servizio di bilanciamento del carico solo in uscita

Usare una combinazione di bilanciamento del carico standard interno ed esterno per creare connettività in uscita per le macchine virtuali dietro un servizio di bilanciamento del carico interno. 

Questa configurazione fornisce la NAT in uscita per uno scenario di bilanciamento del carico interno, producendo un'installazione "solo in uscita" per il pool back-end.


<p align="center">
  <img src="./media/egress-only/load-balancer-egress-only.png" alt="Figure depicts a egress only load balancer configuration." width="500" title="NAT di rete virtuale">
</p>


<!-- 
:::image type="content" source="./media/egress-only/load-balancer-egress-only.png" alt-text="Figure depicts a egress only load balancer configuration" border="true":::
-->


*Figura: configurazione del servizio di bilanciamento del carico solo in uscita*

I passaggi necessari sono:

1. Creare una rete virtuale con un host Bastion.
2. Creare una macchina virtuale con un solo indirizzo IP privato.
3. Creare bilanciamenti del carico standard interni e pubblici.
4. Aggiungere i pool back-end a entrambi i bilanciamenti del carico e collocare la macchina virtuale in ogni pool.
5. Connettersi alla macchina virtuale tramite l'host Bastion e:
    1. Testare la connettività in uscita, 
    2. Configurare una regola in uscita per il servizio di bilanciamento del carico pubblico.
    3. Eseguire nuovamente il test della connettività in uscita.

## <a name="create-virtual-network-and-virtual-machine"></a>Creare una rete virtuale e una macchina virtuale

Creare una rete virtuale con due subnet:

* Subnet primaria
* Subnet Bastion

Creare una macchina virtuale nella nuova rete virtuale.

### <a name="create-the-virtual-network"></a>Creare la rete virtuale

1. [Accedere](https://portal.azure.com) al portale di Azure.

2. In alto a sinistra nella schermata selezionare **Crea una risorsa > Rete > Rete virtuale** o cercare **Rete virtuale** nella casella di ricerca.

2. In **Crea rete virtuale** immettere o selezionare queste informazioni nella scheda **Generale**:

    | **Impostazione**          | **Valore**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Dettagli del progetto**  |                                                                 |
    | Subscription     | Selezionare la sottoscrizione ad Azure                                  |
    | Gruppo di risorse   | Selezionare **Crea nuovo**. </br> Immettere **myResourceGroupLB**. </br> Selezionare **OK**. |
    | **Dettagli istanza** |                                                                 |
    | Nome             | Immettere **myVNet**                                    |
    | Region           | Seleziona **Stati Uniti orientali 2** |

3. Selezionare la scheda **Indirizzi IP** oppure il pulsante **Avanti: Indirizzi IP** nella parte inferiore della pagina.

4. Nella scheda **Indirizzi IP** immettere queste informazioni:

    | Impostazione            | valore                      |
    |--------------------|----------------------------|
    | Spazio indirizzi IPv4 | Immettere **10.1.0.0/16** |

5. In **Nome subnet** selezionare la parola **predefinito**.

6. In **Modifica subnet** immettere queste informazioni:

    | Impostazione            | valore                      |
    |--------------------|----------------------------|
    | Nome della subnet | Immettere **myBackendSubnet** |
    | Intervallo di indirizzi subnet | Immettere **10.1.0.0/24** |

7. Selezionare **Salva**.

8. Selezionare la scheda **Sicurezza**.

9. In **BastionHost** selezionare **Abilita**. Immettere le informazioni seguenti:

    | Impostazione            | valore                      |
    |--------------------|----------------------------|
    | Nome bastion | Immettere **myBastionHost** |
    | Spazio indirizzi della subnet AzureBastionSubnet | Immettere **10.1.1.0/24** |
    | Indirizzo IP pubblico | Selezionare **Crea nuovo**. </br> Per **Nome** immettere **myBastionIP**. </br> Selezionare **OK**. |


8. Selezionare la scheda **Rivedi e crea** oppure il pulsante **Rivedi e crea**.

9. Selezionare **Create** (Crea).

### <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

1. Nella parte superiore sinistra del portale selezionare **Crea una risorsa** > **Calcolo** > **Macchina virtuale**. 
   
2. In **Crea macchina virtuale** digitare o selezionare i valori nella scheda **Nozioni di base**:

    | Impostazione | Valore                                          |
    |-----------------------|----------------------------------|
    | **Dettagli del progetto** |  |
    | Subscription | Selezionare la sottoscrizione ad Azure |
    | Gruppo di risorse | Selezionare **myResourceGroupLB** |
    | **Dettagli istanza** |  |
    | Nome macchina virtuale | Immettere **myVM** |
    | Area | Seleziona **Stati Uniti orientali 2** |
    | Opzioni di disponibilità | Selezionare **La ridondanza dell'infrastruttura non è richiesta** |
    | Immagine | Selezionare **Windows Server 2019 Datacenter** |
    | Istanza Spot di Azure | Selezionare **No** |
    | Dimensione | Scegliere le dimensioni della macchina virtuale o usare l'impostazione predefinita |
    | **Account amministratore** |  |
    | Username | Immettere un nome utente |
    | Password | Immettere una password |
    | Conferma password | Reimmettere la password |
    | **Regole porta in ingresso** |  |
    | Porte in ingresso pubbliche | Selezionare **Consenti porte selezionate** |
    | Selezionare le porte in ingresso | Selezionare **RDP (3389)** |

3. Selezionare la scheda **Rete**, oppure selezionare **Avanti: Dischi**, quindi **Avanti: Rete**.
  
4. Nella scheda Rete selezionare o immettere:

    | Impostazione | Valore |
    |-----|------------|
    | **Interfaccia di rete** |  |
    | Rete virtuale | **myVNet** |
    | Subnet | **myBackendSubnet** |
    | IP pubblico | Selezionare **Nessuno**. |
    | Gruppo di sicurezza di rete della scheda di interfaccia di rete | Selezionare **Nessuno**|
    | Posizionare questa macchina virtuale dietro una soluzione di bilanciamento del carico esistente? | Selezionare **No** |
   
5. Selezionare la scheda **Gestione** oppure **Avanti** > **Gestione**.

6. Nella scheda **Gestione** selezionare o immettere:
    
    | Impostazione | Valore |
    |-|-|
    | **Monitoring** |  |
    | Diagnostica di avvio | Selezionare **Disattivato** |
   
7. Selezionare **Rivedi e crea**. 
  
8. Rivedere le impostazioni e quindi selezionare **Crea**.

## <a name="create-load-balancers-and-test-connectivity"></a>Creare bilanciamenti del carico e testare la connettività

Usare il portale di Azure per creare:

* Servizio di bilanciamento del carico interno
* Bilanciamento del carico pubblico
 
Aggiungere la macchina virtuale creata al pool back-end di ogni.  Si configurerà quindi una configurazione per consentire solo la connettività in uscita dalla macchina virtuale, test prima e dopo.

### <a name="create-internal-load-balancer"></a>Creare un servizio di bilanciamento del carico interno

1. In alto a sinistra nella schermata selezionare **Crea una risorsa** > **Rete** > **Load Balancer**.

2. Nella scheda **Informazioni di base** della pagina **Crea servizio di bilanciamento del carico** immettere o selezionare le informazioni seguenti: 

    | Impostazione                 | Valore                                              |
    | ---                     | ---                                                |
    | Subscription               | Selezionare la propria sottoscrizione.    |    
    | Resource group         | Selezionare il gruppo **myResourceGroupLB** creato nel passaggio precedente.|
    | Nome                   | Immettere **myInternalLoadBalancer**                                   |
    | Region         | Selezionare **Stati Uniti orientali 2**.                                        |
    | Type          | Selezionare **Interno**.                                        |
    | SKU           | selezionare **Standard** |
    | Rete virtuale | Selezionare la rete **myVNet** creata nel passaggio precedente. |
    | Subnet  | Selezionare la subnet **myBackendSubnet** creata nel passaggio precedente. |
    | Assegnazione indirizzi IP | Selezionare **Dinamico**. |

3. Accettare i valori predefiniti per le impostazioni rimanenti e quindi selezionare **Rivedi e crea**.

4. Nella scheda **Rivedi e crea** selezionare **Crea**.   

### <a name="create-public-load-balancer"></a>Creare un servizio di bilanciamento del carico pubblico

1. In alto a sinistra nella schermata selezionare **Crea una risorsa** > **Rete** > **Load Balancer**.

2. Nella scheda **Informazioni di base** della pagina **Crea servizio di bilanciamento del carico** immettere o selezionare le informazioni seguenti: 

    | Impostazione                 | Valore                                              |
    | ---                     | ---                                                |
    | Subscription               | Selezionare la propria sottoscrizione.    |    
    | Resource group         | Selezionare **Crea nuovo** e immettere **myResourceGroupLB** nella casella di testo.|
    | Nome                   | Immettere **myPublicLoadBalancer**                                   |
    | Region         | Selezionare **Stati Uniti orientali 2**.                                        |
    | Type          | Selezionare **Pubblica**.                                        |
    | SKU           | selezionare **Standard** |
    | Indirizzo IP pubblico | Selezionare **Crea nuovo**. |
    | Nome dell'indirizzo IP pubblico | Immettere **myFrontendIP** nella casella di testo.|
    | Zona di disponibilità | Selezionare **Con ridondanza della zona**. |
    | Aggiungi un indirizzo IPv6 pubblico | Selezionare **No**. |

3. Accettare i valori predefiniti per le impostazioni rimanenti e quindi selezionare **Rivedi e crea**.

4. Nella scheda **Rivedi e crea** selezionare **Crea**.   
   
### <a name="create-internal-backend-address-pool"></a>Crea pool di indirizzi back-end interno

Creare il pool di indirizzi back-end **myInternalBackendPool**:

1. Selezionare **tutti i servizi** nel menu a sinistra, selezionare **tutte le risorse** e quindi selezionare **myInternalLoadBalancer** nell'elenco delle risorse.

2. In **Impostazioni** selezionare **Pool back-end** e quindi selezionare **Aggiungi**.

3. Nella pagina **Aggiungi un pool back-end** digitare **myInternalBackendPool** come nome del pool back-end per nome.
 
4. In **Rete virtuale** selezionare **myVNet**.

5. In **macchine virtuali** selezionare **Aggiungi** e scegliere **myVM**.
 
6. Selezionare **Aggiungi**.

### <a name="create-public-backend-address-pool"></a>Crea pool di indirizzi back-end pubblico

Creare il pool di indirizzi back-end **myPublicBackendPool**:

1. Selezionare **tutti i servizi** nel menu a sinistra, selezionare **tutte le risorse** e quindi selezionare **myPublicLoadBalancer** nell'elenco delle risorse.

2. In **Impostazioni** selezionare **Pool back-end** e quindi selezionare **Aggiungi**.

3. Nella pagina **Aggiungi un pool back-end** digitare **myPublicBackendPool** come nome del pool back-end per nome.

4. In **Rete virtuale** selezionare **myVNet**.
 
5. In **macchine virtuali** selezionare **Aggiungi** e scegliere **myVM**.
 
6. Selezionare **Aggiungi**.

### <a name="test-connectivity-before-outbound-rule"></a>Testare la connettività prima della regola in uscita

1. Selezionare **tutti i servizi** nel menu a sinistra, selezionare **tutte le risorse** e quindi nell'elenco delle risorse selezionare **myVM** che si trova nel gruppo di risorse **myResourceGroupLB** .

2. Nella pagina **Panoramica** selezionare **Connetti**, quindi **Bastion**.

4. Immettere il nome utente e la password specificati durante la creazione della VM.

5. Selezionare **Connetti**.

6. Aprire Internet Explorer.

7. Immettere **https://whatsmyip.org** nella barra degli indirizzi.

8. La connessione deve avere esito negativo. Per impostazione predefinita, il servizio di bilanciamento del carico pubblico standard [non consente il traffico in uscita senza una regola in uscita definita](load-balancer-overview.md#securebydefault).
 
### <a name="create-a-public-load-balancer-outbound-rule"></a>Creare una regola in uscita del servizio di bilanciamento del carico pubblico

1. Selezionare **tutti i servizi** nel menu a sinistra, selezionare **tutte le risorse** e quindi selezionare **myPublicLoadBalancer** nell'elenco delle risorse.

2. In **Impostazioni** selezionare **Regole in uscita** e quindi **Aggiungi**.

3. Usare questi valori per configurare le regole in uscita:

    | Impostazione | Valore |
    | ------- | ----- |
    | Name (Nome) | Immettere **myOutboundRule**. |
    | Indirizzo IP front-end | Selezionare **LoadBalancerFrontEnd**.|
    | Timeout di inattività (minuti) | Spostare il dispositivo di scorrimento su **15 minuti**.|
    | Reimpostazione TCP | Selezionare **Enabled**.|
    | Pool back-end | Selezionare **myPublicBackendPool**.| |
    | Allocazione porte -> Allocazione porte | Selezionare **Usa il numero predefinito di porte in uscita** |

4. Selezionare **Aggiungi**.

### <a name="test-connectivity-after-outbound-rule"></a>Testare la connettività dopo la regola in uscita

1. Selezionare **tutti i servizi** nel menu a sinistra, selezionare **tutte le risorse** e quindi nell'elenco delle risorse selezionare **myVM** che si trova nel gruppo di risorse **myResourceGroupLB** .

2. Nella pagina **Panoramica** selezionare **Connetti**, quindi **Bastion**.

4. Immettere il nome utente e la password specificati durante la creazione della VM.

5. Selezionare **Connetti**.

6. Aprire Internet Explorer.

7. Immettere **https://whatsmyip.org** nella barra degli indirizzi.

8. La connessione deve avere esito positivo.

9. L'indirizzo IP visualizzato deve essere l'indirizzo IP front-end di **myPublicLoadBalancer**.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse, i bilanciamenti del carico, la macchina virtuale e tutte le risorse correlate. 

A tale scopo, selezionare il gruppo di risorse **myResourceGroupLB** , quindi selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata creata una configurazione "solo uscita" con una combinazione di bilanciamenti del carico pubblici e interni.  

Questa configurazione consente di bilanciare il carico del traffico interno in ingresso nel pool back-end evitando comunque le connessioni in ingresso pubbliche.

- Informazioni sulle [Azure Load Balancer](load-balancer-overview.md).
- Informazioni sulle [connessioni in uscita in Azure](load-balancer-outbound-connections.md).
- [Domande frequenti](load-balancer-faqs.md)sul servizio di bilanciamento del carico.
- Scopri di più su [Azure Bastion](../bastion/bastion-overview.md)