---
title: "Risoluzione dei problemi: dati mancanti nei log attività scaricati di Azure Active Directory - Anteprima | Microsoft Docs"
description: "Offre una soluzione per i dati mancanti nell&quot;anteprima dei log attività scaricati di Azure Active Directory."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/09/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: e0d65edcb7c14114565402038b0958c3a2ffb477
ms.lasthandoff: 03/10/2017


---

# <a name="i-cant-find-any-data-in-the-azure-active-directory-activity-logs-i-have-downloaded"></a>Non è possibile trovare dati nei log attività di Azure Active Directory scaricati


## <a name="symptoms"></a>Sintomi

I log attività (controllo o accessi) sono stati scaricati ma non vengono visualizzati tutti i record per l'orario scelto. Perché? 

 ![Creazione di report](./media/active-directory-reporting-troubleshoot-missing-data-download/01.png)
 

## <a name="cause"></a>Causa

Quando si scaricano i log attività nel portale di Azure, viene applicata una limitazione a 120.000 record, ordinati a partire dal più recente. 

## <a name="resolution"></a>Risoluzione

È possibile sfruttare le [API di Creazione rapporti di Azure AD](active-directory-reporting-api-getting-started.md) per recuperare fino a un milione di record per un momento specifico. L'approccio consigliato consiste nell'eseguire uno script in base a una pianificazione per chiamare le API di Creazione rapporti per recuperare i record in modo incrementale in un periodo di tempo specifico, ad esempio ogni giorno oppure ogni settimana.

## <a name="next-steps"></a>Passaggi successivi
Vedere [Domande frequenti sulla creazione di report in Azure Active Directory](active-directory-reporting-faq.md).


