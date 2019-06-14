---
title: Completare le attività contratto Enterprise Agreement cliente Microsoft - Azure | Microsoft Docs
description: Informazioni su come completare attività contratto Enterprise nel nuovo account di fatturazione.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/24/2018
ms.author: banders
ms.openlocfilehash: 9b76fa935263904957c87cd062c84d0607771369
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60371335"
---
# <a name="complete-enterprise-agreement-tasks-in-your-billing-account-for-a-microsoft-customer-agreement"></a>Completare le attività di contratto Enterprise Agreement nell'account di fatturazione per un contratto di Microsoft dal cliente

Se l'organizzazione ha sottoscritto un contratto di clienti Microsoft per rinnovare l'iscrizione con contratto Enterprise, viene creato un nuovo account di fatturazione per l'accordo. La fatturazione nel nuovo account è organizzata in modo diverso rispetto al contratto Enterprise Agreement. Questo articolo descrive come è possibile usare il nuovo account di fatturazione per eseguire le attività eseguite nel contratto Enterprise Agreement.

## <a name="how-billing-is-organized-in-the-new-account"></a>La fatturazione è organizzata nel nuovo account

Il diagramma seguente viene descritto come la fatturazione è organizzata nel tuo nuovo account di fatturazione.

![Immagine di ea mca-post-transizione-gerarchia](./media/billing-mca-setup-account/mca-post-transition-hierarchy.png)

| Enterprise Agreement   | Contratto del cliente Microsoft    |
|------------------------|--------------------------------------------------------|
| Registrazione            | Utilizzare un profilo di fatturazione per gestire la fatturazione per l'organizzazione, simile all'iscrizione con contratto Enterprise. Gli amministratori dell'organizzazione diventano proprietari di tale profilo di fatturazione. Per altre informazioni sui profili di fatturazione, vedere [comprendere i profili di fatturazione](billing-mca-overview.md#understand-billing-profiles).
| department            | Si usa una sezione della fattura per organizzare i costi, simili a reparti dell'iscrizione con contratto Enterprise. Reparto diventa sezioni della fattura e gli amministratori di reparto diventano i proprietari delle sezioni della fattura corrispondente. Per altre informazioni sulle sezioni della fattura, vedere [sezioni della fattura Understand](billing-mca-overview.md#understand-invoice-sections). |
| Account               | Gli account che sono stati creati nel contratto Enterprise non sono supportati nel nuovo account di fatturazione. Le sottoscrizioni dell'account appartengono alla sezione della fattura corrispondente per il dipartimento. I proprietari di account possono creare e gestire sottoscrizioni per le sezioni della fattura. |

## <a name="changes-for-enterprise-administrators-in-the-new-billing-account"></a>Modifiche per gli amministratori dell'organizzazione nel nuovo account di fatturazione

Le modifiche seguenti si applicano agli amministratori aziendali un contratto Enterprise che è stato rinnovato a un contratto di clienti Microsoft.

