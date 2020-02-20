---
title: Visualizzare e scaricare la fattura di Microsoft Azure
description: Descrive come visualizzare e scaricare la fattura di Microsoft Azure.
keywords: fatturazione, scaricare la fatture, fattura di Azure, uso di Azure
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 691d27acebf238e84265870e8c01976bfc2412b2
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200266"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>Visualizzare e scaricare la fattura di Microsoft Azure

Per la maggior parte delle sottoscrizioni, è possibile scaricare la fattura dal [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) o richiedere l'invio tramite posta elettronica. I clienti di Azure con un contratto Enterprise (clienti EA) non possono scaricare le fatture della loro organizzazione. Le fatture vengono inviate a chiunque sia configurato per la ricezione delle fatture per la registrazione.

Solo determinati ruoli dispongono delle autorizzazioni per visualizzare le fatture. Ad esempio, l'amministratore dell'account o l'amministratore dell'organizzazione. Per altre informazioni sull'accesso alle informazioni di fatturazione, vedere [Gestire l'accesso alla fatturazione di Azure usando i ruoli](../manage/manage-billing-access.md).

Se si ha un Contratto del cliente Microsoft, è necessario disporre di uno dei ruoli seguenti per ricevere le fatture:

- Proprietario del profilo di fatturazione
- Collaboratore
- Reader
- Gestione fatture

Per i clienti con Contratto Microsoft Partner, è necessario disporre del ruolo di amministratore globale e agente amministratore nell'organizzazione partner per visualizzare e scaricare le fatture di Azure. [Controllare il tipo di account di fatturazione ](#check-your-billing-account-type) per verificare le autorizzazioni necessarie.

<!-- For more information about billing roles for Microsoft Customer Agreements, see [Billing profile roles and tasks](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks). -->

## <a name="noinvoice"></a> Perché si potrebbe non ricevere una fattura

Potrebbero esserci diversi motivi per cui non è visualizzata alcuna fattura:

- Sono passati meno di trenta giorni dalla data della sottoscrizione di Azure.

- Azure fattura alla fine del periodo di fatturazione. È possibile quindi che una fattura non sia stata ancora generata. Attendere la fine del periodo di fatturazione.

- Non si è autorizzati a visualizzare le fatture. Per i clienti con Contratto del cliente Microsoft o Contratto Microsoft Partner, è necessario avere il ruolo di proprietario, collaboratore, lettore o gestione fatture per il profilo di fatturazione. Per le altre sottoscrizioni, è possibile che non vengano visualizzate le fatture precedenti se non si è l'amministratore dell'account. Per altre informazioni sull'accesso alle informazioni di fatturazione, vedere [Manage access to Azure billing using roles](../manage/manage-billing-access.md) (Gestire l'accesso alla fatturazione di Azure usando i ruoli).

- Se si dispone di una versione di valutazione gratuita o di un importo del credito mensile con la sottoscrizione, si riceve una fattura solo quando si supera l'importo del credito mensile. Se si dispone di un Contratto del cliente Microsoft o Contratto Microsoft Partner, si riceverà sempre una fattura.

## <a name="download-invoices-in-the-azure-portal"></a>Scaricare le fatture dal portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Cercare *Gestione dei costi e fatturazione*.
1. A seconda dell'accesso, potrebbe essere necessario selezionare un account di fatturazione o un profilo di fatturazione.
1. Nel menu a sinistra selezionare **Fatture** in **Fatturazione**.
1. Nella griglia della fattura trovare la riga della fattura da scaricare.
1. Fare clic sull'icona del download o sui puntini di sospensione (`...`) alla fine della riga.
1. Selezionare **Fattura** nel menu di download.

Per altre informazioni sulla fattura, vedere [Comprendere la fattura per Microsoft Azure](review-individual-bill.md). Per informazioni sulla gestione dei costi, vedere [Evitare costi imprevisti con la gestione dei costi e la fatturazione di Azure](../manage/getting-started.md).

