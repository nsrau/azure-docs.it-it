---
title: Creazione di un'applicazione di Rendering remoto di Azure pronta per la commercializzazione
description: Strategie e considerazioni per la creazione di un'applicazione pronta per la commercializzazione con Rendering remoto di Azure
author: m-the-hoff
ms.author: v-michof
ms.date: 06/15/2020
ms.topic: tutorial
ms.openlocfilehash: fa1a49aeef8b86230dc1d5ea898832cfb1cee852
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/30/2020
ms.locfileid: "85566939"
---
# <a name="tutorial-creating-a-commercial-ready-azure-remote-rendering-application"></a>Esercitazione: Creazione di un'applicazione di Rendering remoto di Azure pronta per la commercializzazione

Questa esercitazione descrive quanto segue:

> [!div class="checklist"]
>
> * Gestione delle sessioni per le applicazioni commerciali
> * Monitoraggio delle sessioni per la fatturazione
> * Ottimizzazione dell'esperienza utente relativa al tempo di caricamento delle sessioni
> * Considerazioni sulla latenza di rete

## <a name="prerequisites"></a>Prerequisiti

* Questa esercitazione si basa su [Esercitazione: Protezione di Rendering remoto di Azure e dello spazio di archiviazione dei modelli](../security/security.md).

## <a name="introduction-to-commercial-readiness"></a>Introduzione alla preparazione delle applicazioni per la commercializzazione

Rendering remoto di Azure estende i limiti del possibile con la realtà mista. Una volta integrate le funzionalità di base nella soluzione, occorre prenderne in considerazione alcune altre per garantire che la soluzione sia sicura, scalabile e pronta a fornire un valore aggiunto.

Questo modulo presenta alcune funzionalità aggiuntive da considerare per la propria applicazione commerciale.

Per un'ampia panoramica delle procedure consigliate per l'architettura a livello di sistema, vedere:

