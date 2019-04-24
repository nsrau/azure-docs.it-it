---
title: Comprendere la fattura di Microsoft dal cliente contratto | Microsoft Docs
description: Informazioni su come leggere e comprendere la fattura MCA
services: ''
documentationcenter: ''
author: jureid
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: ee6317f61f95b19effd64308b88f53c027582b63
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60371432"
---
# <a name="understand-terms-on-your-microsoft-customer-agreement-invoice"></a>Comprendere i termini sulla fattura di contratto di Microsoft dal cliente

Questo articolo si applica a un account di fatturazione per un contratto di clienti Microsoft. [Controllare se si ha accesso a un contratto di Microsoft dal cliente](#check-access-to-a-microsoft-customer-agreement).

La fattura viene fornito un riepilogo dei costi e le istruzioni per il pagamento. È disponibile per il download in formato PDF (Portable Document Format) dal [Portale di Azure](https://portal.azure.com/). In alternativa, è possibile riceverla tramite posta elettronica. Per altre informazioni, vedere [visualizzazione e download della fattura di Microsoft Azure](billing-download-azure-invoice.md).

<!-- ## When am I billed?

You are invoiced on a monthly basis. You can find out which day of the month you receive invoices by checking *invoice date* under billing profile properties in the [Azure portal](https://portal.azure.com/). Charges that occur between the end of the billing period and the invoice date are included in the next month's invoice, since they are in the next billing period. The billing period start and end dates for each invoice are listed in the invoice PDF above **Billing Summary**. -->

## <a name="invoice-terms-and-descriptions"></a>Condizioni della fattura e descrizioni

Le sezioni seguenti elencano i termini importanti che presenti sulle fatture e le descrizioni per ogni termine.

### <a name="invoice-summary"></a>Riepilogo della fattura

Il **riepilogo della fattura** nella parte superiore della prima pagina e contiene informazioni sul profilo di fatturazione e il metodo di pagamento.

![Sezione di riepilogo della fattura](./media/billing-understand-your-invoice-mca/invoicesummary.png)

| Termine | DESCRIZIONE |
| --- | --- |
| Venduto a |Indirizzo della persona giuridica, trovato nella proprietà dell'account di fatturazione|
| Indirizzo di fatturazione |Indirizzo del profilo di fatturazione ricevuto la fattura di fatturazione, trovato nella proprietà del profilo di fatturazione|
| Profilo di fatturazione |Il nome del profilo di fatturazione ricevuto la fattura |
| ORDINE number |Numero d'ordine d'acquisto, facoltativo, assegnato dall'utente per il monitoraggio |
| Numero di fattura |Un numero di fattura univoco, generata da Microsoft usato per scopi di rilevamento |
| Data fattura |Data in cui viene generata la fattura, in genere cinque a 12 giorni dopo la fine del ciclo di fatturazione. È possibile controllare la data di fatturazione nella fatturazione le proprietà del profilo.|
| Condizioni di pagamento |Il metodo di pagamento per la fattura di Microsoft. *NET 30 giorni* significa paghi entro 30 giorni dalla data della fattura. |

### <a name="billing-summary"></a>Riepilogo di fatturazione

Il **riepilogo di fatturazione** indica i costi in base al profilo di fatturazione dopo il periodo di fatturazione precedente, eventuali crediti che sono stati applicati, imposte e l'importo totale dovuto.

![Sezione di riepilogo di fatturazione](./media/billing-understand-your-invoice-mca/billingsummary.png)

| Termine | DESCRIZIONE |
| --- | --- |
| Charges|Numero totale di addebiti di Microsoft per questo profilo di fatturazione dall'ultimo periodo di fatturazione |
| Credits |È stato ricevuto da restituisce crediti |
| Applicate i crediti di Azure | Crediti di Azure che vengono applicati automaticamente ai costi di Azure ogni periodo di fatturazione |
| Subtotale |L'importo lordo delle imposte dovuto |
| Iva |Il tipo e la quantità della tassa che effettua un pagamento, a seconda del paese del profilo di fatturazione. Se non è necessario imposte da pagare, quindi, non si assisterà fiscali nella fattura. |
| Risparmio totale stimato |La quantità totale stimata che è stato salvato dagli sconti efficaci. Se applicabile, efficace fascia oraria con tariffe sono elencate sotto le voci nei dettagli dell'acquisto dalla sezione della fattura. |

### <a name="invoice-sections"></a>Sezioni della fattura

Per ogni sezione della fattura sotto il profilo di fatturazione, si noterà i costi, la quantità di crediti di Azure applicate, imposte e l'importo totale dovuto.

`Total = Charges - Azure Credit + Tax`

### <a name="details-by-invoice-section"></a>Dettagli in base alla sezione della fattura

I dettagli mostrano il costo per ogni sezione della fattura suddivisi in base all'ordine del prodotto. All'interno di ogni ordine di prodotto, costo è ripartito in base al tipo di servizio. È possibile trovare le spese giornaliere per i prodotti e servizi nel portale di Azure e di utilizzo di Azure e gli addebiti CSV. Per altre informazioni, vedere [comprendere gli addebiti nella fattura per un contratto di Microsoft dal cliente](billing-mca-understand-your-bill.md).

Importo totale dovuto per ogni gruppo di servizio viene calcolato sottraendo *crediti di Azure* dalla *crediti o addebiti* e l'aggiunta *Tax*:

<!-- `Total = Charges/Credits - Azure Credit + Tax` -->

![Dettagli in base alla sezione della fattura](./media/billing-understand-your-invoice-mca/invoicesectiondetails.png)

| Termine |DESCRIZIONE |
| --- | --- |
| Prezzo unitario | Il prezzo unitario effettivo del servizio (nel prezzo valuta) che viene usato per la frequenza di utilizzo. Questo è univoco per un prodotto, famiglia service, contatore e offerta. |
| Qty | Quantità acquistate o utilizzate durante il periodo di fatturazione |
| Gli addebiti/crediti | Importo degli addebiti dopo aver applicati i crediti o rimborsi |
| Credito di Azure | La quantità di crediti di Azure applicate ai crediti o addebiti|
| Aliquota d'imposta | Rate(s) imposte in base al paese |
| Imposte | Quantità di imposta applicata per acquistare base aliquota d'imposta |
| Totale | Importo totale dovuto per l'acquisto |

### <a name="how-to-pay"></a>Come pagare

Nella parte inferiore della fattura, sono disponibili istruzioni per pagare la fattura. Puoi pagare tramite controllo, rete, oppure online. Se si effettuano pagamenti online, è possibile usare una carta di credito/debito o i crediti di Azure, se applicabile.

### <a name="publisher-information"></a>Informazioni sull'editore

Se si dispone di servizi di terze parti nella fattura, il nome e l'indirizzo di ogni server di pubblicazione viene elencato nella parte inferiore della fattura.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificare l'accesso a un contratto di Microsoft dal cliente
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contatti

Se si hanno domande o assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

- [Comprendere gli addebiti nella fattura del proprio profilo di fatturazione](billing-mca-understand-your-bill.md)
- [Come ottenere la fattura e i dati di uso giornalieri di Azure](billing-download-azure-invoice-daily-usage-date.md)
- [Visualizzare i prezzi di Azure dell'organizzazione](billing-ea-pricing.md)
- [Visualizzare i documenti imposte per il profilo di fatturazione](billing-mca-download-tax-document.md)
