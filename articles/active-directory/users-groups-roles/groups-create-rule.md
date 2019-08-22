---
title: Creare un gruppo dinamico e controllare lo stato - Azure Active Directory | Microsoft Docs
description: Come creare una regola di appartenenza a un gruppo nella portale di Azure, controllare lo stato.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/12/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb4f9d2f78857231d0ecd81a2538a75b4b8a2f74
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650326"
---
# <a name="create-a-dynamic-group-and-check-status"></a>Creare un gruppo dinamico e controllare lo stato

In Azure Active Directory (Azure AD), è possibile usare le regole per determinare l'appartenenza al gruppo in base alle proprietà dell'utente o del dispositivo. Questo articolo illustra come configurare una regola per un gruppo dinamico nella portale di Azure.
L'appartenenza dinamica è supportata per i gruppi di sicurezza o per i gruppi di Office 365. Quando viene applicata una regola di appartenenza a un gruppo, gli attributi utente e dispositivo vengono valutati per le corrispondenze con la regola di appartenenza. Quando un attributo viene modificato per un utente o un dispositivo, vengono elaborate tutte le regole dinamiche del gruppo nell'organizzazione per le modifiche dell'appartenenza. Utenti e dispositivi vengono aggiunti o rimossi se soddisfano le condizioni per un gruppo. I gruppi di sicurezza possono essere usati per dispositivi o utenti, ma i gruppi di Office 365 possono essere solo gruppi di utenti.

Per esempi di sintassi, proprietà, operatori e valori supportati per una regola di appartenenza, vedere [regole di appartenenza dinamiche per i gruppi in Azure Active Directory](groups-dynamic-membership.md).

## <a name="to-create-a-group-membership-rule"></a>Per creare una regola di appartenenza a un gruppo

1. Accedere al centro di [amministrazione di Azure ad](https://aad.portal.azure.com) con un account che si trova nel ruolo amministratore globale, amministratore di Intune o amministratore utente nel tenant.
2. Selezionare **Gruppi**.
3. Selezionare **Tutti i gruppi** e selezionare **Nuovo gruppo**.

   ![Selezionare il comando per aggiungere un nuovo gruppo](./media/groups-create-rule/new-group-creation.png)

4. Nella pagina **gruppo** immettere un nome e una descrizione per il nuovo gruppo. Selezionare un **tipo di appartenenza** per utenti o dispositivi e quindi selezionare **Aggiungi query dinamica**. Il generatore regole supporta fino a cinque espressioni. Per aggiungere un sesto o qualsiasi espressione successiva, è necessario utilizzare la casella di testo.

   ![Aggiungi regola di appartenenza per un gruppo dinamico](./media/groups-create-rule/add-dynamic-group-rule.png)

5. Per visualizzare le proprietà dell'estensione personalizzata disponibili per la query di appartenenza
   1. Selezionare **Ottieni proprietà estensione personalizzata**
   2. Immettere l'ID applicazione, quindi selezionare **Aggiorna proprietà**.
6. Dopo aver creato la regola, selezionare **Salva**.
7. Selezionare **Crea** nella pagina **nuovo gruppo** per creare il gruppo.

Se la regola immessa non è valida, una spiegazione del motivo per cui non è stato possibile elaborare la regola viene visualizzata nella notifica di Azure nel portale. Leggerlo attentamente per comprendere come correggere la regola.

## <a name="turn-on-or-off-welcome-email"></a>Attivare o disattivare il messaggio di posta elettronica di benvenuto

Quando viene creato un nuovo gruppo di Office 365, viene inviata una notifica di posta elettronica di benvenuto agli utenti aggiunti al gruppo. In seguito, se vengono modificati tutti gli attributi di un utente o un dispositivo, tutte le regole dinamiche del gruppo nell'organizzazione vengono elaborate per le modifiche dell'appartenenza. Anche gli utenti aggiunti ricevono la notifica di benvenuto. Questo comportamento può essere disattivato in [Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps).

## <a name="check-processing-status-for-a-rule"></a>Controllare lo stato di elaborazione di una regola

È possibile visualizzare lo stato di elaborazione dell'appartenenza e la data dell'ultimo aggiornamento nella pagina **Panoramica** per il gruppo.
  
  ![visualizzazione dello stato dinamico dei gruppi](./media/groups-create-rule/group-status.png)

I seguenti messaggi di stato possono essere visualizzati per lo stato di **Elaborazione appartenenza**:

* **Valutazione**:  la modifica dei gruppi è stata ricevuta e gli aggiornamenti sono in fase di valutazione.
* **Elaborazione**: gli aggiornamenti sono in fase di elaborazione.
* **Aggiornamento completato**: l'elaborazione è stata completata e tutti gli aggiornamenti sono stati apportati.
* **Errore di elaborazione**:  Non è stato possibile completare l'elaborazione a causa di un errore durante la valutazione della regola di appartenenza.
* **Aggiornamento sospeso**: gli aggiornamenti della regola di appartenenza dinamica sono stati sospesi dall'amministratore. MembershipRuleProcessingState è impostato su "Paused".

I seguenti messaggi di stato possono essere visualizzati per lo stato dell'**Ultimo aggiornamento dell'appartenenza**:

* &lt;**Data e ora**&gt;: ora dell'ultimo aggiornamento dell'appartenenza.
* **In corso**: aggiornamenti in corso.
* **Sconosciuto**: Non è possibile recuperare l'ora dell'ultimo aggiornamento. Il gruppo potrebbe essere nuovo.

Se si verifica un errore durante l'elaborazione della regola di appartenenza per un gruppo specifico, un avviso viene visualizzato nella parte superiore della **Pagina di panoramica** per il gruppo. Se non è possibile elaborare aggiornamenti in sospeso per l'appartenenza dinamica per tutti i gruppi all'interno del tenant per oltre 24 ore, viene visualizzato un avviso nella parte superiore di **Tutti i gruppi** .

![elaborazione degli avvisi dei messaggi di errore](./media/groups-create-rule/processing-error.png)

Questi articoli forniscono informazioni aggiuntive sui gruppi in Azure Active Directory.

* [Vedere i gruppi esistenti](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Creare un nuovo gruppo e aggiunta di membri](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Gestire le impostazioni di un gruppo](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Gestire le appartenenze di un gruppo](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Gestire le regole dinamiche per gli utenti in un gruppo](groups-dynamic-membership.md)
