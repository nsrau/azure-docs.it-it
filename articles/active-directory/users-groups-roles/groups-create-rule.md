---
title: Creare un gruppo dinamico e controllare lo stato in Azure Active Directory | Microsoft Docs
description: Come creare regole di appartenenza a un gruppo nel portale di Azure e controllare lo stato.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 09/20/2018
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: e418316a74ccf27ec730261957a8b6c64de5d063
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "47040602"
---
# <a name="create-a-dynamic-group-and-check-status"></a>Creare un gruppo dinamico e controllare lo stato

In Azure Active Directory (Azure AD) è possibile creare gruppi applicando una regola per determinare l'appartenenza in base alle proprietà degli utenti o dei dispositivi. Quando gli attributi di un utente o un dispositivo cambiano, Azure AD valuta tutte le regole dei gruppi dinamici nel tenant di Azure AD ed esegue eventuali operazioni di aggiunta o rimozione. Se un utente o un dispositivo soddisfa una regola per un gruppo, viene aggiunto come membro e quando non la soddisfa più viene rimosso.

Questo articolo illustra in modo dettagliato come configurare una regola nel portale di Azure per l'appartenenza dinamica ai gruppi di sicurezza o ai gruppi di Office 365. Per esempi di sintassi delle regole e un elenco completo di proprietà, operatori e valori supportati per una regola di appartenenza, vedere [Regole di appartenenza dinamica per i gruppi in Azure Active Directory](groups-dynamic-membership.md).

## <a name="to-create-a-group-membership-rule"></a>Per creare una regola di appartenenza a un gruppo

1. Accedere all'[interfaccia di amministrazione di Azure AD](https://aad.portal.azure.com) con un account con il ruolo di amministratore globale, amministratore del servizio Intune o amministratore account utente nel tenant.
2. Selezionare **Gruppi**.
3. Selezionare **Tutti i gruppi** e selezionare **Nuovo gruppo**.

   ![Aggiungere un nuovo gruppo](./media/groups-create-rule/new-group-creation.png)

4. Nel pannello **Gruppo** immettere un nome e una descrizione per il nuovo gruppo. Selezionare un **Tipo di appartenenza** di **Utente dinamico** o **Dispositivo dinamico**, a seconda che si intenda creare una regola per gli utenti o per i dispositivi e quindi selezionare **Aggiungi query dinamica**. È possibile usare il generatore di regole per creare una regola semplice oppure scrivere manualmente una regola di appartenenza. Questo articolo contiene informazioni aggiuntive sugli attributi disponibili per utenti e dispositivi, oltre a esempi di regole di appartenenza.

   ![Aggiungere una regola di appartenenza dinamica](./media/groups-create-rule/add-dynamic-group-rule.png)

5. Dopo avere creato la regola, selezionare **Aggiungi query** nella parte inferiore del pannello.
6. Selezionare **Crea** on the **Gruppo** per creare il gruppo.

> [!TIP]
> La creazione del gruppo non riesce se la regola che è stata immessa è in un formato non corretto o non è valida. Verrà visualizzata una notifica nell'angolo superiore destro del portale, che contiene una spiegazione dei motivi per cui la regola non è stata elaborata. Leggere attentamente per capire come occorre modificare la regola per renderla valida.

## <a name="check-processing-status-for-a-membership-rule"></a>Controllare lo stato di elaborazione per una regola di appartenenza

È possibile visualizzare lo stato di elaborazione dell'appartenenza e la data dell'ultimo aggiornamento nella pagina **Panoramica** per il gruppo.
  
  ![visualizzazione stato del gruppo dinamico](./media/groups-create-rule/group-status.png)

I seguenti messaggi di stato possono essere visualizzati per lo stato di **Elaborazione appartenenza**:

* **Valutazione**: la modifica dei gruppi è stata ricevuta e gli aggiornamenti sono in fase di valutazione.
* **Elaborazione**: gli aggiornamenti sono in fase di elaborazione.
* **Aggiornamento completato**: l’elaborazione è stata completata e tutti gli aggiornamenti sono stati apportati.
* **Errore di elaborazione**: si è verificato un errore durante la valutazione della regola di appartenenza e l'elaborazione non è stata completata.
* **Aggiornamento sospeso**: regola di appartenenza dinamica, gli aggiornamenti sono stati sospesi dall'amministratore. MembershipRuleProcessingState è impostato su "Paused".

I seguenti messaggi di stato possono essere visualizzati per lo stato dell'**Ultimo aggiornamento dell'appartenenza**:

* &lt;**Data e ora**&gt;: ora dell'ultimo aggiornamento dell'appartenenza.
* **In corso**: aggiornamenti in corso.
* **Sconosciuto**: non è possibile recuperare l'orario dell'ultimo aggiornamento. Potrebbe essere dovuto alla recente creazione di un gruppo.

Se si verifica un errore durante l'elaborazione della regola di appartenenza per un gruppo specifico, un avviso viene visualizzato nella parte superiore della **Pagina di panoramica** per il gruppo. Se non è possibile elaborare aggiornamenti in sospeso per l'appartenenza dinamica per tutti i gruppi all'interno del tenant per oltre 24 ore, viene visualizzato un avviso nella parte superiore di **Tutti i gruppi** .

![messaggio di errore di elaborazione](./media/groups-create-rule/processing-error.png)

Questi articoli forniscono informazioni aggiuntive sui gruppi in Azure Active Directory.

* [Vedere i gruppi esistenti](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Creare un nuovo gruppo e aggiunta di membri](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Gestire le impostazioni di un gruppo](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Gestire le appartenenze di un gruppo](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Gestire le regole dinamiche per gli utenti in un gruppo](groups-dynamic-membership.md)
