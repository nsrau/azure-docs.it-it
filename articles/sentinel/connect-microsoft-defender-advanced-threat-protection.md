---
title: Connettere i dati di Microsoft Defender ATP ad Azure Sentinel | Microsoft Docs
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
ms.openlocfilehash: 831baabcc26130d9b2c730afd85f2ed4b3842690
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80756347"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>Connetti avvisi da Microsoft Defender Advanced Threat Protection 


> [!IMPORTANT]
> L'inserimento di avvisi di Microsoft Defender Advanced Threat Protection è attualmente disponibile in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 

Il connettore [Microsoft Defender Advanced Threat Protection](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) consente di trasmettere avvisi da Microsoft Defender Advanced Threat Protection in Azure Sentinel. Questo consentirà di analizzare in modo più completo gli eventi di sicurezza dell'organizzazione e di creare PlayBook per una risposta efficace e immediata.

## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre di una licenza valida per Microsoft Defender Advanced Threat Protection, come descritto in [configurare la distribuzione di Microsoft Defender ATP](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing). 
- È necessario essere un amministratore o un amministratore della sicurezza nel tenant di Azure Sentinel.


## <a name="connect-to-microsoft-defender-advanced-threat-protection"></a>Connettersi a Microsoft Defender Advanced Threat Protection

Se Microsoft Defender Advanced Threat Protection viene distribuito e l'inserimento dei dati, gli avvisi possono essere facilmente trasmessi in Sentinel di Azure.


1. In Sentinel di Azure selezionare **connettori dati**, fare clic sul riquadro **Microsoft Defender Advanced Threat Protection** e selezionare **Apri pagina connettore**.
1. Fare clic su **Connetti**. 
1. Per utilizzare lo schema pertinente in Log Analytics per gli avvisi di Defender ATP, cercare **SecurityAlert** e il **nome del provider** è **MDATP**.




## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere Microsoft Defender ATP ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats.md).
