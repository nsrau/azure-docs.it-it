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
ms.date: 09/16/2020
ms.author: yelevin
ms.openlocfilehash: 72b2ba0ea444fb14ef9fc1bc3ea6aea3654677df
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655528"
---
# <a name="connect-alerts-from-microsoft-defender-for-endpoint-formerly-microsoft-defender-atp"></a>Connetti avvisi da Microsoft Defender for endpoint (in precedenza Microsoft Defender ATP)

> [!IMPORTANT]
>
> - **Microsoft Defender for endpoint** era noto in precedenza come **Microsoft Defender Advanced Threat Protection** o **MDATP**.
>
>     Per un certo periodo di tempo, è possibile che venga visualizzato il vecchio nome ancora in uso nel prodotto (incluso il relativo connettore dati in Sentinel di Azure).
>
> - L'inserimento di Microsoft Defender per gli avvisi degli endpoint è attualmente disponibile in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Microsoft Defender per endpoint](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) Connector consente di trasmettere avvisi da Microsoft Defender per l'endpoint in Azure Sentinel. Questo consentirà di analizzare in modo più completo gli eventi di sicurezza dell'organizzazione e di creare PlayBook per una risposta efficace e immediata.

> [!NOTE]
>
> Per inserire i nuovi log dei dati non elaborati da Microsoft Defender per la [caccia avanzata](/windows/security/threat-protection/microsoft-defender-atp/advanced-hunting-overview)dell'endpoint, usare il nuovo connettore per Microsoft 365 Defender (in precedenza Microsoft Threat Protection, [vedere la documentazione](./connect-microsoft-365-defender.md)).

## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre di una licenza valida per Microsoft Defender per endpoint, come descritto in [configurare Microsoft Defender per la distribuzione degli endpoint](/windows/security/threat-protection/microsoft-defender-atp/licensing). 

- È necessario essere un amministratore globale o un amministratore della sicurezza nel tenant di Azure Sentinel.

## <a name="connect-to-microsoft-defender-for-endpoint"></a>Connettersi a Microsoft Defender per l'endpoint

Se Microsoft Defender for endpoint viene distribuito e l'inserimento dei dati, gli avvisi possono essere facilmente trasmessi in Sentinel di Azure.

1. In Sentinel di Azure selezionare **connettori dati**, selezionare **Microsoft Defender per endpoint** (potrebbe essere ancora chiamato *Microsoft Defender Advanced Threat Protection*) dalla raccolta e selezionare **Apri connettore pagina**.

1. Fare clic su **Connetti**. 

1. Per eseguire una query su Microsoft Defender per gli avvisi dell'endpoint nei **log**, immettere **SecurityAlert** nella finestra query e aggiungere un filtro in cui il **nome del provider** è **MDATP**.

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere Microsoft Defender per l'endpoint ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](./tutorial-detect-threats-built-in.md).