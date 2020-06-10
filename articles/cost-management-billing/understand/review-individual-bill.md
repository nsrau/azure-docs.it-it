---
title: Controllare una singola fattura di Azure
description: Informazioni su come comprendere la fattura e l'utilizzo delle risorse e verificare gli addebiti per una singola sottoscrizione di Azure.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.topic: tutorial
ms.date: 05/28/2020
ms.author: banders
ms.openlocfilehash: 5a9e21cacb17743cbb8324a06c083c52aa261eb9
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84194156"
---
# <a name="tutorial-review-your-individual-azure-bill"></a>Esercitazione: Controllare una singola fattura di Azure

Questo articolo consente di comprendere e controllare una fattura di Azure. Per ogni periodo di fatturazione si riceve in genere una fattura tramite posta elettronica. La fattura è una rappresentazione della fattura di Azure. Le stesse informazioni sui costi riportate nella fattura sono disponibili nella portale di Azure. In questa esercitazione si confronterà una fattura con il file di utilizzo giornaliero dettagliato e con l'analisi dei costi nel portale di Azure.

Questa esercitazione si applica solo ai clienti di Azure con una singola sottoscrizione. In genere le sottoscrizioni singole sono quelle con tariffe con pagamento in base al consumo acquistate direttamente dal sito Web di Azure.

