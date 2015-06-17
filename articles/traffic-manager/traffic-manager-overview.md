<properties 
   pageTitle="Panoramica di Gestione traffico"
   description="In questo articolo vengono fornite istruzioni per comprendere cos'è e come funziona Gestione traffico"
   services="traffic-manager"
   documentationCenter=""
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/27/2015"
   ms.author="cherylmc" />

# Panoramica di Gestione traffico

Gestione traffico di Microsoft Azure consente di controllare la distribuzione del traffico dell'utente verso gli endpoint specificati, che possono includere servizi cloud Azure, siti Web e altri endpoint. Gestione traffico applica un motore dei criteri intelligente alle query DNS (Domain Name System) relative ai nomi di dominio delle risorse Internet. I servizi cloud o i siti Web Azure possono essere in esecuzione su differenti datacenter ubicati in aree geografiche diverse.

Con Gestione traffico è possibile:

- **Migliorare l'affidabilità delle applicazioni critiche** - Gestione traffico consente di migliorare la disponibilità delle applicazioni critiche effettuando il monitoraggio degli endpoint in Azure e fornendo capacità di failover automatiche quando un servizio cloud Azure, un sito Web Azure o un'altra posizione non risponde.
- **Migliorare la velocità di risposta per applicazioni a elevate prestazioni** - Azure consente di eseguire servizi cloud o siti Web nei datacenter di tutto il mondo. Gestione traffico può migliorare la velocità di risposta delle applicazioni e i tempi di consegna dei contenuti indirizzando gli utenti finali agli endpoint con la minore latenza di rete dal client.
- **Aggiornamento ed esecuzione della manutenzione del servizio senza tempi di inattività** - Gestione traffico supporta scenari estesi per distribuzioni in locale e cloud ibride tra cui gli scenari "burst-to-cloud", "migrate-to-cloud" e "failover-to-cloud". Per la manutenzione pianificata, disabilitare l'endpoint in Gestione traffico e attendere il completamento della manutenzione delle connessioni esistenti da parte dell'endpoint. Una volta terminato il traffico verso l'endpoint, effettuare l'aggiornamento del servizio su tale endpoint e verificarlo. Quindi abilitarlo nuovamente in Gestione traffico. In questo modo è più facile effettuare la manutenzione e aggiornare i servizi senza tempi di inattività per i client.
- **Distribuzione del traffico per distribuzioni grandi e complesse** - Con i profili di Gestione traffico nidificati, in cui il profilo di Gestione traffico può disporre di un altro profilo di Gestione traffico come un endpoint, è possibile creare configurazioni per ottimizzare le prestazioni e la distribuzione per distribuzioni più grandi e complesse. Per altre informazioni, vedere [Profili nidificati](#nested-profiles).

## Funzionamento di Gestione traffico

Quando si configura un profilo di Gestione traffico, le impostazioni specificate forniscono a Gestione traffico le informazioni necessarie a determinare quale endpoint deve fornire le richieste in base a una query DNS. Attualmente nessun traffico endpoint viene instradato tramite Gestione traffico.

*Figura 1* illustra come Gestione traffico dirige gli utenti verso uno degli endpoint. I numeri nella Figura 1 corrispondono alle seguenti descrizioni numerate:

![Funzionamento di Gestione traffico](./media/traffic-manager-overview/IC740854.jpg)

**Figura 1**

1. **Traffico utente sul nome di dominio aziendale**: Il client richiede informazioni utilizzando il nome di dominio aziendale. L'obiettivo è risolvere un nome DNS in un indirizzo IP. I domini aziendali devono essere riservati tramite le normali registrazioni del nome di dominio Internet mantenute al di fuori di Gestione traffico. Nella Figura 1, il dominio aziendale di esempio è  *www.contoso.com*.
2. **Il dominio aziendale per il nome di dominio Gestione traffico**: Il record di risorse DNS per il dominio aziendale fa riferimento al nome di dominio di Gestione traffico mantenuto in Gestione traffico Azure. Ciò si ottiene utilizzando un record di risorse CNAME che mappa il nome del dominio aziendale sul nome di dominio di Gestione traffico. Nell'esempio, il nome di dominio di Gestione traffico è  *contoso.trafficmanager.net*.
3. **Profilo e nome di dominio di Gestione traffico**: Il nome di dominio di Gestione traffico fa parte del profilo di Gestione traffico. Il server DNS dell'utente invia una nuova query DNS per il nome di dominio di Gestione traffico (nell'esempio,  *contoso.trafficmanager.net*), che viene ricevuto dai server del nome DNS di Gestione traffico.
4. **Regole del profilo di Gestione traffico elaborate**: Gestione traffico utilizza il metodo di bilanciamento del carico specificato e lo stato di monitoraggio per determinare quale endpoint di Azure o altro deve eseguire la richiesta.
5. **Nome di dominio endpoint inviato all'utente**: Gestione traffico restituisce un record CNAME che mappa il nome di dominio di Gestione traffico sul nome di dominio dell'endpoint. Il server DNS dell'utente risolve il nome di dominio dell'endpoint sul proprio indirizzo IP e lo invia all'utente.
6. **L'utente chiama l'endpoint**: L'utente chiama l'endpoint restituito utilizzando direttamente il proprio indirizzo IP.

