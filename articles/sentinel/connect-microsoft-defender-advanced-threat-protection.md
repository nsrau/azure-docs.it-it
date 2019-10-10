---
title: Connettere i dati di Microsoft Defender ATP ad Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere i dati di Microsoft Defender Advanced Threat Protection ad Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: rkarlin
ms.openlocfilehash: 19d496ebb61a3ceb47f69f661e30ab529dc64f3d
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256747"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>Connetti avvisi da Microsoft Defender Advanced Threat Protection 


> [!IMPORTANT]
> L'inserimento dei log di Microsoft Defender Advanced Threat Protection è attualmente disponibile in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 

È possibile trasmettere avvisi da [Microsoft Defender Advanced Threat Protection](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) in Azure Sentinel con un solo clic. Questa connessione consente di trasmettere gli avvisi da Microsoft Defender Advanced Threat Protection in Sentinel di Azure. 

## <a name="prerequisites"></a>Prerequisiti

- Licenza valida per Microsoft Defender Advanced Threat Protection abilitata come descritto in [convalidare il provisioning delle licenze e completare la configurazione per Microsoft Defender Advanced Threat Protection](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing). 
- È necessario essere un amministratore o un amministratore della sicurezza nel tenant di Azure Sentinel.


## <a name="connect-to-microsoft-defender-advanced-threat-protection"></a>Connettersi a Microsoft Defender Advanced Threat Protection

Se Microsoft Defender Advanced Threat Protection viene distribuito e l'inserimento dei dati, gli avvisi possono essere facilmente trasmessi in Sentinel di Azure.


1. In Sentinel di Azure selezionare **connettori dati**, fare clic sul riquadro **Microsoft Defender Advanced Threat Protection** e selezionare **Apri pagina connettore**.
1. Fare clic su **Connetti**. 
1. Per utilizzare lo schema pertinente in Log Analytics per gli avvisi di Defender ATP, cercare **SecurityAlert** e il **nome del provider** è **MDATP**.




## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere Microsoft Defender ATP ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Scopri come [ottenere visibilità sui dati e potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare le minacce con Azure Sentinel](tutorial-detect-threats.md).
