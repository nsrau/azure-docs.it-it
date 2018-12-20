---
title: Gestire l'accesso alla fatturazione di Azure | Microsoft Docs
description: Informazioni su come fornire l’accesso alle informazioni di fatturazione Azure ai membri del team.
services: ''
documentationcenter: ''
author: vikramdesai01
manager: amberb
editor: ''
tags: billing
ms.assetid: e4c4d136-2826-4938-868f-a7e67ff6b025
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: cwatson
ms.custom: seodec18
ms.openlocfilehash: a15d055505ca0f28ad28b477e90e6fe859cc9ac7
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53094288"
---
# <a name="manage-access-to-billing-information-for-azure"></a>Gestire l'accesso alle informazioni di fatturazione per Azure

Per la maggior parte delle sottoscrizioni è possibile consentire l'accesso alle informazioni di fatturazione ai membri del team da **Sottoscrizioni** nel portale di Azure. Il cliente di Azure che dispone di un Contratto Enterprise (clienti EA) ed è Amministratore dell'organizzazione può concedere le autorizzazioni agli amministratori di reparto e ai proprietari di account in Enterprise portal.

## <a name="give-access-to-billing"></a>Fornire l'accesso alla fatturazione

Tutti, tranne i clienti con Contratto Enterprise, possono concedere l'accesso alle informazioni di fatturazione di Azure assegnando uno dei ruoli utente seguenti ai membri del team:

- Amministratore dell'account
- Amministratore del servizio
- Co-amministratore
- Proprietario
- Collaboratore
- Reader
- Lettore per la fatturazione