Per informazioni sugli addebiti imprevisti, vedere [Analizzare gli addebiti imprevisti](https://docs.microsoft.com/azure/cost-management-billing/manage/getting-started#analyze-unexpected-charges). In alternativa, se è necessario annullare la sottoscrizione di Azure, vedere [Annullare la sottoscrizione di Azure](../manage/cancel-azure-subscription.md).

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Confrontare gli addebiti fatturati con il file di utilizzo
> * Confrontare gli addebiti e l'utilizzo nell'analisi dei costi

## <a name="prerequisites"></a>Prerequisiti

È necessario avere un account di fatturazione del *Programma di Microsoft Online Services* a pagamento. L'account viene creato quando ci si iscrive ad Azure tramite il sito Web di Azure. Ad esempio, se si ha un [account con tariffe con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/) o se si è un [sottoscrittore di Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

Le fatture per gli [account gratuiti di Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) vengono create solo quando viene superato l'importo del credito mensile.

Devono essere trascorsi più di 30 giorni dalla data della sottoscrizione di Azure. Azure fattura alla fine del periodo di fatturazione.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

- Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="compare-invoiced-charges-with-usage-file"></a>Confrontare gli addebiti fatturati con il file di utilizzo

<a name="charges"></a>

Il primo passaggio per confrontare l'utilizzo e i costi consiste nello scaricare i file della fattura e di utilizzo. Il file con i dettagli di utilizzo in formato CSV illustra gli addebiti per periodo di fatturazione e utilizzo giornaliero. Non include informazioni fiscali. Per scaricare i file, è necessario essere un amministratore account o avere il ruolo Proprietario.

Nel portale di Azure digitare *sottoscrizioni* nella casella di ricerca e quindi fare clic su [Sottoscrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

[![Passare alle sottoscrizioni](./media/review-individual-bill/navigate-subscriptions.png)](./media/review-individual-bill/navigate-subscriptions.png#lightbox)

Nell'elenco delle sottoscrizioni fare clic sulla sottoscrizione.

In **Fatturazione** fare clic su **Fatture**.

Nell'elenco di fatture cercare quella che si vuole scaricare e quindi fare clic sul simbolo di download. Potrebbe essere necessario modificare l'intervallo di tempo per visualizzare le fatture meno recenti. Potrebbero essere necessari alcuni minuti per generare il file dei dettagli di utilizzo e la fattura.

![Schermata che mostra i periodi di fatturazione, l'opzione per il download e gli addebiti totali per ogni periodo di fatturazione](./media/review-individual-bill/download-invoice.png)

Nella finestra Scarica l'utilizzo e gli addebiti fare clic su **Scarica CSV** e **Scarica fattura**.

![Schermata che mostra la pagina per scaricare la fattura e i dati di utilizzo](./media/review-individual-bill/usageandinvoice.png)

Se viene visualizzato **Non disponibile**, esistono diversi motivi per cui non vengono visualizzati i dettagli di utilizzo o una fattura:

- Sono passati meno di trenta giorni dalla data della sottoscrizione di Azure.
- Non c'è alcun utilizzo per il periodo di fatturazione.
- Non è stata ancora generata una fattura. Attendere la fine del periodo di fatturazione.
- Non si è autorizzati a visualizzare le fatture. Se non si è l'amministratore account, è possibile che non vengano visualizzate le fatture precedenti. Per altre informazioni sull'accesso alle informazioni di fatturazione, vedere [Manage access to Azure billing using roles](../manage/manage-billing-access.md) (Gestire l'accesso alla fatturazione di Azure usando i ruoli).
- Se si dispone di una versione di valutazione gratuita o di un credito mensile per la sottoscrizione che non è stato superato, non si riceverà alcuna fattura a meno che non sia stato sottoscritto un Contratto del cliente Microsoft.

Controllare quindi gli addebiti. La fattura mostra i valori delle imposte e gli addebiti per l'utilizzo.

![Esempio di una fattura di Azure](./media/review-individual-bill/invoice-usage-charge.png)

Aprire il file di utilizzo CSV scaricato. Alla fine del file, sommare il valore di tutti gli elementi nella colonna *Cost*.

![Esempio di file di utilizzo con costo sommato](./media/review-individual-bill/usage-file-usage-charges.png)

 Il valore sommato della colonna *Cost* deve corrispondere esattamente al costo degli *addebiti per l'utilizzo* nella fattura.

Gli addebiti relativi all'utilizzo vengono visualizzati a livello di contatore. I termini seguenti hanno lo stesso significato sia nella fattura che nel file con i dettagli di utilizzo. Ad esempio, il ciclo di fatturazione indicato nella fattura corrisponde al periodo di fatturazione indicato nel file con i dettagli di utilizzo.

| Fattura (PDF) | Dettagli di utilizzo (CSV)|
| --- | --- |
|Ciclo di fatturazione | BillingPeriodStartDate BillingPeriodEndDate |
|Nome |Categoria misuratore |
|Type |Sottocategoria contatore |
|Risorsa |MeterName |
|Region |MeterRegion |
|Consumato | Quantità |
|Incluso |Quantità inclusa |
|Fatturabile |Quantità in eccesso |
|Tariffa | EffectivePrice|
| valore | Costi |

La sezione **Addebiti per l'utilizzo** della fattura mostra il valore totale (costo) per ogni contatore utilizzato durante il periodo di fatturazione. L'immagine seguente illustra ad esempio l'addebito per l'utilizzo del servizio Archiviazione di Azure per la risorsa *P10 Disks*.

![Addebiti di utilizzo nella fattura](./media/review-individual-bill/invoice-usage-charges.png)

Nel file di utilizzo CSV filtrare per *MeterName* per la risorsa corrispondente visualizzata nella fattura. Quindi, sommare il valore della colonna *Cost* per gli elementi nella colonna. Di seguito è riportato un esempio.

![Valore sommato del file di utilizzo per MeterName](./media/review-individual-bill/usage-file-usage-charge-resource.png)

Il valore sommato della colonna *Cost* deve corrispondere esattamente al costo degli *addebiti per l'utilizzo* per la singola risorsa addebitata nella fattura.

Per altre informazioni, vedere [Comprendere la fattura di Azure](understand-invoice.md) e [Comprendere il file di utilizzo dettagliato di Azure](understand-usage.md).

## <a name="compare-charges-and-usage-in-cost-analysis"></a>Confrontare gli addebiti e l'utilizzo nell'analisi dei costi

L'analisi dei costi nel portale di Azure consente anche di verificare gli addebiti. Per ottenere una rapida panoramica dell'utilizzo e degli addebiti fatturati, selezionare la sottoscrizione nella [pagina Sottoscrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) del portale di Azure. Fare quindi clic su **Analisi dei costi** e quindi nell'elenco viste fare clic su **Dettagli della fattura**.

![Esempio che mostra la selezione Dettagli della fattura](./media/review-individual-bill/cost-analysis-select-invoice-details.png)

Quindi, nell'elenco intervallo di date selezionare un periodo di tempo per la fattura. Aggiungere un filtro per il numero di fattura e quindi selezionare il valore InvoiceNumber corrispondente a quello della fattura. L'analisi dei costi mostra i dettagli sui costi per gli elementi fatturati.

![Esempio che mostra i dettagli sui costi fatturati nell'analisi dei costi](./media/review-individual-bill/cost-analysis-service-usage-charges.png)

I costi mostrati nell'analisi dei costi devono corrispondere esattamente al costo degli *addebiti per l'utilizzo* per la singola risorsa addebitata nella fattura.

![Addebiti di utilizzo nella fattura](./media/review-individual-bill/invoice-usage-charges.png)

## <a name="external-services-billed-separately"></a><a name="external"></a>Servizi esterni fatturati separatamente

I servizi esterni o gli addebiti per Marketplace riguardano le risorse create da fornitori di software di terze parti. Tali risorse sono disponibili per l'uso in Azure Marketplace. Un firewall Barracuda è ad esempio una risorsa di Azure Marketplace offerta da terze parti. Tutti gli addebiti per il firewall e i relativi contatori corrispondenti verranno visualizzati come addebiti per servizi esterni.

Gli addebiti per i servizi esterni vengono fatturati separatamente. Questi addebiti non compaiono nella fattura di Azure. Per altre informazioni, vedere [Informazioni sugli addebiti per i servizi esterni](understand-azure-marketplace-charges.md).

### <a name="resources-billed-by-usage-meters"></a>Risorse fatturate in base a contatori di utilizzo

Azure non fattura direttamente in base al costo della risorsa. Gli addebiti relativi a una risorsa vengono calcolati con uno o più contatori, che vengono usati per tenere traccia dell'utilizzo di una risorsa per tutta la sua durata. Questi contatori vengono quindi usati per calcolare la fattura.

Quando si crea una singola risorsa di Azure, come una macchina virtuale, vengono create una o più istanze del contatore. I contatori vengono usati per tenere traccia dell'utilizzo della risorsa nel tempo. Ogni contatore genera record di utilizzo che vengono usati per elaborare la fattura.

Ad esempio, in una singola macchina virtuale creata in Azure possono essere creati i contatori seguenti per tenere traccia dell'utilizzo:

- Ore di calcolo
- Ore indirizzo IP
- Importazione dati
- Trasferimento dati in uscita
- Managed Disks Standard
- Operazioni disco gestito Standard
- I/O Standard - Disco
- I/O Standard - Lettura BLOB in blocchi
- I/O Standard - Scrittura BLOB in blocchi
- I/O Standard - Eliminazione BLOB in blocchi

Dopo la creazione della VM, ogni contatore inizia a emettere record di utilizzo. L'utilizzo e il costo indicato dal contatore vengono registrati nel sistema di misurazione di Azure.

È possibile visualizzare i contatori usati per calcolare la fattura nel file CSV di utilizzo, come nell'esempio precedente.

## <a name="pay-your-bill"></a><a name="payment"></a>Pagare la fattura

Se si configura una carta di credito come metodo di pagamento, il pagamento viene addebitato automaticamente entro 10 giorni dalla fine del periodo di fatturazione. Nell'estratto conto della carta di credito la voce sarà **MSFT Azure**.

Per modificare la carta di credito su cui viene effettuato l'addebito, vedere [Aggiungere, aggiornare o rimuovere una carta di credito per Azure](../manage/change-credit-card.md).

Se si [paga tramite fattura](../manage/pay-by-invoice.md), inviare il pagamento al destinatario elencato nella parte inferiore della fattura.

Per controllare lo stato del pagamento, [creare un ticket di supporto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Confrontare gli addebiti fatturati con il file di utilizzo
> * Confrontare gli addebiti e l'utilizzo nell'analisi dei costi

Completare la guida di avvio rapido per iniziare a usare l'analisi dei costi.

> [!div class="nextstepaction"]
> [Avviare l'analisi dei costi](../costs/quick-acm-cost-analysis.md)
