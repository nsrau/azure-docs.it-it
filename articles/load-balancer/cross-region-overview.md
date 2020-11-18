---
title: Bilanciamento del carico tra aree (anteprima)
titleSuffix: Azure Load Balancer
description: Panoramica del livello di bilanciamento del carico tra aree per Azure Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: allensu
ms.custom: references_regions
ms.openlocfilehash: 8b2f8e82cac1d26689659286a5a0f81f06fb68ca
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94695062"
---
# <a name="cross-region-load-balancer-preview"></a>Bilanciamento del carico tra aree (anteprima)

Azure Load Balancer distribuisce il traffico in ingresso che arriva al front-end del servizio di bilanciamento del carico alle istanze del pool back-end.

Azure Load Balancer Standard supporta il bilanciamento del carico tra aree che consente scenari a disponibilità elevata con ridondanza geografica, ad esempio:

* Traffico in ingresso proveniente da più aree.
* [Failover globale immediato](#regional-redundancy) alla successiva distribuzione a livello di area ottimale.
* Caricare la distribuzione tra aree nell'area di Azure più vicina con [latenza ultra](#ultra-low-latency).
* Possibilità di eseguire la [scalabilità verticale o verticale](#ability-to-scale-updown-behind-a-single-endpoint) dietro un singolo endpoint.
* [Indirizzo IP statico](#static-ip)
* [Conservazione IP client](#client-ip-preservation)
* Crea una soluzione di [bilanciamento del carico esistente](#build-cross-region-solution-on-existing-azure-load-balancer) senza alcuna curva di apprendimento

> [!IMPORTANT]
> Il servizio di bilanciamento del carico tra aree è attualmente disponibile in anteprima e non è disponibile a livello generale.  Per accedere all'anteprima per il servizio di bilanciamento del carico tra aree, contattare: [crossregionlb@microsoft.com](mailto:crossregionlb@microsoft.com) . </br> </br>
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Il bilanciamento del carico tra aree offre gli stessi vantaggi di prestazioni elevate e bassa latenza del servizio Load Balancer standard regionale. 

La configurazione IP front-end del servizio di bilanciamento del carico tra aree è statica e viene annunciata nella [maggior parte delle aree di Azure](#participating-regions).

:::image type="content" source="./media/cross-region-overview/cross-region-load-balancer.png" alt-text="Diagramma del servizio di bilanciamento del carico tra aree." border="true":::

> [!NOTE]
> La porta back-end della regola di bilanciamento del carico nel servizio di bilanciamento del carico tra aree deve corrispondere alla porta front-end della regola di bilanciamento del carico/regola NAT in ingresso nel servizio di bilanciamento del carico standard regionale. 

### <a name="regional-redundancy"></a>Ridondanza a livello di area

Configurare la ridondanza a livello di area aggiungendo un indirizzo IP pubblico front-end globale ai bilanciamenti del carico esistenti. 

Se si verifica un errore in un'area, il traffico viene indirizzato al servizio di bilanciamento del carico a livello di area integro più vicino.  

Il probe di integrità del servizio di bilanciamento del carico tra aree raccoglie informazioni sulla disponibilità ogni 20 secondi. Se un servizio di bilanciamento del carico a livello di area ne rilascia la disponibilità a 0, il servizio di bilanciamento del carico tra aree rileverà l'errore. Il servizio di bilanciamento del carico a livello di area viene quindi tolto dalla rotazione. 

:::image type="content" source="./media/cross-region-overview/global-region-view.png" alt-text="Diagramma della visualizzazione traffico dell'area globale." border="true":::

### <a name="ultra-low-latency"></a>Latenza ultra-bassa

L'algoritmo di bilanciamento del carico con prossimità geografica è basato sulla posizione geografica degli utenti e delle distribuzioni internazionali. 

Il traffico avviato da un client raggiungerà l'area di partecipazione più vicina e passerà attraverso la backbone della rete globale Microsoft per giungere alla distribuzione regionale più vicina. 

Ad esempio, si dispone di un servizio di bilanciamento del carico tra aree con bilanciamento del carico standard nelle aree di Azure:

* Stati Uniti occidentali
* Europa settentrionale

Se un flusso viene avviato da Seattle, il traffico entra negli Stati Uniti occidentali. Questa area rappresenta l'area di partecipazione più vicina a Seattle. Il traffico viene indirizzato al servizio di bilanciamento del carico dell'area più vicino, che è Stati Uniti occidentali.

Il servizio di bilanciamento del carico tra aree di Azure usa l'algoritmo di bilanciamento del carico con prossimità geografica per la decisione di routing. 

La modalità di distribuzione del carico configurata dei bilanciamenti del carico a livello di area viene usata per stabilire la decisione di routing finale quando vengono usati più bilanciamenti del carico a livello di area per la prossimità

Per altre informazioni, vedere [Configurare la modalità di distribuzione per Azure Load Balancer](./load-balancer-distribution-mode.md).


### <a name="ability-to-scale-updown-behind-a-single-endpoint"></a>Possibilità di eseguire la scalabilità verticale e verticale dietro un singolo endpoint

Quando si espone l'endpoint globale di un servizio di bilanciamento del carico tra aree ai clienti, è possibile aggiungere o rimuovere le distribuzioni regionali dietro l'endpoint globale senza alcun intervento da parte del cliente. 

<!---To learn about how to add or remove a regional deployment from the backend, read more [here](TODO: Insert CLI doc here).--->

### <a name="static-ip"></a>IP statico
Il servizio di bilanciamento del carico tra aree è dotato di un IP pubblico statico, che garantisce che l'indirizzo IP rimanga invariato. Per altre informazioni sull'IP statico, vedere [qui](../virtual-network/public-ip-addresses.md#allocation-method)

### <a name="client-ip-preservation"></a>Conservazione IP client
Il servizio di bilanciamento del carico tra aree è un servizio di bilanciamento del carico di rete pass-through di livello 4. Questo pass-through conserva l'IP originale del pacchetto.  L'IP originale è disponibile per il codice in esecuzione nella macchina virtuale. Questa conservazione consente di applicare la logica specifica di un indirizzo IP.

## <a name="build-cross-region-solution-on-existing-azure-load-balancer"></a>Compila la soluzione tra aree in Azure Load Balancer esistenti
Il pool back-end del servizio di bilanciamento del carico tra aree contiene uno o più bilanciamenti del carico a livello di area. 

Aggiungere le distribuzioni del servizio di bilanciamento del carico esistenti a un servizio di bilanciamento del carico tra aree per una distribuzione a disponibilità elevata e tra aree.

L' **area principale** è la posizione in cui viene distribuito il servizio di bilanciamento del carico tra aree. Questa area non influisce sul modo in cui il traffico verrà indirizzato. Se un'area di casa diventa inattiva, non influisce sul flusso di traffico.

### <a name="home-regions"></a>Aree domestiche
* Stati Uniti orientali 2
* Stati Uniti occidentali
* Europa occidentale
* Asia sud-orientale
* Stati Uniti centrali
* Europa settentrionale
* Asia orientale

> [!NOTE]
> È possibile distribuire il servizio di bilanciamento del carico tra aree solo in una delle 8 aree precedenti.

Un' **area partecipante** è la posizione in cui è disponibile l'indirizzo IP pubblico globale del servizio di bilanciamento del carico. 

Il traffico avviato dall'utente passerà all'area partecipata più vicina attraverso la rete core Microsoft. 

Il servizio di bilanciamento del carico tra aree instrada il traffico al servizio di bilanciamento del carico a livello di area appropriato.

### <a name="participating-regions"></a>Aree partecipanti
* Stati Uniti orientali 
* Europa occidentale 
* Stati Uniti centrali 
* Stati Uniti orientali 2 
* Stati Uniti occidentali 
* Europa settentrionale 
* Stati Uniti centro-meridionali 
* Stati Uniti occidentali 2 
* Regno Unito meridionale 
* Asia sud-orientale 
* Stati Uniti centro-settentrionali 
* Giappone orientale 
* Asia orientale 
* Stati Uniti centro-occidentali 
* Australia sud-orientale 
* Australia orientale 
* India centrale 

## <a name="limitations"></a>Limitazioni

* Le configurazioni IP front-end tra aree sono solo pubbliche. Un front-end interno non è al momento supportato.

* Il servizio di bilanciamento del carico interno o privato non può essere aggiunto al pool back-end del servizio di bilanciamento del carico tra più aree 

* Le configurazioni IP front-end IPv6 tra più aree non sono supportate. 

* Attualmente non è possibile configurare un probe di integrità. Un probe di integrità predefinito raccoglie automaticamente le informazioni sulla disponibilità relative al servizio di bilanciamento del carico a livello di area ogni 20 secondi. 

## <a name="pricing-and-sla"></a>Prezzi e contratto di servizio
Il servizio di bilanciamento del carico tra aree, condivide il [contratto](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/ ) di servizio del servizio di bilanciamento del carico standard.

 
## <a name="next-steps"></a>Passaggi successivi

- Per iniziare a usare un servizio di bilanciamento del carico, vedere [Creare un servizio di bilanciamento del carico Standard pubblico](quickstart-load-balancer-standard-public-portal.md).
- Altre informazioni su [Azure Load Balancer](load-balancer-overview.md).
- [Domande frequenti](load-balancer-faqs.md) sul servizio di bilanciamento del carico