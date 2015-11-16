<properties
	pageTitle="Risoluzione dei problemi relativi a errori di allocazione delle VM| Microsoft Azure"
	description="Risoluzione dei problemi relativi a errori di allocazione quando si crea, si riavvia o si ridimensiona una macchina virtuale in Azure"
	services="virtual-machines, azure-resource-manager"
	documentationCenter=""
	authors="jiangchen79"
	manager="felixwu"
	editor=""
	tags="top-support-issue"/>

<tags
	ms.service="virtual-machines"
	ms.workload="na"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/29/2015"
	ms.author="kenazk"/>



# Risoluzione dei problemi relativi a errori di allocazione quando si crea, si riavvia o si ridimensiona una macchina virtuale in Azure

## Riepilogo
Quando si crea una macchina virtuale, si riavviano VM arrestate (deallocate) e si ridimensiona una VM, Microsoft Azure alloca risorse di calcolo alla sottoscrizione. In alcuni casi possono verificarsi errori quando si eseguono queste operazioni anche prima di raggiungere i limiti della sottoscrizione di Azure. Questo articolo illustra le cause di alcuni dei più comuni errori di allocazione e suggerisce una possibile correzione. Queste informazioni potrebbero risultare utili anche quando si pianifica la distribuzione dei servizi.

Se in qualsiasi punto dell'articolo sono necessarie altre informazioni, è possibile contattare gli esperti di Azure nei [forum MSDN e overflow dello stack relativi ad Azure](http://azure.microsoft.com/support/forums/). In alternativa, è anche possibile archiviare un evento imprevisto di supporto tecnico di Azure. Passare al [sito del Supporto tecnico per Azure](http://azure.microsoft.com/support/options/) e fare clic su **Ottenere supporto**.

La prima sezione "Passaggi di base" elenca i passaggi per risolvere i problemi comuni, la seconda sezione include i passaggi per la risoluzione in base al messaggio di errore specifico.

## Passaggi di base

Questi passaggi di base possono facilitare la risoluzione di molti errori di allocazione nelle macchine virtuali.

### Modello di distribuzione classica 

- Ridimensionare la VM specificando una dimensione della VM diversa.<br> Fare clic su Esplora tutto > Macchine virtuali (classico) > macchina virtuale personale > Impostazioni > **Dimensioni**. Per i passaggi dettagliati, vedere l'articolo relativo alla [modifica delle dimensioni di una macchina virtuale di Microsoft Azure](https://msdn.microsoft.com/library/dn168976.aspx).

- Eliminare tutte le VM dal servizio cloud e ricrearle.<br> Fare clic su Esplora tutto > Macchine virtuali (classico) > macchina virtuale personale > Elimina >. Fare quindi clic su Nuovo > Calcolo > [immagine di macchina virtuale]

### Modello di distribuzione di Gestione risorse

- Arrestare, ovvero deallocare, tutte le VM nello stesso set di disponibilità, quindi riavviarle.<br> Per arrestare: fare clic su Gruppi di risorse > gruppo di risorse personale > Risorse > set di disponibilità personale > Macchine virtuali > macchina virtuale personale > Arresta

	Dopo l'arresto di tutte le VM, selezionare la prima e fare clic su Avvia.

### Informazioni preliminari: come funziona l'allocazione
I server nei data center di Azure sono partizionati in cluster. In genere, viene eseguita una richiesta di allocazione in più cluster, ma è possibile che determinati vincoli nella richiesta di allocazione impongano alla piattaforma Azure di eseguire la richiesta in un solo cluster. In questo articolo si farà riferimento a questa operazione con il termine "bloccata su un cluster". Il diagramma 1 seguente illustra un'allocazione normale eseguita in più cluster. Il diagramma 2 illustra un'allocazione bloccata sul Cluster 2, perché è quello che ospita il servizio cloud CS\_1 o il set di disponibilità. ![Diagramma di allocazione](./media/virtual-machines-allocation-failure/Allocation1.png)

