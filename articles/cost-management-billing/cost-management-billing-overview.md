---
title: Panoramica di Gestione dei costi e fatturazione di Azure
description: Le funzionalità di Gestione dei costi e fatturazione di Azure consentono di eseguire attività amministrative di fatturazione e di gestire l'accesso per la fatturazione dei costi. È anche possibile usare le funzionalità per monitorare e controllare la spesa di Azure e ottimizzare l'uso delle risorse di Azure.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/26/2020
ms.topic: overview
ms.service: cost-management-billing
ms.subservice: common
ms.custom: contentperfq2
ms.openlocfilehash: 1dca2fa938c26ba45f162fae962aec0bfaf6cef7
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353035"
---
# <a name="what-is-azure-cost-management--billing"></a>Che cos'è Gestione dei costi e fatturazione di Azure?

Con il cloud Microsoft è possibile migliorare considerevolmente le prestazioni tecniche dei carichi di lavoro aziendali. È anche possibile ridurre i costi e l'overhead necessario per gestire le risorse dell'organizzazione. L'opportunità di business, tuttavia, crea un rischio dovuto all'introduzione di potenziali sprechi e inefficienze nelle distribuzioni cloud. Gestione dei costi e fatturazione di Azure è una suite di strumenti offerti da Microsoft che consentono di analizzare, gestire e ottimizzare i costi dei carichi di lavoro. Usando la suite l'organizzazione ha la certezza di sfruttare i vantaggi offerti dal cloud.

I carichi di lavoro di Azure possono essere paragonati alle luci di una casa. Quando si esce per l'intera giornata, si lasciano le luci accese? È possibile usare lampadine diverse e più efficienti che consentano di ridurre la bolletta mensile dell'energia? In una sola stanza sono presenti più luci del necessario? È possibile usare Gestione dei costi e fatturazione di Azure per applicare un ragionamento simile ai carichi di lavoro usati nell'organizzazione.

Con i prodotti e i servizi di Azure, si paga solo per le risorse che si usano. Quando si creano e si usano risorse di Azure, vengono addebitati solo i relativi costi. Grazie alla facilità di distribuzione delle nuove risorse, i costi dei carichi di lavoro possono scendere in modo significativo senza un'analisi e un monitoraggio veri e propri. Le funzionalità di Gestione dei costi e fatturazione di Azure consentono di:

- Eseguire attività amministrative di fatturazione, ad esempio il pagamento di una fattura
- Gestire l'accesso alla fatturazione dei costi
- Scaricare i dati relativi a costi e utilizzo usati per generare la fattura mensile
- Applicare in modo proattivo l'analisi dei dati ai costi
- Impostare le soglie di spesa
- Identificare le opportunità di modifica dei carichi di lavoro per ottimizzare la spesa

Per altre informazioni su come approcciare la gestione dei costi come organizzazione, consultare l'articolo [Procedure consigliate di Gestione costi di Azure](./costs/cost-mgt-best-practices.md).

## <a name="understand-azure-billing"></a>Informazioni sulla fatturazione di Azure

Le funzionalità di fatturazione di Azure vengono usate per verificare i costi fatturati e per gestire l'accesso alle informazioni di fatturazione. Nelle organizzazioni più grandi, le attività di fatturazione vengono in genere gestite dai reparti acquisti e contabilità.

L'account di fatturazione viene creato quando ci si iscrive per usare Azure. Viene usato per gestire le fatture e i pagamenti, oltre che per tenere traccia dei costi. È possibile avere accesso a più account di fatturazione. Ad esempio, l'iscrizione ad Azure potrebbe essere stata eseguita per i progetti personali. Quindi, si potrebbe avere una singola sottoscrizione di Azure con un account di fatturazione. Si potrebbe anche avere accesso tramite il contratto Enterprise Agreement o il Contratto del cliente Microsoft dell'organizzazione. Per ogni scenario sarà disponibile un account di fatturazione distinto.

### <a name="billing-accounts"></a>Account di fatturazione

Il portale di Azure attualmente supporta i tipi di account di fatturazione seguenti:

- **Programma dei Microsoft Online Services**: viene creato un account di fatturazione individuale per un programma di Microsoft Online Services quando ci si iscrive ad Azure tramite il sito Web di Azure, ad esempio, quando ci si iscrive per ricevere un account Azure gratuito, un account con pagamento in base al consumo o un account di sottoscrittore di Visual Studio.

