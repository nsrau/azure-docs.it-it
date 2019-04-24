---
title: Creare un gruppo dinamico e controllare lo stato - Azure Active Directory | Microsoft Docs
description: Come creare una regola di appartenenza al gruppo nel portale di Azure, controllare lo stato.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f828ff83e6b9c60eb08edef7f47e88185fb5aef8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60472143"
---
# <a name="create-a-dynamic-group-and-check-status"></a>Creare un gruppo dinamico e controllare lo stato

In Azure Active Directory (Azure AD), è possibile utilizzare le regole per determinare l'appartenenza al gruppo in base alle proprietà utente o dispositivo. Questo articolo descrive come configurare una regola per un gruppo dinamico nel portale di Azure.
L'appartenenza dinamica è supportata per i gruppi di sicurezza o gruppi di Office 365. Quando viene applicata una regola di appartenenza al gruppo, vengono valutati gli attributi utente e dispositivo per individuare corrispondenze con la regola di appartenenza. Quando viene modificato un attributo per un utente o dispositivo, vengono elaborate tutte le regole di gruppo dinamico all'interno dell'organizzazione per modifiche all'appartenenza. Utenti e dispositivi vengono aggiunti o rimossi se soddisfano le condizioni per un gruppo.

Per esempi di sintassi, le proprietà supportate, operatori e valori per una regola di appartenenza, vedere [regole di appartenenza dinamica per i gruppi in Azure Active Directory](groups-dynamic-membership.md).

## <a name="to-create-a-group-membership-rule"></a>Per creare una regola di appartenenza a un gruppo

1. Accedi per il [interfaccia di amministrazione di Azure AD](https://aad.portal.azure.com) con un account che si trova nell'amministratore globale, amministratore di Intune o ruolo di utente amministratore nel tenant.
2. Selezionare **Gruppi**.
3. Selezionare **Tutti i gruppi** e selezionare **Nuovo gruppo**.

   ![Selezionare il comando per aggiungere nuovo gruppo](./media/groups-create-rule/new-group-creation.png)

4. Nel **gruppo** pagina, immettere un nome e una descrizione per il nuovo gruppo. Selezionare una **tipo di appartenenza** per gli utenti o dispositivi e quindi selezionare **Aggiungi query dinamica**. È possibile usare il generatore di regole per creare una regola semplice, o [scrivere una regola di appartenenza](groups-dynamic-membership.md).

   ![Aggiungi regola di appartenenza per un gruppo dinamico](./media/groups-create-rule/add-dynamic-group-rule.png)

5. Per visualizzare le proprietà di estensione personalizzato disponibile per la query di appartenenza
   1. Selezionare **ottenere le proprietà di estensione personalizzato**
   2. Immettere l'ID applicazione e quindi selezionare **Aggiorna le proprietà**. 
6. Dopo avere creato la regola, selezionare **Aggiungi query** nella parte inferiore del pannello.
7. Selezionare **Crea** on the **Gruppo** per creare il gruppo.

Se la regola che è stato immesso non è valida, una spiegazione del motivo per cui non può essere elaborata la regola viene visualizzata nell'angolo superiore destro del portale. Leggerla attentamente per capire come correggere la regola.

## <a name="turn-on-or-off-welcome-email"></a>Attivare o disattivare il messaggio di posta elettronica di benvenuto

Quando viene creato un nuovo gruppo di Office 365, è possibile che gli utenti che vengono aggiunti al gruppo viene inviata una notifica di benvenuto. In un secondo momento, se gli attributi di un utente o un dispositivo cambiano, tutte le regole di gruppo dinamico all'interno dell'organizzazione verranno elaborate modifiche dell'appartenenza. Anche gli utenti aggiunti ricevono la notifica di benvenuto. È possibile disattivare questo comportamento nel [PowerShell per Exchange](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps). 

## <a name="check-processing-status-for-a-rule"></a>Controllare lo stato di elaborazione per una regola

È possibile visualizzare lo stato di elaborazione dell'appartenenza e la data dell'ultimo aggiornamento nella pagina **Panoramica** per il gruppo.
  
  ![visualizzazione dello stato di gruppi dinamici](./media/groups-create-rule/group-status.png)

I seguenti messaggi di stato possono essere visualizzati per lo stato di **Elaborazione appartenenza**:

* **Valutazione**:  la modifica dei gruppi è stata ricevuta e gli aggiornamenti sono in fase di valutazione.
* **Elaborazione**: gli aggiornamenti sono in fase di elaborazione.
* **Aggiornamento completato**: l'elaborazione è stata completata e tutti gli aggiornamenti sono stati apportati.
* **Errore di elaborazione**:  Non è stato possibile completare l'elaborazione a causa di un errore durante la valutazione la regola di appartenenza.
* **Aggiornamento sospeso**: gli aggiornamenti della regola di appartenenza dinamica sono stati sospesi dall'amministratore. MembershipRuleProcessingState è impostato su "Paused".

I seguenti messaggi di stato possono essere visualizzati per lo stato dell'**Ultimo aggiornamento dell'appartenenza**:

* &lt;**Data e ora**&gt;: ora dell'ultimo aggiornamento dell'appartenenza.
* **In corso**: aggiornamenti in corso.
* **Sconosciuto**: Non è possibile recuperare l'ora dell'ultimo aggiornamento. Il gruppo potrebbe non essere ancora.

Se si verifica un errore durante l'elaborazione della regola di appartenenza per un gruppo specifico, un avviso viene visualizzato nella parte superiore della **Pagina di panoramica** per il gruppo. Se non è possibile elaborare aggiornamenti in sospeso per l'appartenenza dinamica per tutti i gruppi all'interno del tenant per oltre 24 ore, viene visualizzato un avviso nella parte superiore di **Tutti i gruppi** .

![gli avvisi di messaggio di errore di elaborazione](./media/groups-create-rule/processing-error.png)

Questi articoli forniscono informazioni aggiuntive sui gruppi in Azure Active Directory.

* [Vedere i gruppi esistenti](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Creare un nuovo gruppo e aggiunta di membri](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Gestire le impostazioni di un gruppo](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Gestire le appartenenze di un gruppo](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Gestire le regole dinamiche per gli utenti in un gruppo](groups-dynamic-membership.md)
