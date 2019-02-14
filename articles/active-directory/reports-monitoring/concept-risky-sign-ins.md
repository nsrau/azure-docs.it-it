---
title: Report degli accessi a rischio nel portale di Azure Active Directory | Microsoft Docs
description: Informazioni sul report degli accessi a rischio nel portale di Azure Active Directory
services: active-directory
author: priyamohanram
manager: daveba
ms.assetid: 7728fcd7-3dd5-4b99-a0e4-949c69788c0f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b294d20242c1d6fdf1c998cd61706146a3156b9
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56173318"
---
# <a name="risky-sign-ins-report-in-the-azure-active-directory-portal"></a>Report degli accessi a rischio nel portale di Azure Active Directory

Azure Active Directory (Azure AD) rileva le azioni sospette correlate agli account utente. Per ogni azione rilevata viene creato un record detto **evento di rischio**. Per altre informazioni, vedere [Eventi di rischio di Azure Active Directory](concept-risk-events.md). 

È possibile accedere ai report di sicurezza dal [portale di Azure](https://portal.azure.com) selezionando il pannello **Azure Active Directory** e quindi passando alla sezione **Sicurezza**. 

Sono disponibili due report di sicurezza diversi che vengono calcolati in base agli eventi di rischio:

- **Accessi a rischio**. Un accesso rischioso è indicativo di un tentativo di accesso che potrebbe essere stato eseguito da qualcuno che non è il legittimo proprietario di un account utente.

- **Utenti contrassegnati per il rischio**. Un utente rischioso è indicativo di un account utente che potrebbe essere stato compromesso. 

![Accessi a rischio](./media/concept-risky-sign-ins/10.png)

Per informazioni su come configurare i criteri che attivano gli eventi di rischio, vedere [Procedura: Configurare i criteri di rischio utente](../identity-protection/howto-user-risk-policy.md).  

## <a name="who-can-access-the-risky-sign-ins-report"></a>Chi può accedere al report sugli accessi a rischio?

I report sugli accessi a rischio sono disponibili agli utenti nei seguenti ruoli:

- Amministratore della sicurezza
- Amministratore globale
- Ruolo con autorizzazioni di lettura per la sicurezza

Per informazioni su come assegnare ruoli amministrativi a un utente in Azure Active Directory, vedere [Visualizzare e assegnare i ruoli di amministratore in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-manage-roles-portal).

## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>Licenza di Azure AD necessaria per accedere a un report sulla sicurezza  

Tutte le edizioni di Azure AD offrono report relativi agli accessi a rischio. Tuttavia, il livello di granularità dei report varia a seconda delle edizioni: 

- Nelle edizioni **Azure Active Directory Free e Basic**  è incluso un elenco di accessi a rischio. 

- L'edizione **Azure Active Directory Premium 1** consente anche di esaminare alcuni degli eventi di rischio sottostanti che sono stati rilevati per ogni report. 

- L'edizione **Azure Active Directory Premium 2** offre informazioni più dettagliate su tutti gli eventi di rischio sottostanti e permette anche di configurare criteri di sicurezza che rispondono automaticamente a livelli di rischio configurati.

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

- Informazioni aggregate sui [tipi di evento di rischio](concept-risk-events.md) che sono stati rilevati. Con l'**edizione Azure AD Premium P1**, i rilevamenti non previsti dalla licenza in uso vengono visualizzati come evento di rischio con l'indicazione **È stato rilevato un accesso con rischi aggiuntivi**. Con l'**edizione Azure AD Premium P2**, si ottengono le informazioni più dettagliate su tutti i rilevamenti sottostanti.

- un'opzione per scaricare il report

![Accessi a rischio](./media/concept-risky-sign-ins/456.png)

Quando si seleziona un evento di rischio, si ottiene la visualizzazione di un report dettagliato per questo evento di rischio che consente di:

- avere un'opzione per configurare i [criteri per la correzione del rischio per gli utenti](../identity-protection/howto-user-risk-policy.md)  

- esaminare la cronologia di rilevamento per l'evento di rischio  

- esaminare l'elenco di utenti per cui è stato rilevato l'evento di rischio

- chiudere manualmente gli eventi di rischio. 

![Accessi a rischio](./media/concept-risky-sign-ins/457.png)

> [!IMPORTANT]
> A volte è possibile trovare un evento di rischio senza una voce di accesso corrispondente nel [report sugli accessi](concept-sign-ins.md). Questo avviene perché Identity Protection valuta il rischio per gli accessi sia **interattivi** che **non interattivi** mentre il report sugli accessi mostra solo gli accessi interattivi.

Quando si seleziona un utente, si ottiene la visualizzazione di un report dettagliato per questo utente che consente di:

- aprire le visualizzazioni di tutti gli accessi

- Reimpostare la password dell'utente

- eliminare tutti gli eventi

- ricercare la causa degli eventi a rischio segnalati per l'utente. 

![Accessi a rischio](./media/concept-risky-sign-ins/324.png)

Per ricercare la causa di un evento di rischio, selezionarlo dall'elenco.  
Verrà visualizzato il pannello **Dettagli** per questo evento di rischio. Nel pannello **Dettagli**, è possibile chiudere manualmente un evento di rischio o riattivare un evento di rischio chiuso manualmente. 

![Accessi a rischio](./media/concept-risky-sign-ins/325.png)

## <a name="next-steps"></a>Passaggi successivi

- [Procedura: Configurare i criteri di rischio utente](../identity-protection/howto-user-risk-policy.md)
- [Procedura: Configurare i criteri di correzione del rischio utente](../identity-protection/howto-user-risk-policy.md)
- [Tipi di eventi di rischio](concept-risk-events.md)
