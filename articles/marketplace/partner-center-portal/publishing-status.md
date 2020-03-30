---
title: Verifica lo stato di pubblicazione della tua offerta di Marketplace commerciale
description: Controlla lo stato dei passaggi di convalida, certificazione e anteprima necessari per pubblicare un'offerta tramite il Marketplace commerciale nel Microsoft Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 012a574887d9980e0c71c3af84ff70ca8d31312c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275680"
---
# <a name="check-the-publishing-status-of-your-commercial-marketplace-offer"></a>Verifica lo stato di pubblicazione della tua offerta di Marketplace commerciale

È possibile visualizzare **lo stato di pubblicazione** corrente nella scheda **Panoramica offerta** del portale del Marketplace [commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) nel Centro per i partner.

Per ogni offerta deve essere visualizzato uno degli indicatori di stato seguenti.

| **Stato**    | **Descrizione**  |
| :---------- | :-------------------|
| **Bozza** | L'offerta è stata creata ma non viene pubblicata. |
| **Pubblicazione in corso** | Offerta/Piano sta facendo il suo modo attraverso le fasi del processo di pubblicazione. |
| **Intervento necessario** | È stato rilevato un problema critico durante la certificazione da parte di Microsoft o di uno dei passaggi di pubblicazione. |
| **Anteprima** | L'offerta è stata certificata da Microsoft e ora attende una verifica finale da parte dell'editore. Seleziona Vai in diretta per rendere l'offerta in diretta. |
| **Live** | L'offerta è attiva nel mercato e può essere vista e acquisita dai clienti. |
| **Vendita in sospeso** | L'editore ha selezionato "stop sell" in offerta o piano, ma l'azione non è ancora stata completata. |
| **Non disponibile sul marketplace** | Un'offerta/piano pubblicato in precedenza nel marketplace è stato rimosso. |

## <a name="automated-validation"></a>Convalida automatizzata

Il primo passaggio del processo di pubblicazione è un set di convalide automatiche. Ogni passaggio di convalida corrisponde a una funzionalità che hai scelto di abilitare nella creazione dell'offerta. Se tale funzionalità non è stata abilitata, la convalida passa al passaggio di pubblicazione successivo. Ogni controllo di convalida deve essere completato prima dell'approvazione dello stato di pubblicazione.

- **Impostare il flusso di acquisto dell'offerta (<10 min)**

In questo passaggio ci assicuriamo che l'offerta possa essere soddisfatta quando viene acquistata dai clienti tramite il portale di Azure.In this step, we ensure that your offer can be fulfilled when purchased by customers through the Azure portal. Questo passaggio è applicabile solo per le offerte vendute tramite Microsoft.

- **Convalida dei dati del drive di test (5 min)**

In questo passaggio vengono convalidati i dati forniti nella sezione Configurazione tecnica del test dell'offerta. La funzionalità di test drive viene testata e approvata. Questo passaggio è applicabile solo per le offerte con un test drive abilitato.

- **Provisioning delle unità di test (30 min)**

In questo passaggio, dopo aver convalidato i dati e le funzionalità del test drive nel passaggio precedente, vengono distribuite e replicate le istanze del test drive in modo che siano pronte per l'uso da parte del cliente.  Questo passaggio è applicabile solo per le offerte con un test drive abilitato.

- **Convalida e registrazione della gestione dei lead (<15 min)**

In questo passaggio confermiamo che il sistema di gestione dei lead può ricevere i lead dei clienti in base ai dettagli forniti nella configurazione dell'offerta. Questo passaggio è applicabile solo per le offerte con gestione lead abilitata.

## <a name="certification"></a>Certificazione

