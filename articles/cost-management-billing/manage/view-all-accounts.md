---
title: Visualizzare gli account di fatturazione nel portale di Azure | Microsoft Docs
description: Informazioni su come visualizzare gli account di fatturazione nel portale di Azure.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: ''
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 2768d6e146a37e86bb36353f661179ebd7b5033d
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75994129"
---
# <a name="billing-accounts-and-scopes-in-the-azure-portal"></a>Account e ambiti di fatturazione nel portale di Azure

L'account di fatturazione viene creato quando ci si iscrive per usare Azure. Viene usato per gestire le fatture e i pagamenti, oltre che per tenere traccia dei costi. È possibile avere accesso a più account di fatturazione. Ad esempio, l'iscrizione ad Azure potrebbe essere stata eseguita per i progetti personali. Si potrebbe anche avere accesso tramite il contratto Enterprise Agreement o il Contratto del cliente Microsoft dell'organizzazione. Per ognuno di questi scenari, si avrà un account di fatturazione distinto.

Portale di Azure supporta il tipo di account di fatturazione seguente:

- **Programma di Microsoft Online Services**: un account di fatturazione per un programma di Microsoft Online Services viene creato quando si esegue l'iscrizione ad Azure tramite il sito Web di Azure. ad esempio, quando ci si iscrive per ricevere un [account Azure gratuito](https://azure.microsoft.com/offers/ms-azr-0044p/), un [account con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/) o un account di [sottoscrittore di Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Enterprise Agreement**: viene creato un account di fatturazione per un Enterprise Agreement quando l'organizzazione firma un [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) per l'uso di Azure.

- **Contratto del cliente Microsoft**: un account di fatturazione per un contratto cliente Microsoft viene creato quando l'organizzazione collabora con un rappresentante Microsoft per firmare un contratto per i clienti Microsoft. Alcuni clienti in aree selezionate, che effettuano l'iscrizione tramite il sito Web di Azure per un [account con tariffe](https://azure.microsoft.com/offers/ms-azr-0003p/) a consumo o un [account Azure gratuito](https://azure.microsoft.com/offers/ms-azr-0044p/) possono avere un account di fatturazione per un contratto cliente Microsoft. Per altre informazioni, vedere [Introduzione all'account di fatturazione per il Contratto del cliente Microsoft](../understand/mca-overview.md).

- **Contratto**per i partner Microsoft: viene creato un account di fatturazione per un contratto partner Microsoft per i partner Cloud Solution Provider (CSP) per gestire i clienti nella nuova esperienza commerciale. I partner devono avere almeno un cliente con un [piano di Azure](https://docs.microsoft.com/partner-center/purchase-azure-plan) per gestire il proprio account di fatturazione nel portale di Azure. Per ulteriori informazioni, vedere [la pagina relativa all'introduzione al proprio account di fatturazione per il contratto partner Microsoft](../understand/mpa-overview.md).

Per determinare il tipo di account di fatturazione, vedere [Verificare il tipo di account di fatturazione](#check-the-type-of-your-account).

## <a name="scopes-for-billing-accounts"></a>Ambiti degli account di fatturazione
Un ambito è un nodo all'interno di un account di fatturazione utilizzato per visualizzare e gestire la fatturazione. È il punto in cui si gestiscono i dati di fatturazione, i pagamenti, le fatture e la gestione degli account generali.

### <a name="microsoft-online-services-program"></a>Programma dei Microsoft Online Services

 ![Screenshot che mostra la gerarchia di MOSP](./media/view-all-accounts/mosp-hierarchy.png)

|Ambito  |Definizione  |
|---------|---------|
|Account di fatturazione     | Rappresenta un accordo accettato da un cliente per l'uso di Azure. Contiene una o più sottoscrizioni.  |
|Sottoscrizione     |  Rappresenta un raggruppamento di risorse di Azure. La fattura viene generata in questo ambito. Altre informazioni sulla fatturazione, ad esempio i metodi di pagamento e l'indirizzo di utilizzo, sono associate a questo ambito.|

### <a name="enterprise-agreement"></a>Contratto Enterprise Agreement

![Screenshot che mostra la gerarchia EA](./media/view-all-accounts/ea-hierarchy.png)

|Ambito  |Definizione  |
|---------|---------|
|Account di fatturazione    | Rappresenta una registrazione del contratto Enterprise Agreement. Contiene uno o più reparti e account. La fattura viene generata in questo ambito. |
|department     |  Raggruppamento facoltativo di account per segmentare i costi in raggruppamenti logici e impostare il budget.     |
|Account     |  Rappresenta un singolo proprietario dell'account. I proprietari dell'account hanno le autorizzazioni per creare e gestire le sottoscrizioni di Azure fatturate per la registrazione. |

### <a name="microsoft-customer-agreement"></a>Contratto del cliente Microsoft

![Screenshot che mostra la gerarchia MCA](./media/view-all-accounts/mca-hierarchy.png)

|Ambito  |Attività  |
|---------|---------|
|Account di fatturazione     |   Rappresenta un accordo accettato da un cliente per utilizzare i prodotti e i servizi Microsoft. Contiene uno o più profili di fatturazione. |
|Profilo di fatturazione     |   Rappresenta una fattura e le informazioni di fatturazione correlate, ad esempio i metodi di pagamento e l'indirizzo di fatturazione. Contiene una o più sezioni della fattura. |
|Sezione della fattura     |   Rappresenta un raggruppamento di costi in una fattura. Le sottoscrizioni di Azure e altri acquisti come Azure Marketplace e i prodotti di origine dell'app sono associati a questo ambito.    |

### <a name="microsoft-partner-agreement"></a>Contratto per i partner Microsoft

![Screenshot che mostra la gerarchia MPA](./media/view-all-accounts/mpa-hierarchy.png)

|Ambito  |Attività  |
|---------|---------|
|Account di fatturazione     |   Rappresenta un accordo tra partner per gestire i prodotti e i servizi Microsoft dei clienti nella nuova esperienza commerciale. Contiene uno o più profili di fatturazione e clienti.   |
|Profilo di fatturazione     |   Rappresenta una fattura per una valuta.     |
|Customer    |   Rappresenta un cliente per un partner Cloud Solution Provider (CSP).  Le sottoscrizioni di Azure e altri acquisti come Azure Marketplace e i prodotti di origine dell'app sono associati a questo ambito.  |
|Rivenditore    |   Rivenditore che fornisce servizi a un cliente. Si tratta di un campo facoltativo per una sottoscrizione ed è applicabile solo ai provider indiretti nel modello a due livelli CSP.     |

## <a name="switch-billing-scope-in-the-azure-portal"></a>Cambiare ambito di fatturazione nel portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Cercare **Gestione dei costi e fatturazione**.

   ![Screenshot che mostra una ricerca nel portale di Azure](./media/view-all-accounts/billing-search-cost-management-billing.png)

3. Nella pagina Panoramica selezionare **Cambia ambito**.

   ![Screenshot che Mostra gli ambiti di fatturazione](./media/view-all-accounts/overview-select-scopes.png)

   > [!Note]
    >
    > Se si ha accesso a un solo ambito, non sarà possibile visualizzare l'ambito del cambio.

4. Selezionare un ambito per visualizzare i dettagli.

   ![Screenshot che Mostra gli ambiti di fatturazione](./media/view-all-accounts/list-of-scopes.png)

## <a name="check-the-type-of-your-account"></a>Verificare il tipo di account
[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Opzioni per Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come iniziare ad [analizzare i costi](../costs/quick-acm-cost-analysis.md).
