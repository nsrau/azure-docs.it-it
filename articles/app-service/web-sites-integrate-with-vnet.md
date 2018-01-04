---
title: Integrazione di un'app in una rete virtuale di Azure
description: Illustra come connettere un'app del servizio app di Azure a una rete virtuale di Azure nuova o esistente
services: app-service
documentationcenter: 
author: ccompy
manager: erikre
editor: cephalin
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2017
ms.author: ccompy
ms.openlocfilehash: b755197af7e8791e01273bcc25f72c0d92ef6bc2
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2017
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrare un'app in una rete virtuale di Azure
Questo documento descrive la funzionalità di integrazione del servizio app di Azure in una rete virtuale, specificando come configurarla con le app del [servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714). Se non si ha familiarità con le reti virtuali di Azure, si tratta di una funzionalità che consente di posizionare molte delle risorse di Azure in una rete instradabile non Internet a cui si controlla l'accesso. Queste reti possono quindi essere connesse alle reti locali usando diverse tecnologie VPN. Per altre informazioni sulle reti virtuali di Azure, è possibile iniziare dalla [Panoramica sulla rete virtuale di Azure][VNETOverview]. 

Il servizio app di Azure è disponibile in due forme. 

1. I sistemi multi-tenant che supportano l'intera gamma dei piani tariffari
2. La funzionalità Premium ambiente del servizio app (ASE) che consente la distribuzione nella rete virtuale. 

