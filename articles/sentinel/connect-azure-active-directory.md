---
title: Connetti i dati di Azure AD all'anteprima di Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere i dati di Azure Active Directory per Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 357435b8a4ac396c1548c89206f269730e871f6b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65204487"
---
# <a name="connect-data-from-azure-active-directory"></a>Connetti i dati da Azure Active Directory

> [!IMPORTANT]
> Azure Sentinel è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Sentinel Azure consente di raccogliere i dati dai [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) e trasmetterlo in Sentinel di Azure. È possibile scegliere di flusso [i log di accesso](../active-directory/reports-monitoring/concept-sign-ins.md) e [log di controllo](../active-directory/reports-monitoring/concept-audit-logs.md) .

## <a name="prerequisites"></a>Prerequisiti

- Se si desidera esportare i dati di accesso da Active Directory, è necessario avere una licenza Azure AD P1 o P2.

- Utente con global admin o della sicurezza le autorizzazioni di amministratore nel tenant di cui che si vuole trasmettere i log dal.


## <a name="connect-to-azure-ad"></a>Connessione ad Azure AD

1. In Azure Sentinel, selezionare **connettori di dati** e quindi fare clic sui **Azure Active Directory** riquadro.

2. Accanto i log si vuole trasmettere in Sentinel di Azure, fare clic su **Connect**.

6. Per usare lo schema appropriato nel Log Analitica per gli avvisi di Azure AD, cercare **SigninLogs** e **AuditLogs**.




## <a name="next-steps"></a>Passaggi successivi
In questo documento è stato descritto come connettere Azure AD per Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere la visibilità di dati e le potenziali minacce](quickstart-get-visibility.md).
- Iniziare a usare [rilevando minacce con Azure Sentinel](tutorial-detect-threats.md).