Siccome il dominio aziendale e l'indirizzo IP risolto sono memorizzati nella cache sulla macchina client, l'utente continua a interagire con l'endpoint scelto fino alla scadenza della voce della cache DNS locale. È importante considerare che il client DNS memorizza nella cache le voci host DNS per la loro durata (TTL). Il recupero delle voci host dalla cache del client DNS ignora il profilo di Gestione traffico e, se l'endpoint diventa non disponibile prima della scadenza del TTL, si verificano ritardi nella connessione. Se il TTL di una voce host DNS memorizzato nella cache scade e il computer client necessita di risolvere nuovamente il nome di dominio aziendale, invia una nuova query DNS. In base al metodo di bilanciamento del carico applicato e all'integrità degli endpoint al momento della richiesta, il computer client potrebbe ricevere un indirizzo IP di un endpoint differente.

## Come implementare Gestione traffico

*Figura 2* illustra i passaggi necessari all'implementazione di Gestione traffico nell'ordine di esecuzione. I passaggi possono essere eseguiti anche in un ordine leggermente differente dopo che la configurazione e le procedure consigliate di Gestione traffico sono state esattamente comprese. I numeri nella Figura 2 corrispondono alle seguenti descrizioni numerate:

![Come configurare Gestione traffico](./media/traffic-manager-overview/IC740855.jpg)

**Figura 2**

