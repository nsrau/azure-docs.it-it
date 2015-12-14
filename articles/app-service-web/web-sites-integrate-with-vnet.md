<properties 
	pageTitle="Integrazione di un'app in una rete virtuale di Azure" 
	description="Illustra come connettere un'app del servizio app di Azure a una rete virtuale di Azure nuova o esistente" 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="wpickett" 
	editor="cephalin"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/18/2015" 
	ms.author="ccompy"/>

# Integrare un'app in una rete virtuale di Azure #

Questo documento descrive la funzionalità di integrazione del servizio app di Azure in una rete virtuale, specificando come configurarla con le app del [servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714). Se non si ha familiarità con le reti virtuali di Azure, si tratta di una funzionalità che consente di posizionare molte delle risorse di Azure in una rete instradabile non Internet a cui si controlla l'accesso. Queste reti possono quindi essere connesse alle reti locali con diverse tecnologie VPN. Per altre informazioni sulle reti virtuali di Azure, è possibile iniziare dalla [Panoramica sulla rete virtuale di Azure][VNETOverview].

Il servizio app di Azure è disponibile in due forme. La prima, che è anche la più comune, è costituita da sistemi multi-tenant che supportano l'intera gamma dei piani tariffari. La seconda è costituita dalla funzionalità Premium ambiente del servizio app (ASE) che consente la distribuzione in una rete virtuale del cliente. Con un ambiente del servizio app in genere non è necessario usare l'integrazione di reti virtuali, perché il sistema si trova già nella rete virtuale e ha accesso a tutte le risorse in quella rete. L'unico motivo per usare la funzionalità Integrazione rete virtuale con un ambiente del servizio app potrebbe essere la necessità di accedere a risorse in un'altra rete virtuale non connessa a quella che ospita l'ambiente del servizio app.

Integrazione rete virtuale consente all'app Web di accedere alle risorse presenti nella propria rete virtuale. Al contrario, l'accesso privato all'app Web dalla rete virtuale non viene concesso. Questa funzionalità viene comunemente usata per consentire all'app Web l'accesso a un database o a servizi Web in esecuzione in una macchina virtuale nella rete virtuale di Azure. Con Integrazione rete virtuale non è necessario esporre un endpoint pubblico per le applicazioni nella macchina virtuale ma è possibile usare gli indirizzi instradabili non Internet privati.

La funzionalità Integrazione rete virtuale:

- richiede un piano tariffario Premium o Standard 
- attualmente funziona solo con reti virtuali V1 o classiche 
- supporta TCP e UDP
- funziona con le app Web, le app per dispositivi mobili e le app per le API
- consente a un'app di connettersi a 1 sola rete virtuale alla volta
- consente di integrare fino a 5 reti virtuali in un piano di servizio app 
- consente l'uso della stessa rete virtuale da parte di più app in un piano di servizio app
- supporta un contratto di servizio pari al 99,9% grazie alla dipendenza dal gateway di rete virtuale

Tra le operazioni non supportate da Integrazione rete virtuale:

- montaggio di un'unità
- integrazione di AD 
- NetBios
- accesso al sito privato

### Introduzione ###
Prima di procedere con la connessione della propria app Web a una rete virtuale, è opportuno prendere in considerazione i seguenti aspetti:

- Integrazione rete virtuale funziona solo con le app dei piani tariffari **Standard** o **Premium**. Se si abilita la funzionalità e successivamente il piano di servizio app viene ridimensionato a un piano tariffario non supportato, le app perdono la connessione alle relative reti virtuali.  
- Se la rete virtuale di destinazione esiste già, deve trattarsi di una VPN da punto a sito con un gateway di routing dinamico per poter eseguire la connessione a un'app. Non è possibile attivare la VPN da punto a sito se il gateway è configurato con il routing statico.
- La rete virtuale deve essere inclusa nella stessa sottoscrizione del piano di servizio app (ASP).  
- Le app che si integrano con una rete virtuale usano il DNS specificato per quella rete virtuale.
- Per impostazione predefinita, le app di integrazione instradano il traffico nella rete virtuale esclusivamente in base alle route definite nella rete virtuale.  


