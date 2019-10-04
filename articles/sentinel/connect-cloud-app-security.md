---
title: Connettere i dati Cloud App Security ad Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere Cloud App Security dati ad Azure Sentinel.
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
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 6e71028855344d083dedf3493682e1e27685de48
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240113"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Connettere i dati da Microsoft Cloud App Security 



È possibile trasmettere i log da [cloud app Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) in Sentinel di Azure con un solo clic. Questa connessione consente di trasmettere gli avvisi da Cloud App Security in Sentinel di Azure. 

## <a name="prerequisites"></a>Prerequisiti

- Utente con autorizzazioni di amministratore globale o amministratore della sicurezza

## <a name="connect-to-cloud-app-security"></a>Connetti a Cloud App Security

Se si dispone già di Cloud App Security, assicurarsi che sia [abilitato sulla rete](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Se Cloud App Security viene distribuita e si inseriscono i dati, i dati dell'avviso possono essere facilmente trasmessi in Sentinel di Azure.


1. In Sentinel di Azure selezionare **connettori dati** , quindi fare clic sul riquadro **cloud app Security** .

1. Selezionare i log di cui si vuole eseguire lo streaming in Sentinel di Azure. è possibile scegliere gli **avvisi**. 

1. È possibile scegliere se si desidera che gli avvisi Microsoft Cloud App Security generino automaticamente eventi imprevisti in Sentinel di Azure. In **crea eventi imprevisti** selezionare **Abilita** per abilitare la regola analitica predefinita che crea automaticamente gli eventi imprevisti dagli avvisi generati nel servizio di sicurezza connesso. È quindi possibile modificare questa regola in **Analytics** e quindi **le regole attive**.

1. Fare clic su **Connetti**.

1. Per utilizzare lo schema pertinente in Log Analytics per gli avvisi di Cloud App Security, cercare **SecurityAlert**.




## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere Microsoft Cloud App Security ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Scopri come [ottenere visibilità sui dati e potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare le minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
