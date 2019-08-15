---
title: Ottenere la proprietà della fatturazione delle sottoscrizioni di Azure
description: Informazioni su come richiedere la proprietà della fatturazione delle sottoscrizioni di Azure da altri utenti.
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
ms.openlocfilehash: 110bd975de1d865c2aa3d3b088c98cdc8b9e45ad
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2019
ms.locfileid: "69019567"
---
# <a name="get-billing-ownership-of-azure-subscriptions-from-other-accounts"></a>Ottenere la proprietà della fatturazione delle sottoscrizioni di Azure da altri account

È possibile che si desideri assumere la proprietà delle sottoscrizioni di Azure se il proprietario della fatturazione esistente abbandona l'organizzazione o si desidera pagare le sottoscrizioni tramite il proprio account di fatturazione. L'assunzione della proprietà trasferisce le responsabilità di fatturazione delle sottoscrizioni al tuo account.

Questo articolo si applica a un account di fatturazione per un contratto per i clienti Microsoft. [Controllare se si dispone dell'accesso a un contratto per i clienti Microsoft](#check-for-access).

Per richiedere la proprietà della fatturazione, è necessario essere un **proprietario della sezione della fattura** o un collaboratore della sezione della **fattura**. Per altre informazioni, vedere [attività dei ruoli della sezione della fattura](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="request-billing-ownership"></a>Richiedi la proprietà della fatturazione

1. Accedere al [portale di Azure](https://portal.azure.com) come proprietario o collaboratore di una sezione di fattura per un account di fatturazione per il contratto del cliente Microsoft.

2. Cercare **Gestione costi e fatturazione**.

   ![Screenshot che Mostra portale di Azure ricerca di gestione costi e fatturazione](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. Selezionare le **sezioni fattura** dal lato sinistro. A seconda dell'accesso, potrebbe essere necessario selezionare un account di fatturazione o un profilo di fatturazione. Dal profilo o account di fatturazione selezionare **sezioni fattura**.
   
   ![Screenshot che mostra la selezione di sezioni di fattura](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. Selezionare una sezione di fattura nell'elenco. Una volta acquisita la proprietà delle sottoscrizioni, queste verranno fatturate in base a questa sezione di fattura.

5. Selezionare **Trasferisci richieste** dal lato inferiore sinistro, quindi selezionare **Aggiungi**.
 
   ![Screenshot che mostra la selezione delle richieste di trasferimento](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests.png)

6. Immettere l'indirizzo di posta elettronica dell'utente da cui viene richiesta la proprietà della fatturazione. L'utente deve essere un amministratore dell'account per un account di fatturazione del programma di Microsoft Online Services o un proprietario di un account in un Enterprise Agreement. Per altre informazioni, vedere [visualizzare gli account di fatturazione in portale di Azure](billing-view-all-accounts.md). Selezionare **Invia richiesta di trasferimento**.

   ![Screenshot che mostra l'invio di una richiesta di trasferimento](./media/billing-mca-request-billing-ownership/mca-send-transfer-requests.png)

7. L'utente riceve un messaggio di posta elettronica con le istruzioni per esaminare la richiesta di trasferimento.

   ![Screenshot che mostra l'indirizzo di posta elettronica della richiesta di trasferimento](./media/billing-mca-request-billing-ownership/mca-review-transfer-request-email.png)

8. Per approvare la richiesta di trasferimento, l'utente seleziona il collegamento nel messaggio di posta elettronica e segue le istruzioni.

    ![Screenshot che mostra l'indirizzo di posta elettronica della richiesta di trasferimento](./media/billing-mca-request-billing-ownership/mca-review-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>Verificare lo stato della richiesta di trasferimento

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Cercare **Gestione costi e fatturazione**.

   ![Screenshot che Mostra portale di Azure ricerca di gestione costi e fatturazione](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)


3. Selezionare le **sezioni fattura** dal lato sinistro. A seconda dell'accesso, potrebbe essere necessario selezionare un account di fatturazione o un profilo di fatturazione. Dal profilo o account di fatturazione selezionare **sezioni fattura**.
   
   ![Screenshot che mostra la selezione di sezioni di fattura](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. Selezionare la sezione della fattura nell'elenco per cui è stata inviata la richiesta di trasferimento.

5. Selezionare le **richieste di trasferimento** dal lato inferiore sinistro. Nella pagina richieste di trasferimento vengono visualizzate le informazioni seguenti:

    ![Screenshot che mostra l'elenco delle richieste di trasferimento](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests-for-status.png)

   |Colonna|Definizione|
   |---------|---------|
   |Data richiesta|Data di invio della richiesta di trasferimento|
   |Destinatario|Indirizzo di posta elettronica dell'utente che ha inviato la richiesta di trasferimento della proprietà della fatturazione|
   |Data scadenza|Data di scadenza della richiesta|
   |Stato|Stato della richiesta di trasferimento|

    La richiesta di trasferimento può avere uno degli Stati seguenti:

   |Stato|Definizione|
   |---------|---------|
   |In corso|L'utente non ha accettato la richiesta di trasferimento|
   |Elaborazione in corso|L'utente ha approvato la richiesta di trasferimento. Fatturazione per le sottoscrizioni che l'utente ha selezionato viene trasferito alla sezione della fattura|
   |Operazione completata| La fatturazione per le sottoscrizioni selezionate dall'utente viene trasferita alla sezione della fattura|
   |Completato con errori|La richiesta è stata completata ma non è stato possibile trasferire la fatturazione per alcune sottoscrizioni selezionate dall'utente|
   |Scaduto|L'utente non ha accettato la richiesta in tempo ed è scaduta|
   |Annullato|Un utente con accesso alla richiesta di trasferimento ha annullato la richiesta|
   |Rifiutato|L'utente ha rifiutato la richiesta di trasferimento|

7. Selezionare una richiesta di trasferimento per visualizzare i dettagli. Nella pagina Dettagli trasferimento vengono visualizzate le seguenti informazioni:
   
   ![Screenshot che mostra l'elenco delle sottoscrizioni trasferite](./media/billing-mca-request-billing-ownership/mca-transfer-completed.png)
    
   |Colonna  |Definizione|
   |---------|---------|
   |ID richiesta di trasferimento|ID univoco per la richiesta di trasferimento. Se si invia una richiesta di supporto, condividere l'ID con il supporto di Azure per velocizzare la richiesta di supporto|
   |Trasferimento richiesto in|Data di invio della richiesta di trasferimento|
   |Trasferimento richiesto da|Indirizzo di posta elettronica dell'utente che ha inviato la richiesta di trasferimento|
   |La richiesta di trasferimento scade il| Data di scadenza della richiesta di trasferimento|
   |Indirizzo di posta elettronica del destinatario|Indirizzo di posta elettronica dell'utente che ha inviato la richiesta di trasferimento della proprietà della fatturazione|
   |Collegamento di trasferimento inviato al destinatario|URL inviato all'utente per esaminare la richiesta di trasferimento|

## <a name="supported-subscription-types"></a>Tipi di sottoscrizioni supportati

È possibile richiedere la proprietà della fatturazione dei tipi di sottoscrizione elencati di seguito.

- [Action Pack](https://azure.microsoft.com/offers/ms-azr-0025p/)\* 
- [Licenze Azure in Open](https://azure.microsoft.com/offers/ms-azr-0111p/)\*
- [Sponsorizzazione Azure Pass](https://azure.microsoft.com/offers/azure-pass/)\*
- [Sviluppo/test Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Versione di valutazione gratuita](https://azure.microsoft.com/offers/ms-azr-0044p/)\*
- [Pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/) 
- [Sviluppo/test con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Piano di Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*
- [Microsoft Azure Offerta sponsorizzata](https://azure.microsoft.com/offers/ms-azr-0036p/)\*
- [Contratto Enterprise Microsoft](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)\*
- [MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p/)\*
- [Sottoscrittori Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/)\*
- [Sottoscrittori Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/)\*
- [Sottoscrittori Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)\*
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)\*
- [Sottoscrittori Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)\*

\*Qualsiasi credito disponibile per la sottoscrizione non sarà disponibile nel nuovo account dopo il trasferimento.

\*\*Supportato solo per le sottoscrizioni negli account creati durante l'iscrizione nel sito Web di Azure.


## <a name="additional-information"></a>Informazioni aggiuntive

Nella sezione seguente vengono fornite ulteriori informazioni sul trasferimento di sottoscrizioni.

### <a name="no-service-downtime"></a>Non sono previsti tempi di inattività del servizio

I servizi di Azure nella sottoscrizione continuano a funzionare senza interruzioni. Viene solo effettuata la transizione della relazione di fatturazione per le sottoscrizioni di Azure selezionate dall'utente per il trasferimento.

### <a name="disabled-subscriptions"></a>Sottoscrizioni disabilitate

Non è possibile trasferire le sottoscrizioni disabilitate. Le sottoscrizioni devono essere in stato attivo per trasferire la proprietà della fatturazione.

### <a name="azure-resources-transfer"></a>Trasferimento di risorse di Azure

Tutte le risorse delle sottoscrizioni, ad esempio macchine virtuali, dischi e siti Web, vengono trasferite.

### <a name="azure-marketplace-products-transfer"></a>Trasferimento di prodotti Azure Marketplace

I prodotti Azure Marketplace vengono trasferiti insieme alle rispettive sottoscrizioni.

### <a name="azure-reservations-transfer"></a>Trasferimento prenotazioni di Azure

Le prenotazioni di Azure non vengono spostate automaticamente con le sottoscrizioni. [Contattare il supporto tecnico di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per spostare le prenotazioni.

### <a name="access-to-azure-services"></a>Accesso ai servizi di Azure

Accesso per gli utenti, i gruppi o le entità servizio esistenti assegnati usando (controllo degli accessi in base al ruolo) (RBAC di Azure) [... /Role-Based-Access-Control/Overview.MD] non è influenzato durante la transizione.

### <a name="azure-support-plan"></a>Piano di supporto di Azure

Il supporto tecnico di Azure non viene trasferito con le sottoscrizioni. Se l'utente trasferisce tutte le sottoscrizioni di Azure, chiedere di annullare il piano di supporto.

### <a name="charges-for-transferred-subscription"></a>Addebiti per la sottoscrizione trasferita

Il proprietario della fatturazione originale delle sottoscrizioni è responsabile di tutti gli addebiti segnalati fino al termine del trasferimento. La sezione relativa alla fattura è responsabile degli addebiti segnalati a partire dal momento del trasferimento. Potrebbero essere presenti addebiti che hanno avuto luogo prima del trasferimento, ma sono stati segnalati in seguito. Questi addebiti vengono visualizzati nella sezione fattura.

### <a name="cancel-a-transfer-request"></a>Annulla una richiesta di trasferimento

È possibile annullare la richiesta di trasferimento finché la richiesta non viene approvata o rifiutata. Per annullare la richiesta di trasferimento, passare alla [pagina Dettagli trasferimento](#check-the-transfer-request-status) e selezionare Annulla nella parte inferiore della pagina.

### <a name="software-as-a-service-saas-transfer"></a>Trasferimento SaaS (software as a Service)

I prodotti SaaS non vengono trasferiti con le sottoscrizioni. Chiedere all'utente di [contattare il supporto tecnico di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per trasferire la proprietà di fatturazione dei prodotti SaaS. Insieme alla proprietà fatturazione, l'utente può anche trasferire la proprietà delle risorse. La proprietà delle risorse consente di eseguire operazioni di gestione, ad esempio l'eliminazione e la visualizzazione dei dettagli del prodotto. L'utente deve essere un proprietario di risorse nel prodotto SaaS per trasferire la proprietà delle risorse.

## <a name="check-for-access"></a>Verifica l'accesso
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contatta il supporto tecnico

Se si necessita assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

## <a name="next-steps"></a>Passaggi successivi

- Il proprietario della fatturazione delle sottoscrizioni di Azure viene trasferito alla sezione fattura. Tenere traccia degli addebiti per queste sottoscrizioni nella [portale di Azure](https://portal.azure.com).
- Concedere ad altri utenti le autorizzazioni per visualizzare e gestire la fatturazione per queste sottoscrizioni. Per altre informazioni, vedere [ruoli e attività della sezione della fattura](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).
