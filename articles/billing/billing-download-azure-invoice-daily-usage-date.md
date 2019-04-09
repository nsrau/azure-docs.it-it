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
ms.openlocfilehash: b78fb7d697f8a72b3c2f99c4509ea6ac5c5e5566
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59281183"
---
# <a name="download-or-view-your-azure-billing-invoice-and-daily-usage-data"></a>Scaricare o visualizzare la fattura e i dati di uso giornalieri di Azure

Per la maggior parte delle sottoscrizioni, è possibile scaricare la fattura dal [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) o richiedere l'invio tramite posta elettronica. I clienti di Azure con un contratto Enterprise (clienti EA) non possono scaricare le fatture della loro organizzazione. Le fatture vengono inviate a chiunque sia configurato per la ricezione delle fatture per la registrazione.

Se si è un cliente con contratto Enterprise Agreement o avere una [contratto di Microsoft dal cliente](#check-access-to-a-microsoft-customer-agreement), è possibile scaricare l'utilizzo nel [portale di Azure](https://portal.azure.com/). Per altre sottoscrizioni, visitare il [centro Account Azure](https://account.azure.com/Subscriptions) scaricare l'utilizzo.

Solo determinati ruoli dispongono dell'autorizzazione per ottenere informazioni di fatturazione e di utilizzo, ad esempio l'Account amministratore o amministratore dell'organizzazione di fatturazione. Per altre informazioni sull'accesso alle informazioni di fatturazione, vedere [Manage access to Azure billing using roles](billing-manage-access.md) (Gestire l'accesso alla fatturazione di Azure usando i ruoli).

Se hai un contratto di Microsoft dal cliente, è necessario essere un profilo di fatturazione proprietario, collaboratore, lettore, o manager per visualizzare le informazioni di fatturazione e utilizzo della fattura. Per altre informazioni sui ruoli di fatturazione per i contratti dei clienti Microsoft, vedere [attività e ruoli del profilo di fatturazione](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="download-your-azure-invoices-pdf"></a>Scaricare le fatture di Azure (PDF)

Per la maggior parte delle sottoscrizioni, è possibile scaricare la fattura dal portale di Azure. Se hai un contratto di clienti Microsoft, vedere il Download delle fatture per un profilo di fatturazione.

### <a name="download-invoices-for-an-individual-subscription"></a>Scaricare le fatture per una singola sottoscrizione

1. Selezionare la sottoscrizione di [pagina sottoscrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) nel portale di Azure come [un utente con accesso alle fatture](billing-manage-access.md).

2. Selezionare **Fatture**.

    ![Schermata che mostra l'opzione Fatturazione e utilizzo](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png)

3. Fare clic su **Scarica fattura** per visualizzare una copia della fattura in formato PDF. Se l'indicazione è **Non disponibile**, vedere [Perché non viene visualizzata una fattura per l'ultimo periodo di fatturazione?](#noinvoice)

    ![Schermata che mostra i periodi di fatturazione, l'opzione per il download e gli addebiti totali per ogni periodo di fatturazione](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. Per visualizzare l'uso giornaliero, fare clic sul periodo di fatturazione.

Per altre informazioni sulla fattura, vedere [Comprendere la fattura per Microsoft Azure](billing-understand-your-bill.md). Per informazioni sulla gestione dei costi, vedere [Evitare costi imprevisti con la gestione dei costi e la fatturazione di Azure](billing-getting-started.md).

### <a name="download-invoices-for-a-microsoft-customer-agreement"></a>Scaricare le fatture per un contratto di Microsoft dal cliente

Le fatture vengono generate per ogni [profilo di fatturazione](billing-mca-overview.md#understand-billing-profiles) contratto in Microsoft dal cliente. È necessario essere un profilo di fatturazione proprietario, collaboratore, lettore, o fatturerà manager per scaricare le fatture dal portale di Azure.

1. Eseguire ricerche in **Gestione dei costi e fatturazione**.
2. Selezionare un profilo di fatturazione.
3. Selezionare **Fatture**.
4. Nella griglia della fattura, individuare la riga della fattura da scaricare.
5. Fare clic sui puntini di sospensione (`...`) alla fine della riga.
6. Nel menu di scelta rapida del download, selezionare **fattura**.

Se non trovi una fattura per l'ultimo periodo di fatturazione, vedere **informazioni aggiuntive**. <!-- Fix this -->
### <a name="noinvoice"></a> Perché non viene visualizzata una fattura per l'ultimo periodo di fatturazione?

Potrebbero esserci diversi motivi per cui non è visualizzata alcuna fattura:

- Sono passati meno di trenta giorni dalla data della sottoscrizione di Azure.

- La fattura non è stata ancora generata. Attendere la fine del periodo di fatturazione.

- Non si è autorizzati a visualizzare le fatture. Se hai un contratto di Microsoft dal cliente, è necessario essere il profilo di fatturazione proprietario, collaboratore, lettore, o gestione della fattura. Per altre sottoscrizioni, potrebbe non essere visibile le fatture precedenti se non si è amministratore dell'Account. Per altre informazioni sull'accesso alle informazioni di fatturazione, vedere [Manage access to Azure billing using roles](billing-manage-access.md) (Gestire l'accesso alla fatturazione di Azure usando i ruoli).

- Se si dispone di una versione di valutazione gratuita o un credito mensile con la sottoscrizione che non è stato superato, si otterranno una fattura a meno che non disponibile un contratto di clienti Microsoft.

## <a name="get-your-invoice-in-email-pdf"></a>Ottenere la fattura tramite posta elettronica (formato PDF)

Per ricevere una fattura di Azure tramite posta elettronica, è possibile fornire il consenso esplicito e configurare altri destinatari. Questa funzionalità potrebbe non essere disponibile per alcune sottoscrizioni, ad esempio offerte di supporto, contratti Enterprise o Azure in Open. Se si dispone di un contratto di Microsoft Customer, vedere Get il profilo di fatturazione fatture tramite posta elettronica.

### <a name="get-your-subscriptions-invoices-in-email"></a>Ottenere le fatture della sottoscrizione tramite posta elettronica

1. Selezionare la sottoscrizione nella [pagina delle sottoscrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Dare il consenso esplicito per ogni sottoscrizione posseduta. Fare clic su **Invoices** (Fatture) quindi su **Email my invoice** (Invia fattura tramite posta elettronica).

    ![Schermata che mostra il flusso per il consenso esplicito](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)

2. Fare clic su **Acconsenti esplicitamente** e accettare i termini.

    ![Schermata che illustra il passaggio 2 del flusso per il consenso esplicito](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)

3. Dopo l'accettazione del contratto, sarà possibile configurare altri destinatari. Dopo che un destinatario è stato rimosso, l'indirizzo di posta elettronica non è più archiviato. Se si cambia idea, è necessario aggiungerlo di nuovo.

    ![Schermata che illustra il passaggio 3 del flusso per il consenso esplicito](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)

Se non si riceve un'email dopo aver eseguito i passaggi seguenti, assicurarsi che l'indirizzo email sia corretto nelle [preferenze di comunicazione del proprio profilo](https://account.windowsazure.com/profile).

### <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Rifiutare esplicitamente recupero fatture della tua sottoscrizione nel messaggio di posta elettronica

È possibile rifiutare esplicitamente di ottenere la fattura tramite posta elettronica seguendo i passaggi precedenti e facendo clic su **rifiutare esplicitamente la fatture tramite posta elettronica**. Questa opzione rimuove tutti gli indirizzi di posta elettronica impostati per la ricezione di fatture tramite posta elettronica. Se si decide di nuovo, è possibile riconfigurare i destinatari.

 ![Schermata che illustra il flusso del rifiuto esplicito](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

### <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>Ottenere le fatture di contratto di Microsoft dal cliente tramite posta elettronica

Se hai un contratto di Microsoft dal cliente, è possibile acconsentire esplicitamente a ottenere la fattura tramite posta elettronica. Tutti i fatturazione profilo proprietari, collaboratori, i lettori e fattura Manager otterrà la fattura tramite posta elettronica. I lettori non è possibile aggiornare la preferenza di fattura tramite posta elettronica.

1. Eseguire ricerche in **Gestione dei costi e fatturazione**.
1. Selezionare un profilo di fatturazione.
1. In **Impostazioni** selezionare **Proprietà**.
1. Sotto **fattura tramite posta elettronica**, selezionare **la preferenza di fattura tramite posta elettronica di aggiornamento**.
1. Selezionare **acconsentire esplicitamente**.
1. Fare clic su **Update**.

### <a name="opt-out-of-getting-your-billing-profile-invoices-in-email"></a>Rifiutare esplicitamente le fatture di profilo di fatturazione di introduzione nel messaggio di posta elettronica

È possibile rifiutare esplicitamente di ottenere la fattura tramite posta elettronica seguendo i passaggi precedenti e facendo clic su **Opt out**. Tutti i proprietari, collaboratori, i lettori e fattura verranno esclusi dal recupero troppo la fattura tramite posta elettronica, i responsabili. Se sei un lettore, è possibile modificare la preferenza di fattura tramite posta elettronica.

## <a name="download-usage"></a>Scaricare informazioni sull'utilizzo

 Per la maggior parte delle sottoscrizioni, il file di utilizzo giornaliero è disponibile nel [Centro account di Azure](https://account.azure.com/Subscriptions). Se si è un cliente con contratto Enterprise Agreement o dispone di un contratto di Microsoft dal cliente, è possibile scaricare l'utilizzo nel [portale di Azure](https://portal.azure.com/). <!-- TO DO: update PayG experience to Ibiza once it ships-->

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

Amministratore di reparto con la vista spese criterio abilitato o per visualizzare e scaricare i dati di utilizzo per i clienti con contratto Enterprise Agreement, è necessario essere un amministratore dell'organizzazione, proprietario dell'Account.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Cercare *costi di gestione e fatturazione*.

    ![Screenshot che mostra una ricerca nel portale di Azure](./media/billing-download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. Selezionare **Utilizzo e addebiti**.
1. Per il mese da scaricare, selezionare **Scarica**.

### <a name="download-usage-for-your-microsoft-customer-agreement"></a>Scarica utilizzo per il contratto di Microsoft dal cliente

Per visualizzare e scaricare i dati di utilizzo per un profilo di fatturazione, è necessario essere un profilo di fatturazione proprietario, collaboratore, lettore, o gestione della fattura.

#### <a name="download-usage-for-billed-charges"></a>Scarica utilizzo per gli addebiti fatturati

1. Eseguire ricerche in **Gestione dei costi e fatturazione**.
2. Selezionare un profilo di fatturazione.
3. Selezionare **Fatture**.
4. Nella griglia della fattura, individuare la riga della fattura corrispondente per l'utilizzo da scaricare.
5. Fare clic sui puntini di sospensione (`...`) alla fine della riga.
6. Nel menu di scelta rapida del download, selezionare **utilizzo di Azure e gli addebiti**.

#### <a name="download-usage-for-open-charges"></a>Scarica utilizzo per gli addebiti open

È anche possibile scaricare utilizzo month-to-date per il periodo di fatturazione corrente, vale a dire che gli addebiti non hanno ancora fatturati.

1. Eseguire ricerche in **Gestione dei costi e fatturazione**.
2. Selezionare un profilo di fatturazione.
3. Nel **Overview** pannello, fare clic su **utilizzo di Download di Azure e negli addebiti**.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificare l'accesso a un contratto di Microsoft dal cliente
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contatti

Se si hanno domande o assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla fattura e i costi, vedere:

- [Comprendere la fattura per Microsoft Azure](billing-understand-your-bill.md)
- [Comprendere i termini nella fattura di Azure](billing-understand-your-invoice.md)
- [Comprendere le condizioni in Microsoft Azure dettagliati sull'utilizzo](billing-understand-your-usage.md)
- [Visualizzare i prezzi di Azure dell'organizzazione](billing-ea-pricing.md)

Se si dispone di un contratto di clienti Microsoft, vedere:

- [Comprendere gli addebiti nella fattura per il profilo di fatturazione](billing-mca-understand-your-bill.md)
- [Comprendere i termini sulla fattura per il profilo di fatturazione](billing-mca-understand-your-invoice.md)
- [Comprendere il file di utilizzo e costi di Azure per il profilo di fatturazione](billing-mca-understand-your-usage.md)
- [Visualizzare e scaricare documenti di imposta per il profilo di fatturazione](billing-mca-download-tax-document.md)
- [Visualizzare i prezzi di Azure dell'organizzazione](billing-ea-pricing.md)