## <a name="get-your-subscriptions-invoices-in-email"></a>Ricevere le fatture della sottoscrizione tramite posta elettronica

Per ricevere una fattura di Azure tramite posta elettronica, è possibile fornire il consenso esplicito e configurare altri destinatari. Questa funzionalità potrebbe non essere disponibile per alcune sottoscrizioni, ad esempio offerte di supporto, contratti Enterprise o Azure in Open.

1. Selezionare la sottoscrizione nella [pagina delle sottoscrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Dare il consenso esplicito per ogni sottoscrizione posseduta. Fare clic su **Invoices** (Fatture) quindi su **Email my invoice** (Invia fattura tramite posta elettronica).

    ![Schermata che mostra il flusso per il consenso esplicito](./media/download-azure-invoice/invoicesdeeplink01.png)

2. Fare clic su **Acconsenti esplicitamente** e accettare i termini.

    ![Schermata che illustra il passaggio 2 del flusso per il consenso esplicito](./media/download-azure-invoice/invoicearticlestep02.png)

3. Dopo l'accettazione del contratto, sarà possibile configurare altri destinatari. Dopo che un destinatario è stato rimosso, l'indirizzo di posta elettronica non è più archiviato. Se si cambia idea, è necessario aggiungerlo di nuovo.

    ![Schermata che illustra il passaggio 3 del flusso per il consenso esplicito](./media/download-azure-invoice/invoicearticlestep03.png)

Se non si riceve un'email dopo aver eseguito i passaggi seguenti, assicurarsi che l'indirizzo email sia corretto nelle [preferenze di comunicazione del proprio profilo](https://account.windowsazure.com/profile).

## <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Rifiutare esplicitamente di ricevere le fatture della sottoscrizione tramite posta elettronica

Per rifiutare esplicitamente di ricevere la fattura tramite posta elettronica, seguire i passaggi precedenti e fare clic su **Rifiuta esplicitamente le fatture tramite posta elettronica**. Questa opzione rimuove tutti gli indirizzi di posta elettronica impostati per la ricezione di fatture tramite posta elettronica. Se si acconsente esplicitamente di nuovo, sarà necessario riconfigurare i destinatari.

 ![Schermata che illustra il flusso del rifiuto esplicito](./media/download-azure-invoice/invoicearticlestep04.png)

<!-- Does following section apply to MPA too? -->
## <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>Ricevere le fatture del Contratto del cliente Microsoft tramite posta elettronica

Se si ha un account di fatturazione per il Contratto del cliente Microsoft, è possibile acconsentire esplicitamente a ricevere la fattura tramite posta elettronica. Tutti gli utenti con ruolo proprietario, collaboratore, lettore o responsabile delle fatture in un profilo di fatturazione riceveranno la fattura tramite posta elettronica.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Cercare **Gestione dei costi e fatturazione**.

   ![Screenshot che mostra la ricerca di Sottoscrizioni nel portale](./media/download-azure-invoice/search-cmb.png)

1. Selezionare **Profili di fatturazione** sul lato sinistro. Nell'elenco dei profili di fatturazione selezionare un profilo di fatturazione per ricevere le fatture corrispondenti tramite posta elettronica.

   [![Screenshot che visualizza l'elenco dei profili di fatturazione](./media/download-azure-invoice/mca-select-profile.png)](./media/download-azure-invoice/mca-select-profile-zoomed-in.png#lightbox)

1. Selezionare **Proprietà** sul lato sinistro, quindi selezionare **Aggiorna le preferenze di fatturazione tramite posta elettronica**.

   [![Screenshot che visualizza l'elenco dei profili di fatturazione](./media/download-azure-invoice/mca-select-update-email-preferences.png)](./media/download-azure-invoice/mca-select-update-email-preferences.png#lightbox)

1. Selezionare **Acconsenti esplicitamente** e quindi **Aggiorna**.

   [![Screenshot che visualizza l'elenco dei profili di fatturazione](./media/download-azure-invoice/mca-select-email-opt-in.png)](./media/download-azure-invoice/mca-select-email-opt-in.png#lightbox)

## <a name="opt-out-of-getting-your-microsoft-customer-agreement-invoices-in-email"></a>Rifiutare esplicitamente di ricevere le fatture del Contratto del cliente Microsoft tramite posta elettronica

Per rifiutare esplicitamente di ricevere la fattura tramite posta elettronica, seguire i passaggi precedenti e fare clic su **Rifiuta esplicitamente**. Tutti gli utenti con ruolo proprietario, collaboratore, lettore o responsabile delle fatture in un profilo di fatturazione vengono esclusi dalla ricezione della fattura tramite posta elettronica.

## <a name="give-others-access-to-your-microsoft-customer-agreement-invoices"></a>Consentire ad altri utenti l'accesso alle fatture del Contratto del cliente Microsoft

È possibile concedere ad altri utenti l'accesso per la visualizzazione, il download e il pagamento delle fatture, assegnando a tali utenti il ruolo di gestione fatture per un profilo di fatturazione. Se si è acconsentito esplicitamente ad ottenere la fattura tramite posta elettronica, anche questi utenti ricevono le fatture tramite posta elettronica.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Cercare **Gestione dei costi e fatturazione**.

   ![Screenshot che mostra la ricerca di Sottoscrizioni nel portale](./media/download-azure-invoice/search-cmb.png)

1. Selezionare **Profili di fatturazione** sul lato sinistro. Nell'elenco dei profili di fatturazione selezionare il profilo di fatturazione al quale si vuole assegnare un ruolo di gestione fatture.

   [![Screenshot che visualizza l'elenco dei profili di fatturazione](./media/download-azure-invoice/mca-select-profile.png)](./media/download-azure-invoice/mca-select-profile-zoomed-in.png#lightbox)

1. Selezionare **Controllo di accesso (IAM)** sul lato sinistro e quindi selezionare **Aggiungi** nella parte superiore della pagina.

   [![Screenshot che visualizza la pagina Controllo di accesso](./media/download-azure-invoice/mca-select-access-control.png)](./media/download-azure-invoice/mca-select-access-control-zoomed-in.png#lightbox)

1. Nell'elenco a discesa Ruolo selezionare **Gestione fatture**. Immettere l'indirizzo di posta elettronica dell'utente a cui si vuole concedere l'accesso. Selezionare **Salva** per assegnare il ruolo.

   [![Screenshot che visualizza l'aggiunta di un utente con il ruolo Gestione fatture](./media/download-azure-invoice/mca-added-invoice-manager.png)](./media/download-azure-invoice/mca-added-invoice-manager.png#lightbox)

## <a name="check-your-billing-account-type"></a>Controllare il tipo di account di fatturazione
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla fattura e sugli addebiti, vedere:

- [Visualizzare e scaricare l'utilizzo e gli addebiti di Microsoft Azure](download-azure-daily-usage.md)
- [Comprendere la fattura per Microsoft Azure](review-individual-bill.md)
- [Informazioni sui termini della fattura di Azure](understand-invoice.md)
- [Comprendere i termini sui dati di utilizzo dettagliato di Microsoft Azure](understand-usage.md)
- [Visualizzare i prezzi di Azure dell'organizzazione](../manage/ea-pricing.md)

Se si ha un Contratto del cliente Microsoft, vedere:

- [Informazioni sugli addebiti nella fattura del profilo di fatturazione](review-customer-agreement-bill.md)
- [Informazioni sui termini nella fattura per il profilo di fatturazione](mca-understand-your-invoice.md)
- [Informazioni sul file dei dati di utilizzo e addebiti di Azure per il profilo di fatturazione](mca-understand-your-usage.md)
- [Visualizzare e scaricare i documenti fiscali per il profilo di fatturazione](mca-download-tax-document.md)
- [Visualizzare i prezzi di Azure dell'organizzazione](../manage/ea-pricing.md)
