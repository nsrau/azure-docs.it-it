---
title: Analizzare gli addebiti imprevisti di Azure
description: Informazioni su come analizzare gli addebiti imprevisti relativi alla sottoscrizione di Azure.
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.subservice: cost-management
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: banders
ms.custom: contperfq1
ms.openlocfilehash: 2e0b9f10a321fce0b8ccc31bf914fe4f2995c49a
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2020
ms.locfileid: "91651905"
---
# <a name="analyze-unexpected-charges"></a>Analizzare gli addebiti imprevisti

L'infrastruttura di risorse cloud creata per l'organizzazione è probabilmente complessa. Molti tipi di risorse di Azure possono prevedere tipi di addebiti diversi. Le risorse di Azure potrebbero essere di proprietà di diversi team nell'organizzazione ed essere caratterizzate da tipi di modelli di fatturazione diversi in base alle risorse. Per comprendere meglio gli addebiti, iniziare l'analisi usando una o più delle strategie descritte nelle sezioni seguenti.

## <a name="review-invoice-for-resource-responsible-for-charge"></a>Esaminare la fattura della risorsa responsabile dell'addebito

Il modo in cui si acquistano i servizi di Azure consente di determinare la metodologia e gli strumenti disponibili quando si identifica la risorsa associata a un addebito. Per determinare quale metodologia si applica, è prima necessario [determinare il tipo di offerta di Azure](../costs/understand-cost-mgt-data.md#determine-your-offer-type). Quindi, identificare la categoria cliente nell'elenco di [offerte di Azure supportate](../costs/understand-cost-mgt-data.md#supported-microsoft-azure-offers).

Gli articoli seguenti illustrano le procedure dettagliate che spiegano come esaminare la fattura in base al tipo di cliente. In ogni articolo sono disponibili istruzioni su come scaricare un file CSV contenente i dettagli relativi all'utilizzo e ai costi per un determinato periodo di fatturazione.

- [Processo di revisione della fattura con pagamento in base al consumo](review-individual-bill.md#charges)
- [Processo di revisione della fattura del Contratto Enterprise](review-enterprise-agreement-bill.md)
- [Processo di revisione della fattura del Contratto del cliente Microsoft](review-customer-agreement-bill.md#analyze-your-azure-usage-charges)
- [Processo di revisione della fattura del Contratto Microsoft Partner](review-partner-agreement-bill.md#analyze-your-azure-usage-charges)

La fattura di Azure aggrega i costi per il mese in base al _contatore_. I contatori vengono usati per tenere traccia dell'utilizzo di una risorsa nel tempo e per calcolare la fattura. Quando si crea una singola risorsa di Azure, come una macchina virtuale, vengono create una o più istanze del contatore per la risorsa.

Filtrare il file CSV di utilizzo in base a _MeterName_, come illustrato nella fattura da analizzare per visualizzare tutte le voci che si applicano al contatore. Il valore di _InstanceID_ per la voce corrisponde alla risorsa di Azure effettiva che ha generato l'addebito.

Una volta identificata la risorsa in questione, è possibile usare Analisi dei costi in Gestione costi di Azure per analizzare ulteriormente i costi correlati alla risorsa. Per altre informazioni sull'uso dell'analisi dei costi, vedere [Avviare l'analisi dei costi](../costs/quick-acm-cost-analysis.md).

## <a name="review-invoiced-charges-in-cost-analysis"></a>Esaminare le tariffe fatturate in Analisi dei costi

Per visualizzare i dettagli della fattura nel portale di Azure, passare ad Analisi dei costi per l'ambito associato alla fattura che si sta analizzando. Selezionare la visualizzazione **Dettagli della fattura**, in cui vengono mostrati gli addebiti indicati nella fattura.

[![Esempio che mostra i dettagli della fattura](./media/analyze-unexpected-charges/invoice-details.png)](./media/analyze-unexpected-charges/invoice-details.png#lightbox)

La visualizzazione dei dettagli della fattura consente di identificare il servizio con costi imprevisti e determinare le risorse direttamente associate alla risorsa in Analisi dei costi. Se, ad esempio, si vogliono analizzare gli addebiti per il servizio Macchine virtuali, passare alla visualizzazione **Costi accumulati**. Impostare quindi la granularità su **Giornaliera**, filtrare gli addebiti per **Nome del servizio: Macchine virtuali** e raggruppare gli addebiti per **Risorsa**.

[![Esempio che mostra i costi accumulati per Macchine virtuali](./media/analyze-unexpected-charges/virtual-machines.png)](./media/analyze-unexpected-charges/virtual-machines.png#lightbox)

## <a name="identify-spikes-in-cost-over-time"></a>Identificare i picchi dei costi nel tempo

In alcuni casi è possibile che non si conoscano i costi recenti che hanno comportato variazioni rispetto agli addebiti fatturati. Per comprendere tali variazioni, è possibile usare l'analisi dei costi per [visualizzare un dettaglio giornaliero o mensile dei costi nel tempo](../costs/cost-analysis-common-uses.md#view-costs-per-day-or-by-month). Dopo aver creato la visualizzazione, raggruppare gli addebiti in base a **Servizio** o **Risorsa** per identificare le modifiche. È anche possibile modificare la visualizzazione in un **grafico a linee** per visualizzare meglio i dati.

![Esempio che mostra i costi nel tempo nell'analisi dei costi](./media/analyze-unexpected-charges/costs-over-time.png)

## <a name="determine-resource-pricing-and-billing-model"></a>Determinare il prezzo della risorsa e il modello di fatturazione

Una singola risorsa può accumulare addebiti generati da più prodotti e servizi di Azure. Per altre informazioni sui prezzi di ogni servizio di Azure, vedere la pagina relativa ai [prezzi di Azure per prodotto](https://azure.microsoft.com/pricing/#product-pricing). Ad esempio, per una singola macchina virtuale creata in Azure potrebbero essere creati i contatori seguenti per tenere traccia dell'utilizzo. Ognuno di essi potrebbe prevedere prezzi diversi.

- Ore di calcolo
- Ore indirizzo IP
- Trasferimento dati in entrata
- Trasferimento dati in uscita
- Disco gestito Standard
- Operazioni disco gestito Standard
- I/O Standard - Disco
- I/O Standard - Lettura BLOB in blocchi
- I/O Standard - Scrittura BLOB in blocchi
- I/O Standard - Eliminazione BLOB in blocchi

Dopo la creazione della VM, ogni contatore inizia a emettere record di utilizzo. L'utilizzo e il prezzo del contatore vengono monitorati nel sistema di misurazione di Azure. È possibile visualizzare i contatori usati per calcolare la fattura nel file CSV di utilizzo.

## <a name="find-people-responsible-for-the-resource-and-engage"></a>Individuare le persone responsabili della risorsa e coinvolgerle

In genere il team responsabile di una determinata risorsa è a conoscenza delle modifiche apportate a una risorsa. Il loro coinvolgimento è utile quando si identificano i motivi degli addebiti. Ad esempio, il team proprietario potrebbe avere creato la risorsa di recente, averne aggiornato lo SKU (modificando quindi la frequenza della risorsa) o aumentato il carico sulla risorsa a causa di modifiche del codice. Continuare a leggere le sezioni seguenti per altre tecniche per determinare il proprietario di una risorsa.

### <a name="analyze-the-audit-logs-for-the-resource"></a>Analizzare i log di controllo per la risorsa

Se si è autorizzati a visualizzare una risorsa, dovrebbe essere possibile accedere ai log di controllo. Esaminare i log per trovare l'utente responsabile delle ultime modifiche apportate a una risorsa. Per altre informazioni, vedere [Visualizzare e recuperare gli eventi del log attività di Azure](../../azure-monitor/platform/activity-log-view.md).

### <a name="analyze-user-permissions-to-the-resources-parent-scope"></a>Analizzare le autorizzazioni utente per l'ambito padre della risorsa

Gli utenti con accesso in scrittura a una sottoscrizione o a un gruppo di risorse hanno in genere informazioni sulle risorse create. Dovrebbero quindi essere in grado di spiegare lo scopo di una risorsa o di indicare la persona in possesso di tali informazioni. Per identificare gli utenti con autorizzazioni per un ambito della sottoscrizione, vedere [Visualizzare le assegnazioni di ruolo](../../role-based-access-control/check-access.md#view-role-assignments). È possibile usare un processo simile per i gruppi di risorse.

## <a name="get-help-to-identify-charges"></a>Richiedere assistenza per identificare gli addebiti

Se sono state usate le strategie precedenti e il motivo dell'addebito non è tuttora chiaro o se sono necessarie altre informazioni sui problemi di fatturazione, [Creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [Ottimizzare gli investimenti nel cloud con Gestione costi di Azure](../costs/cost-mgt-best-practices.md).