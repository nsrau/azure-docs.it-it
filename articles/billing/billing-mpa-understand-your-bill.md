---
title: Informazioni sugli addebiti riportati nella fattura del Contratto Microsoft Partner - Azure
description: Informazioni su come leggere e interpretare gli addebiti nella fattura.
author: jureid
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2019
ms.author: banders
ms.openlocfilehash: 9994ef6da379d0063930912a4d198b81623ddbe8
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223704"
---
# <a name="understand-charges-on-your-microsoft-partner-agreement-invoice"></a>Informazioni sugli addebiti riportati nella fattura del Contratto Microsoft Partner

 Nell'account di fatturazione per un Contratto Microsoft Partner, ogni mese viene generata una fattura per ogni profilo di fatturazione. La fattura include tutti gli addebiti dei clienti del mese precedente. Per capire a cosa si riferiscono gli addebiti riportati nella fattura, è possibile analizzare le singole transazioni nel portale di Azure. È anche possibile visualizzare le fatture nel portale di Azure. Per altre informazioni, vedere [Come scaricare le fatture dal portale di Azure](billing-download-azure-invoice.md).

Questo articolo di applica a un account di fatturazione per un Contratto Microsoft Partner. [Verificare di avere accesso a un Contratto Microsoft Partner](#check-access-to-a-microsoft-partner-agreement).

## <a name="view-transactions-for-an-invoice-in-the-azure-portal"></a>Visualizzare le transazioni di una fattura nel portale di Azure

1. Accedere al [portale di Azure](https://www.azure.com).

2. Cercare *Gestione dei costi e fatturazione*.

   <!--  ![Screenshot that shows Azure portal search for cost management + billing](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png) -->

3. Selezionare **Tutte le transazioni** sul lato sinistro della pagina. A seconda dell'accesso, può essere necessario selezionare un account di fatturazione, un profilo di fatturazione o un cliente, quindi selezionare **Tutte le transazioni**.

4. Nella pagina Tutte le transazioni vengono visualizzate le informazioni seguenti:

    <!-- ![Screenshot that shows the billed transactions list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-all-transactions.png) -->

    |Colonna  |Definizione  |
    |---------|---------|
    |Data     | La data della transazione  |
    |ID fattura     | L'identificatore della fattura in cui viene fatturata la transazione. Se si invia una richiesta di supporto, condividere l'ID con il supporto tecnico di Azure per velocizzarne la gestione |
    |Tipo di transazione     |  Il tipo di transazione, ad esempio acquisto, annullamento e addebiti per l'utilizzo  |
    |Famiglia di prodotti     | La categoria del prodotto, ad esempio calcolo per le macchine virtuali o database per il database SQL di Azure|
    |SKU di prodotto     | Un codice univoco che identifica l'istanza del prodotto |
    |Amount     |  L'importo della transazione      |
    |Profilo di fatturazione     | La transazione viene visualizzata nella fattura di questo profilo di fatturazione |

5. Cercare l'ID fattura per filtrare le transazioni in base alla fattura.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Esaminare gli addebiti in sospeso per stimare la fattura successiva

Gli addebiti vengono stimati e considerati in sospeso fino a quando non vengono fatturati. È possibile visualizzare gli addebiti in sospeso per il profilo di fatturazione del Contratto Microsoft Partner nel portale di Azure per stimare la fattura successiva. Gli addebiti in sospeso sono stime e non includono le imposte. Gli addebiti effettivi riportati nella fattura successiva variano a seconda degli addebiti in sospeso.

### <a name="view-pending-transactions"></a>Visualizzare le transazioni in sospeso

Una volta identificati gli addebiti in sospeso, è possibile analizzare le singole transazioni che li hanno generati. A questo punto, gli addebiti per l'utilizzo in sospeso non vengono visualizzati nella pagina Tutte le transazioni. È possibile visualizzarli nella pagina di sottoscrizioni di Azure. Per altre informazioni, vedere [Visualizzare gli addebiti per l'utilizzo in sospeso](#view-pending-usage-charges)

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Cercare *Gestione dei costi e fatturazione*.

   <!-- ![Screenshot that shows Azure portal search for cost management + billing](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png) -->

3. Selezionare un profilo di fatturazione. A seconda dell'accesso, potrebbe essere necessario selezionare un account di fatturazione. Nell'account di fatturazione selezionare **Profili di fatturazione** e quindi un profilo di fatturazione.

4. Selezionare **Tutte le transazioni** sul lato sinistro della pagina.

5. Cercare *In sospeso*. Usare il filtro **Intervallo di tempo** per visualizzare gli addebiti in sospeso per il mese corrente e per quello scorso.

   <!-- ![Screenshot that shows the pending transactions list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-pending-transactions.png) -->

### <a name="view-pending-charges-by-customer"></a>Visualizzare gli addebiti in sospeso per cliente

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Cercare *Gestione dei costi e fatturazione*.

3. Selezionare un profilo di fatturazione. A seconda dell'accesso, potrebbe essere necessario selezionare un account di fatturazione. Nell'account di fatturazione selezionare **Profili di fatturazione** e quindi un profilo di fatturazione.

4. Selezionare **Clienti** nella parte sinistra della pagina.

    <!-- ![screenshot of billing profile customers list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-customers.png) -->

5. La pagina Clienti visualizza gli addebiti del mese corrente e di quello precedente per ogni cliente associato al profilo di fatturazione. Gli addebiti da inizio mese sono quelli in sospeso per il mese corrente e vengono fatturati quando viene generata la fattura per il mese. Se la fattura relativa al mese scorso non è stata ancora generata, anche i relativi addebiti sono in sospeso.

### <a name="view-pending-usage-charges"></a>Visualizzare gli addebiti per l'utilizzo in sospeso

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Cercare *Gestione dei costi e fatturazione*.

3. Selezionare un profilo di fatturazione. A seconda dell'accesso, potrebbe essere necessario selezionare un account di fatturazione. Nell'account di fatturazione selezionare **Profili di fatturazione** e quindi un profilo di fatturazione.

4. Selezionare **Sottoscrizioni di Azure** nella parte sinistra della pagina.

5. La pagina Sottoscrizioni di Azure visualizza gli addebiti del mese corrente e di quello scorso per ogni sottoscrizione del profilo di fatturazione. Gli addebiti da inizio mese sono quelli in sospeso per il mese corrente e vengono fatturati quando viene generata la fattura per il mese. Se la fattura relativa al mese scorso non è stata ancora generata, anche i relativi addebiti sono in sospeso.

<!--     ![Screenshot that shows the Azure subscriptions list for MPA billing profile](./media/billing-mpa-understand-your-bill/mpa-billing-profile-subscriptions-list.png) -->

## <a name="analyze-your-azure-usage-charges"></a>Analizzare gli addebiti per l'utilizzo di Azure

Usare il file CSV dei dati di utilizzo e addebiti di Azure per analizzare gli addebiti basati sull'utilizzo. È possibile [scaricare il file CSV relativo a utilizzo e addebiti di Azure dal portale di Azure](billing-download-azure-daily-usage.md).

È possibile filtrare il file relativo a utilizzo e addebiti di Azure per riconciliare gli addebiti per l'utilizzo per ogni prodotto elencato nel file PDF della fattura. Per visualizzare gli addebiti dettagliati per l'utilizzo per un prodotto specifico, filtrare in base alla colonna **product** nel file CSV relativo a utilizzo e addebiti di Azure in modo da includere solo il nome del prodotto desiderato.

È anche possibile filtrare in base alla colonna **customerName** nel file CSV relativo a utilizzo e addebiti di Azure per visualizzare gli addebiti per l'utilizzo giornaliero per ogni cliente. Per visualizzare gli addebiti per l'utilizzo giornaliero in base alla sottoscrizione di Azure, filtrare in base alla colonna **subscriptionName**.


## <a name="pay-your-bill"></a>Pagare la fattura

Nella parte inferiore della fattura sono riportate le istruzioni per il relativo pagamento. È possibile pagare tramite bonifico o assegno entro 60 giorni dalla data della fattura.

Se la fattura è già stata saldata, è possibile verificare lo stato del pagamento nella pagina Fatture del portale di Azure.

## <a name="check-access-to-a-microsoft-partner-agreement"></a>Verificare l'accesso a un Contratto Microsoft Partner
[!INCLUDE [billing-check-mpa](../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla fattura e sull'utilizzo dettagliato, vedere:
