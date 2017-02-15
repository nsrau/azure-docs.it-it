---
title: Come scaricare la fattura e i dati di utilizzo giornalieri di Azure | Documentazione Microsoft
description: Descrive come scaricare la fatturazione e dati di utilizzo giornalieri di Azure
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
ms.date: 12/2/2016
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 9b864a55d413ec7775e96fced4770b414f379a23
ms.openlocfilehash: ffc5cae2c4559dd8c7a9042dc676485966b976f4


---
# <a name="how-to-download-your-azure-billing-invoice-and-daily-usage-data"></a>Come scaricare la fattura e i dati di utilizzo giornalieri di Azure 
La fattura e i dati di utilizzo giornalieri sono scaricabili dal [Centro account di Azure](https://account.windowsazure.com/subscriptions). Solo l'amministratore account dispone dell'autorizzazione per ottenere le informazioni di fatturazione e i dati di utilizzo. Per individuare l'amministratore account della sottoscrizione, vedere [Trasferimento di proprietà di una sottoscrizione di Azure - Domande frequenti (FAQ)](billing-subscription-transfer.md#faq).

## <a name="get-invoice-and-usage-from-azure-account-center"></a>Ottenere la fattura e i dati di utilizzo dal Centro account di Azure
1. Accedere al [Centro account di Azure](https://account.windowsazure.com/subscriptions) come amministratore account.
2. Selezionare la sottoscrizione per cui si desiderano le informazioni delle fatture e degli utilizzi.
3. Selezionare **Cronologia di fatturazione**. 

    ![Schermata che mostra l'opzione Cronologia di fatturazione](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. Vengono elencati i rendiconti per gli ultimi sei periodi di fatturazione e il periodo corrente non ancora fatturato. 

    ![Schermata che mostra i periodi di fatturazione, le opzioni per il download della fattura e dei dati di utilizzo giornalieri e gli addebiti totali per ogni periodo di fatturazione](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. Selezionare **Visualizza estratto conto corrente** per visualizzare una stima degli addebiti fino al momento in cui è stata generata la stima. Queste informazioni vengono aggiornate solo una volta al giorno e potrebbero non includere tutti gli utilizzi effettuati. È possibile che la fattura mensile non corrisponda a questa stima.

    ![Schermata che mostra l'opzione Visualizza estratto conto corrente](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![Schermata che mostra la stima degli addebiti correnti](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. Selezionare **Scarica fattura** per visualizzare una copia dell'ultima fattura in formato pdf. 

    ![Schermata che mostra l'opzione Scarica fattura](./media/billing-download-azure-invoice-daily-usage-date/DLInvoice1.png)

7. Selezionare **Scarica utilizzo** per scaricare i dati di utilizzo giornalieri come file CSV. Se vengono visualizzate due versioni disponibili, scaricare la versione 2.

    ![Schermata che mostra l'opzione Scarica utilizzo](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

Per altre informazioni sulla fattura e i dati di utilizzo giornalieri, vedere [Comprendere la fattura per Microsoft Azure](./billing/billing-understand-your-bill.md).

## <a name="get-invoice-from-azure-portal"></a>Ottenere la fattura dal portale di Azure
È possibile visualizzare i dati di utilizzo giornalieri dal portale di Azure, ma solo la fattura è disponibile per il download.

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore account. 
2. Nel menu Hub selezionare **Sottoscrizioni**. 

    ![Schermata che mostra l'opzione Sottoscrizioni](./media/billing-download-azure-invoice-daily-usage-date/submenu.png) 

3. Nel pannello **Sottoscrizioni** selezionare la sottoscrizione che si vuole visualizzare e quindi **Fatturazione e utilizzo**. 

    ![Schermata che mostra l'opzione Fatturazione e utilizzo](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png) 

4. Nel pannello **Fatturazione e utilizzo** fare clic su **Scarica fattura** per visualizzare una copia della fattura in formato pdf. 

    ![Schermata che mostra i periodi di fatturazione, l'opzione per il download e gli addebiti totali per ogni periodo di fatturazione](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

5. È possibile visualizzare l'utilizzo giornaliero facendo clic sul periodo di fatturazione. 

Per altre informazioni sulla fattura e i dati di utilizzo giornalieri, vedere [Comprendere la fattura per Microsoft Azure](./billing/billing-understand-your-bill.md).

## <a name="a-namenoinvoicea-why-dont-i-see-an-invoice-for-the-last-billing-period"></a><a name="noinvoice"></a> Perché non viene visualizzata una fattura per l'ultimo periodo di fatturazione?
Potrebbero esserci diversi motivi per cui non è visualizzata alcuna fattura:
- Si dispone di un credito mensile per la sottoscrizione che non è stato superato oppure si sta usando una versione di prova gratuita. La fattura viene generata solo se occorre corrispondere un importo in denaro.
- Sono passati meno di trenta giorni dalla data della sottoscrizione di Azure.
- La fattura non è stata ancora generata. Attendere la fine del periodo di fatturazione.

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico.
Per altre domande, è possibile [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.




<!--HONumber=Dec16_HO2-->


