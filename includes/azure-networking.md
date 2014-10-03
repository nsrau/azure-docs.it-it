# Rete di Azure

Il modo più semplice per connettersi alle applicazioni e ai dati di Azure consiste nell'utilizzare una connessione Internet normale. Pur essendo semplice, questa soluzione non rappresenta sempre l'approccio migliore. In Azure sono disponibili anche tecnologie per la connessione degli utenti ai data center di Azure. In questo articolo verranno esaminate tali tecnologie.

## Sommario

-   [Rete virtuale di Azure][]
-   [Gestione traffico di Azure][]

<a name="Vnet"></a>

## Rete virtuale di Azure

Azure consente di creare macchine virtuali (VM, Virtual Machine) in esecuzione in data center Microsoft. Si supponga che l'organizzazione desideri utilizzare tali macchine virtuali per l'esecuzione di applicazioni aziendali o altro software che verrà utilizzato dai dipendenti dell'azienda. È possibile che si desideri creare una farm SharePoint nel cloud, ad esempio, oppure eseguire un'applicazione per la gestione del magazzino. Per semplificare l'utilizzo da parte degli utenti, queste applicazioni dovrebbero avere la stessa accessibilità delle applicazioni in esecuzione nel proprio data center.

Per questo tipo di problemi è disponibile una soluzione standard, ovvero la creazione di una rete privata virtuale (VPN, Virtual Private Network). Questa soluzione viene adottata attualmente da organizzazioni di qualsiasi dimensione per collegare, ad esempio, i computer di una succursale al data center principale dell'azienda. Questo stesso approccio può essere utilizzato con le macchine virtuali di Azure, come mostrato nella figura 1.

<a name="Fig1"></a>

![01\_Networking][]

**Figura 1: la rete virtuale di Azure consente di creare una rete virtuale nel cloud connessa al data center locale.**

Come mostrato nella figura, la rete virtuale di Azure consente di creare un limite logico intorno a un gruppo di macchine virtuali, denominato *rete virtuale o VNET*, in un data center di Azure. Consente inoltre di stabilire una connessione IPsec tra tale VNET e la rete locale. È possibile creare le macchine virtuali in una VNET utilizzando Macchine virtuali di Azure, Servizi cloud di Azure o entrambi. In altri termini, può trattarsi di macchine virtuali create usando la tecnologia di infrastruttura distribuita come servizio (IaaS, Infrastructure as a Service) di Azure o la tecnologia PaaS (Platform as a Service).
Qualunque sia la soluzione adottata, per la creazione della connessione IPsec sono necessari un dispositivo gateway VPN, hardware specializzato collegato alla rete locale e il supporto dell'amministratore di rete. Dopo la creazione di questa connessione, le macchine virtuali di Azure in esecuzione nella VNET saranno analoghe a qualsiasi altra parte della rete dell'organizzazione.

Come indicato nella [figura 1][], gli indirizzi IP per le macchine virtuali di Azure vengono allocati dallo stesso spazio degli indirizzi IP utilizzato nella propria rete. In questo scenario, in cui vengono utilizzati indirizzi IP privati, le macchine virtuali nel cloud sono semplicemente un'altra subnet IP. Tali macchine virtuali verranno viste dal software in esecuzione nella rete virtuale come se fossero locali, analogamente alle reti private virtuali tradizionali. È inoltre importante notare che, poiché questa connessione viene stabilita a livello IP, nelle macchine virtuali e fisiche di entrambi i lati è possibile eseguire qualsiasi sistema operativo. Le macchine virtuali di Azure con Windows Server o Linux possono interagire con computer locali in cui è in esecuzione Windows, Linux o un altro sistema. È inoltre possibile utilizzare i principali strumenti di gestione, inclusi System Center e altri strumenti, per gestire le macchine virtuali sul cloud e le applicazioni in esse disponibili.

L'utilizzo di Rete virtuale di Azure è la soluzione ideale in molte situazioni. Come indicato in precedenza, questo approccio consente agli utenti aziendali di accedere in modo più semplice alle applicazioni cloud. Un aspetto importante di tale semplicità di utilizzo è costituito dalla possibilità di rendere le macchine virtuali di Azure parte di un dominio Active Directory locale esistente, in modo da offrire un accesso Single Sign-On alle applicazioni da eseguire. È inoltre possibile creare un dominio Active Directory sul cloud, se lo si desidera, quindi connettere tale dominio alla rete locale.

La creazione di una VNET in un data center di Azure consente in effetti di accedere a un ampio pool di risorse su richiesta. È possibile creare macchine virtuali su richiesta, effettuare il pagamento per tali macchine quando sono in esecuzione, quindi rimuoverle e interrompere il pagamento quando non sono più necessarie. Ciò può risultare utile per gli scenari che richiedono accesso rapido a una macchina preconfigurata, ad esempio nel caso di team che si occupano della creazione di nuovo software. Invece di attendere la configurazione delle risorse necessarie da parte dell'amministratore locale, i membri del team possono creare direttamente tali risorse nel cloud pubblico.

Grazie a Rete virtuale, le macchine virtuali di Azure vengono considerate come computer locali dalle risorse locali. Analogamente, il software in esecuzione nella rete locale viene considerato come locale dalle applicazioni in esecuzione nella VNET di Azure. Si supponga che si desideri spostare un'applicazione locale esistente su Azure, ad esempio, poiché l'utilizzo dal cloud risulta economicamente conveniente. È tuttavia possibile che si sia legalmente vincolati ad archiviare in locale i dati utilizzati dall'applicazione. In tale situazione, l'utilizzo di Rete virtuale consente all'applicazione cloud di visualizzare un sistema database locale come se l'applicazione stessa fosse locale, rendendo possibile un accesso senza errori. Qualsiasi sia lo scenario scelto, il risultato è costante: Azure diventa un'estensione del proprio data center.

<a name="TrafficMngr"></a>

## Gestione traffico di Azure

Si supponga di avere creato correttamente un'applicazione di Azure. L'applicazione viene utilizzata da molti utenti in tutto il mondo. Il successo dell'applicazione è entusiasmante, ma comporta nuovi problemi. È ad esempio probabile che l'applicazione sia in esecuzione in più data center di Azure in paesi diversi. È necessario pertanto indirizzare in modo efficiente il traffico relativo alle richieste degli utenti in tali data center, in modo da offrire sempre un'esperienza ottimale agli utenti.

Gestione traffico di Azure è stato progettato per risolvere questo problema, come illustrato nella figura 2.

<a name="Fig3"></a>

![03\_TrafficManager][]

**Figura 2: Gestione traffico di Azure indirizza in modo efficiente le richieste degli utenti verso le istanze di un'applicazione in esecuzione in diversi data center di Azure.**

L'applicazione utilizzata nell'esempio è in esecuzione in macchine virtuali distribuite in quattro data center, due negli Stati Uniti, uno in Europa e uno in Asia. Si supponga che un utente di Roma desideri accedere all'applicazione. Se si utilizza Gestione traffico, l'accesso viene gestito come indicato di seguito.

Come sempre, il sistema utente verifica il nome DNS dell'applicazione (Passaggio 1). La query viene indirizzata al sistema DNS di Azure (Passaggio 2), che cerca i criteri di Gestione traffico per l'applicazione specifica. Ogni criterio viene creato dal proprietario di ogni applicazione di Azure specifica, tramite un'interfaccia grafica o un'API REST. Indipendentemente dalla modalità di creazione, i criteri consentono di specificare una delle tre opzioni di bilanciamento del carico seguenti:

-   **Prestazioni:** tutte le richieste vengono inviate dal sistema dell'utente al data center che presenta la latenza più bassa.
-   **Failover:** tutte le richieste vengono inviate al data center specificato dall'autore del criterio, purché il data center sia disponibile. In caso contrario, le richieste vengono indirizzate ad altri data center, in base all'ordine di priorità definito dall'autore del criterio.
-   **Round robin:** tutte le richieste vengono distribuite in modo uniforme in tutti i data center in cui l'applicazione è in esecuzione.

Quando è disponibile il criterio corretto, Gestione traffico stabilisce il data center a cui inviare la richiesta in base all'opzione specificata (Passaggio 3). L'ubicazione del data center scelto viene quindi restituita all'utente (Passaggio 4), che accede a tale istanza dell'applicazione (Passaggio 5).

Per eseguire in modo corretto tale procedura, è necessario che Gestione traffico disponga di un'immagine aggiornata delle istanze dell'applicazione in esecuzione in ogni data center. A tale scopo, Gestione traffico effettua periodicamente il ping di ogni copia dell'applicazione tramite HTTP GET, quindi registra l'eventuale ricezione di una risposta. Se un'istanza dell'applicazione non risponde più, Gestione traffico interromperà l'indirizzamento degli utenti a tale istanza, fino alla ripresa delle risposte ai ping.

Le applicazioni di dimensioni e diffusione ridotte non necessitano di Gestione traffico, che risulta tuttavia molto utile per applicazioni di grandi dimensioni e con diffusione globale.

  [Rete virtuale di Azure]: #Vnet
  [Gestione traffico di Azure]: #TrafficMngr
  [01\_Networking]: ./media/azure-networking/Networking_01Networking.png
  [figura 1]: #Fig1
  [03\_TrafficManager]: ./media/azure-networking/Networking_03TrafficManager.png