1. **Distribuzione dei servizi cloud di Azure, siti Web di Azure o altri endpoint nell'ambiente di produzione**. Quando viene creato un profilo di Gestione traffico, questo deve essere associato a una sottoscrizione. Bisogna poi aggiungere gli endpoint per i servizi cloud e i siti Web di livello standard nella produzione che fanno parte della stessa sottoscrizione. Se un endpoint si trova in gestione temporanea e non in un ambiente di produzione di Azure oppure non fa parte della stessa sottoscrizione, può essere aggiunto come endpoint esterno. Per altre informazioni sui servizi cloud, vedere [Servizi cloud](http://go.microsoft.com/fwlink/p/?LinkId=314074). Per altre informazioni sui siti Web, vedere [Siti Web](http://go.microsoft.com/fwlink/p/?LinkId=393327).
2. **Decidere un nome per il dominio di Gestione traffico**. Prendere in considerazione un nome per il dominio con un prefisso univoco. L'ultima parte del dominio, trafficmanager.net, è fissa. Per altre informazioni, vedere [Procedure consigliate](#best-practices).
3. **Decidere il monitoraggio della configurazione che si desidera utilizzare**. Gestione traffico monitora gli endpoint per assicurare che siano online, indipendentemente dal metodo di bilanciamento del carico. Dopo aver configurato le impostazioni di monitoraggio, in base al sistema di monitoraggio, Gestione traffico non dirigerà il traffico direttamente agli endpoint offline a meno che non rilevi che tutti gli endpoint sono offline o nel caso in cui non sia possibile rilevare lo stato di ciascun endpoint contenuto nel profilo. Per altre informazioni sul monitoraggio, vedere [Monitoraggio Gestione traffico](traffic-manager-monitoring.md).
4. **Decidere il metodo di bilanciamento del carico da utilizzare**. Sono disponibili tre differenti metodi di bilanciamento del carico. Decidere accuratamente il metodo più adatto ai propri requisiti. Se necessario, il metodo scelto può essere modificato in qualunque momento. Considerare inoltre che ciascun metodo richiede passaggi di configurazione leggermente differenti. Per informazioni sui metodi di bilanciamento del carico, vedere [Informazioni sui metodi di bilanciamento del carico di Gestione traffico](traffic-manager-load-balancing-methods.md).
5. **Creazione del profilo e configurazione delle impostazioni**. È possibile utilizzare API REST, Windows PowerShell o il portale di gestione per creare il profilo di Gestione traffico e configurare le impostazioni. Per altre informazioni, vedere [Come configurare le impostazioni di Gestione traffico](#how-to-configure-traffic-manager-settings). I passaggi seguenti presuppongono l'utilizzo di **Creazione rapida** nel portale di gestione. 
   - **Creazione del profilo di Gestione traffico** - Per creare un profilo utilizzando Creazione rapida nel portale di gestione, vedere [Gestione dei profili di Gestione traffico](traffic-manager-manage-profiles.md).
   - **Configurazione delle impostazioni del metodo di bilanciamento del carico** - In Creazione rapida, è necessario selezionare il metodo di bilanciamento del carico per il proprio profilo. Tale impostazione può essere modificata in qualunque momento al termine del completamento dei passaggi di Creazione rapida. Per i passaggi di configurazione, vedere l'argomento corrispondente al metodo di bilanciamento del carico: [Configurazione bilanciamento del carico basato sulle prestazioni](traffic-manager-configure-performance-load-balancing.md), [Configurazione bilanciamento del carico con criterio di failover](traffic-manager-configure-failover-load-balancing.md), [Bilanciamento del carico con metodo Round Robin](traffic-manager-configure-round-robin-load-balancing.md).
   >[AZURE.NOTE] Il metodo Round Robin del bilanciamento del carico ora supporta la distribuzione ponderata del traffico di rete. Tuttavia, a questo punto è necessario utilizzare API REST o Windows PowerShell per configurare il peso. Per altre informazioni e per un esempio di configurazione, vedere [Endpoint esterni di Gestione traffico di Azure e Round Robin ponderato tramite PowerShell](http://azure.microsoft.com/blog/2014/06/26/azure-traffic-manager-external-endpoints-and-weighted-round-robin-via-powershell/) nel blog di Azure.

   - **Configurazione endpoint** - Gli endpoint non sono configurati durante Creazione rapida. Dopo aver creato il profilo e specificato il metodo di bilanciamento del carico, Gestione traffico deve essere a conoscenza degli endpoint. Per i passaggi sulla configurazione degli endpoint, vedere [Gestione endpoint in Gestione traffico](traffic-manager-endpoints.md)

   - **Configurazione delle impostazioni di monitoraggio** - Le impostazioni di monitoraggio non sono configurate durante Creazione rapida. Dopo la creazione del profilo e dopo aver specificato il metodo di bilanciamento del carico, Gestione traffico deve essere a conoscenza dell'oggetto del monitoraggio. Per i passaggi sulla configurazione del monitoraggio, vedere [Monitoraggio Gestione traffico](traffic-manager-monitoring.md).
6. **Verifica del profilo di Gestione traffico**. Accertarsi che il profilo e il dominio lavorino come previsto. Per informazioni su come effettuare la verifica, vedere [Verifica delle impostazioni di Gestione traffico](traffic-manager-testing-settings.md).
7. **Scegliere il record di risorse DNS del nome di dominio aziendale del profilo per attivarlo**. Per altre informazioni, vedere [Scegliere un dominio Internet aziendale per un dominio Gestione traffico](traffic-manager-point-internet-domain.md).

Utilizzando l'esempio della Figura 1, è possibile sostituire il record di risorse DNS sui server con il seguente per scegliere il nome di dominio aziendale per il nome di dominio di Gestione traffico:
    www.contoso.com IN CNAME contoso.trafficmanager.net

## Come configurare le impostazioni di Gestione traffico

È possibile configurare le impostazioni di Gestione traffico utilizzando il portale di gestione con API REST e con cmdlet di Windows PowerShell.

Anche se tutti gli elementi API REST non sono visibili nel portale di gestione, molte impostazioni sono disponibili utilizzando qualunque metodo. Per altre informazioni sull'utilizzo delle API REST, vedere [Operazioni relative a Gestione traffico (riferimento all'API REST)](http://go.microsoft.com/fwlink/p/?LinkId=313584).

Per altre informazioni sui cmdlet di Windows PowerShell per Gestione traffico, vedere [Cmdlet Gestione traffico di Azure](http://go.microsoft.com/fwlink/p/?LinkId=400769).

>[AZURE.NOTE] Attualmente non sono disponibili supporti per la configurazione degli endpoint esterni (tipo = "Any"), pesi per il metodo di bilanciamento del carico Round Robin e profili nidificati con il portale di gestione. È necessario utilizzare REST (vedere [Creazione definizione](http://go.microsoft.com/fwlink/p/?LinkId=400772)) o Windows PowerShell (vedere [Add-AzureTrafficManagerEndpoint](https://msdn.microsoft.com/library/azure/dn690257.aspx)).

### Impostazioni di configurazione nel portale di gestione

Nel portale di gestione è possibile creare il profilo di Gestione traffico utilizzando Creazione rapida. Creazione rapida consente di creare un profilo di base. Dopo aver creato il profilo, è possibile configurare impostazioni aggiuntive o modificare quelle precedentemente configurate. Per altre informazioni sulla creazione del profilo di Gestione traffico utilizzando Creazione rapida, vedere [Gestione profili di Gestione traffico](traffic-manager-manage-profiles.md).

Nel portale di gestione, è possibile configurare le seguenti impostazioni:

- **Prefisso DNS** - Un prefisso univoco creato dall'utente. I profili vengono visualizzati, tramite prefisso, nel portale di gestione.
- **TTL DNS** - Il valore della durata (TTL) DNS controlla la frequenza di creazione di una query, da parte del nome del server di memorizzazione nella cache locale del client, per il sistema DNS di Gestione traffico di Azure per le voci DNS aggiornate.
- **Sottoscrizione** - Selezionare la sottoscrizione corrispondente al profilo. Considerare che tale opzione viene visualizzata solamente se si dispone di sottoscrizioni multiple.
- **Metodo di bilanciamento del carico** - Il modo in cui si desidera che Gestione traffico gestisca il bilanciamento del carico.
- **Ordine di failover** - L'ordine degli endpoint quando si utilizza il metodo di bilanciamento del carico con criterio di failover.
- **Monitoraggio** - Le impostazioni di monitoraggio contengono il protocollo (HTTP o HTTPS), porta e il percorso e nome del file corrispondenti.

### Configurazione delle impostazioni utilizzando API REST

È possibile creare e configurare il profilo di Gestione traffico utilizzando API REST. Per altre informazioni, vedere [Operazioni su Gestione traffico (riferimento API REST)](http://go.microsoft.com/fwlink/?LinkId=313584).

- **Profilo** - Un profilo contiene un prefisso di nome di dominio creato dall'utente. Ciascun profilo corrisponde alla sottoscrizione. È possibile creare ulteriori profili per sottoscrizione. Il nome del profilo è visualizzabile nel portale di gestione. Il nome creato, contenuto nel profilo, è riferito al dominio di Gestione traffico.
- **Definizione** - Una definizione contiene le impostazioni dei criteri e di monitoraggio. Una definizione corrisponde a un profilo. È possibile disporre solamente di una definizione per profilo. La definizione non è visualizzabile nel portale di gestione, nonostante molte delle impostazioni contenute nella definizione siano visibili e possano essere configurate nel portale di gestione.
- **Opzioni DNS** - Ciascuna definizione contiene le opzioni DNS. Qui è possibile configurare la durata TTL DNS.
- **Monitoraggi** - Ciascuna definizione contiene le impostazioni di monitoraggio. Qui è possibile configurare il protocollo, la porta, il percorso relativo e il nome del file. Le impostazioni di monitoraggio sono visualizzabili e possono essere configurate nel portale di monitoraggio. Per altre informazioni, vedere [Monitoraggio di Gestione traffico](traffic-manager-monitoring.md).
- **Criteri** - Ciascuna definizione contiene le impostazioni dei criteri. I metodi di bilanciamento del carico e gli endpoint vengono specificati nei criteri. I criteri non sono visualizzabili nel portale di gestione, anche se alcune impostazioni dei criteri sono visualizzabili e possono essere configurate nel portale di gestione. Per altre informazioni, vedere [Informazioni sui metodi di bilanciamento del carico](traffic-manager-load-balancing-methods.md).

## Configurazione delle impostazioni tramite Windows PowerShell

È possibile creare e configurare il profilo di Gestione traffico utilizzando Windows PowerShell. Per altre informazioni, vedere [Cdmlet di Gestione traffico di Azure](http://go.microsoft.com/fwlink/p/?LinkId=400769).

## Procedure consigliate

- **Rendere il prefisso univoco e facile da comprendere** - Il nome DNS del profilo di Gestione traffico deve essere univoco. È possibile controllare solo la prima parte del nome DNS. Il dominio di Gestione traffico è utilizzato solo a scopi di identificazione e per l'indirizzamento della richiesta client. Gli utenti finali non potranno mai visualizzare tali nomi nei computer client. Tuttavia, i profili vengono identificati dal nome di dominio quindi è importante che l'utente sia in grado di identificarlo rapidamente dagli altri domini elencati nel portale di gestione.
- **Utilizzare i punti per maggiore univocità o per rendere leggibili i nomi di dominio** - È possibile utilizzare i punti anche per separare parti del prefisso del nome di dominio.  Se si desidera creare più criteri nel portale di gestione, utilizzare una gerarchia consistente per differenziare un servizio dall'altro. Ad esempio, Contoso dispone di servizi globali per il Web, la fatturazione e per la gestione dell'utilità. I tre criteri sarebbero  *web.contoso.trafficmanager.net*,  *bill.contoso.trafficmanager.net* e  *util.contoso.trafficmanager.net*. Quando si impostano i servizi cloud o i siti Web, utilizzare i nomi che includono la posizione. Ad esempio,  *web-us-contoso.cloudapp.net* e  *web-asia-contoso.cloudapp.net*. I limiti vengono imposti da DNS. Si presuppone che un dominio sia una sequenza di etichette separate da punti (label.label.label.label.etc.). Alla data attuale, i limiti per i nomi di dominio nel portale di gestione sono i seguenti:
   - Ciascuna etichetta non può disporre di più di 63 caratteri.
   - Non è possibile disporre di più di 40 etichette. Siccome due etichette vengono utilizzate da trafficmanager.net, per il proprio prefisso rimangono 38 etichette.
   - Il nome di dominio intero non può contenere più di 253 caratteri. Tenere presente che trafficmanager.net occupa 19 di tali caratteri.
- **TTL DNS** - Il valore TTL DNS controlla la frequenza di creazione di una query, da parte del nome del server di memorizzazione nella cache locale del client, per il sistema DNS di Gestione traffico di Azure per le voci DNS aggiornate. Tutte le modifiche effettuate in Gestione traffico, come le modifiche al profilo o alla disponibilità dell'endpoint, impiegheranno tale periodo di tempo per essere aggiornate tramite il sistema globale dei server DNS. Si consiglia di lasciare l'impostazione al valore predefinito di 300 secondi (5 minuti). Un numero più elevato aumenta il tempo di memorizzazione nella cache delle risposte DNS di Gestione traffico da parte delle risoluzioni e dei client DNS, riducendo la latenza generale della query DNS. Tuttavia, se è richiesto un failover molto rapido, è preferibile configurare un valore inferiore.
- **Gli endpoint devono trovarsi in una sottoscrizione singola** - Tutti gli endpoint devono trovarsi nella stessa sottoscrizione in cui è stato creato il profilo. È possibile aggiungere endpoint da sottoscrizioni differenti a un profilo come endpoint esterni ma Azure non li rimuoverà automaticamente se si disabilita o elimina il servizio associato. Quindi, gli endpoint esterni rimangono nel profilo di Gestione traffico e l'utente potrà continuare a essere fatturato a meno che non vengano rimossi manualmente.
- **Solo servizi di produzione** - Sono disponibili solo gli endpoint nell'ambiente di produzione. Non è possibile rivolgersi a endpoint in esecuzione in un ambiente in gestione temporanea. Considerare che se si esegue uno scambio di indirizzo IP virtuale (VIP) mentre un profilo indirizza del traffico, questo utilizza l'endpoint appena scambiato nell'ambiente di produzione.
- **Denominare gli endpoint in modo che possano essere facilmente identificati** - Considerare il prefisso DNS che si desidera utilizzare. Il nome DNS viene utilizzato perché è garantito come univoco in una sottoscrizione, mentre il nome del servizio cloud o sito Web potrebbe non esserlo. Per evitare confusione, denominare un servizio cloud o un sito Web e fornire un prefisso DNS uguale o simile. Se si dispone di più di 20 servizi cloud e siti Web, delle denominazioni inadeguate potrebbero complicare la ricerca dell'endpoint corretto. Inoltre, endpoint denominati in modo inadeguato rendono i profili difficili da gestire.
- **Tutti gli endpoint in un profilo devono eseguire le stesse operazioni e porte** - Se gli endpoint vengono mischiati, un client potrebbe chiamarne uno che non esegue la richiesta desiderata.
- **Tutti i servizi cloud in un profilo devono utilizzare le stesse impostazioni di monitoraggio** - È possibile scegliere solamente un singolo percorso e file per monitorare tutti gli endpoint in una definizione fornita. È possibile immettere "/" nella casella di testo **Percorso e nome file corrispondente** in modo che il monitoraggio tenti di accedere al percorso e al nome del file predefinito.
- **Disabilitare gli endpoint per le modifiche temporanee, piuttosto che modificare la configurazione** - In molti casi, potrebbe essere conveniente utilizzare un endpoint offline. Piuttosto che rimuovere l'endpoint dal profilo, disabilitare semplicemente il singolo endpoint nel profilo. In questo modo l'endpoint continua a far parte del profilo a questo agisce come se l'endpoint non sia incluso. Si tratta di un'operazione molto utile per la rimozione temporanea di un endpoint in modalità di manutenzione o in corso di ridistribuzione. Dopo che l'endpoint è nuovamente in esecuzione, è possibile abilitarlo. Per altre informazioni, vedere [Gestione endpoint in Gestione traffico](traffic-manager-endpoints.md).
- **Disabilitare un profilo per modifiche temporanee, invece che eliminarlo** - Potrebbe essere conveniente tenere un profilo offline e non solo degli endpoint specifici. A tale scopo, disabilitare il profilo. Quando si disabilita un profilo, tutte le impostazioni restano disponibili per poter effettuare modifiche nel portale di gestione e riportare facilmente e velocemente il profilo online quando si desidera utilizzarlo nuovamente. Per altre informazioni, vedere [Gestione degli endpoint in Gestione traffico](traffic-manager-endpoints.md).
- **Archiviazione** - Quando si utilizza Gestione traffico, è di fondamentale importanza il modo in cui si progetta il percorso e la distribuzione dell'archiviazione. Pensare alla transazione end-to-end e alla modalità di scorrimento dei dati quando si progetta e distribuisce l'applicazione per Gestione traffico.
- **SQL Azure** - Come per la progettazione di archiviazione, analizzare lo stato e i requisiti per i dati dell'applicazione quando gli endpoint vengono estesi a più aree geografiche.

## Profili nidificati

È possibile specificare il nome di un altro profilo di Gestione traffico come endpoint. Si tratta di una procedura denominata profili nidificati. Il nome del profilo di Gestione traffico è il corrispondente nome DNS, come contoso-europe.trafficmanager.net.

In questo modo è possibile configurare Gestione traffico in modo che le query nome DNS in arrivo vengano analizzate in un insieme di livelli per assicurare che il client richiedente sia indirizzato al set di endpoint corretti. *Figure 3* illustra un esempio.

![Esempio di profili di Gestione traffico nidificati](./media/traffic-manager-overview/IC751072.png)

**Figura 3**

È possibile nidificare fino a 10 livelli e ciascun profilo può essere configurato con un differente metodo di bilanciamento del carico.

Ad esempio, è possibile creare una configurazione nei seguenti punti:

- Al livello superiore (il profilo di Gestione traffico mappato per il nome DNS esterno), è possibile configurare il profilo con il metodo di bilanciamento del carico basato sulle prestazioni.
- A livello intermedio, un set di profili di Gestione traffico rappresenta differenti datacenter e l'utilizzo del metodo di bilanciamento del carico Round Robin.
- A livello inferiore, un set di endpoint del servizio client in ciascun servizio datacenter richiesto dal traffico dell'utente.

Il risultato è che gli utenti vengono indirizzati a un datacenter appropriato a livello regionale in base alle prestazioni e verso un servizio cloud all'interno del datacenter in base a una distribuzione del carico uguale o ponderata. Ad esempio, la ponderazione può essere utilizzata per distribuire una piccola percentuale di traffico su una distribuzione nuova o di prova per la verifica o per suggerimenti clienti.

*Figura 4* illustra la configurazione.

![Esempio di profili di Gestione traffico multilivello](./media/traffic-manager-overview/IC751073.png)

**Figura 4**

Nella  *Figure 4*, il profilo di Gestione traffico nel livello superiore è un profilo padre e i profili di Gestione traffico nel livello intermedio sono profili figli.

Se Gestione traffico indirizza gli utenti verso un profilo figlio che dispone di un piccolo numero di endpoint integri, è possibile sovraccaricare tali endpoint e causare problemi di prestazioni. Per impedire tale situazione, è possibile configurare il profilo di Gestione traffico padre con una soglia di endpoint integri per determinare se ciascun endpoint all'interno dei profili figli di tale padre possono ricevere traffico. Ad esempio, se si desidera assicurare la disponibilità di almeno tre endpoint integri all'interno dei profili figli, è necessario impostare il valore della soglia su 3. Nell'esempio della Figura 4, è necessario configurare il profilo di Gestione traffico di livello superiore per tale soglia.

Per aggiungere un profilo di Gestione traffico come un endpoint e configurare il numero minimo di endpoint integri, è necessario utilizzare REST (vedere [Creazione definizione](http://go.microsoft.com/fwlink/p/?LinkId=400772)) o Windows PowerShell (vedere [Add-AzureTrafficManagerEndpoint](https://msdn.microsoft.com/library/azure/dn690257.aspx)). Non è possibile utilizzare il portale di gestione.

## Figure di Gestione traffico

Se si desidera visualizzare le figure di questo argomento su PowerPoint per la propria presentazione su Gestione traffico o per effettuare modifiche a scopo personale, vedere [Figure di Gestione traffico nella Documentazione MSDN](http://gallery.technet.microsoft.com/Traffic-Manager-figures-in-887e7c99).

## Vedere anche

[Servizi cloud](http://go.microsoft.com/fwlink/p/?LinkId=314074)

[Siti Web](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[Operazioni relative a Gestione traffico (riferimento all'API REST)](http://go.microsoft.com/fwlink/p/?LinkId=313584)

[Cdmlet di Gestione traffico di Azure](http://go.microsoft.com/fwlink/p/?LinkId=400769)

<!--HONumber=49--> 