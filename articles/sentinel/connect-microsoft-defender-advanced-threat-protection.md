---
title: Connettere i dati di Microsoft Defender ATP ad Azure Sentinel Documenti Microsoft
description: Informazioni su come connettere i dati di Microsoft Defender Advanced Threat Protection ad Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: yelevin
ms.openlocfilehash: ee2c917d02a442ebabc0e1b40bc245982af794f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588213"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>Connettere avvisi da Microsoft Defender Advanced Threat Protection 


> [!IMPORTANT]
> L'inserimento dei log di Microsoft Defender Advanced Threat Protection è attualmente disponibile nell'anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 

È possibile trasmettere gli avvisi da [Microsoft Defender Advanced Threat Protection](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) in Azure Sentinel con un solo clic. Questa connessione consente di trasmettere gli avvisi da Microsoft Defender Advanced Threat Protection in Azure Sentinel.This connection enables you to stream the alerts from Microsoft Defender Advanced Threat Protection into Azure Sentinel. 

## <a name="prerequisites"></a>Prerequisiti

- Licenza valida per Microsoft Defender Advanced Threat Protection abilitata come descritto in Convalidare il [provisioning delle licenze e completare la configurazione di Microsoft Defender Advanced Threat Protection](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing). 
- È necessario essere un amministratore o un amministratore della sicurezza nel tenant di Azure Sentinel.You must be an administrator or an security administrator on the Azure Sentinel tenant.


## <a name="connect-to-microsoft-defender-advanced-threat-protection"></a>Connettersi a Microsoft Defender Advanced Threat Protection

Se Microsoft Defender Advanced Threat Protection viene distribuito e l'inserimento dei dati, gli avvisi possono essere facilmente trasmessi in Azure Sentinel.


1. In Azure Sentinel selezionare **Connettori dati**, fare clic sul riquadro **Microsoft Defender Advanced Threat Protection** e selezionare Pagina Apri **connettore**.
1. Fare clic su **Connetti**. 
1. Per utilizzare lo schema pertinente in Log Analytics per gli avvisi di Defender ATP, cercare **SecurityAlert** e il **nome del provider** è **MDATP**.




## <a name="next-steps"></a>Passaggi successivi
In this document, you learned how to connect Microsoft Defender ATP to Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats.md).
