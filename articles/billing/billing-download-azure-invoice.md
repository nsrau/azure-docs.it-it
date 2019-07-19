---
title: Visualizzare e scaricare la fattura di Microsoft Azure
description: Viene descritto come visualizzare e scaricare la fattura Microsoft Azure.
keywords: fatturazione, scaricare la fatture, fattura di Azure, uso di Azure
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: banders
ms.openlocfilehash: 4dc5f88f7a5994e5bcd50d71c86bf1ba35b10734
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68321775"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>Visualizzare e scaricare la fattura di Microsoft Azure

Per la maggior parte delle sottoscrizioni, è possibile scaricare la fattura dal [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) o richiedere l'invio tramite posta elettronica. I clienti di Azure con un contratto Enterprise (clienti EA) non possono scaricare le fatture della loro organizzazione. Le fatture vengono inviate a chiunque sia configurato per la ricezione delle fatture per la registrazione.

Solo determinati ruoli dispongono delle autorizzazioni per visualizzare le fatture. Ad esempio, l'amministratore dell'account o l'amministratore dell'organizzazione. Per altre informazioni su come ottenere l'accesso alle informazioni di fatturazione, vedere [gestire l'accesso alla fatturazione di Azure tramite i ruoli](billing-manage-access.md).

Se si dispone di un contratto per i [clienti Microsoft](#check-your-access-to-a-microsoft-customer-agreement), è necessario disporre di uno dei ruoli seguenti per ottenere le fatture:

- Proprietario del profilo di fatturazione
- Collaboratore
- Reader
- Gestione fattura

Per ulteriori informazioni sui ruoli di fatturazione per i contratti dei clienti Microsoft, vedere [attività e ruoli del profilo di fatturazione](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="noinvoice"></a>Perché non è possibile ottenere una fattura

Potrebbero esserci diversi motivi per cui non è visualizzata alcuna fattura:

- Sono passati meno di trenta giorni dalla data della sottoscrizione di Azure.

- Azure fattura alla fine del periodo di fatturazione. Una fattura potrebbe quindi non essere ancora stata generata. Attendere la fine del periodo di fatturazione.

- Non si è autorizzati a visualizzare le fatture. Se si dispone di un contratto per i clienti Microsoft, è necessario essere proprietari del profilo di fatturazione, collaboratore, lettore o responsabile delle fatture. Per le altre sottoscrizioni, è possibile che non vengano visualizzate le fatture precedenti se non si è l'amministratore dell'account. Per altre informazioni sull'accesso alle informazioni di fatturazione, vedere [Manage access to Azure billing using roles](billing-manage-access.md) (Gestire l'accesso alla fatturazione di Azure usando i ruoli).

- Se si dispone di una versione di valutazione gratuita o di un importo di credito mensile con la sottoscrizione, si ottiene una fattura solo quando si supera la quantità di credito mensile. Se si dispone di un contratto per i clienti Microsoft, viene sempre ricevuta una fattura.

## <a name="download-your-azure-invoices-pdf"></a>Scaricare le fatture di Azure (PDF)

Per la maggior parte delle sottoscrizioni, è possibile scaricare la fattura dal portale di Azure. Se si dispone di un contratto per i clienti Microsoft, vedere [scaricare le fatture per un contratto per i clienti Microsoft](#download-invoices-for-a-microsoft-customer-agreement).

### <a name="download-invoices-for-an-individual-subscription"></a>Scaricare le fatture per una singola sottoscrizione

1. Selezionare la sottoscrizione nella [pagina Sottoscrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) del portale di Azure come [utente con accesso alle fatture](billing-manage-access.md).

2. Selezionare **Fatture**.

    ![Schermata che mostra l'opzione Fatturazione e utilizzo](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png)

3. Fare clic su **Scarica fattura** per visualizzare una copia della fattura in formato PDF. Se l'indicazione è **Non disponibile**, vedere [Perché non viene visualizzata una fattura per l'ultimo periodo di fatturazione?](#noinvoice)

    ![Schermata che mostra i periodi di fatturazione, l'opzione per il download e gli addebiti totali per ogni periodo di fatturazione](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. Per visualizzare l'uso giornaliero, fare clic sul periodo di fatturazione.

Per altre informazioni sulla fattura, vedere [Comprendere la fattura per Microsoft Azure](billing-understand-your-bill.md). Per informazioni sulla gestione dei costi, vedere [evitare costi imprevisti con la gestione dei costi e la fatturazione di Azure](billing-getting-started.md).

### <a name="download-invoices-for-a-microsoft-customer-agreement"></a>Scarica le fatture per un contratto del cliente Microsoft

Le fatture vengono generate per ogni [profilo di fatturazione](billing-mca-overview.md#billing-profiles) nel contratto del cliente Microsoft. Per scaricare le fatture dalla portale di Azure, è necessario essere proprietario del profilo di fatturazione, collaboratore, lettore o responsabile delle fatture.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Cercare *Gestione costi e fatturazione*.
1. Selezionare un profilo di fatturazione. A seconda dell'accesso, potrebbe essere necessario selezionare prima un account di fatturazione.
1. Selezionare **Fatture**.
1. Nella griglia fattura individuare la riga della fattura che si desidera scaricare.
1. Fare clic sui puntini di sospensione (`...`) alla fine della riga.
    ![Screenshot che mostra i puntini di sospensione alla fine della riga](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)
1. Nel menu di scelta rapida download selezionare **fattura**.

    ![Screenshot che mostra il menu di scelta rapida](./media/billing-download-azure-invoice/contextmenu.png)

Se non viene visualizzata una fattura per l'ultimo periodo di fatturazione, vedere [perché non viene visualizzata una fattura per l'ultimo periodo di fatturazione?](#noinvoice)

## <a name="get-your-invoice-in-email-pdf"></a>Ottenere la fattura tramite posta elettronica (formato PDF)

Per ricevere una fattura di Azure tramite posta elettronica, è possibile fornire il consenso esplicito e configurare altri destinatari. Questa funzionalità potrebbe non essere disponibile per alcune sottoscrizioni, ad esempio offerte di supporto, contratti Enterprise o Azure in Open. Se si dispone di un contratto per i clienti Microsoft, vedere la sezione successiva, [ottenere le fatture del profilo di fatturazione nei messaggi di posta elettronica](#get-your-subscriptions-invoices-in-email).

### <a name="get-your-subscriptions-invoices-in-email"></a>Ricevi le fatture della tua sottoscrizione tramite posta elettronica

1. Selezionare la sottoscrizione nella [pagina Sottoscrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Dare il consenso esplicito per ogni sottoscrizione posseduta. Fare clic su **Invoices** (Fatture) quindi su **Email my invoice** (Invia fattura tramite posta elettronica).

    ![Schermata che mostra il flusso per il consenso esplicito](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)

2. Fare clic su **Acconsenti esplicitamente** e accettare i termini.

    ![Schermata che illustra il passaggio 2 del flusso per il consenso esplicito](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)

3. Dopo aver accettato il contratto, è possibile configurare altri destinatari. Dopo che un destinatario è stato rimosso, l'indirizzo di posta elettronica non è più archiviato. Se si cambia idea, è necessario leggerli.

    ![Schermata che illustra il passaggio 3 del flusso per il consenso esplicito](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)

Se non si riceve un'email dopo aver eseguito i passaggi seguenti, assicurarsi che l'indirizzo email sia corretto nelle [preferenze di comunicazione del proprio profilo](https://account.windowsazure.com/profile).

### <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Rifiutare esplicitamente di ricevere le fatture della sottoscrizione nel messaggio di posta elettronica

Per rifiutare esplicitamente di ottenere la fattura tramite posta elettronica, seguire i passaggi precedenti e fare clic su **rifiutare esplicitamente le fatture tramite posta elettronica**. Questa opzione rimuove tutti gli indirizzi di posta elettronica impostati per la ricezione di fatture tramite posta elettronica. È possibile riconfigurare i destinatari se si sceglie di nuovo.

 ![Schermata che illustra il flusso del rifiuto esplicito](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

### <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>Ricevi le fatture del contratto clienti Microsoft nella posta elettronica

Se si dispone di un contratto per i clienti Microsoft, è possibile acconsentire esplicitamente a ricevere la fattura in un messaggio di posta elettronica. Tutti i proprietari del profilo di fatturazione, i collaboratori, i lettori e i responsabili delle fatture otterranno la fattura tramite posta elettronica. I lettori non possono aggiornare la preferenza per la fattura tramite posta elettronica.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Cercare **Gestione costi e fatturazione**.
1. Selezionare un profilo di fatturazione. A seconda dell'accesso, potrebbe essere necessario selezionare prima un account di fatturazione.
1. In **Impostazioni** selezionare **Proprietà**.
1. In **fattura e-mail**selezionare **Aggiorna preferenza di fatturazione posta elettronica**.

    ![Screenshot che mostra le proprietà della fattura della posta elettronica](./media/billing-download-azure-invoice/billingprofile-email.png)

1. Selezionare **acconsenti esplicitamente**.
1. Fare clic su **Aggiorna**.

### <a name="opt-out-of-getting-your-microsoft-customer-agreement-invoices-in-email"></a>Rifiutare esplicitamente di ricevere le fatture del contratto clienti Microsoft nella posta elettronica

Per rifiutare esplicitamente di ottenere la fattura tramite posta elettronica, seguire i passaggi precedenti e fare clic su **rifiuta esplicitamente**. Anche tutti i proprietari, i collaboratori, i lettori e i responsabili delle fatture non ricevono la fattura tramite posta elettronica. Se si è un lettore, non è possibile modificare la preferenza per la fattura tramite posta elettronica.



## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Controllare l'accesso a un contratto per i clienti Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla fattura e sugli addebiti, vedere:

- [Visualizza e Scarica i Microsoft Azure utilizzo e gli addebiti](billing-download-azure-daily-usage.md)
- [Comprendere la fattura per Microsoft Azure](billing-understand-your-bill.md)
- [Informazioni sui termini della fattura di Azure](billing-understand-your-invoice.md)
- [Comprendere i termini sui dati di utilizzo dettagliato di Microsoft Azure](billing-understand-your-usage.md)
- [Visualizza i prezzi di Azure dell'organizzazione](billing-ea-pricing.md)

Se si dispone di un contratto per i clienti Microsoft, vedere:

- [Informazioni sugli addebiti per la fattura del profilo di fatturazione](billing-mca-understand-your-bill.md)
- [Comprendere i termini della fattura per il profilo di fatturazione](billing-mca-understand-your-invoice.md)
- [Informazioni sul file di addebito e sull'utilizzo di Azure per il profilo di fatturazione](billing-mca-understand-your-usage.md)
- [Visualizzare e scaricare i documenti fiscali per il profilo di fatturazione](billing-mca-download-tax-document.md)
- [Visualizza i prezzi di Azure dell'organizzazione](billing-ea-pricing.md)
