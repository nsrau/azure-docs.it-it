---
title: Verificare lo stato di pubblicazione dell'offerta del Marketplace commerciale
description: Verificare lo stato dei passaggi di convalida, certificazione e anteprima necessari per pubblicare un'offerta tramite il Marketplace commerciale nel centro per i partner Microsoft.
author: ChJenk
manager: evansma
ms.author: v-chjen
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: ec2a174c365e31520fe99ca0b505bbb62faad8c6
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70883412"
---
# <a name="check-the-publishing-status-of-your-commercial-marketplace-offer"></a>Verificare lo stato di pubblicazione dell'offerta del Marketplace commerciale

È possibile visualizzare **lo stato di pubblicazione** corrente nella scheda **Panoramica offerta** del [portale del Marketplace commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) nel centro per i partner.

Per ogni offerta deve essere visualizzato uno degli indicatori di stato seguenti.

| **Status**    | **Descrizione**  |
| :---------- | :-------------------|
| **Bozza** | L'offerta è stata creata, ma non è in fase di pubblicazione. |
| **Pubblicazione in corso** | L'offerta o il piano sta procedendo attraverso i passaggi del processo di pubblicazione. |
| **Attenzione necessaria** | Un problema critico è stato individuato durante la certificazione da Microsoft o uno dei passaggi di pubblicazione. |
| **Anteprima** | L'offerta è stata certificata da Microsoft e ora attende una verifica finale da parte del server di pubblicazione. Selezionare Go Live per rendere disponibile l'offerta. |
| **Offerta pubblicata** | L'offerta è disponibile nel Marketplace e può essere visualizzata e acquisita dai clienti. |
| **Stop sell in sospeso** | Il server di pubblicazione ha selezionato "Interrompi vendita" per l'offerta o il piano, ma l'azione non è ancora stata completata. |
| **Non disponibile nel Marketplace** | Un'offerta/piano pubblicato in precedenza nel Marketplace è stato rimosso. |

## <a name="automated-validation"></a>Convalida automatica

Il primo passaggio del processo di pubblicazione è costituito da un set di convalide automatiche. Ogni passaggio di convalida corrisponde a una funzionalità che si è scelto di abilitare durante la creazione dell'offerta. Se tale funzionalità non è stata abilitata, la convalida passa al passaggio successivo della pubblicazione. Ogni controllo di convalida deve essere completato prima che lo stato di pubblicazione venga approvato.

- **Impostazione del flusso di acquisto offerta (< 10 min)**

In questo passaggio si garantisce che l'offerta possa essere soddisfatta quando viene acquistata dai clienti tramite il portale di Azure. Questo passaggio è applicabile solo alle offerte vendute tramite Microsoft.

- **Convalida dei dati di test drive (~ 5 min)**

In questo passaggio vengono convalidati i dati forniti nella sezione relativa alla configurazione tecnica test drive dell'offerta. La funzionalità test drive è testata e approvata. Questo passaggio è applicabile solo alle offerte con una test drive abilitata.

- **Provisioning del test drive (~ 30 min)**

In questo passaggio, dopo aver convalidato i dati e le funzionalità del test drive nel passaggio precedente, verranno distribuite e replicate le istanze del test drive in modo che siano pronte per l'uso da parte dei clienti.  Questo passaggio è applicabile solo alle offerte con una test drive abilitata.

- **Convalida e registrazione del Lead Management (< 15 min)**

In questo passaggio viene verificato che il sistema di gestione dei lead possa ricevere i lead dei clienti in base ai dettagli forniti nell'installazione dell'offerta. Questo passaggio è applicabile solo alle offerte con la gestione dei lead abilitata.

## <a name="certification"></a>Certification

