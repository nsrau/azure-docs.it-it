<properties 
	pageTitle="Crittografia lato client per Archiviazione di Microsoft Azure | Microsoft Azure" 
	description="L'anteprima della libreria client di archiviazione di Azure per .NET offre supporto per la crittografia lato client e l'integrazione con l'insieme di credenziali chiave di Azure. La crittografia lato client offre una protezione massima per le applicazioni di Archiviazione di Azure poiché le chiavi di accesso dell'utente non sono mai disponibili per il servizio. La crittografia lato client è disponibile per BLOB, code e tabelle." 
	services="storage" 
	documentationCenter=".net" 
	authors="tamram" 
	manager="carolz" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/10/2015" 
	ms.author="tamram"/>


# Crittografia lato client per Archiviazione di Microsoft Azure (anteprima)

## Panoramica

Introduzione all'[anteprima della libreria client di archiviazione di Azure per .NET](https://www.nuget.org/packages/WindowsAzure.Storage/4.4.1-preview). In questa libreria di anteprima è disponibile una nuova funzionalità che consente agli sviluppatori di crittografare i dati all'interno delle applicazioni client prima del caricamento nell'Archiviazione di Azure, nonché di decrittografare i dati durante il download. La libreria di anteprima, inoltre, supporta l'integrazione con l'[insieme di credenziali chiave](http://azure.microsoft.com/services/key-vault/) di Azure per la gestione delle chiavi dell'account di archiviazione.

## Perché utilizzare la crittografia lato client?

La crittografia lato client offre un vantaggio significativo rispetto alla crittografia lato server: la possibilità di controllare completamente le chiavi di accesso dell'account. La crittografia lato client offre una protezione massima per le applicazioni poiché in Archiviazione di Azure non vengono mai visualizzate le chiavi dell'utente e non vengono mai decrittografati i dati. La libreria di anteprima è completamente disponibile su [GitHub](https://github.com/Azure/azure-storage-net/tree/preview), affinché sia possibile conoscere la modalità con cui la libreria consente di crittografare i dati per assicurarsi che sia compatibile con i propri standard.

## Per quale motivo viene offerta una libreria che supporta la crittografia lato client?

Tutti gli sviluppatori possono crittografare i dati sul lato client prima di caricarli, ma questa operazione richiede una certa esperienza con la crittografia. Inoltre, è necessario progettare le prestazioni e la protezione. Diversi sviluppatori progetterebbero la propria soluzione di crittografia personale e, dal momento che ognuna di queste soluzioni sarebbe diversa dalle altre, non sarebbe possibile nessuna interazione tra loro.

La libreria di anteprima è progettata per:

- Implementare le procedure consigliate di sicurezza.
- Ottimizzare le prestazioni.
- Offrire facilità di utilizzo per scenari comuni.
- Supportare l'interoperabilità tra linguaggi. In futuro i dati crittografati usando la libreria client .NET potranno essere decrittografati tramite le librerie client per gli altri linguaggi supportati, tra cui Java, Node.js e C++ (e viceversa).

## Che cosa è disponibile al momento?

Attualmente la libreria client è in grado di supportare la crittografia per BLOB, tabelle e code utilizzando la tecnica basata su envelope. La crittografia e la decrittografia con chiavi asimmetriche richiedono attività di calcolo complesse. Pertanto, nella tecnica envelope i dati stessi non vengono crittografati direttamente con tali chiavi, ma tramite una chiave di crittografia dei contenuti simmetrica casuale. Questa chiave di crittografia dei contenuti viene quindi crittografata con una chiave pubblica. Il supporto per l'insieme di credenziali chiave di Azure consente di gestire le chiavi in modo efficiente.

È facile utilizzare la crittografia lato client. È possibile specificare le opzioni di richiesta con i criteri di crittografia appropriati (BLOB, coda o tabella) e trasmetterli alle API di caricamento/download dei dati. La libreria client consente di crittografare automaticamente i dati nel client durante il caricamento in Archiviazione di Azure e di decrittografare i dati quando vengono recuperati. Ulteriori informazioni ed esempi di codice sono disponibili nel post di blog [Introduzione alla crittografia lato client per Archiviazione di Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/29/getting-started-with-client-side-encryption-for-microsoft-azure-storage.aspx).

Alcune informazioni aggiuntive sulla crittografia lato client:

- **Protezione**: i dati crittografati non sono leggibili anche se le chiavi dell'account di archiviazione degli utenti vengono compromesse.
- **Crittografia overhead fissa**: i dati crittografati avranno una dimensione stimabile in base alla dimensione originale.
- **Crittografia autosufficiente**: ogni BLOB, entità di tabella o messaggio della coda consente di archiviare tutte le informazioni di crittografia nell'oggetto stesso o nei relativi metadati. L'unico valore esterno richiesto è la chiave di crittografia dell'utente.
- **Rotazione delle chiavi**: è possibile ruotare le chiavi e, durante tale procedura, sono supportate più chiavi.
- **Percorso di aggiornamento pulito**: in futuro saranno supportati ulteriori algoritmi di crittografia e versioni del protocollo, senza la necessità di apportare modifiche significative al codice.
- **Supporto della crittografia dei BLOB**:
	- **Caricamento completo dei BLOB**: è possibile crittografare e caricare file come documenti, foto e video nella loro interezza.
	- **Download completo o basato sull'intervallo dei BLOB**: è possibile scaricare e decrittografare un BLOB interamente o in intervalli.


>[AZURE.IMPORTANT]Quando si utilizza la libreria di anteprima, tenere presente i seguenti aspetti importanti:
>
>- Non utilizzare la libreria di anteprima per i dati di produzione. In futuro, eventuali modifiche apportate alla libreria potrebbero influire sugli schemi utilizzati. La decrittografia dei dati crittografati con la libreria di anteprima non è garantita nelle versioni future.  
>- Durante la lettura da o scrittura in un BLOB crittografato, utilizzare i comandi di caricamento completo dei BLOB e i comandi di download completo o basato sull'intervallo dei BLOB. Evitare di scrivere in un BLOB crittografato usando le operazioni di protocollo, ad esempio Put Block, Put Block List, Write Pages o Clear Pages. In caso contrario, si potrebbe danneggiare il BLOB crittografato e renderlo illeggibile.
>- Per le tabelle esiste un vincolo simile. Prestare attenzione a non aggiornare le proprietà crittografate senza aggiornare i metadati di crittografia.
>- Se si impostano i metadati nel BLOB crittografato, si potrebbero sovrascrivere i metadati correlati alla crittografia richiesti per la decrittografia, poiché i metadati di impostazione non sono additivi. Ciò vale anche per gli snapshot: evitare di specificare i metadati durante la creazione di uno snapshot di un BLOB crittografato.

## Vedere anche

- [Introduzione alla crittografia lato client per Archiviazione di Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/29/getting-started-with-client-side-encryption-for-microsoft-azure-storage.aspx)  
- [Pacchetto NuGet per la libreria client di archiviazione di Azure per .NET (anteprima)](http://www.nuget.org/packages/WindowsAzure.Storage/4.4.0-preview)  
- [Codice sorgente per la libreria client di archiviazione di Azure per .NET (anteprima)](https://github.com/Azure/azure-storage-net/tree/preview)
 

<!---HONumber=62-->