### Perché si verifica un errore di allocazione
Quando una richiesta di allocazione è bloccata su un cluster, la probabilità di non riuscire a trovare risorse disponibili è più alta, perché il pool di risorse disponibili è più ridotto. Inoltre, se la richiesta di allocazione è bloccata su un cluster, ma il tipo di risorsa richiesto non è supportato da quel cluster, la richiesta non riuscirà anche se nel cluster ci sono risorse disponibili. Il diagramma 3 seguente illustra un'allocazione bloccata non riuscita perché nel solo cluster candidato non ci sono risorse disponibili. Il diagramma 4 illustra un'allocazione bloccata non riuscita perché il solo cluster candidato non supporta le dimensioni della VM richieste, anche se nel cluster ci sono risorse disponibili. ![Errore di allocazione bloccata](./media/virtual-machines-allocation-failure/Allocation2.png)

## Risoluzione dei problemi relativi a errori di allocazione nel modello di distribuzione di Gestione risorse di Azure
Ecco gli scenari di allocazione comuni che causano una richiesta di allocazione bloccata. Ogni scenario sarà esaminato in dettaglio più avanti in questo articolo. -Ridimensionamento di una VM o aggiunta di altre VM o istanze del ruolo a un servizio cloud esistente - Riavvio di VM arrestate (deallocate): deallocazione **parziale** - Riavvio di VM arrestate (deallocate): deallocazione **completa**

Quando viene visualizzato un errore di allocazione, vedere se è applicabile uno degli scenari descritti. Usare l'errore di allocazione restituito dalla piattaforma Azure per identificare lo scenario corrispondente. Se la richiesta è bloccata su un cluster esistente, provare a rimuovere alcuni dei vincoli di blocco per rendere la richiesta eseguibile su più cluster, aumentando di conseguenza la possibilità che l'allocazione riesca.

In generale, purché l'errore non indichi che "le dimensioni della VM richieste non sono supportate", è sempre possibile riprovare in un momento successivo perché nel cluster potrebbero liberarsi risorse sufficienti per soddisfare la richiesta. Se il problema sono le dimensioni richieste per la VM non supportate, vedere di seguito le soluzioni alternative.

### Scenario di allocazione: ridimensionamento di una VM o aggiunta di altre VM a un set di disponibilità esistente
**Errore**

Upgrade\_VMSizeNotSupported* o GeneralError*

**Causa del blocco su un cluster**

La richiesta di ridimensionamento di una VM o l'aggiunta di una VM a un set di disponibilità esistente deve essere eseguita nel cluster originale che ospita il set di disponibilità esistente. La creazione di un nuovo set di disponibilità consente alla piattaforma Azure di trovare un altro cluster con risorse disponibili o che supporti le dimensioni della VM richieste.

**Soluzione alternativa**

Se l'errore è Upgrade\_VMSizeNotSupported*, provare con dimensioni della VM diverse. Se non è possibile usare dimensioni della VM diverse, arrestare tutte le VM nel set di disponibilità. In questo modo si potranno modificare le dimensioni della VM allocandola a un cluster che supporti le dimensioni della VM desiderate.

Se l'errore è GeneralError*, è probabile che il tipo di risorsa (ad esempio dimensioni della VM specifiche) sia supportato dal cluster, che al momento non ci siano risorse disponibili nel cluster. Se la VM può far parte di un set di disponibilità diverso, creare una nuova VM in un altro set di disponibilità nella stessa area. La nuova VM può quindi essere aggiunta alla stessa rete virtuale.

### Scenario di allocazione: riavvio di VM arrestate (deallocate) - deallocazione parziale
**Errore**

GeneralError*

**Causa del blocco su un cluster**

Deallocazione **parziale** significa che una o più VM in un set di disponibilità sono state arrestate (deallocate), ma **non tutte**. Quando si arresta (dealloca) una VM, vengono rilasciate le risorse associate. Il riavvio della VM arrestata (deallocata) è quindi una nuova richiesta di allocazione. Il riavvio di VM in un set di disponibilità parzialmente deallocato equivale all'aggiunta di VM a un set di disponibilità esistente e la richiesta di allocazione deve essere eseguita nel cluster originale che ospita il set di disponibilità esistente.

**Soluzione alternativa**

Provare ad arrestare tutte le VM nel set di disponibilità prima di riavviare la prima. In questo modo si assicura che venga eseguito un nuovo tentativo di allocazione e sia possibile selezionare un nuovo cluster con capacità disponibile.

