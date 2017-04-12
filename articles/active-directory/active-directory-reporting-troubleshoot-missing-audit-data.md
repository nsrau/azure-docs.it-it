---
title: "Risoluzione dei problemi: dati mancanti nel log attività di Azure Active Directory - Anteprima | Microsoft Docs"
description: Elenca i vari report disponibili per l&quot;anteprima di Azure Active Directory
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
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: c372fe5f3a419a6a27ef00d755d5d46325b956c6
ms.lasthandoff: 04/06/2017


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


