---
title: Iniziare con l'account di fatturazione per un contratto del cliente di Microsoft - Azure | Microsoft Docs
description: Informazioni sull'account di fatturazione per un contratto di Microsoft dal cliente
services: billing
documentationcenter: ''
author: amberbhargava
manager: amberbhargava
editor: banders
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: ea625a61ed600dbaa22fef85987e9570a6fb7dbc
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57337414"
---
# <a name="get-started-with-your-billing-account-for-a-microsoft-customer-agreement"></a>Iniziare con l'account di fatturazione per un contratto di Microsoft dal cliente

Viene creato un account di fatturazione per ogni accordo che si accede con Microsoft a usare Azure. Utilizzare l'account di fatturazione per gestire la fatturazione e tenere traccia dei costi. È possibile accedere a più account di fatturazione. Ad esempio, è possibile che hanno effettuato l'iscrizione per Azure per i progetti personali. Si è stato possibile accedere a Azure grazie al contratto Enterprise o contratto di Microsoft dei clienti della propria organizzazione. Per ognuno di questi scenari, è necessario un account di fatturazione separato.

Questo articolo si applica a un account di fatturazione per un contratto di clienti Microsoft. [Controllare se si ha accesso a un contratto di Microsoft dal cliente](#check-access-to-a-microsoft-customer-agreement).

## <a name="understand-billing-account"></a>Comprendere l'account di fatturazione

L'account di fatturazione per l'accordo di clienti Microsoft contiene uno o più profili di fatturazione che consentono di gestire le fatture e i metodi di pagamento. Ogni profilo di fatturazione contiene uno o più sezioni di fatturazione che consentono di organizzare i costi in fattura del profilo di fatturazione.

Il diagramma seguente mostra la relazione tra un account di fatturazione, i profili di fatturazione e nelle sezioni della fattura.

![Diagramma che mostra la gerarchia fatturazione per il contratto di clienti Microsoft](./media/billing-mca-overview/mca-billing-hierarchy.png)

I ruoli nell'account di fatturazione avere il massimo livello di autorizzazioni. Per impostazione predefinita, solo gli amministratori globali in Active Directory di Azure dell'organizzazione accedere all'account di fatturazione. Questi ruoli devono essere assegnati agli utenti che devono visualizzare le fatture e tenere traccia dei costi per l'intera organizzazione, come finanziario o i responsabili IT. Per altre informazioni, vedere [attività e ruoli degli account di fatturazione](billing-understand-mca-roles.md#billing-account-roles-and-tasks).

## <a name="understand-billing-profiles"></a>Comprendere i profili di fatturazione

Usare un profilo di fatturazione per gestire i metodi di pagamento e fatturazione. Viene generata una fattura mensile per le sottoscrizioni di Azure e altri prodotti acquistati tramite il profilo di fatturazione. Utilizzare i metodi di pagamento per pagare le fatture.

Un profilo di fatturazione viene creato automaticamente per l'account di fatturazione. È possibile creare nuovi profili di fatturazione per impostare le fatture aggiuntive. Ad esempio, è possibile diverse fatture per ogni reparto o progetto all'interno dell'organizzazione.

È anche possibile creare sezioni della fattura per organizzare i costi in fattura del profilo di fatturazione. Gli addebiti per le sottoscrizioni di Azure e i prodotti acquistati per una sezione della fattura visualizzati nella sezione. Fattura del profilo fatturazione include i costi per tutte le sezioni della fattura.

Ruoli per i profili di fatturazione dispongono delle autorizzazioni per visualizzare e gestire le fatture e i metodi di pagamento. Assegnare questi ruoli per gli utenti di pagano le fatture, ad esempio i membri del team contabilità dell'organizzazione. Per altre informazioni, vedere [attività e ruoli del profilo di fatturazione](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="monthly-invoice-generated-for-each-billing-profile"></a>Fattura mensile generata per ogni profilo di fatturazione

Alla data di fatturazione per ogni profilo di fatturazione viene generata una fattura mensile. La fattura include tutti gli addebiti per il mese precedente.

È possibile visualizzare la fattura, scaricare i documenti e modificare l'impostazione da ottenere future fatture tramite posta elettronica, nel portale di Azure. Per altre informazioni, vedere [scaricare le fatture per un contratto di Microsoft dal cliente](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement).

### <a name="invoices-paid-through-payment-methods"></a>Fatture a pagamento tramite i metodi di pagamento

Ogni profilo di fatturazione ha i propri metodi di pagamento che consentono di pagare le fatture. Sono supportati i seguenti metodi di pagamento:

| Type             | Definizione  |
|------------------|-------------|
|Crediti Azure    |  I crediti vengono applicati automaticamente per l'importo fatturato totale sulla fattura per calcolare la quantità che è necessario pagare. Per altre informazioni, vedere [tenere traccia di saldo del credito di Azure per il profilo di fatturazione](billing-mca-check-azure-credits-balance.md). |
|Assegno o bonifico | È possibile pagare dell'importo dovuto per ottenere la fattura tramite controllo o wire transfer. Vengono fornite le istruzioni per il pagamento della fattura |

### <a name="control-azure-marketplace-and-reservation-purchases-by-applying-policies"></a>Acquisti di controllo Azure Marketplace e prenotazione applicando i criteri

Applicare i criteri per controllare gli acquisti effettuati usando un profilo di fatturazione. È possibile impostare criteri per disabilitare l'acquisto di prenotazioni di Azure e prodotti di Marketplace. Quando vengono applicati i criteri, le sottoscrizioni create per le sezioni della fattura nel profilo di fatturazione non sono utilizzabile per l'acquisto di prenotazioni di Azure e prodotti di Marketplace.

### <a name="allow-users-to-create-azure-subscriptions-by-enabling-azure-plans"></a>Consentire agli utenti di creare sottoscrizioni di Azure abilitando i piani di Azure

I piani di Azure vengono attivati automaticamente quando si crea un profilo di fatturazione. Tutte le sezioni della fattura nel profilo di fatturazione Ottieni l'accesso a questi piani. Gli utenti con accesso alla sezione della fattura usano i piani per creare le sottoscrizioni di Azure. Non può creare sottoscrizioni di Azure, a meno che un piano di Azure è abilitato per il profilo di fatturazione. Sono supportati i seguenti piani di Azure nell'account di fatturazione per contratto dei clienti Microsoft:

| Pianificazione             | Definizione  |
|------------------|-------------|
|Piano di Microsoft Azure   | Consentire agli utenti di creare sottoscrizioni che è possono eseguire carichi di lavoro. Per altre informazioni, vedere [piano di Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g/) |
|Piano di Microsoft Azure per sviluppo/test | Consentire ai sottoscrittori di Visual Studio creare le sottoscrizioni che sono limitate per lo sviluppo o test di carichi di lavoro. Queste sottoscrizioni ottengono i vantaggi, ad esempio inferiore tariffe e accedere a immagini esclusive macchina virtuale nel portale di Azure. Per altre informazioni, vedere [piano di Microsoft Azure per sviluppo/test](https://azure.microsoft.com/offers/ms-azr-0148g/)|

## <a name="understand-invoice-sections"></a>Informazioni sulle sezioni della fattura

Creare una sezione della fattura per organizzare i costi in fattura del profilo di fatturazione. Ad esempio, potrebbe essere necessario una singola fattura per l'organizzazione ma si desidera organizzare i costi per ogni dipartimento, del team o progetto. Per questo scenario, è necessario un singolo profilo di fatturazione in cui si crea una sezione della fattura per ogni dipartimento, del team o progetto.

Quando viene creata una sezione della fattura, è possibile concedere ad altri utenti autorizzati a creare sottoscrizioni di Azure per la sezione. Eventuali addebiti di utilizzo e gli acquisti per le sottoscrizioni, quindi si riflettono nella sezione appropriata della fattura.

I ruoli nella sezione della fattura dispongono delle autorizzazioni per controllare chi crea le sottoscrizioni di Azure. Assegnare questi ruoli agli utenti che ha configurato l'ambiente di Azure per i team all'interno dell'organizzazione, ad esempio responsabili tecnici e architetti tecnici. Per altre informazioni, vedere [fattura sezione ruoli e attività](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificare l'accesso a un contratto di Microsoft dal cliente
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico

Se si necessita assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti per informazioni su account di fatturazione:

- [Comprendere i ruoli amministrativi di contratto di Microsoft dal cliente in Azure](billing-understand-mca-roles.md)
- [Creare un'altra sottoscrizione di Azure per il contratto di clienti Microsoft](billing-mca-create-subscription.md)
- [Creare sezioni sulla fattura per organizzare i costi](billing-mca-section-invoice.md)