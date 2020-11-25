---
title: Ottenere la proprietà della fatturazione delle sottoscrizioni di Azure
description: Informazioni su come richiedere la proprietà della fatturazione delle sottoscrizioni di Azure da altri utenti.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 2aae71836a4eaba845e4752edf708607977763fa
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014288"
---
# <a name="get-billing-ownership-of-azure-subscriptions-from-other-accounts"></a>Ottenere la proprietà della fatturazione delle sottoscrizioni di Azure da altri account

Può capitare che si debba assumere la proprietà delle sottoscrizioni di Azure se l'attuale proprietario della fatturazione abbandona l'organizzazione o si intende pagare le sottoscrizioni tramite il proprio account di fatturazione. Quando si assume la proprietà, le responsabilità di fatturazione delle sottoscrizioni vengono trasferite al proprio account.

Questo articolo di applica a un account di fatturazione per un Contratto del cliente Microsoft. [Verificare di avere accesso a un Contratto del cliente Microsoft](#check-for-access).

Per richiedere la proprietà della fatturazione, è necessario essere un **proprietario della sezione della fattura** o un **collaboratore per la sezione della fattura**. Per altre informazioni, vedere la sezione sulle [attività dei ruoli per le sezioni della fattura](understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="request-billing-ownership"></a>Richiedere la proprietà della fatturazione

1. Accedere al [portale di Azure](https://portal.azure.com) come proprietario o collaboratore della sezione della fattura per un account di fatturazione relativo al Contratto del cliente Microsoft.
1. Cercare **Gestione dei costi e fatturazione**.  
    ![Screenshot che mostra la ricerca di Gestione dei costi e fatturazione nel portale di Azure](./media/mca-request-billing-ownership/billing-search-cost-management-billing.png)
1. Nella pagina Ambiti di fatturazione selezionare l'account di fatturazione che verrà usato per pagare l'utilizzo delle sottoscrizioni. L'account di fatturazione dovrebbe essere di tipo **Contratto del cliente Microsoft**.  
    [![Screenshot che mostra la ricerca di Gestione dei costi e fatturazione nel portale](./media/mca-request-billing-ownership/list-of-scopes.png)](./media/mca-request-billing-ownership/list-of-scopes.png#lightbox)
    > [!NOTE]
    > Il portale di Azure tiene traccia dell'ultimo ambito di fatturazione a cui si accede e lo visualizza al successivo accesso alla pagina Gestione dei costi e fatturazione. La pagina Ambiti di fatturazione non viene visualizzata se la pagina Gestione dei costi e fatturazione è stata visitata in precedenza. In tal caso, verificare di essere nell'[ambito corretto](#check-for-access). In caso contrario, [cambiare ambito](view-all-accounts.md#switch-billing-scope-in-the-azure-portal) per selezionare l'account di fatturazione relativo a un Contratto del cliente Microsoft.
1. Selezionare **Profili di fatturazione** sul lato sinistro.  
    [![Screenshot che mostra la selezione dei profili di fatturazione](./media/mca-request-billing-ownership/mca-select-profiles.png)](./media/mca-request-billing-ownership/mca-select-profiles.png#lightbox)
    > [!Note]
    > Se l'opzione Profili di fatturazione non è visualizzata, significa che non si è nell'ambito di fatturazione corretto. Occorre selezionare un account di fatturazione per un Contratto del cliente Microsoft e quindi selezionare Profili di fatturazione. Per informazioni su come cambiare ambito, vedere [Cambiare ambito di fatturazione nel portale di Azure](view-all-accounts.md#switch-billing-scope-in-the-azure-portal).
1. Selezionare un **profilo di fatturazione** dall'elenco. Dopo aver assunto la proprietà delle sottoscrizioni, il loro utilizzo verrà fatturato in base a questo profilo di fatturazione.
1. Selezionare **Sezioni della fattura** sul lato sinistro.  
    [![Screenshot che mostra la selezione di Sezioni della fattura](./media/mca-request-billing-ownership/mca-select-invoice-sections.png)](./media/mca-request-billing-ownership/mca-select-invoice-sections.png#lightbox)   
1. Selezionare una sezione della fattura nell'elenco. Dopo aver assunto la proprietà delle sottoscrizioni, il loro utilizzo verrà assegnato a questa sezione della fattura del profilo di fatturazione.
1. Selezionare **Richieste di trasferimento** sul lato sinistro e quindi **Aggiungi una nuova richiesta**.  
    [![Screenshot che mostra la selezione delle richieste di trasferimento](./media/mca-request-billing-ownership/mca-select-transfer-requests.png)](./media/mca-request-billing-ownership/mca-select-transfer-requests.png#lightbox)
1. Immettere l'indirizzo di posta elettronica dell'utente da cui viene richiesta la proprietà della fatturazione. L'utente deve essere un amministratore dell'account per un account di fatturazione del Programma di Microsoft Online Services o un proprietario di un account in un contratto Enterprise Agreement. Per altre informazioni, vedere [Visualizzare gli account di fatturazione nel portale di Azure](view-all-accounts.md). Selezionare **Invia la richiesta di trasferimento**.  
    [![Screenshot che mostra l'invio di una richiesta di trasferimento](./media/mca-request-billing-ownership/mca-send-transfer-requests.png)](./media/mca-request-billing-ownership/mca-send-transfer-requests.png#lightbox)
1. L'utente riceve un messaggio di posta elettronica con le istruzioni per esaminare la richiesta di trasferimento.  
    ![Screenshot che mostra il messaggio di posta elettronica per l'esame della richiesta di trasferimento](./media/mca-request-billing-ownership/mca-review-transfer-request-email.png)
1. Per approvare la richiesta di trasferimento, l'utente seleziona il collegamento nel messaggio di posta elettronica e segue le istruzioni.
    [![Screenshot che mostra la verifica della richiesta di trasferimento](./media/mca-request-billing-ownership/review-transfer-requests.png)](./media/mca-request-billing-ownership/review-transfer-requests.png#lightbox) L'utente può selezionare l'account di fatturazione da cui vuole trasferire i prodotti di Azure. Dopo la selezione, vengono visualizzati i prodotti idonei che possono essere trasferiti. **Nota:** le sottoscrizioni disabilitate non possono essere trasferite e verranno mostrate nell'elenco "Non-transferrable Azure Products" (Prodotti di Azure non trasferibili), se applicabile. Dopo la selezione dei prodotti di Azure da trasferire, selezionare **Convalida**.
1. L'area **Transfer Validation Result** (Risultato della convalida del trasferimento) mostrerà l'impatto dei prodotti di Azure che verranno trasferiti. Ecco gli stati possibili:
    * **Superata** -La convalida per questo prodotto di Azure è stata superata ed è possibile trasferirlo.
    * **Avviso** - È presente un avviso per il prodotto di Azure selezionato. Benché sia comunque possibile trasferire il prodotto, il trasferimento avrà impatti di cui l'utente deve essere consapevole nel caso in cui voglia eseguire azioni di mitigazione. La sottoscrizione di Azure da trasferire sfrutta ad esempio i vantaggi di un'istanza riservata. Dopo il trasferimento la sottoscrizione non riceverà più tale vantaggio. Per massimizzare i risparmi, assicurarsi che l'istanza riservata sia associata a un'altra sottoscrizione che può usare i rispettivi vantaggi. L'utente può anche scegliere invece di tornare alla pagina di selezione e deselezionare questa sottoscrizione di Azure.
    * **Non riuscita** - Il prodotto di Azure selezionato non può essere trasferito a causa di un errore. L'utente dovrà tornare alla pagina di selezione e deselezionare il prodotto per trasferire gli altri prodotti di Azure selezionati.  
    ![Screenshot che mostra l'esperienza di convalida](./media/mca-request-billing-ownership/validate-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>Verificare lo stato della richiesta di trasferimento

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Cercare **Gestione dei costi e fatturazione**.  
    ![Screenshot che mostra la ricerca di Gestione dei costi e fatturazione nel portale di Azure](./media/mca-request-billing-ownership/billing-search-cost-management-billing.png)
1. Nella pagina Ambiti di fatturazione selezionare l'account di fatturazione per il quale è stata inviata la richiesta di trasferimento.
1. Selezionare **Profili di fatturazione** sul lato sinistro.  
    [![Screenshot che mostra la selezione dei profili di fatturazione](./media/mca-request-billing-ownership/mca-select-profiles.png)](./media/mca-request-billing-ownership/mca-select-profiles.png#lightbox)
1. Selezionare il **profilo di fatturazione** per il quale è stata inviata la richiesta di trasferimento.
1. Selezionare **Sezioni della fattura** sul lato sinistro.  
    [![Screenshot che mostra la selezione di Sezioni della fattura](./media/mca-request-billing-ownership/mca-select-invoice-sections.png)](./media/mca-request-billing-ownership/mca-select-invoice-sections.png#lightbox)   
1. Selezionare nell'elenco la sezione della fattura per cui è stata inviata la richiesta di trasferimento.
1. Selezionare **Richieste di trasferimento** sul lato sinistro. Nella pagina Richieste di trasferimento vengono visualizzate le informazioni seguenti:  
    [![Screenshot che mostra l'elenco delle richieste di trasferimento](./media/mca-request-billing-ownership/mca-select-transfer-requests-for-status.png)](./media/mca-request-billing-ownership/mca-select-transfer-requests-for-status.png#lightbox)
   |Colonna|Definizione|
   |---------|---------|
   |Data richiesta|Data di invio della richiesta di trasferimento|
   |Recipient|Indirizzo di posta elettronica dell'utente che ha inviato la richiesta di trasferimento della proprietà della fatturazione|
   |Expiration date|Data di scadenza della richiesta|
   |Stato|Stato della richiesta di trasferimento|

    Alla richiesta di trasferimento può essere associato uno degli stati seguenti:

   |Stato|Definizione|
   |---------|---------|
   |In corso|L'utente non ha accettato la richiesta di trasferimento|
   |Elaborazione in corso|L'utente ha approvato la richiesta di trasferimento. La fatturazione per le sottoscrizioni che l'utente ha selezionato verrà trasferita alla sezione della fattura|
   |Completi| La fatturazione per le sottoscrizioni che l'utente ha selezionato è stata trasferita alla sezione della fattura|
   |Completato con errori|La richiesta è stata completata ma non è stato possibile trasferire la fatturazione per alcune sottoscrizioni selezionate dall'utente|
   |Scaduto|L'utente non ha accettato la richiesta in tempo e la richiesta è scaduta|
   |Cancellati|Un utente con accesso alla richiesta di trasferimento ha annullato la richiesta|
   |Rifiutata|L'utente ha rifiutato la richiesta di trasferimento|

1. Selezionare una richiesta di trasferimento per visualizzare i dettagli. Nella pagina Dettagli del trasferimento vengono visualizzate le informazioni seguenti:  
    [![Screenshot che mostra l'elenco delle sottoscrizioni trasferite](./media/mca-request-billing-ownership/mca-transfer-completed.png)](./media/mca-request-billing-ownership/mca-transfer-completed.png#lightbox)

   |Colonna  |Definizione|
   |---------|---------|
   |ID della richiesta di trasferimento|ID univoco della richiesta di trasferimento. Se si invia una richiesta di supporto, condividere l'ID con il supporto tecnico di Azure per velocizzarne la gestione|
   |Data della richiesta di trasferimento|Data di invio della richiesta di trasferimento|
   |Autore della richiesta di trasferimento|Indirizzo di posta elettronica dell'utente che ha inviato la richiesta di trasferimento|
   |Data di scadenza della richiesta di trasferimento| Data di scadenza della richiesta di trasferimento|
   |Indirizzo di posta elettronica del destinatario|Indirizzo di posta elettronica dell'utente che ha inviato la richiesta di trasferimento della proprietà della fatturazione|
   |Collegamento di trasferimento inviato al destinatario|URL inviato all'utente per esaminare la richiesta di trasferimento|

## <a name="supported-subscription-types"></a>Tipi di sottoscrizioni supportati

È possibile richiedere la proprietà della fatturazione dei tipi di sottoscrizione elencati di seguito.

- [Action pack](https://azure.microsoft.com/offers/ms-azr-0025p/)\*
- [Licenze Azure in Open](https://azure.microsoft.com/offers/ms-azr-0111p/)\*
- [Azure Pass Sponsorship](https://azure.microsoft.com/offers/azure-pass/)\*
- [Sviluppo/test Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Versione di valutazione gratuita](https://azure.microsoft.com/offers/ms-azr-0044p/)\*
- [Pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Sviluppo/test con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Piano di Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*
- [Offerta Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/)\*
- [Contratto Enterprise Microsoft](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Contratto del cliente Microsoft](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/)
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)\*
- [MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p/)\*
- [Sottoscrittori di Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/)\*
- [Sottoscrittori di Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/)\*
- [Sottoscrittori di Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)\*
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)\*
- [Sottoscrittori di Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)\*

\* Dopo il trasferimento l'eventuale credito disponibile nella sottoscrizione non sarà disponibile nel nuovo account.

\*\* Supportato solo per le sottoscrizioni negli account creati durante l'iscrizione nel sito Web di Azure.

## <a name="additional-information"></a>Informazioni aggiuntive

Nella sezione seguente sono disponibili informazioni aggiuntive sul trasferimento delle sottoscrizioni.

### <a name="no-service-downtime"></a>Non sono previsti tempi di inattività del servizio

L'esecuzione dei servizi di Azure della sottoscrizione continua senza interruzioni. Viene eseguita la transizione solo della relazione di fatturazione per le sottoscrizioni di Azure che l'utente sceglie di trasferire.

### <a name="disabled-subscriptions"></a>Sottoscrizioni disabilitate

Non è possibile trasferire le sottoscrizioni disabilitate. Per trasferire la proprietà della fatturazione, le sottoscrizioni devono trovarsi nello stato attivo.

### <a name="azure-resources-transfer"></a>Trasferimento delle risorse di Azure

Vengono trasferite tutte le risorse delle sottoscrizioni, come macchine virtuali, dischi e siti Web.

### <a name="azure-marketplace-products-transfer"></a>Trasferimento di prodotti di Azure Marketplace

I prodotti di Azure Marketplace vengono trasferiti unitamente alle rispettive sottoscrizioni.

### <a name="azure-reservations-transfer"></a>Trasferimento di prenotazioni di Azure

Se si trasferiscono sottoscrizioni con contratto Enterprise (EA) o Contratti del cliente Microsoft, le prenotazioni di Azure non vengono spostate automaticamente con le sottoscrizioni. Per spostare le prenotazioni, [contattare il supporto tecnico di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="access-to-azure-services"></a>Accesso ai servizi di Azure

La transizione non influisce sull'accesso per utenti, gruppi o entità servizio esistenti assegnati con [Controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/overview.md).

### <a name="azure-support-plan"></a>Piano di supporto tecnico di Azure

Il supporto tecnico di Azure non viene trasferito con le sottoscrizioni. Se l'utente trasferisce tutte le sottoscrizioni di Azure, chiedere di annullare il relativo piano di supporto tecnico.

### <a name="charges-for-transferred-subscription"></a>Addebiti per la sottoscrizione trasferita

Il proprietario originale della fatturazione delle sottoscrizioni è responsabile degli eventuali addebiti segnalati fino al completamento del trasferimento. La sezione della fattura è responsabile degli addebiti segnalati a partire dal momento del trasferimento. Potrebbero esserci addebiti relativi a un periodo precedente al trasferimento, ma che sono stati segnalati successivamente. Questi addebiti sono indicati nella sezione della fattura.

### <a name="cancel-a-transfer-request"></a>Annullare una richiesta di trasferimento

È possibile annullare la richiesta di trasferimento finché la richiesta non viene approvata o rifiutata. Per annullare la richiesta di trasferimento, passare alla [pagina Dettagli del trasferimento](#check-the-transfer-request-status) e selezionare Annulla nella parte inferiore della pagina.

### <a name="software-as-a-service-saas-transfer"></a>Trasferimento di prodotti SaaS

I prodotti SaaS non vengono trasferiti con le sottoscrizioni. Chiedere all'utente di [contattare il supporto tecnico di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per trasferire la proprietà della fatturazione di prodotti SaaS. Unitamente alla proprietà della fatturazione, l'utente può trasferire anche la proprietà delle risorse. La proprietà delle risorse consente di eseguire operazioni di gestione, ad esempio l'eliminazione e la visualizzazione dei dettagli del prodotto. Per trasferire la proprietà delle risorse, l'utente deve essere un proprietario delle risorse nel prodotto SaaS.

## <a name="check-for-access"></a>Verificare l'accesso
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico

Se si necessita assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

## <a name="next-steps"></a>Passaggi successivi

- La proprietà della fatturazione delle sottoscrizioni di Azure è stata trasferita alla sezione della fattura. È possibile tenere traccia degli addebiti per queste sottoscrizioni nel [portale di Azure](https://portal.azure.com).
- Concedere ad altri utenti le autorizzazioni per visualizzare e gestire la fatturazione per queste sottoscrizioni. Per altre informazioni, vedere [Ruoli e attività della sezione della fattura](understand-mca-roles.md#invoice-section-roles-and-tasks).
