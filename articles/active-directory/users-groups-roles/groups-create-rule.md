---
title: Creare o modificare un gruppo dinamico e ottenere lo stato - Azure AD Documenti Microsoft
description: Come creare o aggiornare una regola di appartenenza al gruppo nel portale di Azure e verificarne lo stato di elaborazione.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/07/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2ed7f27e2145f666f38eec5ddc6c985a4d32138
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266377"
---
# <a name="create-or-update-a-dynamic-group-in-azure-active-directory"></a>Creare o aggiornare un gruppo dinamico in Azure Active DirectoryCreate or update a dynamic group in Azure Active Directory

In Azure Active Directory (Azure AD) è possibile usare le regole per determinare l'appartenenza al gruppo in base alle proprietà dell'utente o del dispositivo. Questo articolo illustra come configurare una regola per un gruppo dinamico nel portale di Azure.This article tells how to set up a rule for a dynamic group in the Azure portal.
L'appartenenza dinamica è supportata per i gruppi di sicurezza o i gruppi di Office 365.Dynamic membership is supported for security groups or Office 365 groups. Quando viene applicata una regola di appartenenza al gruppo, gli attributi utente e dispositivo vengono valutati per le corrispondenze con la regola di appartenenza. Quando un attributo viene modificato per un utente o un dispositivo, tutte le regole di gruppo dinamiche nell'organizzazione vengono elaborate per le modifiche di appartenenza. Gli utenti e i dispositivi vengono aggiunti o rimossi se soddisfano le condizioni di un gruppo. I gruppi di sicurezza possono essere usati per dispositivi o utenti, ma i gruppi di Office 365 possono essere solo gruppi di utenti.

## <a name="rule-builder-in-the-azure-portal"></a>Generatore di regole nel portale di AzureRule builder in the Azure portal

Azure AD offre un generatore di regole per creare e aggiornare le regole importanti più rapidamente. Il generatore di regole supporta la costruzione fino a cinque espressioni. Il Generatore di regole semplifica la creazione di una regola con alcune espressioni semplici, tuttavia non può essere utilizzato per riprodurre ogni regola. Se il Generatore di regole non supporta la regola che si desidera creare, è possibile utilizzare la casella di testo.

Di seguito sono riportati alcuni esempi di regole o sintassi avanzate per le quali è consigliabile creare utilizzando la casella di testo:

