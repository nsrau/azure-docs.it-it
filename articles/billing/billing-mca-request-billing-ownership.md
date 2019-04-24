---
title: Ottenere la proprietà delle sottoscrizioni di Azure di fatturazione | Microsoft Docs
description: Informazioni su come richiedere la proprietà della fatturazione delle sottoscrizioni di Azure ad altri utenti.
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
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: be8c7fcebca224196d9eac7d22387989b1bdfd46
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60371923"
---
# <a name="get-billing-ownership-of-azure-subscriptions-from-other-users"></a>Ottenere la proprietà delle sottoscrizioni di Azure ad altri utenti di fatturazione

È possibile acquisire la proprietà delle sottoscrizioni di Azure se il proprietario di fatturazione esistente lascia l'organizzazione o si vuole pagare per le sottoscrizioni tramite l'account di fatturazione.

È possibile inviare una richiesta per diventare proprietari di sottoscrizioni di Azure dai proprietari esistenti in altri account di fatturazione. Assunzione di proprietà trasferisce le responsabilità di fatturazione delle sottoscrizioni per la sezione della fattura.

Per richiedere le proprietà della fatturazione, è necessario essere un **proprietario di sezione fatturazione** oppure **collaboratore alla sezione della fattura**. Per altre informazioni, vedere [attività di ruoli di sezione della fattura](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

Questo articolo si applica a un account di fatturazione per un contratto di clienti Microsoft. [Controllare se si ha accesso a un contratto di Microsoft dal cliente](#check-access-to-a-microsoft-customer-agreement).

## <a name="request-billing-ownership-in-the-azure-portal"></a>Richiedere la proprietà della fatturazione nel portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Eseguire ricerche in **Gestione dei costi e fatturazione**.

   ![Screenshot che mostra una ricerca nel portale Azure per gestione costi + fatturazione](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. Passare alla sezione Fattura. In base all'accesso, si potrebbe essere necessario selezionare un account di fatturazione o di un profilo di fatturazione. L'account di fatturazione o profilo, selezionare **sezioni della fattura** e quindi una sezione della fattura.
   <!-- Todo - Add a screenshot -->

4. Selezionare **richieste di trasferimento** dal lato inferiore sinistro.

5. Nella parte superiore della pagina selezionare **Aggiungi**.

6. Immettere l'indirizzo di posta elettronica dell'utente a da che cui si richiede la proprietà della fatturazione. L'utente deve essere un amministratore dell'account in un account di fatturazione di programma del servizio Online Microsoft o un proprietario dell'account in un contratto Enterprise Agreement. Per altre informazioni, vedere [visualizzare gli account di fatturazione nel portale di Azure](billing-view-all-accounts.md).

   ![Screenshot che mostra l'aggiunta di una nuova richiesta di trasferimento](./media/billing-mca-request-billing-ownership/mca-new-transfer-request.png)

7. Selezionare **richiesta di trasferimento della trasmissione**.

8. L'utente riceve un messaggio di posta elettronica con istruzioni per rivedere la richiesta di trasferimento.

   ![Screenshot che mostra la revisione trasferimento richiesta di posta elettronica](./media/billing-mca-request-billing-ownership/mca-review-transfer-request-email.png)

9. Per approvare la richiesta di trasferimento, l'utente seleziona il collegamento nel messaggio di posta elettronica e segue le istruzioni.

    ![Screenshot che mostra la revisione trasferimento richiesta di posta elettronica](./media/billing-mca-request-billing-ownership/mca-review-transfer-request.png)

## <a name="check-the-status-of-your-transfer-request-in-the-azure-portal"></a>Controllare lo stato della richiesta di trasferimento nel portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Eseguire ricerche in **Gestione dei costi e fatturazione**.

   ![Screenshot che mostra una ricerca nel portale Azure per gestione costi + fatturazione](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. Passare alla sezione Fattura. In base all'accesso, si potrebbe essere necessario selezionare un account di fatturazione o di un profilo di fatturazione. L'account di fatturazione o profilo, selezionare **sezioni della fattura** e quindi una sezione della fattura.
   <!-- Todo - Add a screenshot -->

4. Selezionare **richieste di trasferimento** dal lato inferiore sinistro.

5. La pagina di richieste di trasferimento consente di visualizzare le informazioni seguenti:

    ![Screenshot che mostra l'elenco di richieste di trasferimento](./media/billing-mca-request-billing-ownership/mca-view-transfer-requests.png)

   |Colonna|Definizione|
   |---------|---------|
   |Data richiesta|La data quando è stata inviata la richiesta di trasferimento|
   |Recipient|L'indirizzo di posta elettronica dell'utente che è stata inviata la richiesta di trasferimento della proprietà della fatturazione|
   |Expiration date|Data di scadenza della richiesta|
   |Stato|Lo stato della richiesta di trasferimento|

    La richiesta di trasferimento può avere uno dei seguenti stati:

   |Stato|Definizione|
   |---------|---------|
   |In corso|L'utente non ha accettato la richiesta di trasferimento|
   |Elaborazione in corso|L'utente è approvato la richiesta di trasferimento. La fatturazione per le sottoscrizioni selezionato dall'utente verrà trasferita per la sezione della fattura|
   |Completi| La fatturazione per le sottoscrizioni che l'utente ha selezionato viene trasferita alla sezione della fattura|
   |Completato con errori|Non è stato possibile trasferire la richiesta è stata completata ma la fatturazione per alcune sottoscrizioni selezionato dall'utente|
   |Scaduto|L'utente non ha accettato la richiesta nel tempo e scaduto|
   |Cancellati|Un utente con accesso alla richiesta di trasferimento annullata la richiesta|
   |Rifiutato|L'utente ha rifiutato la richiesta di trasferimento|

6. Selezionare una richiesta di trasferimento per visualizzare i dettagli. Pagina dei dettagli del trasferimento Visualizza le informazioni seguenti:
   <!-- Todo - Add a screenshot -->

   |Colonna  |Definizione|
   |---------|---------|
   |ID richiesta di trasferimento|ID univoco per la richiesta di trasferimento. Se si invia una richiesta di supporto, condividere l'ID con il supporto di Azure per accelerare la richiesta di supporto|
   |Trasferimento richiesto in|La data quando è stata inviata la richiesta di trasferimento|
   |Trasferimento richiesto da|L'indirizzo di posta elettronica dell'utente che ha inviato la richiesta di trasferimento|
   |Data di scadenza richiesta di trasferimento| Data di scadenza la richiesta di trasferimento|
   |Indirizzo di posta elettronica del destinatario|L'indirizzo di posta elettronica dell'utente che è stata inviata la richiesta di trasferimento della proprietà della fatturazione|
   |Collegamento di trasferimento inviato al destinatario|L'url che è stato inviato all'utente per esaminare la richiesta di trasferimento|

## <a name="additional-information"></a>Informazioni aggiuntive

La sezione seguente fornisce informazioni aggiuntive sul trasferimento di sottoscrizioni.

### <a name="no-service-downtime"></a>Non sono previsti tempi di inattività del servizio

Servizi di Azure nella sottoscrizione di mantengano in esecuzione senza alcuna interruzione. Solo eseguire la transizione della relazione di fatturazione per le sottoscrizioni di Azure che consente di selezionare l'utente da trasferire.

### <a name="disabled-subscriptions"></a>Sottoscrizioni disabilitate

Non è possibile trasferire le sottoscrizioni disabilitate. Le sottoscrizioni devono essere in stato attivo per trasferire le proprietà della fatturazione.

### <a name="azure-resources-transfer"></a>Trasferire le risorse di Azure

Tutte le risorse da sottoscrizioni, ad esempio le macchine virtuali, dischi e il trasferimento di siti Web.

### <a name="azure-marketplace-products-transfer"></a>Trasferire i prodotti di Azure Marketplace

I prodotti di Azure Marketplace trasferire insieme le rispettive sottoscrizioni.

### <a name="azure-reservations-transfer"></a>Trasferire le prenotazioni di Azure

Le prenotazioni di Azure non spostano automaticamente con le sottoscrizioni. [Contattare il supporto tecnico Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per spostare le prenotazioni.

### <a name="access-to-azure-services"></a>Accesso ai servizi di Azure

Accesso alle risorse di Azure che è stato impostato l'uso di Azure RBAC (controllo degli accessi basato su ruolo) non subiscono modifiche durante la transizione.

### <a name="azure-support-plan"></a>Piano di supporto tecnico di Azure

Supporto di Azure non trasferisce con le sottoscrizioni. Se l'utente vengono trasferite tutte le sottoscrizioni di Azure, chiedere loro di annullare il piano di supporto.

### <a name="charges-for-transferred-subscription"></a>Addebiti per la sottoscrizione trasferiti

Il proprietario originale fatturazione delle sottoscrizioni è responsabile di eventuali addebiti che erano stati segnalati fino al punto che il trasferimento è stato completato. La sezione della fattura è responsabile delle spese segnalate dal momento del trasferimento in avanti. Potrebbero essere presenti alcuni costi che ha avuto luogo prima del trasferimento, ma è stati segnalati successivamente. Questi addebiti visualizzati nella sezione della fattura.

### <a name="supported-offers"></a>Offerte supportate

Sottoscrizione di qualsiasi tipo o a offerte, ad eccezione del fatto offre CSP può essere trasferito.

### <a name="cancel-a-transfer-request"></a>Annullare una richiesta di trasferimento

È possibile annullare la richiesta di trasferimento fino a quando la richiesta è approvata o rifiutata. Per annullare la richiesta di trasferimento, passare alla pagina dei dettagli del trasferimento e selezionare Annulla nella parte inferiore della pagina.

### <a name="software-as-a-service-saas-transfer"></a>Software come un trasferimento di servizio (SaaS)

Non è possibile trasferire prodotti SaaS e le sottoscrizioni. Chiedere all'utente [supporto tecnico di Azure contatta](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per trasferire la proprietà della fatturazione di prodotti SaaS. Con la proprietà della fatturazione, l'utente può anche trasferire la proprietà di risorsa. La proprietà di risorse consente di eseguire operazioni di gestione, ad esempio l'eliminazione e visualizzarne i dettagli del prodotto. Utente deve essere un proprietario della risorsa su un prodotto SaaS per trasferire la proprietà di risorsa.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificare l'accesso a un contratto di Microsoft dal cliente
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico

Se si necessita assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

## <a name="next-steps"></a>Passaggi successivi

- La proprietà della fatturazione delle sottoscrizioni di Azure viene trasferito alla sezione di fattura. Tenere traccia degli addebiti per le sottoscrizioni nel [portale di Azure](https://portal.azure.com).
- Assegnare ad altri utenti le autorizzazioni per visualizzare e gestire la fatturazione per le sottoscrizioni. Per altre informazioni, vedere [fattura sezione ruoli e attività](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).
