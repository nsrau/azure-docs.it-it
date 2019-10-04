---
title: Report sull'utilizzo e le informazioni dettagliate nel portale Azure Active Directory | Microsoft Docs
description: Introduzione ai report di utilizzo e informazioni dettagliate nel portale Azure Active Directory
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: 3fba300d-18fc-4355-9924-d8662f563a1f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/13/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.openlocfilehash: 0c6723c28bf6ab8af112763a6485bc89b46409c6
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989851"
---
# <a name="usage-and-insights-report-in-the-azure-active-directory-portal"></a>Report sull'utilizzo e le informazioni dettagliate nel portale Azure Active Directory

Con il report utilizzo e informazioni dettagliate è possibile ottenere una visualizzazione incentrata sull'applicazione dei dati di accesso. È possibile trovare risposte alle domande seguenti:

*   Quali sono le applicazioni principali usate nell'organizzazione?
*   Quali applicazioni hanno gli accessi più non riusciti? 
*   Quali sono gli errori di accesso principali per ogni applicazione?

## <a name="prerequisites"></a>Prerequisiti 

Per accedere ai dati dal report utilizzo e informazioni dettagliate, è necessario:

* Un tenant di Azure AD
* Una licenza di Azure AD Premium (P1/P2) per visualizzare i dati di accesso
* Utente nei ruoli amministratore globale, amministratore della sicurezza, lettore sicurezza o lettore report. Inoltre, qualsiasi utente (non amministratore) può accedere ai propri accessi. 

## <a name="access-the-usage-and-insights-report"></a>Accedere al report utilizzo e informazioni dettagliate

1. Passare al [portale di Azure](https://portal.azure.com).
2. Selezionare la directory giusta, quindi selezionare **Azure Active Directory** e scegliere **applicazioni aziendali**.
3. Nella sezione **Activity** selezionare **Usage & Insights** per aprire il report. 

![Report su utilizzo e informazioni dettagliate](./media/concept-usage-insights-report/main-menu.png)
                                     

## <a name="use-the-report"></a>Utilizzare il report

Il report utilizzo e informazioni dettagliate Mostra l'elenco di applicazioni con uno o più tentativi di accesso e consente di eseguire l'ordinamento in base al numero di accessi riusciti, gli accessi non riusciti e la percentuale di successo.

Facendo clic su carica altro nella parte inferiore dell'elenco è possibile visualizzare altre applicazioni nella pagina. È possibile selezionare l'intervallo di date per visualizzare tutte le applicazioni utilizzate nell'intervallo.

È inoltre possibile impostare lo stato attivo su un'applicazione specifica. Selezionare **Visualizza attività di accesso** per visualizzare l'attività di accesso nel tempo per l'applicazione, nonché gli errori principali.  

Quando si seleziona un giorno nel grafico sull'utilizzo dell'applicazione, si ottiene un elenco dettagliato delle attività di accesso per l'applicazione.  

![Report su utilizzo e informazioni dettagliate](./media/concept-usage-insights-report/usage-and-insights-report.png)

## <a name="next-steps"></a>Passaggi successivi

* [Report degli accessi](concept-sign-ins.md)