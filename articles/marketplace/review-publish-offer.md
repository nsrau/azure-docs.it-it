---
title: Esaminare e pubblicare un'offerta in Microsoft Commercial Marketplace
description: Usare il centro per i partner per inviare l'offerta all'anteprima, visualizzare un'anteprima dell'offerta e quindi pubblicarla nel Marketplace commerciale Microsoft.
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 07/05/2020
ms.openlocfilehash: 9c05753b44b0c644b121eb3ccd3316633f9e5964
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2020
ms.locfileid: "88009529"
---
# <a name="review-and-publish-an-offer-to-the-commercial-marketplace"></a>Esaminare e pubblicare un'offerta nel Marketplace commerciale

Questo articolo illustra come usare il centro per i partner per inviare l'offerta all'anteprima, visualizzare un'anteprima dell'offerta e quindi pubblicarla in Microsoft Commercial Marketplace. Viene inoltre illustrato come controllare lo stato di pubblicazione durante la procedura di pubblicazione. È necessario avere già creato un'offerta che si desidera pubblicare.

## <a name="go-to-your-offer-in-the-commercial-marketplace"></a>Vai alla tua offerta nel Marketplace commerciale

1. Accedere al [Centro per i partner](https://partner.microsoft.com/dashboard/home).
1. Nel menu di spostamento a sinistra, selezionare **Commercial Marketplace**  >  **Overview**.
1. Nella scheda **Panoramica** , in **offerte**, uno degli indicatori di stato seguenti viene visualizzato nella colonna **stato** per ogni offerta.
 
    | Stato | Descrizione |
    | ------------ | ------------- |
    | Bozza | L'offerta è stata creata, ma non è in fase di pubblicazione. |
    | Pubblicazione in corso | L'offerta sta funzionando nel processo di pubblicazione. |
    | Intervento necessario | È stato rilevato un problema critico durante la certificazione o durante un'altra fase di pubblicazione. |
    | Anteprima | L'offerta è stata certificata, che ora attende una verifica finale da parte del server di pubblicazione. Selezionare **Go Live** per pubblicare l'offerta in tempo reale. |
    | Live | L'offerta è disponibile nel Marketplace e può essere visualizzata e acquisita dai clienti. |
    | Stop sell in sospeso | Il server di pubblicazione ha selezionato "Stop sell" per un'offerta o un piano, ma l'azione non è ancora stata completata. |
    | Non disponibile nel Marketplace | Un'offerta precedentemente pubblicata nel marketplace è stata rimossa. |
    |||

4. Nella colonna **alias offerta** selezionare l'offerta che si desidera visualizzare in anteprima e pubblicare.

## <a name="submit-your-offer-to-preview"></a>Invia l'offerta all'anteprima

1. Per inviare l'offerta all'anteprima, selezionare **revisione e pubblicazione** nell'angolo superiore destro del portale. Verrà visualizzata la pagina **revisione e pubblicazione** .
1. Verificare che nella colonna **stato** per ogni pagina sia stato **completato**. I tre possibili stati sono i seguenti:
   - **Non avviato** : la pagina non è stata toccata e deve essere completata.
   - **Incompleto** : nella pagina mancano le informazioni necessarie o sono presenti errori che devono essere corretti. È necessario tornare alla pagina e aggiornarla.
   - **Completato** : la pagina è stata completata. Sono stati forniti tutti i dati necessari e non sono presenti errori.
1. Se una delle pagine presenta uno stato diverso da **completo**, nella colonna **pagina** Selezionare il nome della pagina, correggere il problema, salvare la pagina, quindi selezionare **Verifica e pubblica** di nuovo per tornare a questa pagina.
1. Al termine di tutte le pagine, nella casella **Note per la certificazione** , fornire istruzioni di test al team di certificazione per assicurarsi che l'app venga testata correttamente. Fornire eventuali note supplementari utili per comprendere l'app.
1. Per inviare l'offerta per la pubblicazione, selezionare **Pubblica**. Viene visualizzata la pagina **Panoramica dell'offerta** che mostra lo stato di pubblicazione.

## <a name="validation-and-publishing-steps"></a>Passaggi di convalida e pubblicazione

Dopo aver selezionato **pubblica**, i processi di convalida e pubblicazione procedono in ordine. In questa tabella viene illustrato il processo di pubblicazione più comune:

| Fase | Risultato finale | 
| ------------ | ------------- | ------------- |
| Convalida automatica | Viene elaborato un set di convalide automatiche. | 
| Certificazione | Si conducono le convalide manuali. | 
| Creazione anteprima | La pagina di elenco per l'anteprima dell'offerta è disponibile per tutti gli utenti che dispongono del collegamento anteprima. Se l'offerta viene venduta tramite Microsoft (transazionale), solo i destinatari specificati nella pagina di **anteprima dei destinatari** dell'offerta possono acquistare e accedere all'offerta per il test. | 
| Approvazione dell'editore | Ti invieremo un messaggio di posta elettronica con una richiesta per visualizzare in anteprima e approvare l'offerta. | 
| Pubblica | Viene eseguita una serie di passaggi per verificare che l'offerta di anteprima venga pubblicata in diretta nel Marketplace commerciale. | 
|||

## <a name="automated-validation-phase"></a>Fase di convalida automatica

Il primo passaggio del processo di pubblicazione è costituito da un set di convalide automatiche. Ogni passaggio di convalida corrisponde a una funzionalità scelta al momento della creazione dell'offerta. Ogni controllo di convalida deve essere completato prima che l'offerta possa passare al passaggio successivo del processo di pubblicazione.

- **Impostazione del flusso di acquisto offerta (<10 min)**

   In questo passaggio si garantisce che l'offerta possa essere soddisfatta quando viene acquistata dai clienti tramite il portale di Azure. Questo passaggio è applicabile solo alle offerte vendute tramite Microsoft.
- **Convalida dei dati di test drive (~ 5 min)**

   In questo passaggio vengono convalidati i dati forniti nella pagina configurazione tecnica dell'offerta. La funzionalità test drive è testata e approvata. Questo passaggio è applicabile solo alle offerte con una test drive abilitata.

-   **Provisioning del test drive (~ 30 min)**

    In questo passaggio, dopo aver convalidato i dati e le funzionalità del test drive nel passaggio precedente, verranno distribuite e replicate le istanze del test drive in modo che siano pronte per l'uso da parte dei clienti. Questo passaggio è applicabile solo alle offerte con una test drive abilitata.

-   **Convalida e registrazione del Lead Management (<15 min)**

    In questo passaggio viene verificato che il sistema di gestione dei lead possa ricevere i lead dei clienti in base ai dettagli specificati nella pagina **installazione offerta** . Questo passaggio è applicabile solo alle offerte con la gestione dei lead abilitata.

## <a name="certification-phase"></a>Fase di certificazione

Prima della pubblicazione, le offerte inviate al Marketplace commerciale devono essere certificate. Le offerte inviate vengono sottoposte a test rigorosi, alcuni manuali automatici e altri. Per altre informazioni, vedere i [criteri di certificazione del Marketplace commerciale](https://aka.ms/commercial-marketplace-certification-policies) .

### <a name="types-of-validation-that-take-place-during-certification"></a>Tipi di convalida che si verificano durante la certificazione
Esistono tre livelli di convalida inclusi nel processo di certificazione per ogni offerta inviata.
-   Idoneità aziendale dell'editore
-   Convalida contenuto
-   Convalida tecnica

#### <a name="publisher-business-eligibility"></a>Idoneità aziendale dell'editore
Ogni tipo di offerta controlla un set di criteri di idoneità di base che devono essere soddisfatti dall'editore. I criteri di idoneità possono includere lo stato MPN dell'editore, le competenze possedute, i livelli di competenza e così via.

#### <a name="content-validation"></a>Convalida contenuto

Le informazioni immesse al momento della creazione dell'offerta vengono verificate per qualità e pertinenza. Questi controlli esamineranno le voci relative ai dettagli, ai prezzi, alla disponibilità, ai piani associati e così via del Marketplace. Per soddisfare i criteri di elenco di Azure Marketplace e Microsoft AppSource, si convaliderà che l'offerta include:
-   Titolo che descrive in modo accurato l'offerta
-   Descrizioni ben scritte che forniscono una panoramica approfondita e una proposta di valore
-   Schermate e video di qualità
-   Una spiegazione del modo in cui l'offerta utilizza piattaforme e strumenti Microsoft.

Per altre informazioni sui criteri di convalida del contenuto, vedere Criteri di [elenco generale](https://aka.ms/commercial-marketplace-certification-policies#100-general).

#### <a name="technical-validation"></a>Convalida tecnica
Durante la convalida tecnica, l'offerta (pacchetto o binario) subisce i controlli seguenti.
-   Analizzato per malware
-   Chiamate di rete monitorate
-   Pacchetto analizzato
-   Analisi completa della funzionalità dell'offerta

L'offerta viene testata su diverse piattaforme e versioni per garantirne l'affidabilità.

### <a name="certification-failure-report"></a>Report errori di certificazione

Se l'offerta non ha esito positivo, o se non si è idonei a inviare un'offerta di tale tipo, viene inviato un messaggio di errore di certificazione all'utente.

Questo report contiene le descrizioni dei criteri non riusciti, insieme alle note di revisione. Esaminare questo report di posta elettronica, risolvere eventuali problemi, apportare aggiornamenti all'offerta, laddove necessario, e inviare nuovamente l'offerta usando il [portale di Marketplace commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) nel centro per i partner. È possibile inviare nuovamente l'offerta il numero di volte necessario fino al passaggio della certificazione.

## <a name="preview-creation-phase"></a>Fase di creazione anteprima

Durante la fase di creazione dell'anteprima, viene creata una versione dell'offerta che sarà accessibile solo ai destinatari specificati nella pagina di **anteprima del pubblico** dell'offerta, se disponibile. La versione di anteprima dell'offerta non sarà disponibile per gli utenti esterni ai destinatari dell'anteprima finché non si pubblica l'offerta in tempo reale.

> [!NOTE]
> Non usare i destinatari della versione di anteprima per fornire agli utenti esterni all'organizzazione visibilità in un'offerta. In alternativa, usare l'opzione offerta privata. A questo punto, l'offerta non è stata completamente testata e convalidata e non è pronta per la distribuzione esterna. 

## <a name="publisher-signoff-phase"></a>Fase di conclusione dell'editore

Quando l'offerta è pronta per la revisione e la conclusione, ti invieremo un messaggio di posta elettronica per richiedere la verifica e l'approvazione dell'anteprima dell'offerta. È anche possibile aggiornare la pagina **Panoramica dell'offerta** nel browser per verificare se l'offerta ha raggiunto la fase di conclusione dell'editore. In caso contrario, saranno disponibili i collegamenti pulsante **Vai in diretta** e anteprima.

Lo screenshot seguente mostra la pagina di **Panoramica dell'offerta** per un'offerta SaaS. I passaggi di convalida visualizzati in questa pagina variano a seconda del tipo di offerta e delle selezioni effettuate durante la creazione dell'offerta.

![Viene illustrata la pagina di panoramica dell'offerta per un'offerta nel centro per i partner. Vengono visualizzati il pulsante Go Live e i collegamenti di anteprima.](./partner-center-portal/media/publish-status-publisher-signoff.png)

**Per visualizzare in anteprima l'offerta e la conclusione**
1. Nella pagina **Panoramica dell'offerta** , per visualizzare l'anteprima dell'offerta, selezionare il collegamento sotto il pulsante **Go Live** .
   > [!NOTE]
   > Sarà disponibile un collegamento per l'anteprima di AppSource, per l'anteprima di Azure Marketplace o per entrambi, a seconda delle opzioni selezionate durante la creazione dell'offerta. Se si sceglie di vendere l'offerta tramite Microsoft, tutti gli utenti che sono stati aggiunti ai destinatari dell'anteprima possono testare l'acquisizione e la distribuzione dell'offerta per garantire che soddisfi i requisiti durante questa fase.

1. Se si desidera apportare modifiche dopo l'anteprima dell'offerta, è possibile modificare e inviare di nuovo per pubblicare una nuova anteprima. Per ulteriori informazioni, vedere [aggiornare un'offerta esistente nel Marketplace commerciale](./partner-center-portal/update-existing-offer.md).

1. Dopo aver approvato l'anteprima, per pubblicare l'offerta Live nel Marketplace commerciale, selezionare **Go Live**.
   > [!TIP]
   > Se l'offerta è già attiva e disponibile per il pubblico nel Marketplace, gli eventuali aggiornamenti non verranno attivati fino a quando non si seleziona **Go Live**.

## <a name="publish-phase"></a>Fase di pubblicazione

Ora che si è scelto di partecipare all'offerta, rendendola disponibile nel Marketplace commerciale, viene eseguita una serie di controlli di convalida finali per garantire che l'offerta Live sia configurata esattamente come la versione di anteprima dell'offerta.

-   **Impostazione del flusso di acquisto offerta (>10 min)**

    In questo passaggio si garantisce che l'offerta possa essere soddisfatta quando viene acquistata dai clienti tramite il portale di Azure. Questo passaggio è applicabile solo alle offerte vendute tramite Microsoft.
-   **Convalida dei dati di test drive (~ 5 min)**

    In questo passaggio vengono convalidati i dati forniti nella pagina configurazione tecnica dell'offerta. La funzionalità test drive è testata e approvata. Questo passaggio è applicabile solo alle offerte con una test drive abilitata.

-   **Provisioning del test drive (~ 30 min)**

      In questo passaggio verranno distribuite e replicate le istanze del test drive in modo che siano pronte per l'uso da parte dei clienti. Questo passaggio è applicabile solo alle offerte con una test drive abilitata.
-   **Convalida e registrazione del Lead Management (>15 min)**

    In questo passaggio viene verificato che il sistema di gestione dei lead possa ricevere i lead dei clienti in base ai dettagli forniti nella pagina di **configurazione** dell'offerta dell'offerta. Questo passaggio è applicabile solo alle offerte con la gestione dei lead abilitata.

-   **Pubblicazione finale (>30 minuti)**

    In questo passaggio si garantisce che l'offerta sarà disponibile pubblicamente nel Marketplace.

Al termine di questi controlli di convalida, l'offerta sarà disponibile nel Marketplace.

## <a name="next-step"></a>Passaggio successivo

[Accedere ai report analitici per il Marketplace commerciale nel centro per i partner](./partner-center-portal/analytics.md)