Questo documento tratta dell'integrazione rete virtuale, ma non dell'ambiente del servizio app. Per altre informazioni sulla funzionalità dell'ambiente del servizio app, iniziare dall'articolo [Introduzione all'ambiente del servizio app][ASEintro].

Integrazione rete virtuale consente all'app Web di accedere alle risorse presenti nella propria rete virtuale. Al contrario, l'accesso privato all'app Web dalla rete virtuale non viene concesso. Per accesso privato s'intende la possibilità di rendere l'app accessibile soltanto da una rete privata, ad esempio all'interno di una rete virtuale di Azure. L'accesso privato al sito è disponibile solo con un ambiente del servizio app configurato con un servizio di bilanciamento del carico interno (ILB). Per informazioni dettagliate in proposito, vedere [Uso di un servizio di bilanciamento del carico interno con un ambiente del servizio app][ILBASE]. 

L'integrazione rete virtuale viene comunemente usata per consentire l'accesso dall'app Web a un database o a un servizio Web in esecuzione in una macchina virtuale nella rete virtuale di Azure. Con Integrazione rete virtuale non è necessario esporre un endpoint pubblico per le applicazioni nella macchina virtuale ma è possibile usare gli indirizzi instradabili non Internet privati. 

La funzionalità Integrazione rete virtuale:

* richiede un piano tariffario Standard, Premium o Isolated 
* funziona con reti virtuali classiche o di Resource Manager 
* supporta TCP e UDP
* funzionamento del servizio Web, con dispositivi mobili, App per le API e App (funzione)
* consente a un'app di connettersi a 1 sola rete virtuale alla volta
* consente di integrare fino a cinque reti virtuali in un piano di servizio app 
* consente l'uso della stessa rete virtuale da parte di più app in un piano di servizio app
* supporta un contratto di servizio pari al 99,9% grazie al contratto di servizio sul gateway di rete virtuale

Tra le operazioni non supportate da Integrazione rete virtuale:

* montaggio di un'unità
* integrazione di AD 
* NetBios
* accesso al sito privato

### <a name="getting-started"></a>Introduzione
Prima di procedere con la connessione della propria app Web a una rete virtuale, è opportuno prendere in considerazione i seguenti aspetti:

* Integrazione rete virtuale funziona solo con le app dei piani tariffari **Standard**, **Premium** o **Isolated**. Se si abilita la funzionalità e successivamente il piano di servizio app viene ridimensionato a un piano tariffario non supportato, le app perdono la connessione alle relative reti virtuali. 
* Se la rete virtuale di destinazione esiste già, deve trattarsi di una VPN da punto a sito con un gateway di routing dinamico per poter eseguire la connessione a un'app. Non è possibile attivare la VPN da punto a sito se il gateway è configurato con il routing statico.
* La rete virtuale deve essere inclusa nella stessa sottoscrizione del piano di servizio app (ASP). 
* Le app che si integrano con una rete virtuale usano il DNS specificato per quella rete virtuale.
* Per impostazione predefinita, le app di integrazione instradano il traffico nella rete virtuale esclusivamente in base alle route definite nella rete virtuale. 

## <a name="enabling-vnet-integration"></a>Abilitazione della funzionalità Integrazione rete virtuale

È possibile connettere l'app a una rete virtuale nuova o a una esistente. Se si crea una nuova rete durante l'integrazione, oltre a creare la rete virtuale, viene preconfigurato un gateway di routing dinamico e viene abilitata una VPN da punto a sito. 

> [!NOTE]
> La configurazione di una nuova integrazione di reti virtuali può richiedere diversi minuti. 
> 
> 

Per abilitare Integrazione rete virtuale, aprire le impostazioni dell'app e selezionare Rete. L'interfaccia utente visualizzata offre tre opzioni di rete. Questa guida tratta solo la funzionalità Integrazione rete virtuale, anche se le connessioni ibride e gli ambienti del servizio app sono descritti più avanti in questo documento. 

Se l'app non è inclusa nel piano tariffario corretto, l'interfaccia utente permette di scegliere un piano tariffario superiore.

![][1]

### <a name="enabling-vnet-integration-with-a-pre-existing-vnet"></a>Abilitazione della funzionalità Integrazione rete virtuale con una rete virtuale preesistente
L'interfaccia utente di Integrazione rete virtuale consente di selezionare da un elenco di reti virtuali. Le reti virtuali classiche sono quelle indicate dalla parola "classica" tra parentesi accanto al nome della rete virtuale. L'elenco è ordinato in modo che le reti virtuali di Resource Manager siano riportate per prime. L'immagine seguente mostra che è possibile selezionare solo una rete virtuale. Una rete virtuale può risultare disattivata per diversi motivi, ad esempio:

* la rete virtuale è in un'altra sottoscrizione a cui l'account ha accesso
* la rete virtuale non è da punto a sito
* la rete virtuale non ha un gateway di routing dinamico

![][2]

Per abilitare l'integrazione è sufficiente fare clic sulla rete virtuale con cui eseguire l'integrazione. Dopo aver selezionato la rete virtuale, l'app verrà riavviata automaticamente per rendere effettive le modifiche. 

##### <a name="enable-point-to-site-in-a-classic-vnet"></a>Abilitare la connessione da punto a sito in una rete virtuale classica
Se la rete virtuale non ha un gateway e non è da punto a sito, è necessario eseguirne prima di tutto la configurazione. Per eseguire questa operazione per una rete virtuale classica, accedere al [portale di Azure][AzurePortal] e visualizzare l'elenco Reti virtuali (classico). Qui fare clic sulla rete con cui eseguire l'integrazione e quindi sulla casella grande Connessioni VPN in Informazioni di base. Da qui è possibile creare la VPN da punto a sito e anche fare in modo che venga creato un gateway. La rete da punto a sito con gateway appena creata sarà pronta dopo circa 30 minuti. 

![][8]

##### <a name="enabling-point-to-site-in-a-resource-manager-vnet"></a>Abilitare la connessione da punto a sito in una rete virtuale di Resource Manager
Per configurare una connessione da punto a sito in una rete virtuale di Resource Manager con un gateway, è possibile usare PowerShell come descritto in [Configurare una connessione da punto a sito a una rete virtuale con PowerShell][V2VNETP2S] o usare il portale di Azure come descritto in [Configurare una connessione da punto a sito a una rete virtuale usando il portale di Azure][V2VNETPortal]. L'interfaccia utente per eseguire questa funzionalità non è ancora disponibile. Si noti che non è necessario creare i certificati per la configurazione del sito del punto. poiché viene configurata automaticamente quando si connette l'app Web alla rete virtuale. 

### <a name="creating-a-pre-configured-vnet"></a>Creazione di una rete virtuale preconfigurata
Se si vuole creare una nuova rete virtuale configurata con un gateway e una connessione da punto a sito, è possibile usare l'interfaccia utente di rete del servizio app, ma solo per una rete virtuale di Resource Manager. Se si vuole creare una rete virtuale classica con un gateway e una connessione da punto a sito, l'operazione deve essere eseguita manualmente tramite l'interfaccia utente di rete. 

Per creare una rete virtuale di Resource Manager tramite l'interfaccia utente dell'integrazione rete virtuale, è sufficiente selezionare **Crea una nuova rete virtuale** e specificare quanto segue:

* Nome della rete virtuale
* Blocco di indirizzi della rete virtuale
* Nome della subnet
* Blocco di indirizzi della subnet
* Blocco di indirizzi del gateway
* Blocco di indirizzi della connessione da punto a sito

Se si vuole che la rete virtuale si connetta alle altre reti è consigliabile evitare di scegliere uno spazio di indirizzi IP che si sovrappone a tali reti. 

> [!NOTE]
> Per creare una rete virtuale di Resource Manager con un gateway sono necessari circa 30 minuti e attualmente la rete virtuale non viene integrata con l'app. Dopo la creazione della rete virtuale con il gateway è necessario tornare all'interfaccia utente di Integrazione rete virtuale dell'app e selezionare la nuova rete virtuale.
> 
> 

![][3]

Le reti virtuali di Azure in genere vengono create all'interno di indirizzi di rete privati. Per impostazione predefinita, la funzionalità Integrazione rete virtuale instrada nella rete virtuale il traffico destinato a tali intervalli di indirizzi IP. Gli intervalli di indirizzi IP privati sono:

* 10.0.0.0/8 - equivalente a 10.0.0.0 - 10.255.255.255
* 172.16.0.0/12 - equivalente a 172.16.0.0 - 172.31.255.255 
* 192.168.0.0/16 - equivalente a 192.168.0.0 - 192.168.255.255

Lo spazio di indirizzi della rete virtuale deve essere specificato usando la notazione CIDR. Se non si ha familiarità con la notazione CIDR, si tratta di un metodo per specificare blocchi di indirizzi usando un indirizzo IP e un numero intero che rappresenta la network mask. Come riferimento rapido, tenere presente che 10.1.0.0/24 equivale a 256 indirizzi e 10.1.0.0/25 equivale a 128 indirizzi. Un indirizzo IPv4 che termina con /32 equivale a 1 indirizzo. 

Le informazioni sul server DNS impostate qui verranno usate per impostare la rete virtuale. Dopo aver creato la rete virtuale è possibile modificare queste informazioni dall'esperienza utente della rete virtuale. Se si modifica il DNS della rete virtuale, è necessario eseguire un'operazione Sincronizza rete.

Quando si crea una rete virtuale classica usando l'interfaccia utente dell'integrazione rete virtuale, questa viene creata nello stesso gruppo di risorse dell'app. 

## <a name="how-the-system-works"></a>Funzionamento del sistema
Questa funzionalità sfrutta la tecnologia VPN da punto a sito per connettere l'app alla rete virtuale. Le app nei Siti Web di Microsoft Azure presentano un'architettura del sistema multi-tenant che preclude il provisioning di un'app direttamente in una rete virtuale come si fa con le macchine virtuali. Grazie al ricorso alla tecnologia da punto a sito, è possibile consentire l'accesso alla rete solo alla macchina virtuale che ospita l'app. L'accesso alla rete è ulteriormente limitato su questi host di app, per consentire alle app di accedere solo alle reti appositamente configurate a questo scopo. 

![][4]

Se non è stato configurato un server DNS con la propria rete virtuale, l'app dovrà usare gli indirizzi IP per trovare la risorsa nella rete virtuale. Quando si usano gli indirizzi IP, il vantaggio principale di questa funzionalità consiste nella possibilità di usare gli indirizzi privati all'interno della rete privata. Se si imposta l'app per l'uso di indirizzi IP pubblici per una delle proprie macchine virtuali, la funzionalità Integrazione rete virtuale non viene usata e le comunicazioni avvengono via Internet.

## <a name="managing-the-vnet-integrations"></a>Gestione delle integrazioni di reti virtuali
La possibilità di connettersi e disconnettersi da una rete virtuale è a livello di app. Le operazioni che possono influenzare l'integrazione di reti virtuali tra più app sono a livello di ASP. Dall'interfaccia utente visualizzata a livello di app è possibile ottenere dettagli sulla rete virtuale. La maggior parte di queste informazioni viene visualizzata anche a livello di ASP. 

![][5]

Dalla pagina Stato funzionalità di rete è possibile vedere se l'app è connessa alla rete virtuale. Se il gateway della rete virtuale è inattivo per qualsiasi motivo, la rete viene visualizzata come non connessa. 

Le informazioni ora disponibili nell'interfaccia utente di integrazione rete virtuale a livello di app sono le stesse fornite a livello di ASP. Nel dettaglio:

* Nome della rete virtuale: questo collegamento apre l'interfaccia utente della rete virtuale di Azure
* Località: indica la località della rete virtuale. È possibile eseguire l'integrazione con una rete virtuale situata in un'altra località.
* Stato dei certificati: qui sono riportati i certificati usati per proteggere la connessione VPN tra l'app e la rete virtuale. Lo stato restituisce il risultato di un test di verifica della sincronizzazione.
* Stato dei gateway: se i gateway sono inattivi per qualsiasi motivo, l'app non può accedere alle risorse nella rete virtuale. 
* Spazio di indirizzi della rete virtuale: lo spazio di indirizzi IP della rete virtuale. 
* Spazio di indirizzi da punto a sito: lo spazio di indirizzi IP da punto a sito della rete virtuale. L'app visualizza le comunicazioni come provenienti da uno degli IP in questo spazio di indirizzi. 
* Spazio di indirizzi da sito a sito: è possibile usare una connessione VPN da sito a sito per connettere la rete virtuale alle risorse locali o ad altre reti virtuali. Se questa impostazione è configurata, gli intervalli IP specificati con la connessione VPN vengono visualizzati qui.
* Server DNS: qui sono elencati gli eventuali server DNS configurati con la rete virtuale.
* Indirizzi IP instradati alla rete virtuale: viene qui visualizzato l'elenco di indirizzi IP per cui è stato specificato il routing nella rete virtuale. 

L'unica operazione possibile nella vista app di Integrazione rete virtuale è la disconnessione dell'app dalla rete virtuale a cui è attualmente connessa. Per eseguire questa operazione, è sufficiente fare clic su Disconnetti nella parte superiore. Questa azione non modifica la rete virtuale. La rete virtuale e la relativa configurazione, inclusi i gateway, rimane invariata. Per eliminare la rete virtuale è necessario eliminare prima le risorse al suo interno, inclusi i gateway. 

La visualizzazione del piano di servizio app consente alcune operazioni aggiuntive ed è anche accessibile diversamente, oltre che dall'app. Per raggiungere l'interfaccia utente della rete ASP, è sufficiente aprire l'interfaccia utente ASP e scorrere verso il basso fino a raggiungere l'elemento denominato Stato funzionalità di rete. L'elemento fornisce alcuni dettagli secondari sull'integrazione di reti virtuali. Facendo clic su questo elemento, viene visualizzata l'interfaccia utente di Stato funzionalità di rete. Selezionando "Fare clic qui per gestire", viene visualizzato un elenco delle integrazioni di reti virtuali nell'ASP.

![][6]

È utile ricordare la località dell'ASP quando si esaminano le località delle reti virtuali di cui si esegue l'integrazione. Quando la rete virtuale è in un'altra località, è molto più probabile che ci siano problemi di latenza. 

La voce relativa alle reti virtuali integrate indica il numero di reti virtuali con cui le app sono integrate in questo ASP rispetto al numero di reti possibile. 

Per visualizzare altri dettagli su ogni rete virtuale, fare clic sulla rete virtuale in questione. Oltre ai dettagli rilevati in precedenza, viene visualizzato anche un elenco delle app nell'ASP che usano la rete virtuale. 

Sono disponibili due azioni principali. La prima è la possibilità di aggiungere route indirizzando il traffico dall'app alla propria rete virtuale. La seconda è la possibilità di sincronizzare i certificati e le informazioni di rete.

![][7]

**Routing**: come indicato in precedenza le route definite in una rete virtuale vengono usate per indirizzare il traffico dall'app alla propria rete virtuale. L'invio di traffico aggiuntivo in uscita da un'app alla rete virtuale è possibile, ma la gestione del traffico da quel punto in poi dipende dalla configurazione della rete virtuale del cliente. 

**Certificati** : lo stato dei certificati riflette un controllo eseguito dal servizio app per verificare la validità dei certificati usati per la connessione VPN. Quando la funzionalità Integrazione rete virtuale è abilitata, alla prima integrazione nella rete virtuale da una qualsiasi app in questo ASP è necessario uno scambio di certificati per garantire la sicurezza della connessione. Con i certificati si ottengono la configurazione DNS, le route e altre informazioni simili che descrivono la rete.
Se vengono modificati i certificati o le informazioni di rete, è necessario eseguire nuovamente la sincronizzazione della rete. **NOTA**: la sincronizzazione della rete provoca una breve interruzione della connettività tra l'app e la rete virtuale. L'app non viene riavviata, ma la perdita di connettività potrebbe causare il funzionamento non corretto del sito. 

## <a name="accessing-on-premises-resources"></a>Accesso alle risorse locali
Uno dei vantaggi offerti dalla funzionalità Integrazione rete virtuale è la possibilità per le app di accedere alle risorse locali direttamente dall'app, se la rete virtuale è connessa alla rete locale con una VPN da sito a sito. A questo scopo, tuttavia, è possibile che sia necessario aggiornare il gateway VPN locale con le route per l'intervallo IP da punto a sito. Quando la connessione VPN da sito a sito viene configurata per la prima volta, gli script usati per la configurazione devono impostare route che includono la VPN da punto a sito. Se la VPN da punto a sito viene aggiunta dopo aver creato la VPN da sito a sito, è necessario aggiornare le route manualmente. Le informazioni dettagliate su come eseguire questa operazione variano in base al gateway e non sono descritte in questo documento. 

> [!NOTE]
> La funzionalità Integrazione rete virtuale non consente l'integrazione di un'app con una rete virtuale dotata di un gateway ExpressRoute. Anche se il gateway ExpressRoute è configurato in [modalità di coesistenza][VPNERCoex], Integrazione rete virtuale non funziona. Se è necessario accedere alle risorse tramite una connessione ExpressRoute, è possibile usare un [ambiente del servizio app][ASE] eseguito nella rete virtuale.
> 
> 

## <a name="pricing-details"></a>Dettagli prezzi
Quando si usa la funzionalità Integrazione rete virtuale è opportuno tenere presente alcune differenze nei prezzi. I 3 costi correlati all'uso della funzionalità sono:

* Requisiti del piano tariffario ASP
* Costi di trasferimento dati
* Costi del gateway VPN

Perché le app possano usare questa funzionalità, devono far parte di un piano di servizio app di livello Premium o Standard. Per altre informazioni sui costi, vedere i [prezzi del servizio app][ASPricing]. 

A causa della modalità di gestione delle VPN da punto a sito, è sempre previsto un addebito per i dati in uscita attraverso la connessione di Integrazione rete virtuale anche se la rete virtuale si trova nello stesso data center. Per conoscere gli addebiti, vedere [Dettagli prezzi dei trasferimenti di dati][DataPricing]. 

L'ultimo elemento è il costo dei gateway di rete virtuale. Se il gateway non è necessario per altri motivi, ad esempio per le reti VPN da sito a sito, viene addebitato il costo dei gateway per il supporto della funzionalità Integrazione rete virtuale. Per informazioni su questi costi, vedere i [prezzi di Gateway VPN][VNETPricing]. 

## <a name="troubleshooting"></a>risoluzione dei problemi
La funzionalità è semplice da configurare, ma possono comunque verificarsi problemi durante l'uso. In caso di problemi di accesso all'endpoint desiderato, sono disponibili varie utilità che permettono di testare la connettività dalla console dell'app. Le console disponibili sono due: la console Kudu e la console raggiungibile nel portale di Azure. Per accedere alla console Kudu dalla propria app, selezionare Strumenti -> Kudu. Questa operazione equivale a visitare [nomesito].scm.azurewebsites.net. Dopo l'apertura, passare alla scheda della console di debug. Per accedere alla console ospitata nel portale di Azure dalla propria app, selezionare Strumenti -> Console. 

#### <a name="tools"></a>Strumenti
Gli strumenti ping, nslookup e tracert non funzionano dalla console a causa di vincoli di sicurezza. Per questo motivo sono stati aggiunti due strumenti separati. Per testare la funzionalità del DNS è stato aggiunto lo strumento nameresolver.exe. La sintassi è:

    nameresolver.exe hostname [optional: DNS Server]

Si può usare nameresolver per controllare i nomi host da cui dipende l'app. In questo modo è possibile verificare se la configurazione del server DNS è corretta e se si ha accesso al server DNS.

Il secondo strumento permette di verificare la connettività TCP a una combinazione di host e porta. Si tratta dello strumento tcpping.exe, la cui sintassi è:

    tcpping.exe hostname [optional: port]

L'utilità tcpping indica se è possibile raggiungere una porta e un host specifici. Essa visualizza il completamento solo se: è presente un'applicazione in ascolto presso la combinazione di host e porta ed è presente un accesso di rete dall'app all'host e alla porta specificati.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Accesso di debug a risorse ospitate su una rete virtuale
L'app potrebbe non riuscire a raggiungere un host e una porta specifici per una serie di motivi. Nella maggior parte dei casi il motivo è uno dei seguenti:

* **L'ostacolo è rappresentato da un firewall**: se è presente un firewall, si verifica il timeout TCP, che in questo caso è di 21 secondi. Usare lo strumento tcpping per testare la connettività. Il timeout TCP può essere dovuto anche ad altri motivi, ma è consigliabile iniziare dal firewall. 
* **Il DNS non è accessibile**: il timeout DNS è di tre secondi per ogni server DNS. Se si dispone di due server DNS, il timeout è di 6 secondi. Usare nameresolver per verificare il funzionamento del DNS. Si noti che non è possibile usare nslookup, perché non usa il DNS con cui è configurata la rete virtuale.
* **Intervallo IP P2S non valido** L'intervallo da punto a sito deve essere compreso tra gli intervalli IP privati RFC 1918 (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255). Se l'intervallo usa indirizzi IP esterni a tali intervalli, allora il processo non funzionerà. 

Se il problema non rientra in nessuno dei tre casi, cercare prima di tutto le cause più semplici. Ad esempio: 

* Il gateway viene visualizzato come attivo nel portale?
* I certificati vengono visualizzati come sincronizzati?
* La configurazione di rete è stata modificata senza poi eseguire una sincronizzazione della rete nell'ASP interessato? 

Se il gateway è inattivo, è necessario riattivarlo. Se i certificati non sono sincronizzati, passare alla visualizzazione ASP dell'integrazione di reti virtuali ed eseguire la sincronizzazione della rete. Se si sospetta che sia stata apportata una modifica alla configurazione di rete virtuale senza la successiva sincronizzazione con gli ASP, passare alla visualizzazione ASP di integrazione rete virtuale ed eseguire la sincronizzazione della rete. Ricordarsi che questa operazione provocherà una breve interruzione della connessione di rete virtuale e del funzionamento delle app. 

Se i quesiti precedenti non permettono di risolvere il problema, è necessario approfondire l'analisi:

* Sono presenti altre app che usano l'integrazione rete virtuale per raggiungere risorse nella stessa rete virtuale? 
* È possibile accedere alla console dell'app e usare tcpping per raggiungere altre risorse nella rete virtuale? 

Se viene soddisfatta una delle condizioni precedenti, l'integrazione di reti virtuali funziona correttamente e il problema è altrove. In tal caso, scoprire perché non è possibile raggiungere una combinazione host:porta può risultare più complesso. Tra le cause possibili:

* È presente un firewall sull'host che impedisce l'accesso alla porta dell'applicazione dall'intervallo IP da punto a sito. Il passaggio tra subnet spesso richiede l'accesso pubblico.
* L'host di destinazione è inattivo.
* L'applicazione è inattiva.
* L'IP o il nome host è errato.
* L'applicazione è in ascolto su una porta diversa da quella prevista. Per verificare ciò, accedere all'host e immettere "netstat -aon" dal prompt dei comandi di cmd. Vengono visualizzati gli ID processo in ascolto e le relative porte. 
* I gruppi di sicurezza di rete sono configurati in modo da impedire l'accesso all'host e alla porta dell'applicazione dall'intervallo IP da punto a sito.

Tenere presente che non si conosce l'IP usato dall'app nell'intervallo IP da punto a sito, quindi è necessario consentire l'accesso all'intero intervallo. 

Di seguito è riportata la procedura di debug aggiuntiva:

* Accedere a un'altra macchina virtuale nella rete virtuale e provare a raggiungere la combinazione host:porta della risorsa da lì. A questo scopo è possibile usare alcune utilità ping TCP o telnet, se necessario. In questo caso si vuole solo determinare se è disponibile la connettività dall'altra macchina virtuale. 
* Visualizzare un'applicazione in un'altra macchina virtuale e testare l'accesso all'host e alla porta dalla console dell'app.

#### <a name="on-premises-resources"></a>Risorse locali ####
Se l'applicazione non riesce a raggiungere le risorse locali, è necessario prima di tutto verificare se è possibile raggiungere una risorsa nella rete virtuale. Se funziona, cercare di raggiungere l'applicazione locale da una macchina virtuale presente nella rete virtuale. A questo scopo è possibile usare telnet o un'utilità ping TCP. Se la macchina virtuale non riesce a raggiungere la risorsa locale, verificare che la connessione VPN da sito a sito funzioni correttamente. Se la connessione funziona, eseguire gli stessi controlli descritti in precedenza, nonché la configurazione e lo stato del gateway locale. 

A questo punto, se la macchina virtuale ospitata nella rete virtuale può raggiungere il sistema locale ma l'app non ci riesce, la causa è probabilmente una delle seguenti:

* le route non sono configurate con gli intervalli di IP da punto a sito nel gateway locale
* i gruppi di sicurezza della rete bloccano l'accesso all'intervallo di IP da punto a sito
* i firewall locali bloccano il traffico proveniente dall'intervallo di IP da punto a sito
* nella rete virtuale è presente una route definita dall'utente (UDR) che impedisce al traffico da punto a sito di raggiungere la rete locale

## <a name="powershell-automation"></a>Automazione di PowerShell

Servizio App è possibile integrare con una rete virtuale di Azure tramite PowerShell. Per uno script ready to run, vedere [Connetti un'app in Azure App Service a una rete virtuale di Azure](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).

## <a name="hybrid-connections-and-app-service-environments"></a>Connessioni ibride e ambienti del servizio App
Sono disponibili tre funzioni che consentono l'accesso alle risorse ospitate su reti virtuali. Sono:

* Integrazione rete virtuale
* connessioni ibride
* Ambienti del servizio app

Per le connessioni ibride è necessario installare nella rete un agente di inoltro denominato Gestione connessione ibrida. Gestione connessione ibrida deve potersi connettere ad Azure e anche all'applicazione. Questa soluzione è particolarmente vantaggiosa se adottata da una rete remota, come la propria rete locale o un'altra rete ospitata sul cloud, poiché non richiede un endpoint accessibile da Internet. Gestione connessione ibrida può essere eseguita solo su sistemi Windows e prevede un massimo di cinque istanze in esecuzione per garantire un'elevata disponibilità. Le connessioni ibride, tuttavia, supportano solo il TCP e ogni endpoint di connessione ibrida deve corrispondere a una combinazione host:porta specifica. 

La funzionalità Ambiente del servizio app consente di eseguire un'istanza dei Siti Web di Microsoft Azure nella propria rete virtuale. In questo modo le app possono accedere alle risorse nella rete virtuale senza eseguire altri passaggi. Tra i vantaggi dell'ambiente del servizio app c'è la possibilità di usare ruoli di lavoro basati su Dv2 con fino a 14 GB di RAM. Un altro vantaggio è la possibilità di ridimensionare il sistema in base alle esigenze. A differenza degli ambienti multi-tenant, in cui la pagina ASP è limitata a 20 istanze, in un ambiente del servizio app è possibile scalare fino a 100 istanze ASP. Uno dei vantaggi dell'ambiente del servizio app rispetto all'integrazione di reti virtuali è che il primo è compatibile con una VPN ExpressRoute. 

Nonostante ci sia una parziale sovrapposizione, nessuna di queste funzionalità può sostituire le altre. In base all'uso che se ne intende fare è possibile stabilire quali funzionalità usare. Ad esempio: 

* Uno sviluppatore che voglia semplicemente eseguire un sito in Azure e consentire l'accesso al database nella workstation locale, può usare Connessioni ibride. 
* Una grande organizzazione che voglia inserire un gran numero di proprietà Web nel cloud pubblico e gestirle nella propria rete, può usare Ambiente del servizio app. 
* Per accedere alle risorse nella rete virtuale con un numero elevato di app ospitate nel servizio app, è possibile usare la funzionalità Integrazione rete virtuale. 

Oltre ai casi d'uso è opportuno tenere conto di alcuni aspetti relativi alla semplicità. Se la rete virtuale è già connessa alla rete locale, uno dei modi più semplici per usare le risorse locali è costituito dall'integrazione della rete virtuale o dall'uso di un ambiente del servizio app. Se invece la rete virtuale non è connessa alla rete locale, è molto più complicato configurare una VPN da sito a sito con la rete virtuale piuttosto che installare Gestione connessione ibrida. 

Oltre a differenze funzionali, vanno considerate le differenze di prezzo. La funzionalità Ambiente del servizio app è un'offerta di servizio Premium che garantisce il massimo delle possibilità di configurazione di rete e altre funzionalità. La funzionalità Integrazione rete virtuale può essere usata con ASP Standard o Premium ed è ideale per l'uso sicuro delle risorse in una rete virtuale dal servizio app multi-tenant. La funzionalità Connessioni ibride dipende attualmente da un account BizTalk che prevede diversi piani tariffari, da quello gratuito a quello meno economico, a seconda della quantità richiesta. Per l'uso su più reti, tuttavia, la scelta ideale è la funzionalità Connessioni ibride, che permette di accedere a risorse in oltre 100 reti separate. 

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
[AzurePortal]: http://portal.azure.com/
[ASPricing]: http://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: http://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: http://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: http://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ASEintro]: environment/intro.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
