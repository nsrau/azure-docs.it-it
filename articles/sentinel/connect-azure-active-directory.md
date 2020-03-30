---
title: Connettere i dati di Azure AD ad Azure Sentinel Documenti Microsoft
description: Informazioni su come connettere i dati di Azure Active Directory ad Azure Sentinel.
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
ms.openlocfilehash: be9241a6156621d3f90dbab2da5bebeb463b4232
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588621"
---
# <a name="connect-data-from-azure-active-directory"></a>Connettere dati da Azure Active DirectoryConnect data from Azure Active Directory



Azure Sentinel consente di raccogliere dati da Azure Active Directory e trasmetterlo in Azure Sentinel.Azure Sentinel enables you to collect data from [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) and stream it into Azure Sentinel. È possibile scegliere di trasmettere [i log di accesso](../active-directory/reports-monitoring/concept-sign-ins.md) e i log di [controllo.](../active-directory/reports-monitoring/concept-audit-logs.md)

## <a name="prerequisites"></a>Prerequisiti

- Se si desidera esportare i dati di accesso da Active Directory, è necessario disporre di una licenza di Azure AD P1 o P2.

- Utente con autorizzazioni di amministratore globale o di amministratore di sicurezza per il tenant da cui si desidera trasmettere i log.

- Per poter visualizzare lo stato della connessione, è necessario disporre dell'autorizzazione per accedere ai log di diagnostica di Azure AD. 


## <a name="connect-to-azure-ad"></a>Connessione ad Azure AD

1. In Azure Sentinel selezionare **Connettori dati** e quindi fare clic sul riquadro Azure Active Directory.In Azure Sentinel, select Data connectors and then click the **Azure Active Directory** tile.

1. Accanto ai log che si desidera trasmettere in Azure Sentinel fare clic su **Connetti**.

1. You can select whether you want the alerts from Azure AD to automatically generate incidents in Azure Sentinel. In **Create incidents** (Crea eventi imprevisti) selezionare **Enable** (Abilita) per abilitare la regola di analisi predefinita che crea automaticamente gli eventi imprevisti dagli avvisi generati nel servizio di sicurezza connesso. È quindi possibile modificare questa regola in **Analisi** e quindi **Active rules** (Regole attive).

1. Per usare lo schema pertinente in Log Analytics per gli avvisi di Azure AD, cercare **SigninLogs** e **AuditLogs**.




## <a name="next-steps"></a>Passaggi successivi
In this document, you learned how to connect Azure AD to Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
