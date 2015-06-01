<properties 
	pageTitle="Usare l'emulatore di archiviazione di Azure per sviluppo e test" 
	description="Informazioni su come usare l'emulatore di archiviazione di Azure per sviluppo e test." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>
<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="tamram"/>

# Usare l'emulatore di archiviazione di Azure per sviluppo e test

## Panoramica
L'emulatore di archiviazione di Microsoft Azure offre un ambiente locale che emula i servizi BLOB, code e tabelle di Azure per scopi di sviluppo. Usando l'emulatore di archiviazione, è possibile testare l'applicazione sui servizi di archiviazione in locale, senza sostenere alcun costo.

> [AZURE.NOTE] L'emulatore di archiviazione è disponibile come parte di Microsoft Azure SDK. È inoltre possibile installare l'emulatore di archiviazione come pacchetto autonomo.
Per configurare l'emulatore di archiviazione, è necessario avere i privilegi di amministratore nel computer. 
> Si noti che i dati creati in una versione dell'emulatore di archiviazione non sono necessariamente accessibili quando si utilizza una versione diversa. Se è necessario rendere persistenti i dati a lungo termine, si consiglia di archiviare i dati in un account di archiviazione di Azure anziché nell'emulatore di archiviazione.
 
Esistono alcune differenze tra l'emulatore di archiviazione e i servizi di archiviazione di Azure. Per altre informazioni su queste differenze, vedere [Differenze tra l'emulatore di archiviazione e i servizi di archiviazione di Azure](https://msdn.microsoft.com/library/azure/gg433135.aspx).

L'emulatore di archiviazione utilizza un'istanza di Microsoft(r) SQL Server(tm) e il file system locale per emulare i servizi di archiviazione di Azure. Per impostazione predefinita, l'emulatore di archiviazione è configurato per un database in Microsoft(r) SQL Server(tm) 2012 Express LocalDB. È possibile installare SQL Server Management Studio Express per gestire l'installazione di LocalDB. L'emulatore di archiviazione si connette a SQL Server o a LocalDB usando l'autenticazione Windows. È possibile scegliere di configurare l'emulatore di archiviazione per accedere a un'istanza locale di SQL Server anziché a LocalDB mediante i riferimenti dello strumento da riga di comando emulatore di archiviazione.

## Autenticazione delle richieste

L'emulatore di archiviazione supporta solo un singolo account fisso e una chiave di autenticazione nota. Questo account e questa chiave sono le uniche credenziali consentite per l'uso con l'emulatore di archiviazione. Sono:

 Account name: devstoreaccount1
 Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtrKBHBeksoGMGw==
 
> [AZURE.NOTE] La chiave di autenticazione supportata dall'emulatore di archiviazione è destinata solo al test della funzionalità del codice di autenticazione client. Non viene utilizzata per eventuali scopi di sicurezza. Non è possibile usare l'account di archiviazione di produzione e la chiave con l'emulatore di archiviazione. Si noti inoltre che non è consigliabile usare l'account di sviluppo con dati di produzione.
 

## Avviare e inizializzare l'emulatore di archiviazione
Per avviare l'emulatore di archiviazione di Azure, selezionare il pulsante Start o premere il tasto Windows. Iniziare a digitare emulatore di archiviazione di Azure e selezionare l'emulatore di archiviazione di Azure dall'elenco di applicazioni. 

In alternativa, se l'emulatore di calcolo di Azure è già in esecuzione, è possibile avviare l'emulatore di archiviazione facendo clic con il pulsante destro del mouse sull'icona della barra delle applicazioni del sistema e selezionando Avvia emulatore di archiviazione. Per altre informazioni sull'esecuzione dell'emulatore di calcolo, vedere [Eseguire un'applicazione Azure nell'emulatore di calcolo](https://msdn.microsoft.com/library/azure/hh403990.aspx).

Quando viene avviato l'emulatore di archiviazione, viene visualizzata una riga di comando. È possibile usare questa riga di comando per avviare e arrestare l'emulatore di archiviazione, cancellare i dati, ottenere lo stato corrente e inizializzare l'emulatore. Per altre informazioni, vedere [Riferimenti allo strumento da riga di comando emulatore di archiviazione](https://msdn.microsoft.com/library/azure/gg433005.aspx).

Quando la finestra della riga di comando viene chiusa, l'emulatore di archiviazione resta in esecuzione. Per visualizzare nuovamente la riga di comando, eseguire la procedura precedente come se si volesse avviare l'emulatore di archiviazione.


La prima volta che si esegue l'emulatore di archiviazione, l'ambiente di archiviazione locale viene inizializzato automaticamente. È possibile usare lo strumento da riga di comando emulatore di archiviazione per puntare a un'istanza di database diversa o per reinizializzare il database esistente. Il processo di inizializzazione crea un database in LocalDB e riserva le porte HTTP per ogni servizio di archiviazione locale. Questo passaggio richiede privilegi amministrativi. Per i dettagli, vedere [Riferimenti allo strumento da riga di comando emulatore di archiviazione](https://msdn.microsoft.com/library/azure/gg433005.aspx).

## Informazioni sugli URI del servizio di archiviazione

La modalità di indirizzamento di una risorsa nei servizi di archiviazione di Azure varia a seconda che la risorsa si trovi in Azure o nei servizi dell'emulatore di archiviazione. Uno schema URI viene usato per indirizzare una risorsa di archiviazione in Azure e un altro schema URI viene usato per indirizzare una risorsa di archiviazione nell'emulatore di archiviazione. La differenza è dovuta al fatto che il computer locale non esegue la risoluzione dei nomi di dominio. Entrambi gli schemi URI includono sempre il nome dell'account e l'indirizzo della risorsa richiesta.

## Indirizzamento delle risorse di archiviazione di Azure nel cloud

Nello schema URI per l'indirizzamento delle risorse di archiviazione in Azure, il nome dell'account fa parte del nome host dell'URI e la risorsa indirizzata fa parte del percorso URI. Lo schema di indirizzamento di base seguente viene usato per accedere alle risorse di archiviazione:

 <http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>


`<account-name>` è il nome dell'account di archiviazione. `<service-name>` è il nome del servizio a cui si accede e `<resource-path>` è il percorso della risorsa richiesta. Nell'elenco seguente viene illustrato lo schema URI per ciascuno dei servizi di archiviazione:

	Blob Service: <http|https>://<account-name>.blob.core.windows.net/<resource-path>
	Queue Service: <http|https>://<account-name>.queue.core.windows.net/<resource-path>
	Table Service: <http|https>://<account-name>.table.core.windows.net/<resource-path>

Ad esempio, l'indirizzo seguente può essere usato per accedere a un BLOB nel cloud:
 
 http://myaccount.blob.core.windows.net/mycontainer/myblob.txt

> [AZURE.NOTE] È inoltre possibile associare un nome di dominio personalizzato a un endpoint di archiviazione BLOB nel cloud e usare il nome di dominio personalizzato per indirizzare BLOB e contenitori. Vedere 
 
## Indirizzamento delle risorse di archiviazione di Azure locali nell'emulatore di archiviazione

Nell'emulatore di archiviazione, poiché il computer locale non esegue la risoluzione dei nomi di dominio, il nome dell'account fa parte del percorso URI. Lo schema URI per una risorsa in esecuzione nell'emulatore di archiviazione ha il formato seguente:

 http://<local-machine-address>:<port>/<account-name>/<resource-path>

Per indirizzare le risorse in esecuzione nell'emulatore di archiviazione viene usato il formato seguente:

	Blob Service: http://127.0.0.1:10000/<account-name>/<resource-path>
	Queue Service: http://127.0.0.1:10001/<account-name>/<resource-path>
	Table Service: http://127.0.0.1:10002/<account-name>/<resource-path>

Ad esempio, l'indirizzo seguente può essere usato per accedere a un BLOB nell'emulatore di archiviazione:

 http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt

> [AZURE.NOTE] HTTPS non è un protocollo consentito per l'indirizzamento delle risorse di archiviazione locali.
 
## Indirizzamento dell'account secondario con RA-GRS
A partire dalla versione 3.1, l'account dell'emulatore di archiviazione supporta RA-GRS (replica con ridondanza geografica in sola lettura). Per le risorse di archiviazione nel cloud e nell'emulatore locale, è possibile accedere al percorso secondario aggiungendo "secondario" al nome dell'account. Ad esempio, l'indirizzo seguente può essere usato per accedere a un BLOB mediante il percorso secondario di sola lettura nell'emulatore di archiviazione:

 http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt 

> [AZURE.NOTE] Per l'accesso a livello di codice al percorso secondario con l'emulatore di archiviazione, usare la libreria client di archiviazione per .NET versione 3.2 o successiva. Per i dettagli vedere il riferimento alla libreria client di archiviazione.
 
## Inizializzare l'emulatore di archiviazione usando lo strumento da riga di comando
È possibile usare lo strumento da riga di comando emulatore di archiviazione per inizializzare l'emulatore di archiviazione in modo che punti a un'istanza di database diverso da quella predefinita. Se si vuole usare l'istanza di database LocalDB predefinita, non è necessario eseguire un passaggio di inizializzazione separato.

Questo strumento viene installato per impostazione predefinita nella directory C:\Programmi (x86)\Microsoft SDKs\Azure\Emulatore di archiviazione. L'inizializzazione viene eseguita automaticamente al primo avvio dell'emulatore.

> [AZURE.NOTE] Per eseguire il comando 'init' descritto di seguito è necessario avere i privilegi amministrativi.

1. Fare clic sul pulsante **Start** o premere il tasto **Windows**. Iniziare a digitare `Emulatore di archiviazione di Azure` e selezionare l'emulatore di archiviazione quando viene visualizzato. Viene visualizzata una finestra del prompt dei comandi.


2. Nella finestra del prompt dei comandi, digitare il seguente comando dove `<SQLServerInstance>' è il nome dell'istanza di SQL Server. Per usare LocalDb, specificare `(localdb)\v11.0` come istanza di SQL Server.

 WAStorageEmulator init /sqlInstance <SQLServerInstance> 
 
Per indicare all'emulatore di usare l'istanza di SQL Server predefinita, digitare il comando seguente:

 WAStorageEmulator init /server .\\ 

In alternativa, è possibile usare il comando seguente, che consente di inizializzare nuovamente il database:

 WAStorageEmulator init /forceCreate 

## Riferimenti dello strumento da riga di comando emulatore di archiviazione

A partire dalla versione 3.0, quando si avvia l'emulatore di archiviazione viene visualizzato un prompt dei comandi. Usare il prompt dei comandi per avviare e arrestare l'emulatore, nonché per eseguire query sullo stato e altre operazioni.

> [AZURE.NOTE] Se l'emulatore di calcolo è installato, un'icona nella barra delle applicazioni viene visualizzata quando si avvia l'emulatore di archiviazione. Fare clic con il pulsante destro del mouse sull'icona per visualizzare un menu che fornisce una modalità grafica per avviare e arrestare l'emulatore di archiviazione.

### Sintassi della riga di comando

	WAStorageEmulator [/start] [/stop] [/status] [/clear] [/init] [/help]

### Opzioni
Per visualizzare l'elenco di opzioni, digitare '/help ' al prompt dei comandi.

## Passaggi successivi
- [Riferimenti dello strumento da riga di comando emulatore di archiviazione](https://msdn.microsoft.com/library/azure/gg433005.aspx)
- [Differenze tra l'emulatore di archiviazione e i servizi di archiviazione di Azure](https://msdn.microsoft.com/library/azure/gg433135.aspx)
- [Note sulla versione di Emulatore di archiviazione](https://msdn.microsoft.com/library/azure/dn683879.aspx) 

<!--HONumber=47-->
