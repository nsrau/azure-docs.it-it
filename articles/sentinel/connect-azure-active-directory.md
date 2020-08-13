---
title: Connettere i dati Azure Active Directory ad Azure Sentinel | Microsoft Docs
description: Informazioni su come raccogliere dati da Azure Active Directory e trasmettere Azure AD log di accesso e log di controllo in Sentinel di Azure.
services: sentinel
documentationcenter: na
author: yelevin
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
ms.author: yelevin
ms.openlocfilehash: a19ced1cb9496bddd1f9aa7ea9e3eb58ba7eea3d
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88185690"
---
# <a name="connect-data-from-azure-active-directory-azure-ad"></a>Connettere i dati da Azure Active Directory (Azure AD)



Azure Sentinel consente di raccogliere dati da [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) e di trasmettere i dati in Azure Sentinel. È possibile scegliere di eseguire lo streaming dei log di [accesso](../active-directory/reports-monitoring/concept-sign-ins.md) e dei [log di controllo](../active-directory/reports-monitoring/concept-audit-logs.md) .

## <a name="prerequisites"></a>Prerequisiti

- Qualsiasi licenza di Azure AD (free/O365/P1/P2) può integrarsi con monitoraggio di Azure e inserire i log di accesso in Sentinel di Azure. Verranno applicati addebiti aggiuntivi per Gigabyte per monitoraggio di Azure o Azure Sentinel.

- Utente con autorizzazioni di amministratore globale o di amministratore della sicurezza per il tenant da cui si vogliono trasmettere i log.

- Per poter visualizzare lo stato della connessione, è necessario disporre dell'autorizzazione per accedere ai log di diagnostica Azure AD. 


## <a name="connect-to-azure-active-directory"></a>Connessione ad Azure Active Directory

1. In Sentinel di Azure selezionare **connettori dati** dal menu di navigazione.

1. Dalla raccolta di connettori dati selezionare **Azure Active Directory** e quindi fare clic sul pulsante **Apri pagina connettore** .

1. Selezionare le caselle di controllo accanto ai log che si vuole trasmettere in Azure Sentinel e fare clic su **Connetti**.

1. È possibile scegliere se si desidera che gli avvisi Azure AD generino automaticamente eventi imprevisti in Sentinel di Azure. In **Create incidents** (Crea eventi imprevisti) selezionare **Enable** (Abilita) per abilitare la regola di analisi predefinita che crea automaticamente gli eventi imprevisti dagli avvisi generati nel servizio di sicurezza connesso. È quindi possibile modificare questa regola in **Analisi** e quindi **Active rules** (Regole attive).

1. Per utilizzare lo schema pertinente in Log Analytics per l'esecuzione di query sugli avvisi Azure AD, digitare `SigninLogs` o `AuditLogs` nella finestra query.




## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere Azure Active Directory ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