- **Contratto Enterprise**: quando l'organizzazione firma un contratto Enterprise Agreement (EA) per l'uso di Azure, viene creato un apposito account di fatturazione.

- **Contratto del cliente Microsoft**: quando l'organizzazione collabora con un rappresentante Microsoft per firmare un Contratto del cliente Microsoft, viene creato un apposito account di fatturazione. Anche alcuni clienti in specifiche aree che si iscrivono tramite il sito Web di Azure per ricevere un account con pagamento in base al consumo oppure aggiornano il loro account Azure gratuito possono avere un account di fatturazione per un Contratto del cliente Microsoft.

### <a name="scopes-for-billing-accounts"></a>Ambiti degli account di fatturazione
Un ambito è un nodo di un account di fatturazione che si usa per visualizzare e gestire la fatturazione. In questa area vengono gestiti i dati di fatturazione, i pagamenti e le fatture e vengono eseguite le attività generali di gestione degli account.

#### <a name="microsoft-online-services-program"></a>Programma dei Microsoft Online Services

|Scope  |Definizione  |
|---------|---------|
|Account di fatturazione     | Rappresenta un singolo proprietario (amministratore account) per una o più sottoscrizioni di Azure. Un amministratore account è autorizzato a eseguire varie attività di fatturazione, ad esempio creare sottoscrizioni, visualizzare fatture o cambiare la fatturazione per le sottoscrizioni.  |
|Subscription     |  Rappresenta un raggruppamento di risorse di Azure. Le fatture vengono generate nell'ambito della sottoscrizione. Prevede metodi specifici di pagamento della fattura corrispondente.|

#### <a name="enterprise-agreement"></a>Enterprise Agreement

|Scope  |Definizione  |
|---------|---------|
|Account di fatturazione    | Rappresenta una registrazione del contratto Enterprise Agreement. Le fatture vengono generate nell'ambito dell'account di fatturazione. È strutturato tramite reparti e account di registrazione.  |
|department     |  Raggruppamento facoltativo di account di registrazione.      |
|Account di registrazione     |  Rappresenta un singolo proprietario dell'account. Le sottoscrizioni di Azure vengono create nell'ambito dell'account di registrazione.  |

#### <a name="microsoft-customer-agreement"></a>Contratto del cliente Microsoft

|Scope  |Attività  |
|---------|---------|
|Account di fatturazione     |   Rappresenta un contratto del cliente per più prodotti e servizi Microsoft. L'account di fatturazione è strutturato tramite profili di fatturazione e sezioni della fattura.   |
|Profilo di fatturazione     |  Rappresenta una fattura e i relativi metodi di pagamento. La fattura viene generata in questo ambito. Il profilo di fatturazione può includere più sezioni della fattura.      |
|Sezione della fattura     |   Rappresenta un gruppo di costi in una fattura. Le sottoscrizioni e altri acquisti sono associati all'ambito delle sezioni di fattura.    |

## <a name="understand-azure-cost-management"></a>Informazioni su Gestione costi di Azure

Anche se correlate, le operazioni di fatturazione e di gestione dei costi sono differenti. La fatturazione è il processo di invio di fatture ai clienti per di merci o servizi e di gestione delle relazioni commerciali.

Gestione costi mostra i costi aziendali e i criteri di utilizzo tramite analisi avanzate. I report in Gestione costi mostrano i costi basati sull'utilizzo da parte dei servizi di Azure e delle offerte di Marketplace di terze parti. I costi sono basati sui prezzi negoziati e considerati negli sconti per la prenotazione e per il Vantaggio Azure Hybrid. Collettivamente, i report mostrano i costi interni ed esterni sull'utilizzo e gli addebiti per Azure Marketplace. Altri addebiti, ad esempio gli acquisti di prenotazioni, il supporto e le imposte, non sono ancora disponibili nei report. I report consentono di comprendere l'uso di risorse e spese e possono suggerire come trovare le anomalie di spesa. È inoltre disponibile analisi predittiva. Gestione costi usa i gruppi di gestione di Azure, i budget e gli elementi consigliati per mostrare chiaramente come sono organizzate le spese e come si potrebbero ridurre i costi.

