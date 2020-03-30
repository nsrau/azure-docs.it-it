---
title: Report di accesso rischioso nel portale Documenti Microsoft
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
ms.date: 03/04/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: b77486064139895799ac5a48327377154f75da6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273828"
---
# <a name="risky-sign-ins-report-in-the-azure-active-directory-portal"></a>Report degli accessi a rischio nel portale di Azure Active Directory

Azure Active Directory (Azure AD) rileva le azioni sospette correlate agli account utente. Per ogni azione rilevata, viene creato un record denominato **rilevamento dei rischi.** Per altre informazioni, vedere [Rilevamenti dei rischi](concept-risk-events.md)di Azure AD. 

È possibile accedere ai report di sicurezza dal [portale di Azure](https://portal.azure.com) selezionando il pannello **Azure Active Directory** e quindi passando alla sezione **Sicurezza**. 

Esistono due diversi report di sicurezza che vengono calcolati in base ai rilevamenti dei rischi:

- **Accessi a rischio**. Un accesso rischioso è indicativo di un tentativo di accesso che potrebbe essere stato eseguito da qualcuno che non è il legittimo proprietario di un account utente.

- **Utenti contrassegnati per il rischio**. Un utente rischioso è indicativo di un account utente che potrebbe essere stato compromesso. 

![Accessi a rischio](./media/concept-risky-sign-ins/10.png)

Per informazioni su come configurare i criteri che attivano questi rilevamenti dei rischi, vedere [Come configurare i criteri di rischio utente.](../identity-protection/howto-user-risk-policy.md)  

## <a name="who-can-access-the-risky-sign-ins-report"></a>Chi può accedere al report sugli accessi a rischio?

I report sugli accessi a rischio sono disponibili agli utenti nei seguenti ruoli:

- Amministratore della sicurezza
- Amministratore globale
- Ruolo con autorizzazioni di lettura per la sicurezza

Per informazioni su come assegnare ruoli amministrativi a un utente in Azure Active Directory, vedere [Visualizzare e assegnare i ruoli di amministratore in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-manage-roles-portal).

## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>Licenza di Azure AD necessaria per accedere a un report sulla sicurezza  

Tutte le edizioni di Azure AD offrono report relativi agli accessi a rischio. Tuttavia, il livello di granularità dei report varia a seconda delle edizioni: 

- Nell'edizione gratuita di **Azure Active Directory**viene visualizzato un elenco di accessi rischiosi. 

- Inoltre, l'edizione **Azure Active Directory Premium 1** consente di esaminare alcuni dei rilevamenti dei rischi sottostanti rilevati per ogni report. 

- L'edizione **Azure Active Directory Premium 2** offre le informazioni più dettagliate su tutti i rilevamenti dei rischi sottostanti e consente inoltre di configurare criteri di sicurezza che rispondono automaticamente ai livelli di rischio configurati.

## <a name="risky-sign-ins-report-for-azure-ad-free-edition"></a>Report di accesso rischioso per l'edizione gratuita di Azure AD

L'edizione gratuita di Azure AD fornisce un elenco di accessi rischiosi rilevati per gli utenti. Ogni record contiene gli attributi seguenti:

- **Utente:** nome dell'utente utilizzato durante l'operazione di accesso.
- **IP:** indirizzo IP del dispositivo utilizzato per connettersi ad Azure Active Directory.
- **Percorso:** percorso utilizzato per connettersi ad Azure Active Directory.Location - The location used to connect to Azure Active Directory. È un'approssimazione basata su tracce, dati del Registro di sistema, ricerche inverse e altre informazioni.
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

- Informazioni aggregate sui [tipi di rilevamento](concept-risk-events.md) dei rischi rilevati. Con **l'edizione Azure AD Premium P1,** i rilevamenti non coperti dalla licenza vengono visualizzati come accesso al rilevamento dei rischi **con rischio aggiuntivo rilevato.** Con **l'edizione Azure AD Premium P2,** si ottengono le informazioni più dettagliate su tutti i rilevamenti sottostanti.

- un'opzione per scaricare il report

![Accessi a rischio](./media/concept-risky-sign-ins/456.png)

Quando si seleziona un rilevamento dei rischi, si ottiene una visualizzazione report dettagliata per questo rilevamento dei rischi che consente di:

- avere un'opzione per configurare i [criteri per la correzione del rischio per gli utenti](../identity-protection/howto-user-risk-policy.md)  

- Esaminare la sequenza temporale di rilevamento per il rilevamento dei rischi  

- Esaminare un elenco di utenti per i quali è stato rilevato il rilevamento dei rischiReview a list of users for which this risk detection has been detected

- Chiudere manualmente i rilevamenti dei rischi. 

![Accessi a rischio](./media/concept-risky-sign-ins/457.png)

> [!IMPORTANT]
> In alcuni stati, è possibile trovare un rilevamento dei rischi senza una voce di accesso corrispondente nel [report di accesso](concept-sign-ins.md). Questo avviene perché Identity Protection valuta il rischio per gli accessi sia **interattivi** che **non interattivi** mentre il report sugli accessi mostra solo gli accessi interattivi.

Quando si seleziona un utente, si ottiene la visualizzazione di un report dettagliato per questo utente che consente di:

- aprire le visualizzazioni di tutti gli accessi

- Reimpostare la password dell'utente

- eliminare tutti gli eventi

- Esaminare i rilevamenti dei rischi segnalati per l'utente. 

![Accessi a rischio](./media/concept-risky-sign-ins/324.png)

Per analizzare il rilevamento dei rischi, selezionarne uno dall'elenco.  
Verrà aperto il pannello **Dettagli** per il rilevamento dei rischi. Nel pannello **Dettagli** è possibile chiudere manualmente un rilevamento dei rischi o riattivarlo manualmente. 

![Accessi a rischio](./media/concept-risky-sign-ins/325.png)

## <a name="next-steps"></a>Passaggi successivi

- [Come configurare i criteri di rischio per gli utenti](../identity-protection/howto-user-risk-policy.md)
- [Procedura: Configurare i criteri di correzione del rischio utente](../identity-protection/howto-user-risk-policy.md)
- [Tipi di rilevamento dei rischi](concept-risk-events.md)
