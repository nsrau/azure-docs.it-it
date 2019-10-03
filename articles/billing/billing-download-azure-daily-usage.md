---
title: Visualizzare e scaricare il file dei dati di utilizzo e addebiti di Azure
description: Questo articolo descrive come scaricare o visualizzare il file dei dati quotidiani di utilizzo e addebiti di Azure.
keywords: utilizzo fatturazione, addebiti utilizzo, download utilizzo, visualizzazione utilizzo, fattura di azure, utilizzo di azure
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 23cd7c3765fc99eb5907aa853d7431d5e247aea6
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709718"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>Visualizzare e scaricare il file dei dati di utilizzo e addebiti di Azure

I clienti con contratto EA o con [Contratto del cliente Microsoft](#check-your-access-to-a-microsoft-customer-agreement) possono scaricare il file dei dati di utilizzo e addebiti di Azure nel [portale di Azure](https://portal.azure.com/). Per altre sottoscrizioni, visitare il [Centro account di Azure](https://account.azure.com/Subscriptions) per scaricare tale file.

Solo determinati ruoli sono autorizzati a ottenere informazioni sull'utilizzo di Azure, ad esempio l'amministratore dell'account o l'amministratore dell'organizzazione. Per altre informazioni sull'accesso alle informazioni di fatturazione, vedere [Manage access to Azure billing using roles](billing-manage-access.md) (Gestire l'accesso alla fatturazione di Azure usando i ruoli).

Per visualizzare i dati di utilizzo e addebiti di Azure, un cliente con [Contratto del cliente Microsoft](#check-your-access-to-a-microsoft-customer-agreement) deve avere il ruolo di proprietario, collaboratore, lettore o gestione fatture del profilo di fatturazione. Per altre informazioni sui ruoli di fatturazione per i Contratti del cliente Microsoft, vedere [Ruoli e attività del profilo di fatturazione](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="download-usage-from-the-account-center-csv"></a>Scaricare i dati di utilizzo dal Centro account (CSV)

1. Accedere al [Centro account di Azure](https://account.windowsazure.com/subscriptions) come Amministratore account.

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

Per visualizzare e scaricare i dati di utilizzo per i clienti con contratto EA, è necessario essere un amministratore dell'organizzazione, un proprietario dell'account o un amministratore del reparto con i criteri per la visualizzazione degli addebiti abilitati.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Cercare *Gestione dei costi e fatturazione*.

    ![Screenshot che mostra una ricerca nel portale di Azure](./media/billing-download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. Selezionare **Utilizzo e addebiti**.
1. Per il mese da scaricare, selezionare **Scarica**.

## <a name="download-usage-for-your-microsoft-customer-agreement"></a>Scaricare i dati di utilizzo per il Contratto del cliente Microsoft

Se si ha un Contratto del cliente Microsoft, è possibile scaricare i dati di utilizzo e addebiti per il profilo di fatturazione. È necessario avere il ruolo di proprietario, collaboratore, lettore o gestione fatture del profilo di fatturazione.

### <a name="download-usage-for-billed-charges"></a>Scarica i dati di utilizzo per gli addebiti fatturati

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Cercare *Gestione dei costi e fatturazione*.
3. Selezionare un profilo di fatturazione. A seconda dell'accesso, potrebbe essere necessario selezionare prima un account di fatturazione.
4. Selezionare **Fatture**.
5. Nella griglia della fattura trovare la riga corrispondente ai dati di utilizzo da scaricare.
6. Fare clic sui puntini di sospensione (`...`) alla fine della riga.

    ![Screenshot che mostra i puntini di sospensione alla fine della riga](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)

7. Nel menu di scelta rapida Download scegliere **Utilizzo e addebiti di Azure**.

     ![Screenshot che mostra l'utilizzo e gli addebiti di Azure selezionati](./media/billing-download-azure-usage/contextmenu-usage.png)

### <a name="download-usage-for-pending-charges"></a>Scaricare i dati di utilizzo per gli addebiti in sospeso

È anche possibile scaricare i dati di utilizzo da inizio mese per il periodo di fatturazione corrente. Questi addebiti per l'utilizzo non sono stati ancora fatturati.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Cercare *Gestione dei costi e fatturazione*.
3. Selezionare un profilo di fatturazione. A seconda dell'accesso, potrebbe essere necessario selezionare prima un account di fatturazione.
4. Nell'area **Panoramica** trovare i collegamenti di download sotto agli addebiti da inizio mese.
5. Selezionare **Utilizzo e addebiti di Azure**.

    ![Screenshot che mostra il download da Panoramica](./media/billing-download-azure-usage/open-usage.png)

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Verificare l'accesso a un Contratto cliente Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla fattura e sugli addebiti per l'utilizzo, vedere:

- [Comprendere i termini sui dati di utilizzo dettagliato di Microsoft Azure](billing-understand-your-usage.md)
- [Comprendere la fattura per Microsoft Azure](billing-understand-your-bill.md)
- [Visualizzare e scaricare la fattura di Microsoft Azure](billing-download-azure-invoice.md)
- [Visualizzare e scaricare i prezzi di Azure dell'organizzazione](billing-ea-pricing.md)

Se si ha un Contratto del cliente Microsoft, vedere:

- [Comprendere i termini sui dati di utilizzo dettagliati di Azure del Contratto del cliente Microsoft](billing-mca-understand-your-usage.md)
- [Comprendere gli addebiti nella fattura del Contratto del cliente Microsoft](billing-mca-understand-your-bill.md)
- [Visualizzare e scaricare la fattura di Microsoft Azure](billing-download-azure-invoice.md)
- [Visualizzare e scaricare i documenti fiscali per il Contratto del cliente Microsoft](billing-mca-download-tax-document.md)
- [Visualizzare e scaricare i prezzi di Azure dell'organizzazione](billing-ea-pricing.md)