## Abilitazione della funzionalità Integrazione rete virtuale ##

È possibile connettersi a una rete virtuale nuova o a una esistente. Se si crea una nuova rete oltre a creare la rete virtuale, viene preconfigurato un gateway di routing dinamico e viene abilitata una VPN da punto a sito.

>[AZURE.NOTE]La configurazione di una nuova integrazione di reti virtuali può richiedere diversi minuti.

Per abilitare Integrazione rete virtuale, aprire le impostazioni dell'app e selezionare Rete. L'interfaccia utente visualizzata offre tre opzioni di rete. Questa guida tratta solo la funzionalità Integrazione rete virtuale, anche se le connessioni ibride e gli ambienti del servizio app sono descritti più avanti in questo documento.

Se l'app non è inclusa nel piano tariffario corretto, l'interfaccia utente permette di scegliere un piano tariffario superiore.


![][1]
 
###Abilitazione della funzionalità Integrazione rete virtuale con una rete virtuale preesistente###
L'interfaccia utente di Integrazione rete virtuale consente di selezionare da un elenco di reti virtuali V1. L'immagine seguente mostra che è possibile selezionare solo una rete virtuale. Una rete virtuale può risultare disattivata per diversi motivi, ad esempio:

- la rete virtuale è in un'altra sottoscrizione a cui l'account ha accesso
- la rete virtuale non è da punto a sito
- la rete virtuale non ha un gateway di routing dinamico

Si noti anche che le reti virtuali V2 non sono presenti nell'elenco perché l'integrazione con queste reti non è ancora supportata.

![][2]

Per abilitare l'integrazione è sufficiente fare clic sulla rete virtuale con cui eseguire l'integrazione. Dopo aver selezionato la rete virtuale, l'app verrà riavviata automaticamente per rendere effettive le modifiche.

Se la rete virtuale non ha un gateway e non è da punto a sito, è necessario eseguirne prima di tutto la configurazione. A questo scopo, passare al portale di Azure e visualizzare l'elenco Reti virtuali (classico). Qui fare clic sulla rete con cui eseguire l'integrazione e quindi sulla casella grande Connessioni VPN in Informazioni di base. Da qui è possibile creare la VPN da punto a sito e anche fare in modo che venga creato un gateway. La rete da punto a sito con gateway appena creata sarà pronta dopo circa 30 minuti.

![][8]

### Creazione e integrazione di una rete virtuale ###
Nella creazione di una nuova rete virtuale occorre tenere presente che attualmente è possibile creare solo una rete virtuale V1 o classica. Per creare una rete virtuale V1 usando l'interfaccia utente di Integrazione rete virtuale, è sufficiente selezionare Crea una nuova rete virtuale e quindi specificare il nome della rete virtuale e il relativo spazio di indirizzi.

Per permettere alla rete virtuale di connettersi alle altre reti è consigliabile evitare di scegliere uno spazio di indirizzi IP che si sovrappone a tali reti.

>[AZURE.NOTE]La creazione di una nuova rete virtuale completa di gateway operativi può richiedere fino a 30 minuti. Al termine, l'interfaccia utente verrà aggiornata.

![][3]

Le reti virtuali di Azure in genere vengono create all'interno di indirizzi di rete privati. Per impostazione predefinita, la funzionalità Integrazione rete virtuale instrada nella rete virtuale il traffico destinato a tali intervalli di indirizzi IP. Gli intervalli di indirizzi IP privati sono:

- 10\.0.0.0/8 - equivalente a 10.0.0.0 - 10.255.255.255
- 172\.16.0.0/12 - equivalente a 172.16.0.0 - 172.31.255.255 
- 192\.168.0.0/16 - equivalente a 192.168.0.0 - 192.168.255.255
 
