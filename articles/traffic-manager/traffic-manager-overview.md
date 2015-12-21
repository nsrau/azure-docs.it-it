<properties 
   pageTitle="Che cos'è Gestione traffico | Microsoft Azure"
   description="In questo articolo vengono fornite istruzioni per comprendere cos'è e come funziona Gestione traffico"
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/07/2015"
   ms.author="joaoma" />

# Gestione traffico di Azure

Con Gestione traffico di Microsoft Azure è possibile controllare la distribuzione del traffico utente agli endpoint specificati, che possono includere servizi cloud, siti Web e altri endpoint di Azure. Gestione traffico applica un motore dei criteri intelligente alle query DNS (Domain Name System) relative ai nomi di dominio delle risorse Internet. I servizi cloud o i siti Web Azure possono essere in esecuzione su differenti datacenter ubicati in aree geografiche diverse.

Con Gestione traffico è possibile:

- **Migliorare l'affidabilità delle applicazioni critiche**: Gestione traffico consente di migliorare la disponibilità delle applicazioni critiche effettuando il monitoraggio degli endpoint in Azure e fornendo capacità di failover automatiche quando un servizio cloud Azure, un sito Web Azure o un'altra posizione non risponde.
- **Migliorare la velocità di risposta per applicazioni a elevate prestazioni**: Azure consente di eseguire servizi cloud o siti Web nei datacenter di tutto il mondo. Gestione traffico può migliorare la velocità di risposta delle applicazioni e i tempi di consegna dei contenuti indirizzando gli utenti finali agli endpoint con la minore latenza di rete dal client.
- **Aggiornamento ed esecuzione della manutenzione del servizio senza tempi di inattività**: Gestione traffico supporta scenari estesi per distribuzioni in locale e cloud ibride tra cui gli scenari "burst-to-cloud", "migrate-to-cloud" e "failover-to-cloud". Per la manutenzione pianificata, l'endpoint in Gestione traffico deve essere disabilitato e si deve attendere che completi la manutenzione delle connessioni esistenti. Una volta terminato il traffico verso l'endpoint, effettuare l'aggiornamento del servizio su tale endpoint e verificarlo. Quindi abilitarlo nuovamente in Gestione traffico. In questo modo è possibile gestire e aggiornare i servizi senza tempi di inattività per i client.
- **Distribuzione del traffico per distribuzioni grandi e complesse**: con i profili di Gestione traffico nidificati, in cui il profilo di Gestione traffico può disporre di un altro profilo di Gestione traffico come un endpoint, è possibile creare configurazioni per ottimizzare le prestazioni e la distribuzione per distribuzioni più grandi e complesse. Per altre informazioni, vedere [Profili annidati](#nested-profiles).

## Modalità di funzionamento di Gestione traffico

Quando si configura un profilo di Gestione traffico, le impostazioni specificate offrono le informazioni necessarie per determinare quale endpoint deve soddisfare la richiesta in base a una query DNS. Il traffico dell'endpoint non viene effettivamente indirizzato attraverso Gestione traffico.

La *Figura 1* mostra il modo in cui Gestione traffico indirizza gli utenti a un set di endpoint. I numeri nella Figura 1 corrispondono alle seguenti descrizioni numerate:

![Modalità di funzionamento di Gestione traffico](./media/traffic-manager-overview/IC740854.jpg)

**Figura 1**

1. **Traffico utente a un nome di dominio aziendale**: il client richiede informazioni usando il nome di dominio aziendale. L'obiettivo è risolvere un nome DNS in un indirizzo IP. I domini aziendali devono essere riservati tramite le normali registrazioni del nome di dominio Internet mantenute al di fuori di Gestione traffico. Nella Figura 1, il dominio aziendale di esempio è *www.contoso.com*.
2. **Dal nome di dominio aziendale al nome di dominio di Gestione traffico**: il record di risorse DNS per il dominio aziendale fa riferimento al nome di dominio di Gestione traffico mantenuto in Gestione traffico di Azure. Ciò si ottiene utilizzando un record di risorse CNAME che mappa il nome del dominio aziendale sul nome di dominio di Gestione traffico. Nell'esempio, il nome di dominio di Gestione traffico è *contoso.trafficmanager.net*.
3. **Nome di dominio e profilo di Gestione traffico**: il nome di dominio di Gestione traffico fa parte del profilo di Gestione traffico. Il server DNS dell'utente invia una nuova query DNS per il nome di dominio di Gestione traffico (nell'esempio, *contoso.trafficmanager.net*), che viene ricevuta dai server del nome DNS di Gestione traffico.
4. **Elaborazione delle regole del profilo di Gestione traffico**: Gestione traffico usa il metodo di bilanciamento del carico specificato e lo stato del monitoraggio per determinare quale endpoint di Azure o di altro tipo soddisferà la richiesta.
5. **Invio del nome di dominio dell'endpoint all'utente**: Gestione traffico restituisce un record CNAME che esegue il mapping del nome di dominio di Gestione traffico al nome di dominio dell'endpoint. Il server DNS dell'utente risolve il nome di dominio dell'endpoint sul proprio indirizzo IP e lo invia all'utente.
6. **L'endpoint viene chiamato dall'utente**: l'endpoint restituito viene chiamato direttamente dall'utente mediante il relativo indirizzo IP.

