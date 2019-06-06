---
title: Controllare lo stato di pubblicazione dell'offerta commerciale Marketplace
description: Controllare lo stato della convalida, certificazione e anteprima passaggi necessari per pubblicare un'offerta tramite il Marketplace commerciale in Microsoft Partner Center.
author: mattwojo
manager: evansma
ms.author: mattwoj
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 9bf3136969974abbe9a99a5632478e3cbb22307e
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66474518"
---
# <a name="check-the-publishing-status-of-your-commercial-marketplace-offer"></a>Controllare lo stato di pubblicazione dell'offerta commerciale Marketplace

È possibile visualizzare correnti **lo stato di pubblicazione** nel **panoramica dell'offerta** scheda della finestra di [portale Marketplace commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) nel centro per i Partner.

Per ogni offerta deve essere visualizzato uno degli indicatori di stato seguenti.

| **Status**    | **Descrizione**  |
| :---------- | :-------------------|
| **Bozza** | Offerta è stata creata ma non è in corso di pubblicazione. |
| **Pubblicazione in corso** | Piano di offerta/è in modo i passaggi del processo di pubblicazione. |
| **Attenzione necessita** | Un problema critico è stato rilevato durante la certificazione da Microsoft o i passaggi di pubblicazione. |
| **Anteprima** | Offerta è stata certificata da Microsoft e a questo punto resta in attesa una verifica finale dal server di pubblicazione. Selezionare go live a rendere l'offerta in tempo reale. |
| **Offerta pubblicata** | Offerta è disponibile nel marketplace e può essere visualizzata e acquisita da parte dei clienti. |
| **Origine della vendita arresto in sospeso** | Server di pubblicazione selezionato "stop Selling" in offerta o piano, ma l'azione non è stata ancora completata. |
| **Non è disponibile nel marketplace** | È stato rimosso un pubblicati in precedenza/piano di offerta nel marketplace. |

## <a name="automated-validation"></a>Convalida automatizzati

Il primo passaggio nel processo di pubblicazione è una serie di convalide automatizzate. Ogni passaggio di convalida corrisponde a una funzionalità che si è scelto di abilitare la creazione dell'offerta. Se tale funzionalità non è stato abilitato, la convalida passa con il passaggio successivo di pubblicazione. Ogni controllo di convalida deve essere completata prima che lo stato di pubblicazione è stato approvato.

- **Impostazione del flusso di acquisto dell'offerta (< 10 min)**

In questo passaggio, si assicura che l'offerta può essere soddisfatta dai clienti tramite il portale di Azure con l'acquisto. Questo passaggio riguarda solo per le offerte da venduti in Microsoft.

- **Testare la convalida dei dati di unità (5 min)**

In questo passaggio è convalidare i dati fornita nella sezione di configurazione tecnici dell'offerta, il test drive. Funzionalità di test unità viene testata e approvata. Questo passaggio è applicabile per le offerte solo con una test drive abilitato.

- **Testare il provisioning di unità (circa 30 min)**

In questo passaggio, dopo la convalida di dati e le funzionalità di un test dell'unità nel passaggio precedente, abbiamo distribuire e replicare le istanze di un test dell'unità in modo che siano pronti per l'uso dei clienti.  Questo passaggio è applicabile per le offerte solo con una test drive abilitato.

- **Portare la convalida di gestione e la registrazione (< 15 min)**

In questo passaggio è verificare che il sistema di gestione lead può ricevere clienti potenziali clienti in base ai dettagli forniti nel programma di installazione offerta. Questo passaggio è applicabile per le offerte solo con la gestione dei Lead abilitata.

## <a name="certification"></a>Certificazione

