---
title: Come rivedere e pubblicare un'offerta in Microsoft Commercial Marketplace
description: Usare il centro per i partner per inviare l'offerta all'anteprima, visualizzare un'anteprima dell'offerta e quindi pubblicarla nel Marketplace commerciale Microsoft.
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 08/12/2020
ms.openlocfilehash: eff1db3aca2cab77d18698634b84e8e1b1c99f7e
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89375882"
---
# <a name="how-to-review-and-publish-an-offer-to-the-commercial-marketplace"></a>Come rivedere e pubblicare un'offerta nel Marketplace commerciale

Questo articolo illustra come usare il centro per i partner per inviare l'offerta per la pubblicazione, visualizzare l'anteprima dell'offerta e quindi pubblicarla nel Marketplace commerciale. Viene inoltre illustrato come controllare lo stato di pubblicazione durante la procedura di pubblicazione. È necessario avere già creato un'offerta che si desidera pubblicare.

## <a name="offer-status"></a>Stato dell'offerta

È possibile esaminare lo stato dell'offerta nella scheda **Panoramica** del dashboard del Marketplace commerciale nel [centro](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)per i partner. Uno degli indicatori di stato seguenti viene visualizzato nella colonna **stato** per ogni offerta.

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

## <a name="validation-and-publishing-steps"></a>Passaggi di convalida e pubblicazione

Quando si è pronti per inviare un'offerta per la pubblicazione, selezionare **revisione e pubblicazione** nell'angolo superiore destro del portale. Nella pagina **revisione e pubblicazione** viene visualizzato lo stato di ogni pagina dell'offerta. i possibili elementi sono i seguenti:

   - **Non avviato** : la pagina è incompleta.
   - **Incompleto** : nella pagina mancano le informazioni necessarie o sono presenti errori che devono essere corretti. È necessario tornare alla pagina e aggiornarla.
   - **Completato** : la pagina è stata completata. Sono stati forniti tutti i dati necessari e non sono presenti errori.

Se una delle pagine presenta uno stato diverso da **completo**, è necessario correggere il problema in tale pagina, quindi tornare alla pagina **revisione e pubblicazione** per confermare che lo stato è ora **completo**. Per alcuni tipi di offerta è necessario eseguire il test. In tal caso, viene visualizzato un campo **Note per la certificazione** in cui è necessario fornire istruzioni di test al team di certificazione ed eventuali note supplementari utili per comprendere l'app.

Una volta completate tutte le pagine e immesse le note di test applicabili, selezionare **pubblica** per avviare i processi di convalida e pubblicazione. Le fasi e la sequenza complessiva possono variare a seconda del tipo di offerta che si sta pubblicando. La tabella seguente illustra un possibile flusso di pubblicazione. Ogni fase è illustrata più dettagliatamente nelle sezioni seguenti.

