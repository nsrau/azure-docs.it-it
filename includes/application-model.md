# Calcolo

Azure consente di distribuire ed eseguire il monitoraggio del codice dell'applicazione
in esecuzione in un data center Microsoft. Quando si crea un'applicazione
e la si esegue in Azure, la combinazione del codice e della configurazione viene definita
servizio ospitato di Azure. I servizi ospitati sono facili da
gestire, aumentare e ridurre, riconfigurare e aggiornare con le nuove versioni del
codice dell'applicazione. Questo articolo descrive principalmente il
modello applicativo del servizio ospitato di Azure.<a id="compare" name="compare"></a>

## Sommario<a id="_GoBack" name="_GoBack"></a>

-   [Vantaggi del modello applicativo di Azure][]
-   [Concetti principali relativi al servizio ospitato][]
-   [Considerazioni sulla progettazione del servizio ospitato][]
-   [Progettazione dell'applicazione per la scalabilità][]
-   [Definizione e configurazione del servizio ospitato][]
-   [File di definizione del servizio][]
-   [File di configurazione del servizio][]
-   [Creazione e distribuzione di un servizio ospitato][]
-   [Riferimenti][]

## <a id="benefits"> </a>Vantaggi del modello applicativo di Azure

Quando si distribuisce l'applicazione come servizio ospitato, in Azure
vengono create una o più macchine virtuali (VM) che contengono il 
codice dell'applicazione. Queste macchine virtuali vengono avviate nei computer fisici che risiedono in
uno dei data center di Azure. Un servizio di bilanciamento del carico provvede a distribuire in modo uniforme alle macchine virtuali le richieste
client all'applicazione
ospitata non appena arrivano al data center. Un'applicazione ospitata in 
Azure può usufruire di tre vantaggi principali:

-   **Disponibilità elevata.** Azure assicura infatti che
 l'applicazione sia eseguita il più possibile senza interruzioni e sia quindi in grado di
 rispondere alle richieste client. L'eventuale arresto dell'applicazione, ad esempio a causa di
 un'eccezione non gestita, viene rilevato da Azure
 e l'applicazione viene riavviata automaticamente. Azure rileva inoltre i casi in cui nel
computer che esegue l'applicazione si verifica un
 errore hardware e
 crea automaticamente una nuova macchina virtuale, da cui eseguire il codice, in un altro
 computer fisico funzionante. NOTA: per garantire il 99,95% di disponibilità dell'applicazione previsto dal
 contratto di servizio Microsoft, è necessario
 avere almeno due macchine virtuali che eseguono il codice dell'applicazione. In questo modo
 una macchina virtuale potrà elaborare le richieste client mentre il codice viene
 spostato da una macchina virtuale malfunzionante a una nuova funzionante.

-   **Scalabilità.** Azure consente di modificare in modo semplice e dinamico
 il numero di macchine virtuali che eseguono il codice dell'applicazione per gestire il
 carico effettivo imposto all'applicazione. In questo modo è possibile
 adattare l'applicazione al carico di lavoro dei clienti,
 che potranno pagare solo per le macchine virtuali necessarie quando vengono effettivamente
 usate. Quando si vuole modificare il numero di macchine virtuali, la risposta di Azure
 perviene entro pochi minuti ed è quindi possibile adeguare dinamicamente il
 numero di macchine virtuali in esecuzione in qualsiasi momento.

-   **Gestibilità.** Poiché Azure è una piattaforma distribuita come servizio
 (PaaS), gestisce l'infrastruttura (hardware,
 elettricità e rete) necessaria per garantire l'operatività
 dei componenti. Azure gestisce inoltre la piattaforma, garantendo un sistema operativo aggiornato
 con tutte le patch e gli
 aggiornamenti della sicurezza corretti, oltre che con gli aggiornamenti di componenti quali .NET
 Framework e Internet Information Server. Poiché tutte le macchine virtuali
 eseguono Windows Server 2008, Azure offre altre
 funzionalità, tra cui il monitoraggio diagnostico, il supporto per desktop remoto,
 i firewall e la configurazione dell'archivio certificati. Tutte queste funzionalità
 sono offerte senza costi aggiuntivi. Quando si esegue l'applicazione
 in Azure, è infatti inclusa la licenza per il sistema
 operativo Windows Server 2008. Dal momento che tutte le macchine virtuali eseguono
 Windows Server 2008, tutto il codice eseguito in Windows Server 2008 funziona
 perfettamente anche in Azure.

## <a id="concepts"> </a>Concetti principali relativi al servizio ospitato

