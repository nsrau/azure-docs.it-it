---
title: Connettere i dati Cloud App Security all'anteprima di Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere Cloud App Security dati ad Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: cd9e5e27-fdd4-4717-8924-be4c1c430f23
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2019
ms.author: rkarlin
ms.openlocfilehash: 92de4120d73af70c5de013488344a15ba46513c7
ms.sourcegitcommit: 1b7b0e1c915f586a906c33d7315a5dc7050a2f34
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67881076"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Connettere i dati da Microsoft Cloud App Security 

> [!IMPORTANT]
> Azure Sentinel è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

È possibile trasmettere i log da [cloud app Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) in Sentinel di Azure con un solo clic. Questa connessione consente di trasmettere gli avvisi da Cloud App Security in Sentinel di Azure. 

## <a name="prerequisites"></a>Prerequisiti

- Utente con autorizzazioni di amministratore globale o amministratore della sicurezza

## <a name="connect-to-cloud-app-security"></a>Connetti a Cloud App Security

Se si dispone già di Cloud App Security, assicurarsi che sia [abilitato sulla rete](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Se Cloud App Security viene distribuita e si inseriscono i dati, i dati dell'avviso possono essere facilmente trasmessi in Sentinel di Azure.


1. In Sentinel di Azure selezionare **connettori dati** , quindi fare clic sul riquadro **cloud app Security** .

2. Fare clic su **Connetti**.

3. Per utilizzare lo schema pertinente in Log Analytics per gli avvisi di Cloud App Security, cercare **SecurityAlert**.


## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere Microsoft Cloud App Security ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Scopri come [ottenere visibilità sui dati e potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare le minacce con Azure Sentinel](tutorial-detect-threats.md).