Prima di essere pubblicata, devono essere certificate offerte presentate nel Marketplace nel centro per i Partner commerciali. Inviate le offerte vengono sottoposti a test rigorosi, alcune automatizzati e altri manuali, tra cui un controllo a fronte di [criteri di certificazione di Azure Marketplace](https://docs.microsoft.com/legal/marketplace/general-policies). Gli invii devono essere contrassegnati idonei per la certificazione prima di procedere al passaggio successivo nel flusso di pubblicazione dell'offerta.

### <a name="types-of-validation-that-take-place-during-certification"></a>Tipi di convalida che si verificano durante la certificazione

Esistono tre livelli di convalida inclusi nel processo di certificazione per ogni offerta inviato.

- Idoneità business server di pubblicazione
- Convalida del contenuto
- Convalida tecnica

#### <a name="publisher-business-eligibility"></a>Idoneità business server di pubblicazione

Ogni tipo di offerta controlla un set di criteri di idoneità di base che il server di pubblicazione deve soddisfare. I criteri di idoneità possono includere lo stato MPN dell'editore, competenze mantenute, livelli di competenza e così via.

#### <a name="content-validation"></a>Convalida del contenuto

Durante la convalida del contenuto, le informazioni immesse al momento della creazione dell'offerta viene verificata la qualità e la rilevanza. Questi controlli verranno esaminati i dati immessi per il marketplace Elenca i dettagli, prezzi, disponibilità, i piani associati e così via. Per soddisfare il Marketplace di Azure e/o AppSource listato criteri, viene eseguita la convalida che l'offerta include:

- un nome che descriva in modo accurato l'offerta;
- descrizioni ben scritte che forniscono una panoramica completa e una proposta di valore;
- screenshot di qualità e che accompagna i video; e
- Spiegazione del modo in cui l'offerta Usa gli strumenti e piattaforme Microsoft.

Altre informazioni relative ai criteri di convalida del contenuto, vedere la [elenco di criteri generale](https://docs.microsoft.com/legal/marketplace/general-policies#10-general-listing-policies).

#### <a name="technical-validation"></a>Convalida tecnica

Durante la convalida tecnica, all'offerta (pacchetto o binaria) viene sottoposto a controlli seguenti.
- Scansione di malware
- Chiamate di rete monitorate
- Pacchetto analizzato
- L'analisi completa di funzionalità effettive dell'offerta

L'offerta è stato testato su varie piattaforme e le versioni per assicurarsi che sia affidabile.

Esaminare i dettagli di configurazione specifiche necessari per l'offerta nella sezione di configurazione di tecniche di questo documento.

### <a name="certification-failure-report"></a>Report degli errori di certificazione

Dopo il completamento della verifica, se l'offerta ha superato certificazione quindi si sposta lungo al passaggio successivo nel processo di pubblicazione. Se l'offerta non è riuscito uno dei controlli elenco, tecnici o dei criteri, o se non si è idonei per inviare un'offerta di tale tipo, un report di errori di certificazione viene generato e inviato tramite posta elettronica all'utente.

Questo report contiene le descrizioni di tutti i criteri che non è riuscito, insieme alle note sulla revisione. Esaminare il report tramite posta elettronica, risolvere eventuali problemi, effettuare gli aggiornamenti per l'offerta laddove necessario e quindi inviare nuovamente l'offerta usando il [portale Marketplace commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) nel centro per i Partner. (È possibile inviare nuovamente l'offerta tutte le volte in base alle esigenze finché il passaggio di certificazione).

## <a name="preview-creation"></a>Creazione di anteprima

Durante la **anteprima creazione** passaggio, creiamo una versione dell'offerta accessibile al gruppo di destinatari specificato nella sezione di anteprima dell'offerta.

## <a name="publisher-approval"></a>Approvazione di server di pubblicazione

In questo passaggio, verrà inviato un messaggio con una richiesta per esaminare e approvare la versione di anteprima di offerta prima il passaggio finale di pubblicazione.

Se sono stati selezionati per la vendita dell'offerta tramite il Microsoft, sarà possibile testare l'acquisizione e distribuzione dell'offerta per assicurarsi che vengano soddisfatti i requisiti durante questa fase di approvazione di anteprima. L'offerta non ancora sarà disponibile nel marketplace sono riportate alcune. Dopo aver verificato e approvare questa versione di anteprima, sarà necessario selezionare **Go-Live** nel [ **Panoramica offrono** ](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) dashboard.

Se si desidera apportare modifiche all'offerta durante questa fase di anteprima, è possibile modificare e inviare di nuovo per pubblicare una nuova versione di anteprima. Vedere l'articolo [aggiornamento offerte di marketplace esistenti](#update-existing-marketplace-offers) per informazioni dettagliate sulle altre modifiche.

Se l'offerta è già disponibile al pubblico in marketplace e in tempo reale, tutti gli aggiornamenti apportati non vengono pubblicati fino a quando non si seleziona **Go-live** nel [ **Panoramica offrono** ](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) cruscotto.

### <a name="publish-offer-to-the-public"></a>Pubblicare l'offerta al pubblico

Accedere al centro per i Partner e accedere all'offerta. Si verrà reindirizzati per il **panoramica dell'offerta** pagina. Nella parte superiore della pagina, verrà visualizzata un'opzione per **lanciate**. Selezionare **attivare l'offerta,** e dopo la conferma, l'offerta verrà avviata la pubblicazione per il pubblico. Si riceverà una notifica di posta elettronica quando l'offerta è attiva.

## <a name="publish"></a>Pubblica

Ora che si è scelto di **lanciate** nell'offerta, rendendolo disponibile nel marketplace, esistono una serie di controlli di convalida finale che verranno esaminati per verificare che l'offerta in tempo reale viene configurato esattamente come l'anteprima versione dell'offerta.

- **Impostazione del flusso di acquisto dell'offerta (> 10 min)**

In questo passaggio, si assicura che l'offerta può essere soddisfatta dai clienti tramite il portale di Azure con l'acquisto. Questo passaggio riguarda solo per le offerte da venduti in Microsoft.

- **Testare la convalida dei dati di unità (5 min)**

In questo passaggio è convalidare i dati fornita nella sezione di configurazione tecnici dell'offerta, il test drive. Funzionalità di test unità viene testata e approvata. Questo passaggio è applicabile per le offerte solo con una test drive abilitato.

- **Testare il provisioning di unità (circa 30 min)**

In questo passaggio si distribuire e replicano le istanze di un test dell'unità in modo che siano pronti per l'uso dei clienti.  Questo passaggio è applicabile per le offerte solo con una test drive abilitato.

- **Portare la convalida di gestione e la registrazione (> 15 min)**

In questo passaggio è verificare che il sistema di gestione lead può ricevere clienti potenziali clienti in base ai dettagli forniti nel programma di installazione offerta. Questo passaggio è applicabile per le offerte solo con la gestione dei Lead abilitata.

- **Pubblicare finale (> 30 minuti)**

In questo passaggio, si assicura che l'offerta diventa disponibile pubblicamente nel marketplace.

## <a name="update-existing-marketplace-offers"></a>Aggiorna quelli esistenti offerte del marketplace

Se si desidera apportare modifiche a un'offerta che è già stato pubblicato, è necessario aggiornare prima di tutto l'offerta esistente e quindi pubblicarlo nuovamente.

## <a name="next-steps"></a>Passaggi successivi

- [Aggiornare un'offerta esistente nel Marketplace commerciale](./update-existing-offer.md)