- Viene creato un profilo di fatturazione per l'iscrizione. Si userà il profilo di fatturazione per gestire la fatturazione per l'organizzazione, come l'iscrizione con contratto Enterprise. Per altre informazioni sui profili di fatturazione [comprendere i profili di fatturazione](billing-mca-overview.md#understand-billing-profiles).
- Per ogni reparto di iscrizione con contratto Enterprise viene creata una sezione della fattura. Si userà le sezioni della fattura per gestire i dipartimenti. È possibile creare nuove sezioni della fattura per configurare altri reparti. Per altre informazioni sulle sezioni della fattura, vedere [informazioni sulle sezioni della fattura](billing-mca-overview.md#understand-invoice-sections).
- Si userà il ruolo di creatore della sottoscrizione di Azure sulle sezioni della fattura per concedere ad altri utenti l'autorizzazione per creare una sottoscrizione di Azure, come gli account creati nell'iscrizione Enterprise Agreement.
- Si userà il [portale di Azure](https://portal.azure.com) per gestire la fatturazione per l'organizzazione, anziché tramite il portale di Azure con contratto Enterprise Agreement.

Nell'account di fatturazione di nuovo l'utente ha i seguenti ruoli:

**Proprietario del profilo di fatturazione** -si è assegnato il ruolo di proprietario del profilo fatturazione sul profilo di fatturazione che è stato creato quando è stato firmato il contratto. Il ruolo consente di gestire la fatturazione per l'organizzazione. È possibile visualizzare i costi e le fatture, organizzare i costi in fattura, Gestisci i metodi di pagamento e controllare l'accesso alla fatturazione della propria organizzazione.

**Proprietario di sezione della fattura** -si è assegnato il ruolo di proprietario sezione della fattura in tutte le sezioni di fattura che vengono creati per i reparti di iscrizione con contratto Enterprise. Il ruolo consente di controllare chi può creare sottoscrizioni di Azure e altri prodotti di acquisto.

### <a name="view-charges-and-credits-balance-for-your-organization"></a>La visualizzazione del saldo crediti e gli addebiti per l'organizzazione

Utilizzare il profilo di fatturazione per tenere traccia delle spese e bilanciare i crediti di Azure per l'organizzazione simile per l'iscrizione con contratto Enterprise.

Per informazioni su come visualizzare saldo del credito per un profilo di fatturazione, vedere [tenere traccia di saldo del credito di Azure per il profilo di fatturazione](billing-mca-check-azure-credits-balance.md).

Per informazioni su come visualizzare gli addebiti per un profilo di fatturazione, vedere [comprendere gli addebiti nella fattura del contratto Microsoft Customer](billing-mca-understand-your-bill.md).

### <a name="view-charges-for-a-department"></a>Visualizza addebiti per un reparto

Per ogni reparto che nel contratto Enterprise viene creata una sezione della fattura. È possibile visualizzare gli addebiti per una sezione fatturazione nel portale di Azure. Per altre informazioni, vedere [visualizzare le transazioni da sezioni della fattura](billing-mca-understand-your-bill.md#view-transactions-by-invoice-sections).

### <a name="view-charges-for-an-account"></a>Visualizza addebiti per un account

Gli account creati durante l'iscrizione con contratto Enterprise non sono supportati nel nuovo account di fatturazione. Le sottoscrizioni dell'account appartengono alla sezione della fattura corrispondente per il dipartimento. I proprietari di account possono creare e gestire sottoscrizioni per le sezioni della fattura.

Per visualizzare l'aggregazione dei costi per le sottoscrizioni appartenenti a un account, è necessario impostare un centro di costo per ogni sottoscrizione. È possibile usare file csv sull'utilizzo e costi di Azure per filtrare le sottoscrizioni dal centro di costo.

### <a name="download-usage-and-charges-csv-price-sheet-and-tax-documents"></a>Csv di utilizzo e costi di download, elenco prezzi e documenti fiscali

Viene generata una fattura mensile per ogni profilo di fatturazione nell'account di fatturazione. Per ogni fattura, è possibile scaricare file csv di utilizzo e costi di Azure, elenco prezzi e il documento fiscale (se applicabile). È anche possibile scaricare Azure file csv di utilizzo e costi per gli addebiti del mese corrente.

Per informazioni su come scaricare file csv di utilizzo e costi di Azure, vedere [Scarica utilizzo per il contratto del cliente Microsoft](billing-download-azure-daily-usage.md#download-usage-for-your-microsoft-customer-agreement).

Per informazioni su come scaricare l'elenco prezzi, vedere [Scarica i prezzi per il contratto di Microsoft dal cliente](billing-ea-pricing.md#view-and-download-pricing-for-your-microsoft-customer-agreement).

Per informazioni su come scaricare documenti fiscali, vedere [visualizzare i documenti fiscali del contratto del cliente Microsoft](billing-mca-download-tax-document.md#view-and-download-tax-documents).

### <a name="add-an-additional-enterprise-administrator"></a>Aggiungere un amministratore di enterprise aggiuntive

Fornire agli utenti di accedere al profilo di fatturazione per consentire di visualizzazione e gestione della fatturazione per l'organizzazione. È possibile usare la **controllo di accesso (IAM)** pagina nel portale di Azure per concedere l'accesso.  Per altre informazioni sui ruoli del profilo di fatturazione, vedere [attività e ruoli del profilo di fatturazione](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

Per informazioni su come fornire, accedere al profilo di fatturazione, vedere [gestire i ruoli di fatturazione nel portale di Azure](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="create-a-new-department"></a>Creare un nuovo reparto

Creare una sezione della fattura per organizzare i costi in base alle esigenze, ad esempio i reparti nell'iscrizione con contratto Enterprise. È possibile creare una nuova sezione fatturazione nel portale di Azure. Per altre informazioni, vedere [creare sezioni sulla fattura per organizzare i costi](billing-mca-section-invoice.md).

### <a name="create-a-new-account"></a>Creare un nuovo account

Assegnare gli utenti il ruolo di creatore di sottoscrizione di Azure sulle sezioni della fattura per concedere le autorizzazioni per creare una sottoscrizione di Azure, come gli account creati in iscrizione con contratto Enterprise. Per altre informazioni, vedere [Concedi ad altri utenti l'autorizzazione per creare sottoscrizioni di Azure](billing-mca-create-subscription.md#give-others-permission-to-create-azure-subscriptions).

## <a name="changes-for-department-administrators-in-the-new-billing-account"></a>Modifiche per gli amministratori di reparto nel nuovo account di fatturazione

Le modifiche seguenti si applicano agli amministratori di reparto in un contratto Enterprise che è stato rinnovato a un contratto di clienti Microsoft.

- Per ogni reparto di iscrizione con contratto Enterprise viene creata una sezione della fattura. Si userà le sezioni della fattura per gestire i reparto o i reparti. Per altre informazioni sulle sezioni della fattura, vedere [informazioni sulle sezioni della fattura](billing-mca-overview.md#understand-invoice-sections).
- Si userà il ruolo di creatore della sottoscrizione di Azure nella sezione della fattura per concedere ad altri utenti l'autorizzazione per creare una sottoscrizione di Azure, come gli account creati in iscrizione con contratto Enterprise.
- Si userà il portale di Azure per gestire la fatturazione per l'organizzazione, anziché tramite il portale di Azure con contratto Enterprise Agreement.

Nell'account di fatturazione di nuovo l'utente ha i seguenti ruoli:

**Proprietario di sezione della fattura** -si è assegnato il ruolo di proprietario sezione della fattura nella sezione della fattura creato per i reparto o i reparti nel contratto Enterprise Agreement. Il ruolo consente di visualizzare e gli addebiti di tenere traccia e controllare chi può creare sottoscrizioni di Azure e acquistare altri prodotti per la sezione della fattura.

### <a name="view-charges-for-your-department"></a>Visualizza addebiti per il reparto IT

È possibile visualizzare gli addebiti per la sezione della fattura creato per il reparto, nel portale di Azure [gestione costi + fatturazione pagina](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview).

### <a name="add-an-additional-department-administrator"></a>Aggiungere un amministratore del reparto aggiuntive

Per ogni reparto che nel contratto Enterprise viene creata una sezione della fattura. È possibile usare la **accesso (IAM)** pagina nel portale di Azure per concedere ad altri utenti l'accesso per visualizzare e gestire la sezione della fattura. Per altre informazioni sui ruoli della sezione della fattura, vedere [fattura sezione ruoli e attività](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

Per informazioni su come fornire, accedere alla sezione fattura, vedere [gestire i ruoli di fatturazione nel portale di Azure](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="create-a-new-account-in-your-department"></a>Creare un nuovo account del reparto

Assegnare gli utenti il ruolo di creatore di sottoscrizione di Azure nella sezione della fattura creato per il reparto IT. Per altre informazioni, vedere [Concedi ad altri utenti l'autorizzazione per creare sottoscrizioni di Azure](billing-mca-create-subscription.md#give-others-permission-to-create-azure-subscriptions).

### <a name="view-charges-for-accounts-in-your-departments"></a>Visualizza addebiti per gli account in dipartimenti

Gli account creati durante l'iscrizione con contratto Enterprise non sono supportati nel nuovo account di fatturazione. Le sottoscrizioni dell'account appartengono alla sezione della fattura corrispondente per il dipartimento. I proprietari di account possono creare e gestire sottoscrizioni per le sezioni della fattura.

Per visualizzare l'aggregazione dei costi per le sottoscrizioni appartenenti a un account del reparto, è necessario impostare un centro di costo per ogni sottoscrizione. È possibile usare il file di utilizzo e costi di Azure per filtrare le sottoscrizioni dal centro di costo.

## <a name="changes-for-account-owners-in-the-new-billing-account"></a>Modifiche per i proprietari di account nel nuovo account di fatturazione

I proprietari di account nel contratto Enterprise Agreement ottenere autorizzazioni per creare le sottoscrizioni di Azure nel nuovo account di fatturazione. Le sottoscrizioni di Azure esistente appartengono alla sezione della fattura creato per il reparto IT. Se l'account non appartiene a un reparto, le sottoscrizioni appartengono a una sezione della fattura denominata sezione della fattura predefinita.

Per creare ulteriori sottoscrizioni di Azure, è possibile il ruolo seguente nel nuovo account di fatturazione.

**Creazione sottoscrizione Azure** -si è assegnato il ruolo di creatore sottoscrizione di azure nella sezione della fattura creato per il tuo reparto in contratto Enterprise Agreement. Se l'account non appartiene a un reparto, otterrai ruolo autore di sottoscrizione di Azure in una sezione denominata sezione della fattura predefinita. Il ruolo consente di creare sottoscrizioni di Azure per la sezione della fattura.

### <a name="create-an-azure-subscription"></a>Crea una sottoscrizione di Azure

È possibile creare sottoscrizioni di Azure per la sezione della fattura nel portale di Azure. Per altre informazioni, vedere [creare un'altra sottoscrizione di Azure per il contratto di clienti Microsoft](billing-mca-create-subscription.md)

### <a name="view-charges-for-your-account"></a>Visualizza addebiti per l'account

Per gli addebiti per le sottoscrizioni appartenenti a un account, vedere la [pagina sottoscrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) nel portale di Azure. La pagina sottoscrizioni consente di visualizzare gli addebiti per tutte la sottoscrizione.

### <a name="view-charges-for-a-subscription"></a>Visualizza addebiti per una sottoscrizione

È possibile visualizzare sia i costi per una sottoscrizione nel [pagina sottoscrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) o l'analisi dei costi di Azure. Per ulteriori informazioni sull'analisi dei costi di Azure, vedere [esplorare e analizzare i costi con l'analisi del costo](../cost-management/quick-acm-cost-analysis.md).

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico

Se si necessita assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sull'account di fatturazione per un contratto di Microsoft dal cliente](billing-mca-overview.md)
- [Comprendere la fattura](billing-understand-your-bill.md)
- [Informazioni sulla fattura](billing-understand-your-invoice.md)
- [Ottenere la proprietà delle sottoscrizioni di Azure ad altri utenti di fatturazione](billing-mca-request-billing-ownership.md)
