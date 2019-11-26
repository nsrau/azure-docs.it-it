---
title: Aggiornare e gestire una regola di gruppo dinamica e risolvere i problemi di appartenenza-Azure Active Directory | Microsoft Docs
description: Come creare una regola di appartenenza a un gruppo nella portale di Azure, controllare lo stato.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/30/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c387e2d78adcaebc430073a2a45818c4a0928b9f
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422363"
---
# <a name="update-a-dynamic-group-to-manage-membership-in-azure-active-directory"></a>Aggiornare un gruppo dinamico per gestire l'appartenenza a Azure Active Directory

In Azure Active Directory (Azure AD), è possibile usare le regole per determinare l'appartenenza al gruppo in base alle proprietà dell'utente o del dispositivo. Questo articolo illustra come configurare una regola per un gruppo dinamico nella portale di Azure.
L'appartenenza dinamica è supportata per i gruppi di sicurezza o per i gruppi di Office 365. Quando viene applicata una regola di appartenenza a un gruppo, gli attributi utente e dispositivo vengono valutati per le corrispondenze con la regola di appartenenza. Quando un attributo viene modificato per un utente o un dispositivo, vengono elaborate tutte le regole dinamiche del gruppo nell'organizzazione per le modifiche dell'appartenenza. Utenti e dispositivi vengono aggiunti o rimossi se soddisfano le condizioni per un gruppo.

## <a name="rule-builder-in-the-azure-portal"></a>Generatore regole nella portale di Azure

Azure AD fornisce un generatore di regole per creare e aggiornare le regole importanti più rapidamente. Il generatore regole supporta la costruzione di un massimo di cinque espressioni. Il generatore regole rende più semplice formare una regola con alcune semplici espressioni, ma non può essere usata per riprodurre ogni regola. Se il generatore regole non supporta la regola che si desidera creare, è possibile utilizzare la casella di testo.

Di seguito sono riportati alcuni esempi di regole avanzate o sintassi per cui è consigliabile costruire utilizzando la casella di testo:

- Regola con più di cinque espressioni
- Regola dei report diretti
- Impostazione della [precedenza degli operatori](groups-dynamic-membership.md#operator-precedence)
- [Regole con espressioni complesse](groups-dynamic-membership.md#rules-with-complex-expressions); ad esempio `(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> Il generatore regole potrebbe non essere in grado di visualizzare alcune regole costruite nella casella di testo. Potrebbe essere visualizzato un messaggio quando il generatore regole non è in grado di visualizzare la regola. Il generatore regole non modifica in alcun modo la sintassi, la convalida o l'elaborazione delle regole di gruppo dinamiche supportate.

![Aggiungi regola di appartenenza per un gruppo dinamico](./media/groups-update-rule/update-dynamic-group-rule.png)

Per esempi di sintassi, proprietà, operatori e valori supportati per una regola di appartenenza, vedere [regole di appartenenza dinamiche per i gruppi in Azure Active Directory](groups-dynamic-membership.md).

## <a name="to-update-a-group-membership-rule"></a>Per aggiornare una regola di appartenenza a un gruppo

1. Accedere all'interfaccia di [amministrazione di Azure ad](https://aad.portal.azure.com) con un account di amministratore globale, amministratore del gruppo, amministratore di Intune o ruolo di amministratore utente nel tenant.
1. Selezionare **gruppi** > **tutti i gruppi**.
1. Selezionare un gruppo per aprirne il profilo.
1. Nella pagina profilo per il gruppo selezionare regole di **appartenenza dinamica**. Il generatore regole supporta fino a cinque espressioni. Per aggiungere più di cinque espressioni, è necessario utilizzare la casella di testo.

   ![Aggiungi regola di appartenenza per un gruppo dinamico](./media/groups-update-rule/update-dynamic-group-rule.png)

1. Per visualizzare le proprietà dell'estensione personalizzata disponibili per la regola di appartenenza:
   1. Selezionare **Ottieni proprietà estensione personalizzata**
   1. Immettere l'ID applicazione, quindi selezionare **Aggiorna proprietà**.
1. Dopo aver aggiornato la regola, selezionare **Salva**.

Se la regola immessa non è valida, una spiegazione del motivo per cui non è stato possibile elaborare la regola viene visualizzata in una notifica di Azure nel portale. Leggerlo attentamente per comprendere come correggere la regola.

## <a name="check-processing-status-for-a-rule"></a>Controllare lo stato di elaborazione di una regola

È possibile visualizzare lo stato di elaborazione dell'appartenenza e la data dell'ultimo aggiornamento nella pagina **Panoramica** per il gruppo.
  
  ![visualizzazione dello stato dinamico dei gruppi](./media/groups-create-rule/group-status.png)

I seguenti messaggi di stato possono essere visualizzati per lo stato di **Elaborazione appartenenza**:

- **Valutazione**: la modifica dei gruppi è stata ricevuta e gli aggiornamenti sono in fase di valutazione.
- **Elaborazione**: gli aggiornamenti sono in fase di elaborazione.
- **Aggiornamento completato**: l’elaborazione è stata completata e tutti gli aggiornamenti sono stati apportati.
- **Errore di elaborazione**: non è stato possibile completare l'elaborazione a causa di un errore durante la valutazione della regola di appartenenza.
- **Aggiornamento sospeso**: regola di appartenenza dinamica, gli aggiornamenti sono stati sospesi dall'amministratore. MembershipRuleProcessingState è impostato su "Paused".

I seguenti messaggi di stato possono essere visualizzati per lo stato dell'**Ultimo aggiornamento dell'appartenenza**:

- **Data e ora**: ora dell'ultimo aggiornamento dell'appartenenza.
- **In corso**: aggiornamenti in corso.
- **Sconosciuto**: non è possibile recuperare l'ora dell'ultimo aggiornamento. Il gruppo potrebbe essere nuovo.

Se si verifica un errore durante l'elaborazione della regola di appartenenza per un gruppo specifico, un avviso viene visualizzato nella parte superiore della **Pagina di panoramica** per il gruppo. Se non è possibile elaborare aggiornamenti in sospeso per l'appartenenza dinamica per tutti i gruppi all'interno del tenant per oltre 24 ore, viene visualizzato un avviso nella parte superiore di **Tutti i gruppi** .

![elaborazione degli avvisi dei messaggi di errore](./media/groups-create-rule/processing-error.png)

## <a name="next-steps"></a>Passaggi successivi

Questi articoli forniscono informazioni aggiuntive sull'uso dei gruppi dinamici in Azure AD.

- Per un riferimento completo alla struttura della regola dinamica, vedere [sintassi della regola di appartenenza dinamica](groups-dynamic-membership.md).
- [Creare un gruppo di appartenenze statico e aggiungere membri](../fundamentals/active-directory-groups-create-azure-portal.md).
