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
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 6726c876d0895f9488aa2ae5c225a6b2ac19e69f
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491168"
---
# <a name="manage-access-to-billing-information-for-azure"></a>Gestire l'accesso alle informazioni di fatturazione per Azure

È possibile fornire ad altri utenti l'accesso alle informazioni di fatturazione per l'account nel portale di Azure. Il tipo di fatturazione di ruoli e le istruzioni per fornire l'accesso alle informazioni di fatturazione variano in base al tipo di account di fatturazione. Per determinare il tipo di account di fatturazione, vedere [controllare il tipo di account di fatturazione](#check-the-type-of-your-billing-account).

L'articolo si applica ai clienti con account programma Microsoft Online Services. Se si ha un cliente di Azure con un contratto Enterprise (EA) e l'amministratore dell'organizzazione, è possibile assegnare autorizzazioni per gli amministratori di reparto e i proprietari di Account in Enterprise portal. Per altre informazioni, vedere [Informazioni sui ruoli amministrativi per il Contratto Enterprise di Azure](billing-understand-ea-roles.md). Se sei un cliente con contratto di Microsoft dal cliente, vedere, [ruoli amministrativi comprendere contratto di Microsoft dal cliente in Azure](billing-understand-mca-roles.md). 

## <a name="account-administrators-for-microsoft-online-service-program-accounts"></a>Amministratori dell'account per gli account di programma Microsoft Online Services

Un amministratore Account è l'unico proprietario per un account di fatturazione di programma del servizio Online Microsoft. Il ruolo viene assegnato a una persona che ha effettuato l'iscrizione a Azure. Gli amministratori dell'account sono autorizzati a eseguire diverse attività di fatturazione, ad esempio creare le sottoscrizioni, visualizzare le fatture o modificare la fatturazione per una sottoscrizione.

## <a name="give-others-access-to-view-billing-information"></a>Assegnare ad altri utenti di accedere e visualizzare le informazioni di fatturazione

Account amministratore può concedere ad altri utenti l'accesso alle informazioni di fatturazione Azure assegnando i ruoli seguenti in una sottoscrizione nel proprio account.

- Amministratore del servizio
- Co-amministratore
- Proprietario
- Collaboratore
- Reader
- Fatturazione per lettore