Poiché il dominio aziendale e l'indirizzo IP risolto vengono memorizzati nella cache del computer client, l'utente continua a interagire con l'endpoint scelto fino alla scadenza della voce cache DNS locale. È importante considerare che il client DNS memorizza nella cache le voci host DNS per la loro durata (TTL). Recuperando le voci host dalla cache del client DNS, viene ignorato il profilo di Gestione traffico causando ritardi nella connessione se l'endpoint diventa non disponibile prima della scadenza della durata TTL. Se la durata TTL di una voce host DNS nella cache scade e il computer client deve risolvere nuovamente il nome di dominio aziendale, viene inviata una nuova query DNS. Il computer client può ricevere l'indirizzo IP di un endpoint diverso a seconda del metodo di bilanciamento del carico applicato e dell'integrità degli endpoint al momento della richiesta.

## Come implementare Gestione traffico

La *Figura 2* mostra i passaggi, in ordine, necessari per l'implementazione di Gestione traffico. Questi passaggi possono essere eseguiti in un ordine leggermente diverso, se si conoscono con precisione la configurazione e le procedure consigliate di Gestione traffico. I numeri nella Figura 2 corrispondono alle seguenti descrizioni numerate:

![Modalità di configurazione di Gestione traffico](./media/traffic-manager-overview/IC740855.jpg)

**Figura 2**

