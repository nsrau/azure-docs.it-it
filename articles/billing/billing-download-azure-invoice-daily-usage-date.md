---
title: Scaricare la fattura e i dati di uso giornalieri di Azure | Microsoft Docs
description: L'articolo descrive come scaricare o visualizzare la fatturazione e dati di uso giornalieri di Azure.
keywords: fatturazione, scaricare la fatture, fattura di Azure, uso di Azure
services: billing
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: 16b2eaef74a7aa0e3e28bfcbb6dbd9da568db6cf
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "67491413"
---
# <a name="download-or-view-your-azure-billing-invoice-and-daily-usage-data"></a>Scaricare o visualizzare la fattura e i dati di uso giornalieri di Azure

Per la maggior parte delle sottoscrizioni, è possibile scaricare la fattura dal [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) o richiedere l'invio tramite posta elettronica. I clienti di Azure con un contratto Enterprise (clienti EA) non possono scaricare le fatture della loro organizzazione. Le fatture vengono inviate a chiunque sia configurato per la ricezione delle fatture per la registrazione.

Se si è un cliente EA o si ha un [Contratto del cliente Microsoft](#check-access-to-a-microsoft-customer-agreement), è possibile scaricare i dati di utilizzo nel [portale di Azure](https://portal.azure.com/). Per altre sottoscrizioni, visitare il [Centro account di Azure](https://account.azure.com/Subscriptions) per scaricare tale file.

Solo determinati ruoli sono autorizzati a ottenere informazioni sull'utilizzo e sulla fatturazione, ad esempio l'amministratore account o l'amministratore dell'organizzazione. Per altre informazioni sull'accesso alle informazioni di fatturazione, vedere [Manage access to Azure billing using roles](billing-manage-access.md) (Gestire l'accesso alla fatturazione di Azure usando i ruoli).

Per visualizzare le informazioni sull'utilizzo e la fatturazione, un cliente con il Contratto del cliente Microsoft deve avere il ruolo di proprietario, collaboratore, lettore o gestione fatture del profilo di fatturazione. Per altre informazioni sui ruoli di fatturazione per i Contratti del cliente Microsoft, vedere [Ruoli e attività del profilo di fatturazione](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="download-your-azure-invoices-pdf"></a>Scaricare le fatture di Azure (PDF)

Per la maggior parte delle sottoscrizioni è possibile scaricare la fattura dal portale di Azure. Se si ha un Contratto del cliente Microsoft, vedere Scaricare le fatture per un profilo di fatturazione.

### <a name="download-invoices-for-an-individual-subscription"></a>Scaricare le fatture per una singola sottoscrizione

1. Selezionare la sottoscrizione nella [pagina Sottoscrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) del portale di Azure come [utente con accesso alle fatture](billing-manage-access.md).

