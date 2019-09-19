---
title: Completare le attività del Contratto Enterprise nel contratto del cliente Microsoft - Azure
description: Informazioni su come completare le attività del Contratto Enterprise nel nuovo account di fatturazione.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 9404908b7c486801480474c5a2c9ff7688e1de48
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "67490702"
---
# <a name="complete-enterprise-agreement-tasks-in-your-billing-account-for-a-microsoft-customer-agreement"></a>Completare le attività del Contratto Enterprise nell'account di fatturazione per un contratto del cliente Microsoft

Se l'organizzazione ha firmato un contratto del cliente Microsoft per rinnovare l'iscrizione al Contratto Enterprise, viene creato un nuovo account di fatturazione per il contratto. La fatturazione nel nuovo account è organizzata in modo diverso rispetto al Contratto Enterprise. Questo articolo descrive come è possibile usare il nuovo account di fatturazione per completare le attività eseguite nel Contratto Enterprise.

## <a name="billing-organization-in-the-new-account"></a>Organizzazione della fatturazione nel nuovo account

Il diagramma seguente descrive il modo in cui la fatturazione è organizzata nel nuovo account di fatturazione.

![Immagine della gerarchia post transizione Contratto Enterprise-Contratto del cliente Microsoft](./media/billing-mca-setup-account/mca-post-transition-hierarchy.png)

