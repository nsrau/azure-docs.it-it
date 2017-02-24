---
title: Come ottenere la fattura e i dati di utilizzo giornalieri di Azure | Documentazione Microsoft
description: Descrive come ottenere la fattura e i dati di utilizzo giornalieri di Azure
services: 
documentationcenter: 
author: genlin
manager: ruchic
editor: 
tags: billing
ms.assetid: 6d568d1d-3bd6-4348-97d0-1098b5fe0661
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 4d974590ee7677a4f3fcebd9e2474c99a974f663
ms.openlocfilehash: 60ac57fd22f237b7fe62773486fe46913aca8dd0


---
# <a name="how-to-get-your-azure-billing-invoice-and-daily-usage-data"></a>Come ottenere la fattura e i dati di utilizzo giornalieri di Azure
Per ricevere una fattura di Azure tramite posta elettronica, è possibile fornire il consenso esplicito e configurare altri destinatari. La fattura può anche essere scaricata dal [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). È inoltre possibile scaricare un file CSV relativo ai dati di utilizzo nel [Centro account di Azure](https://account.windowsazure.com). Solo l'amministratore account dispone dell'autorizzazione per ottenere le informazioni di fatturazione e i dati di utilizzo. Per individuare l'amministratore account della sottoscrizione, vedere la sezione relativa al [Trasferimento di proprietà di una sottoscrizione di Azure (Domande frequenti)](billing-subscription-transfer.md#faq).

## <a name="get-your-invoice-over-email-pdf"></a>Ottenere la fattura tramite posta elettronica (PDF)
1. Selezionare la sottoscrizione dal [pannello delle sottoscrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). È necessario fornire il consenso esplicito per ogni sottoscrizione posseduta. Fare clic su **Invoices** (Fatture) quindi su **Email my invoice** (Invia fattura tramite posta elettronica). Questa opzione potrebbe non essere visibile per utenti diversi dall'amministratore account.

    ![Schermata che mostra il flusso per il consenso esplicito](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)
    
2. Fare clic su **Acconsenti esplicitamente** e accettare i termini:

    ![Schermata che mostra il flusso per il consenso esplicito](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)
 
3. Dopo l'accettazione del contratto, sarà possibile configurare altri destinatari:

    ![Schermata che mostra il flusso per il consenso esplicito](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)
    
È inoltre possibile accedere a questo collegamento diretto al pannello tramite il messaggio di posta elettronica di notifica dell'estratto conto mensile

### <a name="i-cant-access-the-email-settings-blade"></a>Non è possibile accedere al pannello delle impostazioni della posta elettronica
* Cosa significa che per configurare questa impostazione è necessario essere l'amministratore account? Fare clic [qui](billing-add-change-azure-subscription-administrator.md) per altre informazioni.
* Se si dispone di una fattura mensile ma non si riceve un messaggio di posta elettronica, accertarsi di aver [impostato correttamente l'indirizzo di posta elettronica di comunicazione](https://account.windowsazure.com/profile).
* Questa funzionalità potrebbe non essere disponibile per alcune sottoscrizioni, ad esempio offerte di supporto, contratti Enterprise o Azure in Open.

## <a name="download-invoice-from-azure-portal-pdf"></a>Scaricare la fattura dal portale di Azure (PDF)

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore account. 
2. Nel menu Hub selezionare **Sottoscrizioni**. 

    ![Schermata che mostra l'opzione Sottoscrizioni](./media/billing-download-azure-invoice-daily-usage-date/submenu.png) 

3. Nel pannello **Sottoscrizioni** selezionare la sottoscrizione che si vuole visualizzare e quindi **Fatturazione e utilizzo**. 

    ![Schermata che mostra l'opzione Fatturazione e utilizzo](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png) 

4. Nel pannello **Fatturazione e utilizzo** fare clic su **Scarica fattura** per visualizzare una copia della fattura in formato pdf. 

    ![Schermata che mostra i periodi di fatturazione, l'opzione per il download e gli addebiti totali per ogni periodo di fatturazione](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

5. È possibile visualizzare l'utilizzo giornaliero facendo clic sul periodo di fatturazione. 

Per altre informazioni sulla fattura, vedere [Comprendere la fattura per Microsoft Azure](billing-understand-your-bill.md).

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

Per altre informazioni sui dati di utilizzo giornalieri, vedere [Comprendere la fattura per Microsoft Azure](billing-understand-your-bill.md).

## <a name="a-namenoinvoicea-why-dont-i-see-an-invoice-for-the-last-billing-period"></a><a name="noinvoice"></a> Perché non viene visualizzata una fattura per l'ultimo periodo di fatturazione?
Potrebbero esserci diversi motivi per cui non è visualizzata alcuna fattura:
- Si dispone di un credito mensile per la sottoscrizione che non è stato superato oppure si sta usando una versione di prova gratuita. La fattura viene generata solo se occorre corrispondere un importo in denaro.
- Sono passati meno di trenta giorni dalla data della sottoscrizione di Azure.
- La fattura non è stata ancora generata. Attendere la fine del periodo di fatturazione.

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico.
Per altre domande, è possibile [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.




<!--HONumber=Feb17_HO3-->


