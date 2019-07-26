---
title: Informazioni sulla fattura del contratto clienti Microsoft in Azure
description: Informazioni su come leggere e comprendere la fattura del contratto clienti Microsoft in Azure
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/22/2019
ms.author: banders
ms.openlocfilehash: a5f77120c1d4e8a6721f3bc207132bee19a7772f
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68383548"
---
# <a name="terms-in-your-microsoft-customer-agreement-invoice"></a>Termini nella fattura del contratto clienti Microsoft

Questo articolo si applica a un account di fatturazione di Azure per un contratto per i clienti Microsoft. [Controllare se si dispone dell'accesso a un contratto per i clienti Microsoft](#check-access-to-a-microsoft-customer-agreement).

La fattura fornisce un riepilogo degli addebiti e le istruzioni per il pagamento. È disponibile per il download in formato PDF (Portable Document Format) dal [Portale di Azure](https://portal.azure.com/). In alternativa, è possibile riceverla tramite posta elettronica. Per altre informazioni, vedere [visualizzare e scaricare la fattura Microsoft Azure](billing-download-azure-invoice.md).

## <a name="billing-period"></a>Periodo di fatturazione

L'utente viene fatturato su base mensile. È possibile individuare il giorno del mese in cui si ricevono le fatture controllando la *Data* di fatturazione in proprietà profilo di fatturazione nel [portale di Azure](https://portal.azure.com/). I costi che si verificano tra la fine del periodo di fatturazione e la data di fatturazione sono inclusi nella fattura del mese successivo, perché si trovano nel periodo di fatturazione successivo. Le date di inizio e di fine del periodo di fatturazione per ogni fattura sono elencate nella fattura PDF sopra il **Riepilogo della fatturazione**.

## <a name="invoice-terms-and-descriptions"></a>Termini e descrizioni della fattura

Le sezioni seguenti elencano i termini importanti visualizzati nella fattura e forniscono le descrizioni per ogni termine.

### <a name="invoice-summary"></a>Riepilogo fattura

Il **Riepilogo della fattura** si trova nella parte superiore della prima pagina e visualizza le informazioni sul profilo di fatturazione e su come si paga.

![Sezione di riepilogo della fattura](./media/billing-understand-your-invoice-mca/invoicesummary.png)

| Termine | Descrizione |
| --- | --- |
| Venduto a |Indirizzo dell'entità legale, disponibile nelle proprietà dell'account di fatturazione|
| Indirizzo di fatturazione |Indirizzo di fatturazione del profilo di fatturazione che riceve la fattura, disponibile nelle proprietà del profilo di fatturazione|
| Profilo di fatturazione |Nome del profilo di fatturazione che riceve la fattura |
| P.O. number |Numero d'ordine d'acquisto, facoltativo, assegnato dall'utente per il monitoraggio |
| Numero fattura |Numero di fattura univoco generato da Microsoft utilizzato a scopo di rilevamento |
| Data fattura |Data di generazione della fattura, in genere da cinque a 12 giorni dopo la fine del ciclo di fatturazione. È possibile controllare la data di fatturazione nelle proprietà del profilo di fatturazione.|
| Condizioni di pagamento |Come paghi per la fattura Microsoft. *Net 30 giorni* significa che paghi entro 30 giorni dalla data di fatturazione. |

### <a name="billing-summary"></a>Riepilogo fatturazione

Il **Riepilogo di fatturazione** Mostra gli addebiti per il profilo di fatturazione dal periodo di fatturazione precedente, i crediti applicati, le imposte e l'importo totale dovuto.

![Sezione Riepilogo fatturazione](./media/billing-understand-your-invoice-mca/billingsummary.png)

| Termine | Descrizione |
| --- | --- |
| Addebiti|Numero totale di addebiti Microsoft per questo profilo di fatturazione dall'ultimo periodo di fatturazione |
| Riconoscimenti |Crediti ricevuti da restituzione |
| Crediti di Azure applicati | I crediti di Azure applicati automaticamente ad Azure addebitano ogni periodo di fatturazione |
| Subtotale |Importo pre-tax dovuto |
| Iva |Il tipo e la quantità di IVA pagata, a seconda del paese/area geografica del profilo di fatturazione. Se non è necessario pagare le imposte, non verrà visualizzata alcuna tassa per la fattura. |
| Risparmio totale stimato |Importo totale stimato salvato dagli sconti effettivi. Se applicabile, le tariffe di sconto valide sono elencate sotto la sezione articoli di acquisto nella sezione dettagli per fattura. |

### <a name="invoice-sections"></a>Sezioni della fattura

Per ogni sezione della fattura sotto il profilo di fatturazione, verranno visualizzati i costi, la quantità di crediti di Azure applicati, le imposte e l'importo totale dovuto.

`Total = Charges - Azure Credit + Tax`

### <a name="details-by-invoice-section"></a>Dettagli per sezione fattura

I dettagli mostrano il costo di ogni sezione della fattura suddivisa in base all'ordine del prodotto. All'interno di ogni ordine di prodotto, il costo viene suddiviso in base al tipo di servizio. È possibile trovare i prezzi giornalieri per i prodotti e i servizi nell'portale di Azure e nell'utilizzo di Azure e gli addebiti. Per ulteriori informazioni, vedere informazioni [sugli addebiti nella fattura per un contratto del cliente Microsoft](billing-mca-understand-your-bill.md).

L'importo totale dovuto per ogni famiglia di servizi viene calcolato sottraendo crediti di *Azure* da *crediti/* addebiti e aggiungendo le imposte:


![Dettagli per sezione fattura](./media/billing-understand-your-invoice-mca/invoicesectiondetails.png)

| Termine |DESCRIZIONE |
| --- | --- |
| Prezzo unitario | Prezzo unitario effettivo del servizio (in valuta prezzo) usato per valutare l'utilizzo. Questa operazione è univoca per un prodotto, una famiglia di servizi, un contatore e un'offerta. |
| Quantità | Quantità acquistata o consumata durante il periodo di fatturazione |
| Addebiti/crediti | Importo netto degli addebiti dopo l'applicazione di crediti/rimborsi |
| Credito di Azure | Quantità di crediti di Azure applicati agli addebiti/crediti|
| Aliquota d'imposta | Aliquote fiscali a seconda del paese/area geografica |
| Importo imposte | Importo delle imposte applicate all'acquisto in base al tasso d'imposta |
| Totale | Importo totale dovuto per l'acquisto |

### <a name="how-to-pay"></a>Come pagare

Nella parte inferiore della fattura sono disponibili istruzioni per il pagamento della fattura. Puoi pagare tramite check, Wire o online. Se si paga online, è possibile usare una carta di credito o crediti di Azure, se applicabile.

### <a name="publisher-information"></a>Informazioni sull'editore

Se si dispone di servizi di terze parti nella fattura, il nome e l'indirizzo di ogni editore sono elencati nella parte inferiore della fattura.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Controllare l'accesso a un contratto per i clienti Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sugli addebiti per la fattura del profilo di fatturazione](billing-mca-understand-your-bill.md)
- [Come ottenere la fattura e i dati di uso giornalieri di Azure](billing-download-azure-invoice-daily-usage-date.md)
- [Visualizza i prezzi di Azure dell'organizzazione](billing-ea-pricing.md)
- [Visualizzare i documenti fiscali per il profilo di fatturazione](billing-mca-download-tax-document.md)
