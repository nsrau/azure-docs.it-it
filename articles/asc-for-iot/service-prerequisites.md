---
title: Centro sicurezza di Azure per i prerequisiti per l'it | Microsoft Docs
description: Informazioni dettagliate su tutti gli elementi necessari per iniziare a usare il Centro sicurezza di Azure per i prerequisiti del servizio Internet.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 790cbcb7-1340-4cc1-9509-7b262e7c3181
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/28/2019
ms.author: mlottner
ms.openlocfilehash: cc2dc3a190e3ad06bdc048f2a5770eae2a6990ec
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596821"
---
# <a name="azure-security-center-for-iot-prerequisites"></a>Centro sicurezza di Azure per i prerequisiti per l'it

Questo articolo fornisce una spiegazione dei diversi blocchi predefiniti del Centro sicurezza di Azure (ASC) per il servizio Internet delle cose, le informazioni necessarie per iniziare e illustra i concetti di base per comprendere il servizio. 

## <a name="minimum-requirements"></a>Requisiti minimi

- Livello standard dell'hub Internet
    - Privilegi a livello di **proprietario** del ruolo RBAC 
- [Area di lavoro Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) 
- Centro sicurezza di Azure (scelta consigliata)
    - L'uso del Centro sicurezza di Azure è un'indicazione e non un requisito. Senza il Centro sicurezza di Azure, non sarà possibile visualizzare le altre risorse di Azure all'interno dell'hub Internet. 
 
## <a name="working-with-asc-for-iot-service"></a>Uso di ASC per il servizio Internet

Il servizio ASC per informazioni dettagliate e la creazione di report sono disponibili tramite l'hub Azure e il Centro sicurezza di Azure. Per abilitare ASC per le cose nell'hub Azure, è necessario un account con privilegi a livello di **proprietario** . Dopo aver abilitato ASC per le cose nell'hub Internet delle cose, ASC per l'it Insights viene visualizzato come funzionalità di **sicurezza** nell'hub Azure Internet **e nel centro** sicurezza di Azure. 

## <a name="supported-service-regions"></a>Aree del servizio supportate 

ASC for Internet è attualmente supportato per gli hub Internet nelle aree di Azure seguenti:
  - Stati Uniti centrali  
  - East US 
  - Stati Uniti orientali 2
  - Stati Uniti centro-occidentali
  - Stati Uniti occidentali
  - Stati Uniti occidentali 2
  - Stati Uniti centro-meridionali
  - Stati Uniti centro-settentrionali
  - Canada centrale
  - Canada orientale 
  - Europa settentrionale    
  - Brasile meridionale
  - Francia centrale  
  - Regno Unito occidentale 
  - Regno Unito meridionale
  - Europa occidentale 
  - Europa settentrionale 
  - Giappone occidentale  
  - Giappone orientale  
  - Australia sud-orientale
  - Australia orientale
  - Asia orientale   
  - Asia sud-orientale
  - Corea del Sud centrale
  - Corea del Sud meridionale 
  - India centrale
  - India meridionale
  
## <a name="wheres-my-iot-hub"></a>Dov'è il mio hub Internet?

Controllare la posizione dell'hub Internet per verificare la disponibilità del servizio prima di iniziare. 

1. Aprire l'hub IoT. 
2. Fare clic su **Panoramica**. 
3. Verificare che il percorso elencato corrisponda a una delle [aree di servizio supportate](#supported-service-regions). 


## <a name="supported-platforms-for-agents"></a>Piattaforme supportate per gli agenti 

ASC for Internet Agents supporta un elenco in continua crescita di dispositivi e piattaforme. Vedere l' [elenco piattaforma supportata](how-to-deploy-agent.md) per verificare la libreria di dispositivi esistente o pianificata.  

## <a name="next-steps"></a>Passaggi successivi
- Leggi la [Panoramica](overview.md) sulla sicurezza di Azure.
- Informazioni su come [abilitare il servizio](quickstart-onboard-iot-hub.md)
- Leggi le [domande frequenti sul centro sicurezza di Azure](resources-frequently-asked-questions.md)
- Scopri come [comprendere il Centro sicurezza di Azure per gli avvisi](concept-security-alerts.md)
