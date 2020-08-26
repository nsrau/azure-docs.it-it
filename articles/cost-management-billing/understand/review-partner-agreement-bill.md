---
title: Esaminare la fattura del Contratto Microsoft Partner - Azure
description: Informazioni su come controllare la fattura e l'utilizzo delle risorse e verificare gli addebiti per la fattura del Contratto Microsoft Partner.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: tutorial
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 9e5ecc67fe86afa15c66d175f0705818154588bf
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684355"
---
# <a name="tutorial-review-your-microsoft-partner-agreement-invoice"></a>Esercitazione: Esaminare la fattura del Contratto Microsoft Partner

 Nell'account di fatturazione per un Contratto Microsoft Partner, ogni mese viene generata una fattura per ogni profilo di fatturazione. La fattura include tutti gli addebiti dei clienti del mese precedente. Per capire a cosa si riferiscono gli addebiti riportati nella fattura, è possibile analizzare le singole transazioni nel portale di Azure. È anche possibile visualizzare le fatture nel portale di Azure e confrontare gli addebiti con il file dei dettagli di utilizzo.

Per altre informazioni, vedere [Come scaricare le fatture dal portale di Azure](download-azure-invoice.md).

Questa esercitazione si applica solo ai partner Azure con un Contratto Microsoft Partner.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Controllare le transazioni fatturate nel portale di Azure
> * Esaminare gli addebiti in sospeso per stimare la fattura successiva
> * Analizzare gli addebiti per l'utilizzo di Azure

## <a name="prerequisites"></a>Prerequisiti

È necessario avere accesso a un account di fatturazione per un Contratto Microsoft Partner.

Devono essere trascorsi più di 30 giorni dalla data della sottoscrizione di Azure. Azure fattura alla fine del periodo di fatturazione.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

- Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificare l'accesso a un Contratto del cliente Microsoft

Verificare il tipo di contratto per determinare se si dispone dell'accesso a un account di fatturazione per un Contratto Microsoft Partner.

Nella casella di ricerca del portale di Azure digitare *gestione dei costi e fatturazione* e quindi selezionare **Gestione dei costi e fatturazione**.

![Screenshot che mostra una ricerca nel portale di Azure](./media/review-partner-agreement-bill/billing-search-cost-management-billing.png)

Se si ha accesso a un solo ambito di fatturazione, selezionare **Proprietà** sul lato sinistro. Si ha accesso a un account di fatturazione per un Contratto Microsoft Partner se il tipo di account di fatturazione è **Contratto Microsoft Partner**.

![Screenshot che illustra il Contratto Microsoft Partner nella pagina delle proprietà](./media/review-partner-agreement-bill/billing-account-properties-partner-agreement.png)

Se si ha accesso a più ambiti di fatturazione, controllare il tipo nella colonna dell'account di fatturazione. Si ha accesso a un account di fatturazione per un Contratto Microsoft Partner se il tipo di account di fatturazione per uno degli ambiti è **Contratto Microsoft Partner**.