Lo spazio di indirizzi della rete virtuale deve essere specificato usando la notazione CIDR. Se non si ha familiarità con la notazione CIDR, si tratta di un metodo per specificare blocchi di indirizzi usando un indirizzo IP e un numero intero che rappresenta la network mask. Come riferimento rapido, tenere presente che 10.1.0.0/24 equivale a 256 indirizzi e 10.1.0.0/25 equivale a 128 indirizzi. Un indirizzo IPv4 che termina con /32 equivale a 1 indirizzo.

Le informazioni sul server DNS impostate qui verranno usate per impostare la rete virtuale. Dopo aver creato la rete virtuale è possibile modificare queste informazioni dall'esperienza utente della rete virtuale.

Quando si crea una rete virtuale V1 usando l'interfaccia utente di Integrazione rete virtuale, questa viene creata nello stesso gruppo di risorse dell'app.

## Funzionamento del sistema ##
Questa funzionalità sfrutta la tecnologia VPN da punto a sito per connettere l'app alla rete virtuale. Le app nel servizio app di Azure presentano un'architettura del sistema multi-tenant che preclude il provisioning di un'app direttamente in una rete virtuale come si fa con le macchine virtuali. Grazie al ricorso alla tecnologia da punto a sito, è possibile consentire l'accesso alla rete solo alla macchina virtuale che ospita l'app. L'accesso alla rete è ulteriormente limitato su questi host di app, per consentire alle app di accedere solo alle reti appositamente configurate a questo scopo.

![][4]
 
Se non è stato configurato un server DNS con la propria rete virtuale, è necessario usare gli indirizzi IP. Quando si usano gli indirizzi IP, il vantaggio principale di questa funzionalità consiste nella possibilità di usare gli indirizzi privati all'interno della rete privata. Se si imposta l'app per l'uso di indirizzi IP pubblici per una delle proprie macchine virtuali, la funzionalità Integrazione rete virtuale non viene usata e le comunicazioni avvengono via Internet.



##Gestione delle integrazioni di reti virtuali##

La possibilità di connettersi e disconnettersi da una rete virtuale è a livello di app. Le operazioni che possono influenzare l'integrazione di reti virtuali tra più app sono a livello di ASP. Dall'interfaccia utente visualizzata a livello di app è possibile ottenere dettagli sulla rete virtuale. La maggior parte di queste informazioni viene visualizzata anche a livello di ASP.

![][5]

Dalla pagina Stato funzionalità di rete è possibile vedere se l'app è connessa alla rete virtuale. Se il gateway della rete virtuale è inattivo per qualsiasi motivo, la rete viene visualizzata come non connessa.

Le informazioni ora disponibili nell'interfaccia utente di Integrazione rete virtuale a livello di app sono le stesse fornite a livello di ASP. Nel dettaglio:

- Nome della rete virtuale: questo collegamento apre l'interfaccia utente di rete.
- Località: indica la località della rete virtuale. È possibile eseguire l'integrazione con una rete virtuale situata in un'altra località.
- Stato dei certificati: qui sono riportati i certificati usati per proteggere la connessione VPN tra l'app e la rete virtuale. Lo stato restituisce il risultato di un test di verifica della sincronizzazione.
- Stato dei gateway: se i gateway sono inattivi per qualsiasi motivo, l'app non può accedere alle risorse nella rete virtuale.  
- Spazio di indirizzi della rete virtuale: lo spazio di indirizzi IP della rete virtuale.  
- Spazio di indirizzi da punto a sito: lo spazio di indirizzi IP da punto a sito della rete virtuale. L'app visualizza le comunicazioni come provenienti da uno degli IP in questo spazio di indirizzi.  
- Spazio di indirizzi da sito a sito: è possibile usare una VPN da sito a sito per connettere la rete virtuale alle risorse locali o ad altre reti virtuali. Se questa impostazione è configurata, gli intervalli IP specificati con la connessione VPN vengono visualizzati qui.
- Server DNS: qui sono elencati gli eventuali server DNS configurati con la rete virtuale.
- Indirizzi IP instradati alla rete virtuale: l'elenco di indirizzi IP per cui è stato specificato il routing nella rete virtuale viene visualizzato qui.  