Prima della pubblicazione, le offerte inviate al Marketplace commerciale nel centro per i partner devono essere certificate. Le offerte inviate vengono sottoposte a test rigorosi, alcune automatizzate e altre manuali, incluso un controllo rispetto ai [criteri di certificazione di Azure Marketplace](https://docs.microsoft.com/legal/marketplace/general-policies). Gli invii di offerte devono essere contrassegnati come idonei per la certificazione prima di procedere al passaggio successivo nel flusso di pubblicazione.

### <a name="types-of-validation-that-take-place-during-certification"></a>Tipi di convalida che si verificano durante la certificazione

Esistono tre livelli di convalida inclusi nel processo di certificazione per ogni offerta inviata.

- Idoneità aziendale dell'editore
- Convalida del contenuto
- Convalida tecnica

#### <a name="publisher-business-eligibility"></a>Idoneità aziendale dell'editore

Ogni tipo di offerta controlla un set di criteri di idoneità di base che devono essere soddisfatti dall'editore. I criteri di idoneità possono includere lo stato MPN dell'editore, le competenze possedute, i livelli di competenza e così via.

#### <a name="content-validation"></a>Convalida del contenuto

Durante la convalida del contenuto, le informazioni immesse al momento della creazione dell'offerta vengono verificate per la qualità e la pertinenza. Questi controlli rileveranno le voci relative a dettagli, prezzi, disponibilità, piani associati e così via. Per soddisfare i criteri di elenco di Azure Marketplace e/o AppSource, si convaliderà che l'offerta includa:

- titolo che descrive accuratamente l'offerta;
- descrizioni ben scritte che forniscono una panoramica approfondita e una proposta di valore.
- schermate di qualità e video associati; e
- una spiegazione del modo in cui l'offerta utilizza piattaforme e strumenti Microsoft.

Per altre informazioni sui criteri di convalida del contenuto, vedere Criteri di [elenco generale](https://docs.microsoft.com/legal/marketplace/certification-policies#100-general).

#### <a name="technical-validation"></a>Convalida tecnica

Durante la convalida tecnica, l'offerta (pacchetto o binario) subisce i controlli seguenti.
- Analizzato per malware
- Chiamate di rete monitorate
- Pacchetto analizzato
- Analisi completa delle funzionalità effettive dell'offerta

L'offerta viene testata su diverse piattaforme e versioni per garantire che sia affidabile.

Esaminare i dettagli di configurazione specifici richiesti per l'offerta nella sezione configurazione tecnica di questo documento.

### <a name="certification-failure-report"></a>Report errori di certificazione

Al termine della verifica, se l'offerta ha superato la certificazione, si sposta nel passaggio successivo del processo di pubblicazione. Se l'offerta non ha superato i controlli di elenco, tecnici o criteri o se non si è idonei a inviare un'offerta di tale tipo, viene generato un report di errore di certificazione che viene inviato tramite posta elettronica all'utente.

Questo report contiene le descrizioni dei criteri non riusciti, insieme alle note di revisione. Esaminare questo report di posta elettronica, risolvere eventuali problemi, apportare aggiornamenti all'offerta, laddove necessario, e inviare nuovamente l'offerta usando il [portale di Marketplace commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) nel centro per i partner. (È possibile inviare nuovamente l'offerta il numero di volte necessario fino al passaggio della certificazione).

## <a name="preview-creation"></a>Creazione anteprima

Durante il passaggio di **creazione dell'anteprima** , viene creata una versione dell'offerta accessibile solo ai destinatari specificati nella sezione di anteprima dell'offerta.

>[!Note]
> Non usare questo passaggio per fornire agli utenti esterni all'organizzazione visibilità in un'offerta. In alternativa, usare l'opzione **offerta privata** . A questo punto, l'offerta non è stata completamente testata e convalidata e non è pronta per la distribuzione esterna.

## <a name="publisher-approval"></a>Approvazione dell'editore

In questo passaggio verrà inviato un messaggio di posta elettronica con una richiesta di verifica e approvazione dell'anteprima dell'offerta prima del passaggio di pubblicazione finale.

Se si è scelto di vendere l'offerta tramite Microsoft, sarà possibile testare l'acquisizione e la distribuzione dell'offerta per verificare che soddisfi i propri requisiti durante questa fase di approvazione della versione di anteprima. L'offerta non sarà ancora disponibile nel Marketplace del pube. Una volta testata e approvata questa anteprima, sarà necessario selezionare **Go-Live** nel dashboard [**Panoramica dell'offerta**](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) .

Se si desidera apportare modifiche all'offerta durante questa fase di anteprima, è possibile modificare e inviare di nuovo per pubblicare una nuova anteprima. Per informazioni dettagliate su altre modifiche, vedere l'articolo [aggiornare le offerte del Marketplace esistente](#update-existing-marketplace-offers) .

Se l'offerta è già attiva e disponibile per il pubblico nel Marketplace, gli eventuali aggiornamenti non verranno attivati fino a quando non si seleziona **Go-Live** nel dashboard della [**Panoramica dell'offerta**](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) .

### <a name="publish-offer-to-the-public"></a>Pubblica l'offerta per il pubblico

Accedere al centro per i partner e accedere all'offerta. Si verrà reindirizzati alla pagina di **Panoramica dell'offerta** . Nella parte superiore di questa pagina verrà visualizzata un'opzione per **Go Live**. Selezionare **Go Live** (attiva). dopo la conferma, l'offerta inizierà a essere pubblicata sul pubblico. Si riceverà una notifica di posta elettronica quando l'offerta è Live.

## <a name="publish"></a>Pubblica

Ora che si è scelto di **partecipare** all'offerta, rendendola disponibile nel Marketplace, è possibile eseguire una serie di verifiche finali sulla convalida per assicurarsi che l'offerta Live sia configurata esattamente come la versione di anteprima dell'offerta.

- **Impostazione del flusso di acquisto offerta (> 10 min)**

In questo passaggio si garantisce che l'offerta possa essere soddisfatta quando viene acquistata dai clienti tramite il portale di Azure. Questo passaggio è applicabile solo alle offerte vendute tramite Microsoft.

- **Convalida dei dati di test drive (~ 5 min)**

In questo passaggio vengono convalidati i dati forniti nella sezione relativa alla configurazione tecnica test drive dell'offerta. La funzionalità test drive è testata e approvata. Questo passaggio è applicabile solo alle offerte con una test drive abilitata.

- **Provisioning del test drive (~ 30 min)**

In questo passaggio verranno distribuite e replicate le istanze del test drive in modo che siano pronte per l'uso da parte dei clienti.  Questo passaggio è applicabile solo alle offerte con una test drive abilitata.

- **Convalida e registrazione del Lead Management (> 15 min)**

In questo passaggio viene verificato che il sistema di gestione dei lead possa ricevere i lead dei clienti in base ai dettagli forniti nell'installazione dell'offerta. Questo passaggio è applicabile solo alle offerte con la gestione dei lead abilitata.

- **Pubblicazione finale (> 30 minuti)**

In questo passaggio si garantisce che l'offerta sarà disponibile pubblicamente nel Marketplace.

## <a name="update-existing-marketplace-offers"></a>Aggiornare le offerte esistenti del Marketplace

Se si desidera apportare modifiche a un'offerta già pubblicata, è necessario prima aggiornare l'offerta esistente e quindi pubblicarla nuovamente.

## <a name="next-steps"></a>Passaggi successivi

- [Aggiornare un'offerta esistente nel Marketplace commerciale](./update-existing-offer.md)