Per assegnare questi ruoli, vedere [Gestire l'accesso usando il controllo degli accessi in base al ruolo e il portale di Azure](../role-based-access-control/role-assignments-portal.md).

Questi ruoli possono accedere alle informazioni di fatturazione nel [portale di Azure](https://portal.azure.com/). Gli utenti assegnati a tali ruoli possono anche usare le [API di fatturazione](billing-usage-rate-card-overview.md) per ottenere le fatture e i dettagli di utilizzo a livello di codice. Per altre informazioni, vedere [Ruoli nel Controllo degli accessi in base al ruolo di Azure](../role-based-access-control/built-in-roles.md).

### <a name="opt-in"></a> Consentire agli utenti di scaricare le fatture

Dopo aver assegnato i ruoli appropriati ai membri del team, l'amministratore dell'account deve consentire agli utenti di scaricare le fatture nel portale di Azure. Le fatture redatte prima di dicembre 2016 sono disponibili solo all'amministratore dell'account.

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. In qualità di amministratore dell'account, selezionare la sottoscrizione dal [pannello Sottoscrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) nel portale di Azure.

1. Selezionare **Fatture** e quindi **Access to invoices** (Accesso alle fatture).

    ![Lo screenshot mostra come delegare l'accesso alle fatture](./media/billing-manage-access/AA-optin.png)

1. Selezionare **On** e salvare.

    ![Lo screenshot mostra le impostazioni on-off per delegare l'accesso alla fattura](./media/billing-manage-access/AA-optinAllow.png)

L'amministratore dell'account può anche impostare una configurazione in modo da ricevere le fatture tramite posta elettronica. Per altre informazioni, vedere [Ottenere la fatturazione tramite posta elettronica](billing-download-azure-invoice-daily-usage-date.md).

## <a name="give-read-only-access-to-billing"></a>Fornire l'accesso di sola lettura alla fatturazione

Assegnare il ruolo di lettore della fatturazione a un utente che richiede l'accesso di sola lettura alle informazioni di fatturazione della sottoscrizione, ma non la possibilità di gestire o creare i servizi di Azure. Questo ruolo è appropriato per gli utenti che in un'organizzazione sono responsabili solo della gestione finanziaria e dei costi per le sottoscrizioni di Azure.

Un cliente con Contratto Enterprise, un amministratore di reparto o un proprietario dell'account possono assegnare il ruolo di lettore della fatturazione di sola lettura ai membri del team. Affinché questo lettore della fatturazione possa visualizzare le informazioni di fatturazione del reparto o dell'account, l'amministratore dell'organizzazione deve abilitare i criteri **AO view charges** (Visualizzazione addebiti per proprietari dell'account) oppure **DA view charges** (Visualizzazione addebiti per amministratori di reparto) in Enterprise Portal.

La funzionalità Fatturazione per lettore è disponibile in anteprima e non supporta ancora i cloud non globali.

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Selezionare la sottoscrizione dal [pannello delle Sottoscrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) nel portale di Azure.

1. Selezionare **Controllo di accesso (IAM)**.
1. Selezionare **Assegnazioni di ruolo** per visualizzare tutte le assegnazioni di ruolo in questa sottoscrizione.
1. Selezionare **Aggiungi assegnazione di ruolo**.
1. Nell'elenco a discesa **Ruolo** scegliere **Lettore per la fatturazione**.
1. Nella casella di testo **Seleziona** digitare il nome o l'indirizzo di posta elettronica dell'utente da aggiungere.
1. Selezionare l'utente.
1. Selezionare **Salva**.
1. Dopo qualche istante, all'utente viene assegnato il ruolo di Lettore per la fatturazione per l'ambito della sottoscrizione.
1. Il Lettore per la fatturazione riceve un messaggio di posta elettronica con un collegamento per l'accesso.

    ![Schermata che mostra ciò che vede il lettore della fatturazione nel portale di Azure](./media/billing-manage-access/billing-reader-view.png)

## <a name="allow-department-administrator-or-account-owner-billing-access"></a>Consentire l'accesso alla fatturazione all'amministratore di reparto o al proprietario dell'account

L'amministratore dell'organizzazione può consentire degli amministratori di reparto e ai proprietari di account di visualizzare i dettagli di utilizzo e i costi associati ai reparti e agli account che gestiscono.

1. Accedere al [Portale EA](https://ea.azure.com/) con un account di amministratore aziendale.
1. Selezionare **Gestisci**.
1. In **Registrazione** modificare il **DA view charges** (Visualizzazione addebiti per amministratori di reparto) su **Abilitato** affinché l'amministratore di reparto possa visualizzare utilizzo e costi.
1. Impostare **AO view charges** (Visualizzazione addebiti per proprietari dell'account) su **Abilitato** affinché il proprietario dell'account possa visualizzare utilizzo e costi.


Per altre informazioni, vedere [Informazioni sui ruoli amministrativi per il Contratto Enterprise di Azure](billing-understand-ea-roles.md).

## <a name="only-account-admins-can-access-account-center"></a>Solo gli amministratori dell'account possono accedere al Centro account

L'amministratore dell'account è il proprietario legale della sottoscrizione. Per impostazione predefinita, la persona che ha effettuato l'accesso o ha acquistato la sottoscrizione di Azure è l'amministratore dell'account, a meno che non la [proprietà della sottoscrizione non venga trasferita](billing-subscription-transfer.md) a un altro utente. L'amministratore dell'account può creare e annullare le sottoscrizioni, modificare l'indirizzo di fatturazione per una sottoscrizione e gestire i criteri di accesso alla sottoscrizione dal [Centro account](https://account.azure.com/Subscriptions).

## <a name="next-steps"></a>Passaggi successivi

- Gli utenti in altri ruoli, ad esempio proprietario o collaboratore, possono accedere non solo alle informazioni di fatturazione, ma anche ai servizi di Azure. Per gestire questi ruoli, vedere [Gestire l'accesso usando il controllo degli accessi in base al ruolo e il portale di Azure](../role-based-access-control/role-assignments-portal.md).
- Per altre informazioni sui ruoli, vedere [Ruoli predefiniti per le risorse di Azure](../role-based-access-control/built-in-roles.md).

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
