---
title: Connettere i dati di Azure AD Identity Protection ad Azure SentinelConnect Azure AD Identity Protection data to Azure Sentinel
description: Informazioni su come connettere i dati di Azure AD Identity Protection ad Azure Sentinel.Learn how to connect Azure AD Identity Protection data to Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 11/17/2019
ms.author: yelevin
ms.openlocfilehash: b82ddfef57efaaca0ae43750cd306a63a772b911
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80616817"
---
# <a name="connect-data-from-azure-ad-identity-protection"></a>Connettere i dati da Azure AD Identity ProtectionConnect data from Azure AD Identity Protection



È possibile trasmettere i log da [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection) in Azure Sentinel per trasmettere avvisi in Azure Sentinel per visualizzare dashboard, creare avvisi personalizzati e migliorare l'analisi. Azure Active Directory Identity Protection offre una visualizzazione consolidata a rischio per gli utenti, i rilevamenti dei rischi e le vulnerabilità, con la possibilità di correggere immediatamente i rischi e impostare criteri per il correzione automatica degli eventi futuri. Il servizio si basa sull'esperienza di Microsoft nella protezione delle identità dei consumatori e ottiene un'enorme precisione dal segnale da oltre 13 miliardi di log-in al giorno. 


## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre di una [licenza Azure Active Directory Premium P1 o P2](https://azure.microsoft.com/pricing/details/active-directory/)
- Utente con autorizzazioni di amministratore globale o amministratore della sicurezza


## <a name="connect-to-azure-ad-identity-protection"></a>Connettersi a Azure AD Identity ProtectionConnect to Azure AD Identity Protection

Se si dispone già di Azure AD Identity Protection, assicurarsi che sia [abilitato nella rete.](../active-directory/identity-protection/overview-identity-protection.md)
Se Azure AD Identity Protection viene distribuito e si ottengono dati, i dati degli avvisi possono essere facilmente trasmessi in Azure Sentinel.If Azure AD Identity Protection is deployed and getting data, the alert data can easily be streamed into Azure Sentinel.


1. In Azure Sentinel selezionare **Connettori dati** e quindi fare clic sul riquadro Protezione identità di **Azure AD.**

2. Fare clic su Connetti per avviare lo streaming di eventi di Azure AD Identity Protection in Azure Sentinel.Click **Connect** to start streaming Azure AD Identity Protection events into Azure Sentinel.


6. Per usare lo schema pertinente in Log Analytics per gli avvisi di Azure AD Identity Protection, cercare **SecurityAlert**.

## <a name="next-steps"></a>Passaggi successivi
In this document, you learned how to connect Azure AD Identity Protection to Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
