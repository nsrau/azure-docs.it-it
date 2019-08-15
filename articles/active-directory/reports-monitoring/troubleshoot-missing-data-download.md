---
title: 'Risoluzione dei problemi: dati mancanti nei log attività scaricati di Azure Active Directory | Microsoft Docs'
description: Offre una soluzione per i dati mancanti nei log attività scaricati di Azure Active Directory.
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: f120c1b86efe94f4ff6316e6116b9049582b07e9
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68987991"
---
# <a name="i-cant-find-all-the-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>Non è possibile trovare tutti i dati nei log attività di Azure Active Directory scaricati

## <a name="symptoms"></a>Sintomi

I log attività (controllo o accessi) sono stati scaricati ma non vengono visualizzati tutti i record per l'orario scelto. Perché? 

 ![Report](./media/troubleshoot-missing-data-download/01.png)
 
## <a name="cause"></a>Causa

Quando si scaricano i log attività nel portale di Azure, viene limitata la scalabilità a 250.000 record, ordinati in base alla prima più recente. 

## <a name="resolution"></a>Risoluzione

È possibile sfruttare le [API di Creazione rapporti di Azure AD](concept-reporting-api.md) per recuperare fino a un milione di record per un momento specifico.

## <a name="next-steps"></a>Passaggi successivi

* [Domande frequenti sui report di Azure Active Directory](reports-faq.md)

