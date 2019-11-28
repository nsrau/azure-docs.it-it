---
title: Introduzione all'account di fatturazione del Contratto del cliente Microsoft - Azure
description: Informazioni sull'account di fatturazione del Contratto del cliente Microsoft
author: bandersmsft
manager: amberbhargava
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/10/2019
ms.author: banders
ms.openlocfilehash: e27d2116cf58c64fca7fb0273c852d6223f00c6e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226127"
---
# <a name="get-started-with-your-microsoft-customer-agreement-billing-account"></a>Introduzione all'account di fatturazione per il Contratto del cliente Microsoft

L'account di fatturazione viene creato quando ci si iscrive per usare Azure. Viene usato per gestire le fatture e i pagamenti, oltre che per tenere traccia dei costi. È possibile avere accesso a più account di fatturazione. Ad esempio, l'iscrizione ad Azure potrebbe essere stata eseguita per i progetti personali. È anche possibile accedere ad Azure tramite il contratto Enterprise o il Contratto del cliente Microsoft dell'organizzazione. Per ognuno di questi scenari, si avrà un account di fatturazione distinto.

Questo articolo si applica a un account di fatturazione per un Contratto del cliente Microsoft. [Verificare di avere accesso a un Contratto del cliente Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="your-billing-account"></a>Account di fatturazione

L'account di fatturazione per il Contratto del cliente Microsoft contiene uno o più profili di fatturazione che consentono di gestire le fatture e i metodi di pagamento. Ogni profilo di fatturazione contiene una o più sezioni della fattura che consentono di organizzare i costi per la fattura del profilo di fatturazione.

Il diagramma seguente mostra la relazione tra un account di fatturazione, i profili di fatturazione e le sezioni della fattura.

![Diagramma che mostra la gerarchia di fatturazione del Contratto del cliente Microsoft](./media/billing-mca-overview/mca-billing-hierarchy.png)

I ruoli nell'account di fatturazione hanno il livello di autorizzazioni più elevato. Per impostazione predefinita, solo l'utente che ha effettuato l'iscrizione ad Azure può accedere all'account di fatturazione. Questi ruoli devono essere assegnati agli utenti che devono visualizzare le fatture e tenere traccia dei costi dell'intera organizzazione, ad esempio responsabili finanziari o IT. Per altre informazioni, vedere [Ruoli e attività dell'account di fatturazione](billing-understand-mca-roles.md#billing-account-roles-and-tasks).

## <a name="billing-profiles"></a>Profili di fatturazione

Usare un profilo di fatturazione per gestire fatture e metodi di pagamento. Viene generata una fattura mensile all'inizio del mese per ogni profilo di fatturazione incluso nell'account. La fattura contiene gli addebiti rispettivi per tutte le sottoscrizioni di Azure e gli altri acquisti del mese precedente.

Per l'account di fatturazione viene creato automaticamente un profilo di fatturazione, che per impostazione predefinita contiene una sezione della fattura. È possibile creare sezioni aggiuntive per tenere traccia dei costi e allocarli facilmente in base alle esigenze, ad esempio per progetto, reparto o ambiente di sviluppo. Queste sezioni verranno visualizzate nella fattura del profilo di fatturazione e rispecchieranno l'utilizzo di ogni sottoscrizione e gli acquisti assegnati ad essa assegnati.

Ai ruoli nei profili di fatturazione sono assegnate le autorizzazioni per visualizzare e gestire fatture e metodi di pagamento. Assegnare questi ruoli agli utenti che pagano fatture, ad esempio i membri del team di contabilità nell'organizzazione. Per altre informazioni, vedere [Ruoli e attività del profilo di fatturazione](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="each-billing-profile-gets-a-monthly-invoice"></a>Ogni profilo di fatturazione riceve una fattura mensile

All'inizio del mese viene generata una fattura mensile per ogni profilo di fatturazione. La fattura contiene tutti gli addebiti del mese precedente.

È possibile visualizzare la fattura, scaricare i documenti e modificare l'impostazione per ricevere le fatture future tramite posta elettronica, nel portale di Azure. Per altre informazioni, vedere [Scaricare le fatture per un Contratto del cliente Microsoft](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement).

### <a name="invoice-payment-methods"></a>Metodi di pagamento delle fatture

Per ogni profilo di fatturazione esistono metodi di pagamento specifici che vengono usati per pagare le rispettive fatture. Sono supportati i metodi di pagamento seguenti:

| Type             | Definizione  |
|------------------|-------------|
|Crediti Azure    |  I crediti vengono applicati automaticamente agli addebiti idonei nella fattura, riducendo l'importo dovuto. Per altre informazioni, vedere [Tenere traccia del saldo del credito Azure per il profilo di fatturazione](billing-mca-check-azure-credits-balance.md). |
|Assegno/bonifico | Indica se l'account è approvato per il pagamento tramite assegno/bonifico. È possibile pagare l'importo dovuto per la fattura tramite assegno/bonifico. Le istruzioni per il pagamento sono indicate nella fattura |
|Carta di credito | I clienti che si iscrivono ad Azure tramite il sito Web di Azure possono pagare con carta di credito. |

### <a name="apply-policies-to-control-purchases"></a>Applicare criteri per controllare gli acquisti

Applicare criteri per controllare gli acquisti di prenotazioni e quelli effettuati su Azure Marketplace usando un profilo di fatturazione. È possibile impostare criteri per disabilitare l'acquisto di prenotazioni di Azure e prodotti di Azure Marketplace. Quando si applicano i criteri, non è possibile usare le sottoscrizioni addebitate al profilo di fatturazione per effettuare questi acquisti.

### <a name="azure-plans-determine-pricing-and-service-level-agreement-for-subscriptions"></a>I piani di Azure determinano i prezzi e il contratto di servizio per le sottoscrizioni

I piani di Azure determinano i prezzi e il contratto di servizio per le sottoscrizioni di Azure. Vengono abilitati automaticamente durante la creazione di un profilo di fatturazione. Tutte le sezioni della fattura associate al profilo di fatturazione possono usare questi piani. Gli utenti con accesso alla sezione della fattura usano i piani per creare sottoscrizioni di Azure. I piani di Azure seguenti sono supportati negli account di fatturazione per il Contratto del cliente Microsoft:

| Pianificazione             | Definizione  |
|------------------|-------------|
|Piano di Microsoft Azure   | Consente agli utenti di creare sottoscrizioni in grado di eseguire qualsiasi carico di lavoro.  |
|Piano di Microsoft Azure per Sviluppo/test | Consente ai sottoscrittori di Visual Studio di creare sottoscrizioni limitate per carichi di lavoro di sviluppo o test. A queste sottoscrizioni sono associati vantaggi quali tariffe inferiori e accesso a immagini di macchine virtuali esclusive nel portale di Azure. |

## <a name="invoice-sections"></a>Sezioni della fattura

Creare sezioni della fattura per organizzare i costi nella fattura. Ad esempio, si consideri il caso in cui si preferisce una singola fattura per l'organizzazione, ma si vogliono organizzare i costi per reparto, team o progetto. Questo scenario prevede un singolo profilo di fatturazione in cui viene creata una sezione della fattura per ogni reparto, team o progetto.

Quando si crea una sezione della fattura, è possibile concedere ad altri utenti l'autorizzazione a creare sottoscrizioni di Azure fatturate nella sezione. Eventuali addebiti per l'utilizzo e acquisti per le sottoscrizioni vengono quindi fatturati nella sezione.

I ruoli nella sezione della fattura dispongono delle autorizzazioni per controllare chi crea le sottoscrizioni di Azure. Assegnare questi ruoli agli utenti che configurano l'ambiente Azure per i team dell'organizzazione, ad esempio i responsabili e gli architetti tecnici. Per altre informazioni, vedere [Ruoli e attività della sezione della fattura](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificare l'accesso a un Contratto del cliente Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico.

Se si necessita assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sull'account di fatturazione, vedere gli articoli seguenti:

- [Informazioni sui ruoli amministrativi per il Contratto del cliente Microsoft in Azure](billing-understand-mca-roles.md)
- [Creare un'altra sottoscrizione di Azure per il Contratto del cliente Microsoft](billing-mca-create-subscription.md)
- [Creare sezioni nella fattura per organizzare i costi](billing-mca-section-invoice.md)
