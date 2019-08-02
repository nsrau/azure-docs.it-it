---
title: Connettere i dati di Azure ATP ad Azure Sentinel Preview | Microsoft Docs
description: Informazioni su come connettere i dati di Azure ATP ad Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 5bf3cc44-ecda-4c78-8a63-31ab42f43605
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/28/2019
ms.author: rkarlin
ms.openlocfilehash: 6f41a5704c783ba8aeab7bc2e82ef731c6a257ac
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599149"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp"></a>Connettere i dati da Azure Advanced Threat Protection (ATP)

> [!IMPORTANT]
> Azure Sentinel è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


È possibile trasmettere i log da [Azure Advanced Threat Protection](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) in Azure Sentinel con un solo clic.

## <a name="prerequisites"></a>Prerequisiti

- Utente con autorizzazioni di amministratore globale o amministratore della sicurezza
- È necessario essere un cliente di anteprima di Azure ATP

## <a name="connect-to-azure-atp"></a>Connettersi ad Azure ATP

Verificare che la versione di anteprima di Azure ATP sia [abilitata nella rete](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1).
Se Azure ATP viene distribuito e l'inserimento dei dati, gli avvisi sospetti possono essere facilmente trasmessi in Sentinel di Azure. Potrebbero essere necessarie fino a 24 ore prima che gli avvisi avviino lo streaming in Sentinel di Azure.


1. Per connettere Azure ATP ad Azure Sentinel, è necessario innanzitutto abilitare l'integrazione tra Azure ATP e Microsoft Cloud App Security. Per informazioni su come eseguire questa operazione, vedere [integrazione di Azure Advanced Threat Protection](https://docs.microsoft.com/cloud-app-security/aatp-integration).

1. In Sentinel di Azure selezionare **connettori dati** e quindi fare clic sul riquadro **Azure ATP** .

2. Fare clic su **Connetti**.

6. Per usare lo schema pertinente in Log Analytics per gli avvisi di Azure ATP, cercare **SecurityAlert**.

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere Azure Advanced Threat Protection ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Scopri come [ottenere visibilità sui dati e potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare le minacce con Azure Sentinel](tutorial-detect-threats.md).