L'unica operazione possibile nella vista app di Integrazione rete virtuale è la disconnessione dell'app dalla rete virtuale a cui è attualmente connessa. Per eseguire questa operazione, è sufficiente fare clic su Disconnetti nella parte superiore. Questa azione non modifica la rete virtuale. La rete virtuale e la relativa configurazione, inclusi i gateway, rimane invariata. Per eliminare la rete virtuale è necessario eliminare prima le risorse al suo interno, inclusi i gateway.

La visualizzazione del piano di servizio app consente alcune operazioni aggiuntive ed è anche accessibile diversamente, oltre che dall'app. Per raggiungere l'interfaccia utente della rete ASP, è sufficiente aprire l'interfaccia utente ASP e scorrere verso il basso fino a raggiungere l'elemento denominato Stato funzionalità di rete. L'elemento fornisce alcuni dettagli secondari sull'integrazione di reti virtuali. Facendo clic su questo elemento, viene visualizzata l'interfaccia utente di Stato funzionalità di rete. Selezionando "Fare clic qui per gestire", viene visualizzato un elenco delle integrazioni di reti virtuali nell'ASP.

![][6]

È utile ricordare la località dell'ASP quando si esaminano le località delle reti virtuali di cui si esegue l'integrazione. Quando la rete virtuale è in un'altra località, è molto più probabile che ci siano problemi di latenza.

La voce relativa alle reti virtuali integrate indica il numero di reti virtuali con cui le app sono integrate in questo ASP rispetto al numero di reti possibile.

Per visualizzare altri dettagli su ogni rete virtuale, fare clic sulla rete virtuale in questione. Oltre ai dettagli rilevati in precedenza, viene visualizzato anche un elenco delle app nell'ASP che usano la rete virtuale.

Sono disponibili due azioni principali. La prima è la possibilità di aggiungere route indirizzando il traffico dall'app alla propria rete virtuale. La seconda è la possibilità di sincronizzare i certificati e le informazioni di rete.

![][7]

**Routing**: come indicato in precedenza le route definite in una rete virtuale vengono usate per indirizzare il traffico dall'app alla propria rete virtuale. L'invio di traffico aggiuntivo in uscita da un'app alla rete virtuale è possibile, ma la gestione del traffico da quel punto in poi dipende dalla configurazione della rete virtuale del cliente.

**Certificati**: lo stato dei certificati riflette un controllo eseguito dal servizio app per verificare la validità dei certificati usati per la connessione VPN. Quando la funzionalità Integrazione rete virtuale è abilitata, alla prima integrazione nella rete virtuale da una qualsiasi app in questo ASP è necessario uno scambio di certificati per garantire la sicurezza della connessione. Con i certificati si ottengono la configurazione DNS, le route e altre informazioni simili che descrivono la rete. Se vengono modificati i certificati o le informazioni di rete, è necessario eseguire nuovamente la sincronizzazione della rete. **NOTA**: la sincronizzazione della rete provoca una breve interruzione della connettività tra l'app e la rete virtuale. L'app non viene riavviata, ma la perdita di connettività potrebbe causare il funzionamento non corretto del sito.

##Accesso alle risorse in sede##

Uno dei vantaggi della funzionalità Integrazione rete virtuale è la possibilità per le app di accedere alle risorse locali dall'app, se la rete virtuale è connessa alla rete locale con una VPN da sito a sito. Perché ciò sia possibile, potrebbe essere necessario aggiornare il gateway VPN locale con le route per l'intervallo IP da punto a sito. Quando la connessione VPN da sito a sito viene configurata per la prima volta, gli script usati per la configurazione devono impostare route che includono la VPN da punto a sito. Se la VPN da punto a sito viene aggiunta dopo aver creato la VPN da sito a sito, è necessario aggiornare le route manualmente. Le informazioni dettagliate su come eseguire questa operazione variano in base al gateway e non sono descritte in questo documento.