| Fase | Risultato finale |
| ------------ | ------------- | ------------- |
| [Convalida automatica](#automated-validation-phase) | Viene elaborato un set di convalide automatiche. |
| [Certificazione](#certification-phase) | Si conducono le convalide manuali. |
| [Creazione anteprima](#preview-creation-phase) | La pagina di elenco per l'anteprima dell'offerta è disponibile per tutti gli utenti che dispongono del collegamento anteprima. Se l'offerta viene venduta tramite Microsoft (transazionale), solo i destinatari specificati nella pagina di **anteprima dei destinatari** dell'offerta possono acquistare e accedere all'offerta per il test. |
| [Approvazione del server di pubblicazione](#publisher-sign-off-phase) | Ti invieremo un messaggio di posta elettronica con una richiesta per visualizzare in anteprima e approvare l'offerta. |
| [Pubblica](#publish-phase) | Viene eseguita una serie di passaggi per verificare che l'offerta di anteprima venga pubblicata in diretta nel Marketplace commerciale. |
|||

## <a name="automated-validation-phase"></a>Fase di convalida automatica

Il primo passaggio del processo di pubblicazione è costituito da un set di convalide automatiche. Ogni passaggio di convalida corrisponde a una funzionalità scelta al momento della creazione dell'offerta. Ogni controllo di convalida deve essere completato prima che l'offerta possa passare al passaggio successivo del processo di pubblicazione.

- **Impostazione del flusso di acquisto offerta** (<10 min)

   Si garantisce che l'offerta possa essere soddisfatta quando viene acquistata dai clienti tramite il portale di Azure. Questo passaggio è applicabile solo alle offerte vendute tramite Microsoft.

- **Convalida dei dati di test drive** (~ 5 min)

   Vengono convalidati i dati forniti nella pagina configurazione tecnica dell'offerta. Viene testato e approvato test drive funzionalità. Questo passaggio è applicabile solo alle offerte con una test drive abilitata.

- **Provisioning del test drive** (~ 30 min)

    Dopo aver convalidato i dati e le funzionalità del test drive nel passaggio precedente, vengono distribuite e replicate le istanze del test drive in modo che siano pronte per l'uso da parte dei clienti. Questo passaggio è applicabile solo alle offerte con una test drive abilitata.

- **Convalida e registrazione del Lead Management** (<15 min)

    Si conferma che il sistema di gestione dei lead può ricevere i lead dei clienti in base ai dettagli specificati nella pagina **installazione offerta** . Questo passaggio è applicabile solo alle offerte con la gestione dei lead abilitata.

## <a name="certification-phase"></a>Fase di certificazione

Le offerte inviate al Marketplace commerciale devono essere certificate prima di essere pubblicate. Le offerte vengono sottoposte a test rigorosi, alcuni manuali automatici e altri. Per altre informazioni, vedere [criteri di certificazione per Marketplace commerciali](https://aka.ms/commercial-marketplace-certification-policies).

### <a name="types-of-validation-that-take-place-during-certification"></a>Tipi di convalida che si verificano durante la certificazione

Esistono tre livelli di convalida inclusi nel processo di certificazione per ogni offerta inviata.

- Idoneità aziendale dell'editore
- Convalida contenuto
- Convalida tecnica

#### <a name="publisher-business-eligibility"></a>Idoneità aziendale dell'editore

Ogni tipo di offerta controlla un set di criteri di idoneità di base richiesti. Questo criterio può includere lo stato MPN dell'editore, le competenze possedute, i livelli di competenza e così via.

#### <a name="content-validation"></a>Convalida contenuto

Le informazioni immesse al momento della creazione dell'offerta vengono verificate per qualità e pertinenza. Questi controlli esamineranno le voci relative ai dettagli, ai prezzi, alla disponibilità, ai piani associati e così via del Marketplace. Per soddisfare i criteri di elenco di Microsoft AppSource e Azure Marketplace, si convaliderà che l'offerta include:

- Titolo che descrive in modo accurato l'offerta
- Descrizioni ben scritte che forniscono una panoramica approfondita e una proposta di valore
- Schermate e video di qualità
- Una spiegazione del modo in cui l'offerta utilizza piattaforme e strumenti Microsoft.

Per altre informazioni sui criteri di convalida del contenuto, vedere Criteri di [elenco generale](https://aka.ms/commercial-marketplace-certification-policies#100-general).

#### <a name="technical-validation"></a>Convalida tecnica

Durante la convalida tecnica, l'offerta (pacchetto o binario) subisce i controlli seguenti.

- Analizzato per malware
- Chiamate di rete monitorate
- Pacchetto analizzato
- Analisi completa della funzionalità dell'offerta

L'offerta viene testata su diverse piattaforme e versioni per garantirne l'affidabilità.

### <a name="certification-failure-report"></a>Report errori di certificazione

Se l'offerta non ha esito positivo, o se non si è idonei a inviare un'offerta di tale tipo, viene inviato un messaggio di errore di certificazione all'utente.

Questo report contiene le descrizioni dei criteri non riusciti, insieme alle note di revisione. Esaminare questo report di posta elettronica, risolvere eventuali problemi, apportare aggiornamenti all'offerta, laddove necessario, e inviare nuovamente l'offerta usando il [portale di Marketplace commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) nel centro per i partner. È possibile inviare nuovamente l'offerta il numero di volte necessario fino al passaggio della certificazione.

## <a name="preview-creation-phase"></a>Fase di creazione dell'anteprima

Durante la fase di creazione dell'anteprima, viene creata una versione dell'offerta che sarà accessibile solo ai destinatari specificati nella pagina di **anteprima del pubblico** dell'offerta, se disponibile. La versione di anteprima dell'offerta non sarà disponibile per gli utenti esterni ai destinatari dell'anteprima finché non si pubblica l'offerta in tempo reale.

> [!NOTE]
> Non usare i destinatari della versione di anteprima per fornire agli utenti esterni all'organizzazione visibilità in un'offerta. In alternativa, usare l'opzione offerta privata. A questo punto, l'offerta non è stata completamente testata e convalidata e non è pronta per la distribuzione esterna.

## <a name="publisher-sign-off-phase"></a>Fase di approvazione dell'editore

Quando l'offerta è pronta per la revisione e la disconnessione, ti invieremo un messaggio di posta elettronica per richiedere la verifica e l'approvazione dell'anteprima dell'offerta. È anche possibile aggiornare la pagina **Panoramica dell'offerta** nel browser per verificare se l'offerta ha raggiunto la fase di approvazione dell'editore. In caso contrario, saranno disponibili i collegamenti pulsante **Vai in diretta** e anteprima.

Lo screenshot seguente mostra la pagina di **Panoramica dell'offerta** per un'offerta SaaS. I passaggi di convalida visualizzati in questa pagina variano a seconda del tipo di offerta e delle selezioni effettuate durante la creazione dell'offerta.

![Viene illustrata la pagina di panoramica dell'offerta per un'offerta nel centro per i partner. Vengono visualizzati il pulsante Go Live e i collegamenti di anteprima.](./media/publish-status-publisher-signoff.png)

### <a name="previewing-and-approving-your-offer"></a>Visualizzazione in anteprima e approvazione dell'offerta

> [!IMPORTANT]
> Per convalidare il flusso di acquisto e configurazione end-to-end, acquistare l'offerta mentre è in anteprima. Invia prima una notifica a Microsoft con un [ticket di supporto](https://aka.ms/marketplacesupport) per assicurarsi che non venga elaborato alcun addebito.

Nella pagina **Panoramica dell'offerta** verranno visualizzati i collegamenti di anteprima nel pulsante **Vai in diretta** . Sarà disponibile un collegamento per l'anteprima di AppSource, per l'anteprima di Azure Marketplace o per entrambi, a seconda delle opzioni selezionate durante la creazione dell'offerta. Se si sceglie di vendere l'offerta tramite Microsoft, tutti gli utenti che sono stati aggiunti ai destinatari dell'anteprima possono testare l'acquisizione e la distribuzione dell'offerta per garantire che soddisfi i requisiti durante questa fase.

Dopo aver approvato l'anteprima, selezionare **Go Live** per pubblicare l'offerta in diretta nel Marketplace commerciale. 

Se si desidera apportare modifiche dopo l'anteprima dell'offerta, è possibile modificare e inviare nuovamente la richiesta di pubblicazione. Se l'offerta è già attiva e disponibile per il pubblico nel Marketplace, gli eventuali aggiornamenti non verranno attivati fino a quando non si seleziona **Go Live*. Per ulteriori informazioni, vedere [l'aggiornamento di un'offerta esistente nel Marketplace commerciale](./partner-center-portal/update-existing-offer.md)

## <a name="publish-phase"></a>Fase di pubblicazione

Ora che si è scelto di partecipare all'offerta, che lo rende disponibile nel Marketplace commerciale, viene eseguita una serie di controlli di convalida finali per assicurarsi che l'offerta Live sia configurata esattamente come la versione di anteprima dell'offerta.

- **Impostazione del flusso di acquisto offerta** (>10 min)

    Si garantisce che l'offerta possa essere soddisfatta quando viene acquistata dai clienti tramite il portale di Azure. Questo passaggio è applicabile solo alle offerte vendute tramite Microsoft.

- **Convalida dei dati di test drive** (~ 5 min)

    Vengono convalidati i dati forniti nella pagina configurazione tecnica dell'offerta. Viene testato e approvato test drive funzionalità. Questo passaggio è applicabile solo alle offerte con una test drive abilitata.

- **Provisioning del test drive** (~ 30 min)

    Verranno distribuite e replicate le istanze del test drive in modo che siano pronte per l'uso da parte dei clienti. Questo passaggio è applicabile solo alle offerte con una test drive abilitata.

- **Convalida e registrazione del Lead Management** (>15 min)

    Si conferma che il sistema di gestione dei lead può ricevere i lead dei clienti in base ai dettagli forniti nella pagina di **configurazione dell'offerta** . Questo passaggio è applicabile solo alle offerte con la gestione dei lead abilitata.

- **Pubblicazione finale (>30 minuti)**

    Assicuriamo che l'offerta diventi disponibile pubblicamente nel Marketplace.

Al termine di questi controlli di convalida, l'offerta sarà disponibile nel Marketplace.

## <a name="next-steps"></a>Passaggi successivi

[Accedere ai report analitici per il Marketplace commerciale nel centro per i partner](partner-center-portal/analytics.md)
