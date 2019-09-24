---
title: Connessione dei dati Azure AD Identity Protection ad Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere Azure AD Identity Protection dati ad Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: de8f31a02b62164a8a6b099a90297a207aefae80
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240805"
---
# <a name="connect-data-from-azure-ad-identity-protection"></a>Connettere i dati da Azure AD Identity Protection



È possibile eseguire lo streaming dei log da [Azure ad Identity Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) in Sentinel di Azure per trasmettere avvisi in Azure Sentinel per visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. Azure Active Directory Identity Protection offre una visualizzazione consolidata a rischio di utenti, rilevamento dei rischi e vulnerabilità, con la possibilità di correggere immediatamente il rischio e di impostare criteri per la correzione automatica di eventi futuri. Il servizio è basato sull'esperienza di Microsoft nella protezione delle identità degli utenti e offre una notevole precisione dal segnale da oltre 13 miliardi accessi al giorno. 


## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre di una [licenza Azure Active Directory Premium P1 o P2](https://azure.microsoft.com/pricing/details/active-directory/)
- Utente con autorizzazioni di amministratore globale o amministratore della sicurezza


## <a name="connect-to-azure-ad-identity-protection"></a>Connetti a Azure AD Identity Protection

Se si dispone già di Azure AD Identity Protection, assicurarsi che sia [abilitato sulla rete](../active-directory/identity-protection/enable.md).
Se Azure AD Identity Protection viene distribuito e recupera i dati, i dati dell'avviso possono essere facilmente trasmessi in Sentinel di Azure.


1. In Sentinel di Azure selezionare **connettori dati** , quindi fare clic sul riquadro **Azure ad Identity Protection** .

2. Fare clic su **Connetti** per avviare lo streaming di eventi Azure ad Identity Protection in Sentinel di Azure.


6. Per utilizzare lo schema pertinente in Log Analytics per gli avvisi di Azure AD Identity Protection, cercare **IdentityProtectionLogs_CL**.

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere Azure AD Identity Protection ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Scopri come [ottenere visibilità sui dati e potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare le minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