| Enterprise Agreement   | Contratto del cliente Microsoft    |
|------------------------|--------------------------------------------------------|
| Registrazione            | Usare un profilo di fatturazione per gestire la fatturazione per l'organizzazione, in modo analogo alla registrazione del Contratto Enterprise. Gli amministratori dell'organizzazione diventano proprietari del profilo di fatturazione. Per saperne di più sui profili di fatturazione, vedere [Informazioni sui profili di fatturazione](billing-mca-overview.md#billing-profiles).
| department            | Usare una sezione della fattura per organizzare i costi, in modo analogo ai reparti nella registrazione del Contratto Enterprise. Il reparto diventa una sezione di fatturazione e gli amministratori del reparto diventano proprietari delle rispettive sezioni della fattura. Per saperne di più sulle sezioni della fattura, vedere [Informazioni sulle sezioni della fattura](billing-mca-overview.md#invoice-sections). |
| Account               | Gli account creati nel Contratto Enterprise non sono supportati nel nuovo account di fatturazione. Le sottoscrizioni dell'account appartengono alla rispettiva sezione della fattura per il relativo reparto. I proprietari dell'account possono creare e gestire le sottoscrizioni per le sezioni della fattura. |

## <a name="changes-for-enterprise-administrators"></a>Modifiche per gli amministratori dell'organizzazione

Le modifiche seguenti si applicano agli amministratori dell'organizzazione in un Contratto Enterprise che è stato rinnovato in un contratto del cliente Microsoft.

- Viene creato un profilo di fatturazione per la registrazione. Si usa il profilo di fatturazione per gestire la fatturazione per l'organizzazione, in modo analogo alla registrazione del Contratto Enterprise. Per saperne di più sui profili di fatturazione, vedere [Informazioni sui profili di fatturazione](billing-mca-overview.md#billing-profiles).
- Viene creata una sezione della fattura per ogni reparto nella registrazione del Contratto Enterprise. Le sezioni della fattura verranno usate per gestire i reparti. È possibile creare nuove sezioni della fattura per configurare altri reparti. Per saperne di più sulle sezioni della fattura, vedere [Informazioni sulle sezioni della fattura](billing-mca-overview.md#invoice-sections).
- Per concedere ad altri utenti l'autorizzazione a creare una sottoscrizione di Azure, ad esempio gli account creati nella registrazione del Contratto Enterprise, si userà il ruolo creatore della sottoscrizione di Azure nelle sezioni della fattura.
- Si userà il [portale di Azure](https://portal.azure.com) per gestire la fatturazione per l'organizzazione, anziché il portale EA di Azure.

Nel nuovo account di fatturazione vengono assegnati i ruoli seguenti:

**Proprietario del profilo di fatturazione**: viene assegnato il ruolo di proprietario del profilo di fatturazione nel profilo di fatturazione che è stato creato alla firma del contratto. Il ruolo consente di gestire la fatturazione per l'organizzazione. È possibile visualizzare gli addebiti e le fatture, organizzare i costi per la fattura, gestire i metodi di pagamento e controllare l'accesso alla fatturazione dell'organizzazione.

**Proprietario della sezione della fattura**: viene assegnato il ruolo di proprietario della sezione della fattura in tutte le sezioni della fattura create per i reparti nella registrazione del Contratto Enterprise. Il ruolo consente di controllare gli utenti che possono creare sottoscrizioni di Azure e acquistare altri prodotti.

### <a name="view-charges-and-credits-balance-for-your-organization"></a>Visualizzare il saldo degli addebiti e dei crediti per l'organizzazione

È possibile usare il profilo di fatturazione per tenere traccia degli addebiti e dei crediti Azure per l'organizzazione in modo analogo alla registrazione del Contratto Enterprise.

Per informazioni su come visualizzare il saldo del credito per un profilo di fatturazione, vedere [Tenere traccia del saldo del credito Azure per il profilo di fatturazione](billing-mca-check-azure-credits-balance.md).

Per saperne di più su come visualizzare gli addebiti per un profilo di fatturazione, vedere [Informazioni sugli addebiti nella fattura del contratto del cliente Microsoft](billing-mca-understand-your-bill.md).

### <a name="view-charges-for-a-department"></a>Visualizzare gli addebiti per un reparto

Viene creata una sezione della fattura per ogni reparto nel Contratto Enterprise. È possibile visualizzare gli addebiti per una sezione della fattura nel portale di Azure. Per altre informazioni, vedere [Visualizzare le transazioni in base alle sezioni della fattura](billing-mca-understand-your-bill.md#view-transactions-by-invoice-sections).

### <a name="view-charges-for-an-account"></a>Visualizzare gli addebiti per un account

Gli account creati nella registrazione del Contratto Enterprise non sono supportati nel nuovo account di fatturazione. Le sottoscrizioni dell'account appartengono alla rispettiva sezione della fattura per il relativo reparto. I proprietari dell'account possono creare e gestire le sottoscrizioni per le sezioni della fattura.

Per visualizzare il costo aggregato per le sottoscrizioni appartenenti a un account, è necessario impostare un centro di costo per ogni sottoscrizione. È quindi possibile usare il file CSV Utilizzo e addebiti di Azure per filtrare le sottoscrizioni in base al centro di costo.

### <a name="download-usage-and-charges-csv-price-sheet-and-tax-documents"></a>Scaricare il file CSV Utilizzo e addebiti, l'elenco prezzi e i documenti fiscali

Una fattura mensile viene generata per ogni profilo di fatturazione nell'account di fatturazione. Per ogni fattura, è possibile scaricare il file CSV Utilizzo e addebiti di Azure, l'elenco prezzi e il documento fiscale (se applicabile). È anche possibile scaricare il file CSV Utilizzo e addebiti di Azure per gli addebiti del mese corrente.

Per informazioni su come scaricare il file CSV Utilizzo e addebiti di Azure, vedere [Scaricare l'utilizzo per il contratto del cliente Microsoft](billing-download-azure-daily-usage.md#download-usage-for-your-microsoft-customer-agreement).

Per scoprire come scaricare l'elenco prezzi, vedere [Scaricare i prezzi per il contratto del cliente Microsoft](billing-ea-pricing.md#microsoft-customer-agreement-pricing).

Per scoprire come scaricare i documenti fiscali, vedere [Scaricare i documenti fiscali per il contratto del cliente Microsoft](billing-mca-download-tax-document.md#view-and-download-tax-documents).

### <a name="add-an-additional-enterprise-administrator"></a>Aggiungere un amministratore dell'organizzazione aggiuntivo

Concedere agli utenti l'accesso al profilo di fatturazione per consentire loro di visualizzare e gestire la fatturazione per l'organizzazione. Per consentire l'accesso, è possibile usare la pagina **Controllo di accesso (IAM)** nel portale di Azure.  Per saperne di più sui ruoli del profilo di fatturazione, vedere [Ruoli e attività del profilo di fatturazione](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

Per informazioni su come consentire l'accesso al profilo di fatturazione, vedere [Gestire i ruoli di fatturazione nel portale di Azure](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="create-a-new-department"></a>Creare un nuovo reparto

Creare una sezione della fattura per organizzare i costi in base alle esigenze, come per i reparti nella registrazione del Contratto Enterprise. È possibile creare una nuova sezione della fattura nel portale di Azure. Per altre informazioni, vedere [Creare sezioni nella fattura per organizzare i costi](billing-mca-section-invoice.md).

### <a name="create-a-new-account"></a>Creare un nuovo account

Per concedere ad altri utenti l'autorizzazione a creare una sottoscrizione di Azure, ad esempio gli account creati nella registrazione del Contratto Enterprise, si può assegnare il ruolo creatore della sottoscrizione di Azure nelle sezioni della fattura. Per altre informazioni, vedere [Concedere ad altri l'autorizzazione a creare sottoscrizioni di Azure](billing-mca-create-subscription.md#give-others-permission).

## <a name="changes-for-department-administrators"></a>Modifiche per gli amministratori del reparto

Le modifiche seguenti si applicano agli amministratori di reparto in un Contratto Enterprise che è stato rinnovato in un contratto del cliente Microsoft.

- Viene creata una sezione della fattura per ogni reparto nella registrazione del Contratto Enterprise. Le sezioni della fattura verranno usate per gestire i reparti. Per saperne di più sulle sezioni della fattura, vedere [Informazioni sulle sezioni della fattura](billing-mca-overview.md#invoice-sections).
- Per concedere ad altri utenti l'autorizzazione a creare una sottoscrizione di Azure, ad esempio gli account creati nella registrazione del Contratto Enterprise, si userà il ruolo creatore della sottoscrizione di Azure nella sezione della fattura.
- Si userà il portale di Azure per gestire la fatturazione per l'organizzazione, anziché il portale EA di Azure.

Nel nuovo account di fatturazione vengono assegnati i ruoli seguenti:

**Proprietario della sezione della fattura**: viene assegnato il ruolo di proprietario della sezione della fattura nella sezione della fattura creata per il reparto o i reparti precedentemente inclusi nel Contratto Enterprise. Il ruolo consente di visualizzare e tenere traccia delle modifiche e controllare gli utenti che possono creare sottoscrizioni di Azure e acquistare altri prodotti per la sezione della fattura.

### <a name="view-charges-for-your-department"></a>Visualizza gli addebiti per il reparto

È possibile visualizzare gli addebiti per la sezione della fattura creata per il reparto, nella pagina [Gestione dei costi e fatturazione](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview) del portale di Azure.

### <a name="add-an-additional-department-administrator"></a>Aggiungere un amministratore di reparto aggiuntivo

Viene creata una sezione della fattura per ogni reparto nel Contratto Enterprise. Per consentire ad altri l'accesso per visualizzare e gestire la sezione della fattura, è possibile usare la pagina **Controllo di accesso (IAM)** nel portale di Azure. Per saperne di più sui ruoli della sezione della fattura, vedere [Ruoli e attività della sezione della fattura](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

Per informazioni su come consentire l'accesso alla sezione della fattura, vedere [Gestire i ruoli di fatturazione nel portale di Azure](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="create-a-new-account-in-your-department"></a>Creare un nuovo account nel reparto

Assegnare agli utenti il ruolo di autore della sottoscrizione di Azure nella sezione della fattura creata per il reparto. Per altre informazioni, vedere [Concedere ad altri l'autorizzazione a creare sottoscrizioni di Azure](billing-mca-create-subscription.md#give-others-permission).

### <a name="view-charges-for-accounts-in-your-departments"></a>Visualizzare gli addebiti per gli account nei reparti

Gli account creati nella registrazione del Contratto Enterprise non sono supportati nel nuovo account di fatturazione. Le sottoscrizioni dell'account appartengono alla rispettiva sezione della fattura per il relativo reparto. I proprietari dell'account possono creare e gestire le sottoscrizioni per le sezioni della fattura.

Per visualizzare il costo aggregato per le sottoscrizioni appartenenti a un account nel proprio reparto, è necessario impostare un centro di costo per ogni sottoscrizione. È quindi possibile usare il file Utilizzo e addebiti di Azure per filtrare le sottoscrizioni in base al centro di costo.

## <a name="changes-for-account-owners"></a>Modifiche per i proprietari dell'account

I proprietari dell'account nel Contratto Enterprise ottengono l'autorizzazione per creare sottoscrizioni di Azure nel nuovo account di fatturazione. Le sottoscrizioni di Azure esistenti appartengono alla sezione della fattura creata per il proprio reparto. Se l'account non appartiene a un reparto, le sottoscrizioni appartengono a una sezione della fattura denominata Sezione della fattura predefinita.

Per creare altre sottoscrizioni di Azure, nel nuovo account di fatturazione viene assegnato il ruolo seguente.

**Autore di sottoscrizioni di Azure**: viene assegnato il ruolo di autore di sottoscrizioni di Azure nella sezione della fattura creata per il proprio reparto incluso nel Contratto Enterprise. Se l'account non appartiene a un reparto, si ottiene il ruolo di autore di sottoscrizioni di Azure in una sezione denominata Sezione della fattura predefinita. Il ruolo consente di creare sottoscrizioni di Azure per la sezione della fattura.

### <a name="create-an-azure-subscription"></a>Crea una sottoscrizione di Azure

È possibile creare sottoscrizioni di Azure per la sezione della fattura nel portale di Azure. Per altre informazioni, vedere [Creare una sottoscrizione di Azure aggiuntiva per il contratto del cliente Microsoft](billing-mca-create-subscription.md)

### <a name="view-charges-for-your-account"></a>Visualizzare gli addebiti per l'account

Per visualizzare gli addebiti per le sottoscrizioni appartenenti a un account, passare alla [pagina sottoscrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) nel portale di Azure. La pagina sottoscrizioni mostra gli addebiti per tutte le sottoscrizioni.

### <a name="view-charges-for-a-subscription"></a>Visualizzare gli addebiti per una sottoscrizione

È possibile visualizzare gli addebiti per una sottoscrizione nella [pagina sottoscrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) o nell'analisi dei costi di Azure. Per altre informazioni sull'analisi dei costi di Azure, vedere [Esplorare e analizzare i costi con l'analisi dei costi](../cost-management/quick-acm-cost-analysis.md).

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico

Se si necessita assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sull'account di fatturazione del Contratto del cliente Microsoft](billing-mca-overview.md)
- [Comprendere la fattura](billing-understand-your-bill.md)
- [Informazioni sulla fattura](billing-understand-your-invoice.md)
- [Ottenere la proprietà della fatturazione delle sottoscrizioni di Azure da altri utenti](billing-mca-request-billing-ownership.md)
