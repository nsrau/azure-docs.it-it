---
title: Scaricare la fattura e i dati di uso giornalieri di Azure | Microsoft Docs
description: L'articolo descrive come scaricare o visualizzare la fatturazione e dati di uso giornalieri di Azure.
keywords: fatturazione, scaricare la fatture, fattura di Azure, uso di Azure
services: 
documentationcenter: 
author: genlin
manager: tonguyen
editor: 
tags: billing
ms.assetid: 6d568d1d-3bd6-4348-97d0-1098b5fe0661
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 04/25/2017
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 01057dd7832cab6121a22e3bb7d0dc4cd7e9b35d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="download-or-view-your-azure-billing-invoice-and-daily-usage-data"></a>Scaricare o visualizzare la fattura e i dati di uso giornalieri di Azure
La fattura può essere scaricata dal [Portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) o inviata via email. Per scaricare l'uso giornaliero, accedere al [Centro account di Azure](https://account.azure.com/Subscriptions). Solo determinati ruoli dispongono dell'autorizzazione per ottenere informazioni sull'uso e sulla fatturazione, ad esempio l'amministratore account. Per altre informazioni sull'accesso alle informazioni di fatturazione, vedere [Manage access to Azure billing using roles](billing-manage-access.md) (Gestire l'accesso alla fatturazione di Azure usando i ruoli).

>[!NOTE]
>Questo articolo non si applica ai clienti con contratto Enterprise Agreement (EA). Se si è un cliente con contratto Enterprise Agreement (EA), le fatture vengono inviate direttamente agli amministratori delle registrazioni.

## <a name="get-your-invoice-in-email-pdf"></a>Ottenere la fattura tramite posta elettronica (formato PDF)
Per ricevere una fattura di Azure tramite posta elettronica, è possibile fornire il consenso esplicito e configurare altri destinatari. Questa funzionalità potrebbe non essere disponibile per alcune sottoscrizioni, ad esempio offerte di supporto, contratti Enterprise o Azure in Open.

1. Selezionare la sottoscrizione nella [pagina delle sottoscrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Dare il consenso esplicito per ogni sottoscrizione posseduta. Fare clic su **Invoices** (Fatture) quindi su **Email my invoice** (Invia fattura tramite posta elettronica). 

    ![Schermata che mostra il flusso per il consenso esplicito](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)
    
2. Fare clic su **Acconsenti esplicitamente** e accettare i termini.

    ![Schermata che mostra il flusso per il consenso esplicito](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)
 
3. Dopo l'accettazione del contratto, sarà possibile configurare altri destinatari.

    ![Schermata che mostra il flusso per il consenso esplicito](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)
    
Se non si riceve un'email dopo aver eseguito i passaggi seguenti, assicurarsi che l'indirizzo email sia corretto nelle [preferenze di comunicazione del proprio profilo](https://account.windowsazure.com/profile).

## <a name="download-invoice-from-azure-portal-pdf"></a>Scaricare la fattura dal portale di Azure (PDF)

1. Selezionare la sottoscrizione nella [pagina delle sottoscrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) del portale di Azure come [utente con accesso alle fatture](billing-manage-access.md).

2. Selezionare **Fatture**. 

    ![Schermata che mostra l'opzione Fatturazione e utilizzo](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png) 

3. Fare clic su **Scarica fattura** per visualizzare una copia della fattura in formato PDF. Se l'indicazione è **Non disponibile**, vedere [Perché non viene visualizzata una fattura per l'ultimo periodo di fatturazione?](#noinvoice)

    ![Schermata che mostra i periodi di fatturazione, l'opzione per il download e gli addebiti totali per ogni periodo di fatturazione](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. Per visualizzare l'uso giornaliero, fare clic sul periodo di fatturazione. 

Per altre informazioni sulla fattura, vedere [Comprendere la fattura per Microsoft Azure](billing-understand-your-bill.md). Per informazioni sulla gestione dei costi, vedere [Evitare costi aggiuntivi con la gestione dei costi e la fatturazione di Azure](billing-getting-started.md).

## <a name="download-usage-from-the-account-center-csv"></a>Scaricare i dati di utilizzo dal Centro account (CSV)

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

Per altre informazioni sui dati di utilizzo giornalieri, vedere [Comprendere la fattura per Microsoft Azure](billing-understand-your-bill.md). Per informazioni sulla gestione dei costi, vedere [Evitare costi aggiuntivi con la gestione dei costi e la fatturazione di Azure](billing-getting-started.md).

## <a name="noinvoice"></a> Perché non viene visualizzata una fattura per l'ultimo periodo di fatturazione?

Potrebbero esserci diversi motivi per cui non è visualizzata alcuna fattura:

- Si dispone di un credito mensile per la sottoscrizione che non è stato superato oppure si sta usando una versione di prova gratuita. La fattura viene generata solo quando si possiede del denaro.

- Sono passati meno di trenta giorni dalla data della sottoscrizione di Azure.

- La fattura non è stata ancora generata. Attendere la fine del periodo di fatturazione.

- Se non si è l'amministratore account, le fatture precedenti potrebbero non essere disponibili.

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico.
Per altre domande, è possibile [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