* [Centro architetture di Azure](https://docs.microsoft.com/azure/architecture/)
* [Guida introduttiva per gli sviluppatori di Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide)

## <a name="analytics"></a>Analytics

L'integrazione di strumenti di analisi facilita la gestione, il monitoraggio e il miglioramento della soluzione.

Per un elenco completo delle risorse di analisi disponibili, vedere:

* [Servizi di analisi di Azure](https://azure.microsoft.com/product-categories/analytics/)

### <a name="tracking-usage-for-billing"></a>Monitoraggio dell'utilizzo per la fatturazione

Tenere traccia dell'utilizzo di Rendering remoto di Azure da parte di più team interni o clienti esterni è molto importante, soprattutto in situazioni multi-tenant.

Per ottenere questo risultato, Azure offre un servizio di assegnazione di tag alle risorse, che associa l'utilizzo del servizio Rendering remoto di Azure a ogni cliente.

Per informazioni introduttive sulla denominazione delle risorse e l'assegnazione di tag, vedere:

* [Guida alle decisioni per la denominazione delle risorse e l'assegnazione di tag](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

### <a name="diagnostics"></a>Diagnostica

Strumenti efficaci come Event Trace for Windows (ETW) e la registrazione traccia eventi semplificano la generazione di eventi di traccia nell'applicazione e la diagnostica di problemi di rete, inserimento di contenuti, sessioni, applicazioni e di problemi di altro tipo che possono verificarsi durante la distribuzione di una soluzione commerciale.

Per altre informazioni, vedere:

* [Creare tracce di prestazioni lato client](https://docs.microsoft.com/azure/remote-rendering/how-tos/performance-tracing)
* [Come raccogliere dati di Event Trace for Windows (ETW)](https://docs.microsoft.com/visualstudio/profiling/how-to-collect-event-tracing-for-windows-etw-data)
* [Uso del Portale di dispositivi di Windows - Registrazione](https://docs.microsoft.com/windows/mixed-reality/using-the-windows-device-portal)

### <a name="usage-analysis"></a>Analisi dell'utilizzo

È possibile usare Azure Application Insights per esaminare il modo in cui gli utenti usano l'applicazione di Rendering remoto di Azure. Ogni volta che si aggiorna l'app, è possibile valutare la sua capacità di soddisfare le esigenze degli utenti e migliorare la soluzione di conseguenza. Con queste informazioni è possibile prendere decisioni basate sui dati sui cicli di sviluppo successivi.

Per altre informazioni, vedere:

* [Utilizzo dell'analisi con Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview)

## <a name="fast-startup-time-strategies"></a>Strategie per tempi di avvio rapidi

Alcuni casi d'uso possono richiedere un avvio rapido dall'avvio dell'applicazione alla visualizzazione del modello 3D, ad esempio durante una riunione importante in cui è fondamentale che tutto funzioni tempestivamente. Un altro esempio è la revisione di un modello CAD 3D, in cui l'iterazione di progettazione rapida tra un'applicazione CAD e la realtà mista è fondamentale ai fini dell'efficienza.

Rendering remoto di Azure richiede modelli 3D pre-elaborati e attualmente Azure impiega alcuni minuti per creare una macchina virtuale e caricare un modello per il rendering. Per semplificare e velocizzare il più possibile questo processo, è necessario preparare in anticipo i dati del modello 3D e la sessione di Rendering remoto di Azure.

I suggerimenti condivisi in questo articolo non fanno attualmente parte del servizio Rendering remoto di Azure standard, ma è possibile implementarli autonomamente per accelerare i tempi di avvio.

### <a name="initiate-early"></a>Anticipazione delle fasi iniziali

La soluzione più semplice per ridurre i tempi di avvio è spostare la creazione e l'inizializzazione della macchina virtuale il più possibile all'inizio del flusso di lavoro dell'utente. Una strategia consiste nell'inizializzare la sessione di Rendering remoto di Azure non appena si stabilisce che sarà necessaria. Questo avviene spesso quando l'utente inizia a caricare in Archiviazione BLOB di Azure un modello 3D da usare con Rendering remoto di Azure. In questo caso, la creazione della sessione e l'inizializzazione della VM possono essere avviate contemporaneamente al caricamento del modello 3D in modo che entrambi i flussi di lavoro vengano eseguiti in parallelo.

Questo processo può essere ulteriormente semplificato scegliendo i contenitori di input e output di Archiviazione BLOB di Azure nello stesso data center locale della sessione di Rendering remoto di Azure.

### <a name="scheduling"></a>Pianificazione

Se si prevede di aver bisogno in futuro di Rendering remoto di Azure, è possibile pianificare una data e un'ora specifiche in cui avviare la sessione di Rendering remoto di Azure.

Questa opzione potrebbe essere offerta tramite un portale Web in cui gli utenti possono caricare un modello 3D e pianificare una data e ora per visualizzarlo in futuro. Il portale sarebbe anche una posizione ideale in cui richiedere altre preferenze, ad esempio il rendering Standard o Premium. Il rendering Premium può essere adatto se si vuole mostrare una combinazione di asset la cui dimensione ideale è più difficile da determinare automaticamente o se è necessario assicurarsi che l'area di Azure abbia VM disponibili in quel momento specifico.

### <a name="session-pooling"></a>Pool di sessioni

Un'altra opzione adatta alle situazioni più complesse è il pool di sessioni, in cui una o più sessioni vengono create e inizializzate continuamente. Si crea così un pool di sessioni che può essere usato immediatamente dall'utente che ne fa richiesta. Lo svantaggio di questo approccio è che appena viene inizializzata la VM viene avviata anche la fatturazione per il servizio. Potrebbe non essere conveniente tenere sempre in esecuzione un pool di sessioni, ma in base alle analisi può essere possibile stimare i carichi di picco oppure si può usare in combinazione la strategia di pianificazione illustrata sopra per stimare quando saranno necessarie le sessioni e aumentare o ridurre il pool di sessioni di conseguenza.

Questa strategia consente inoltre di ottimizzare la scelta tra le sessioni Standard e Premium in modo più dinamico, in quanto renderebbe molto più veloce il passaggio tra i due tipi all'interno di una singola sessione utente, come nel caso in cui un modello di complessità Premium viene visualizzato per primo, seguito da un modello che può funzionare in una sessione Standard. Se queste sessioni utente sono molto lunghe, il risparmio sui costi può essere notevole.

Per altre informazioni sulle sessioni di Rendering remoto di Azure, vedere:

* [Sessioni di Rendering remoto](https://docs.microsoft.com/azure/remote-rendering/concepts/sessions)

## <a name="standard-vs-premium-vm-routing-strategies"></a>Strategie di routing delle VM Standard e Premium

La necessità di scegliere se creare una VM Standard o Premium è una vera sfida per la progettazione dell'esperienza utente e del sistema end-to-end. Benché sia possibile usare solo sessioni Premium, le sessioni Standard usano molte meno risorse di calcolo di Azure e sono meno costose rispetto alle sessioni Premium. Ciò costituisce una forte motivazione a usare le sessioni Standard dove possibile e a usare le sessioni Premium solo quando necessario.

Di seguito sono descritte alcune opzioni, dalla meno completa alla più completa, per gestire la scelta delle sessioni.

### <a name="use-only-standard-or-premium"></a>Usare solo le sessioni Standard o Premium

Se si è certi che le proprie esigenze saranno *sempre* sotto la soglia tra Standard e Premium, questo semplifica notevolmente la decisione. Usare solo le sessioni Standard. Tenere presente, però, che l'impatto sull'esperienza utente è notevole se la somma totale della complessità degli asset caricati viene rifiutata perché eccessiva per una sessione Standard.

Analogamente, se si prevede che una buona parte degli utilizzi superi la soglia tra Standard e Premium o se il costo non è un fattore chiave per il caso d'uso, scegliere sempre le sessioni Premium può essere una buona opzione ai fini della semplicità.

### <a name="ask-the-user"></a>Chiedere all'utente

Se si vogliono supportare sia le sessioni Standard che Premium, il modo più semplice per determinare il tipo di sessione della VM di cui creare un'istanza consiste nel chiederlo all'utente quando seleziona gli asset 3D da visualizzare. Il problema con questo approccio è che richiede che l'utente comprenda la complessità dell'asset 3D o dei molteplici asset che verranno visualizzati. In genere questa opzione non è consigliata proprio per questo motivo. Se l'utente sceglie l'opzione Standard e la scelta è sbagliata,l'esperienza utente potrebbe risultare compromessa in un momento inopportuno.

### <a name="analyze-the-3d-model"></a>Analizzare il modello 3D

Un altro approccio relativamente semplice consiste nell'analizzare la complessità degli asset 3D selezionati. Se la complessità del modello è sotto la soglia per l'opzione Standard, avviare una sessione Standard, altrimenti avviare una sessione Premium. Qui la difficoltà sta nel fatto che una singola sessione potrebbe essere usata per visualizzare più modelli, alcuni dei quali potrebbero superare la soglia di complessità di una sessione Standard e, di conseguenza, non sarebbe possibile usare ininterrottamente la stessa sessione per una sequenza di asset 3D diversi.

### <a name="automatic-switching"></a>Cambio di sessione automatico

Il passaggio automatico tra le sessioni Standard e Premium può essere molto appropriato in una progettazione di sistema che include anche il pool di sessioni. Questa strategia consente un'ulteriore ottimizzazione dell'utilizzo delle risorse. La complessità viene determinata quando l'utente carica i modelli per la visualizzazione e le dimensioni corrette della sessione vengono richieste dal servizio di pool di sessioni.

## <a name="working-with-networks"></a>Utilizzo delle reti

### <a name="diagnostics"></a>Diagnostica

Per Rendering remoto di Azure è necessaria una connessione Internet veloce con bassa latenza. La qualità della rete dell'utente può avere un impatto notevole sulla qualità dell'esperienza. Dato che è probabile che i clienti abbiano configurazioni di rete diverse e solo occasionalmente una latenza di rete insufficiente, gli strumenti di diagnostica sono fondamentali.  

Per garantire un'esperienza utente uniforme e di qualità elevata, è consigliabile integrare strumenti di analisi lato server e lato client nelle applicazioni di Rendering remoto di Azure. Questi strumenti raccolgono le informazioni necessarie per diagnosticare e attenuare i problemi di rete che i clienti potrebbero riscontrare.

### <a name="client-network-configurations"></a>Configurazioni di rete dei clienti

Una delle maggiori sfide nello sviluppo di soluzioni di collaborazione efficaci distribuite in un'ampia varietà di ambienti aziendali è l'essere preparati per le diverse configurazioni della topologia di rete e del firewall aziendale dei clienti.

Molte aziende bloccano tutto il traffico peer-to-peer all'interno di una LAN. In questo modo è difficile sfruttare la semplicità e l'esperienza utente semplificata di individuazione LAN automatica per stabilire una sessione condivisa locale fra tutte le istanze individuate dell'applicazione di realtà mista.

Altri potenziali punti di errore sono i router configurati per limitare intenzionalmente la larghezza di banda e i firewall che bloccano la maggior parte delle porte TCP/IP.

Se si prevede di usare Rendering remoto di Azure in una rete di cui non si conosce la configurazione, è consigliabile:

* Fornire un elenco di controllo pre-riunione per valutare la conformità della rete.
* Verificare che il data center locale appropriato sia in grado di servire la richiesta.
* Dedicare tempo in abbondanza alla diagnostica di eventuali problemi.
* Implementare un hotspot mobile con un piano dati a larghezza di banda elevata come backup.

### <a name="end-to-end-bandwidth"></a>Larghezza di banda end-to-end

È importante valutare le funzionalità della larghezza di banda per ogni via della rete che può esistere tra la macchina virtuale di Rendering remoto di Azure e il cliente finale. Tenere presente che il segmento di rete dal data center di Azure all'ISP del cliente può essere un fattore più limitante rispetto al segmento dall'ISP al cliente. Per facilitare la diagnosi di questi problemi è possibile usare il test della velocità di download dei BLOB.

### <a name="bandwidth-competition"></a>Contesa della larghezza di banda

Quando si progetta un'applicazione di realtà mista, tenere presente che diverse funzionalità dell'app possono contendersi la larghezza di banda con Rendering remoto di Azure. L'esempio più probabilmente imprevisto è quello in cui molti partecipanti in una singola stanza si aspettano di usare simultaneamente Rendering remoto di Azure per visualizzare un asset 3D. Ogni via del flusso di dati di rete dovrà avere sufficiente capacità per trasportare la somma totale di tutti i flussi di dati di Rendering remoto di Azure combinati.

Altri esempi includono i flussi video, i caricamenti in background simultanei di altri contenuti correlati e la chat vocale, in particolare quando ci sono molti partecipanti e il sistema usa un approccio peer-to-peer distribuito anziché un server di missaggio audio.

Per altre informazioni sull'analisi della rete, vedere:

* [Test della velocità di download dei BLOB del servizio di archiviazione di Azure](https://www.azurespeed.com/Azure/Download)
* [Statistiche sulla latenza di andata e ritorno della rete di Azure](https://docs.microsoft.com/azure/networking/azure-network-latency)
* [Analisi delle prestazioni lato server](https://docs.microsoft.com/azure/remote-rendering/overview/features/performance-queries)
* [Analisi delle prestazioni lato client](https://docs.microsoft.com/azure/remote-rendering/how-tos/performance-tracing)

## <a name="collaboration-considerations"></a>Considerazioni sulla collaborazione

Alcuni degli usi più importanti di Rendering remoto di Azure implicano la collaborazione tra più partecipanti che visualizzano la stessa esperienza 3D nello stesso momento. In queste sessioni condivise è importante riconoscere che ogni partecipante dovrà avere una sessione di Rendering remoto di Azure univoca, indipendentemente dal fatto che i partecipanti si trovino nella stessa posizione della stessa rete o meno.

Ogni partecipante, infatti, sta effettivamente osservando la stessa esperienza da punti di osservazione diversi, per cui è necessario eseguire il rendering degli stessi asset 3D da ognuna di queste prospettive simultaneamente.

### <a name="multiple-azure-remote-rendering-sessions"></a>Più sessioni di Rendering remoto di Azure

Se si ha intenzione di supportare esperienze condivise con Rendering remoto di Azure, i sistemi implementati per creare e gestire le sessioni di Rendering remoto di Azure devono essere in grado di avviare più sessioni. Se i partecipanti sono dislocati in diverse aree geografiche, queste sessioni devono essere inizializzate in data center di Azure diversi.

Il sistema deve anche gestire la possibilità che uno o più partecipanti si trovino in un'area geografica che attualmente non è supportata da Rendering remoto di Azure o in cui non sono attualmente disponibili istanze di VM di Rendering remoto di Azure.

Questa gestione di più sessioni simultanee può essere ulteriormente semplificata aggiungendo il pool di sessioni e altre strategie illustrate in questo documento.

### <a name="azure-blob-storage-considerations"></a>Considerazioni su Archiviazione BLOB di Azure

Tutte le sessioni di Rendering remoto di Azure simultanee possono fare riferimento allo stesso URI di firma di accesso condiviso per il modello convertito da visualizzare. In questo modo è possibile caricare e convertire gli asset 3D desiderati una volta sola e quindi condividerli fra tutte le sessioni. Questo vale soprattutto quando i partecipanti si trovano nella stessa area geografica e usano lo stesso data center in cui non ci sono problemi di prestazioni correlati all'archiviazione BLOB di Azure che si trova in un data center diverso da quello usato dal server di rendering remoto di Azure e dall'utente.

Anche nel caso in cui gli asset 3D vengono in genere caricati per una singola sessione di visualizzazione e quindi eliminati, ad esempio in una sessione di revisione della progettazione, l'area geografica dell'archiviazione BLOB di Azure relativa al server di Rendering remoto di Azure è meno critica.

Per gli asset 3D che verranno usati ripetutamente, ad esempio in un caso d'uso di formazione, è invece consigliabile mantenere gli asset 3D pronti nell'archiviazione BLOB in ogni data center locale in cui si prevede di usare Rendering remoto di Azure. Questa operazione può essere automatizzata tramite la ridondanza di Archiviazione di Azure. A questo scopo viene spesso usata anche la rete CDN, ma questa opzione non è ancora disponibile per Rendering remoto di Azure.

Per altre informazioni:

* [Esperienze condivise nella realtà mista](https://docs.microsoft.com/windows/mixed-reality/shared-experiences-in-mixed-reality)
* [Ridondanza di Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

## <a name="managing-model-access"></a>Gestione dell'accesso ai modelli

Per sfruttare appieno Rendering remoto di Azure, è necessario considerare attentamente l'infrastruttura end-to-end per la gestione dei modelli 3D.

Un vantaggio dell'uso di Rendering remoto di Azure è che gli asset 3D di grandi dimensioni non devono mai essere trasmessi direttamente al dispositivo di realtà mista prima di essere visualizzati.  Inoltre, una volta che un asset 3D è stato caricato e convertito per l'uso con Rendering remoto di Azure, quella singola istanza del modello 3D può essere condivisa da un numero illimitato di utenti.

### <a name="considerations-for-3d-model-access"></a>Considerazioni sull'accesso ai modelli 3D

Di seguito sono illustrati alcuni degli aspetti principali da considerare per decidere la strategia di accesso ai modelli da adottare.

In base al caso d'uso previsto, determinare la posizione o la combinazione di posizioni migliore per consentire a un utente di selezionare gli asset 3D da visualizzare. Alcune opzioni comuni sono:

* Direttamente all'interno dell'esperienza di realtà mista
* Tramite un portale Web complementare
* In un'applicazione desktop o per dispositivi mobili complementare

Se il caso d'uso ha modelli di utilizzo in base ai quali lo stesso asset 3D può essere caricato più volte, il back-end tiene traccia dei modelli che sono già stati convertiti per l'uso con Rendering remoto di Azure in modo che un modello venga pre-elaborato una sola volta per più selezioni future. Un esempio di revisione della progettazione può essere quello in cui un team ha accesso a un asset 3D originale comune. Ogni membro del team deve rivedere il modello usando Rendering remoto di Azure in un determinato punto del flusso di lavoro. Solo la prima visualizzazione attiva quindi la fase di pre-elaborazione. Le visualizzazioni successive analizzeranno il file post-elaborato associato nel contenitore di output SAS.

A seconda del caso d'uso, può essere utile determinare e potenzialmente rendere permanenti le dimensioni corrette della VM di Rendering remoto di Azure, Standard o Premium, per ogni asset o gruppo di asset 3D che verranno visualizzati insieme nella stessa sessione.  

### <a name="on-device-model-selection-list"></a>Elenco di selezione del modello su dispositivo

In molti casi d'uso, ad esempio un processo di training, una serie di istruzioni per le attività o un'applicazione di marketing, il set di asset 3D da visualizzare comunemente in Rendering remoto di Azure può essere abbastanza statico. In queste situazioni, un set curato di asset 3D può essere pre-convertito e reso disponibile tramite un database contenente le informazioni necessarie per popolare un elenco di selezione di asset curati. Questi dati possono quindi essere recuperati dall'applicazione di realtà mista per popolare un menu di selezione.

È possibile offrire in aggiunta anche un modo per caricare asset 3D privati, univoci per ogni singolo individuo o gruppo. L'elenco di asset privati può quindi essere combinato con l'elenco di asset curati comuni nell'esperienza utente per la selezione degli asset 3D da visualizzare.

### <a name="on-device-onedrive-access"></a>Accesso a OneDrive su dispositivo

Dato che un selettore di file di OneDrive è integrato in modo nativo nei dispositivi di realtà mista Microsoft, la selezione di asset 3D su dispositivo da OneDrive è accattivante, in particolare per i casi d'uso in cui è normale caricare modelli 3D diversi o modificati. In questo scenario l'utente seleziona uno o più asset 3D tramite il selettore file di OneDrive all'interno dell'applicazione di realtà mista. Viene quindi eseguita la migrazione degli asset 3D in un contenitore di input SAS, convertito in un contenitore di output SAS e collegato alla sessione di Rendering remoto di Azure. Idealmente, l'applicazione di realtà mista richiama un processo basato sul cloud per eseguire questi passaggi invece di trasferire tutti i bit da OneDrive al dispositivo e di nuovo ad Archiviazione BLOB di Azure.

Questo approccio può essere arricchito rendendo permanente un'associazione tra gli asset 3D visualizzati in precedenza in modo che, quando lo stesso modello viene scelto di nuovo da OneDrive, l'applicazione possa ignorare il processo di conversione e caricare direttamente l'asset 3D convertito associato tramite il relativo URI di firma di accesso condiviso.

Per altre informazioni:

* [Modello di Microsoft Power Automate per la replica da OneDrive ad Archiviazione di Azure](https://flow.microsoft.com/galleries/public/templates/2f90b5d3-029b-4e2e-ad37-1c0fe6d187fe/when-a-file-is-uploaded-to-onedrive-copy-it-to-azure-storage-container/)
* [Panoramica dell'API di archiviazione file di OneDrive](https://docs.microsoft.com/graph/onedrive-concept-overview)

### <a name="direct-cad-access"></a>Accesso diretto al sistema CAD

Un caso d'uso interessante per la realtà mista è la revisione della progettazione in corso del sistema CAD. In questo scenario, il tempo di caricamento più rapido dal desktop al dispositivo di realtà mista è fondamentale. Una soluzione ideale potrebbe implicare lo sviluppo di plug-in per applicazioni CAD specifiche che gestiscano direttamente ogni aspetto dei processi di caricamento, conversione e visualizzazione:

* Fornire un'esperienza utente per:
  * Associare l'applicazione CAD a uno specifico dispositivo di realtà mista (una volta sola)
  * Richiedere che la geometria selezionata venga visualizzata sul dispositivo di realtà mista
* Se non è già in esecuzione, avviare la sessione di Rendering remoto di Azure in modo che possa essere elaborata in parallelo con il caricamento e la conversione del file CAD
* Normalizzare i dati della geometria CAD in uno dei formati supportati da Rendering remoto di Azure
* Trasmettere i dati normalizzati direttamente al contenitore di input di Archiviazione BLOB di Azure
* Avviare il processo di conversione del modello
* Collegare l'URI di firma di accesso condiviso del contenitore di output del modello alla sessione di Rendering remoto di Azure
* Notificare all'applicazione di realtà mista abbinata che il modello è disponibile e pronto per la visualizzazione e fornire l'URI di firma di accesso condiviso del contenitore di output in modo che l'applicazione possa collegarlo alla sessione

Un approccio molto più semplice, anche se leggermente meno diretto, potrebbe automatizzare il processo di salvataggio del modello 3D in un disco rigido locale e quindi avviare un processo per trasmettere il file salvato al contenitore di input SAS.

### <a name="azure-marketplace"></a>Azure Marketplace

Molti clienti aziendali richiedono che Azure Stack venga distribuito usando gli account Azure e le credenziali dell'azienda per motivi di sicurezza. Per soddisfare questa esigenza, è possibile creare un pacchetto dell'applicazione gestita di Azure in modo che possa essere pubblicata in Azure Marketplace come offerta di applicazione Azure.

Per altre informazioni:

* [Azure Marketplace](https://azure.microsoft.com/marketplace/)
* [Esercitazione: Pubblicare un'applicazione gestita di Azure nel Marketplace](https://docs.microsoft.com/azure/azure-resource-manager/managed-applications/publish-marketplace-app)

### <a name="security"></a>Sicurezza

È fondamentale creare da zero la soluzione di Rendering remoto di Azure end-to-end per la sicurezza. Nella progettazione della soluzione end-to-end occorre considerare molti aspetti della sicurezza, tra cui:

* Strategie di autenticazione
* Gestione degli accessi - gruppi, criteri e autorizzazioni
* Multi-tenancy
* Archiviazione dati e crittografia dei trasferimenti
* Token ad uso temporaneo
* Attacchi Distributed Denial of Service (DDoS)
* Introduzione al rilevamento delle minacce
* VPN e reti sicure
* Firewall
* Gestione di certificati e chiavi private
* Vulnerabilità delle applicazioni ed exploit

Per l'autenticazione, è opportuno spostare quanto più possibile le operazioni di gestione delle sessioni e autenticazione di Rendering remoto di Azure in un servizio Web di Azure. Ne risulterà una soluzione meglio gestita e più sicura.

Per altre informazioni:

* [Autenticazione per il servizio Azure AD](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp#azure-ad-service-authentication)
* [Migliorare il comportamento di sicurezza con Azure](https://azure.microsoft.com/overview/security/)
* [Sicurezza del cloud](https://azure.microsoft.com/product-categories/security/)