L'applicazione distribuita come servizio ospitato in Azure,
viene eseguita con uno o più *ruoli*. Un *role* si riferisce semplicemente ai 
file e alla configurazione dell'applicazione. Si possono definire uno o più ruoli per
l'applicazione, ognuno con un proprio set di file dell'applicazione e
configurazione. Per ogni ruolo dell'applicazione è possibile specificare il
numero di macchine virtuali o *istanze del ruolo* da eseguire. La figura seguente illustra due
semplici esempi di un'applicazione modellata come servizio ospitato con
ruoli e istanze del ruolo.

##### Figura 1: un singolo ruolo con tre istanze (macchine virtuali) in esecuzione in un data center di Azure

![image][0]

##### Figura 2: due ruoli, ognuno con due istanze (macchine virtuali) in esecuzione in un data center di Azure

![image][1]

Le istanze del ruolo elaborano in genere le richieste client Internet che pervengono al
data center tramite il cosiddetto *endpoint di input*. Per un singolo ruolo
possono essere disponibili 0 o più endpoint di input. Ogni endpoint indica un protocollo
(HTTP, HTTPS o TCP) e una porta. In genere si configura un ruolo con
due endpoint di input: HTTP in ascolto sulla porta 80 e HTTPS in ascolto
sulla porta 443. La figura seguente illustra un esempio dei due diversi ruoli
con endpoint di input diversi ai quali vengono indirizzate le richieste client.

![image][2]

A un servizio ospitato creato in Azure viene assegnato un
indirizzo IP indirizzabile pubblicamente che i client possono usare per accedere al servizio. Quando si
crea il servizio ospitato, è necessario selezionare anche un prefisso URL che viene
mappato a tale indirizzo IP. Questo prefisso deve essere univoco dal momento che viene
usato essenzialmente per riservare l'URL *prefisso*.cloudapp.net in modo che nessun altro
possa disporne. L'URL
viene usato dai client per comunicare con le istanze del ruolo. In genere, non si distribuisce né si pubblica l'
URL *prefisso*.cloudapp.net di Azure. Per reindirizzare le
richieste client all'URL di Azure, si usa invece un nome DNS acquistato presso
un registrar DNS a scelta e appositamente configurato. Per informazioni dettagliate, vedere 
[Configurazione di un nome di dominio personalizzato in Azure][].

## <a id="considerations"> </a>Considerazioni sulla progettazione del servizio ospitato

Quando si progetta un'applicazione da eseguire in un ambiente cloud, è necessario considerare
 diversi fattori, tra cui latenza,
 disponibilità elevata e scalabilità.

Anche scegliere dove inserire il codice dell'applicazione è una decisione
importante quando si esegue un servizio ospitato in Azure. In genere,
per ridurre la latenza e garantire le migliori prestazioni possibili, l'applicazione viene distribuita nei data center più vicini ai
client.
Per evitare problemi legali o di giurisdizione sui dati e sulla posizione in cui risiedono, è tuttavia possibile scegliere un data center più vicino
alla società o ai
dati. In tutto il
mondo sono disponibili sei data center in grado di ospitare il codice dell'applicazione. La tabella sottostante elenca
le località disponibili:

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
<tbody>
<tr>
<td style="width: 100px;">
**Paese/Area geografica**

</td>
<td style="width: 200px;">
**Sottoaree**

</td>
</tr>
<tr>
<td>
Stati Uniti

</td>
<td>
Centro-meridionali e centro-settentrionali

</td>
</tr>
<tr>
<td>
Europa

</td>
<td>
Settentrionale e occidentale

</td>
</tr>
<tr>
<td>
Asia

</td>
<td>
Sudorientale e orientale

</td>
</tr>
</tbody>
</table>
Quando si crea un servizio ospitato, è necessario selezionare una sottoarea che indica la
posizione in cui si desidera eseguire il codice.