>[AZURE.NOTE]La funzionalità Integrazione rete virtuale permette di accedere alle risorse locali con una VPN da sito a sito. Attualmente non è possibile fare lo stesso con una VPN ExpressRoute.

##Dettagli prezzi##
Quando si usa la funzionalità Integrazione rete virtuale è opportuno tenere presente alcune differenze nei prezzi. I 3 costi correlati all'uso della funzionalità sono:

- Requisiti del piano tariffario ASP
- Costi di trasferimento dati
- Costi del gateway VPN

Perché le app possano usare questa funzionalità, devono far parte di un piano di servizio app di livello Premium o Standard. Per altre informazioni sui costi, vedere [Prezzi di Servizio app][ASPricing].

A causa della modalità di gestione delle VPN da punto a sito, è sempre previsto un addebito per i dati in uscita attraverso la connessione di Integrazione rete virtuale anche se la rete virtuale si trova nello stesso data center. Per conoscere gli addebiti, vedere [Dettagli prezzi dei trasferimenti di dati][DataPricing].

L'ultimo elemento è il costo dei gateway di rete virtuale. Se il gateway non è necessario per altri motivi, ad esempio per le reti VPN da sito a sito, viene addebitato il costo dei gateway per il supporto della funzionalità Integrazione rete virtuale. Per informazioni su questi costi, vedere [Prezzi di Gateway VPN][VNETPricing].

##Risoluzione dei problemi##

La funzionalità è semplice da configurare, ma possono comunque verificarsi problemi durante l'uso. In caso di problemi di accesso all'endpoint desiderato, sono disponibili varie utilità che permettono di testare la connettività dalla console dell'app. Le console disponibili sono due: la console Kudu e la console raggiungibile nel portale di Azure. Per accedere alla console Kudu dalla propria app, selezionare Strumenti -> Kudu. Questa operazione equivale a visitare [nomesito].scm.azurewebsites.net. Dopo l'apertura, passare alla scheda della console di debug. Per accedere alla console ospitata nel portale di Azure dalla propria app, selezionare Strumenti -> Console.


####Strumenti####

Gli strumenti ping, nslookup e tracert non funzionano dalla console a causa di vincoli di sicurezza. Per questo motivo sono stati aggiunti due strumenti separati. Per testare la funzionalità del DNS è stato aggiunto lo strumento nameresolver.exe. La sintassi è:

    nameresolver.exe hostname [optional: DNS Server]

Si può usare nameresolver per controllare i nomi host da cui dipende l'app. In questo modo è possibile verificare se la configurazione del server DNS è corretta e se si ha accesso al server DNS.

Il secondo strumento permette di verificare la connettività TCP a una combinazione di host e porta. Si tratta dello strumento tcpping.exe, la cui sintassi è:

    tcpping.exe hostname [optional: port]

Lo strumento indica se è possibile raggiungere un host e una porta specifici ma non equivale all'utilità ping basata su ICMP. L'utilità ping ICMP permette di verificare se l'host è attivo. tcpping verifica invece se è possibile accedere a una porta specifica in un host.


####Accesso di debug a risorse ospitate su una rete virtuale####

L'app potrebbe non riuscire a raggiungere un host e una porta specifici per una serie di motivi. Per analizzare il problema è possibile iniziare dai quesiti più semplici, ad esempio:

- Il gateway viene visualizzato come attivo nel portale?
- I certificati vengono visualizzati come sincronizzati?
- La configurazione di rete è stata modificata senza poi eseguire una sincronizzazione della rete nell'ASP interessato?

