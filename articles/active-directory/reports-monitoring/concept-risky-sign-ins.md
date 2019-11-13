---
title: Report degli accessi a rischio nel portale | Microsoft Docs
description: Informazioni sul report degli accessi a rischio nel portale di Azure Active Directory
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 7728fcd7-3dd5-4b99-a0e4-949c69788c0f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 10/28/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f19744e6c860ae315b681f5eb090cba23b153af
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74008348"
---
# <a name="risky-sign-ins-report-in-the-azure-active-directory-portal"></a>Report degli accessi a rischio nel portale di Azure Active Directory

Azure Active Directory (Azure AD) rileva le azioni sospette correlate agli account utente. Per ogni azione rilevata, viene creato un record denominato **rilevamento dei rischi** . Per informazioni dettagliate, vedere [Azure ad rilevamento dei rischi](concept-risk-events.md). 

È possibile accedere ai report di sicurezza dal [portale di Azure](https://portal.azure.com) selezionando il pannello **Azure Active Directory** e quindi passando alla sezione **Sicurezza**. 

Sono disponibili due report di sicurezza diversi che vengono calcolati in base ai rilevamenti dei rischi:

- **Accessi a rischio**. Un accesso rischioso è indicativo di un tentativo di accesso che potrebbe essere stato eseguito da qualcuno che non è il legittimo proprietario di un account utente.

- **Utenti contrassegnati per il rischio**. Un utente rischioso è indicativo di un account utente che potrebbe essere stato compromesso. 

![Accessi a rischio](./media/concept-risky-sign-ins/10.png)

Per informazioni su come configurare i criteri per l'attivazione di questi rilevamenti dei rischi, vedere [come configurare i criteri di rischio utente](../identity-protection/howto-user-risk-policy.md).  

## <a name="who-can-access-the-risky-sign-ins-report"></a>Chi può accedere al report sugli accessi a rischio?

I report sugli accessi a rischio sono disponibili agli utenti nei seguenti ruoli:

- Amministratore della sicurezza
- Amministratore globale
- Ruolo con autorizzazioni di lettura per la sicurezza

Per informazioni su come assegnare ruoli amministrativi a un utente in Azure Active Directory, vedere [Visualizzare e assegnare i ruoli di amministratore in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-manage-roles-portal).

## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>Licenza di Azure AD necessaria per accedere a un report sulla sicurezza  

Tutte le edizioni di Azure AD offrono report relativi agli accessi a rischio. Tuttavia, il livello di granularità dei report varia a seconda delle edizioni: 

- Nelle edizioni **Azure Active Directory Free e Basic**  è incluso un elenco di accessi a rischio. 

- Inoltre, la **Azure Active Directory Premium 1** edizione consente di esaminare alcuni dei rilevamenti di rischio sottostanti rilevati per ogni report. 

- L'edizione **Azure Active Directory Premium 2** fornisce le informazioni più dettagliate su tutti i rilevamenti di rischi sottostanti e consente inoltre di configurare criteri di sicurezza che rispondono automaticamente ai livelli di rischio configurati.

## <a name="risky-sign-ins-report-for-azure-ad-free-and-basic-edition"></a>Report degli accessi a rischio per le edizioni Azure AD Free e Basic

Le edizioni Azure AD Free e Basic forniscono un elenco di accessi a rischio che sono stati rilevati per gli utenti. Ogni record contiene gli attributi seguenti:

- **Utente**: nome dell'utente usato durante l'operazione di accesso.
- **IP**: indirizzo IP del dispositivo usato per connettersi ad Azure Active Directory.
- **Località**: località usata per connettersi ad Azure Active Directory. È un'approssimazione basata su tracce, dati del Registro di sistema, ricerche inverse e altre informazioni.
- **Ora di accesso**: ora in cui è stato eseguito l'accesso.
- **Stato**: stato dell'accesso.

![Accessi a rischio](./media/concept-risky-sign-ins/01.png)

In base all'analisi dell'accesso a rischio, è possibile fornire un feedback ad Azure AD eseguendo queste azioni:

- Risolvi
- Contrassegna come falso positivo
- Ignora
- Riattiva

![Accessi a rischio](./media/concept-risky-sign-ins/21.png)

Questo report offre anche la possibilità di:

- Cercare risorse
- Scaricare i dati del report

![Accessi a rischio](./media/concept-risky-sign-ins/93.png)

## <a name="risky-sign-ins-report-for-azure-ad-premium-editions"></a>Report degli accessi a rischio per le edizioni Azure AD Premium

Il report degli accessi a rischio nelle edizioni Azure AD Premium fornisce:

- Informazioni aggregate sui [tipi di rilevamento dei rischi](concept-risk-events.md) rilevati. Con l' **edizione Azure ad Premium P1**, i rilevamenti che non sono coperti dalla licenza vengono visualizzati come l'accesso al rilevamento dei rischi **con rischi aggiuntivi rilevati**. Con l'**edizione Azure AD Premium P2**, si ottengono le informazioni più dettagliate su tutti i rilevamenti sottostanti.

- un'opzione per scaricare il report

![Accessi a rischio](./media/concept-risky-sign-ins/456.png)

Quando si seleziona un rilevamento del rischio, si ottiene una visualizzazione dettagliata del report per questo rilevamento dei rischi che consente di:

- avere un'opzione per configurare i [criteri per la correzione del rischio per gli utenti](../identity-protection/howto-user-risk-policy.md)  

- Esaminare la sequenza temporale di rilevamento per il rilevamento dei rischi  

- Esaminare un elenco di utenti per cui è stato rilevato questo rilevamento dei rischi

- Chiudere manualmente i rilevamenti di rischio. 

![Accessi a rischio](./media/concept-risky-sign-ins/457.png)

> [!IMPORTANT]
> In alcuni casi, è possibile che si verifichi un rilevamento dei rischi senza una voce di accesso corrispondente nel [report degli accessi](concept-sign-ins.md). Questo avviene perché Identity Protection valuta il rischio per gli accessi sia **interattivi** che **non interattivi** mentre il report sugli accessi mostra solo gli accessi interattivi.

Quando si seleziona un utente, si ottiene la visualizzazione di un report dettagliato per questo utente che consente di:

- aprire le visualizzazioni di tutti gli accessi

- Reimpostare la password dell'utente

- eliminare tutti gli eventi

- Esaminare i rilevamenti dei rischi segnalati per l'utente. 

![Accessi a rischio](./media/concept-risky-sign-ins/324.png)

Per esaminare un rilevamento dei rischi, selezionarne uno dall'elenco.  
Verrà visualizzato il pannello dei **Dettagli** per questo rilevamento dei rischi. Nel pannello dei **Dettagli** è possibile scegliere di chiudere manualmente un rilevamento dei rischi oppure riattivare un rilevamento dei rischi chiuso manualmente. 

![Accessi a rischio](./media/concept-risky-sign-ins/325.png)

## <a name="next-steps"></a>Passaggi successivi

- [Procedura: Configurare i criteri di rischio utente](../identity-protection/howto-user-risk-policy.md)
- [Procedura: Configurare i criteri di correzione del rischio utente](../identity-protection/howto-user-risk-policy.md)
- [Tipi di rilevamento del rischio](concept-risk-events.md)