2. Selezionare **Fatture**.

    ![Schermata che mostra l'opzione Fatturazione e utilizzo](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png)

3. Fare clic su **Scarica fattura** per visualizzare una copia della fattura in formato PDF. Se l'indicazione è **Non disponibile**, vedere [Perché non viene visualizzata una fattura per l'ultimo periodo di fatturazione?](#noinvoice)

    ![Schermata che mostra i periodi di fatturazione, l'opzione per il download e gli addebiti totali per ogni periodo di fatturazione](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. Per visualizzare l'uso giornaliero, fare clic sul periodo di fatturazione.

Per altre informazioni sulla fattura, vedere [Comprendere la fattura per Microsoft Azure](billing-understand-your-bill.md). Per informazioni sulla gestione dei costi, vedere [Evitare costi imprevisti con la gestione dei costi e la fatturazione di Azure](billing-getting-started.md).

### <a name="download-invoices-for-a-microsoft-customer-agreement"></a>Scaricare le fatture per un Contratto del cliente Microsoft

Le fatture vengono generate per ogni [profilo di fatturazione](billing-mca-overview.md#billing-profiles) nel Contratto del cliente Microsoft. Per scaricare le fatture dal portale di Azure, è necessario avere il ruolo di proprietario, collaboratore, lettore o gestione fatture del profilo di fatturazione.

1. Cercare **Gestione dei costi e fatturazione**.
2. Selezionare un profilo di fatturazione.
3. Selezionare **Fatture**.
4. Nella griglia della fattura trovare la riga della fattura da scaricare.
5. Fare clic sui puntini di sospensione (`...`) alla fine della riga.
6. Nel menu di scelta rapida Download selezionare **Fattura**.

Se non viene visualizzata alcuna fattura per l'ultimo periodo di fatturazione, vedere **Informazioni aggiuntive**. <!-- Fix this -->
### <a name="noinvoice"></a> Perché non viene visualizzata una fattura per l'ultimo periodo di fatturazione?

Potrebbero esserci diversi motivi per cui non è visualizzata alcuna fattura:

- Sono passati meno di trenta giorni dalla data della sottoscrizione di Azure.

- La fattura non è stata ancora generata. Attendere la fine del periodo di fatturazione.

- Non si è autorizzati a visualizzare le fatture. Se si ha un Contratto del cliente a Microsoft, è necessario avere il ruolo di proprietario, collaboratore, lettore o gestione fatture del profilo di fatturazione. Per le altre sottoscrizioni, è possibile che non vengano visualizzate le fatture precedenti se non si è l'amministratore dell'account. Per altre informazioni sull'accesso alle informazioni di fatturazione, vedere [Manage access to Azure billing using roles](billing-manage-access.md) (Gestire l'accesso alla fatturazione di Azure usando i ruoli).

- Se si dispone di una versione di valutazione gratuita o di un credito mensile per la sottoscrizione che non è stato superato, non si riceverà alcuna fattura a meno che non sia stato sottoscritto un Contratto del cliente Microsoft.

## <a name="get-your-invoice-in-email-pdf"></a>Ottenere la fattura tramite posta elettronica (formato PDF)

Per ricevere una fattura di Azure tramite posta elettronica, è possibile fornire il consenso esplicito e configurare altri destinatari. Questa funzionalità potrebbe non essere disponibile per alcune sottoscrizioni, ad esempio offerte di supporto, contratti Enterprise o Azure in Open. Se si ha un Contratto del cliente Microsoft, vedere Ricevere le fatture del profilo di fatturazione tramite posta elettronica.

### <a name="get-your-subscriptions-invoices-in-email"></a>Ricevere le fatture della sottoscrizione tramite posta elettronica

1. Selezionare la sottoscrizione nella [pagina delle sottoscrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Dare il consenso esplicito per ogni sottoscrizione posseduta. Fare clic su **Invoices** (Fatture) quindi su **Email my invoice** (Invia fattura tramite posta elettronica).

    ![Schermata che mostra il flusso per il consenso esplicito](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)

2. Fare clic su **Acconsenti esplicitamente** e accettare i termini.

    ![Schermata che illustra il passaggio 2 del flusso per il consenso esplicito](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)

3. Dopo l'accettazione del contratto, sarà possibile configurare altri destinatari. Dopo che un destinatario è stato rimosso, l'indirizzo di posta elettronica non è più archiviato. Se si cambia idea, è necessario aggiungerlo di nuovo.

    ![Schermata che illustra il passaggio 3 del flusso per il consenso esplicito](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)

Se non si riceve un'email dopo aver eseguito i passaggi seguenti, assicurarsi che l'indirizzo email sia corretto nelle [preferenze di comunicazione del proprio profilo](https://account.windowsazure.com/profile).

### <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Rifiutare esplicitamente di ricevere le fatture della sottoscrizione tramite posta elettronica

Per rifiutare esplicitamente di ricevere la fattura tramite posta elettronica, seguire la procedura precedente e fare clic su **Rifiuta esplicitamente le fatture tramite posta elettronica**. Questa opzione rimuove tutti gli indirizzi di posta elettronica impostati per la ricezione di fatture tramite posta elettronica. Se si acconsente esplicitamente di nuovo, sarà necessario riconfigurare i destinatari.

 ![Schermata che illustra il flusso del rifiuto esplicito](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

### <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>Ricevere le fatture del Contratto del cliente Microsoft tramite posta elettronica

Se si ha un Contratto del cliente Microsoft, è possibile acconsentire esplicitamente a ricevere la fattura tramite posta elettronica. Tutti gli utenti con il ruolo di proprietario, collaboratore, lettore o gestione fatture del profilo di fatturazione riceveranno la fattura tramite posta elettronica. I lettori non possono aggiornare la preferenza per la fattura tramite posta elettronica.

1. Cercare **Gestione dei costi e fatturazione**.
1. Selezionare un profilo di fatturazione.
1. In **Impostazioni** selezionare **Proprietà**.
1. In **Fattura tramite posta elettronica** selezionare **Aggiorna le preferenze di fatturazione tramite posta elettronica**.
1. Selezionare **Acconsenti esplicitamente** .
1. Fare clic su **Update**.

### <a name="opt-out-of-getting-your-billing-profile-invoices-in-email"></a>Rifiutare esplicitamente di ricevere le fatture del profilo di fatturazione tramite posta elettronica

Per rifiutare esplicitamente di ricevere la fattura tramite posta elettronica, seguire la procedura precedente e fare clic su **Rifiuta esplicitamente**. Tutti gli utenti con il ruolo di proprietario, collaboratore, lettore o gestione fatture potranno anche scegliere di non ricevere la fattura tramite posta elettronica. Se si è un lettore, non è possibile modificare la preferenza per la fattura tramite posta elettronica.

## <a name="download-usage"></a>Scaricare informazioni sull'utilizzo

 Per la maggior parte delle sottoscrizioni, il file di utilizzo giornaliero è disponibile nel [Centro account di Azure](https://account.azure.com/Subscriptions). Se si è un cliente EA o si ha un Contratto del cliente Microsoft, è possibile scaricare i dati di utilizzo nel [portale di Azure](https://portal.azure.com/). <!-- TO DO: update PayG experience to Ibiza once it ships-->

### <a name="download-usage-from-the-account-center-csv"></a>Scaricare i dati di utilizzo dal Centro account (CSV)

1. Accedere al [Centro account di Azure](https://account.windowsazure.com/subscriptions) come amministratore account.

2. Selezionare la sottoscrizione per cui si desiderano le informazioni delle fatture e degli utilizzi.

3. Selezionare **Cronologia di fatturazione**.

    ![Schermata che mostra l'opzione Cronologia di fatturazione](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. Vengono elencati i rendiconti per gli ultimi sei periodi di fatturazione e il periodo corrente non ancora fatturato.

    ![Schermata che mostra i periodi di fatturazione, le opzioni per il download della fattura e dei dati di utilizzo giornalieri e gli addebiti totali per ogni periodo di fatturazione](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. Selezionare **Visualizza estratto conto corrente** per visualizzare una stima degli addebiti fino al momento in cui è stata generata la stima. Queste informazioni vengono aggiornate solo una volta al giorno e potrebbero non includere tutti gli utilizzi effettuati. È possibile che la fattura mensile non corrisponda a questa stima.

    ![Schermata che mostra l'opzione Visualizza estratto conto corrente](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![Schermata che mostra la stima degli addebiti correnti](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. Selezionare **Scarica utilizzo** per scaricare i dati di utilizzo giornalieri come file CSV. Se vengono visualizzate due versioni disponibili, scaricare la versione 2.

    ![Schermata che mostra l'opzione Scarica utilizzo](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

Solo l'amministratore account può accedere al Centro account di Azure. Gli altri amministratori della fatturazione, ad esempio il proprietario, possono ottenere le informazioni sull'uso tramite le [API di fatturazione](billing-usage-rate-card-overview.md).

Per altre informazioni sui dati di utilizzo giornalieri, vedere [Comprendere la fattura per Microsoft Azure](billing-understand-your-bill.md). Per informazioni sulla gestione dei costi, vedere [Evitare costi imprevisti con la gestione dei costi e la fatturazione di Azure](billing-getting-started.md).

### <a name="download-usage-for-ea-customers"></a>Scaricare informazioni sull'utilizzo per i clienti EA

Per visualizzare e scaricare i dati di utilizzo per i clienti EA, è necessario essere un amministratore dell'organizzazione, un proprietario dell'account o un amministratore del reparto con i criteri per la visualizzazione degli addebiti abilitati.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Cercare *Gestione dei costi e fatturazione*.

    ![Screenshot che mostra una ricerca nel portale di Azure](./media/billing-download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. Selezionare **Utilizzo e addebiti**.
1. Per il mese da scaricare, selezionare **Scarica**.

### <a name="download-usage-for-your-microsoft-customer-agreement"></a>Scaricare i dati di utilizzo per il Contratto del cliente Microsoft

Per visualizzare e scaricare i dati di utilizzo per un profilo di fatturazione, è necessario avere il ruolo di proprietario, collaboratore, lettore o gestione fatture del profilo di fatturazione.

#### <a name="download-usage-for-billed-charges"></a>Scaricare i dati di utilizzo per gli addebiti fatturati

1. Cercare **Gestione dei costi e fatturazione**.
2. Selezionare un profilo di fatturazione.
3. Selezionare **Fatture**.
4. Nella griglia della fattura trovare la riga corrispondente ai dati di utilizzo da scaricare.
5. Fare clic sui puntini di sospensione (`...`) alla fine della riga.
6. Nel menu di scelta rapida Download scegliere **Utilizzo e addebiti di Azure**.

#### <a name="download-usage-for-open-charges"></a>Scaricare i dati di utilizzo per gli addebiti aperti

È anche possibile scaricare i dati di utilizzo da inizio mese per il periodo di fatturazione corrente, ovvero gli addebiti che non sono stati ancora fatturati.

1. Cercare **Gestione dei costi e fatturazione**.
2. Selezionare un profilo di fatturazione.
3. Nel pannello **Panoramica** fare clic su **Scarica il file relativo a utilizzo e addebiti di Azure**.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificare l'accesso a un Contratto del cliente Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contatti

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla fattura e sugli addebiti, vedere:

- [Comprendere la fattura per Microsoft Azure](billing-understand-your-bill.md)
- [Informazioni sui termini della fattura di Azure](billing-understand-your-invoice.md)
- [Comprendere i termini sui dati di utilizzo dettagliato di Microsoft Azure](billing-understand-your-usage.md)
- [Visualizzare i prezzi di Azure dell'organizzazione](billing-ea-pricing.md)

Se si ha un Contratto del cliente Microsoft, vedere:

- [Informazioni sugli addebiti nella fattura per il profilo di fatturazione](billing-mca-understand-your-bill.md)
- [Informazioni sui termini nella fattura per il profilo di fatturazione](billing-mca-understand-your-invoice.md)
- [Informazioni sul file relativo a utilizzo e addebiti di Azure per il profilo di fatturazione](billing-mca-understand-your-usage.md)
- [Visualizzare e scaricare i documenti fiscali per il profilo di fatturazione](billing-mca-download-tax-document.md)
- [Visualizzare i prezzi di Azure dell'organizzazione](billing-ea-pricing.md)
