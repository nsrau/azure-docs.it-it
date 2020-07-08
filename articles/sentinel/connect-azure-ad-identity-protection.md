---
title: Connettere i dati Azure AD Identity Protection ad Azure Sentinel
description: Informazioni su come eseguire lo streaming di log e avvisi da Azure AD Identity Protection in Sentinel di Azure per visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi.
author: yelevin
manager: rkarlin
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 06/24/2020
ms.author: yelevin
ms.openlocfilehash: 69ab76bf213653ea10db8dfd181b615a7e0f47b5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564478"
---
# <a name="connect-data-from-azure-active-directory-azure-ad-identity-protection"></a>Connettere i dati da Azure Active Directory (Azure AD) Identity Protection

È possibile eseguire lo streaming dei log da [Azure ad Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) in Sentinel di Azure per trasmettere avvisi in Azure Sentinel per visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. Azure Active Directory Identity Protection offre una visualizzazione consolidata a rischio di utenti, rilevamento dei rischi e vulnerabilità, con la possibilità di correggere immediatamente il rischio e di impostare criteri per la correzione automatica di eventi futuri. Il servizio è basato sull'esperienza di Microsoft nella protezione delle identità degli utenti e offre una notevole precisione dal segnale da oltre 13 miliardi accessi al giorno. 

## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre di una [sottoscrizione Azure ad Premium P2](https://azure.microsoft.com/pricing/details/active-directory/).
- È necessario disporre di un utente con autorizzazioni di amministratore globale o di amministratore della sicurezza.


## <a name="connect-to-azure-ad-identity-protection"></a>Connetti a Azure AD Identity Protection

Se si dispone di una sottoscrizione Azure AD Premium P2, viene incluso Azure AD Identity Protection. Se i [criteri sono abilitati](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) e generano avvisi, i dati dell'avviso possono essere facilmente trasmessi in Sentinel di Azure.

1. In Sentinel di Azure selezionare **connettori dati** , quindi fare clic sul riquadro **Azure ad Identity Protection** .

1. Fare clic su **Connetti** per avviare lo streaming di eventi Azure ad Identity Protection in Sentinel di Azure.

1. Per utilizzare lo schema pertinente in Log Analytics per gli avvisi di Azure AD Identity Protection, cercare **SecurityAlert**.

Se si vuole testare il connettore, è possibile [simulare i rilevamenti](../active-directory/identity-protection/howto-identity-protection-simulate-risk.md) per generare avvisi di esempio che verranno trasmessi in Sentinel di Azure.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere Azure AD Identity Protection ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