Prima di essere pubblicate, le offerte inviate al Marketplace commerciale nel Centro per i partner devono essere certificate. Le offerte inviate vengono sottoposte a test rigorosi, alcuni manuali automatizzati e altri, incluso un controllo sui criteri di certificazione di [Azure Marketplace.](https://docs.microsoft.com/legal/marketplace/general-policies) Gli invii dell'offerta devono essere contrassegnati come idonei per la certificazione prima di procedere al passaggio successivo del flusso di pubblicazione.

### <a name="types-of-validation-that-take-place-during-certification"></a>Tipi di convalida che avvengono durante la certificazione

Ci sono tre livelli di convalida inclusi nel processo di certificazione per ogni offerta inviata.

- Idoneità aziendale degli editori
- Convalida contenuto
- Convalida tecnica

#### <a name="publisher-business-eligibility"></a>Idoneità aziendale degli editori

Ogni tipo di offerta controlla un set di criteri di idoneità di base che l'editore deve soddisfare. I criteri di ammissibilità possono includere lo stato MPN dell'editore, le competenze mantenute, i livelli di competenza, ecc.

#### <a name="content-validation"></a>Convalida contenuto

Durante la convalida del contenuto, le informazioni immesse al momento della creazione dell'offerta vengono controllate per verificarne la qualità e la pertinenza. Questi controlli esamineranno le voci per i dettagli dell'elenco del marketplace, i prezzi, la disponibilità, i piani associati, ecc. Per soddisfare i criteri di elenco di Azure Marketplace e/o AppSource, verrà verificato che l'offerta includa:To meet the Azure Marketplace and/or AppSource listing criteria, we will validate that your offer includes:

- un titolo che descriva con precisione l'offerta;
- descrizioni ben scritte che forniscono una panoramica approfondita e una proposta di valore;
- screenshot di qualità e video di accompagnamento; E
- una spiegazione di come l'offerta utilizza piattaforme e strumenti Microsoft.

Per ulteriori informazioni sui criteri di convalida del contenuto, leggere i [criteri generali](https://docs.microsoft.com/legal/marketplace/certification-policies#100-general)di elenco .

#### <a name="technical-validation"></a>Convalida tecnica

Durante la convalida tecnica, l'offerta (pacchetto o binario) viene sottoposta ai seguenti controlli.
- Scansione per malware
- Chiamate di rete monitorate
- Pacchetto analizzato
- Scansione approfondita delle funzionalità effettive dell'offerta

L'offerta viene testata su varie piattaforme e versioni al fine di garantire che sia robusta.

Esaminare i dettagli di configurazione specifici necessari per l'offerta nella sezione Configurazione tecnica di questo documento.

### <a name="certification-failure-report"></a>Report di errore di certificazione

Al termine della revisione, se l'offerta ha superato la certificazione, passa alla fase successiva del processo di pubblicazione. Se la tua offerta non ha superato uno dei controlli di quotazione, tecnici o dei criteri o se non sei idoneo a inviare un'offerta di quel tipo, viene generato e inviato un'email all'utente un rapporto di errore di certificazione.

Questo report contiene le descrizioni di tutti i criteri non riusciti, insieme alle note di revisione. Esaminare questo report di posta elettronica, risolvere eventuali problemi, apportare aggiornamenti all'offerta dove necessario e inviare nuovamente l'offerta utilizzando il [portale del Marketplace commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) nel Centro per i partner. (È possibile inviare nuovamente l'offerta tutte le volte che è necessario fino al superamento della certificazione).

## <a name="preview-creation"></a>Creazione dell'anteprima

Durante la fase di **creazione dell'anteprima,** creiamo una versione dell'offerta accessibile solo al pubblico specificato nella sezione Anteprima dell'offerta.

>[!Note]
> Non utilizzare questo passaggio per fornire agli utenti esterni all'organizzazione visibilità in un'offerta. Utilizza invece l'opzione **Offerta privata.** A questo punto, la tua offerta non è stata completamente testata e convalidata e non è pronta per la distribuzione esterna.

## <a name="publisher-approval"></a>Approvazione editore

In questo passaggio verrà inviata tramite posta elettronica una richiesta di revisione e approvazione dell'anteprima dell'offerta prima del passaggio di pubblicazione finale.

Se si è scelto di vendere l'offerta tramite Microsoft, sarà possibile testare l'acquisizione e la distribuzione dell'offerta per assicurarsi che soddisfi i requisiti durante questa fase di approvazione dell'anteprima. La tua offerta non sarà ancora disponibile nel mercato pubblico. Dopo aver testato e approvato questa anteprima, è necessario selezionare **Go-Live** nel dashboard [**Panoramica offerta.**](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)

Se si desidera apportare modifiche all'offerta durante questa fase di anteprima, è possibile modificare e inviare nuovamente per pubblicare una nuova anteprima. Per informazioni dettagliate su altre modifiche, consulta l'articolo [Aggiornare le offerte di marketplace esistenti.](#update-existing-marketplace-offers)

Se la tua offerta è già attiva e disponibile al pubblico nel marketplace, gli aggiornamenti che apporti non saranno disponibili finché non selezioni **Go-live** nel dashboard [**Panoramica offerta.**](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)

### <a name="publish-offer-to-the-public"></a>Pubblicare l'offerta al pubblico

Accedi al Centro per i partner e accedi all'offerta. Si verrà reindirizzati alla pagina **Panoramica offerta.** Nella parte superiore di questa pagina, vedrai un'opzione per **Vai in diretta**. Seleziona **Vai in diretta e,** dopo la conferma, l'offerta inizierà a essere pubblicata al pubblico. Riceverai una notifica via email quando l'offerta è disponibile.

## <a name="publish"></a>Pubblica

Ora che hai scelto **di passare** in diretta con la tua offerta, rendendola disponibile sul marketplace, ci sono una serie di controlli di convalida finali che verranno sottoposti a un passo avanti per garantire che l'offerta live sia configurata proprio come la versione di anteprima dell'offerta.

- **Impostare il flusso di acquisto dell'offerta (>10 min)**

In questo passaggio ci assicuriamo che l'offerta possa essere soddisfatta quando viene acquistata dai clienti tramite il portale di Azure.In this step, we ensure that your offer can be fulfilled when purchased by customers through the Azure portal. Questo passaggio è applicabile solo per le offerte vendute tramite Microsoft.

- **Convalida dei dati del drive di test (5 min)**

In questo passaggio vengono convalidati i dati forniti nella sezione Configurazione tecnica del test dell'offerta. La funzionalità di test drive viene testata e approvata. Questo passaggio è applicabile solo per le offerte con un test drive abilitato.

- **Provisioning delle unità di test (30 min)**

In questo passaggio vengono distribuite e replicate le istanze del test drive in modo che siano pronte per l'uso da parte del cliente.  Questo passaggio è applicabile solo per le offerte con un test drive abilitato.

- **Convalida e registrazione della gestione dei lead (>15 min)**

In questo passaggio confermiamo che il sistema di gestione dei lead può ricevere i lead dei clienti in base ai dettagli forniti nella configurazione dell'offerta. Questo passaggio è applicabile solo per le offerte con gestione lead abilitata.

- **Pubblicazione finale (>30 minuti)**

In questo passaggio, ci assicuriamo che la tua offerta diventi pubblicamente disponibile sul mercato.

## <a name="update-existing-marketplace-offers"></a>Aggiornare le offerte del marketplace esistenti

Se si desidera apportare modifiche a un'offerta già pubblicata, è necessario innanzitutto aggiornare l'offerta esistente e quindi pubblicarla di nuovo.

## <a name="next-steps"></a>Passaggi successivi

- [Aggiornare un'offerta esistente nel marketplace commerciale](./update-existing-offer.md)
