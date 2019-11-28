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
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: c1f9ef78431b6b293972d4adbc0f64dc49d5c4b7
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223836"
---
# <a name="manage-access-to-billing-information-for-azure"></a>Gestire l'accesso alle informazioni di fatturazione per Azure

È possibile concedere ad altri utenti l'accesso alle informazioni di fatturazione per l'account nel portale di Azure. Il tipo di ruoli di fatturazione e le istruzioni per concedere l'accesso alle informazioni di fatturazione variano in base al tipo di account di fatturazione. Per determinare il tipo di account di fatturazione, vedere [Verificare il tipo di account di fatturazione](#check-the-type-of-your-billing-account).

L'articolo si applica ai clienti con account del programma Microsoft Online Services. I clienti di Azure con contratto Enterprise Agreement (EA) che sono amministratori dell'organizzazione possono concedere le autorizzazioni agli amministratori di reparto e ai proprietari dell'account in Enterprise Portal. Per altre informazioni, vedere [Informazioni sui ruoli amministrativi per il Contratto Enterprise di Azure](billing-understand-ea-roles.md). Se si è un cliente con Contratto del Cliente Microsoft, vedere [Informazioni sui ruoli amministrativi per il Contratto del cliente Microsoft in Azure](billing-understand-mca-roles.md).

## <a name="account-administrators-for-microsoft-online-service-program-accounts"></a>Amministratori degli account del programma Microsoft Online Services

Un amministratore account è l'unico proprietario di un account di fatturazione del programma Microsoft Online Services. Il ruolo viene assegnato a una persona che ha effettuato l'iscrizione ad Azure. Gli amministratori account sono autorizzati a eseguire varie attività di fatturazione, ad esempio creare sottoscrizioni, visualizzare le fatture o modificare la fatturazione per una sottoscrizione.

## <a name="give-others-access-to-view-billing-information"></a>Concedere ad altri utenti l'accesso per la visualizzazione delle informazioni di fatturazione

L'amministratore account può concedere ad altri utenti l'accesso alle informazioni di fatturazione di Azure assegnando uno dei ruoli seguenti in una sottoscrizione dell'account.

- Amministratore del servizio
- Co-amministratore
- Proprietario
- Collaboratore
- Reader
- Fatturazione per lettore