È possibile usare il portale di Azure o varie API per l'esportazione di automazione per integrare i dati sui costi con i processi e sistemi esterni. Sono inoltre disponibili l'esportazione automatizzata dei dati di fatturazione e report pianificati.

Guardare il video Azure Cost Management overview (Panoramica di Gestione costi di Azure) per una rapida panoramica di come Gestione costi di Azure consente di risparmiare denaro in Azure. Per guardare altri video, visitare il [canale YouTube di Gestione costi](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/el4yN5cHsJ0]

### <a name="plan-and-control-expenses"></a>Pianificare e controllare le spese

Le modalità che Gestione costi usa per pianificare e controllare i costi includono: analisi dei costi, budget, raccomandazioni ed esportazione dei dati di gestione dei costi.

L'analisi dei costi consente di esplorare e analizzare i costi aziendali. È possibile visualizzare i costi aggregati per organizzazione per comprendere dove i costi sono maggiori e identificare le tendenze di spesa. È possibile visualizzare i costi accumulati nel corso del tempo per stimare i trend mensili, trimestrali o addirittura annuali rispetto a un budget.

I budget consentono di pianificare e soddisfare i controlli amministrativi aziendali. Consentono di impedire il superamento delle soglie per costo o i limiti di costo. I budget consentono inoltre di segnalare ad altri utenti le spese per gestire i costi in modo proattivo. Inoltre, aiutano a visualizzare l'avanzamento della spesa nel corso del tempo.

Gli elementi consigliati mostrano come ottimizzare e migliorare l'efficienza identificando le risorse inattive e sottoutilizzate. In alternativa, mostrano le opzioni delle risorse meno costose. Quando si agisce sugli elementi consigliati, si modifica la modalità di uso delle risorse per risparmiare denaro. Per prendere un'iniziativa, visualizzare innanzitutto gli elementi consigliati per l'ottimizzazione dei costi e identificare potenziali inefficienze di utilizzo. Successivamente, agire su un elemento consigliato per modificare l'utilizzo delle risorse di Azure per un'opzione più conveniente. Verificare quindi l'azione per assicurarsi che la modifica apportata abbia avuto esito positivo.

Se si utilizzano sistemi esterni per accedere o per revisionare i dati di gestione dei costi, è possibile esportare facilmente i dati da Azure. È possibile impostare un'esportazione giornaliera pianificata in formato CSV e archiviare i file di dati in archiviazione di Azure. È quindi possibile accedere ai dati dal sistema esterno.

### <a name="cloudyn-deprecation"></a>Deprecazione di Cloudyn

Cloudyn è un servizio di Azure correlato a Gestione costi che verrà deprecato entro la fine del 2020. Le funzionalità di Cloudyn esistenti vengono integrate direttamente nel portale di Azure laddove possibile. Al momento non verrà eseguito l'onboarding di nuovi clienti, ma il supporto per il prodotto rimarrà disponibile fino a quando non sarà completamente deprecato.
 
### <a name="additional-azure-tools"></a>Strumenti aggiuntivi di Azure

Azure offre altri strumenti che non fanno parte del set di funzionalità di Gestione dei costi e fatturazione di Azure. Tuttavia, svolgono un ruolo importante nel processo di gestione costi. Per altre informazioni riguardo questi strumenti, consultare i collegamenti seguenti.

- [Calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/) - Usare questo strumento per stimare i costi iniziali del cloud.
- [Azure Migrate](../migrate/migrate-services-overview.md) - Valutare il carico di lavoro corrente del proprio centro dati logico per informazioni dettagliate su ciò che serve da una soluzione di sostituzione di Azure.
- [Azure Advisor](../advisor/advisor-overview.md) - Identificare le macchine virtuali inutilizzate e ricevere consigli sugli acquisti di istanza riservata di Azure.
- [Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/) - Utilizzare le licenze correnti di Windows Server o SQL Server per permettere alle macchine virtuali in Azure di effettuare un salvataggio.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è acquisito familiarità con Gestione dei costi e fatturazione, il passaggio successivo consiste nell'iniziare a usare il servizio.

- Iniziare a usare Gestione costi di Azure per eseguire l'[analisi dei costi](./costs/quick-acm-cost-analysis.md).
- Per altre informazioni, consultare [Procedure consigliate di Gestione costi di Azure](./costs/cost-mgt-best-practices.md).