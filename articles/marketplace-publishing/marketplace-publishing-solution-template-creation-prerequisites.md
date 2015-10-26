<properties
   pageTitle="Prerequisiti tecnici per la creazione di un modello di soluzione per il Marketplace | Microsoft Azure"
   description="Informazioni sui requisiti per la creazione di un modello di soluzione da distribuire e vendere in Azure Marketplace"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace-publishing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/09/2015"
   ms.author="hascipio; v-divte" />

# Prerequisiti tecnici per la creazione di un modello di soluzione per Azure Marketplace
Leggere attentamente le informazioni sul processo prima di iniziare e comprendere dove e perché viene eseguito ogni passaggio. Per quanto possibile, è necessario preparare le informazioni sulla società e altri dati, scaricare gli strumenti necessari e/o creare i componenti tecnici prima di iniziare il processo di creazione dell'offerta. Tali elementi dovrebbero risultare chiari dalla lettura di questo articolo.

## Definizione della topologia e valutazione della piattaforma
A questo punto, è probabile che si abbia già familiarità con l'elemento di calcolo di Azure e ci si stia chiedendo cosa compilare esattamente. Immagini di macchine virtuali, estensioni, modelli ARM?

Prima di provare a eseguire il mapping dell'applicazione agli elementi di Azure, sarà utile comprendere le diverse topologie adatte alla soluzione e consigliabili a un cliente intenzionato a distribuire l'applicazione in locale. La maggior parte delle organizzazioni solitamente percorre le quattro fasi seguenti, anche se con qualche differenza: valutazione, modello di verifica, pre-produzione e produzione. In genere, si lavorerà con le organizzazioni durante queste quattro diverse fasi per valutarne i requisiti e consigliare il layout esatto e la scalabilità necessaria per i vari scenari. Anche se ogni cliente è diverso, di solito ci si aspetta di trovare topologie e dimensioni comuni che vengono consigliate sulla base della dimensione della distribuzione del cliente. Quindi, ancora prima di iniziare a sviluppare qualsiasi soluzione, considerare i diversi layout adatti all'applicazione e che potrebbero rispondere a buona parte delle esigenze del cliente senza richiedere personalizzazioni rilevanti. È poi ovvio che ci saranno sempre clienti con esigenze molto specifiche, ma qui verranno presi in considerazione i casi comuni.

Per ogni tipo di distribuzione (valutazione, modello di verifica, pre-produzione e produzione) considerare il layout e la topologia esatti necessari per l'applicazione. Quali sono i diversi livelli? Quali risorse sono necessarie per ogni livello e di quale dimensione? Quali sono le basi necessarie per creare queste risorse? La dimensione di una distribuzione di produzione, come anche di un ambiente di sviluppo/test, può variare in modo significativo a seconda del cliente e delle sue esigenze. Decidere pensando a quale sarebbe il risultato per il carico di lavoro di una topologia piccola, media, grande e molto grande e considerare i requisiti di disponibilità, scalabilità e prestazioni necessari per raggiungere i propri obiettivi.

Una volta presi in considerazione i layout delle diverse topologie e le corrispondenti dimensioni (è possibile che si sia già in possesso della maggior parte di queste conoscenze grazie alla propria esperienza con le distribuzioni locali per i clienti), è opportuno iniziare a valutare cosa offre Azure.

Valutare i requisiti della topologia confrontandoli con ciò che offrono Gestione risorse di Azure e Calcolo in Gestione risorse di Azure. Anche se la maggior parte dei clienti partirà da distribuzioni più piccole, **è consigliabile valutare i requisiti di prestazioni, scalabilità e capacità della topologia di grandi dimensioni in Calcolo in Gestione risorse di Azure, anche se all'inizio non si adotterà una topologia di grandi dimensioni.** L'obiettivo principale è assicurare che i clienti, anche se iniziano con le soluzioni più piccole, abbiano a disposizione una considerevole quantità di spazio per passare senza problemi a distribuzioni su più larga scala. Qualsiasi limitazione relativa a scalabilità, prestazioni o capacità dovrebbe essere comunicata alle controparti Microsoft che potranno così continuare a migliorare le aree necessarie ai partner Marketplace e ai loro clienti.

