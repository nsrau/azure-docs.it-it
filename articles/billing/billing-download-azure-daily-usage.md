---
title: Utilizzo di visualizzazione e Download di Azure e negli addebiti | Microsoft Docs
description: Viene descritto come scaricare o visualizzare i dati di utilizzo giornalieri Azure e gli addebiti.
keywords: utilizzo della fatturazione, costi di utilizzo, utilizzo scaricare, visualizzare l'utilizzo, utilizzo la fattura di azure, azure
services: billing
documentationcenter: ''
author: genlin
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: 520d3f6a45b44ba2023dee34642f796689f48221
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60918987"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>Visualizzare e scaricare l'utilizzo di Azure e i costi

Se si è un cliente con contratto Enterprise Agreement o avere un [contratto di Microsoft dal cliente](#check-your-access-to-a-microsoft-customer-agreement), utilizzo di Azure e dei costi, è possibile scaricare la [portale di Azure](https://portal.azure.com/). Per altre sottoscrizioni, visitare il [centro Account Azure](https://account.azure.com/Subscriptions) scaricare l'utilizzo.

Solo determinati ruoli dispongono dell'autorizzazione per ottenere le informazioni sull'utilizzo di Azure, ad esempio l'Account amministratore o amministratore dell'organizzazione. Per altre informazioni sull'accesso alle informazioni di fatturazione, vedere [Manage access to Azure billing using roles](billing-manage-access.md) (Gestire l'accesso alla fatturazione di Azure usando i ruoli).

Se si dispone di un [contratto di Microsoft dal cliente](#check-your-access-to-a-microsoft-customer-agreement), è necessario essere un profilo di fatturazione proprietario, collaboratore, lettore, o manager per visualizzare l'utilizzo di Azure e i costi della fattura. Per altre informazioni sui ruoli di fatturazione per i contratti dei clienti Microsoft, vedere [attività e ruoli del profilo di fatturazione](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

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

Per altre informazioni sui dati di utilizzo giornalieri, vedere [Comprendere la fattura per Microsoft Azure](billing-understand-your-bill.md). Per informazioni sulla gestione dei costi, vedere [Evitare costi imprevisti con la gestione dei costi e la fatturazione di Azure](billing-getting-started.md).

## <a name="download-usage-for-ea-customers"></a>Scaricare informazioni sull'utilizzo per i clienti EA

Amministratore di reparto con la vista spese criterio abilitato o per visualizzare e scaricare i dati di utilizzo per i clienti con contratto Enterprise Agreement, è necessario essere un amministratore dell'organizzazione, proprietario dell'Account.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Cercare *costi di gestione e fatturazione*.

    ![Screenshot che mostra una ricerca nel portale di Azure](./media/billing-download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. Selezionare **Utilizzo e addebiti**.
1. Per il mese da scaricare, selezionare **Scarica**.

## <a name="download-usage-for-your-microsoft-customer-agreement"></a>Scarica utilizzo per il contratto di Microsoft dal cliente

Se hai un contratto di Microsoft dal cliente, è possibile scaricare l'utilizzo di Azure e gli addebiti per il profilo di fatturazione. È necessario essere un profilo di fatturazione proprietario, collaboratore, lettore, o manager per scaricare l'utilizzo di Azure e gli addebiti CSV di fatturazione.

### <a name="download-usage-for-billed-charges"></a>Scarica utilizzo per gli addebiti fatturati

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Eseguire ricerche in **Gestione dei costi e fatturazione**.
3. Selezionare un profilo di fatturazione. In base all'accesso, occorre prima selezionare un account di fatturazione.
4. Selezionare **Fatture**.
5. Nella griglia della fattura, individuare la riga della fattura corrispondente per l'utilizzo da scaricare.
6. Fare clic sui puntini di sospensione (`...`) alla fine della riga.

    ![Screenshot che mostra i puntini di sospensione alla fine della riga](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)

7. Nel menu di scelta rapida del download, selezionare **utilizzo di Azure e gli addebiti**.

     ![Screenshot che mostra l'utilizzo di Azure e gli addebiti selezionati](./media/billing-download-azure-usage/contextmenu-usage.png)

### <a name="download-usage-for-pending-charges"></a>Scarica utilizzo per in sospeso addebiti

È anche possibile scaricare l'utilizzo di month-to-date per il periodo di fatturazione corrente. Questi addebiti di utilizzo che non hanno ancora fatturati.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Eseguire ricerche in **Gestione dei costi e fatturazione**.
3. Selezionare un profilo di fatturazione. In base all'accesso, occorre prima selezionare un account di fatturazione.
4. Nel **Panoramica** pannello trovare i collegamenti di download sotto gli addebiti month-to-date.
5. Selezionare **utilizzo di Azure e gli addebiti**.

    ![Screenshot che mostra il download dal dashboard Panoramica](./media/billing-download-azure-usage/open-usage.png)

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Controllare l'accesso a un contratto di Microsoft dal cliente
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contatti

Se si hanno domande o assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui costi di fatturazione e utilizzo, vedere:

- [Comprendere i termini sui dati di utilizzo dettagliato di Microsoft Azure](billing-understand-your-usage.md)
- [Comprendere la fattura per Microsoft Azure](billing-understand-your-bill.md)
- [Visualizzare e scaricare la fattura di Microsoft Azure](billing-download-azure-invoice.md)
- [Visualizzare e scaricare i prezzi di Azure dell'organizzazione](billing-ea-pricing.md)

Se si dispone di un contratto di clienti Microsoft, vedere:

- [Informazioni sul contratto di cliente di Microsoft Azure condizioni dettagliate sull'utilizzo](billing-mca-understand-your-usage.md)
- [Comprendere gli addebiti nella fattura di contratto di Microsoft dal cliente](billing-mca-understand-your-bill.md)
- [Visualizzare e scaricare la fattura di Microsoft Azure](billing-download-azure-invoice.md)
- [Visualizzare e scaricare documenti fiscali del contratto di Microsoft dal cliente](billing-mca-download-tax-document.md)
- [Visualizzare e scaricare i prezzi di Azure dell'organizzazione](billing-ea-pricing.md)
