<properties 
    pageTitle="Azure RemoteApp: in che modo la larghezza di banda di rete influisce sulla qualità dell'esperienza? | Microsoft Azure"
	description="Informazioni su come la larghezza di banda di rete in Azure RemoteApp può influire sulla qualità dell'esperienza utente."
	services="remoteapp"
	documentationCenter="" 
	authors="lizap" 
	manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />  

# Azure RemoteApp: in che modo la larghezza di banda di rete influisce sulla qualità dell'esperienza?

> [AZURE.IMPORTANT]
Azure RemoteApp sta per essere sospeso. Per i dettagli, vedere l'[annuncio](https://go.microsoft.com/fwlink/?linkid=821148).

Quando si esamina la [larghezza di banda di rete complessiva](remoteapp-bandwidth.md) necessaria per Azure RemoteApp, tenere presenti i fattori descritti di seguito. Questi fanno tutti parte di un sistema dinamico che influisce sulla soddisfazione complessiva degli utenti.

- **Larghezza di banda disponibile e condizioni della rete**: una serie di parametri, come perdita, latenza e instabilità, nella stessa rete in un determinato momento può influire sull'esperienza di streaming delle applicazioni, offrendo un'esperienza utente complessiva di livello inferiore. La larghezza di banda disponibile nella rete è un fattore importante nei casi di congestione, perdita casuale e latenza, perché tutti questi parametri influiscono sul meccanismo di controllo della congestione, che a sua volta controlla la velocità di trasmissione per evitare conflitti. Ad esempio, una rete con perdita di dati o con latenza elevata rende spiacevole l'esperienza utente anche con una larghezza di banda di 1000 MB. La perdita e la latenza variano in base al numero di utenti connessi alla rete e alle operazioni che tali utenti stanno svolgendo, ad esempio streaming video, download o upload di file di grandi dimensioni, stampa.
- **Scenario di utilizzo**: l'esperienza dipende sia dalle operazioni eseguite dai singoli utenti che dagli utenti della rete nel loro complesso. Ad esempio, la lettura di una diapositiva richiede l'aggiornamento di un solo fotogramma. Se l'utente legge rapidamente e scorre il contenuto di un documento di testo, è necessario aggiornare un numero maggiore di fotogrammi al secondo. La comunicazione di andata e ritorno al server in questo scenario finisce per consumare una maggiore larghezza di banda di rete. Si consideri inoltre un esempio estremo: più utenti che guardano video ad alta definizione (ad esempio con risoluzione 4K), partecipano a conferenze telefoniche HD, giocano con videogiochi 3D o usano sistemi CAD. Tutto questo può rendere praticamente inutilizzabile anche una rete con una larghezza di banda molto elevata.
- **Risoluzione dello schermo e numero di schermi**: è necessaria una maggiore larghezza di banda di rete per aggiornare completamente schermi più grandi rispetto a schermi più piccoli. La tecnologia sottostante è molto efficiente nella codifica e nella trasmissione delle sole aree delle schermate effettivamente da aggiornare, ma a volte deve essere aggiornata l'intera schermata. Quando l'utente dispone di uno schermo con risoluzione superiore (ad esempio a 4K), l'aggiornamento richiede maggiore larghezza di banda di rete rispetto a uno schermo con una risoluzione inferiore (ad esempio 1024x768 px). La stessa logica vale se si usano più schermi per il reindirizzamento. Maggiore il numero di schermi, maggiore deve essere la larghezza di banda.
- **Reindirizzamento degli Appunti e del dispositivo**: si tratta di un problema non molto evidente, ma in molti casi se un utente archivia una grande quantità di dati negli Appunti, è necessario un certo tempo per il trasferimento di tali informazioni dal client Desktop remoto al server. L'esperienza downstream può risentire dell'esperienza dell'invio del contenuto degli Appunti upstream. Lo stesso vale per il reindirizzamento del dispositivo. Se uno scanner o una webcam produce una grande quantità di dati da inviare upstream al server, una stampante deve ricevere un documento di grandi dimensioni oppure una risorsa di archiviazione locale deve essere disponibile per un'app in esecuzione nel cloud per la copia di un file di grandi dimensioni, gli utenti potrebbero notare la perdita di fotogrammi o blocchi temporanei dei video, perché i dati necessari per il reindirizzamento del dispositivo richiedono una maggiore larghezza di banda di rete.

Quando si valutano le esigenze relative alla larghezza di banda di rete, è necessario tenere conto del fatto che l'impatto di tutti questi fattori è il risultato della combinazione di questi ultimi.

Tornare ora all'[articolo principale sulla larghezza di banda di rete](remoteapp-bandwidth.md) o passare al test della [larghezza di banda di rete](remoteapp-bandwidthtests.md).

## Altre informazioni
- [Uso previsto della larghezza di banda di rete di Azure RemoteApp](remoteapp-bandwidth.md)

- [Azure RemoteApp: scenari comuni di test relativi all'utilizzo della larghezza di banda di rete](remoteapp-bandwidthtests.md)

- [Larghezza di banda di rete di Azure RemoteApp: linee guida generali (se non è possibile verificare direttamente)](remoteapp-bandwidthguidelines.md)

<!---HONumber=AcomDC_0817_2016-->