---
title: Informazioni sui ruoli amministrativi di fatturazione per i contratti dei clienti Microsoft - Azure
description: Questo articolo offre informazioni sui ruoli di fatturazione per gli account di fatturazione di Azure per i contratti dei clienti Microsoft.
author: amberbhargava
manager: amberbhargava
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 8b4aeaa885b18f433c366788af350a7fe9c064e4
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709473"
---
# <a name="understand-microsoft-customer-agreement-administrative-roles-in-azure"></a>Informazioni sui ruoli amministrativi per i contratti dei clienti Microsoft in Azure

Per gestire l'account di fatturazione per un contratto del cliente Microsoft, usare i ruoli descritti nelle sezioni seguenti. Questi ruoli si aggiungono ai ruoli predefiniti disponibili in Azure per controllare l'accesso alle risorse. Per altre informazioni, vedere [Ruoli predefiniti per le risorse di Azure](../role-based-access-control/built-in-roles.md).

Questo articolo si applica a un account di fatturazione per un contratto del cliente Microsoft. Verificare di avere accesso a un contratto del cliente Microsoft.

## <a name="billing-role-definitions"></a>Definizioni dei ruoli di fatturazione

La tabella seguente descrive i ruoli di fatturazione usati per gestire l'account di fatturazione, i profili di fatturazione e le sezioni delle fatture.

|Ruolo|DESCRIZIONE|
|---|---|
|Proprietario dell'account di fatturazione|Consente di gestire tutti gli elementi per l'account di fatturazione|
|Collaboratore per l'account di fatturazione|Consente di gestire tutti gli elementi ad eccezione delle autorizzazioni nell'account di fatturazione|
|Lettore per l'account di fatturazione|Fornisce una visualizzazione di sola lettura di tutti gli elementi nell'account di fatturazione|
|Proprietario del profilo di fatturazione|Consente di gestire tutti gli elementi per il profilo di fatturazione|
|Collaboratore per il profilo di fatturazione|Consente di gestire tutti gli elementi ad eccezione delle autorizzazioni per il profilo di fatturazione|
|Lettore per il profilo di fatturazione|Fornisce una visualizzazione di sola lettura di tutti gli elementi nel profilo di fatturazione|
|Gestione fatture|Consente di visualizzare e pagare le fatture per il profilo di fatturazione|
|Proprietario della sezione della fattura|Consente di gestire tutti gli elementi nella sezione della fattura|
|Collaboratore per la sezione della fattura|Consente di gestire tutti gli elementi ad eccezione delle autorizzazioni nella sezione della fattura|
|Ruolo con autorizzazioni di lettura per la sezione della fattura|Fornisce una visualizzazione di sola lettura di tutti gli elementi nella sezione della fattura|
|Autore di sottoscrizioni di Azure|Consente di creare sottoscrizioni di Azure|

## <a name="billing-account-roles-and-tasks"></a>Ruoli e attività dell'account di fatturazione