Ai fini della disponibilità elevata e della scalabilità, è di fondamentale importanza
che i dati dell'applicazione siano mantenuti in un repository centrale accessibile
a più istanze del ruolo. A questo scopo, in Azure sono disponibili diverse
opzioni di archiviazione, tra cui BLOB, tabelle e database SQL. Per altre
informazioni su queste tecnologie di archiviazione, vedere[l'articolo sulle ][]offerte disponibili per l'archiviazione dati in Azure.
 La figura seguente illustra la modalità con cui il
sistema di bilanciamento del carico all'interno del data center di Azure distribuisce
le richieste client a diverse istanze del ruolo che hanno tutte accesso alla
stessa risorsa di archiviazione dati.

![image][3]

In genere, si preferisce che il codice dell'applicazione e i dati siano entrambi disponibili nello
stesso data center, in quanto questa configurazione garantisce una latenza bassa e, di conseguenza, prestazioni migliori
quando il codice dell'applicazione accede ai dati. Lo spostamento dei dati all'interno dello stesso
data center non
comporta inoltre costi di larghezza di banda.

## <a id="scale"> </a>Progettazione dell'applicazione per la scalabilità

A volte è consigliabile ospitare in Azure una singola applicazione, ad esempio un semplice sito
Web, ma spesso
l'applicazione può essere costituita da ruoli diversi che operano congiuntamente. Nella figura seguente, ad
esempio, sono presenti due istanze del ruolo
Sito Web, tre istanze del ruolo Elaborazione degli ordini e un'istanza del
ruolo Generatore di report. Tutti questi ruoli operano congiuntamente ed è possibile
creare e distribuire in un unico pacchetto il relativo codice come una singola
unità in Azure.

![image][4]

Il motivo principale per cui dividere un'applicazione in diversi ruoli ognuno
in esecuzione nel relativo insieme di istanze del ruolo, ovvero macchine virtuali, è per garantire la scalabilità dei
ruoli in modo indipendente. Durante la stagione estiva, ad esempio, è possibile che molti
clienti facciano acquisti presso la società, pertanto potrebbe
essere necessario incrementare il numero di istanze del ruolo che eseguono il ruolo Sito Web,
nonché di quelle che eseguono il ruolo Elaborazione degli
ordini. Dopo la stagione estiva è possibile che aumenti il numero di prodotti restituiti.
In tal caso potrebbe essere ancora necessario mantenere un numero elevato di istanze di Sito Web, ma meno istanze di Elaborazione
degli ordini. Nel resto dell'anno potrebbero essere necessarie solo alcune
istanze di Sito Web e di Elaborazione degli ordini. In tutti i periodi
potrebbe infine essere necessaria una sola istanza di Generatore di report. Grazie alla flessibilità delle
distribuzioni basate sui ruoli in Azure, si può adattare facilmente
l'applicazione alle esigenze aziendali.

È normale che le istanze del ruolo all'interno del servizio ospitato
comunichino tra loro. Ad esempio, il ruolo Sito Web accetta l'ordine di un
cliente, ma successivamente ripartisce il carico di lavoro alle istanze del ruolo Elaborazione
degli ordini. Il modo migliore per passare il lavoro da un set di
istanze del ruolo a un altro consiste nell'usare la tecnologia di
accodamento disponibile in Azure, ovvero il Servizio di accodamento o
le code del bus di servizio. La coda costituisce un fattore importante in questo
contesto, in quanto consente al servizio ospitato di garantire la scalabilità dei relativi ruoli
in modo indipendente e, di conseguenza, di bilanciare il carico di lavoro rispetto al costo. Se il
numero di messaggi nella coda cresce nel tempo, è possibile aumentare
il numero di istanze del ruolo Elaborazione degli ordini. Se invece decresce,
è possibile ridurre il numero di istanze di questo
ruolo. In questo modo si pagherà solo
per le istanze necessarie a gestire l'effettivo carico di lavoro.

La coda offre inoltre una maggiore affidabilità. Quando viene ridotto il numero di istanze del ruolo
Elaborazione degli ordini, Azure stabilisce quali sono le istanze
da terminare. Può ad esempio terminare un'istanza per la quale è in
corso l'elaborazione di un messaggio della coda. Poiché tuttavia l'elaborazione
non viene completata correttamente, il messaggio risulta
nuovamente visibile a un'altra istanza del ruolo Elaborazione degli ordini che la preleva e la
elabora. Dal momento che i messaggi della coda sono visibili, viene
garantita l'elaborazione di tutti i messaggi. La coda funge anche da bilanciamento del
carico, distribuendo in modo efficace i messaggi a tutte le istanze del
ruolo che li richiedono.

Per le istanze del ruolo Sito Web è possibile monitorare il traffico in
entrata e decidere se aumentarne o ridurne il numero.. La
coda consente di garantire la scalabilità del numero di istanze del ruolo Sito Web in modo
indipendente dalle istanze del ruolo Elaborazione degli ordini. Questo aspetto è particolarmente
efficace e offre una notevole flessibilità. Se naturalmente
l'applicazione è costituita da altri ruoli, è possibile aggiungere altre
code come canale di comunicazione tra loro al fine di usufruire degli
stessi vantaggi in termini di scalabilità e costo.

## <a id="defandcfg"> </a>Definizione e configurazione del servizio ospitato

Per distribuire un servizio ospitato in Azure, è inoltre necessario avere un
file di definizione del servizio e di un file di configurazione del servizio. Entrambi questi
file, in formato XML, consentono di specificare in modo dichiarativo le
opzioni di distribuzione per il servizio ospitato. Nel file di definizione del servizio
sono descritti tutti i ruoli che costituiscono il servizio ospitato e la relativa modalità di
comunicazione. Nel file di configurazione del servizio sono invece descritti il numero di
istanze per ogni ruolo e le impostazioni usate per configurare ognuna di
esse.

## <a id="def"> </a>File di definizione del servizio

Come accennato in precedenza, il file di definizione del servizio (CSDEF) è un file XML
in cui sono descritti i diversi ruoli che costituiscono l'applicazione
completa. Lo schema completo per il file XML è disponibile qui:
[http://msdn.microsoft.com/library/windowsazure/ee758711.aspx][].
Il file CSDEF contiene un elemento WebRole o WorkerRole per ogni ruolo.
 che si desidera usare nell'applicazione. Se si distribuisce un ruolo come ruolo Web (mediante
l'elemento WebRole), il codice verrà eseguito su un'istanza del ruolo
che contiene Windows Server 2008 e Internet Information Server (IIS).
Se invece si distribuisce un ruolo come ruolo di lavoro (mediante l'elemento WorkerRole)
, nell'istanza del ruolo sarà presente Windows Server 2008, mentre IIS non
verrà installato.

È certamente possibile creare e distribuire un ruolo di lavoro che usa altri
meccanismi per ascoltare le richieste Web in arrivo. Ad esempio, il codice
potrebbe creare e usare un HttpListener .NET. Poiché in tutte le istanze del ruolo
viene eseguito Windows Server 2008, con il codice è possibile eseguire operazioni
che sono normalmente disponibili per un'applicazione in esecuzione in Windows Server
2008.

Per ogni ruolo è necessario indicare le dimensioni desiderate per la macchina virtuale che sarà usata dalle istanze di quel
ruolo. Nella tabella seguente sono elencate le diverse dimensioni delle macchine virtuali
attualmente disponibili e gli attributi di ognuna:

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
<tbody>
<tr align="left" valign="top">
<td>
**Dimensioni macchina virtuale**

</td>
<td>
**CPU**

</td>
<td>
**RAM**

</td>
<td>
**Disco**

</td>
<td>
**I/O 
di rete di picco**

</td>
</tr>
<tr align="left" valign="top">
<td>
**Molto piccola**

</td>
<td>
1 da 1.0 GHz

</td>
<td>
768 MB

</td>
<td>
20 GB

</td>
<td>
\~5 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**Piccola**

</td>
<td>
1 da 1,6 GHz

</td>
<td>
1,75 GB

</td>
<td>
225GB

</td>
<td>
\~100 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**Media**

</td>
<td>
2 da 1,6 GHz

</td>
<td>
3,5 GB

</td>
<td>
490 GB

</td>
<td>
\~200 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**Grande**

</td>
<td>
4 da 1,6 GHz

</td>
<td>
7 GB

</td>
<td>
1 TB

</td>
<td>
\~400 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**Molto grande**

</td>
<td>
8 da 1,6 GHz

</td>
<td>
14 GB

</td>
<td>
2 TB

</td>
<td>
\~800 Mbps

</td>
</tr>
</tbody>
</table>
Vengono addebitati i costi orari relativi a ogni macchina virtuale usata come istanza del ruolo, nonché i
costi relativi a tutti i dati inviati dalle istanze del ruolo all'esterno del
data center. Non vengono invece addebitati i costi relativi ai dati in ingresso nel data center. Per
altre informazioni, vedere la pagina relativa ai [prezzi di Azure][]. In generale, è
consigliabile usare più istanze del ruolo di piccole dimensioni, anziché alcune istanze del ruolo di grandi
dimensioni, in modo che l'applicazione risulti più resiliente in caso di errore. In linea di
massima, se il numero di istanze del ruolo è ridotto, un errore in
una sola di esse può comportare gravi danni all'intera applicazione. Come accennato in precedenza,
è necessario distribuire almeno due istanze per ogni ruolo per ottenere il
99,95% del contratto di servizio offerto da Microsoft

Nel file di definizione del servizio (CSDEF) è inoltre possibile specificare il numero di
attributi relativi a ogni ruolo dell'applicazione. Di seguito sono elencati alcuni degli elementi più
utili disponibili:

-   **Certificati**. I certificati vengono usati per crittografare i dati oppure se il
servizio Web supporta SSL. Tutti i certificati devono essere caricati
in Azure. Per altre informazioni, vedere [Gestione certificati
in Azure][]. Questa impostazione XML consente di installare certificati
 caricati in precedenza nell'archivio certificati dell'istanza del ruolo in modo che
 possano essere usati dal codice dell'applicazione.

-   **Nomi di impostazioni di configurazione**. Vengono usati per i valori che dovranno essere letti
dall'applicazione o dalle applicazioni durante l'esecuzione in un'istanza del ruolo. Il valore
effettivo dell'impostazione di configurazione viene definito nel file di configurazione del
servizio (CSCFG) che può essere aggiornato in qualsiasi momento senza
ridistribuire il codice. Di fatto, è possibile scrivere il codice per le
 applicazioni in modo che i valori di configurazione modificati
 siano rilevati senza comportare alcun tempo di inattività.

-   **Endpoint di input**. Consentono di specificare tutti gli endpoint HTTP, HTTPS o TCP
 (con porte) da esporre al mondo esterno  tramite l'URL
 *prefisso*.cloudapp.net. Quando il
 ruolo viene distribuito in Azure, il firewall viene configurato automaticamente sulla relativa
 istanza.

-   **Endpoint interni**. Consentono di specificare tutti gli endpoint HTTP o TCP
da esporre ad altre istanze del ruolo distribuite insieme
all'applicazione. Con gli endpoint interni tutte le istanze
 del ruolo all'interno dell'applicazione possono comunicare tra loro, ma risultano
 inaccessibili a istanze del ruolo esterne all'applicazione.

-   **Moduli di importazione**. Consentono di installare componenti utili facoltativi nelle
istanze del ruolo. Sono disponibili componenti per monitoraggio diagnostico,
 desktop remoto e Azure Connect (che consente all'istanza del
 ruolo di accedere a risorse locali tramite un canale sicuro).

-   **Risorsa di archiviazione locale**. Consente di allocare nell'istanza del
ruolo una sottodirectory a disposizione dell'applicazione. Viene descritta in dettaglio
 nell'articolo sulle[ offerte disponibili per l'archiviazione dati in Azure][].

-   **Attività di avvio**. Consentono di installare
componenti prerequisiti in un'istanza del ruolo all'avvio. Se necessario, è possibile eseguire le attività
 con privilegi elevati di amministratore.

## <a id="cfg"> </a>File di configurazione del servizio

Il file di configurazione del servizio (CSCFG) è un file XML in cui sono descritte le
impostazioni che si possono modificare senza ridistribuire l'applicazione. Lo
 schema completo per il file XML è disponibile qui:
[http://msdn.microsoft.com/it-itrary/windowsazure/ee758710.aspx][].
Il file CSCFG contiene un elemento Role per ogni ruolo
dell'applicazione. Di seguito sono elencati alcuni degli elementi che è possibile specificare nel file
CSCFG:

-   **Versione sistema operativo**. Questo attributo consente di selezionare la versione del sistema
operativo da usare per tutte le istanze del ruolo in cui è in esecuzione
il codice dell'applicazione. Il sistema operativo è il cosiddetto *sistema operativo guest* e ogni
 nuova versione include le patch e gli aggiornamenti della sicurezza più recenti
 disponibili al momento del rilascio. Se si imposta il valore dell'attributo
 osVersion su "\", il sistema operativo guest verrà automaticamente
 aggiornato in Azure, in ognuna delle istanze del ruolo, non appena
 saranno disponibili nuove versioni. È tuttavia possibile rifiutare esplicitamente gli aggiornamenti
automatici selezionando una versione specifica del sistema operativo guest. Se ad esempio si
 imposta l'attributo osVersion sul valore
 "WA-GUEST-OS-2.8\_201109-01", in tutte le istanze del ruolo si verificherà
 quanto descritto nella pagina Web all'indirizzo:
    [http://msdn.microsoft.com/library/hh560567.aspx][]. Per altre
 informazioni sulle versioni del sistema operativo guest, vedere [Gestire gli aggiornamenti al
 sistema operativo guest Azure ].

-   **Istanze**. Il valore di questo elemento indica il numero di istanze del
ruolo di cui effettuare il provisioning eseguendo il codice per un ruolo
specifico. Poiché è possibile caricare un nuovo file CSCFG in Azure
(senza ridistribuire l'applicazione), è semplicissimo
modificare il valore di questo elemento e caricare un nuovo file CSCFG per
incrementare o diminuire dinamicamente il numero di istanze del ruolo
che eseguono il codice dell'applicazione. In questo modo
 l'applicazione diventa facilmente scalabile in base alle effettive richieste del carico di lavoro ed è possibile al contempo
 controllare i costi associati all'esecuzione delle istanze del ruolo.

-   **Valori di impostazioni di configurazione**. Questo elemento consente di indicare i valori per le
impostazioni, definiti nel file CSDEF e letti dal ruolo
durante l'esecuzione. Si tratta dei valori di impostazioni di configurazione usati
 in genere per le stringhe di connessione al database SQL o ad
 Archiviazione di Azure, che possono tuttavia essere usati anche per altre finalità.

## <a id="hostedservices"> </a>Creazione e distribuzione di un servizio ospitato

Per creare un servizio ospitato, è innanzitutto necessario passare al [portale di gestione di Azure] ed eseguire il provisioning di un servizio ospitato specificando
un prefisso DNS e il data center in cui eseguire effettivamente il
codice. Successivamente, nell'ambiente di sviluppo è necessario creare il file di definizione
del servizio (CSDEF), compilare il codice dell'applicazione e comprimere
tutti questi file in un file di pacchetto del servizio (CSPKG). È anche necessario
preparare il file di configurazione del servizio (CSCFG). Per distribuire il ruolo,
è necessario caricare i file CSPKG e CSCFG con l'API di gestione
dei servizi di Azure. Dopo la distribuzione, in Azure verrà eseguito il provisioning delle istanze del
ruolo nel data center, sulla base dei dati di configurazione.
Il codice dell'applicazione verrà quindi estratto dal pacchetto e copiato nelle istanze del
ruolo che saranno infine avviate. A questo punto il codice è operativo.

.La figura seguente illustra i file CSPKG e CSCFG creati nel
computer di sviluppo. Il file CSPKG contiene il file CSDEF e il
codice per due ruoli. Dopo il caricamento dei file CSPKG e CSCFG con
l'API di Gestione servizi di Azure, nel data center verranno create le istanze
del ruolo. In questo esempio il file CSCFG indica
che in Azure è necessario creare tre istanze del ruolo 1 e due
istanze del ruolo 2.

![image][5]

Per altre informazioni su distribuzione, aggiornamento e riconfigurazione dei
ruoli, vedere l'articolo relativo a[distribuzione e aggiornamento di applicazioni di Azure][]
.<a id="Ref" name="Ref"></a>

## <a id="references"> </a>Riferimenti

-   [Creazione di un servizio ospitato per Azure][]

-   [Gestione di servizi ospitati in Azure][]

-   [Migrazione di applicazioni ad Azure][]

-   [Configurazione di un'applicazione Azure][]

<div style="width: 700px; border-top: solid; margin-top: 5px; padding-top: 5px; border-top-width: 1px;">

<p>Autore: Jeffrey Richter (Wintellect)</p>

</div>

  [Vantaggi del modello applicativo di Azure]: #benefits
  [Concetti principali relativi al servizio ospitato]: #concepts
  [Considerazioni sulla progettazione del servizio ospitato]: #considerations
  [Progettazione dell'applicazione per la scalabilità]: #scale
  [Definizione e configurazione del servizio ospitato]: #defandcfg
  [File di definizione del servizio]: #def
  [File di configurazione del servizio]: #cfg
  [Creazione e distribuzione di un servizio ospitato]: #hostedservices
  [Riferimenti]: #references
  [0]: ./media/application-model/application-model-3.jpg
  [1]: ./media/application-model/application-model-4.jpg
  [2]: ./media/application-model/application-model-5.jpg
  [Configuring a Custom Domain Name in Azure]: http://www.windowsazure.com/it-it/develop/net/common-tasks/custom-dns/
  [Data Storage Offerings in Azure]: http://www.windowsazure.com/it-it/develop/net/fundamentals/cloud-storage/
  [3]: ./media/application-model/application-model-6.jpg
  [4]: ./media/application-model/application-model-7.jpg
  
  [Prezzi di Azure]: http://www.windowsazure.com/it-it/pricing/calculator/
  [Gestione dei certificati in Azure]: http://msdn.microsoft.com/library/windowsazure/gg981929.aspx
  [http://msdn.microsoft.com/library/windowsazure/ee758710.aspx]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
  [http://msdn.microsoft.com/library/hh560567.aspx]: http://msdn.microsoft.com/library/hh560567.aspx
  [Gestione degli aggiornamenti al sistema operativo guest Azure]: http://msdn.microsoft.com/library/ee924680.aspx
  [Portale di gestione di Azure]: http://manage.windowsazure.com/
  [5]: ./media/application-model/application-model-8.jpg
  [Distribuzione e aggiornamento di applicazioni Azure]: http://www.windowsazure.com/it-it/develop/net/fundamentals/deploying-applications/
  [Creazione di un servizio ospitato per Azure]: http://msdn.microsoft.com/library/gg432967.aspx
  [Gestione di servizi ospitati in Azure]: http://msdn.microsoft.com/library/gg433038.aspx
  [Migrazione di applicazioni ad Azure]: http://msdn.microsoft.com/library/gg186051.aspx
  [Configurazione di un'applicazione Azure]: http://msdn.microsoft.com/library/windowsazure/ee405486.aspx


</td>
</tr>
<tr align="left" valign="top">
<td>
**Medium**

</td>
<td>
2 x 1.6 GHz

</td>
<td>
3.5 GB

</td>
<td>
490GB

</td>
<td>
\~200 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**Large**

</td>
<td>
4 x 1.6 GHz

</td>
<td>
7 GB

</td>
<td>
1TB

</td>
<td>
\~400 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**Extra Large**

</td>
<td>
8 x 1.6 GHz

</td>
<td>
14 GB

</td>
<td>
2TB

</td>
<td>
\~800 Mbps

</td>
</tr>
</tbody>
</table>
You are charged hourly for each VM you use as a role instance and you
are also charged for any data that your role instances send outside the
data center. You are not charged for data entering the data center. For
more information, see [Azure Pricing][]. In general, it is
advisable to use many small role instances as opposed to a few large
instances so that your application is more resilient to failure. After
all, the fewer role instances you have, the more disastrous a failure in
one of them is to your overall application. Also, as mentioned before,
you must deploy at least two instances for each role in order to get the
99.95% service level agreement Microsoft provides.

The service definition (CSDEF) file is also where you would specify many
attributes about each role in your application. Here are some of the
more useful items available to you:

-   **Certificates**. You use certificates for encrypting data or if
    your web service supports SSL. Any certificates need to be uploaded
    to Azure. For more information, see [Managing Certificates
    in Azure][]. This XML setting installs previously-uploaded
    certificates into the role instance's certificate store so that they
    are usable by your application code.

-   **Configuration Setting Names**. For values that you want your
    application(s) to read while running on a role instance. The actual
    value of the configuration settings is set in the service
    configuration (CSCFG) file which can be updated at any time without
    requiring you to redeploy your code. In fact, you can code your
    applications in such a way to detect the changed configuration
    values without incurring any downtime.

-   **Input Endpoints**. Here you specify any HTTP, HTTPS, or TCP
    endpoints (with ports) that you want to expose to the outside world
    via your *prefix*.cloadapp.net URL. When Azure deploys your
    role, it will configure the firewall on the role instance
    automatically.

-   **Internal Endpoints**. Here you specify any HTTP or TCP endpoints
    that you want exposed to other role instances that are deployed as
    part of your application. Internal endpoints allow all the role
    instances within your application to talk to each other but are not
    accessible to any role instances that are outside your application.

-   **Import Modules**. These optionally install useful components on
    your role instances. Components exist for diagnostic monitoring,
    remote desktop, and Azure Connect (which allows your role
    instance to access on-premises resources through a secure channel).

-   **Local Storage**. This allocates a subdirectory on the role
    instance for your application to use. It is described in more detail
    in the [Data Storage Offerings in Azure][] article.

-   **Startup Tasks**. Startup tasks give you a way to install
    prerequisite components on a role instance as it boots up. The tasks
    can run elevated as an administrator if required.

## <a id="cfg"> </a>The Service Configuration File

The service configuration (CSCFG) file is an XML file that describes
settings that can be changed without redeploying your application. The
complete schema for the XML file can be found here:
[http://msdn.microsoft.com/library/windowsazure/ee758710.aspx][].
The CSCFG file contains a Role element for each role in your
application. Here are some of the items you can specify in the CSCFG
file:

-   **OS Version**. This attribute allows you to select the operating
    system (OS) version you want used for all the role instances running
    your application code. This OS is known as the *guest OS*, and each
    new version includes the latest security patches and updates
    available at the time the guest OS is released. If you set the
    osVersion attribute value to "\*", then Azure automatically
    updates the guest OS on each of your role instances as new guest OS
    versions become available. However, you can opt out of automatic
    updates by selecting a specific guest OS version. For example,
    setting the osVersion attribute to a value of
    "WA-GUEST-OS-2.8\_201109-01" causes all your role instances to get
    what is described on this web page:
    [http://msdn.microsoft.com/library/hh560567.aspx][]. For more
    information about guest OS versions, see [Managing Upgrades to the
    Azure Guests OS].

-   **Instances**. This element's value indicates the number of role
    instances you want provisioned running the code for a particular
    role. Since you can upload a new CSCFG file to Azure
    (without redeploying your application), it is trivially simple to
    change the value for this element and upload a new CSCFG file to
    dynamically increase or decrease the number of role instances
    running your application code. This allows you to easily scale your
    application up or down to meet actual workload demands while also
    controlling how much you are charged for running the role instances.

-   **Configuration Setting Values**. This element indicates values for
    settings (as defined in the CSDEF file). Your role can read these
    values while it is running. These configuration settings values are
    typically used for connection strings to SQL Database or to 
    Azure Storage, but they can be used for any purpose you desire.

## <a id="hostedservices"> </a>Creating and Deploying a Hosted Service

Creating a hosted service requires that you first go to the [Azure Management Portal] and provision a hosted service by specifying
a DNS prefix and the data center you ultimately want your code running
in. Then in your development environment, you create your service
definition (CSDEF) file, build your application code and package (zip)
all these files into a service package (CSPKG) file. You must also
prepare your service configuration (CSCFG) file. To deploy your role,
you upload the CSPKG and CSCFG files with the Azure Service
Management API. Once deployed, Azure, will provision role
instances in the data center (based upon the configuration data),
extract your application code from the package, copy it to the role
instances, and boot the instances. Now, your code is up and running.

The figure below shows the CSPKG and CSCFG files you create on your
development computer. The CSPKG file contains the CSDEF file and the
code for two roles. After uploading the CSPKG and CSCFG files with the
Azure Service Management API, Azure creates the role
instances in the data center. In this example, the CSCFG file indicated
that Azure should create three instances of role \#1 and two
instances of Role \#2.

![image][5]

For more information about deploying, upgrading, and reconfiguring your
roles, see the [Deploying and Updating Azure Applications][]
article.<a id="Ref" name="Ref"></a>

## <a id="references"> </a>References

-   [Creating a Hosted Service for Azure][]

-   [Managing Hosted Services in Azure][]

-   [Migrating Applications to Azure][]

-   [Configuring an Azure Application][]

<div style="width: 700px; border-top: solid; margin-top: 5px; padding-top: 5px; border-top-width: 1px;">

<p>Written by Jeffrey Richter (Wintellect)</p>

</div>

  [Azure Application Model Benefits]: #benefits
  [Hosted Service Core Concepts]: #concepts
  [Hosted Service Design Considerations]: #considerations
  [Designing your Application for Scale]: #scale
  [Hosted Service Definition and Configuration]: #defandcfg
  [The Service Definition File]: #def
  [The Service Configuration File]: #cfg
  [Creating and Deploying a Hosted Service]: #hostedservices
  [References]: #references
  [0]: ./media/application-model/application-model-3.jpg
  [1]: ./media/application-model/application-model-4.jpg
  [2]: ./media/application-model/application-model-5.jpg
  [Configuring a Custom Domain Name in Azure]: http://www.windowsazure.com/it-it/develop/net/common-tasks/custom-dns/
  [Data Storage Offerings in Azure]: http://www.windowsazure.com/it-it/develop/net/fundamentals/cloud-storage/
  [3]: ./media/application-model/application-model-6.jpg
  [4]: ./media/application-model/application-model-7.jpg
  
  [Azure Pricing]: http://www.windowsazure.com/it-it/pricing/calculator/
  [Managing Certificates in Azure]: http://msdn.microsoft.com/library/windowsazure/gg981929.aspx
  [http://msdn.microsoft.com/library/windowsazure/ee758710.aspx]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
  [http://msdn.microsoft.com/library/hh560567.aspx]: http://msdn.microsoft.com/library/hh560567.aspx
  [Managing Upgrades to the Azure Guests OS]: http://msdn.microsoft.com/library/ee924680.aspx
  [Azure Management Portal]: http://manage.windowsazure.com/
  [5]: ./media/application-model/application-model-8.jpg
  [Deploying and Updating Azure Applications]: http://www.windowsazure.com/it-it/develop/net/fundamentals/deploying-applications/
  [Creating a Hosted Service for Azure]: http://msdn.microsoft.com/library/gg432967.aspx
  [Managing Hosted Services in Azure]: http://msdn.microsoft.com/library/gg433038.aspx
  [Migrating Applications to Azure]: http://msdn.microsoft.com/library/gg186051.aspx
  [Configuring an Azure Application]: http://msdn.microsoft.com/library/windowsazure/ee405486.aspx
