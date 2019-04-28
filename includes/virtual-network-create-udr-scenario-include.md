---
title: File di inclusione
description: File di inclusione
services: virtual-network
author: rockboyfor
ms.service: virtual-network
ms.topic: include
origin.date: 04/13/2018
ms.date: 06/11/2018
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 40b81904daabfdad7e45571d8ab86cf32cac8964
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743391"
---
## <a name="scenario"></a>Scenario
Per illustrare meglio come creare una route definita dall'utente, in questo documento verrà usato lo scenario seguente.

![DESCRIZIONE DELL’IMMAGINE](./media/virtual-network-create-udr-scenario-include/figure1.png)

In questo scenario creare una route definita dall'utente per la *subnet front-end* e un'altra route definita dall'utente per la *subnet back-end*, come descritto di seguito: 

* **UDR-FrontEnd**. La route front-end definita dall'utente verrà applicata alla subnet *FrontEnd* e contiene una route:    
  * **RouteToBackend**. Questa route invia tutto il traffico nella subnet di back-end per la macchina virtuale **FW1**.
* **Back-end di UDR**. La route back-end definita dall'utente verrà applicata alla subnet *BackEnd* e contiene una route:    
  * **RouteToFrontend**. Questa route invia tutto il traffico nella subnet di font-end per la macchina virtuale **FW1**.

La combinazione di queste route garantisce che tutto il traffico destinato da una subnet a un'altra venga indirizzato alla macchina virtuale **FW1**, che viene usata come appliance virtuale. È inoltre necessario attivare l'inoltro IP per tale macchina virtuale **FW1**, per garantire che possa ricevere il traffico destinato ad altre macchine virtuali.