Questi ruoli hanno accesso alle informazioni di fatturazione nel [portale di Azure](https://portal.azure.com/). Gli utenti assegnati questi ruoli possono utilizzare anche il [API di fatturazione](billing-usage-rate-card-overview.md) per ottenere a livello di codice le fatture e i dettagli di utilizzo.

Per assegnare questi ruoli, vedere [Gestire l'accesso usando il controllo degli accessi in base al ruolo e il portale di Azure](../role-based-access-control/role-assignments-portal.md).

\* * Se sei un cliente con contratto Enterprise Agreement, un proprietario dell'Account può assegnare il ruolo riportato sopra ad altri utenti del proprio team. Ma per questi utenti visualizzare le informazioni di fatturazione, l'amministratore Enterprise deve abilitare Visualizza addebiti AO in Enterprise portal.


### <a name="opt-in"></a> Consentire agli utenti di scaricare le fatture

Dopo che un amministratore Account è assegnato i ruoli appropriati ad altri utenti, deve attivare l'accesso a scaricare le fatture nel portale di Azure. Le fatture redatte prima di dicembre 2016 sono disponibili solo all'amministratore dell'account.

1. Accedi per il [portale di Azure](https://portal.azure.com/), come amministratore Account,

1. Eseguire ricerche in **Gestione dei costi e fatturazione**.

    ![Screenshot che mostra una ricerca nel portale di Azure](./media/billing-manage-access/billing-search-cost-management-billing.png)
 
1. Selezionare **sottoscrizioni** nel riquadro a sinistra. In base all'accesso, è necessario selezionare un ambito di fatturazione e quindi selezionare **sottoscrizioni**.
 
    ![Screenshot che mostra la selezione delle sottoscrizioni](./media/billing-manage-access/billing-select-subscriptions.png)

1. Selezionare **fatture** e quindi **l'accesso alla fattura**.

    ![Lo screenshot mostra come delegare l'accesso alle fatture](./media/billing-manage-access/AA-optin.png)

1. Selezionare **On** e salvare.

    ![Lo screenshot mostra le impostazioni on-off per delegare l'accesso alla fattura](./media/billing-manage-access/AA-optinAllow.png)

L'amministratore dell'account può anche impostare una configurazione in modo da ricevere le fatture tramite posta elettronica. Per altre informazioni, vedere [Ottenere la fatturazione tramite posta elettronica](billing-download-azure-invoice-daily-usage-date.md).

## <a name="give-read-only-access-to-billing"></a>Fornire l'accesso di sola lettura alla fatturazione

Assegnare il ruolo di lettore della fatturazione a un utente che richiede l'accesso di sola lettura alle informazioni di fatturazione della sottoscrizione, ma non la possibilità di gestire o creare i servizi di Azure. Questo ruolo è appropriato per gli utenti che in un'organizzazione sono responsabili solo della gestione finanziaria e dei costi per le sottoscrizioni di Azure.

La funzionalità Fatturazione per lettore è disponibile in anteprima e non supporta ancora i cloud non globali.

1. Accedi per il [portale di Azure](https://portal.azure.com/), come amministratore Account,

1. Eseguire ricerche in **Gestione dei costi e fatturazione**.

    ![Screenshot che mostra una ricerca nel portale di Azure](./media/billing-manage-access/billing-search-cost-management-billing.png)

1. Selezionare **sottoscrizioni** nel riquadro a sinistra. In base all'accesso, è necessario selezionare un ambito di fatturazione e quindi selezionare **sottoscrizioni**.
 
    ![Screenshot che mostra la selezione delle sottoscrizioni](./media/billing-manage-access/billing-select-subscriptions.png)

1. Selezionare **Controllo di accesso (IAM)** .
1. Selezionare **Add** dalla parte superiore della pagina.

    ![Screenshot che mostra facendo clic su Aggiungi assegnazione di ruolo](./media/billing-manage-access/billing-click-add-role-assignment.png)

1. Nell'elenco a discesa **Ruolo** scegliere **Lettore per la fatturazione**.
1. Nella casella di testo **Seleziona** digitare il nome o l'indirizzo di posta elettronica dell'utente da aggiungere.
1. Selezionare l'utente.
1. Selezionare **Salva**.
    ![Screenshot che mostra facendo clic su Aggiungi assegnazione di ruolo](./media/billing-manage-access/billing-save-role-assignment.png)

1. Dopo qualche istante, l'utente è assegnato il ruolo di lettore della fatturazione della sottoscrizione.

\* * Se sei un cliente con contratto Enterprise Agreement, un proprietario dell'Account o un amministratore del reparto può assegnare il ruolo di lettore della fatturazione per i membri del team. Affinché questo lettore della fatturazione possa visualizzare le informazioni di fatturazione del reparto o dell'account, l'amministratore dell'organizzazione deve abilitare i criteri **AO view charges** (Visualizzazione addebiti per proprietari dell'account) oppure **DA view charges** (Visualizzazione addebiti per amministratori di reparto) in Enterprise Portal.

## <a name="check-the-type-of-your-billing-account"></a>Controllare il tipo di account di fatturazione
[!INCLUDE [billing-check-account-type](../../includes/billing-check-account-type.md)]

## <a name="next-steps"></a>Passaggi successivi

- Gli utenti in altri ruoli, ad esempio proprietario o collaboratore, possono accedere non solo alle informazioni di fatturazione, ma anche ai servizi di Azure. Per gestire questi ruoli, vedere [Gestire l'accesso usando il controllo degli accessi in base al ruolo e il portale di Azure](../role-based-access-control/role-assignments-portal.md).
- Per altre informazioni sui ruoli, vedere [Ruoli predefiniti per le risorse di Azure](../role-based-access-control/built-in-roles.md).

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

Se si hanno domande o assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).
