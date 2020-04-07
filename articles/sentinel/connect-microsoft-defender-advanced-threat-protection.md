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
ms.openlocfilehash: 831baabcc26130d9b2c730afd85f2ed4b3842690
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756347"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>Connettere avvisi da Microsoft Defender Advanced Threat Protection 


> [!IMPORTANT]
> L'inserimento degli avvisi di Microsoft Defender Advanced Threat Protection è attualmente disponibile nell'anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 

Il connettore [Microsoft Defender Advanced Threat Protection](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) consente di trasmettere gli avvisi da Microsoft Defender Advanced Threat Protection in Azure Sentinel. In questo modo sarà possibile analizzare in modo più completo gli eventi di sicurezza all'interno dell'organizzazione e creare playbook per una risposta efficace e immediata.

## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre di una licenza valida per Microsoft Defender Advanced Threat Protection, come descritto in Configurare la distribuzione di [Microsoft Defender ATP](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing). 
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
