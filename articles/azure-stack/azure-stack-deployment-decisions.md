---
title: Decisioni di distribuzione per Azure Stack integrati sistemi | Documenti Microsoft
description: Determinare le decisioni per lo Stack di Azure multi-node la pianificazione della distribuzione.
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: d4aaf5af4fdb9ff5d185ef14333db4e204b9a955
ms.sourcegitcommit: 5108f637c457a276fffcf2b8b332a67774b05981
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2018
---
# <a name="deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Decisioni per lo Stack di Azure la pianificazione della distribuzione integrate di sistemi
Se si è interessati in un sistema integrato dello Stack di Azure, è necessario comprendere [diverse considerazioni di pianificazione](azure-stack-deployment-planning.md) per la distribuzione di Azure Stack e quindi determinare il modo in cui verrà integrare il sistema in un Data Center. Inoltre, è necessario stabilire esattamente come si integrerà Stack Azure nell'ambiente cloud ibrido. In questo articolo viene fornita una panoramica di queste decisioni principali inclusi connessione di Azure, archivio di identità, fatturazione e le decisioni del modello.

Se si decide di acquistare un sistema integrato, il fornitore dell'hardware (OEM) original equipment manufacturer consente una Guida per la maggior parte del processo di pianificazione in modo più dettagliato. Eseguono anche la distribuzione effettiva.

## <a name="choose-an-azure-stack-deployment-connection-model"></a>Scegliere un modello di connessione di distribuzione Azure Stack
È possibile scegliere di distribuire Azure Stack disconnessi o connessi a internet (e in Azure). Per ottenere il massimo vantaggio dallo Stack di Azure, inclusi gli scenari di ibrido tra Stack di Azure e Azure, si desidera distribuire connesso ad Azure. Questa scelta definisce quali opzioni sono disponibili per l'archivio di identità (Azure Active Directory o Active Directory Federation Services) e il modello di fatturazione (pagare come è basata sull'utilizzo o di fatturazione basato su capacità di fatturazione) come riepilogato nel diagramma e nella tabella seguente: 

![Distribuzione di Stack Azure e gli scenari di fatturazione](media/azure-stack-deployment-decisions/azure-stack-scenarios.png)   
  
> [!IMPORTANT]
> Si tratta di un punto di decisione chiave! Scelta di Active Directory Federation Services (ADFS) o Azure Active Directory (Azure AD) è una decisione occasionale che è necessario eseguire in fase di distribuzione. È possibile sostituire questa in un secondo momento, senza ridistribuire l'intero sistema.  


|Opzioni|Connesso a Azure|Disconnessione da Azure|
|-----|-----|-----|
|Azure AD|![Supportato](media/azure-stack-deployment-decisions/check.png)| |
|AD FS|![Supportato](media/azure-stack-deployment-decisions/check.png)|![Supportato](media/azure-stack-deployment-decisions/check.png)|
|Fatturazione in base al consumo|![Supportato](media/azure-stack-deployment-decisions/check.png)| |
|Fatturazione basata sulla capacità|![Supportato](media/azure-stack-deployment-decisions/check.png)|![Supportato](media/azure-stack-deployment-decisions/check.png)|
|Scaricare i pacchetti di aggiornamento direttamente allo Stack di Azure|![Supportato](media/azure-stack-deployment-decisions/check.png)|  |

Dopo aver stabilito il modello di connessione di Azure da utilizzare per la distribuzione di Azure Stack, decisioni aggiuntive e connessione dipendenti devono essere eseguite per l'archivio di identità e il metodo di fatturazione. 

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni, vedere [Azure connesso decisioni di distribuzione Azure Stack](azure-stack-connected-deployment.md)
- Altre informazioni, vedere [Azure disconnesso decisioni di distribuzione Azure Stack](azure-stack-disconnected-deployment.md)
