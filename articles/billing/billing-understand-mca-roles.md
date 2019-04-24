---
title: Comprendere i ruoli amministrativi di fatturazione per i contratti dei clienti Microsoft - Azure
description: Informazioni sui ruoli di fatturazione per gli account in Azure di fatturazione per i contratti dei clienti Microsoft.
services: billing
documentationcenter: ''
author: amberbhargava
manager: amberbhargava
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: 780870cc71e95507a52ba6a9338026f895a96ac1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60370968"
---
# <a name="understand-microsoft-customer-agreement-administrative-roles-in-azure"></a>Comprendere i ruoli amministrativi di contratto di Microsoft dal cliente in Azure

Per gestire l'account di fatturazione per un contratto di Microsoft dal cliente, usare i ruoli descritti nelle sezioni seguenti. Questi ruoli sono oltre i ruoli predefiniti di che Azure può controllare l'accesso alle risorse. Per altre informazioni, vedere [Ruoli predefiniti per le risorse di Azure](../role-based-access-control/built-in-roles.md).

Questo articolo si applica a un account di fatturazione per un contratto di clienti Microsoft. Controllare se si ha accesso a un contratto di clienti Microsoft.

## <a name="billing-role-definitions"></a>Le definizioni di ruolo di fatturazione

Nella tabella seguente descrive i ruoli di fatturazione usare per gestire l'account di fatturazione, i profili di fatturazione e nelle sezioni della fattura.

|Ruolo|DESCRIZIONE|
|---|---|
|Proprietario dell'account di fatturazione|Gestire tutto il necessario per l'account di fatturazione|
|Collaboratore per l'account di fatturazione|Gestire tutto ad eccezione di autorizzazioni nell'account di fatturazione|
|Lettore per l'account di fatturazione|Visualizzazione di sola lettura di tutti gli elementi in account di fatturazione|
|Proprietario del profilo di fatturazione|Gestire tutti gli elementi per profilo di fatturazione|
|Collaboratore per il profilo di fatturazione|Gestire tutto ad eccezione delle autorizzazioni per il profilo di fatturazione|
|Lettore per il profilo di fatturazione|Visualizzazione di sola lettura di tutti gli elementi nel profilo di fatturazione|
|Gestione fatture|Visualizzare e pagare le fatture per profilo di fatturazione|
|Proprietario della sezione della fattura|Gestire tutti gli elementi nella sezione della fattura|
|Collaboratore per la sezione della fattura|Gestire tutto ad eccezione delle autorizzazioni per la sezione della fattura|
|Ruolo con autorizzazioni di lettura per la sezione della fattura|Visualizzazione di sola lettura di tutti gli elementi nella sezione della fattura|
|Autore di sottoscrizioni di Azure|Creare sottoscrizioni di Azure|

## <a name="billing-account-roles-and-tasks"></a>Ruoli e attività dell'account di fatturazione

