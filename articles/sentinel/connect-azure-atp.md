---
title: Connettere i dati di Azure ATP ad Azure Sentinel Documenti Microsoft
description: Informazioni su come connettere i dati di Azure ATP ad Azure Sentinel.
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
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 387d04cbbb125006efcc4efc53a02015fe3f5919
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588587"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp"></a>Connettere i dati da Azure Advanced Threat Protection (ATP)

> [!IMPORTANT]
> Il connettore dati di Azure Advanced Threat Protection in Azure Sentinel è attualmente in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

È possibile trasmettere i log da [Azure Advanced Threat Protection](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) in Azure Sentinel con un solo clic.

## <a name="prerequisites"></a>Prerequisiti

- Utente con autorizzazioni di amministratore globale o amministratore della sicurezza
- È necessario essere un cliente di anteprima di Azure ATP e abilitare l'integrazione tra Azure ATP e Microsoft Cloud App Security.You must be a preview customer of Azure ATP and enable integration between Azure ATP and Microsoft Cloud App Security. Per altre informazioni, vedere [Integrazione di Azure Advanced Protection](https://docs.microsoft.com/cloud-app-security/aatp-integration).For more information, see Azure Advanced Protection Integration .

## <a name="connect-to-azure-atp"></a>Connettersi ad Azure ATPConnect to Azure ATP

Verificare che la versione di anteprima di Azure ATP sia [abilitata nella rete.](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1)
Se Azure ATP viene distribuito e inserisce i dati, gli avvisi sospetti possono essere facilmente trasmessi in Azure Sentinel.If Azure ATP is deployed and ingesting your data, the suspicious alerts can easily be streamed into Azure Sentinel. Potrebbero essere decollate fino a 24 ore prima che gli avvisi inizino lo streaming in Azure Sentinel.It may take up to 24 hours for the alerts to start streaming into Azure Sentinel.


1. Per connettere Azure ATP ad Azure Sentinel, è innanzitutto necessario abilitare l'integrazione tra Azure ATP e Microsoft Cloud App Security. Per informazioni su come eseguire questa operazione, vedere [Integrazione](https://docs.microsoft.com/cloud-app-security/aatp-integration)di Azure Advanced Threat Protection .

1. In Azure Sentinel selezionare **Connettori dati** e quindi fare clic sul riquadro **Azure Advanced Threat Protection (anteprima).**

1. È possibile selezionare se si vuole che gli avvisi da Azure ATP generino automaticamente eventi imprevisti in Azure Sentinel.You can select whether you want the alerts from Azure ATP to automatically generate s incidents in Azure Sentinel automatically. In **Create incidents** (Crea eventi imprevisti) selezionare **Enable** (Abilita) per abilitare la regola di analisi predefinita che crea automaticamente gli eventi imprevisti dagli avvisi generati nel servizio di sicurezza connesso. È quindi possibile modificare questa regola in **Analisi** e quindi **Active rules** (Regole attive).

1. Fare clic su **Connetti**.

1. Per usare lo schema pertinente in Log Analytics per gli avvisi di Azure ATP, cercare **SecurityAlert**.

> [!NOTE]
> Se gli avvisi superano i 30 KB, Azure Sentinel interrompe la visualizzazione del campo Entità negli avvisi.

## <a name="next-steps"></a>Passaggi successivi
In this document, you learned how to connect Azure Advanced Threat Protection to Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).