1. **Distribuire i servizi cloud di Azure, siti Web di Azure o altri endpoint nell'ambiente di produzione**. Quando viene creato un profilo di Gestione traffico, questo deve essere associato a una sottoscrizione. Bisogna poi aggiungere gli endpoint per i servizi cloud e i siti Web di livello standard nella produzione che fanno parte della stessa sottoscrizione. Se un endpoint si trova in gestione temporanea e non in un ambiente di produzione di Azure oppure non fa parte della stessa sottoscrizione, può essere aggiunto come endpoint esterno. Per altre informazioni sui servizi cloud, vedere [Servizi cloud](http://go.microsoft.com/fwlink/p/?LinkId=314074). Per altre informazioni sui siti Web, vedere [Siti Web](http://go.microsoft.com/fwlink/p/?LinkId=393327).
2. **Decidere un nome per il dominio di Gestione traffico**. Prendere in considerazione un nome per il dominio con un prefisso univoco. L'ultima parte del dominio, trafficmanager.net, è fissa. Per altre informazioni, vedere [Procedure consigliate](#best-practices).
3. **Decidere il monitoraggio della configurazione da usare**. Gestione traffico monitora gli endpoint per verificare che siano online, indipendentemente dal metodo di routing del traffico. Dopo aver configurato le impostazioni di monitoraggio, in base al sistema di monitoraggio, Gestione traffico non dirigerà il traffico direttamente agli endpoint offline a meno che non rilevi che tutti gli endpoint sono offline o nel caso in cui non sia possibile rilevare lo stato di ciascun endpoint contenuto nel profilo. Per altre informazioni sul monitoraggio, vedere [Monitoraggio di Gestione traffico](traffic-manager-monitoring.md).
4. **Decidere il metodo di routing del traffico che si desidera utilizzare**. Sono disponibili tre diversi metodi di routing del traffico. Decidere accuratamente il metodo più adatto ai propri requisiti. Se necessario, il metodo scelto può essere modificato in qualunque momento. Considerare inoltre che ciascun metodo richiede passaggi di configurazione leggermente differenti. Per informazioni sui metodi di routing del traffico, vedere [metodi di routing del traffico su gestione traffico](traffic-manager-load-balancing-methods.md).
5. **Creare il profilo e configurare le impostazioni**. È possibile usare le API REST, Windows PowerShell o il portale di Azure classico per creare il profilo di Gestione traffico e configurare le impostazioni. Per altre informazioni, vedere [Come configurare le impostazioni di Gestione traffico](#how-to-configure-traffic-manager-settings). I passaggi seguenti presuppongono l'uso di **Creazione rapida** nel portale di Azure classico. 
   - **Creare il profilo di Gestione traffico**: per creare un profilo usando Creazione rapida nel portale di Azure classico, vedere [Gestire i profili di Gestione traffico](traffic-manager-manage-profiles.md).
   - **Configurare impostazioni del metodo di routing del traffico** – nella creazione rapida, è necessario selezionare il metodo di routing del traffico per il profilo. Questa impostazione può essere modificata in qualsiasi momento una volta completati i passaggi di Creazione rapida. Per passaggi di configurazione, vedere l'argomento corrispondente per il metodo di routing del traffico: [Configurare prestazioni metodo di routing del traffico ](traffic-manager-configure-performance-load-balancing.md), [Configurare metodo di routing del traffico failover](traffic-manager-configure-failover-load-balancing.md), [Configurare metodo di routing del traffico Round Robin](traffic-manager-configure-round-robin-load-balancing.md).
   
   >[AZURE.NOTE]Il metodo Round Robin del metodo di routing del traffico supporta ora la distribuzione ponderata del traffico di rete. In questo momento è tuttavia necessario usare le API REST o Windows PowerShell per configurare i pesi. Per altre informazioni e una configurazione di esempio, vedere [Endpoint esterni di Gestione traffico di Azure e metodo Round robin ponderato tramite PowerShell](http://azure.microsoft.com/blog/2014/06/26/azure-traffic-manager-external-endpoints-and-weighted-round-robin-via-powershell/) nel blog di Azure.

   - **Configurare gli endpoint**: gli endpoint non vengono configurati durante la Creazione rapida. Dopo aver creato il profilo e specificato il metodo di routing del traffico, è necessario consentire a gestione traffico conoscere gli endpoint. Per la procedura di configurazione degli endpoint, vedere [Gestire gli endpoint in Gestione traffico](traffic-manager-endpoints.md).

   - **Configurare le impostazioni di monitoraggio**: le impostazioni di monitoraggio non vengono configurate durante la Creazione rapida. Dopo aver creato il profilo e specificato il metodo di routing del traffico, è necessario consentire a gestione traffico di monitoraggio. Per la procedura di configurazione del monitoraggio, vedere [Monitoraggio di Gestione traffico](traffic-manager-monitoring.md).
6. **Testare il profilo di Gestione traffico**. Accertarsi che il profilo e il dominio lavorino come previsto. Per informazioni su come effettuare la verifica, vedere [Test delle impostazioni di Gestione traffico](traffic-manager-testing-settings.md).
7. **Impostare il record di risorse DNS del nome di dominio aziendale in modo che punti al profilo per attivarlo**. Per altre informazioni, vedere [Impostare un dominio Internet aziendale in modo che punti a un dominio di Gestione traffico](traffic-manager-point-internet-domain.md).

Usando l'esempio della Figura 1, è possibile sostituire il record di risorse DNS sui server con il seguente per scegliere il nome di dominio aziendale per il nome di dominio di Gestione traffico: www.contoso.com IN CNAME contoso.trafficmanager.net

## Come configurare le impostazioni di Gestione traffico

È possibile configurare le impostazioni di Gestione traffico usando il portale di Azure classico, le API REST e i cmdlet di Windows PowerShell.

Nonostante nel portale di Azure classico non siano visibili tutti gli elementi dell'API REST, molte impostazioni sono disponibili usando uno dei metodi indicati. Per altre informazioni sull'uso delle API REST, vedere [Operazioni su Gestione traffico (documentazione di riferimento sulle API REST)](http://go.microsoft.com/fwlink/p/?LinkId=313584).

Per altre informazioni sui cmdlet di Windows PowerShell per Gestione traffico, vedere [Cmdlet di Gestione traffico di Azure](http://go.microsoft.com/fwlink/p/?LinkId=400769).

>[AZURE.NOTE]Non sono attualmente disponibili informazioni di supporto per la configurazione degli endpoint esterni (tipo = 'Any'), dei pesi per il metodo di routing del traffico round robin e dei profili annidati con il portale di Azure classico. È necessario utilizzare REST (vedere [Creare la definizione](http://go.microsoft.com/fwlink/p/?LinkId=400772)) o Windows PowerShell (vedere [Add-AzureTrafficManagerEndpoint](https://msdn.microsoft.com/library/azure/dn690257.aspx)).

### Configurazione delle impostazioni nel portale di Azure classico

Nel portale di Azure classico è possibile creare il profilo di Gestione traffico mediante Creazione rapida. Tramite Creazione rapida è possibile creare un profilo di base. Dopo la creazione del profilo, è possibile configurare impostazioni aggiuntive o modificare quelle configurate in precedenza. Per altre informazioni sulla creazione del profilo di Gestione traffico utilizzando la Creazione rapida, vedere [Gestire i profili di Gestione traffico](traffic-manager-manage-profiles.md).

Nel portale di Azure classico è possibile configurare le impostazioni seguenti:

- **Prefisso DNS**: prefisso univoco creato dall'utente. I profili vengono visualizzati nel portale di Azure classico in base al prefisso.
- **TTL DNS**: il valore della durata (TTL) DNS controlla la frequenza con la quale il server dei nomi della cache locale del client eseguirà una query sul sistema DNS di Gestione traffico di Azure per le voci DNS aggiornate.
- **Sottoscrizione**: selezionare la sottoscrizione corrispondente al profilo. Tenere presente che questa opzione viene visualizzata solo se si dispone di più sottoscrizioni.
- **metodo di routing del traffico** : la modalità di routing del traffico di gestione traffico.
- **Ordine di Failover** : quando il metodo di routing, l'ordine degli endpoint tramite il failover del traffico.
- **Monitoraggio**: le impostazioni di monitoraggio contengono il protocollo (HTTP o HTTPS), la porta e il nome file e percorso relativo.

### Configurazione delle impostazioni tramite le API REST

È possibile creare e configurare il profilo di Gestione traffico mediante le API REST. Per altre informazioni, vedere [Operazioni su Gestione traffico (documentazione di riferimento sulle API REST)](http://go.microsoft.com/fwlink/?LinkId=313584).

- **Profilo**: un profilo contiene un prefisso di nome di dominio creato dall'utente. Ogni profilo corrisponde alla sottoscrizione. È possibile creare più profili per sottoscrizione. Il nome del profilo è visibile nel portale di Azure classico. Il nome creato, che è contenuto nel profilo, è definito dominio di Gestione traffico.
- **Definizione**: una definizione contiene le impostazioni dei criteri e di monitoraggio. Una definizione corrisponde a un profilo. È possibile disporre di una sola definizione per profilo. Nonostante molte delle impostazioni contenute nella definizione vengano visualizzate e possano essere configurate nel portale di Azure classico, la definizione non è visibile nel portale di Azure classico.
- **Opzioni DNS**: ciascuna definizione contiene le opzioni DNS. Si tratta del punto in cui viene configurata la durata TTL del DNS.
- **Monitoraggi**: ciascuna definizione contiene le impostazioni di monitoraggio. In questa area vengono configurati il protocollo, la porta, il percorso relativo e il nome del file. Le impostazioni di monitoraggio vengono visualizzate e possono essere configurate nel portale di Azure classico. Per altre informazioni, vedere [Monitoraggio di Gestione traffico](traffic-manager-monitoring.md).
- **Criteri**: ciascuna definizione contiene le impostazioni dei criteri. I criteri sono in cui vengono specificati i metodi di routing del traffico e gli endpoint. Nonostante alcune delle impostazioni dei criteri vengano visualizzate e possano essere configurate nel portale di Azure classico, i criteri non vengono visualizzati nel portale di Azure classico. Per ulteriori informazioni, vedere [metodi di routing del traffico su gestione traffico](traffic-manager-load-balancing-methods.md).

## Configurazione delle impostazioni tramite Windows PowerShell

È possibile creare e configurare il profilo di Gestione traffico mediante Windows PowerShell. Per altre informazioni, vedere [Cmdlet di Gestione traffico di Azure](http://go.microsoft.com/fwlink/p/?LinkId=400769).

## Procedure consigliate

- **Rendere il prefisso univoco e facile da comprendere**: il nome DNS del profilo di Gestione traffico deve essere univoco. È possibile controllare solo la prima parte del nome DNS. Il nome di dominio di Gestione traffico viene usato unicamente a scopo di identificazione e indirizzamento delle richieste del client. Gli utenti finali non potranno mai visualizzare tali nomi nei computer client. Tuttavia, poiché i profili vengono identificati da questo nome di dominio, è importante essere in grado di distinguerlo rapidamente dagli altri nomi di dominio elencati nel portale di Azure classico.
- **Utilizzare i punti per maggiore univocità o per rendere leggibili i nomi di dominio**: è possibile utilizzare i punti anche per separare parti del prefisso del nome di dominio. Se si intende creare più criteri in Gestione traffico, usare una gerarchia coerente per differenziare un servizio da un altro. Ad esempio, Contoso dispone di servizi globali per la gestione di utilità, fatturazione e Web. I tre criteri sono *web.contoso.trafficmanager.net*, *bill.contoso.trafficmanager.net* e *util.contoso.trafficmanager.net*. Quando si configurano i servizi cloud o i siti Web, usare nomi in cui sia incluso il percorso. Ad esempio, *web-us-contoso.cloudapp.net* e *web-asia-contoso.cloudapp.net*. Le limitazioni sono quelle imposte dal servizio DNS. Si supponga che un nome di dominio sia dato da una sequenza di etichette separate da punti (label.label.label.label.ecc.). Attualmente, i limiti relativi ai nomi di dominio di Gestione traffico sono:
   - Ciascuna etichetta non può disporre di più di 63 caratteri.
   - Non possono essere presenti più di 40 etichette in totale. Poiché due etichette provengono da trafficmanager.net, ne rimangono 38 per il prefisso.
   - Il nome di dominio intero non può contenere più di 253 caratteri. Tenere presente che trafficmanager.net occupa 19 di tali caratteri.
- **TTL DNS**: il valore TTL DNS controlla la frequenza con la quale il server dei nomi della cache locale del client eseguirà una query sul sistema DNS di Gestione traffico di Azure per le voci DNS aggiornate. Tutte le modifiche effettuate in Gestione traffico, come le modifiche al profilo o alla disponibilità dell'endpoint, impiegheranno tale periodo di tempo per essere aggiornate tramite il sistema globale dei server DNS. È consigliabile mantenere l'impostazione sul valore predefinito di 300 secondi (5 minuti). Un numero più elevato aumenta il tempo di memorizzazione nella cache delle risposte DNS di Gestione traffico da parte delle risoluzioni e dei client DNS, riducendo la latenza generale della query DNS. Tuttavia, se è necessario un failover molto rapido, è preferibile configurare un valore inferiore.
- **Gli endpoint devono trovarsi in una sottoscrizione singola**: tutti gli endpoint devono trovarsi nella stessa sottoscrizione in cui è stato creato il profilo. È possibile aggiungere a un profilo endpoint da diverse sottoscrizioni come endpoint esterni, ma Azure non li rimuoverà automaticamente se si disabilita o si elimina il servizio associato. Gli endpoint esterni rimangono quindi nel profilo di Gestione traffico e continueranno a essere fatturati, a meno che non li si rimuova manualmente.
- **Solo servizi di produzione**: sono disponibili solo gli endpoint nell'ambiente di produzione. Non è possibile indirizzare endpoint in esecuzione in un ambiente di gestione temporanea. Tenere presente che se si esegue uno scambio dell'indirizzo IP virtuale (VIP) mentre il traffico viene indirizzato tramite un profilo, il traffico userà l'endpoint appena scambiato nell'ambiente di produzione.
- **Denominare gli endpoint in modo che possano essere facilmente identificati**: considerare il prefisso DNS da utilizzare. Il nome DNS viene usato perché è certo che sia univoco in una sottoscrizione, mentre per il nome del servizio cloud o del sito Web non si ha questa garanzia. Per evitare confusione, assegnare a un servizio cloud o a un sito Web un nome e un prefisso DNS uguali o simili. Se si dispone di oltre 20 servizi cloud e siti Web, una denominazione non corretta potrebbe rendere difficoltosa la ricerca dell'endpoint corretto e la gestione dei profili.
- **Tutti gli endpoint in un profilo devono eseguire le stesse operazioni e servire le stesse porte**: se si combinano gli endpoint, è più probabile che un client chiami un endpoint che non è in grado di soddisfare la richiesta.
- **Per tutti i servizi cloud in un profilo devono essere usate le stesse impostazioni di monitoraggio**: è possibile scegliere solo un percorso e un file per monitorare tutti gli endpoint in una definizione specifica. È possibile immettere "/" nella casella di testo **Nome file e percorso relativo** in modo che tramite il monitoraggio venga effettuato il tentativo di accesso al nome file e al percorso predefiniti.
- **Disabilitare gli endpoint per le modifiche temporanee, anziché modificare la configurazione**: in molti casi, è preferibile portare un endpoint offline. Anziché rimuovere l'endpoint dal profilo, disabilitare semplicemente il singolo endpoint nel profilo. Questo fa sì che l'endpoint continui a far parte del profilo, sebbene il comportamento di quest'ultimo potrebbe far sembrare il contrario. Questa operazione è particolarmente utile per rimuovere temporaneamente un endpoint che si trova in modalità di manutenzione o è in corso di ridistribuzione. Quando l'endpoint è di nuovo attivo e in esecuzione, è possibile abilitarlo. Per altre informazioni, vedere [Gestire gli endpoint in Gestione traffico](traffic-manager-endpoints.md).
- **Disabilitare un profilo per le modifiche temporanee, anziché eliminarlo**: potrebbe essere preferibile portare offline l'intero profilo, non solo i singoli endpoint specificati. A tale scopo, disabilitare il profilo. Quando si disabilita un profilo, tutte le impostazioni restano disponibili per la modifica nel portale di Azure classico ed è possibile riportare facilmente e velocemente il profilo online quando lo si vuole usare nuovamente. Per altre informazioni, vedere [Gestire gli endpoint in Gestione traffico](traffic-manager-endpoints.md).
- **Archiviazione**: la progettazione della posizione e della distribuzione dell'archiviazione è un aspetto fondamentale dell'uso di Gestione traffico. Pensare alla transazione end-to-end e alla modalità di scorrimento dei dati quando si progetta e distribuisce l'applicazione per Gestione traffico.
- **SQL Azure**: come per la progettazione dell'archiviazione, analizzare lo stato dell'applicazione e i requisiti dei dati quando si estendono gli endpoint a più aree geografiche.

## Profili annidati

La procedura nota come 'profili annidati' consente di specificare il nome di un altro profilo di Gestione traffico come endpoint. Il nome del profilo di Gestione traffico è il corrispondente nome DNS, come contoso-europe.trafficmanager.net.

Questa procedura consente di configurare Gestione traffico in modo che le query in ingresso del nome DNS siano analizzate in set di livelli per garantire che il client richiedente sia indirizzato al set di endpoint corretto. Nella *Figura 3* viene visualizzato un esempio.

![Esempio di profili di Gestione traffico nidificati](./media/traffic-manager-overview/IC751072.png)

**Figura 3**

È possibile nidificare fino a 10 livelli e ogni profilo può essere configurato con un metodo di routing del traffico diverso.

Ad esempio, si possono creare configurazione per i seguenti elementi:

- Al livello superiore (il profilo di Gestione traffico che viene eseguito il mapping al nome DNS esterno), è possibile configurare il profilo con il metodo di routing del traffico delle prestazioni.
- Livello intermedio, un set di profili di Gestione traffico rappresentano Data Center diversi e utilizzare il metodo di routing round robin del traffico.
- A livello inferiore, un set di endpoint del servizio client in ciascun servizio datacenter richiesto dal traffico dell'utente.

Il risultato è che gli utenti vengono indirizzati a un data center adeguato per l'area geografica in base alle prestazioni e a un servizio cloud all'interno del data center in base a una distribuzione del carico ponderata o equivalente. Ad esempio, la definizione del peso può essere usata per distribuire una ridotta percentuale di traffico a una distribuzione nuova o di prova a scopo di test o di invio di commenti e suggerimenti da parte del cliente.

Nella *Figura 4* viene illustrata la configurazione.

![Esempio di profili di Gestione traffico multilivello](./media/traffic-manager-overview/IC751073.png)

**Figura 4**

Nella *Figura 4*, il profilo di Gestione traffico nel livello superiore è un profilo padre e i profili di Gestione traffico nel livello intermedio sono profili figli.

Se Gestione traffico indirizza gli utenti a un profilo figlio con un numero ridotto di endpoint integri, è possibile che venga eseguito l'overload di tali endpoint e si generino problemi di prestazioni. Per evitare il verificarsi di questa situazione, è possibile configurare il profilo padre di Gestione traffico con una determinata soglia di endpoint integri che determina se qualsiasi endpoint nei profili figlio di tale padre può ricevere traffico. Se ad esempio si desidera garantire che siano presenti almeno tre endpoint integri nei profili figlio, il valore di soglia deve essere impostato su 3. Nell'esempio della Figura 4, è necessario configurare il profilo di Gestione traffico di livello superiore per tale soglia.

Per aggiungere un profilo di Gestione traffico come endpoint e configurare il numero minimo di endpoint integri, è necessario usare REST (vedere [Creare la definizione](http://go.microsoft.com/fwlink/p/?LinkId=400772)) o Windows PowerShell (vedere [Add-AzureTrafficManagerEndpoint](https://msdn.microsoft.com/library/azure/dn690257.aspx)). Non è possibile usare il portale di Azure classico.

## Figure di Gestione traffico

Se si vogliono scaricare le figure presenti in questo argomento come diapositive di PowerPoint da usare per una presentazione su Gestione traffico o da modificare per altri scopi, vedere [Figure di Gestione traffico nella documentazione MSDN](http://gallery.technet.microsoft.com/Traffic-Manager-figures-in-887e7c99).

## Passaggi successivi

[Metodi di routing di Gestione traffico](traffic-manager-routing-methods.md)

[Monitoraggio di Gestione traffico](traffic-manager-monitoring.md)

[Creare un profilo](traffic-manager-manage-profiles.md)

[Cmdlet di Gestione traffico di Azure](http://go.microsoft.com/fwlink/p/?LinkId=400769)

<!---HONumber=AcomDC_1210_2015-->