Se il gateway è inattivo, è necessario riattivarlo. Se i certificati non sono sincronizzati, passare alla visualizzazione ASP dell'integrazione di reti virtuali ed eseguire la sincronizzazione della rete. Se si sospetta che sia stata apportata una modifica alla configurazione di rete virtuale senza la successiva sincronizzazione con gli ASP, passare alla visualizzazione ASP dell'integrazione di reti virtuali ed eseguire la sincronizzazione della rete. Ricordarsi che questa operazione provocherà una breve interruzione della connessione di rete virtuale e del funzionamento delle app.

Se i quesiti precedenti non permettono di risolvere il problema, è necessario approfondire l'analisi:

- Sono presenti altre app che riescono a usare questa rete virtuale per raggiungere una risorsa remota? 
- È possibile accedere alla console dell'app e usare tcpping per raggiungere le risorse nella rete virtuale?  

Se viene soddisfatta una delle condizioni precedenti, l'integrazione di reti virtuali funziona correttamente e il problema è altrove. Potrebbe non essere possibile rispondere ai due quesiti precedenti perché non sono presenti altri elementi nella propria rete virtuale. In tal caso, scoprire perché non è possibile raggiungere una combinazione host:porta può risultare più complesso. Tra le cause possibili:

- L'host di destinazione è inattivo.
- L'applicazione è inattiva.
- L'IP o il nome host è errato.
- L'applicazione è in ascolto su una porta diversa da quella prevista. Per verificare ciò, accedere all'host e immettere "netstat -aon" dal prompt dei comandi di cmd. Verranno visualizzati gli ID processo in ascolto e le relative porte.  
- È presente un firewall sull'host che impedisce l'accesso alla porta dell'applicazione dall'intervallo IP da punto a sito.
- I gruppi di sicurezza di rete sono configurati in modo da impedire l'accesso all'host e alla porta dell'applicazione dall'intervallo IP da punto a sito.

Tenere presente che non si conosce l'IP usato dall'app nell'intervallo IP da punto a sito, quindi è necessario consentire l'accesso all'intero intervallo.

Di seguito è riportata la procedura di debug aggiuntiva:

- Accedere a un'altra macchina virtuale nella rete virtuale e provare a raggiungere la combinazione host:porta della risorsa da lì. A questo scopo è possibile usare alcune utilità ping TCP o telnet, se necessario. In questo caso si vuole solo determinare se è disponibile la connettività dall'altra macchina virtuale. 
- Visualizzare un'applicazione in un'altra macchina virtuale e testare l'accesso all'host e alla porta dalla console dell'app.  

####Risorse locali####
Se non si riesce a raggiungere le risorse locali, è necessario prima di tutto verificare se è possibile raggiungere una risorsa nella rete virtuale. Se ciò è possibile, i passaggi successivi sono piuttosto semplici. Cercare di raggiungere l'applicazione locale da una macchina virtuale nella rete virtuale. A questo scopo è possibile usare telnet o un'utilità ping TCP. Se la macchina virtuale non riesce a raggiungere la risorsa locale, verificare il funzionamento della connessione VPN da sito a sito. Se questa funziona, controllare quanto indicato in precedenza nonché la configurazione e lo stato del gateway locale.

A questo punto, se la macchina virtuale ospitata nella rete virtuale riesce a raggiungere il sistema locale e l'app non ci riesce, i motivi possibili sono: le route non sono configurate con l'intervallo IP da punto a sito all'interno del gateway locale; i gruppi di sicurezza di rete bloccano l'accesso per l'intervallo IP da punto a sito; i firewall locali bloccano il traffico dall'intervallo IP da punto a sito; nella rete virtuale è presente una route definita dall'utente che impedisce al traffico da punto a sito di raggiungere la rete locale.

## Connessioni ibride e ambienti del servizio App##
Sono disponibili 3 funzioni che consentono l'accesso alle risorse ospitate su reti virtuali. Vale a dire:

- Integrazione rete virtuale
- Connessioni ibride
- Ambienti del servizio app