### Scenario di allocazione: riavvio di VM arrestate (deallocate) - deallocazione completa
**Errore**

GeneralError*

**Causa del blocco su un cluster**

Deallocazione **completa** significa che sono state arrestate (deallocate) **tutte** le VM in un set di disponibilità. La richiesta di allocazione per il riavvio di queste VM sarà eseguita in tutti i cluster che supportano le dimensioni desiderate.

**Soluzione alternativa**

Provare a selezionare nuove dimensioni della VM da allocare. In caso contrario, riprovare più tardi.

## Risoluzione dei problemi relativi a errori di allocazione nel modello di distribuzione classica
Ecco gli scenari di allocazione comuni che causano una richiesta di allocazione bloccata. Ogni scenario sarà esaminato in dettaglio più avanti in questo articolo. -Ridimensionamento di una VM o aggiunta di altre VM o istanze del ruolo a un servizio cloud esistente - Riavvio di VM arrestate (deallocate): deallocazione parziale - Riavvio di VM arrestate (deallocate): deallocazione completa - Distribuzioni di staging/produzione, solo PaaS (Platform-as-a-Service) - Gruppo di affinità - Prossimità di VM/servizio - Rete virtuale basata su gruppi di affinità

Quando viene visualizzato un errore di allocazione, vedere se è applicabile uno degli scenari descritti. Usare l'errore di allocazione restituito dalla piattaforma Azure per identificare lo scenario corrispondente. Se la richiesta è bloccata, provare a rimuovere alcuni dei vincoli di blocco per rendere la richiesta eseguibile su più cluster, aumentando di conseguenza la possibilità che l'allocazione riesca.

In generale, purché l'errore non indichi che "le dimensioni della VM richieste non sono supportate", è sempre possibile riprovare in un momento successivo perché nel cluster potrebbero liberarsi risorse sufficienti per soddisfare la richiesta. Se il problema sono le dimensioni richieste per la VM non supportate, provare con dimensioni della VM diverse. In caso contrario, l'unica opzione consiste nel rimuovere il vincolo di blocco.

Due scenari comuni di allocazione non riuscita sono correlati al gruppo di affinità. In passato, il gruppo di affinità è stato usato per fornire la prossimità alle istanze di VM o servizi o per abilitare la creazione della rete virtuale. Con l'introduzione della rete virtuale dell'area, il gruppo di affinità non è più necessario per creare una rete virtuale. A seguito della riduzione della latenza di rete nell'infrastruttura di Azure, l'indicazione che riguarda l'uso del gruppo di affinità per la prossimità di VM/servizi è stata modificata.

Il diagramma 5 seguente illustra la tassonomia degli scenari di allocazione (bloccata). ![Tassonomia di allocazione bloccata](./media/virtual-machines-allocation-failure/Allocation3.png)