- Regola con più di cinque espressioni
- La regola Rapporti diretti
- Impostazione della [precedenza](groups-dynamic-membership.md#operator-precedence) degli operatori
- [Regole con espressioni complesse](groups-dynamic-membership.md#rules-with-complex-expressions); Per esempio`(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> Il Generatore di regole potrebbe non essere in grado di visualizzare alcune regole costruite nella casella di testo. È possibile che venga visualizzato un messaggio quando il Generatore di regole non è in grado di visualizzare la regola. Il Generatore di regole non modifica in alcun modo la sintassi, la convalida o l'elaborazione supportata delle regole di gruppo dinamiche.

![Aggiungere una regola di appartenenza per un gruppo dinamico](./media/groups-create-rule/update-dynamic-group-rule.png)

Per esempi di sintassi, proprietà, operatori e valori supportati per una regola di appartenenza, vedere Regole di [appartenenza dinamiche per](groups-dynamic-membership.md)i gruppi in Azure Active Directory.

## <a name="to-create-a-group-membership-rule"></a>Per creare una regola di appartenenza a un gruppo

1. Accedere all'interfaccia di amministrazione di [Azure AD](https://aad.portal.azure.com) con un account che fa parte del ruolo Amministratore globale, Amministratore di Intune o Amministratore utente nel tenant.
1. Cercare e selezionare **Gruppi**.
1. Selezionare **Tutti i gruppi** e selezionare **Nuovo gruppo**.

   ![Selezionare il comando per aggiungere un nuovo gruppo](./media/groups-create-rule/create-new-group-azure-active-directory.png)

1. Nella pagina **Gruppo** immettere un nome e una descrizione per il nuovo gruppo. Selezionare un tipo di **appartenenza** per utenti o dispositivi e quindi selezionare **Aggiungi query dinamica**. Il generatore di regole supporta fino a cinque espressioni. Per aggiungere più di cinque espressioni, è necessario utilizzare la casella di testo.

   ![Aggiungere una regola di appartenenza per un gruppo dinamico](./media/groups-create-rule/add-dynamic-group-rule.png)

1. Per visualizzare le proprietà di estensione personalizzate disponibili per la query di appartenenza:
   1. Selezionare **Ottieni proprietà di estensione personalizzateSelect Get custom extension properties**
   1. Immettere l'ID applicazione e quindi selezionare **Aggiorna proprietà**.
1. Dopo aver creato la regola, selezionare **Salva**.
1. Selezionare **Crea** nella pagina **Nuovo gruppo** per creare il gruppo.

Se la regola immessa non è valida, viene visualizzata una spiegazione del motivo per cui non è stato possibile elaborare la regola in una notifica di Azure nel portale. Leggere attentamente per capire come risolvere la regola.

## <a name="to-update-an-existing-rule"></a>Per aggiornare una regola esistente

1. Accedere all'interfaccia di amministrazione di [Azure AD](https://aad.portal.azure.com) con un account che fa parte del ruolo Amministratore globale, Amministratore di gruppo, Amministratore di Intune o Amministratore utente nel tenant.
1. Selezionare **Gruppi** > **tutti i gruppi**.
1. Selezionare un gruppo per aprirne il profilo.
1. Nella pagina del profilo del gruppo selezionare Regole di **appartenenza dinamiche**. Il generatore di regole supporta fino a cinque espressioni. Per aggiungere più di cinque espressioni, è necessario utilizzare la casella di testo.

   ![Aggiungere una regola di appartenenza per un gruppo dinamico](./media/groups-create-rule/update-dynamic-group-rule.png)

1. Per visualizzare le proprietà di estensione personalizzate disponibili per la regola di appartenenza:
   1. Selezionare **Ottieni proprietà di estensione personalizzateSelect Get custom extension properties**
   1. Immettere l'ID applicazione e quindi selezionare **Aggiorna proprietà**.
1. Dopo aver aggiornato la regola, selezionare **Salva**.

## <a name="turn-on-or-off-welcome-email"></a>Attivare o disattivare il messaggio di posta elettronica di benvenuto

Quando viene creato un nuovo gruppo di Office 365, viene inviata una notifica di benvenuto e-mail gli utenti aggiunti al gruppo. Successivamente, se gli attributi di un utente o di un dispositivo cambiano, tutte le regole di gruppo dinamiche nell'organizzazione vengono elaborate per le modifiche di appartenenza. Anche gli utenti aggiunti ricevono la notifica di benvenuto. È possibile disattivare questo comportamento in [Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps).

## <a name="check-processing-status-for-a-rule"></a>Controllare lo stato di elaborazione di una regola

È possibile visualizzare lo stato di elaborazione dell'appartenenza e la data dell'ultimo aggiornamento nella pagina **Panoramica** per il gruppo.
  
  ![visualizzazione dello stato dinamico del gruppo](./media/groups-create-rule/group-status.png)

I seguenti messaggi di stato possono essere visualizzati per lo stato di **Elaborazione appartenenza**:

- **Valutazione**: la modifica dei gruppi è stata ricevuta e gli aggiornamenti sono in fase di valutazione.
- **Elaborazione**: gli aggiornamenti sono in fase di elaborazione.
- **Aggiornamento completato**: l’elaborazione è stata completata e tutti gli aggiornamenti sono stati apportati.
- **Errore di elaborazione:** impossibile completare l'elaborazione a causa di un errore durante la valutazione della regola di appartenenza.
- **Aggiornamento sospeso**: regola di appartenenza dinamica, gli aggiornamenti sono stati sospesi dall'amministratore. MembershipRuleProcessingState è impostato su "Paused".

I seguenti messaggi di stato possono essere visualizzati per lo stato dell'**Ultimo aggiornamento dell'appartenenza**:

- &lt;**Data e ora:**&gt;l'ultima volta che l'appartenenza è stata aggiornata.
- **In corso**: aggiornamenti in corso.
- **Sconosciuto:** l'ora dell'ultimo aggiornamento non può essere recuperata. Il gruppo potrebbe essere nuovo.

Se si verifica un errore durante l'elaborazione della regola di appartenenza per un gruppo specifico, un avviso viene visualizzato nella parte superiore della **Pagina di panoramica** per il gruppo. Se non è possibile elaborare aggiornamenti dell'appartenenza dinamica in sospeso per tutti i gruppi all'interno del tenant per più di 24 ore, viene visualizzato un avviso nella parte superiore di **Tutti i gruppi**.

![elaborazione degli avvisi dei messaggi di errore](./media/groups-create-rule/processing-error.png)

Questi articoli forniscono informazioni aggiuntive sui gruppi in Azure Active Directory.

- [Vedere i gruppi esistenti](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Creare un nuovo gruppo e aggiunta di membri](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Gestire le impostazioni di un gruppo](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Gestire le appartenenze di un gruppo](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Gestire le regole dinamiche per gli utenti in un gruppo](groups-dynamic-membership.md)