Per le connessioni ibride è necessario installare nella rete un agente di inoltro denominato Gestione connessione ibrida. Gestione connessione ibrida deve potersi connettere ad Azure e anche all'applicazione. Questa soluzione è particolarmente utile da una rete remota, ad esempio la rete locale o perfino un'altra rete ospitata su cloud, perché non richiede un endpoint accessibile da Internet. Gestione connessione ibrida può essere eseguita solo su sistemi Windows e prevede un massimo di 5 istanze in esecuzione per garantire un'elevata disponibilità. Le connessioni ibride, tuttavia, supportano solo il TCP e ogni endpoint di connessione ibrida deve corrispondere a una combinazione host:porta specifica.

La funzionalità Ambiente del servizio app consente di eseguire un'istanza del servizio app di Azure nella propria rete virtuale. In questo modo le app possono accedere alle risorse nella rete virtuale senza eseguire altri passaggi. Tra i vantaggi dell'ambiente del servizio app c'è la possibilità di usare 8 processi di lavoro dedicati alla memoria centrale con 14 GB di RAM. Un altro vantaggio è la possibilità di ridimensionare il sistema in base alle esigenze. A differenza degli ambienti multi-tenant, in cui l'ASP ha dimensioni limitate, in un ambiente del servizio app è possibile controllare il numero di risorse da assegnare al sistema. Per quanto riguarda l'argomento di questo documento, uno dei vantaggi dell'ambiente del servizio app rispetto all'integrazione di reti virtuali è che il primo è compatibile con una VPN ExpressRoute.

Nonostante ci sia una parziale sovrapposizione, nessuna di queste funzionalità può sostituire le altre. In base alle esigenze e all'uso che se ne intende fare è possibile stabilire quali funzionalità usare. Ad esempio:

- Uno sviluppatore che voglia semplicemente eseguire un sito in Azure e consentire l'accesso al database nella workstation locale, può usare Connessioni ibride.  
- Una grande organizzazione che voglia inserire un gran numero di proprietà Web nel cloud pubblico e gestirle nella propria rete, può usare Ambiente del servizio app.  
- Per accedere alle risorse nella rete virtuale con un numero elevato di app ospitate nel servizio app, è possibile usare la funzionalità Integrazione rete virtuale.  

Oltre ai casi d'uso è opportuno tenere conto di alcuni aspetti relativi alla semplicità. Se la rete virtuale è già connessa alla rete locale, l'integrazione di reti virtuali o un ambiente del servizio app rappresenta un modo semplice per utilizzare le risorse locali. Se invece la rete virtuale non è connessa alla rete locale, è molto più complicato configurare una VPN da sito a sito con la rete virtuale che installare Gestione connessione ibrida.

Oltre a differenze funzionali, vanno considerate le differenze di prezzo. La funzionalità Ambiente del servizio app è un'offerta di servizio Premium che garantisce il massimo delle possibilità di configurazione di rete e altre funzionalità. La funzionalità Integrazione rete virtuale può essere usata con ASP Standard o Premium ed è ideale per l'utilizzo sicuro delle risorse in una rete virtuale dal servizio App multi-tenant. La funzionalità Connessioni ibride dipende attualmente da un account BizTalk che prevede diversi piani tariffari, da quello gratuito a quello meno economico, a seconda della quantità richiesta. Per l'uso su più reti, tuttavia, la scelta ideale è la funzionalità Connessioni ibride, che permette di accedere a risorse in oltre 100 reti separate.


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-upgradeplan.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-existingvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-createvnet.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-howitworks.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-appmanage.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-aspmanage.png
[7]: ./media/web-sites-integrate-with-vnet/vnetint-aspmanagedetail.png
[8]: ./media/web-sites-integrate-with-vnet/vnetint-vnetp2s.png

<!--Links-->
[VNETOverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-overview/
[ASPricing]: http://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: http://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: http://azure.microsoft.com/pricing/details/data-transfers/

<!---HONumber=AcomDC_1203_2015-->