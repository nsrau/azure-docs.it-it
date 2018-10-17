---
title: I modelli di connessione di sistemi integrati di Azure Stack | Microsoft Docs
description: Determinare le decisioni per lo Stack di Azure multi-nodo di pianificazione della distribuzione.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 7509d00815f56dc46bd276ffc67c4c607c54070a
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2018
ms.locfileid: "49338897"
---
# <a name="azure-stack-integrated-systems-connection-models"></a>I modelli di connessione di sistemi integrati di Azure Stack
Se si è interessati a un sistema integrato Azure Stack, è necessario comprendere [diverse considerazioni relative all'integrazione di Data Center](azure-stack-datacenter-integration.md) per la distribuzione di Azure Stack determinare come il sistema possa essere inserito nel tuo Data Center. Inoltre, è necessario decidere esattamente come si integrerà Azure Stack nell'ambiente cloud ibrido. Questo articolo offre una panoramica di queste decisioni principali, inclusi connessione di Azure, archivio di identità, fatturazione e le decisioni del modello.

Se si decide di acquistare un sistema integrato, il fornitore dell'hardware (OEM) original equipment manufacturer Guida l'utente attraverso la maggior parte del processo di pianificazione in modo più dettagliato. Si eseguirà anche la distribuzione effettiva.

## <a name="choose-an-azure-stack-deployment-connection-model"></a>Scegliere un modello di connessione di distribuzione Azure Stack
È possibile scegliere di distribuire Azure Stack sia connesso a internet (e in Azure) o disconnesso. Per ottenere il massimo vantaggio da Azure Stack, inclusi scenari ibridi tra Azure Stack e Azure, si potrebbe voler distribuire connesso ad Azure. Questa scelta definisce quali opzioni sono disponibili per l'archivio di identità (Azure Active Directory o Active Directory Federation Services) e il modello di fatturazione (pagare quando si usa basata su fatturazione o basati su capacità di fatturazione) come riepilogato nel diagramma e nella tabella seguente: 

![Distribuzione di Stack e gli scenari di fatturazione Azure](media/azure-stack-connection-models/azure-stack-scenarios.png)  
  
> [!IMPORTANT]
> Questo è un punto di decisione fondamentale. La scelta di Active Directory Federation Services (ADFS) o Azure Active Directory (Azure AD) è una decisione una-tantum che è necessario prendere in fase di distribuzione. È possibile modificare questo in un secondo momento senza ridistribuzione dell'intero sistema.  


|Opzioni|Connesso ad Azure|Disconnessione da Azure|
|-----|-----|-----|
|Azure AD|![Supportato](media/azure-stack-connection-models/check.png)| |
|AD FS|![Supportato](media/azure-stack-connection-models/check.png)|![Supportato](media/azure-stack-connection-models/check.png)|
|Fatturazione in base al consumo|![Supportato](media/azure-stack-connection-models/check.png)| |
|Fatturazione basata sulla capacità|![Supportato](media/azure-stack-connection-models/check.png)|![Supportato](media/azure-stack-connection-models/check.png)|
|Scaricare i pacchetti di aggiornamento direttamente in Azure Stack|![Supportato](media/azure-stack-connection-models/check.png)|  |

Dopo aver scelto il modello di connessione di Azure da utilizzare per la distribuzione di Azure Stack, è necessario prendere decisioni aggiuntive, dipendenti dalla connessione per l'archivio identità e un metodo di fatturazione. 

## <a name="next-steps"></a>Passaggi successivi

[Azure connesso decisioni di distribuzione di Azure Stack](azure-stack-connected-deployment.md)

[Azure disconnesso decisioni relative alla distribuzione di Azure Stack](azure-stack-disconnected-deployment.md)