> [AZURE.TIP]**Consigliato:** valutare e compilare un modello che corrisponda alla massima dimensione di distribuzione prevista per l'applicazione per offrire opportunità di crescita ai clienti finali.

## Blocchi predefiniti di Calcolo di Azure
Iniziare innanzitutto a identificare gli elementi di calcolo disponibili e a valutare l'applicazione tenendo in considerazione questi elementi.

### 1\. Uso delle macchine virtuali esistenti
I blocchi predefiniti di qualsiasi macchina virtuale sono le immagini VM. Le immagini VM sono il profilo di archiviazione completo di una macchina virtuale, contenente un disco VHD del sistema operativo e zero o più dischi dati. È possibile creare un'immagine VM per creare un pacchetto per l'applicazione software con i bit del sistema operativo, oltre ad eventuali dati aggiuntivi necessari. Ad esempio, attualmente in Azure Marketplace, ci sono alcune immagini VM per SQL Server, che includono in un pacchetto il sistema operativo Windows e l'applicazione SQL Server. Anche se questo documento non entra nel dettaglio della creazione e del caricamento di immagini VM in Azure Marketplace, è importante sapere cosa sia un'immagine VM, perché probabilmente sarà il blocco predefinito della soluzione dell'applicazione. Vedere la procedura illustrata in [Creazione di un'immagine VM per Marketplace](marketplace-publishing-vm-image-creation.md).

### 2\. Estensione VM
Può capitare che un'immagine non sia quello che serve, perché non si ha intenzione di essere proprietari del sistema operativo sottostante e di gestirlo come provider dell'applicazione. In Azure Marketplace sono presenti attualmente alcune società che pubblicano i sistemi operativi Windows, Linux e Unix a intervalli regolari. È possibile trovare immagini per Windows Server, Ubuntu, basate su CentOS, CoreOS, Oracle Linux e così via. Invece di pubblicare la propria immagine VM, creando un pacchetto sia per il sistema operativo che per l'applicazione, è possibile scrivere un gestore estensione VM o sfruttarne uno esistente. Un'estensione VM è un meccanismo software che consente di semplificare la configurazione e la gestione VM, sfruttando così l'agente guest (un processo leggero protetto) in una VM di Azure. L'estensione Script personalizzato, ad esempio, come indica il nome include uno script personalizzato e lo esegue nella VM. Questo consente qualsiasi tipo di configurazione consentito anche da uno script generico.

In uno scenario di immagine tradizionale sarebbero stati installati tutti i bit dell'immagine dall'inizio e sarebbero state eseguite tutte le configurazioni necessarie prima o probabilmente anche dopo l'avvio della VM (con un'attività di avvio). Questa immagine potrebbe essere fortemente personalizzata e quindi non essere facilmente riutilizzabile. In alcuni casi, questo è del tutto normale. In altri casi, soprattutto con applicazioni leggere o con configurazioni più complesse che devono essere eseguite dopo il provisioning della VM, può essere utile il modello dell'estensione. I clienti a volte distribuiscono una combinazione di estensioni e immagini VM per avere entrambe le soluzioni preinstallate, ma in questo modo la configurazione successiva all'avvio risulta complessa.

Come è noto, le immagini e le estensioni sono molto utili per configurare una singola VM. Una sola VM, tuttavia, non è sufficiente per offrire ai clienti una disponibilità elevata nel cloud e la scalabilità necessaria per soddisfare le esigenze di un traffico in costante aumento. Perciò, per tutti i carichi di lavoro di produzione, è consigliabile pianificare una soluzione a disponibilità elevata per più VM che usa set di disponibilità e il bilanciamento del carico.

>[AZURE.TIP]**Consigliato:** tutti i carichi di lavoro di produzione nel Marketplace dovrebbero essere distribuiti con più macchine virtuali che gestiscono il traffico in un set di disponibilità (con tre domini di errore) e con il bilanciamento del carico.