Un account di fatturazione consente di gestire la fatturazione dell'organizzazione. Si usa l'account di fatturazione per organizzare i costi, monitorare gli addebiti e le fatture e controllare l'accesso alla fatturazione per la propria organizzazione. Per altre informazioni, vedere [Informazioni sull'account di fatturazione](billing-mca-overview.md#your-billing-account).

Le tabelle seguenti indicano il ruolo necessario per completare le attività nel contesto dell'account di fatturazione.

### <a name="manage-billing-account-permissions-and-properties"></a>Gestire le autorizzazioni e le proprietà dell'account di fatturazione

|Attività|Proprietario dell'account di fatturazione|Collaboratore per l'account di fatturazione|Lettore per l'account di fatturazione|
|---|---|---|---|
|Visualizzare le autorizzazioni esistenti per l'account di fatturazione|✔|✔|✔|
|Concedere ad altri utenti le autorizzazioni per visualizzare e gestire l'account di fatturazione|✔|✘|✘|
|Visualizzare le proprietà dell'account di fatturazione, come nome della società, indirizzo e altri dati|✔|✔|✔|

### <a name="manage-billing-profiles-for-billing-account"></a>Gestire i profili di fatturazione per l'account di fatturazione

|Attività|Proprietario dell'account di fatturazione|Collaboratore per l'account di fatturazione|Lettore per l'account di fatturazione|
|---|---|---|---|
|Visualizzare tutti i profili di fatturazione nell'account|✔|✔|✔|

### <a name="manage-invoices-for-billing-account"></a>Gestire le fatture per l'account di fatturazione

|Attività|Proprietario dell'account di fatturazione|Collaboratore per l'account di fatturazione|Lettore per l'account di fatturazione|
|---|---|---|---|
|Visualizzare tutte le fatture nell'account|✔|✔|✔|
|Scaricare le fatture, i file sull'utilizzo e gli addebiti di Azure, gli elenchi prezzi e i documenti fiscali nell'account|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-account"></a>Gestire le sezioni della fattura per l'account di fatturazione

|Attività|Proprietario dell'account di fatturazione|Collaboratore per l'account di fatturazione|Lettore per l'account di fatturazione|
|---|---|---|---|
|Visualizzare tutte le sezioni della fattura nell'account|✔|✔|✔|

### <a name="manage-transactions-for-billing-account"></a>Gestire le transazioni per l'account di fatturazione

|Attività|Proprietario dell'account di fatturazione|Collaboratore per l'account di fatturazione|Lettore per l'account di fatturazione|
|---|---|---|---|
|Visualizzare tutte le transazioni di fatturazione per l'account|✔|✔|✔|
|Visualizzare tutti i prodotti acquistati per l'account|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-account"></a>Gestire le sottoscrizioni per l'account di fatturazione

|Attività|Proprietario dell'account di fatturazione|Collaboratore per l'account di fatturazione|Lettore per l'account di fatturazione|
|---|---|---|---|
|Visualizzare tutte le sottoscrizioni di Azure nell'account di fatturazione|✔|✔|✔|

## <a name="billing-profile-roles-and-tasks"></a>Ruoli e attività del profilo di fatturazione

Un profilo di fatturazione consente di gestire le fatture e i metodi di pagamento. Viene generata una fattura mensile per le sottoscrizioni di Azure e altri prodotti acquistati tramite il profilo di fatturazione. Scegliere un metodo di pagamento per pagare la fattura. Per altre informazioni, vedere [Informazioni sui profili di fatturazione](billing-mca-overview.md#billing-profiles).

Le tabelle seguenti indicano il ruolo necessario per completare le attività nel contesto del profilo di fatturazione.

### <a name="manage-billing-profile-permissions-properties-and-policies"></a>Gestire le autorizzazioni, le proprietà e i criteri del profilo di fatturazione

|Attività|Proprietario del profilo di fatturazione|Collaboratore per il profilo di fatturazione|Lettore per il profilo di fatturazione|Gestione fatture|Proprietario dell'account di fatturazione|Collaboratore per l'account di fatturazione|Lettore per l'account di fatturazione
|---|---|---|---|---|---|---|---|
|Visualizzare le autorizzazioni esistenti per il profilo di fatturazione|✔|✔|✔|✔|✔|✔|✔|
|Concedere ad altri utenti le autorizzazioni per visualizzare e gestire il profilo di fatturazione|✔|✘|✘|✘|✘|✘|✘|
|Visualizzare le proprietà del profilo di fatturazione, come il numero di ordine di acquisto, le preferenze di fatturazione tramite posta elettronica e altri dati|✔|✔|✔|✔|✔|✔|✔|
|Aggiornare le proprietà del profilo di fatturazione |✔|✔|✘|✘|✘|✘|✘|
|Visualizzare i criteri applicati al profilo di fatturazione, come l'abilitazione degli acquisti di prenotazioni di Azure, l'abilitazione degli acquisti in Azure Marketplace e altri|✔|✔|✔|✔|✔|✔|✔|
|Applicare criteri al profilo di fatturazione |✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-invoices-for-billing-profile"></a>Gestire le fatture per il profilo di fatturazione

|Attività|Proprietario del profilo di fatturazione|Collaboratore per il profilo di fatturazione|Lettore per il profilo di fatturazione|Gestione fatture|Proprietario dell'account di fatturazione|Collaboratore per l'account di fatturazione|Lettore per l'account di fatturazione
|---|---|---|---|---|---|---|---|
|Visualizzare tutte le fatture per il profilo di fatturazione|✔|✔|✔|✔|✔|✔|✔|
|Scaricare le fatture, i file sull'utilizzo e gli addebiti di Azure, gli elenchi prezzi e i documenti fiscali per il profilo di fatturazione|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-profile"></a>Gestire le sezioni della fattura per il profilo di fatturazione

|Attività|Proprietario del profilo di fatturazione|Collaboratore per il profilo di fatturazione|Lettore per il profilo di fatturazione|Gestione fatture|Proprietario dell'account di fatturazione|Collaboratore per l'account di fatturazione|Lettore per l'account di fatturazione
|---|---|---|---|---|---|---|---|
|Visualizzare tutte le sezioni della fattura per il profilo di fatturazione|✔|✔|✔|✔|✔|✔|✔|
|Creare nuove sezioni della fattura per il profilo di fatturazione|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-transactions-for-billing-profile"></a>Gestire le transazioni per il profilo di fatturazione

|Attività|Proprietario del profilo di fatturazione|Collaboratore per il profilo di fatturazione|Lettore per il profilo di fatturazione|Gestione fatture|Proprietario dell'account di fatturazione|Collaboratore per l'account di fatturazione|Lettore per l'account di fatturazione
|---|---|---|---|---|---|---|---|
|Visualizzare tutte le transazioni di fatturazione per il profilo di fatturazione|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-payment-methods-for-billing-profile"></a>Gestire i metodi di pagamento per il profilo di fatturazione

|Attività|Proprietario del profilo di fatturazione|Collaboratore per il profilo di fatturazione|Lettore per il profilo di fatturazione|Gestione fatture|Proprietario dell'account di fatturazione|Collaboratore per l'account di fatturazione|Lettore per l'account di fatturazione
|---|---|---|---|---|---|---|---|
|Visualizzare i metodi di pagamento per il profilo di fatturazione|✔|✔|✔|✔|✔|✔|✔|
|Tenere traccia del saldo dei crediti di Azure per il profilo di fatturazione|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-profile"></a>Gestire le sottoscrizioni per il profilo di fatturazione

|Attività|Proprietario del profilo di fatturazione|Collaboratore per il profilo di fatturazione|Lettore per il profilo di fatturazione|Gestione fatture|Proprietario dell'account di fatturazione|Collaboratore per l'account di fatturazione|Lettore per l'account di fatturazione
|---|---|---|---|---|---|---|---|
|Visualizzare tutte le sottoscrizioni di Azure per il profilo di fatturazione|✔|✔|✔|✔|✔|✔|✔|

## <a name="invoice-section-roles-and-tasks"></a>Ruoli e attività della sezione della fattura

Una sezione della fattura consente di organizzare i costi della fattura. È possibile creare una sezione per organizzare i costi in base a un reparto, un ambiente di sviluppo o in base alle esigenze dell'organizzazione. È possibile concedere ad altri utenti l'autorizzazione per creare sottoscrizioni di Azure per la sezione. Eventuali addebiti per l'utilizzo e acquisti per le sottoscrizioni vengono quindi visualizzati nella sezione della fattura. Per altre informazioni,, vedere [Informazioni sulla sezione della fattura](billing-mca-overview.md#invoice-sections).

Le tabelle seguenti indicano il ruolo necessario per completare le attività nel contesto delle sezioni della fattura.

### <a name="manage-invoice-section-permissions-and-properties"></a>Gestire le autorizzazioni e le proprietà della sezione della fattura

|Attività|Proprietario della sezione della fattura|Collaboratore per la sezione della fattura|Ruolo con autorizzazioni di lettura per la sezione della fattura|Autore di sottoscrizioni di Azure|Proprietario dell'account di fatturazione|Collaboratore per l'account di fatturazione|Lettore per l'account di fatturazione | |
|---|---|---|---|---|---|---|---|---|
|Visualizzare tutte le autorizzazioni per la sezione della fattura|✔|✔|✔|✔|✔|✔|✔| |
|Concedere ad altri utenti le autorizzazioni per visualizzare e gestire la sezione della fattura|✔|✘|✘|✘|✘|✘|✘| |
|Visualizzare le proprietà della sezione della fattura|✔|✔|✔|✔|✔|✔|✔| |
|Aggiornare le proprietà della sezione della fattura|✔|✔|✘|✘|✘|✘|✘|✘|

### <a name="manage-products-for-invoice-section"></a>Gestire i prodotti per la sezione della fattura

|Attività|Proprietario della sezione della fattura|Collaboratore per la sezione della fattura|Ruolo con autorizzazioni di lettura per la sezione della fattura|Autore di sottoscrizioni di Azure|Proprietario dell'account di fatturazione|Collaboratore per l'account di fatturazione|Lettore per l'account di fatturazione
|---|---|---|---|---|---|---|---|
|Visualizzare tutti i prodotti acquistati nella sezione della fattura|✔|✔|✔|✘|✔|✔|✔|
|Gestire la fatturazione dei prodotti per la sezione della fattura, ad esempio annullamento, disattivazione del rinnovo automatico e altro|✔|✔|✘|✘|✘|✘|✘|
|Modificare la sezione della fattura per i prodotti|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-subscriptions-for-invoice-section"></a>Gestire le sottoscrizioni per la sezione della fattura

|Attività|Proprietario della sezione della fattura|Collaboratore per la sezione della fattura|Ruolo con autorizzazioni di lettura per la sezione della fattura|Autore di sottoscrizioni di Azure|Proprietario dell'account di fatturazione|Collaboratore per l'account di fatturazione|Lettore per l'account di fatturazione
|---|---|---|---|---|---|---|---|
|Visualizzare tutte le sottoscrizioni di Azure per la sezione della fattura|✔|✔|✔|✘|✔|✔|✔|
|Modificare la sezione della fattura per le sottoscrizioni|✔|✔|✘|✘|✘|✘|✘|
|Richiedere la proprietà della fatturazione delle sottoscrizioni da utenti in altri account di fatturazione|✔|✔|✘|✘|✘|✘|✘|

## <a name="subscription-billing-roles-and-tasks"></a>Ruoli e attività di fatturazione della sottoscrizione

La tabella seguente indica il ruolo necessario per completare le attività nel contesto di una sottoscrizione.

|Attività|Proprietario della sezione della fattura|Collaboratore per la sezione della fattura|Ruolo con autorizzazioni di lettura per la sezione della fattura|Autore di sottoscrizioni di Azure|
|---|---|---|---|---|
|Creare sottoscrizioni di Azure|✔|✔|✘|✔|
|Aggiornare il centro di costo per la sottoscrizione|✔|✔|✘|✘|
|Modificare la sezione della fattura per la sottoscrizione|✔|✔|✘|✘|

## <a name="manage-billing-roles-in-the-azure-portal"></a>Gestire i ruoli di fatturazione nel portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Cercare **Gestione dei costi e fatturazione**.

   ![Screenshot che mostra una ricerca nel portale di Azure](./media/billing-understand-mca-roles/billing-search-cost-management-billing.png)

3. Selezionare **Controllo di accesso (IAM)** in un ambito come l'account di fatturazione, il profilo di fatturazione o la sezione della fattura, a cui si vuole concedere l'accesso.

4. Nella pagina Controllo di accesso (IAM) sono elencati gli utenti e i gruppi assegnati a ogni ruolo per tale ambito.

   ![Screenshot che mostra l'elenco degli amministratori dell'account di fatturazione](./media/billing-understand-mca-roles/billing-list-admins.png)

5. Per concedere l'accesso a un utente, selezionare **Aggiungi** nella parte superiore della pagina. Nell'elenco a discesa Ruolo selezionare un ruolo. Immettere l'indirizzo di posta elettronica dell'utente a cui si vuole concedere l'accesso. Selezionare **Salva** per assegnare il ruolo.

   ![Screenshot che mostra l'aggiunta di un amministratore a un account di fatturazione](./media/billing-understand-mca-roles/billing-add-admin.png)

6. Per rimuovere l'accesso per un utente, selezionare l'utente con l'assegnazione di ruolo che si vuole rimuovere. Selezionare Rimuovi.

   ![Screenshot che mostra la rimozione di un amministratore da un account di fatturazione](./media/billing-understand-mca-roles/billing-remove-admin.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificare l'accesso a un contratto del cliente Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico
Se si necessita assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sull'account di fatturazione, vedere gli articoli seguenti:

- [Introduzione all'account di fatturazione per il contratto del cliente Microsoft](billing-mca-overview.md)
- [Creare un'altra sottoscrizione di Azure per il contratto del cliente Microsoft](billing-mca-create-subscription.md)