Questi ruoli hanno accesso alle informazioni di fatturazione nel [portale di Azure](https://portal.azure.com/). Gli utenti a cui sono assegnati tali ruoli possono anche usare le [API di fatturazione](billing-usage-rate-card-overview.md) per ottenere le fatture e i dettagli di utilizzo a livello di codice.

Per assegnare questi ruoli, vedere [Gestire l'accesso usando il controllo degli accessi in base al ruolo e il portale di Azure](../role-based-access-control/role-assignments-portal.md).

** Se si è un cliente con contratto EA, un proprietario dell'account può assegnare tale ruolo ad altri utenti del proprio team. Per consentire a questi utenti di visualizzare le informazioni di fatturazione, tuttavia, l'amministratore dell'organizzazione deve abilitare la visualizzazione degli addebiti per i proprietari dell'account in Enterprise Portal.


### <a name="opt-in"></a> Consentire agli utenti di scaricare le fatture

Dopo che un amministratore account ha assegnato i ruoli appropriati ad altri utenti, è necessario attivare l'accesso per il download delle fatture nel portale di Azure. Le fatture redatte prima di dicembre 2016 sono disponibili solo all'amministratore dell'account.

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore account.

1. Eseguire ricerche in **Gestione dei costi e fatturazione**.

    ![Screenshot che mostra una ricerca nel portale di Azure](./media/billing-manage-access/billing-search-cost-management-billing.png)

1. Selezionare **Sottoscrizioni** nel riquadro a sinistra. A seconda dell'accesso, potrebbe essere necessario selezionare un ambito di fatturazione e quindi **Sottoscrizioni**.

    ![Screenshot che mostra la selezione di sottoscrizioni](./media/billing-manage-access/billing-select-subscriptions.png)

1. Selezionare **Fatture** e quindi **Accesso alla fattura**.

    ![Lo screenshot mostra come delegare l'accesso alle fatture](./media/billing-manage-access/AA-optin.png)

1. Selezionare **On** e salvare.

    ![Lo screenshot mostra le impostazioni on-off per delegare l'accesso alla fattura](./media/billing-manage-access/AA-optinAllow.png)

L'amministratore dell'account può anche impostare una configurazione in modo da ricevere le fatture tramite posta elettronica. Per altre informazioni, vedere [Ottenere la fatturazione tramite posta elettronica](billing-download-azure-invoice-daily-usage-date.md).

## <a name="give-read-only-access-to-billing"></a>Fornire l'accesso di sola lettura alla fatturazione

Assegnare il ruolo di lettore della fatturazione a un utente che richiede l'accesso di sola lettura alle informazioni di fatturazione della sottoscrizione, ma non la possibilità di gestire o creare i servizi di Azure. Questo ruolo è appropriato per gli utenti che in un'organizzazione sono responsabili solo della gestione finanziaria e dei costi per le sottoscrizioni di Azure.

La funzionalità Fatturazione per lettore è disponibile in anteprima e non supporta ancora i cloud non globali.

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore account.

1. Eseguire ricerche in **Gestione dei costi e fatturazione**.

    ![Screenshot che mostra una ricerca nel portale di Azure](./media/billing-manage-access/billing-search-cost-management-billing.png)

1. Selezionare **Sottoscrizioni** nel riquadro a sinistra. A seconda dell'accesso, potrebbe essere necessario selezionare un ambito di fatturazione e quindi **Sottoscrizioni**.

    ![Screenshot che mostra la selezione di sottoscrizioni](./media/billing-manage-access/billing-select-subscriptions.png)

1. Selezionare **Controllo di accesso (IAM)** .
1. Selezionare **Aggiungi** nella parte superiore della pagina.

    ![Screenshot che mostra dove fare clic per aggiungere un'assegnazione di ruolo](./media/billing-manage-access/billing-click-add-role-assignment.png)

1. Nell'elenco a discesa **Ruolo** scegliere **Lettore per la fatturazione**.
1. Nella casella di testo **Seleziona** digitare il nome o l'indirizzo di posta elettronica dell'utente da aggiungere.
1. Selezionare l'utente.
1. Selezionare **Salva**.
    ![Screenshot che mostra dove fare clic per aggiungere un'assegnazione di ruolo](./media/billing-manage-access/billing-save-role-assignment.png)

1. Dopo qualche istante, all'utente viene assegnato il ruolo Fatturazione per lettore per la sottoscrizione.

** Un cliente con contratto EA, un proprietario dell'account o un amministratore di reparto può assegnare il ruolo Fatturazione per lettore ai membri del team. Affinché questo lettore della fatturazione possa visualizzare le informazioni di fatturazione del reparto o dell'account, l'amministratore dell'organizzazione deve abilitare i criteri **AO view charges** (Visualizzazione addebiti per proprietari dell'account) oppure **DA view charges** (Visualizzazione addebiti per amministratori di reparto) in Enterprise Portal.

## <a name="check-the-type-of-your-billing-account"></a>Verificare il tipo di account di fatturazione
[!INCLUDE [billing-check-account-type](../../includes/billing-check-account-type.md)]

## <a name="next-steps"></a>Passaggi successivi

- Gli utenti in altri ruoli, ad esempio proprietario o collaboratore, possono accedere non solo alle informazioni di fatturazione, ma anche ai servizi di Azure. Per gestire questi ruoli, vedere [Gestire l'accesso usando il controllo degli accessi in base al ruolo e il portale di Azure](../role-based-access-control/role-assignments-portal.md).
- Per altre informazioni sui ruoli, vedere [Ruoli predefiniti per le risorse di Azure](../role-based-access-control/built-in-roles.md).

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).
