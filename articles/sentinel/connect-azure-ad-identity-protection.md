---
title: Connettere i dati Azure AD Identity Protection ad Azure Sentinel
description: Informazioni su come connettere Azure AD Identity Protection dati ad Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 11/17/2019
ms.author: yelevin
ms.openlocfilehash: 7d42ff28ddd2d883feb25139096d781efe64d50f
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588570"
---
# <a name="connect-data-from-azure-ad-identity-protection"></a>Connettere i dati da Azure AD Identity Protection



È possibile eseguire lo streaming dei log da [Azure ad Identity Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) in Sentinel di Azure per trasmettere avvisi in Azure Sentinel per visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. Azure Active Directory Identity Protection offre una visualizzazione consolidata a rischio di utenti, rilevamento dei rischi e vulnerabilità, con la possibilità di correggere immediatamente il rischio e di impostare criteri per la correzione automatica di eventi futuri. Il servizio è basato sull'esperienza di Microsoft nella protezione delle identità degli utenti e offre una notevole precisione dal segnale da oltre 13 miliardi accessi al giorno. 


## <a name="prerequisites"></a>Prerequisites

- È necessario disporre di una [licenza Azure Active Directory Premium P1 o P2](https://azure.microsoft.com/pricing/details/active-directory/)
- Utente con autorizzazioni di amministratore globale o amministratore della sicurezza


## <a name="connect-to-azure-ad-identity-protection"></a>Connetti a Azure AD Identity Protection

Se si dispone già di Azure AD Identity Protection, assicurarsi che sia [abilitato sulla rete](../active-directory/identity-protection/overview-identity-protection.md).
Se Azure AD Identity Protection viene distribuito e recupera i dati, i dati dell'avviso possono essere facilmente trasmessi in Sentinel di Azure.


1. In Sentinel di Azure selezionare **connettori dati** , quindi fare clic sul riquadro **Azure ad Identity Protection** .

2. Fare clic su **Connetti** per avviare lo streaming di eventi Azure ad Identity Protection in Sentinel di Azure.


6. Per utilizzare lo schema pertinente in Log Analytics per gli avvisi di Azure AD Identity Protection, cercare **SecurityAlert**.

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere Azure AD Identity Protection ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
