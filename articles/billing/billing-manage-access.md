---
title: Gestire l'accesso alla fatturazione di Azure tramite i ruoli | Microsoft Docs
description: 
services: 
documentationcenter: 
author: vikramdesai01
manager: vikdesai
editor: 
tags: billing
ms.assetid: e4c4d136-2826-4938-868f-a7e67ff6b025
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: vikdesai
ms.openlocfilehash: c70904097f139bc2178feed83f1cf1274f3c738d
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="manage-access-to-billing-information-for-azure-using-role-based-access-control"></a>Gestire l'accesso alle informazioni di fatturazione per Azure tramite il controllo di accesso basato sui ruoli

È possibile concedere l'accesso alle informazioni di fatturazione di Azure ai membri del team assegnando uno dei ruoli utente seguenti alla sottoscrizione: amministratore dell'account, amministratore del servizio, coamministratore, proprietario, collaboratore, lettore e lettore della fatturazione. Questi hanno accesso alle informazioni di fatturazione nel [portale di Azure](https://portal.azure.com/) e possono usare le [API di fatturazione](billing-usage-rate-card-overview.md) per ottenere le fatture a livello di programmazione, dopo aver scelto questa modalità, e i dettagli di uso. Per altre informazioni su chi può concedere ruoli e i compiti per ogni ruolo, vedere [Ruoli in Controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md).

## <a name="opt-in"></a> Consentire ad altri utenti di accedere alle fatture

L'amministratore dell'account deve concedere il consenso esplicito tramite il [portale di Azure](https://portal.azure.com/) che concede ad altri utenti di accedere alle fatture e tramite le API.

1. In qualità di amministratore dell'account, selezionare la sottoscrizione dal [pannello Sottoscrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) nel portale di Azure.

1. Selezionare **Fatture** e quindi **Access to invoices** (Accesso alle fatture).

    ![Lo screenshot mostra come delegare l'accesso alle fatture](./media/billing-manage-access/AA-optin.png)

1. Impostare su **On** l'accesso, quindi salvare le modifiche per consentire agli utenti nei ruoli con ambito della sottoscrizione di scaricare la fattura.

    ![Lo screenshot mostra le impostazioni on-off per delegare l'accesso alla fattura](./media/billing-manage-access/AA-optinAllow.png)

Il consenso esplicito permette all'amministratore del servizio, al coamministratore, al proprietario, al collaboratore, al lettore e al lettore della fatturazione nella sottoscrizione di scaricare le fatture PDF nel portale di Azure. Tuttavia, le fatture redatte prima di dicembre 2016 sono disponibili solo per l'amministratore dell'account attualmente.

L'amministratore dell'account può anche impostare una configurazione in modo da ricevere le fatture tramite posta elettronica. Per altre informazioni, vedere [Ottenere la fatturazione tramite posta elettronica](billing-download-azure-invoice-daily-usage-date.md).

## <a name="adding-users-to-the-billing-reader-role"></a>Aggiunta di utenti al ruolo di lettore della fatturazione

Il ruolo di lettore della fatturazione dispone di accesso in sola lettura alle informazioni di fatturazione della sottoscrizione nel portale di Azure e nessun accesso ai servizi, ad esempio macchine virtuali e account di archiviazione. Assegnare il ruolo di lettore della fatturazione a un utente che richiede l'accesso alle informazioni di fatturazione della sottoscrizione, ma non la possibilità di gestire i servizi di Azure. Questo ruolo è appropriato per gli utenti che in un'organizzazione eseguono solo la gestione finanziaria e dei costi per le sottoscrizioni di Azure.

1. Selezionare la sottoscrizione dal [pannello delle Sottoscrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) nel portale di Azure.

1. Selezionare **Controllo di accesso (IAM)** e quindi fare clic su **Aggiungi**.

    ![Schermate che mostrano l'IAM nel pannello della sottoscrizione](./media/billing-manage-access/select-iam.PNG)

1. Scegliere **Billing Reader** (Lettore della fatturazione) nella pagina **Selezionare un ruolo**.

    ![Schermate che mostrano il lettore della fatturazione nella visualizzazione del popup](./media/billing-manage-access/select-roles.PNG)

1. Digitare il messaggio di posta elettronica per l'utente che si desidera invitare, quindi fare clic su **OK** per inviare l'invito.

    ![Schermata che mostra come immettere il messaggio di posta elettronica per invitare un utente](./media/billing-manage-access/add-user.PNG)

1. Seguire le istruzioni nel messaggio di posta elettronica di invito per accedere come un lettore della fatturazione.

    ![Schermata che mostra ciò che vede il lettore della fatturazione nel portale di Azure](./media/billing-manage-access/billing-reader-view.png)

> [!NOTE]
> La funzionalità Fatturazione per lettore è disponibile in anteprima e non supporta ancora le sottoscrizioni enterprise (EA) o i cloud non globali.

## <a name="adding-users-to-other-roles"></a>Aggiunta di utenti ad altri ruoli

Gli utenti in altri ruoli, ad esempio proprietario o collaboratore, possono accedere non solo alle informazioni di fatturazione, ma anche ai servizi di Azure. Per gestire questi ruoli, vedere [Aggiungere o modificare i ruoli di amministratore di Azure che gestiscono la sottoscrizione o i servizi](billing-add-change-azure-subscription-administrator.md).

## <a name="who-can-access-the-account-centerhttpsaccountwindowsazurecom"></a>Chi può accedere al [Centro account](https://account.windowsazure.com)?

Solo l'amministratore dell'account può accedere al Centro account. L'amministratore dell'account è il proprietario legale della sottoscrizione. Per impostazione predefinita, la persona che ha effettuato l'accesso o ha acquistato la sottoscrizione di Azure è l'amministratore dell'account, a meno che non la [proprietà della sottoscrizione non venga trasferita](billing-subscription-transfer.md) a un altro utente. L'amministratore dell'account può creare e annullare le sottoscrizioni, modificare l'indirizzo di fatturazione per una sottoscrizione e gestire i criteri di accesso alla sottoscrizione.

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico.

Per altre domande, è possibile [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.
