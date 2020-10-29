---
title: Test di simulazione di protezione DDoS di Azure
description: Informazioni su come eseguire il test tramite simulazioni
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: eff738e24b3abce52e80291c55a3ae64c3c8c853
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92905495"
---
# <a name="test-through-simulations"></a>Eseguire test tramite simulazioni

È consigliabile testare le ipotesi su come i servizi risponderanno a un attacco effettuando simulazioni periodiche. Durante il test, verificare che i servizi o le applicazioni continuino a funzionare come previsto e che non ci siano interruzioni nell'esperienza dell'utente finale. Identificare le lacune sia dal punto di vista tecnologico che da quello dei processi e integrarle nella strategia di risposta DDoS. È consigliabile eseguire tali test in ambienti di staging o in ore non di punta per ridurre al minimo l'impatto sull'ambiente di produzione.

Abbiamo collaborato con [BreakingPoint cloud](https://www.ixiacom.com/products/breakingpoint-cloud), un generatore di traffico self-service, per creare un'interfaccia in cui i clienti di Azure possono generare traffico da endpoint pubblici abilitati per la protezione DDoS per simulazioni. È possibile usare la simulazione per:

- Convalidare il modo in cui Protezione DDoS di Azure contribuisce a proteggere le risorse di Azure dagli attacchi DDoS.
- Ottimizzare il processo di risposta agli eventi imprevisti durante un attacco DDoS.
- Documentare la conformità DDoS.
- Eseguire il training dei team di sicurezza di rete.

## <a name="prerequisites"></a>Prerequisiti

- Prima di eseguire i passaggi di questa esercitazione, è necessario creare prima di tutto un [piano di protezione standard DDoS di Azure](manage-ddos-protection.md) con indirizzi IP pubblici protetti.
- Per prima cosa, è necessario creare un account con [BreakingPoint cloud](http://breakingpoint.cloud/). 

## <a name="configure-a-ddos-test-attack"></a>Configurare un attacco di test DDoS

1. Immettere o selezionare i valori seguenti e quindi selezionare **Avvia test** :

    |Impostazione        |valore                                              |
    |---------      |---------                                          |
    |Indirizzo IP di destinazione           | Immettere uno degli indirizzi IP pubblici che si desidera testare.                     |
    |Numero porta   | Immettere _443_ .                       |
    |Profilo DDoS | Selezionare **TCP SYN Flood** .|
    |Dimensioni test       | Selezionare 200.000 **PPS, 100 Mbps e 8 IP di origine.**                                  |
    |Durata test | Selezionare **10 minuti** .|

Il punto dovrebbe essere simile al seguente:

![Esempio di simulazione di attacco DDoS: cloud BreakingPoint](./media/ddos-attack-simulation/ddos-attack-simulation-example-1.png)

## <a name="monitor-and-validate"></a>Monitorare e convalidare

1. Accedere a https://portal.azure.com e andare alla sottoscrizione.
1. Selezionare l'indirizzo IP pubblico su cui è stato testato l'attacco.
1. In **Monitoraggio** selezionare **Metrica** .
1. Per **metrica** selezionare _sotto attacco DDoS o no_ .

Quando la risorsa è sotto attacco, si noterà che il valore cambia da **0** a **1** , come nell'immagine seguente:

![Esempio di simulazione di attacco DDoS: portale](./media/ddos-attack-simulation/ddos-attack-simulation-example-2.png)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [visualizzare e configurare la telemetria di protezione DDoS](telemetry-monitoring-alerting.md).
- Informazioni su come [configurare i report di mitigazione degli attacchi DDoS e i log dei flussi](reports-and-flow-logs.md).
- Informazioni su come [coinvolgere la risposta rapida DDoS](ddos-rapid-response.md).