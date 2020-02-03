---
title: Controllare la fattura del Contratto del cliente Microsoft - Azure
description: Informazioni su come controllare la fattura e l'utilizzo delle risorse e verificare gli addebiti per la fattura del Contratto del cliente Microsoft.
author: bandersmsft
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2019
ms.author: banders
ms.openlocfilehash: 3cbc4ab4f0e2ad18c7d1d430ed5501e23b5f5f4d
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2020
ms.locfileid: "75983726"
---
# <a name="tutorial-review-your-microsoft-customer-agreement-invoice"></a>Esercitazione: Controllare la fattura del Contratto del cliente Microsoft

È possibile controllare gli addebiti riportati nella fattura analizzando le singole transazioni. Nell'account di fatturazione per un Contratto del cliente Microsoft, ogni mese viene generata una fattura per ogni profilo di fatturazione. La fattura include tutti gli addebiti del mese precedente. È possibile visualizzare le fatture nel portale di Azure e confrontare gli addebiti con il file dei dettagli di utilizzo.

Questa esercitazione si applica solo ai clienti di Azure con un Contratto del cliente Microsoft.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Controllare le transazioni fatturate nel portale di Azure
> * Esaminare gli addebiti in sospeso per stimare la fattura successiva
> * Analizzare gli addebiti per l'utilizzo di Azure

## <a name="prerequisites"></a>Prerequisites

È necessario avere un account di fatturazione per un Contratto del cliente Microsoft.

È necessario avere accesso a un Contratto del cliente Microsoft. È necessario usare un profilo di fatturazione con il ruolo di proprietario, collaboratore, lettore o gestione fatture per visualizzare le informazioni sull'utilizzo e la fatturazione. Per altre informazioni sui ruoli di fatturazione per i Contratti del cliente Microsoft, vedere [Ruoli e attività del profilo di fatturazione](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

Devono essere trascorsi più di 30 giorni dalla data della sottoscrizione di Azure. Azure fattura alla fine del periodo di fatturazione.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

- Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificare l'accesso a un Contratto del cliente Microsoft

Verificare il tipo di contratto per determinare se si dispone dell'accesso a un account di fatturazione per un Contratto del cliente Microsoft.

Nella casella di ricerca del portale di Azure digitare *gestione dei costi e fatturazione* e quindi selezionare **Gestione dei costi e fatturazione**.

![Screenshot che mostra la ricerca di Gestione dei costi e fatturazione nel portale di Azure](./media/review-customer-agreement-bill/billing-search-cost-management-billing.png)

Se si ha accesso a un solo ambito di fatturazione, selezionare **Proprietà** sul lato sinistro. Si ha accesso a un account di fatturazione per un Contratto del cliente Microsoft se il tipo di account di fatturazione è **Contratto del cliente Microsoft**.

![Screenshot che mostra il Contratto del cliente Microsoft nella pagina Proprietà](./media/review-customer-agreement-bill/billing-mca-property.png)

Se si ha accesso a più ambiti di fatturazione, controllare il tipo nella colonna dell'account di fatturazione. Si ha accesso a un account di fatturazione per un Contratto del cliente Microsoft se il tipo di account di fatturazione per uno degli ambiti è **Contratto del cliente Microsoft**.