> [AZURE.NOTE]L'errore indicato in ogni scenario di allocazione è in forma breve. Per le stringhe di errore dettagliate, vedere l'[Appendice](#appendix).

### Scenario di allocazione: ridimensionamento di una VM o aggiunta di altre VM o istanze del ruolo a un servizio cloud esistente
**Errore**

Upgrade\_VMSizeNotSupported* o GeneralError*

**Causa del blocco su un cluster**

La richiesta di ridimensionamento di una VM o l'aggiunta di una VM o un'istanza del ruolo a un servizio cloud esistente deve essere eseguita nel cluster originale che ospita il servizio cloud esistente. La creazione di un nuovo servizio cloud consente alla piattaforma Azure di trovare un altro cluster con risorse disponibili o che supporti le dimensioni della VM richieste.

**Soluzione alternativa**

Se l'errore è Upgrade\_VMSizeNotSupported*, provare con dimensioni della VM diverse. Se l'uso di dimensioni della VM diverse non è possibile, ma è accettabile usare un indirizzo IP virtuale (VIP) diverso, creare un nuovo servizio cloud per ospitare la nuova VM e aggiungere il nuovo servizio cloud alla rete virtuale dell'area in cui sono in esecuzione le VM esistenti. Se il servizio cloud esistente non usa la rete virtuale dell'area, è comunque possibile creare una nuova rete virtuale per il nuovo servizio cloud e quindi connettere la [rete virtuale esistente a quella nuova](https://azure.microsoft.com/it-IT/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Per altre informazioni, vedere il blog relativo alle [reti virtuali dell'area](http://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Se l'errore è GeneralError*, è probabile che il tipo di risorsa (ad esempio dimensioni della VM specifiche) sia supportato dal cluster, ma che al momento non ci siano risorse disponibili nel cluster. Come detto sopra, provare ad aggiungere la risorsa di calcolo desiderata tramite la creazione di un nuovo servizio cloud (notare che il nuovo servizio cloud deve usare un indirizzo IP virtuale diverso) e usare la rete virtuale dell'area per connettere i servizi cloud.

### Scenario di allocazione: riavvio di VM arrestate (deallocate) - deallocazione parziale

**Errore**

GeneralError*

**Causa del blocco su un cluster**

Deallocazione **parziale** significa che una o più VM in un servizio cloud sono state arrestate (deallocate), ma **non tutte**. Quando si arresta (dealloca) una VM, vengono rilasciate le risorse associate. Il riavvio della VM arrestata (deallocata) è quindi una nuova richiesta di allocazione. Il riavvio di VM in un servizio cloud parzialmente deallocato equivale all'aggiunta di VM a un servizio cloud esistente e la richiesta di allocazione deve essere eseguita nel cluster originale che ospita il servizio cloud esistente. La creazione di un servizio cloud diverso consente alla piattaforma Azure di trovare un altro cluster con risorse disponibili o che supporti le dimensioni della VM richieste.

**Soluzione alternativa**

Se è accettabile usare un indirizzo IP virtuale diverso, eliminare le VM arrestate (deallocate), mantenendo però i dischi associati, e riaggiungere le VM tramite un servizio cloud diverso. Usare la rete virtuale dell'area per connettere i servizi cloud: 1. Se il servizio cloud esistente usa una rete virtuale dell'area, è sufficiente aggiungere il nuovo servizio cloud alla stessa rete virtuale. 2. Se il servizio cloud esistente non usa la rete virtuale dell'area, creare una nuova rete virtuale per il nuovo servizio cloud e quindi connettere la [rete virtuale esistente a quella nuova](https://azure.microsoft.com/it-IT/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Per altre informazioni, vedere il blog relativo alle [reti virtuali dell'area](http://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

### Scenario di allocazione: riavvio di VM arrestate (deallocate) - deallocazione completa
**Errore**

GeneralError*

**Causa del blocco su un cluster**

Deallocazione **completa** significa che sono state arrestate (deallocate) **tutte** le VM di un servizio cloud. Al momento, le richieste di allocazione per il riavvio di queste VM devono essere eseguite nel cluster originale che ospita il servizio cloud. La creazione di un nuovo servizio cloud consente alla piattaforma Azure di trovare un altro cluster con risorse disponibili o che supporti le dimensioni della VM richieste.

**Soluzione alternativa**

Se è accettabile usare un indirizzo IP virtuale diverso, eliminare le VM arrestate (deallocate) originali, mantenendo però i dischi associati, ed eliminare il servizio cloud corrispondente. Le risorse di calcolo associate sono già state rilasciate quando sono state arrestate (deallocate) le VM. Creare un nuovo servizio cloud per riaggiungere le VM.

### Scenario di allocazione: distribuzioni di staging/produzione, solo PaaS (Platform-as-a-Service)
**Errore**

New\_General* o New\_VMSizeNotSupported*

**Causa del blocco su un cluster**

Le distribuzione di staging e di produzione di un servizio cloud sono ospitate nello stesso cluster. Quando si aggiunge la seconda distribuzione, la richiesta di allocazione corrispondente verrà eseguita nello stesso cluster che ospita la prima distribuzione.

**Soluzione alternativa**

Se è accettabile, eliminare la prima distribuzione e il servizio cloud originale e ridistribuire il servizio cloud. Questa azione potrebbe inserire la prima distribuzione in un cluster con risorse disponibili sufficienti per entrambe le distribuzioni o in un cluster che supporta le dimensioni della VM richieste.

### Scenario di allocazione: gruppo di affinità - prossimità di VM/servizi
**Errore**

New\_General* o New\_VMSizeNotSupported*

**Causa del blocco su un cluster**

Qualsiasi risorsa di calcolo assegnata a un gruppo di affinità è associata a un cluster. Le nuove richieste di risorse di calcolo in quel gruppo di affinità vengono eseguite nello stesso cluster in cui sono ospitate le risorse esistenti. Questo vale indipendentemente dal fatto che le nuove risorse vengano create tramite un servizio cloud nuovo o esistente.

**Soluzione alternativa**

Se non è necessario, non usare un gruppo di affinità o non tentare di raggruppare le risorse di calcolo in più gruppi di affinità.

### Scenario di allocazione: rete virtuale basata su gruppi di affinità
**Errore**

New\_General* o New\_VMSizeNotSupported*

**Causa del blocco su un cluster**

Prima dell'annuncio della rete virtuale dell'area era necessario associare una rete virtuale a un gruppo di affinità. Di conseguenza, le risorse di calcolo inserite nel gruppo di affinità sono soggette agli stessi vincoli descritti nella sezione "Scenario di allocazione: gruppo di affinità - prossimità di VM/servizi" precedente, ovvero le risorse di calcolo sono associate a un cluster.

**Soluzione alternativa**

Se il gruppo di affinità non è necessario, creare una nuova rete virtuale dell'area per le nuove risorse aggiunte, quindi [connettere la rete virtuale esistente a quella nuova](https://azure.microsoft.com/it-IT/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Per altre informazioni, vedere il blog relativo alle [reti virtuali dell'area](http://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

In alternativa, è possibile [eseguire la migrazione della rete virtuale basata su gruppi di affinità alla rete virtuale dell'area](http://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/) e quindi provare a riaggiungere le risorse desiderate.

## Appendice
### Ricerca della stringa di errore
**New\_VMSizeNotSupported***

Impossibile eseguire il provisioning delle dimensioni della macchina virtuale (o della combinazione di dimensioni delle macchine virtuali) richieste da questa distribuzione, a causa di vincoli della richiesta di distribuzione. Se possibile, provare a rilasciare vincoli quali associazioni a reti virtuali, distribuzione a un servizio ospitato che non include alcun'altra distribuzione e a un gruppo di affinità diverso o senza alcun gruppo di affinità oppure provare a distribuire in un'area diversa.

**New\_General***

Allocazione non riuscita. Impossibile soddisfare i vincoli nella richiesta. La nuova distribuzione del servizio richiesta è vincolata a un gruppo di affinità, ha come destinazione una rete virtuale o è presente una distribuzione esistente in questo servizio ospitato. Una di queste condizioni vincola la nuova distribuzione a risorse Azure specifiche. Riprovare più tardi o provare a ridurre le dimensioni della macchina virtuale o il numero di istanze del ruolo. In alternativa, è possibile rimuovere i vincoli sopra indicati o tentare di distribuire in un'area diversa.

**Upgrade\_VMSizeNotSupported***

Non è possibile eseguire l'aggiornamento della distribuzione. È possibile che la dimensione della macchina virtuale XXX richiesta non sia disponibile nella distribuzione esistente. Riprovare più tardi, provare con una dimensione della macchina virtuale diversa o con un numero minore di istanze del ruolo oppure creare una distribuzione in un servizio ospitato vuoto con un nuovo gruppo di affinità o senza associazione a un gruppo di affinità.

**GeneralError***

Si è verificato un errore interno del server. Ripetere la richiesta." o "Non è stato possibile produrre un'allocazione per il servizio.

## Risorse aggiuntive
### Contattare il supporto tecnico di Azure

Se questo articolo non ha consentito di risolvere il problema di Azure, esplorare i forum di Azure su [MSDN e Stack Overflow](http://azure.microsoft.com/support/forums/). È anche possibile registrare una richiesta di supporto per Azure relativa al problema. Accedere al sito del [Supporto tecnico di Azure](http://azure.microsoft.com/support/options/) e fare clic su Ottenere supporto. Per informazioni sull'uso del Supporto tecnico di Azure, leggere le [Domande frequenti sul supporto tecnico di Azure](http://azure.microsoft.com/support/faq/).

<!---HONumber=Nov15_HO2-->