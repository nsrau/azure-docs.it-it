---
title: Connettere Microsoft Defender per i dati dell'endpoint ad Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere i dati di Microsoft Defender for endpoint (in precedenza Microsoft Defender ATP) ad Azure Sentinel.
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
ms.openlocfilehash: 69ce6cb42fd18364a7b93faa9cc01d1f793d5cd6
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89657533"
---
# <a name="connect-alerts-from-microsoft-defender-for-endpoint-formerly-microsoft-defender-atp"></a>Connetti avvisi da Microsoft Defender for endpoint (in precedenza Microsoft Defender ATP) 


> [!IMPORTANT]
> L'inserimento di Microsoft Defender per gli avvisi degli endpoint è attualmente disponibile in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 

[Microsoft Defender per endpoint](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) Connector consente di trasmettere avvisi da Microsoft Defender per l'endpoint in Azure Sentinel. Questo consentirà di analizzare in modo più completo gli eventi di sicurezza dell'organizzazione e di creare PlayBook per una risposta efficace e immediata.

## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre di una licenza valida per Microsoft Defender per endpoint, come descritto in [configurare Microsoft Defender per la distribuzione degli endpoint](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing). 
- È necessario essere un amministratore o un amministratore della sicurezza nel tenant di Azure Sentinel.


## <a name="connect-to-microsoft-defender-for-endpoint"></a>Connettersi a Microsoft Defender per l'endpoint

Se Microsoft Defender for endpoint viene distribuito e l'inserimento dei dati, gli avvisi possono essere facilmente trasmessi in Sentinel di Azure.


1. In Sentinel di Azure selezionare **connettori dati**, selezionare **Microsoft Defender per endpoint** (potrebbe essere ancora chiamato Microsoft Defender Advanced Threat Protection) dalla raccolta e selezionare **Apri connettore pagina**.
1. Fare clic su **Connetti**. 
1. Per utilizzare lo schema pertinente in Log Analytics per gli avvisi di Defender ATP, cercare **SecurityAlert** e il **nome del provider** è **MDATP**.

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere Microsoft Defender per l'endpoint ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats.md).