Per supportare queste numerose topologie di distribuzione che l'applicazione può supportare e consigliare, Azure Marketplace offre Gestione risorse di Azure (ARM) e i modelli ARM. Gestione risorse di Azure distribuisce e gestisce il ciclo di vita di una raccolta di risorse con un linguaggio dichiarativo basato sul modello. Questo modello è semplicemente un file JSON con parametri che esprime il set di risorse e la relazione da usare per le distribuzioni. Ogni risorsa viene inserita in un gruppo di risorse, che è semplicemente un contenitore per le risorse. Gestione risorse offre agli utenti finali anche un set di funzionalità aggiuntive molto importanti, inclusi il controllo centralizzato delle operazioni, l'aggiunta di tag alle risorse fino alla fattura al cliente, il controllo degli accessi in base alla risorsa per la sicurezza granulare, il supporto per 3 domini di errore di calcolo per la massima disponibilità, e tutte le operazioni sono idempotenti.

> [AZURE.IMPORTANT]**Obbligatorio:** considerati i vantaggi per i clienti finali, tutto il contenuto del Marketplace deve essere distribuito usando i modelli di Gestione risorse di Azure.

Con ARM e i modelli ARM, è possibile esprimere distribuzioni più complesse delle risorse di Azure, ad esempio macchine virtuali, account di archiviazione e reti virtuali, basate su contenuti del Marketplace come le immagini VM e le estensioni VM.

## Sviluppo di blocchi predefiniti
Una volta stabilite le topologie dell'applicazione e quali verranno sviluppate e caricate nel Marketplace durante la prima fase, è possibile identificare e sviluppare i blocchi predefiniti VM. Come indicato in precedenza, sarà necessario decidere se creare un pacchetto per i contenuti della VM come propria immagine VM pubblicata o sfruttare un'immagine VM già esistente in Azure Marketplace, configurandola con un gestore estensione pubblicato o con il proprio gestore. Non esiste un'unica soluzione. Tutto dipende da quanto controllo si vuole avere sul sistema operativo sottostante e dai requisiti di installazione, configurazione e gestione dell'applicazione. Su MSDN e nei blog di Azure sono reperibili molte informazioni sulle immagini e sulle estensioni. Un elenco di articoli potenzialmente utili è disponibile più avanti nella sezione **Vedere anche**.

Si noti che ogni immagine o gestore estensione usato deve prima essere pubblicato nel Marketplace.

## Sviluppo di modelli ARM
Se non si ha alcuna esperienza con Gestione risorse di Azure e i modelli ARM, può essere utile esaminare alcuni documenti su Gestione risorse di Azure e Calcolo in Gestione risorse di Azure. Il materiale sussidiario e gli esempi indicati sotto presumono una conoscenza di base del linguaggio dei modelli ARM e dell'uso di Calcolo con ARM. Questa sezione è dedicata in particolare alle procedure consigliate e ai requisiti per lo sviluppo di modelli validi specifici per Azure Marketplace. Vengono tenuti in considerazione i requisiti che consentono un'esperienza unificata, semplice e pertinente per i clienti sia dal portale di Ibiza che a livello di codice.

La soluzione verrà probabilmente espressa con un set di modelli, che sono i componenti logici della topologia. La suddivisione dell'espressione della topologia in più file è necessaria per molti motivi, quali facilità di sviluppo, gestibilità, riusabilità di alcuni subset, test modulari ed espansione delle soluzioni basate su un modello. Dopo avere collaborato con molti partner, la suddivisione proposta dal team di Azure prevede di distinguere la topologia in un modello per le risorse comuni (ad esempio, reti virtuali, account di archiviazione e così via), denominato modello di risorsa condivisa, in zero o più modelli per le risorse facoltative, denominati modelli di risorsa facoltativa, e in uno o più modelli per le risorse specifiche della topologia, denominati modelli di risorse di configurazione note.

Una soluzione completa basata su un modello nel Marketplace sarà costituita dagli elementi seguenti:

1. **Un modello principale (mainTemplate.json)**: è il punto di ingresso alla distribuzione generale espressa nel file di modello JSON ARM. È il primo file valutato per la soluzione basata su un modello e quello usato per unire (collegare) tutti gli altri modelli. Questo file deve contenere anche tutto l'input (parametri) desiderato da parte dell'utente finale.
2. **Zero o più modelli collegati**: tutti gli altri modelli collegati dal modello principale per esprimere la topologia completa della soluzione.
3. **Zero o più file di script**: tutti i file di script pertinenti previsti come input per le estensioni da usare per configurare una macchina virtuale.
4. **Un file per la creazione della definizione dell'interfaccia utente (uiDefinition.json createUiDefinition.json)**: file che esegue il mapping dei parametri nel modello ARM agli elementi dell'esperienza utente. Questo file consente ai partner di controllare l'interfaccia utente di cui viene eseguito il rendering ai clienti finali in Ibiza. Tutti gli input richiesti dall'utente finale dovranno essere espressi in questo file. Ogni voce consente di controllare tutti gli aspetti dell'interfaccia utente associati a ogni input, ad esempio widget dell'interfaccia utente, impostazioni predefinite, regole di convalida, messaggi di errore e così via. Quando si inizia a sviluppare ognuno di questi file per esprimere la topologia completa, considerare i criteri seguenti per garantire il modello migliore da offrire nel Marketplace.

## Altri requisiti e suggerimenti

### Set minimo standardizzato di input utente
I parametri consentono di esprimere i valori che dovrebbero essere specificati dall'utente durante la distribuzione del modello. L'input utente può variare da un'applicazione a un'altra e anche da un livello di topologia a un altro. L'elenco di parametri deve essere un giusto compromesso tra l'esigenza di formulare un minor numero di domande necessarie e quella di fornire all'utente le misure adeguate per controllare la distribuzione. Anche se non esistono particolari requisiti per l'elenco completo di input utente, è opportuno assicurarsi che tutte le soluzioni basate su un modello nel Marketplace abbiano un set di input comuni che molti clienti vogliono controllare. Verificare di avere definito i parametri per i valori seguenti:

1. **Località**: area di Azure in cui vengono distribuite le risorse
2.	**Nome rete virtuale**: per le distribuzioni che creano una nuova rete virtuale, nome da usare per creare tale risorsa. Per le distribuzioni che usano una rete virtuale esistente, nome della rete virtuale in cui eseguire la distribuzione.
3.	**Account di archiviazione**: per le distribuzioni che creano un nuovo account di archiviazione, nome dell'account di archiviazione da creare. Per le distribuzioni che usano una rete virtuale esistente, nome dell'account di archiviazione da usare.
4.	**Nome DNS**: nome di dominio della macchina virtuale o del servizio di bilanciamento del carico a cui un utente finale può connettersi.
5.	**Nome utente**: nome utente per una o più macchine virtuali e potenzialmente una o più applicazioni. All'utente finale può essere chiesto più di un nome utente, ma deve esserne richiesto almeno uno.
6.	**Password**: password per una o più macchine virtuali e potenzialmente una o più applicazioni. All'utente finale può essere chiesta più di una password per diverse VM o applicazioni, ma deve esserne richiesta almeno una.
7.	**Chiave pubblica SSH (solo per VM Linux)**: chiave SSH per una o più macchine virtuali. All'utente finale può essere chiesta più di una chiave per diverse VM, ma deve esserne richiesta almeno una.

## Passaggi successivi
Dopo avere esaminato i prerequisiti e completato le attività necessarie, ora è possibile procedere alla creazione dell'offerta di un modello di soluzione, come illustrato in dettaglio nella [Guida alla creazione di un modello di soluzione](marketplace-publishing-solution-template-creation.md)

## Vedere anche
- [Guida introduttiva: Come pubblicare un'offerta in Azure Marketplace](marketplace-publishing-getting-started.md)
- [Procedure consigliate per la creazione di un modello di soluzione](marketplace-publishing-solution-template-best-practices.md)

[link-acct]: marketplace-publishing-accounts-creation-registration.md

<!---HONumber=Oct15_HO3-->