---
title: Connettere i dati Azure AD ad Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere Azure Active Directory dati ad Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 43405bd3e97dbee325f0a5ed82c5848880775eee
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240809"
---
# <a name="connect-data-from-azure-active-directory"></a>Connettere i dati da Azure Active Directory



Azure Sentinel consente di raccogliere dati da [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) e di trasmettere i dati in Azure Sentinel. È possibile scegliere di eseguire lo streaming dei log di [accesso](../active-directory/reports-monitoring/concept-sign-ins.md) e dei [log di controllo](../active-directory/reports-monitoring/concept-audit-logs.md) .

## <a name="prerequisites"></a>Prerequisiti

- Se si desidera esportare i dati di accesso da Active Directory, è necessario disporre di una licenza Azure AD P1 o P2.

- Utente con autorizzazioni di amministratore globale o di amministratore della sicurezza per il tenant da cui si vogliono trasmettere i log.

- Per poter visualizzare lo stato della connessione, è necessario disporre dell'autorizzazione per accedere ai log di diagnostica Azure AD. 


## <a name="connect-to-azure-ad"></a>Connessione ad Azure AD

1. In Sentinel di Azure selezionare **connettori dati** , quindi fare clic sul riquadro **Azure Active Directory** .

1. Accanto ai log che si vuole trasmettere in Azure Sentinel, fare clic su **Connetti**.

1. È possibile scegliere se si desidera che gli avvisi Azure AD generino automaticamente eventi imprevisti in Sentinel di Azure. In **crea eventi imprevisti** selezionare **Abilita** per abilitare la regola analitica predefinita che crea automaticamente gli eventi imprevisti dagli avvisi generati nel servizio di sicurezza connesso. È quindi possibile modificare questa regola in **Analytics** e quindi **le regole attive**.

1. Per utilizzare lo schema pertinente in Log Analytics per gli avvisi di Azure AD, cercare **SigninLogs** e **AuditLogs**.




## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere Azure AD ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Scopri come [ottenere visibilità sui dati e potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare le minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