Un account di fatturazione consente di gestire la fatturazione per l'organizzazione. Si usano account di fatturazione per organizzare i costi, i costi di monitoraggio e le fatture e controllare l'accesso di fatturazione per l'organizzazione. Per altre informazioni, vedere [comprendere l'account di fatturazione](billing-mca-overview.md#understand-billing-account).

Le tabelle seguenti illustrano il ruolo è necessario completare le attività nel contesto dell'account di fatturazione.

### <a name="manage-billing-account-permissions-and-properties"></a>Gestire le proprietà e autorizzazioni di account di fatturazione

|Attività|Proprietario dell'account di fatturazione|Collaboratore per l'account di fatturazione|Lettore per l'account di fatturazione|
|---|---|---|---|
|Visualizzare le autorizzazioni esistenti per l'account di fatturazione|✔|✔|✔|
|Assegnare ad altri utenti le autorizzazioni per visualizzare e gestire l'account di fatturazione|✔|✘|✘|
|Visualizzazione fatturazione account proprietà come nome della società, indirizzo e altro ancora|✔|✔|✔|

### <a name="manage-billing-profiles-for-billing-account"></a>Gestire i profili di fatturazione per l'account di fatturazione

|Attività|Proprietario dell'account di fatturazione|Collaboratore per l'account di fatturazione|Lettore per l'account di fatturazione|
|---|---|---|---|
|Visualizzare tutti i profili di fatturazione nell'account di|✔|✔|✔|

### <a name="manage-invoices-for-billing-account"></a>Gestire le fatture per account di fatturazione

|Attività|Proprietario dell'account di fatturazione|Collaboratore per l'account di fatturazione|Lettore per l'account di fatturazione|
|---|---|---|---|
|Visualizzare tutte le fatture nell'account di|✔|✔|✔|
|Scaricare le fatture, file di utilizzo e costi di Azure, elenchi prezzi e fiscali documenti nell'account|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-account"></a>Gestire le sezioni della fattura per account di fatturazione

|Attività|Proprietario dell'account di fatturazione|Collaboratore per l'account di fatturazione|Lettore per l'account di fatturazione|
|---|---|---|---|
|Visualizzare tutte le sezioni di fatturazione nell'account di|✔|✔|✔|

### <a name="manage-transactions-for-billing-account"></a>Gestire le transazioni per account di fatturazione

|Attività|Proprietario dell'account di fatturazione|Collaboratore per l'account di fatturazione|Lettore per l'account di fatturazione|
|---|---|---|---|
|Visualizzare tutte le transazioni di fatturazione per l'account|✔|✔|✔|
|Visualizzare tutti i prodotti acquistati per l'account|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-account"></a>Gestire le sottoscrizioni per l'account di fatturazione

|Attività|Proprietario dell'account di fatturazione|Collaboratore per l'account di fatturazione|Lettore per l'account di fatturazione|
|---|---|---|---|
|Visualizzare tutte le sottoscrizioni di Azure nell'account di fatturazione|✔|✔|✔|

## <a name="billing-profile-roles-and-tasks"></a>Ruoli e attività del profilo di fatturazione

Un profilo di fatturazione consente di gestire le fatture e i metodi di pagamento. Viene generata una fattura mensile per le sottoscrizioni di Azure e altri prodotti acquistati tramite il profilo di fatturazione. Utilizzare i metodi di pagamento per pagare le fatture. Per altre informazioni, vedere [comprendere i profili di fatturazione](billing-mca-overview.md#understand-billing-profiles).

Le tabelle seguenti illustrano il ruolo è necessario completare le attività nel contesto del profilo di fatturazione.

### <a name="manage-billing-profile-permissions-properties-and-policies"></a>Gestire i criteri, le proprietà e le autorizzazioni del profilo di fatturazione

|Attività|Proprietario del profilo di fatturazione|Collaboratore per il profilo di fatturazione|Lettore per il profilo di fatturazione|Invoice Manager|Proprietario dell'account di fatturazione|Collaboratore per l'account di fatturazione|Lettore per l'account di fatturazione
|---|---|---|---|---|---|---|---|
|Visualizzare le autorizzazioni esistenti per il profilo di fatturazione|✔|✔|✔|✔|✔|✔|✔|
|Assegnare ad altri utenti le autorizzazioni per visualizzare e gestire il profilo di fatturazione|✔|✘|✘|✘|✘|✘|✘|
|Visualizzare le proprietà di profilo fatturazione, ad esempio numero dell'ordine di acquisto, preferenze fattura tramite posta elettronica e altro ancora|✔|✔|✔|✔|✔|✔|✔|
|Aggiorna le proprietà del profilo di fatturazione |✔|✔|✘|✘|✘|✘|✘|
|Visualizzare i criteri applicati nel profilo di fatturazione, come abilitare l'acquisto di istanze riservate di Azure, abilitare gli acquisti su Azure marketplace e altro ancora|✔|✔|✔|✔|✔|✔|✔|
|Applicare i criteri del profilo di fatturazione |✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-invoices-for-billing-profile"></a>Gestire le fatture per profilo di fatturazione

|Attività|Proprietario del profilo di fatturazione|Collaboratore per il profilo di fatturazione|Lettore per il profilo di fatturazione|Invoice Manager|Proprietario dell'account di fatturazione|Collaboratore per l'account di fatturazione|Lettore per l'account di fatturazione
|---|---|---|---|---|---|---|---|
|Visualizzare tutte le fatture per il profilo di fatturazione|✔|✔|✔|✔|✔|✔|✔|
|Scaricare le fatture, file di utilizzo e costi di Azure, elenchi prezzi e documenti per il profilo di fatturazione fiscali|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-profile"></a>Gestire le sezioni della fattura per il profilo di fatturazione

|Attività|Proprietario del profilo di fatturazione|Collaboratore per il profilo di fatturazione|Lettore per il profilo di fatturazione|Invoice Manager|Proprietario dell'account di fatturazione|Collaboratore per l'account di fatturazione|Lettore per l'account di fatturazione
|---|---|---|---|---|---|---|---|
|Visualizzare tutte le sezioni della fattura per il profilo di fatturazione|✔|✔|✔|✔|✔|✔|✔|
|Crea una nuova sezione della fattura per il profilo di fatturazione|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-transactions-for-billing-profile"></a>Gestire le transazioni per profilo di fatturazione

|Attività|Proprietario del profilo di fatturazione|Collaboratore per il profilo di fatturazione|Lettore per il profilo di fatturazione|Invoice Manager|Proprietario dell'account di fatturazione|Collaboratore per l'account di fatturazione|Lettore per l'account di fatturazione
|---|---|---|---|---|---|---|---|
|Visualizzare tutte le transazioni di fatturazione per il profilo di fatturazione|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-payment-methods-for-billing-profile"></a>Gestisci i metodi di pagamento per profilo di fatturazione

|Attività|Proprietario del profilo di fatturazione|Collaboratore per il profilo di fatturazione|Lettore per il profilo di fatturazione|Invoice Manager|Proprietario dell'account di fatturazione|Collaboratore per l'account di fatturazione|Lettore per l'account di fatturazione
|---|---|---|---|---|---|---|---|
|Visualizzare i metodi di pagamento per il profilo di fatturazione|✔|✔|✔|✔|✔|✔|✔|
|Tenere traccia di bilanciare i crediti di Azure per il profilo di fatturazione|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-profile"></a>Gestire le sottoscrizioni per profilo di fatturazione

|Attività|Proprietario del profilo di fatturazione|Collaboratore per il profilo di fatturazione|Lettore per il profilo di fatturazione|Invoice Manager|Proprietario dell'account di fatturazione|Collaboratore per l'account di fatturazione|Lettore per l'account di fatturazione
|---|---|---|---|---|---|---|---|
|Visualizzare tutte le sottoscrizioni di Azure per il profilo di fatturazione|✔|✔|✔|✔|✔|✔|✔|

## <a name="invoice-section-roles-and-tasks"></a>Ruoli e attività della sezione della fattura

Una sezione della fattura consente di organizzare i costi in fattura. È possibile creare una sezione per organizzare i costi per reparto, ambiente di sviluppo o in base alle esigenze dell'organizzazione. Assegnare ad altri utenti autorizzati a creare sottoscrizioni di Azure per la sezione. Eventuali addebiti di utilizzo e gli acquisti per le sottoscrizioni quindi show nella sezione della fattura. Per altre informazioni, vedere [sezione della fattura Understand](billing-mca-overview.md#understand-invoice-sections).

Le tabelle seguenti illustrano il ruolo è necessario completare le attività nel contesto delle sezioni della fattura.

### <a name="manage-invoice-section-permissions-and-properties"></a>Gestire le autorizzazioni di sezione fatturazione e proprietà

|Attività|Proprietario della sezione della fattura|Collaboratore per la sezione della fattura|Ruolo con autorizzazioni di lettura per la sezione della fattura|Autore di sottoscrizioni di Azure|Proprietario dell'account di fatturazione|Collaboratore per l'account di fatturazione|Lettore per l'account di fatturazione | |
|---|---|---|---|---|---|---|---|---|
|Consente di visualizzare tutte le autorizzazioni nella sezione della fattura|✔|✔|✔|✔|✔|✔|✔| |
|Assegnare ad altri utenti le autorizzazioni per visualizzare e gestire la sezione della fattura|✔|✘|✘|✘|✘|✘|✘| |
|Visualizzare le proprietà di sezione della fattura|✔|✔|✔|✔|✔|✔|✔| |
|Aggiornare le proprietà della sezione della fattura|✔|✔|✘|✘|✘|✘|✘|✘|

### <a name="manage-products-for-invoice-section"></a>Gestire i prodotti per la sezione della fattura

|Attività|Proprietario della sezione della fattura|Collaboratore per la sezione della fattura|Ruolo con autorizzazioni di lettura per la sezione della fattura|Autore di sottoscrizioni di Azure|Proprietario dell'account di fatturazione|Collaboratore per l'account di fatturazione|Lettore per l'account di fatturazione
|---|---|---|---|---|---|---|---|
|Visualizzare tutti i prodotti acquistati nella sezione della fattura|✔|✔|✔|✘|✔|✔|✔|
|Gestire la fatturazione per i prodotti per la sezione della fattura, ad esempio Annulla, disattivare il rinnovo automatico e altro ancora|✔|✔|✘|✘|✘|✘|✘|
|Modifica sezione della fattura per i prodotti|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-subscriptions-for-invoice-section"></a>Gestione delle sottoscrizioni per la sezione della fattura

|Attività|Proprietario della sezione della fattura|Collaboratore per la sezione della fattura|Ruolo con autorizzazioni di lettura per la sezione della fattura|Autore di sottoscrizioni di Azure|Proprietario dell'account di fatturazione|Collaboratore per l'account di fatturazione|Lettore per l'account di fatturazione
|---|---|---|---|---|---|---|---|
|Visualizzare tutte le sottoscrizioni di Azure per la sezione della fattura|✔|✔|✔|✘|✔|✔|✔|
|Modifica sezione della fattura per le sottoscrizioni|✔|✔|✘|✘|✘|✘|✘|
|Richiedere la proprietà della fatturazione delle sottoscrizioni di utenti in altri account di fatturazione|✔|✔|✘|✘|✘|✘|✘|

## <a name="subscription-billing-roles-and-tasks"></a>Ruoli e attività di fatturazione della sottoscrizione

Nella tabella seguente viene illustrato il ruolo è necessario completare le attività nel contesto di una sottoscrizione.

|Attività|Proprietario della sezione della fattura|Collaboratore per la sezione della fattura|Ruolo con autorizzazioni di lettura per la sezione della fattura|Autore di sottoscrizioni di Azure|
|---|---|---|---|---|
|Creare sottoscrizioni di Azure|✔|✔|✘|✔|
|Aggiornare il centro di costo per la sottoscrizione|✔|✔|✘|✘|
|Modifica sezione della fattura per la sottoscrizione|✔|✔|✘|✘|

## <a name="manage-billing-roles-in-the-azure-portal"></a>Gestire i ruoli di fatturazione nel portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Eseguire ricerche in **Gestione dei costi e fatturazione**.

   ![Screenshot che mostra una ricerca nel portale di Azure](./media/billing-understand-mca-roles/billing-search-cost-management-billing.png)

3. Selezionare **controllo di accesso (IAM)** a livello di ambito, ad esempio account di fatturazione, profilo di fatturazione o sezione della fattura, in cui si desidera concedere l'accesso.

4. La pagina di accesso (IAM) controllo Elenca gli utenti e gruppi assegnati a ogni ruolo per quell'ambito.

   ![Screenshot che mostra l'elenco degli amministratori per account di fatturazione](./media/billing-understand-mca-roles/billing-list-admins.png)

5. Per consentire l'accesso a un utente, selezionare **Add** dalla parte superiore della pagina. Nell'elenco a discesa ruolo, selezionare un ruolo. Immettere l'indirizzo di posta elettronica dell'utente a cui si desidera concedere l'accesso. Selezionare **salvare** assegnare il ruolo.

   ![Screenshot che mostra l'aggiunta di un amministratore a un account di fatturazione](./media/billing-understand-mca-roles/billing-add-admin.png)

6. Per rimuovere l'accesso per un utente, selezionare l'utente con l'assegnazione di ruolo che si desidera rimuovere. Scegliere Rimuovi.

   ![Screenshot che mostra la rimozione di un amministratore di un account di fatturazione](./media/billing-understand-mca-roles/billing-remove-admin.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificare l'accesso a un contratto di Microsoft dal cliente
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico
Se si necessita assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti per informazioni su account di fatturazione:

- [Introduzione ad account di fatturazione per il contratto di clienti Microsoft](billing-mca-overview.md)
- [Creare una sottoscrizione di Azure per l'account di fatturazione per il contratto di clienti Microsoft](billing-mca-create-subscription.md)
