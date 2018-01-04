---
title: Creare una regola basata sul percorso per un gateway applicazione - Portale di Azure | Microsoft Docs
description: Informazioni su come creare una regola basata sul percorso per un gateway applicazione tramite il portale di Azure.
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 87bd93bc-e1a6-45db-a226-555948f1feb7
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/03/2017
ms.author: davidmu
ms.openlocfilehash: b207e7e7bd83e56db68288190c7bedafa8b5b7fa
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="create-a-path-based-rule-for-an-application-gateway-by-using-the-azure-portal"></a>Creare una regola basata sul percorso per un gateway applicazione usando il portale di Azure

> [!div class="op_single_selector"]
> * [Azure portal](application-gateway-create-url-route-portal.md)
> * [PowerShell per Azure Resource Manager](application-gateway-create-url-route-arm-ps.md)
> * [Interfaccia della riga di comando di Azure 2.0](application-gateway-create-url-route-cli.md)

Il routing basato sul percorso dell'URL consente di associare le route in base al percorso dell'URL delle richieste HTTP. Verifica se è disponibile una route per il pool back-end configurato per l'URL elencato nel gateway applicazione e quindi invia il traffico di rete al pool definito. In genere il routing basato sul percorso dell'URL viene usato per le richieste di bilanciamento del carico per diversi tipi di contenuto tra vari pool di server back-end.

Per i gateway applicazione sono disponibili due tipi di regole: di base e basate sul percorso dell'URL. Il tipo di regola di base fornisce il servizio di round robin per i pool back-end. Le regole basate sul percorso, oltre alla distribuzione round-robin, usano anche il modello del percorso dell'URL della richiesta quando si sceglie il pool back-end appropriato.

## <a name="scenario"></a>Scenario

Lo scenario seguente crea una regola basata sul percorso in un gateway applicazione esistente.
Lo scenario presuppone che sia già stata seguita la procedura per [creare un gateway applicazione usando il portale](application-gateway-create-gateway-portal.md).

![Route dell'URL][scenario]

## <a name="createrule"></a>Creare la regola basata sul percorso

Ogni regola basata sul percorso richiede il proprio listener. Prima di creare la regola, verificare di disporre di un listener disponibile all'uso.

### <a name="step-1"></a>Passaggio 1

Passare al [portale di Azure](http://portal.azure.com) e selezionare un gateway applicazione esistente. Fare clic su **Regole**.

![Panoramica del gateway applicazione][1]

### <a name="step-2"></a>Passaggio 2

Fare clic sul pulsante **Basata sul percorso** per aggiungere una nuova regola di questo tipo.

### <a name="step-3"></a>Passaggio 3

Il pannello **Add path-based rule** (Aggiungi regola basata sul percorso) contiene due sezioni. La prima è la sezione in cui sono stati definiti il listener, il nome della regola e le impostazioni del percorso predefinito. Le impostazioni del percorso predefinite sono per le route che non rientrano nella route personalizzata basata sul percorso. La seconda sezione dl pannello **Add path-based rule** (Aggiungi regola basata sul percorso) viene usata per definire le regole stesse basate sul percorso.

**Impostazioni di base**

* **Nome**: nome descrittivo della regola accessibile nel portale.
* **Listener**: listener usato per la regola.
* **Pool back-end predefinito**: back-end da usare per la regola predefinita.
* **Impostazioni HTTP predefinite**: impostazioni HTTP da usare per la regola predefinita.

**Impostazioni delle regole basate sul percorso**

* **Nome**: nome descrittivo della regola basata sul percorso.
* **Percorsi**: il percorso cercato dalla regola per l'inoltro del traffico.
* **Pool back-end**: back-end da usare per la regola.
* **Impostazione HTTP**: impostazioni HTTP da usare con questa regola.

> [!IMPORTANT]
> L'impostazione **Percorsi** è l'elenco dei modelli di percorso usati per la corrispondenza. Ogni modello deve iniziare con una barra rovesciata ("/") e l'unica posizione in cui è consentito l'asterisco è alla fine. Sono esempi validi: /xyz, /xyz*e /xyz/*.  

![Pannello Add path-based rule (Aggiungi regola basata sul percorso)][2]

L'aggiunta di una regola basata sul percorso a un gateway applicazione esistente è un processo semplice con il portale di Azure. Dopo aver creato una regola basata sul percorso, è possibile modificarla affinché includa regole aggiuntive. 

![Aggiunta di altre regole basate sul percorso][3]

Questo passaggio configura una route basata sul percorso. È importante comprendere che le richieste non vengono scritte di nuovo: quando arriva una richiesta, il gateway applicazione la controlla e quindi, in base al modello di URL, la invia al pool di back-end appropriato.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come configurare l'offload SSL con un gateway applicazione di Azure, vedere [Configurare un gateway applicazione per l'offload SSL con il portale di Azure](application-gateway-ssl-portal.md).

[1]: ./media/application-gateway-create-url-route-portal/figure1.png
[2]: ./media/application-gateway-create-url-route-portal/figure2.png
[3]: ./media/application-gateway-create-url-route-portal/figure3.png
[scenario]: ./media/application-gateway-create-url-route-portal/scenario.png
