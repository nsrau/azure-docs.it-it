---
title: Rapporto di accesso e uso per Azure MFA | Microsoft Docs
description: "Viene descritto come usare la funzionalità dei report di Azure Multi-Factor Authentication."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: curtand
ms.assetid: 3f6b33c4-04c8-47d4-aecb-aa39a61c4189
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: kgremban
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: f76e726c6a67de4b0472c0e97f9e72c31c14c4f2
ms.contentlocale: it-it
ms.lasthandoff: 05/15/2017


---
# <a name="reports-in-azure-multi-factor-authentication"></a>Report in Azure Multi-Factor Authentication
Azure Multi-Factor Authentication offre diversi report che possono essere utilizzati dall'utente e dall'organizzazione. Questi rapporti sono accessibili tramite il portale di gestione Multi-Factor Authentication. Di seguito è riportato un elenco dei rapporti disponibili:

| Report | Descrizione |
|:--- |:--- |
| Utilizzo |I rapporti di utilizzo consentono di visualizzare informazioni sull'uso generale, un riepilogo e dettagli sull'utente. |
| Stato server |Questo report consente di visualizzare lo stato dei server di Multi-Factor Authentication associati all'account. |
| Cronologia utenti bloccati |Questi report mostrano la cronologia delle richieste per bloccare o sbloccare gli utenti. |
| Cronologia utenti bypass |Consente di visualizzare la cronologia delle richieste per disabilitare Multi-Factor Authentication per il numero di telefono dell'utente. |
| Avviso di illecito |Consente di visualizzare una cronologia di avvisi di illecito inviati durante l'intervallo di date specificato dall'utente. |
| Queued |Consente di elencare i report accodati da elaborare e i relativi stati. Quando il report è completo, viene fornito un collegamento per scaricare o visualizzare il report. |

## <a name="view-reports"></a>Visualizzazione dei report
1. Accedere al [portale di Azure classico](https://manage.windowsazure.com).
2. A sinistra selezionare Active Directory.
3. Attenersi a una di queste due opzioni, a seconda che vengano usati provider di autenticazione:
   * **Option 1**: fare clic sulla scheda dei provider Multi-Factor Authentication. Selezionare il provider di MFA e fare clic sul pulsante **Gestisci** nella parte inferiore.
   * **Opzione 2**: selezionare la directory e passare alla scheda **Configura**. Nella sezione Multi-Factor Authentication selezionare **Gestisci impostazioni del servizio**. Nella parte inferiore della pagina delle impostazioni del servizio MFA, fare clic sul collegamento Vai al portale.
4. Nel portale di gestione Azure Multi-Factor Authentication, selezionare il tipo di rapporto desiderato dalla sezione **Visualizza un report** nel riquadro di navigazione a sinistra.

<center>![Cloud](./media/multi-factor-authentication-manage-reports/report.png)</center>


**Risorse aggiuntive**

* [Per gli utenti](end-user/multi-factor-authentication-end-user.md)
* [Azure Multi-Factor Authentication su MSDN](https://msdn.microsoft.com/library/azure/dn249471.aspx)

