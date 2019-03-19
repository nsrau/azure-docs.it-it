---
title: Report di sicurezza Utenti contrassegnati per il rischio nel portale di Azure Active Directory | Microsoft Docs
description: Informazioni sul report di sicurezza Utenti contrassegnati per il rischio nel portale di Azure Active Directory
services: active-directory
author: priyamohanram
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/17/2019
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe94f516a9675a4a930e4a59411ece58b562cf41
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57769354"
---
# <a name="users-flagged-for-risk-report-in-the-azure-portal"></a>Report Utenti contrassegnati per il rischio nel portale di Azure

Azure Active Directory (Azure AD) rileva le azioni sospette correlate agli account utente. Per ogni azione rilevata viene creato un record detto [evento di rischio](concept-risk-events.md).

È possibile accedere ai report di sicurezza dal [portale di Azure](https://portal.azure.com) selezionando il pannello **Azure Active Directory** e quindi passando alla sezione **Sicurezza**. 

Gli eventi di rischio rilevati vengono usati per calcolare:

- **Accessi a rischio**. Un accesso rischioso è indicativo di un tentativo di accesso che potrebbe essere stato eseguito da qualcuno che non è il legittimo proprietario di un account utente. 

- **Utenti contrassegnati per il rischio**. Un utente rischioso è indicativo di un account utente che potrebbe essere stato compromesso. 

Per informazioni su come configurare i criteri che attivano gli eventi di rischio, vedere [Procedura: Configurare i criteri di rischio utente](../identity-protection/howto-user-risk-policy.md). 

![Accessi a rischio](./media/concept-user-at-risk/10.png)


## <a name="what-azure-ad-license-do-you-need-to-access-the-users-at-risk-report"></a>Quale licenza di Azure AD è necessaria per accedere al report sugli utenti a rischio?  

Tutte le edizioni di Azure Active Directory offrono report sugli utenti contrassegnati per il rischio. Tuttavia, il livello di granularità dei report varia a seconda delle edizioni: 

- Nelle edizioni **Azure Active Directory Free e Basic**  è incluso un elenco degli utenti contrassegnati per il rischio. 

- L'edizione **Azure Active Directory Premium 1** consente anche di esaminare alcuni degli eventi di rischio sottostanti che sono stati rilevati per ogni report. 

- L'edizione **Azure Active Directory Premium 2** offre informazioni più dettagliate su tutti gli eventi di rischio sottostanti e permette anche di configurare criteri di sicurezza che rispondono automaticamente a livelli di rischio configurati.


## <a name="users-at-risk-report-for-azure-ad-free-and-basic-editions"></a>Report sugli utenti a rischio per le edizioni Azure AD Free e Basic

Il report Utenti contrassegnati per il rischio nelle edizioni Azure AD Free e Basic fornisce un elenco degli account utente che potrebbero essere stati compromessi. 

![Accessi a rischio](./media/concept-user-at-risk/03.png)

La selezione di un utente fornisce le informazioni di accesso. Analizzare la cronologia di accesso degli utenti a rischio e reimpostare la password, se necessario.

Questa finestra di dialogo offre la possibilità di:

- Scaricare il report
- Cercare gli utenti

    ![Accessi a rischio](./media/concept-user-at-risk/16.png)

Per informazioni più dettagliate, è necessaria una licenza premium.

## <a name="users-at-risk-report-for-azure-ad-premium-editions"></a>Report sugli utenti a rischio per le edizioni Azure AD Premium

Il report Utenti contrassegnati per il rischio nelle edizioni Azure AD Premium fornisce:

- Un elenco di account utente che potrebbero essere stati compromessi 

- informazioni aggregate sui [tipi di evento di rischio](concept-risk-events.md) che sono stati rilevati

- Un'opzione per scaricare il report

- avere un'opzione per configurare i [criteri per la correzione del rischio per gli utenti](../identity-protection/howto-user-risk-policy.md)  

![Accessi a rischio](./media/concept-user-at-risk/71.png)

Quando si seleziona un utente, si ottiene la visualizzazione di un report dettagliato per questo utente che consente di:

- aprire le visualizzazioni di tutti gli accessi

- Reimpostare la password dell'utente

- eliminare tutti gli eventi

- ricercare la causa degli eventi a rischio segnalati per l'utente. 

![Accessi a rischio](./media/concept-user-at-risk/324.png)

Per analizzare un evento di rischio, selezionarne uno nell'elenco per aprire il pannello **Dettagli** dell'evento di rischio. Nel pannello **Dettagli**, è possibile chiudere manualmente un evento di rischio o riattivare un evento di rischio chiuso manualmente. 

![Accessi a rischio](./media/concept-user-at-risk/325.png)


## <a name="next-steps"></a>Passaggi successivi

- [Procedura: Configurare i criteri di rischio utente](../identity-protection/howto-user-risk-policy.md)
- [Procedura: Configurare i criteri di correzione del rischio utente](../identity-protection/howto-user-risk-policy.md)
- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)

