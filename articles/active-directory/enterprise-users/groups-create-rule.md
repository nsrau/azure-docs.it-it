---
title: Creare o modificare un gruppo dinamico e ottenere lo stato Azure AD | Microsoft Docs
description: Come creare o aggiornare una regola di appartenenza a un gruppo nella portale di Azure e controllarne lo stato di elaborazione.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 198c549be13592ae0626144c52abc9f6a67fd0d0
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348348"
---
# <a name="create-or-update-a-dynamic-group-in-azure-active-directory"></a>Crea o aggiorna un gruppo dinamico in Azure Active Directory

In Azure Active Directory (Azure AD), è possibile usare le regole per determinare l'appartenenza al gruppo in base alle proprietà dell'utente o del dispositivo. Questo articolo illustra come configurare una regola per un gruppo dinamico nella portale di Azure.
L'appartenenza dinamica è supportata per gruppi di sicurezza o gruppi di Microsoft 365. Quando viene applicata una regola di appartenenza a un gruppo, gli attributi utente e dispositivo vengono valutati per le corrispondenze con la regola di appartenenza. Quando un attributo viene modificato per un utente o un dispositivo, vengono elaborate tutte le regole dinamiche del gruppo nell'organizzazione per le modifiche dell'appartenenza. Utenti e dispositivi vengono aggiunti o rimossi se soddisfano le condizioni per un gruppo. I gruppi di sicurezza possono essere usati per dispositivi o utenti, ma Microsoft 365 gruppi possono essere solo gruppi di utenti. L'uso di gruppi dinamici richiede Azure AD licenza Premium P1. Per ulteriori informazioni, vedere [regole di appartenenza dinamiche per i gruppi](./groups-dynamic-membership.md) . 

## <a name="rule-builder-in-the-azure-portal"></a>Generatore di regole nel portale di Azure

Azure AD fornisce un generatore di regole per creare e aggiornare le regole importanti più rapidamente. Il generatore di regole supporta la costruzione di un massimo di cinque espressioni. Il generatore di regole rende più semplice formare una regola con alcune espressioni semplici, ma non può essere usato per riprodurre ogni regola. Se il generatore regole non supporta la regola che si desidera creare, è possibile utilizzare la casella di testo.

Di seguito sono riportati alcuni esempi di regole o sintassi avanzate che si consiglia di generare utilizzando la casella di testo:

- Regola con più di cinque espressioni
- La regola per i dipendenti diretti
- Impostazione della [precedenza degli operatore](groups-dynamic-membership.md#operator-precedence)
- [Regole con espressioni complesse](groups-dynamic-membership.md#rules-with-complex-expressions); ad esempio `(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> Il generatore di regole potrebbe non essere in grado di visualizzare alcune regole costruite nella casella di testo. Può essere visualizzato un messaggio quando il generatore di regole non è in grado di visualizzare la regola. Il generatore di regole non modifica in alcun modo la sintassi, la convalida o l'elaborazione delle regole di gruppo dinamiche supportate.

![Screenshot che mostra la pagina "regole di appartenenza dinamica" con l'azione "Aggiungi espressione" nella scheda "Configura regole" selezionata.](./media/groups-create-rule/update-dynamic-group-rule.png)

Per esempi di sintassi, proprietà, operatori e valori supportati per una regola di appartenenza, vedere [regole di appartenenza dinamiche per i gruppi in Azure Active Directory](groups-dynamic-membership.md).

## <a name="to-create-a-group-membership-rule"></a>Per creare una regola di appartenenza a un gruppo

1. Accedere all'interfaccia di [amministrazione di Azure ad](https://aad.portal.azure.com) con un account appartenente al ruolo amministratore globale, amministratore di Intune o amministratore utente nell'organizzazione Azure ad.
1. Cercare e selezionare i **gruppi**.
1. Selezionare **Tutti i gruppi** e selezionare **Nuovo gruppo**.

   ![Selezionare il comando per aggiungere un nuovo gruppo](./media/groups-create-rule/create-new-group-azure-active-directory.png)

1. Nella pagina **gruppo** immettere un nome e una descrizione per il nuovo gruppo. Selezionare un **tipo di appartenenza** per utenti o dispositivi e quindi selezionare **Aggiungi query dinamica**. Il generatore regole supporta fino a cinque espressioni. Per aggiungere più di cinque espressioni, è necessario utilizzare la casella di testo.

   ![Screenshot che mostra la pagina "tutti i gruppi" con l'azione "nuovo gruppo" selezionata.](./media/groups-create-rule/add-dynamic-group-rule.png)

1. Per visualizzare le proprietà dell'estensione personalizzata disponibili per la query di appartenenza:
   1. Selezionare **Ottieni proprietà estensione personalizzata**
   1. Immettere l'ID applicazione, quindi selezionare **Aggiorna proprietà**.
1. Dopo aver creato la regola, selezionare **Salva**.
1. Selezionare **Crea** nella pagina **nuovo gruppo** per creare il gruppo.

Se la regola immessa non è valida, una spiegazione del motivo per cui non è stato possibile elaborare la regola viene visualizzata in una notifica di Azure nel portale. Leggerlo attentamente per comprendere come correggere la regola.

## <a name="to-update-an-existing-rule"></a>Per aggiornare una regola esistente

1. Accedere all'interfaccia di [amministrazione di Azure ad](https://aad.portal.azure.com) con un account appartenente al ruolo amministratore globale, amministratore gruppo, amministratore di Intune o amministratore utente nell'organizzazione Azure ad.
1. Selezionare **gruppi**  >  **tutti i gruppi**.
1. Selezionare un gruppo per aprirne il profilo.
1. Nella pagina profilo per il gruppo selezionare regole di **appartenenza dinamica**. Il generatore regole supporta fino a cinque espressioni. Per aggiungere più di cinque espressioni, è necessario utilizzare la casella di testo.

   ![Aggiungere la regola di appartenenza per un gruppo dinamico](./media/groups-create-rule/update-dynamic-group-rule.png)

1. Per visualizzare le proprietà dell'estensione personalizzata disponibili per la regola di appartenenza:
   1. Selezionare **Ottieni proprietà estensione personalizzata**
   1. Immettere l'ID applicazione, quindi selezionare **Aggiorna proprietà**.
1. Dopo aver aggiornato la regola, selezionare **Salva**.

## <a name="turn-on-or-off-welcome-email"></a>Attivare o disattivare il messaggio di posta elettronica di benvenuto

Quando viene creato un nuovo gruppo di Microsoft 365, viene inviata una notifica di posta elettronica di benvenuto agli utenti aggiunti al gruppo. In seguito, se vengono modificati tutti gli attributi di un utente o un dispositivo, tutte le regole dinamiche del gruppo nell'organizzazione vengono elaborate per le modifiche dell'appartenenza. Anche gli utenti aggiunti ricevono la notifica di benvenuto. Questo comportamento può essere disattivato in [Exchange PowerShell](/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps&preserve-view=true).

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

- &lt;**Data e ora** &gt; : ora dell'ultimo aggiornamento dell'appartenenza.
- **In corso**: aggiornamenti in corso.
- **Sconosciuto**: non è possibile recuperare l'ora dell'ultimo aggiornamento. Il gruppo potrebbe essere nuovo.

Se si verifica un errore durante l'elaborazione della regola di appartenenza per un gruppo specifico, un avviso viene visualizzato nella parte superiore della **Pagina di panoramica** per il gruppo. Se non è possibile elaborare aggiornamenti di appartenenza dinamici in sospeso per tutti i gruppi all'interno dell'organizzazione per più di 24 ore, viene visualizzato un avviso nella parte superiore di **tutti i gruppi**.

![elaborazione degli avvisi dei messaggi di errore](./media/groups-create-rule/processing-error.png)

Questi articoli forniscono informazioni aggiuntive sui gruppi in Azure Active Directory.

- [Vedere i gruppi esistenti](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Creare un nuovo gruppo e aggiunta di membri](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Gestire le impostazioni di un gruppo](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Gestire le appartenenze di un gruppo](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Gestire le regole dinamiche per gli utenti in un gruppo](groups-dynamic-membership.md)