![Screenshot che mostra il Contratto del cliente Microsoft nella pagina con l'elenco degli account di fatturazione](./media/review-customer-agreement-bill/billing-mca-in-the-list.png)

## <a name="review-invoiced-transactions-in-the-azure-portal"></a>Controllare le transazioni fatturate nel portale di Azure

Nel portale di Azure selezionare **Tutte le transazioni** sul lato sinistro della pagina. A seconda dell'accesso, può essere necessario selezionare un account di fatturazione, un profilo di fatturazione o una sezione della fattura, quindi selezionare **Tutte le transazioni**.

Nella pagina Tutte le transazioni vengono visualizzate le informazioni seguenti:

![Screenshot che mostra l'elenco delle transazioni fatturate](./media/review-customer-agreement-bill/mca-billed-transactions-list.png)

|Colonna  |Definizione  |
|---------|---------|
|Data     | La data della transazione  |
|ID fattura     | L'identificatore della fattura in cui viene fatturata la transazione. Se si invia una richiesta di supporto, condividere l'ID con il supporto tecnico di Azure per velocizzarne la gestione |
|Tipo di transazione     |  Il tipo di transazione, ad esempio acquisto, annullamento e addebiti per l'utilizzo  |
|Famiglia di prodotti     | La categoria del prodotto, ad esempio calcolo per le macchine virtuali o database per il database SQL di Azure|
|SKU di prodotto     | Un codice univoco che identifica l'istanza del prodotto |
|Amount     |  L'importo della transazione      |
|Sezione della fattura     | La transazione viene visualizzata in questa sezione della fattura del profilo di fatturazione |
|Profilo di fatturazione     | La transazione viene visualizzata nella fattura di questo profilo di fatturazione |

Cercare un ID fattura per filtrare le transazioni in base alla fattura.

### <a name="view-transactions-by-invoice-sections"></a>Visualizzare le transazioni per sezioni della fattura

Le sezioni della fattura consentono di organizzare i costi per la fattura di un profilo di fatturazione. Per altre informazioni. Quando viene generata una fattura, al suo interno vengono visualizzati gli addebiti per tutte le sezioni del profilo di fatturazione.

L'immagine seguente mostra gli addebiti per la sezione Accounting Dept di una fattura di esempio.

![Immagine di esempio che mostra i dettagli in base alle informazioni della sezione della fattura](./media/review-customer-agreement-bill/invoicesection-details.png)

Una volta identificati gli addebiti per una sezione della fattura, è possibile visualizzare le transazioni nel portale di Azure per analizzarli.

Passare alla pagina Tutte le transazioni nel portale di Azure per visualizzare le transazioni di una fattura.

Filtrare in base al nome della sezione della fattura per visualizzare le transazioni.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Esaminare gli addebiti in sospeso per stimare la fattura successiva

Nell'account di fatturazione per un Contratto del cliente Microsoft, gli addebiti vengono stimati e considerati in sospeso fino a quando non vengono fatturati. È possibile visualizzare gli addebiti in sospeso nel portale di Azure per stimare la fattura successiva. Gli addebiti in sospeso sono stime e non includono le imposte. Gli addebiti effettivi riportati nella fattura successiva variano a seconda degli addebiti in sospeso.

### <a name="view-summary-of-pending-charges"></a>Visualizzare il riepilogo degli addebiti in sospeso

Accedere al [portale di Azure](https://portal.azure.com).

Selezionare un profilo di fatturazione. A seconda dell'accesso, potrebbe essere necessario selezionare un account di fatturazione. Nell'account di fatturazione selezionare **Profili di fatturazione** e quindi un profilo di fatturazione.

Selezionare la scheda **Riepilogo** nella parte superiore della schermata.

Nella sezione relativa agli addebiti vengono visualizzati gli addebiti da inizio mese e quelli del mese scorso.

![Screenshot che mostra la ricerca di Gestione dei costi e fatturazione nel portale di Azure](./media/review-customer-agreement-bill/mca-billing-profile-summary.png)

Gli addebiti da inizio mese sono quelli in sospeso per il mese corrente e vengono fatturati quando viene generata la fattura per il mese. Se la fattura relativa al mese scorso non è stata ancora generata, anche i relativi addebiti sono in sospeso e verranno visualizzati nella fattura successiva.

### <a name="view-pending-transactions"></a>Visualizzare le transazioni in sospeso

Una volta identificati gli addebiti in sospeso, è possibile analizzare le singole transazioni che li hanno generati. A questo punto, gli addebiti per l'utilizzo in sospeso non vengono visualizzati nella pagina Tutte le transazioni. È possibile visualizzarli nella pagina di sottoscrizioni di Azure.

Accedere al [portale di Azure](https://portal.azure.com).

Nella casella di ricerca del portale di Azure digitare *gestione dei costi e fatturazione* e quindi selezionare **Gestione dei costi e fatturazione**.

Selezionare un profilo di fatturazione. A seconda dell'accesso, potrebbe essere necessario selezionare un account di fatturazione. Nell'account di fatturazione selezionare **Profili di fatturazione** e quindi un profilo di fatturazione.

Selezionare **Tutte le transazioni** sul lato sinistro della pagina.

Cercare *In sospeso*. Usare il filtro **Intervallo di tempo** per visualizzare gli addebiti in sospeso per il mese corrente e per quello scorso.

![Screenshot che mostra l'elenco di transazioni in sospeso](./media/review-customer-agreement-bill/mca-pending-transactions-list.png)

### <a name="view-pending-usage-charges"></a>Visualizzare gli addebiti per l'utilizzo in sospeso

Accedere al [portale di Azure](https://portal.azure.com).

Nella casella di ricerca del portale di Azure digitare *gestione dei costi e fatturazione* e quindi selezionare **Gestione dei costi e fatturazione**.

Selezionare un profilo di fatturazione. A seconda dell'accesso, potrebbe essere necessario selezionare un account di fatturazione. Nell'account di fatturazione selezionare **Profili di fatturazione** e quindi un profilo di fatturazione.

Selezionare **Tutte le sottoscrizioni** sul lato sinistro della pagina.

La pagina Sottoscrizioni di Azure visualizza gli addebiti del mese corrente e di quello scorso per ogni sottoscrizione del profilo di fatturazione. Gli addebiti da inizio mese sono quelli in sospeso per il mese corrente e vengono fatturati quando viene generata la fattura per il mese. Se la fattura relativa al mese scorso non è stata ancora generata, anche i relativi addebiti sono in sospeso.

![Screenshot che mostra l'elenco delle sottoscrizioni di Azure per il profilo di fatturazione](./media/review-customer-agreement-bill/mca-billing-profile-subscriptions-list.png)

## <a name="analyze-your-azure-usage-charges"></a>Analizzare gli addebiti per l'utilizzo di Azure

Usare il file CSV dei dati di utilizzo e addebiti di Azure per analizzare gli addebiti basati sull'utilizzo. È possibile scaricare il file per una fattura o per gli addebiti in sospeso.

### <a name="download-your-invoice-and-usage-details"></a>Scaricare la fattura e i dettagli di utilizzo

A seconda dell'accesso, potrebbe essere necessario selezionare un account di fatturazione o un profilo di fatturazione in Gestione dei costi e fatturazione. Nel menu a sinistra selezionare **Fatture** in **Fatturazione**. Nella griglia della fattura trovare la riga della fattura da scaricare. Fare clic sul simbolo del download o sui puntini di sospensione (...) alla fine della riga. Nella finestra **Download** scaricare il file dei dettagli di utilizzo e la fattura.

### <a name="view-detailed-usage-by-invoice-section"></a>Visualizzare l'utilizzo dettagliato in base alla sezione della fattura

È possibile filtrare il file dei dati di utilizzo e addebiti di Azure per riconciliare gli addebiti per l'utilizzo per le sezioni della fattura.

Le informazioni seguenti illustrano come riconciliare gli addebiti per le risorse di calcolo per la sezione Accounting Dept della fattura:

![Immagine di esempio che mostra i dettagli in base alle informazioni della sezione della fattura](./media/review-customer-agreement-bill/invoicesection-details.png)

| PDF della fattura | CSV dei dati di utilizzo e addebiti di Azure |
| --- | --- |
|Accounting Dept |invoiceSectionName |
|Addebiti per l'utilizzo - Piano di Microsoft Azure |productOrderName |
|Calcolo |serviceFamily |

Filtrare la colonna **invoiceSectionName** nel file CSV in base a **Accounting Dept**. Filtrare quindi la colonna **productOrderName** nel file CSV in base a **Piano di Microsoft Azure**. Filtrare infine la colonna **serviceFamily** del file CSV in base a **Microsoft.Compute**.

![Screenshot che mostra il file dei dati di utilizzo e addebiti filtrato per sezione della fattura](./media/review-customer-agreement-bill/billing-usage-file-filtered-by-invoice-section.png)

### <a name="view-detailed-usage-by-subscription"></a>Visualizzare l'utilizzo dettagliato per sottoscrizione

È possibile filtrare il file dei dati di utilizzo e addebiti di Azure per riconciliare gli addebiti per l'utilizzo per le sottoscrizioni. Una volta identificati gli addebiti per una sottoscrizione, usare il file CSV dei dati di utilizzo e addebiti di Azure per analizzarli.

L'immagine seguente mostra l'elenco di sottoscrizioni nel portale di Azure.

![Screenshot che mostra l'elenco delle sottoscrizioni di Azure per il profilo di fatturazione](./media/review-customer-agreement-bill/mca-billing-profile-subscriptions-list-highlighted.png)

Filtrare la colonna **subscriptionName** nel file CSV dei dati di utilizzo e addebiti di Azure in base a **WA_Subscription** per visualizzare gli addebiti dettagliati per l'utilizzo relativi alla sottoscrizione WA_Subscription.

![Screenshot che mostra il file dei dati di utilizzo e addebiti filtrato per sottoscrizione](./media/review-customer-agreement-bill/billing-usage-file-filtered-by-subscription.png)

## <a name="pay-your-bill"></a>Pagare la fattura

Nella parte inferiore della fattura sono riportate le istruzioni per il relativo pagamento. [Informazioni sulla modalità di pagamento](mca-understand-your-invoice.md#how-to-pay).

Se la fattura è già stata saldata, è possibile verificare lo stato del pagamento nella pagina Fatture del portale di Azure.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Controllare le transazioni fatturate nel portale di Azure
> * Esaminare gli addebiti in sospeso per stimare la fattura successiva
> * Analizzare gli addebiti per l'utilizzo di Azure

Completare la guida di avvio rapido per iniziare a usare l'analisi dei costi.

> [!div class="nextstepaction"]
> [Avviare l'analisi dei costi](../costs/quick-acm-cost-analysis.md)
