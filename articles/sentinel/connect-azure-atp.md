---
title: Connettere i dati di Microsoft Defender for Identity (in precedenza Azure ATP) ad Azure Sentinel | Microsoft Docs
description: Informazioni su come eseguire lo streaming dei log da Microsoft Defender per l'identità (in precedenza Azure Advanced Threat Protection) (ATP) in Azure Sentinel con un solo clic.
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
ms.openlocfilehash: 64c1a7155d0cc4e80f97db138a0626d6e9fdc9e9
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89657817"
---
# <a name="connect-data-from-microsoft-defender-for-identity-formerly-azure-advanced-threat-protection"></a>Connettere i dati da Microsoft Defender per l'identità (in precedenza Azure Advanced Threat Protection)

> [!IMPORTANT]
> Microsoft Defender for Identity Data Connector in Azure Sentinel è attualmente disponibile in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

È possibile trasmettere i log da [Microsoft Defender per l'identità](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) in Azure Sentinel con un solo clic.

## <a name="prerequisites"></a>Prerequisiti

- Utente con autorizzazioni di amministratore globale o amministratore della sicurezza
- È necessario essere un cliente di anteprima di Microsoft Defender per l'identità e abilitare l'integrazione tra Microsoft Defender per identità e Microsoft Cloud App Security. Per ulteriori informazioni, vedere [Microsoft Defender per l'integrazione di identità](https://docs.microsoft.com/cloud-app-security/aatp-integration).

## <a name="connect-to-microsoft-defender-for-identity"></a>Connettersi a Microsoft Defender per l'identità

Verificare che nella [rete sia abilitata](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1)la versione di anteprima di Microsoft Defender for Identity.
Se Microsoft Defender per l'identità è distribuito e inserisce i dati, gli avvisi sospetti possono essere facilmente trasmessi in Sentinel di Azure. Potrebbero essere necessarie fino a 24 ore prima che gli avvisi avviino lo streaming in Sentinel di Azure.


1. Per connettere Microsoft Defender per l'identità a Sentinel di Azure, è necessario innanzitutto abilitare l'integrazione tra Microsoft Defender per Identity e Microsoft Cloud App Security. Per informazioni su come eseguire questa operazione, vedere [Microsoft Defender per l'integrazione di identità](https://docs.microsoft.com/cloud-app-security/aatp-integration).

1. In Sentinel di Azure selezionare **connettori dati** e quindi fare clic sul riquadro **Microsoft Defender for Identity (anteprima)** .

1. È possibile scegliere se si desidera che gli avvisi di Microsoft Defender per l'identità creino automaticamente eventi imprevisti in Sentinel di Azure. In **Create incidents** (Crea eventi imprevisti) selezionare **Enable** (Abilita) per abilitare la regola di analisi predefinita che crea automaticamente gli eventi imprevisti dagli avvisi generati nel servizio di sicurezza connesso. È quindi possibile modificare questa regola in **Analisi** e quindi **Active rules** (Regole attive).

1. Fare clic su **Connetti**.

1. Per usare lo schema pertinente in Log Analytics per Microsoft Defender per gli avvisi di identità, cercare **SecurityAlert**.

> [!NOTE]
> Se gli avvisi superano i 30 KB, Azure Sentinel smette di visualizzare il campo entità negli avvisi.

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere Microsoft Defender per l'identità a Sentinel di Azure. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).

