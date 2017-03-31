---
title: "Risoluzione dei problemi: dati mancanti nel log attività di Azure Active Directory - Anteprima | Microsoft Docs"
description: Offre una soluzione ed elenca i vari report disponibili per l&quot;anteprima di Azure Active Directory
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 7cbe4337-bb77-4ee0-b254-3e368be06db7
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/09/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: ebc92bc23201dbcd01f90e0cc3d5055fb85820be
ms.lasthandoff: 03/10/2017


---

# <a name="i-cant-find-some-actions-that-i-performed-in-the-azure-active-directory-activity-log"></a>Non è possibile trovare alcune azioni eseguite nel log attività di Azure Active Directory


## <a name="symptoms"></a>Sintomi

Sono state eseguite alcune azioni nel portale di Azure e si prevedeva la visualizzazione dei log di controllo per tali azioni nel pannello `Activity logs > Audit Logs`, ma non è possibile trovarli.

 ![Creazione di report](./media/active-directory-reporting-troubleshoot-missing-audit-data/01.png)
 

## <a name="cause"></a>Causa

Le azioni non vengono visualizzate immediatamente nel log di controllo delle attività. La visualizzazione dei log di controllo nel portale può richiedere tra i 15 minuti e un'ora, a partire dal momento in cui viene eseguita l'azione.

## <a name="resolution"></a>Risoluzione

Attendere tra 15 minuti e un'ora e verificare se le azioni vengono visualizzate nel log. Se non vengono ancora visualizzate, creare un ticket di supporto e il problema verrà esaminato.


## <a name="next-steps"></a>Passaggi successivi
Vedere [Domande frequenti sulla creazione di report in Azure Active Directory](active-directory-reporting-faq.md).


