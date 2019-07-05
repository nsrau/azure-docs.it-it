---
title: Introduzione a Microsoft dal cliente contratto account di fatturazione - Azure
description: Comprendere il contratto del cliente Microsoft account di fatturazione
author: bandersmsft
manager: amberbhargava
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 87483c967641489e9548f38c99eebbf121d0d252
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490742"
---
# <a name="get-started-with-your-microsoft-customer-agreement-billing-account"></a>Iniziare con l'account di fatturazione del contratto di Microsoft dal cliente

Quando si effettua l'iscrizione usare Azure, viene creato un account di fatturazione. Si usa l'account di fatturazione per gestire le fatture, pagamenti e tenere traccia dei costi. È possibile accedere a più account di fatturazione. Ad esempio, è possibile che hanno effettuato l'iscrizione per Azure per i progetti personali. Si è stato possibile accedere a Azure grazie al contratto Enterprise o contratto di Microsoft dei clienti della propria organizzazione. Per ognuno di questi scenari, è necessario un account di fatturazione separato.

Questo articolo si applica a un account di fatturazione per un contratto di clienti Microsoft. [Controllare se si ha accesso a un contratto di Microsoft dal cliente](#check-access-to-a-microsoft-customer-agreement).

## <a name="your-billing-account"></a>L'account di fatturazione

L'account di fatturazione per l'accordo di clienti Microsoft contiene uno o più profili di fatturazione che consentono di gestire le fatture e i metodi di pagamento. Ogni profilo di fatturazione contiene uno o più sezioni di fatturazione che consentono di organizzare i costi in fattura del profilo di fatturazione.

Il diagramma seguente mostra la relazione tra un account di fatturazione, i profili di fatturazione e nelle sezioni della fattura.

![Diagramma che mostra la gerarchia di fatturazione del contratto di cliente di Microsoft](./media/billing-mca-overview/mca-billing-hierarchy.png)

I ruoli nell'account di fatturazione avere il massimo livello di autorizzazioni. Per impostazione predefinita, solo l'utente che ha effettuato l'iscrizione per Azure fornisce l'accesso all'account di fatturazione. Questi ruoli devono essere assegnati agli utenti che devono visualizzare le fatture e tenere traccia dei costi per l'intera organizzazione, come finanziario o i responsabili IT. Per altre informazioni, vedere [attività e ruoli degli account di fatturazione](billing-understand-mca-roles.md#billing-account-roles-and-tasks).

## <a name="billing-profiles"></a>Profili di fatturazione

Usare un profilo di fatturazione per gestire i metodi di pagamento e fatturazione. Viene generata una fattura mensile all'inizio del mese per ogni profilo di fatturazione nell'account. La fattura include i rispettivi addebiti per tutte le sottoscrizioni di Azure e gli altri acquisti dal basato sul mese precedente.

Un profilo di fatturazione viene creato automaticamente per l'account di fatturazione. Contiene una sezione della fattura per impostazione predefinita. È possibile creare le sezioni aggiuntive per tenere traccia e organizzare i costi in base alle esigenze facilmente tratti per ogni ambiente di sviluppo, reparto o progetto. Queste sezioni verrà visualizzato nella fattura del profilo di fatturazione che riflettono l'utilizzo di ogni sottoscrizione e acquisti che è stato assegnato a esso.

Ruoli per i profili di fatturazione dispongono delle autorizzazioni per visualizzare e gestire le fatture e i metodi di pagamento. Assegnare questi ruoli per gli utenti di pagano le fatture, ad esempio i membri del team contabilità dell'organizzazione. Per altre informazioni, vedere [attività e ruoli del profilo di fatturazione](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="each-billing-profile-gets-a-monthly-invoice"></a>Ogni profilo di fatturazione Ottiene una fattura mensile

All'inizio del mese per ogni profilo di fatturazione viene generata una fattura mensile. La fattura include tutti gli addebiti del mese precedente.

È possibile visualizzare la fattura, scaricare i documenti e modificare l'impostazione da ottenere future fatture tramite posta elettronica, nel portale di Azure. Per altre informazioni, vedere [scaricare le fatture per un contratto di Microsoft dal cliente](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement).

### <a name="invoice-payment-methods"></a>Metodi di pagamento della fattura

Ogni profilo di fatturazione ha i propri metodi di pagamento che consentono di pagare le fatture. Sono supportati i seguenti metodi di pagamento:

| Type             | Definizione  |
|------------------|-------------|
|Crediti di Azure    |  I crediti vengono applicati automaticamente per gli addebiti nella fattura, riducendo la quantità che è necessario pagare idonei. Per altre informazioni, vedere [tenere traccia di saldo del credito di Azure per il profilo di fatturazione](billing-mca-check-azure-credits-balance.md). |
|Trasferimento di controllo/transito | Se l'account viene approvato per il pagamento tramite controllo/bonifico. È possibile pagare l'importo dovuto per ottenere la fattura tramite controllo/bonifico. Vengono fornite le istruzioni per il pagamento della fattura |
|Carta di credito | L'iscrizione ad Azure tramite il sito Web di Azure i clienti possono pagare tramite carta di credito. |

### <a name="apply-policies-to-control-purchases"></a>Applicare i criteri per controllare gli acquisti

Applicare i criteri di controllo Azure Marketplace e gli acquisti di prenotazioni usando un profilo di fatturazione. È possibile impostare criteri per disabilitare l'acquisto di prenotazioni di Azure e prodotti di Marketplace. Quando vengono applicati i criteri, le sottoscrizioni fatturate al profilo di fatturazione non sono utilizzabile per effettuare acquisti correlati a questi.

### <a name="azure-plans-determine-pricing-and-service-level-agreement-for-subscriptions"></a>Contratto servizio e di determinazione dei prezzi per le sottoscrizioni di determinare i piani di Azure

I piani Azure determinano il piano tariffario e contratti per le sottoscrizioni di Azure di servizio. Vengono abilitati automaticamente quando si crea un profilo di fatturazione. Tutte le sezioni di fattura che sono associate al profilo di fatturazione è possono usare questi piani. Gli utenti con accesso alla sezione della fattura usano i piani per creare le sottoscrizioni di Azure. Sono supportati i seguenti piani di Azure nell'account di fatturazione per contratto dei clienti Microsoft:

| Pianificazione             | Definizione  |
|------------------|-------------|
|Piano di Microsoft Azure   | Consentire agli utenti di creare sottoscrizioni che è possono eseguire carichi di lavoro. Per altre informazioni, vedere [piano di Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g/) |
|Piano di Microsoft Azure per sviluppo/Test | Consentire ai sottoscrittori di Visual Studio creare le sottoscrizioni che sono limitate per lo sviluppo o test di carichi di lavoro. Queste sottoscrizioni ottengono i vantaggi, ad esempio inferiore tariffe e accedere a immagini esclusive macchina virtuale nel portale di Azure. Per altre informazioni, vedere [piano di Microsoft Azure per sviluppo/test](https://azure.microsoft.com/offers/ms-azr-0148g/)|

## <a name="invoice-sections"></a>Sezioni della fattura

Creare una sezione della fattura per organizzare i costi in fattura. Ad esempio, potrebbe essere necessario una singola fattura per l'organizzazione ma si desidera organizzare i costi per ogni dipartimento, del team o progetto. Per questo scenario, è necessario un singolo profilo di fatturazione in cui si crea una sezione della fattura per ogni dipartimento, del team o progetto.

Quando viene creata una sezione della fattura, è possibile concedere ad altri utenti autorizzati a creare le sottoscrizioni di Azure vengono fatturate alla sezione. Eventuali addebiti di utilizzo e gli acquisti per le sottoscrizioni vengono fatturati quindi alla sezione.

I ruoli nella sezione della fattura dispongono delle autorizzazioni per controllare chi crea le sottoscrizioni di Azure. Assegnare questi ruoli agli utenti che ha configurato l'ambiente di Azure per i team all'interno dell'organizzazione, ad esempio responsabili tecnici e architetti tecnici. Per altre informazioni, vedere [fattura sezione ruoli e attività](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificare l'accesso a un contratto di Microsoft dal cliente
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico.

Se si necessita assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti per informazioni su account di fatturazione:

- [Comprendere i ruoli amministrativi di contratto di Microsoft dal cliente in Azure](billing-understand-mca-roles.md)
- [Creare un'altra sottoscrizione di Azure per il contratto di clienti Microsoft](billing-mca-create-subscription.md)
- [Creare sezioni sulla fattura per organizzare i costi](billing-mca-section-invoice.md)