![Screenshot che illustra il Contratto Microsoft Partner nella pagina con l'elenco degli account di fatturazione](./media/review-partner-agreement-bill/mpa-in-the-list.png)

## <a name="review-invoiced-transactions-in-the-azure-portal"></a>Controllare le transazioni fatturate nel portale di Azure

In Gestione dei costi e fatturazione selezionare **Tutte le transazioni** sul lato sinistro della pagina. A seconda dell'accesso, può essere necessario selezionare un account di fatturazione, un profilo di fatturazione o un cliente, quindi selezionare **Tutte le transazioni**.

Nella pagina Tutte le transazioni vengono visualizzate le informazioni seguenti:

![Screenshot che mostra l'elenco delle transazioni fatturate](./media/review-partner-agreement-bill/all-transactions.png)

|Colonna  |Definizione  |
|---------|---------|
|Data     | La data della transazione  |
|ID fattura     | L'identificatore della fattura in cui viene fatturata la transazione. Se si invia una richiesta di supporto, condividere l'ID con il supporto tecnico di Azure per velocizzarne la gestione |
|Tipo di transazione     |  Il tipo di transazione, ad esempio acquisto, annullamento e addebiti per l'utilizzo  |
|Famiglia di prodotti     | La categoria del prodotto, ad esempio calcolo per le macchine virtuali o database per Database SQL di Azure|
|SDKU di prodotto     | Un codice univoco che identifica l'istanza del prodotto |
|Amount     |  L'importo della transazione      |
|Profilo di fatturazione     | La transazione viene visualizzata nella fattura di questo profilo di fatturazione |

Cercare un ID fattura per filtrare le transazioni in base alla fattura.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Esaminare gli addebiti in sospeso per stimare la fattura successiva

Gli addebiti vengono stimati e considerati in sospeso fino a quando non vengono fatturati. È possibile visualizzare gli addebiti in sospeso per il profilo di fatturazione del Contratto Microsoft Partner nel portale di Azure per stimare la fattura successiva. Gli addebiti in sospeso sono stime e non includono le imposte. Gli addebiti effettivi riportati nella fattura successiva variano a seconda degli addebiti in sospeso.

### <a name="view-pending-transactions"></a>Visualizzare le transazioni in sospeso

Una volta identificati gli addebiti in sospeso, è possibile analizzare le singole transazioni che li hanno generati. A questo punto, gli addebiti per l'utilizzo in sospeso non vengono visualizzati nella pagina Tutte le transazioni. È possibile visualizzarli nella pagina di sottoscrizioni di Azure.

Selezionare un profilo di fatturazione in Gestione costi e fatturazione. A seconda dell'accesso, potrebbe essere necessario selezionare un account di fatturazione. Nell'account di fatturazione selezionare **Profili di fatturazione** e quindi un profilo di fatturazione.

Selezionare **Tutte le transazioni** sul lato sinistro della pagina.

Cercare *In sospeso*. Usare il filtro **Intervallo di tempo** per visualizzare gli addebiti in sospeso per il mese corrente e per quello scorso.

<!-- ![Screenshot that shows the pending transactions list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-pending-transactions.png) -->

### <a name="view-pending-charges-by-customer"></a>Visualizzare gli addebiti in sospeso per cliente

Selezionare un profilo di fatturazione in Gestione costi e fatturazione. A seconda dell'accesso, potrebbe essere necessario selezionare un account di fatturazione. Nell'account di fatturazione selezionare **Profili di fatturazione** e quindi un profilo di fatturazione.

Selezionare **Clienti** nella parte sinistra della pagina.

<!-- ![screenshot of billing profile customers list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-customers.png) -->

La pagina Clienti visualizza gli addebiti del mese corrente e di quello precedente per ogni cliente associato al profilo di fatturazione. Gli addebiti da inizio mese sono quelli in sospeso per il mese corrente e vengono fatturati quando viene generata la fattura per il mese. Se la fattura relativa al mese scorso non è stata ancora generata, anche i relativi addebiti sono in sospeso.

### <a name="view-pending-usage-charges"></a>Visualizzare gli addebiti per l'utilizzo in sospeso

Selezionare un profilo di fatturazione in Gestione costi e fatturazione. A seconda dell'accesso, potrebbe essere necessario selezionare un account di fatturazione. Nell'account di fatturazione selezionare **Profili di fatturazione** e quindi un profilo di fatturazione.

Selezionare **Sottoscrizioni di Azure** nella parte sinistra della pagina. La pagina Sottoscrizioni di Azure visualizza gli addebiti del mese corrente e di quello scorso per ogni sottoscrizione del profilo di fatturazione. Gli addebiti da inizio mese sono quelli in sospeso per il mese corrente e vengono fatturati quando viene generata la fattura per il mese. Se la fattura relativa al mese scorso non è stata ancora generata, anche i relativi addebiti sono in sospeso.

<!--     ![Screenshot that shows the Azure subscriptions list for MPA billing profile](./media/billing-mpa-understand-your-bill/mpa-billing-profile-subscriptions-list.png) -->

## <a name="analyze-your-azure-usage-charges"></a>Analizzare gli addebiti per l'utilizzo di Azure

Usare il file CSV dei dati di utilizzo e addebiti di Azure per analizzare gli addebiti basati sull'utilizzo. È possibile filtrare il file relativo a utilizzo e addebiti di Azure per riconciliare gli addebiti per l'utilizzo per ogni prodotto elencato nel file PDF della fattura. Per visualizzare gli addebiti dettagliati per l'utilizzo per un prodotto specifico, filtrare in base alla colonna **product** nel file CSV relativo a utilizzo e addebiti di Azure in modo da includere solo il nome del prodotto desiderato.

È anche possibile filtrare in base alla colonna **customerName** nel file CSV relativo a utilizzo e addebiti di Azure per visualizzare gli addebiti per l'utilizzo giornaliero per ogni cliente. Per visualizzare gli addebiti per l'utilizzo giornaliero in base alla sottoscrizione di Azure, filtrare in base alla colonna **subscriptionName**.

## <a name="pay-your-bill"></a>Pagare la fattura

Nella parte inferiore della fattura sono riportate le istruzioni per il relativo pagamento. È possibile pagare tramite bonifico o assegno entro 60 giorni dalla data della fattura.

Se la fattura è già stata saldata, è possibile verificare lo stato del pagamento nella pagina Fatture del portale di Azure.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Controllare le transazioni fatturate nel portale di Azure
> * Esaminare gli addebiti in sospeso per stimare la fattura successiva
> * Analizzare gli addebiti per l'utilizzo di Azure

Informazioni sull'uso di Gestione costi di Azure per i partner.

> [!div class="nextstepaction"]
> [Introduzione a Gestione costi di Azure per i partner](../costs/get-started-partners.md)
