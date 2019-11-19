---
title: Connettere i dati di Azure ATP ad Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere i dati di Azure ATP ad Azure Sentinel.
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
ms.date: 11/17/2019
ms.author: rkarlin
ms.openlocfilehash: fb8f4de3b3b24d1eba372600c817627771ef0ef6
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158867"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp"></a>Connettere i dati da Azure Advanced Threat Protection (ATP)

> [!IMPORTANT]
> Il connettore dati di Azure Advanced Threat Protection in Sentinel di Azure è attualmente disponibile in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

È possibile trasmettere i log da [Azure Advanced Threat Protection](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) in Azure Sentinel con un solo clic.

## <a name="prerequisites"></a>prerequisiti

- Utente con autorizzazioni di amministratore globale o amministratore della sicurezza
- È necessario essere un cliente di anteprima di Azure ATP e abilitare l'integrazione tra Azure ATP e Microsoft Cloud App Security. Per altre informazioni, vedere [integrazione della protezione avanzata di Azure](https://docs.microsoft.com/cloud-app-security/aatp-integration).

## <a name="connect-to-azure-atp"></a>Connettersi ad Azure ATP

Verificare che la versione di anteprima di Azure ATP sia [abilitata nella rete](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1).
Se Azure ATP viene distribuito e l'inserimento dei dati, gli avvisi sospetti possono essere facilmente trasmessi in Sentinel di Azure. Potrebbero essere necessarie fino a 24 ore prima che gli avvisi avviino lo streaming in Sentinel di Azure.


1. Per connettere Azure ATP ad Azure Sentinel, è necessario innanzitutto abilitare l'integrazione tra Azure ATP e Microsoft Cloud App Security. Per informazioni su come eseguire questa operazione, vedere [integrazione di Azure Advanced Threat Protection](https://docs.microsoft.com/cloud-app-security/aatp-integration).

1. In Sentinel di Azure selezionare **connettori dati** e quindi fare clic sul riquadro **Azure Advanced Threat Protection (anteprima)** .

1. È possibile scegliere se si desidera che gli avvisi di Azure ATP generino automaticamente eventi imprevisti in Sentinel di Azure. In **Create incidents** (Crea eventi imprevisti) selezionare **Enable** (Abilita) per abilitare la regola di analisi predefinita che crea automaticamente gli eventi imprevisti dagli avvisi generati nel servizio di sicurezza connesso. È quindi possibile modificare questa regola in **Analisi** e quindi **Active rules** (Regole attive).

1. Fare clic su **Connect**.

1. Per usare lo schema pertinente in Log Analytics per gli avvisi di Azure ATP, cercare **SecurityAlert**.

> [!NOTE]
> Se gli avvisi superano i 30 KB, Azure Sentinel smette di visualizzare il campo entità negli avvisi.

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere Azure Advanced Threat Protection ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Scopri come [ottenere visibilità sui dati e potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare le minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).

