---
title: Risolvere i problemi relativi al runtime di integrazione self-hosted in Azure Data Factory
description: Informazioni su come risolvere i problemi relativi al runtime di integrazione self-hosted in Azure Data Factory.
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/17/2020
ms.author: lle
ms.openlocfilehash: 635178999398287649d8630fc5262a385afc48b2
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96341785"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Risolvere i problemi relativi al runtime di integrazione self-hosted

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra i metodi più comuni per la risoluzione dei problemi relativi al runtime di integrazione self-hosted in Azure Data Factory.

## <a name="gather-self-hosted-ir-logs-from-azure-data-factory"></a>Raccogli log IR indipendenti da Azure Data Factory

Per le attività non riuscite in esecuzione sul runtime di integrazione self-hosted IR/Shared, Azure Data Factory supporta la visualizzazione e il caricamento dei log degli errori. È possibile seguire questa procedura per ottenere l'ID della segnalazione errori, quindi immettere l'ID del report per individuare i problemi noti correlati.

1. Passare alla pagina **esecuzioni attività** .

1. Nella colonna **errore** fare clic sul pulsante sotto.

    ![Pagina esecuzioni attività](media/self-hosted-integration-runtime-troubleshoot-guide/activity-runs-page.png)

1. Vengono visualizzati i log correlati per l'esecuzione dell'attività non riuscita. Per ulteriore assistenza, fare clic sul pulsante **Invia log** .

    ![Inviare i log](media/self-hosted-integration-runtime-troubleshoot-guide/send-logs.png)

1. È possibile scegliere i log da inviare. Per il runtime di integrazione *self-hosted*, è possibile caricare i log relativi all'attività non riuscita o a tutti i log nel nodo IR self-hosted. Per il runtime di integrazione *condiviso*, è possibile caricare solo i log relativi alle attività non riuscite.

    ![Selezione dei log](media/self-hosted-integration-runtime-troubleshoot-guide/choose-logs.png)

1. Quando si caricano i log, tenere un record dell'ID del report, se è necessaria ulteriore assistenza per risolvere il problema.

    ![Caricare i log](media/self-hosted-integration-runtime-troubleshoot-guide/upload-logs.png)

> [!NOTE]
> La visualizzazione del log e il caricamento delle richieste verranno eseguiti in tutte le istanze del runtime di integrazione self-hosted online. Verificare che tutte le istanze del runtime di integrazione Self-Hosted siano online in caso di log mancanti. 


## <a name="self-hosted-ir-general-failure-or-error"></a>Errore generale del runtime di integrazione self-hosted

### <a name="out-of-memory-issue"></a>Problema di memoria insufficiente

#### <a name="symptoms"></a>Sintomi

Il problema "OutOfMemoryException" si verifica quando si tenta di eseguire l'attività di ricerca con IR collegato o il runtime di integrazione self-hosted.

#### <a name="cause"></a>Causa

Le nuove attività possono essere soddisfatte con il problema di memoria insufficiente (OutOfMemory) se il computer IR ha un utilizzo elevato della memoria al momento. Il problema potrebbe essere causato da una vasta scala di esecuzione di attività simultanee e l'errore si verifica in base alla progettazione.

#### <a name="resolution"></a>Risoluzione

Controllare l'utilizzo delle risorse e l'esecuzione di attività simultanee nel nodo IR. Modificare il tempo interno e il trigger delle esecuzioni di attività per evitare una quantità eccessiva di esecuzione nello stesso nodo IR nello stesso momento.


### <a name="tlsssl-certificate-issue"></a>Problemi per i certificati TLS/SSL

#### <a name="symptoms"></a>Sintomi

Durante il tentativo di abilitazione dei certificati TLS/SSL (avanzati) dalla **Gestione configurazione del runtime di integrazione self-hosted** -> **Accesso remoto da intranet**, dopo aver selezionato i certificati TLS/SSL, viene visualizzato l'errore seguente:

`Remote access settings are invalid. Identity check failed for outgoing message. The expected DNS identity of the remote endpoint was ‘abc.microsoft.com’ but the remote endpoint provided DNS claim ‘microsoft.com’. If this is a legitimate remote endpoint, you can fix the problem by explicitly specifying DNS identity ‘microsoft.com’ as the Identity property of EndpointAddress when creating channel proxy.`

Nel caso presente, l'utente sta usando il certificato con "microsoft.com" come ultimo elemento.

#### <a name="cause"></a>Causa

Questo è un problema noto in WCF: La verifica TLS/SSL di WCF controlla solo l'ultimo DNSName in SAN. 

#### <a name="resolution"></a>Risoluzione

Il certificato con carattere jolly è supportato dal runtime di integrazione self-hosted Azure Data Factory v2. Questo problema avviene generalmente a causa di un certificato SSL errato. È necessario che l'ultimo DNSName in SAN sia valido. Seguire i passaggi seguenti per verificarlo. 
1.  Aprire Management Console, controllare il nome del *soggetto* e il *nome alternativo del soggetto* dai dettagli del certificato. Nel caso precedente, ad esempio, l'ultimo elemento nel *nome alternativo del soggetto*, ovvero "DNS Name = Microsoft.com.com", non è legittimo.
2.  Per rimuovere il nome DNS errato, contattare la società di emissione del certificato.

### <a name="concurrent-jobs-limit-issue"></a>Problemi nelle limiti dei processi simultanei

#### <a name="symptoms"></a>Sintomi

Durante il tentativo di aumentare il limite dei processi simultanei dall'interfaccia utente di Azure Data Factory, si blocca su *aggiornamento in corso*.
Il valore massimo di processi simultanei è stato impostato su 24 e si desidera aumentare il numero per ottenere processi più rapidi. Il valore minimo che può essere inserito è pari a 3 e il valore massimo è pari a 32. È stato aumentato il valore da 24 a 32 e viene visualizzato il pulsante *Aggiorna* nell'interfaccia utente che si è bloccato durante l' *aggiornamento* , come si può vedere di seguito. Dopo aver aggiornato, il cliente vede ancora il valore 24 e non viene aggiornato a 32.

![Aggiornamento dello stato](media/self-hosted-integration-runtime-troubleshoot-guide/updating-status.png)

#### <a name="cause"></a>Causa

Esiste un limite per l'impostazione dal momento che il valore dipende dal logicCore e della memoria del computer, è possibile modificarlo solo con un valore minore ad esempio 24 e osservare il risultato.

> [!TIP] 
> - Per informazioni dettagliate sul numero di core per la logica e su come trovare il numero di core per la logica del computer, vedere [questo articolo](https://www.top-password.com/blog/find-number-of-cores-in-your-cpu-on-windows-10/).
> - Per informazioni dettagliate su come calcolare Math. log, vedere [questo articolo](https://www.rapidtables.com/calc/math/Log_Calculator.html).


### <a name="self-hosted-ir-ha-ssl-certificate-issue"></a>Problemi per i certificati SSL a disponibilità elevata del runtime di integrazione self-hosted

#### <a name="symptoms"></a>Sintomi

Il nodo di lavoro del runtime di integrazione self-hosted ha rilevato l'errore seguente:

`Failed to pull shared states from primary node net.tcp://abc.cloud.corp.Microsoft.com:8060/ExternalService.svc/. Activity ID: XXXXX The X.509 certificate CN=abc.cloud.corp.Microsoft.com, OU=test, O=Microsoft chain building failed. The certificate that was used has a trust chain that cannot be verified. Replace the certificate or change the certificateValidationMode. The revocation function was unable to check revocation because the revocation server was offline.`

#### <a name="cause"></a>Causa

Quando vengono gestiti casi relativi all'handshake SSL/TLS, è possibile riscontrare problemi relativi alla verifica della catena di certificati. 

#### <a name="resolution"></a>Risoluzione

- Ecco un modo rapido e intuitivo per risolvere gli errori di compilazione della catena di certificati X. 509.
 
    1. Esportare il certificato, che deve essere verificato. Andare in gestione certificati computer, trovare il certificato che si desidera controllare e fare clic con il tasto destro su **Tutte le attività** -> **Esporta**.
    
        ![Esporta attività](media/self-hosted-integration-runtime-troubleshoot-guide/export-tasks.png)

    2. Copiare il certificato esportato nel computer client. 
    3. Sul lato client eseguire il comando seguente in CMD. Assicurarsi di aver sostituito i *\<certificate path>* *\<output txt file path>* segnaposto e i segnaposto con i percorsi correlati.
    
        ```
        Certutil -verify -urlfetch    <certificate path>   >     <output txt file path> 
        ```

        Ad esempio:

        ```
        Certutil -verify -urlfetch c:\users\test\desktop\servercert02.cer > c:\users\test\desktop\Certinfo.txt
        ```
    4. Controllare se ci sono errori nell'output del file con estensione .txt. È possibile trovare il riepilogo degli errori alla fine del file con estensione .txt.

        Ad esempio: 

        ![Riepilogo errori](media/self-hosted-integration-runtime-troubleshoot-guide/error-summary.png)

        Se non viene visualizzato alcun errore alla fine del file di log, come illustrato di seguito, è possibile considerare che la catena di certificati è stata compilata correttamente nel computer client.
        
        ![Nessun errore nel file di log](media/self-hosted-integration-runtime-troubleshoot-guide/log-file.png)      

- Nel file di certificato sono configurati AIA, CDP e OCSP. Possiamo archiviarlo in modo più intuitivo.
 
    1. È possibile ottenere queste informazioni controllando i dettagli di un certificato.
    
        ![Dettagli certificato](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-detail.png)
    1. Eseguire il comando seguente. Assicurarsi di aver sostituito *\<certificate path>* il segnaposto con il percorso correlato del certificato.
    
        ```
          Certutil   -URL    <certificate path> 
        ```
    1. Quindi verrà aperto lo **strumento di recupero URL**. È possibile verificare i certificati da AIA, CDP e OCSP facendo clic sul pulsante **Recuperare**.

        ![Pulsante recupero](media/self-hosted-integration-runtime-troubleshoot-guide/retrieval-button.png)
 
        La catena di certificati può essere compilata correttamente se il certificato da AIA è "Verificato" e il certificato da CDP o OCSP è "Verificato".

        Se si verifica un errore durante il recupero di AIA o CDP, collaborare con il team di rete per preparare il computer client per la connessione all'URL di destinazione. Sarà sufficiente se è possibile verificare il percorso HTTP o il percorso LDAP.

### <a name="self-hosted-ir-could-not-load-file-or-assembly"></a>Il runtime di integrazione self-hosted non può caricare il file o l'assembly

#### <a name="symptoms"></a>Sintomi

`Could not load file or assembly 'XXXXXXXXXXXXXXXX, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified. Activity ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3`
 
Ad esempio: 

`Could not load file or assembly 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified. Activity ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3`

#### <a name="cause"></a>Causa

Se si esegue monitoraggio processi, è possibile visualizzare i risultati seguenti:

[![Monitoraggio processi](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png)](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png#lightbox)

> [!TIP] 
> È possibile impostare il filtro come illustrato nello screenshot seguente.
> Indica che la dll **System. ValueTuple** non si trova nella cartella correlata alla GAC oppure in *c:\Programmi\Microsoft Integration Runtime\4.0\Gateway* o nella cartella c:\Programmi\Microsoft *Integration Runtime\4.0\Shared*
> In sostanza, caricherà prima di tutto la dll dalla cartella *GAC*, quindi da *Condivisi* e infine dalla cartella *Gateway*. Pertanto, è possibile inserire l'estensione .dll in qualsiasi percorso che può essere utile.

![Configurare i filtri](media/self-hosted-integration-runtime-troubleshoot-guide/set-filters.png)

#### <a name="resolution"></a>Risoluzione

È possibile scoprire che la **System.ValueTuple.dll** si trova nella cartella *c:\Programmi\Microsoft Integration Runtime\4.0\Gateway\DataScan* Copiare il **System.ValueTuple.dll** nella cartella C:\Programmi\Microsoft *Integration Runtime\4.0\Gateway* per risolvere il problema.

È possibile usare lo stesso metodo per risolvere i problemi mancanti di assembly o file.

#### <a name="more-information"></a>Altre informazioni

Il motivo per cui viene visualizzato il System.ValueTuple.dll in *%windir%\Microsoft.NET\assembly* e *%windir%\assembly* è che si tratta di un comportamento .NET. 

Dall'errore riportato di seguito, è possibile vedere chiaramente che l'assembly *System. ValueTuple* non è presente. Questo problema si verifica quando l'applicazione tenta di controllare l'assembly *System.ValueTuple.dll*.
 
`<LogProperties><ErrorInfo>[{"Code":0,"Message":"The type initializer for 'Npgsql.PoolManager' threw an exception.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.TypeInitializationException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[{"Code":0,"Message":"Could not load file or assembly 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.IO.FileNotFoundException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[]}]}]</ErrorInfo></LogProperties>`
 
Per ulteriori informazioni sulla GAC, vedere [questo articolo](/dotnet/framework/app-domains/gac).


### <a name="how-to-audit-self-hosted-ir-key-missing"></a>Come controllare la mancanza di una chiave di runtime di integrazione self-hosted

#### <a name="symptoms"></a>Sintomi

Il runtime di integrazione self-hosted passa improvvisamente alla modalità offline senza chiave, nel registro eventi viene visualizzato il messaggio di errore seguente: `Authentication Key is not assigned yet`

![Chiave di autenticazione mancante](media/self-hosted-integration-runtime-troubleshoot-guide/key-missing.png)

#### <a name="cause"></a>Causa

- Il nodo di runtime di integrazione self-hosted o il nodo di runtime di integrazione self-hosted logico nel portale sono stati eliminati.
- È stata eseguita una disinstallazione corretta.

#### <a name="resolution"></a>Risoluzione

Se nessuna delle cause precedenti si applica, è possibile passare alla cartella: *%ProgramData%\Microsoft\Data Transfer\DataManagementGateway* e verificare se il file denominato **configurazioni** è stato eliminato. Se viene eliminato, seguire le istruzioni riportate [qui](https://www.netwrix.com/how_to_detect_who_deleted_file.html) per controllare chi elimina il file.

![Verifica file di configurazione](media/self-hosted-integration-runtime-troubleshoot-guide/configurations-file.png)


### <a name="cannot-use-self-hosted-ir-to-bridge-two-on-premises-data-stores"></a>Non è possibile usare il runtime di integrazione self-hosted per collegare due archivi dati locali

#### <a name="symptoms"></a>Sintomi

Dopo aver creato i runtime di integrazione self-hosted per gli archivi dati di origine e di destinazione, è necessario connettere i due runtime di integrazione insieme per completare una copia. Se gli archivi dati sono configurati in reti virtuali diversi o non riescono a comprendere il meccanismo del gateway, si verificano errori simili ai seguenti: *Impossibile trovare il driver dell'origine nel* runtime di integrazione di destinazione. *non è possibile accedere all'origine dal runtime di integrazione di destinazione*.
 
#### <a name="cause"></a>Causa

Il runtime di integrazione self-hosted è progettato come un nodo centrale di un'attività di copia, non di un agente client che è necessario installare per ogni archivio dati.
 
Nel caso soprastante, è necessario creare il servizio collegato per ogni archivio dati con lo stesso runtime di integrazione e il runtime di integrazione deve essere in grado di accedere a entrambi gli archivi dati attraverso la rete. Indipendentemente dal fatto che il runtime di integrazione sia installato con l'archivio dati di origine, con l'archivio dati di destinazione o in un terzo computer, se vengono creati due servizi collegati con runtime di integrazione diversi, ma usati nella stessa attività di copia, verrà usato il runtime di integrazione di destinazione e i driver per entrambi gli archivi dati dovranno essere installati nel computer di runtime di integrazione di destinazione.

#### <a name="resolution"></a>Risoluzione

Installare i driver sia per l'origine che per la destinazione nel runtime di integrazione di destinazione e assicurarsi che sia in grado di accedere all'archivio dati di origine.
 
Se il traffico non è in grado di passare attraverso la rete tra due archivi dati (ad esempio, sono configurati in due reti virtuali), non è possibile completare la copia in un'attività anche se il runtime di integrazione è installato. In tal caso, è possibile creare due attività di copia con due runtime di integrazione, ciascuna in una rete virtuale: 1 runtime di integrazione per la copia dall'archivio dati 1 all'Archiviazione BLOB di Azure, un altro per la copia dall'Archiviazione BLOB di Azure all'archivio dati 2. Questo potrebbe simulare la necessità di usare il runtime di integrazione per creare un bridge che connette due archivi dati disconnessi.


### <a name="credential-sync-issue-causes-credential-lost-from-ha"></a>Il problema di sincronizzazione delle credenziali causa la perdita delle credenziali da disponibilità elevata

#### <a name="symptoms"></a>Sintomi

Le credenziali "XXXXXXXXXX" dell'origine dati sono state eliminate dal nodo corrente di Integration Runtime con payload "quando si elimina il servizio di collegamento nel portale di Azure oppure il payload dell'attività è errato, creare il nuovo servizio di collegamento con le proprie credenziali".

#### <a name="cause"></a>Causa

Il runtime di integrazione self-hosted è compilato in modalità a disponibilità elevata con due nodi, ma non si trovano nello stato di sincronizzazione delle credenziali, il che significa che le credenziali archiviate nel nodo dispatcher non vengono sincronizzate con altri nodi di lavoro. Se si verifica un failover dal nodo dispatcher al nodo di lavoro ma le credenziali erano presenti solo nel nodo dispatcher precedente, se si tenta di accedere alle credenziali, l'accesso sarà negato e si verificherà l'errore soprastante.

#### <a name="resolution"></a>Risoluzione

L'unico modo per evitare questo problema consiste nel verificare che due nodi si trovino nello stato di sincronizzazione delle credenziali. In caso contrario, è necessario reinserire le credenziali per il nuovo dispatcher.


### <a name="cannot-choose-the-certificate-due-to-private-key-missing"></a>Non è possibile scegliere il certificato a causa di una chiave privata mancante

#### <a name="symptoms"></a>Sintomi

1.  Importare un file con estensione .pfx nell'archivio certificati.
2.  Quando si seleziona il certificato tramite l'interfaccia utente di Configuration Manager di runtime di integrazione, si verifica l'errore seguente:

    ![Chiave privata mancante](media/self-hosted-integration-runtime-troubleshoot-guide/private-key-missing.png)

#### <a name="cause"></a>Causa

- L'account utente ha autorizzazione limitata e non può accedere alla chiave privata.
- Il certificato è stato generato come firma ma non come scambio di chiave.

#### <a name="resolution"></a>Risoluzione

1.  Usare un account con autorizzazione alla chiave privata per il funzionamento dell'interfaccia utente.
2.  Eseguire il comando seguente per importare il certificato:
    
    ```
    certutil -importpfx FILENAME.pfx AT_KEYEXCHANGE
    ```


## <a name="self-hosted-ir-setup"></a>Configurazione del runtime di integrazione self-hosted

### <a name="the-integration-runtime-registration-error"></a>Errore di registrazione del Integration Runtime 

#### <a name="symptoms"></a>Sintomi

A volte si vuole eseguire il runtime di integrazione self-hosted in un account diverso per i motivi seguenti:
- I criteri aziendali non consentono l'account del servizio.
- È richiesta l'autenticazione.

Dopo aver modificato l'account del servizio nel pannello del servizio, è possibile che il Integration Runtime smette di funzionare.

![Errore di registrazione IR](media/self-hosted-integration-runtime-troubleshoot-guide/ir-registration-error.png)

#### <a name="cause"></a>Causa

Sono disponibili molte risorse che vengono concesse solo all'account del servizio. Quando si modifica l'account del servizio in un altro account, l'autorizzazione di tutte le risorse dipendenti rimane invariata.

#### <a name="resolution"></a>Risoluzione

Per verificare l'errore, passare al registro eventi Integration Runtime.

![Registro eventi IR](media/self-hosted-integration-runtime-troubleshoot-guide/ir-event-log.png)

Se l'errore si presenta come sopra *UnauthorizedAccessException*, seguire le istruzioni seguenti:


1. Controllare l'account del servizio di accesso *DIAHostService* nel pannello del servizio Windows.

    ![Account del servizio di accesso](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

2. Controllare se l'account del servizio di accesso dispone dell'autorizzazione R/W per la cartella: *%ProgramData%\Microsoft\DataTransfer\DataManagementGateway*.

    - Per impostazione predefinita, se l'account di accesso al servizio non è stato modificato, deve disporre dell'autorizzazione di R/W.

        ![Autorizzazione del servizio](media/self-hosted-integration-runtime-troubleshoot-guide/service-permission.png)

    - Se è stato modificato l'account di accesso al servizio, attenersi alla procedura seguente per attenuare il problema:
        1. Pulisci Disinstalla il runtime di integrazione self-hosted corrente.
        1. Installare i bit del runtime di integrazione self-hosted.
        1. Per modificare l'account del servizio, seguire le istruzioni seguenti: 
            1. Passare alla cartella di installazione del runtime di integrazione self-hosted, passare alla cartella: *Microsoft Integration Runtime\4.0\Shared*.
            1. Avviare una riga di comando utilizzando privilegi elevati. Sostituire *\<user>* e *\<password>* con il nome utente e la password e quindi eseguire il comando seguente:
                       
                ```
                dmgcmd.exe -SwitchServiceAccount "<user>" "<password>"
                ```
            1. Se si desidera passare all'account LocalSystem, assicurarsi di utilizzare un formato corretto per l'account. Di seguito è riportato un esempio del formato corretto:

                ```
                dmgcmd.exe -SwitchServiceAccount "NT Authority\System" ""
                ```         
                **Non** usare Format come illustrato di seguito:

                ```
                dmgcmd.exe -SwitchServiceAccount "LocalSystem" ""
                ```              
            1. In alternativa, poiché il sistema locale ha privilegi più elevati rispetto all'amministratore, è anche possibile modificarlo direttamente in "servizi".
            1. È possibile usare l'utente locale/di dominio per l'account di accesso del servizio IR.            
        1. Registrare il Integration Runtime.

Se l'errore viene visualizzato come, *non è stato possibile avviare il servizio ' Integration Runtime Service ' (DIAHostService). Verificare di disporre di privilegi sufficienti per avviare i servizi di sistema*, seguire le istruzioni seguenti:

1. Controllare l'account del servizio di accesso *DIAHostService* nel pannello del servizio Windows.
   
    ![Account del servizio di accesso](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

2. Controllare se l'account del servizio di accesso dispone dell'autorizzazione **Accedi come servizio** per avviare il servizio Windows:

    ![Accesso come servizio](media/self-hosted-integration-runtime-troubleshoot-guide/logon-as-service.png)

#### <a name="more-information"></a>Altre informazioni

Se nessuno dei due modelli precedenti nella risoluzione si applica nel caso, provare a raccogliere i registri eventi di Windows seguenti: 
- Registri applicazioni e servizi-> Integration Runtime
- Log di Windows-applicazione >

### <a name="cannot-find-register-button-to-register-a-self-hosted-ir"></a>Il pulsante Registra per registrare un runtime di integrazione self-hosted non è stato trovato    

#### <a name="symptoms"></a>Sintomi

Non è stato possibile trovare il pulsante **Register** nell'interfaccia utente di Configuration Manager durante la registrazione di un runtime di integrazione self-hosted.

![Pulsante nessun registro](media/self-hosted-integration-runtime-troubleshoot-guide/no-register-button.png)

#### <a name="cause"></a>Causa

Dal rilascio del *Integration Runtime 3,0*, il pulsante **registra** in un nodo Integration Runtime esistente è stato rimosso per consentire un ambiente più pulito e sicuro. Se un nodo è stato registrato in alcuni Integration Runtime (online o meno), per registrarlo di nuovo in un altro Integration Runtime è necessario disinstallare il nodo precedente, quindi installare e registrare il nodo.

#### <a name="resolution"></a>Risoluzione

1. Passare al pannello di controllo per disinstallare il Integration Runtime esistente.

    > [!IMPORTANT] 
    > Nel processo seguente selezionare Sì. Non conserva i dati durante il processo di disinstallazione.

    ![Eliminare i dati](media/self-hosted-integration-runtime-troubleshoot-guide/delete-data.png)

1. Se non si dispone dell'MSI del programma di installazione di Integration Runtime, passare all' [area download](https://www.microsoft.com/en-sg/download/details.aspx?id=39717) per scaricare la Integration runtime più recente.
1. Installare il file MSI e registrare il Integration Runtime.


### <a name="unable-to-register-the-self-hosted-ir-due-to-localhost"></a>Impossibile registrare il runtime di integrazione self-hosted a causa di localhost    

#### <a name="symptoms"></a>Sintomi

Non è possibile registrare il runtime di integrazione self-hosted in un nuovo computer quando get_LoopbackIpOrName.

**Debug:** Si è verificato un errore di Runtime.
L'inizializzatore di tipo per ' Microsoft. datatransfer. DIAgentHost. DataSourceCache ' ha generato un'eccezione.
Si è verificato un errore irreversibile durante la ricerca nel database.
 
**Dettagli eccezione:** System. TypeInitializationException: l'inizializzatore di tipo per ' Microsoft. datatransfer. DIAgentHost. DataSourceCache ' ha generato un'eccezione. ---> System .NET. Sockets. SocketException: si è verificato un errore irreversibile durante una ricerca nel database in System .NET. DNS. funzione getaddrinfo (nome stringa).

#### <a name="cause"></a>Causa

Il problema si verifica in genere durante la risoluzione del localhost.

#### <a name="resolution"></a>Risoluzione

Usare localhost 127.0.0.1 per ospitare il file e risolvere il problema.


### <a name="self-hosted-setup-failed"></a>Installazione self-hosted non riuscita    

#### <a name="symptoms"></a>Sintomi

Non è possibile disinstallare un runtime di integrazione esistente oppure installare un nuovo IR o aggiornare un runtime di integrazione esistente a un nuovo runtime di integrazione.

#### <a name="cause"></a>Causa

L'installazione dipende dal servizio Windows Installer. Esistono motivi Variant che possono causare problemi di installazione:
- Spazio su disco insufficiente
- Mancanza di autorizzazioni
- Il servizio NT è bloccato per qualche motivo
- L'utilizzo della CPU è troppo elevato
- Il file MSI è ospitato in un percorso di rete lento
- Alcuni file di sistema o registri sono stati modificati inavvertitamente


### <a name="ir-service-account-failed-to-fetch-certificate-access"></a>L'account del servizio IR non è riuscito a recuperare l'accesso al certificato

#### <a name="symptoms"></a>Sintomi

Quando si installa il runtime di integrazione self-hosted tramite il Microsoft Integration Runtime Configuration Manager, viene generato un certificato con una CA attendibile. Non è stato possibile applicare il certificato per crittografare la comunicazione tra due nodi. 

Le informazioni sull'errore sono indicate di seguito: 

`Failed to change Intranet communication encryption mode: Failed to grant Integration Runtime service account the access of to the certificate 'XXXXXXXXXX'. Error code 103`

![Non è stato possibile concedere l'accesso al certificato dell'account del servizio IR](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-account-certificate-error.png)

#### <a name="cause"></a>Causa

Il certificato usa KSP (provider di archiviazione chiavi), che non è ancora supportato. Per questo motivo, è supportato solo il certificato CSP (provider del servizio di crittografia).

#### <a name="resolution"></a>Risoluzione

Per questo caso è consigliato il certificato CSP.

**Soluzione 1:** Usare il comando seguente per importare il certificato:

```
Certutil.exe -CSP "CSP or KSP" -ImportPFX FILENAME.pfx 
```

![Usare certutil](media/self-hosted-integration-runtime-troubleshoot-guide/use-certutil.png)

**Soluzione 2:** Conversione di certificati:

openssl PKCS12-in .\XXXX.pfx-out. \ xxxx_new. pem-pass password:*\<EnterPassword>*

openssl PKCS12-export-in. \ xxxx_new. pem-out xxxx_new. pfx

Prima e dopo la conversione:

![Prima della modifica del certificato](media/self-hosted-integration-runtime-troubleshoot-guide/before-certificate-change.png)

![Dopo la modifica del certificato](media/self-hosted-integration-runtime-troubleshoot-guide/after-certificate-change.png)

### <a name="self-hosted-integration-runtime-version-5x"></a>Self-Hosted Integration Runtime versione 5. x
Per l'aggiornamento alla versione 5. x di Azure Data Factory runtime di integrazione self-hosted, è necessario **4.7.2 di runtime di .NET Framework** o versione successiva. Nella pagina di download sono disponibili collegamenti di download per la versione 4. x più recente e le due versioni 5. x più recenti. 


Per i clienti di ADF V2:
- Se l'aggiornamento automatico è acceso ed è già stato eseguito l'aggiornamento del runtime di .NET Framework a 4.7.2 o versione successiva, il runtime di integrazione self-hosted verrà aggiornato automaticamente alla versione 5. x più recente.
- Se l'aggiornamento automatico è acceso e non è stato eseguito l'aggiornamento del runtime di .NET Framework a 4.7.2 o versione successiva, il runtime di integrazione self-hosted non verrà aggiornato automaticamente alla versione 5. x più recente. Il runtime di integrazione self-hosted resterà nella versione 4. x corrente. È possibile visualizzare un avviso relativo all'aggiornamento del runtime di .NET Framework nel portale e nel client del runtime di integrazione self-hosted.
- Se l'aggiornamento automatico è disattivato ed è già stato eseguito l'aggiornamento del runtime di .NET Framework a 4.7.2 o versione successiva, è possibile scaricare manualmente la versione 5. x più recente e installarla nel computer.
- Se l'aggiornamento automatico è disattivato e non è stato eseguito l'aggiornamento del runtime di .NET Framework a 4.7.2 o versione successiva. Quando si tenta di installare manualmente la chiave di sistema 5. x e si registra la chiave, sarà necessario aggiornare prima il runtime di .NET Framework.


Per i clienti di ADF V1:
- Il runtime di integrazione self-hosted 5. X non supporta ADF V1.
- Il runtime di integrazione self-hosted verrà aggiornato automaticamente alla versione più recente di 4. x. E l'ultima versione di 4. x non scadrà. 
- Se si prova a installare manualmente il runtime di integrazione self-hosted 5. x e si registra la chiave, viene indicato che il runtime di integrazione self-hosted 5. x non supporta V1.


## <a name="self-hosted-ir-connectivity-issues"></a>Problemi di connettività IR self-hosted

### <a name="self-hosted-integration-runtime-cant-connect-to-cloud-service"></a>Il runtime di integrazione self-hosted non può connettersi al servizio cloud

#### <a name="symptoms"></a>Sintomi

![Problema di connessione del runtime di integrazione self-hosted](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>Causa 

Il runtime di integrazione self-hosted non riesce a connettersi al servizio Data Factory (back-end). Questo problema è in genere causato dalle impostazioni di rete nel firewall.

#### <a name="resolution"></a>Risoluzione

1. Controllare se il servizio Integration Runtime è in esecuzione.
    
   ![Stato di esecuzione del servizio Integration Runtime self-hosted](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. Se il servizio è in esecuzione, andare al passaggio 3.

1. Se nel runtime di integrazione self-hosted non è configurato alcun proxy (impostazione predefinita), eseguire il comando di PowerShell seguente nel computer in cui è installato il runtime di integrazione self-hosted:

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > L'URL del servizio può variare in base alla posizione del data factory. È possibile trovare l'URL del servizio in **interfaccia utente di Azure Data Factory** > **Connessioni** > **Runtime di integrazione** > **Edit Self-hosted IR** (Modifica runtime di integrazione self-hosted)  > **Nodi** > **View Service URLs** (Visualizza URL servizi).
            
    La risposta prevista è la seguente:
            
    ![Risposta del comando di PowerShell](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. Se non si riceve la risposta prevista, usare uno dei metodi seguenti in base alla situazione:
            
    * Se viene visualizzato il messaggio "Non è stato possibile risolvere il nome remoto", significa che è presente un problema a livello di DNS (Domain Name System). Per risolvere il problema, contattare il team addetto alla rete.
    * Se viene visualizzato un messaggio che avvisa che il certificato SSL/TLS non è attendibile, verificare se il certificato per https://wu2.frontend.clouddatahub.net/ è attendibile nel computer e quindi installare il certificato pubblico usando Gestione certificati. Questa azione dovrebbe attenuare il problema.
    * Passare a **Windows** > **Visualizzatore eventi (registri)**  > **Registri applicazioni e servizi** > **Integration Runtime** e verificare la presenza di errori causati dal DNS, da una regola del firewall o dalle impostazioni di rete. Se si rileva un errore di questo tipo, forzare la chiusura della connessione. Poiché ogni azienda ha impostazioni di rete personalizzate, contattare il team addetto alla rete per risolvere questi problemi.

1. Se il proxy è stato configurato nel runtime di integrazione self-hosted, verificare che il server proxy possa accedere all'endpoint del servizio. Per un comando di esempio, vedere [PowerShell, Web Requests, and Proxies](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies).    
                
    ```powershell
    $user = $env:username
    $webproxy = (get-itemproperty 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet
    Settings').ProxyServer
    $pwd = Read-Host "Password?" -assecurestring
    $proxy = new-object System.Net.WebProxy
    $proxy.Address = $webproxy
    $account = new-object System.Net.NetworkCredential($user,[Runtime.InteropServices.Marshal]::PtrToStringAuto([Runtime.InteropServices.Marshal]::SecureStringToBSTR($pwd)), "")
    $proxy.credentials = $account
    $url = "https://wu2.frontend.clouddatahub.net/"
    $wc = new-object system.net.WebClient
    $wc.proxy = $proxy
    $webpage = $wc.DownloadData($url)
    $string = [System.Text.Encoding]::ASCII.GetString($webpage)
    $string
    ```

La risposta prevista è la seguente:
            
![Risposta del comando di PowerShell 2](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> Considerazioni sul proxy:
> *    Verificare se il server proxy deve essere inserito nell'elenco Destinatari attendibili. In tal caso, verificare che [questi domini](./data-movement-security-considerations.md#firewall-requirements-for-on-premisesprivate-network) siano nell'elenco Destinatari attendibili.
> *    Verificare che il certificato TLS/SSL "wu2.frontend.clouddatahub.net/" sia attendibile nel server proxy.
> *    Se si usa l'autenticazione Active Directory sul proxy, sostituire l'account del servizio con l'account utente che può accedere al proxy come "servizio Integration Runtime".

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>Messaggio di errore: Il nodo del runtime di integrazione self-hosted o il runtime di integrazione self-hosted logico è in uno stato di inattività o di esecuzione con limitazioni

#### <a name="cause"></a>Causa 

Il nodo del runtime di integrazione self-hosted potrebbe essere in uno stato **inattivo**, come illustrato nello screenshot seguente:

![Nodo del runtime di integrazione self-hosted inattivo](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

Questo comportamento si verifica quando i nodi non possono comunicare tra loro.

#### <a name="resolution"></a>Risoluzione

1. Accedere alla macchina virtuale ospitata dal nodo. In **Registri applicazioni e servizi** > **Integration Runtime** aprire il Visualizzatore eventi e filtrare i log degli errori.

1. Verificare se un log degli errori contiene l'errore seguente: 
    
    ```
    System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
    ```
       
1. Se viene visualizzato questo errore, eseguire il comando seguente nella riga di comando: 

   ```
   telnet 10.2.4.10 8060
   ```
   
1. Se viene visualizzato l'errore seguente, contattare il reparto IT per assistenza per la risoluzione del problema. Dopo aver completato Telnet, contattare supporto tecnico Microsoft se si verificano ancora problemi con lo stato del nodo runtime di integrazione.
        
   ![Errore della riga di comando](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1. Controllare se nel log degli errori sono contenuti gli elementi seguenti:

    ```
    Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)
    ```
    
1. Per risolvere il problema, provare uno o entrambi i metodi seguenti:
    - Inserire tutti i nodi nello stesso dominio.
    - Aggiungere l'indirizzo IP al mapping dell'host in tutti i file host della macchina virtuale ospitata.

### <a name="connectivity-issue-between-self-hosted-ir-and-data-factory-or-self-hosted-ir-and-data-sourcesink"></a>Problema di connettività tra il runtime di integrazione self-hosted e Data Factory o il runtime di integrazione self-hosted e l'origine dati

Per risolvere il problema relativo alla connettività di rete, è necessario sapere come raccogliere la traccia di rete, comprendere come utilizzarla e [analizzare la traccia Netmon](#how-to-analyze-netmon-trace) prima di applicare gli strumenti NetMon in casi reali dal runtime di integrazione self-hosted.

#### <a name="symptoms"></a>Sintomi

In alcuni casi, quando si risolvono i problemi di connettività, ad esempio al di sotto di un runtime di integrazione self-hosted e Data Factory: 

![Richiesta HTTP non riuscita](media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png)

O tra il runtime di integrazione self-hosted e l'origine dati/sink, si verificheranno gli errori seguenti:

`Copy failed with error:Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server: ‘IP address’`

`One or more errors occurred. An error occurred while sending the request. The underlying connection was closed: An unexpected error occurred on a receive. Unable to read data from the transport connection: An existing connection was forcibly closed by the remote host. An existing connection was forcibly closed by the remote host Activity ID.`

#### <a name="resolution"></a>Risoluzione:

Quando si verificano problemi sopra indicati, fare riferimento alle istruzioni seguenti per la risoluzione dei problemi:

Eseguire la traccia Netmon e analizzarla ulteriormente.
- In primo luogo, è possibile impostare il filtro per visualizzare eventuali reimpostazioni dal server al lato client. Nell'esempio seguente è possibile vedere che il lato server è Data Factory server.

    ![Server di data factory](media/self-hosted-integration-runtime-troubleshoot-guide/data-factory-server.png)

- Quando si ottiene il pacchetto di reimpostazione, è possibile trovare la conversazione seguendo TCP.

    ![Trova conversazione](media/self-hosted-integration-runtime-troubleshoot-guide/find-conversation.png)

- È quindi possibile ottenere la conversione tra il client e il server Data Factory riportato di seguito rimuovendo il filtro.

    ![Ottieni conversazione](media/self-hosted-integration-runtime-troubleshoot-guide/get-conversation.png)
- In base alla traccia Netmon raccolta, è possibile stabilire che il totale TTL (TimeToLive) è 64. In base ai **valori predefiniti TTL e limite hop** indicati in [questo articolo](https://packetpushers.net/ip-time-to-live-and-hop-limit-basics/) (come illustrato di seguito), è possibile notare che si tratta del sistema Linux che reimposta il pacchetto e causa la disconnessione.

    I valori TTL predefiniti e limite hop variano tra diversi sistemi operativi. di seguito sono riportate le impostazioni predefinite per alcune:
    - Kernel Linux 2,4 (circa 2001): 255 per TCP, UDP e ICMP
    - Kernel Linux 4,10 (2015): 64 per TCP, UDP e ICMP
    - Windows XP (2001): 128 per TCP, UDP e ICMP
    - Windows 10 (2015): 128 per TCP, UDP e ICMP
    - Windows Server 2008:128 per TCP, UDP e ICMP
    - Windows Server 2019 (2018): 128 per TCP, UDP e ICMP
    - macOS (2001): 64 per TCP, UDP e ICMP

    ![TTL 61](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-61.png)
    
    Tuttavia, viene visualizzato come 61 anziché 64 nell'esempio precedente, perché quando il pacchetto di rete raggiunge la destinazione, deve passare attraverso hop diversi come i router/dispositivi di rete. Il numero di router/dispositivi di rete verrà dedotto nella durata (TTL) finale.
    In questo caso, è possibile notare che la reimpostazione può essere inviata dal sistema Linux con TTL 64.

- È necessario controllare il quarto hop dal runtime di integrazione self-hosted per confermare la provenienza del dispositivo di reimpostazione.
 
    *Pacchetto di rete dal sistema Linux A con TTL 64-> B TTL 64 meno 1 = 63-> C TTL 63 meno 1 = 62-> TTL 62 meno 1 = 61 runtime di integrazione self-hosted*

- In una situazione ideale, la durata (TTL) sarà 128, il che significa che Windows System esegue il Data Factory. Come illustrato nell'esempio seguente, *128 – 107 = 21 hop*, vale a dire che 21 hop per il pacchetto sono stati inviati da Data Factory al runtime di integrazione self-hosted durante l'handshake TCP 3.
 
    ![TTL 107](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-107.png)

    Pertanto, è necessario coinvolgere il team di rete per verificare quale sia il quarto hop dal runtime di integrazione self-hosted. Se si tratta del firewall come sistema Linux, controllare tutti i log sui motivi per cui il dispositivo Reimposta il pacchetto dopo l'handshake TCP 3. Tuttavia, se non si è certi della posizione in cui eseguire l'analisi, provare a ottenere la traccia Netmon dal runtime di integrazione self-hosted e dal firewall insieme nel tempo problematico per determinare quale dispositivo può reimpostare il pacchetto e causare la disconnessione. In questo caso, è anche necessario coinvolgere il team di rete per andare avanti.

### <a name="how-to-analyze-netmon-trace"></a>Come analizzare la traccia Netmon

> [!NOTE] 
> L'istruzione seguente è applicabile alla traccia Netmon. Poiché la traccia Netmon non è attualmente supportata, è possibile utilizzare Wireshark come lo stesso.

Quando si prova a usare telnet **8.8.8.8 888** con la traccia Netmon raccolta, si dovrebbe vedere la traccia seguente:

![traccia Netmon 1](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-1.png)

![traccia Netmon 2](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-2.png)
 

Ciò significa che non è stato possibile eseguire la connessione TCP al lato server **8.8.8.8** in base alla porta **888**, quindi vengono visualizzati due pacchetti aggiuntivi **SynReTransmit** . Poiché l'origine **self-HOST2** non è in grado di eseguire la connessione a **8.8.8.8** al primo pacchetto, la connessione verrà mantenuta.

> [!TIP]
> - È possibile fare clic su **Carica filtro**  ->  **standard**  ->  **indirizzi**  ->  **IPv4 indirizzi**.
> - Input **IPv4. Address = = 8.8.8.8** come filtro, quindi fare clic su **applica**. Successivamente, si vedrà solo la comunicazione dal computer locale al **8.8.8.8** di destinazione.

![filtra indirizzi 1](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-1.png)
        
![filtra indirizzi 2](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-2.png)

Nell'esempio seguente viene illustrato l'aspetto di uno scenario valido. 

- Se telnet **8.8.8.8 53** funziona senza problemi, è possibile vedere l'handshake TCP 3, quindi la sessione termina con l'handshake TCP 4.

    ![esempio di scenario valido 1](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-1.png)
     
    ![esempio di scenario valido 2](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-2.png)

- In base all'handshake TCP 3 precedente, è possibile visualizzare il flusso di lavoro seguente:

    ![Flusso di lavoro di handshake TCP 3](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-3-handshake-workflow.png)
 
- L'handshake TCP 4 per completare la sessione e il relativo flusso di lavoro verranno visualizzati come segue:

    ![Handshake TCP 4](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake.png)

    ![Flusso di lavoro TCP 4 handshake](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake-workflow.png) 


### <a name="receiving-email-to-update-the-network-configuration-to-allow-communication-with-new-ip-addresses"></a>Ricezione di un messaggio di posta elettronica per aggiornare la configurazione di rete per consentire la comunicazione con nuovi indirizzi IP

#### <a name="email-notification-from-microsoft"></a>Notifica tramite posta elettronica da Microsoft

È possibile ricevere una notifica di posta elettronica di seguito, che consiglia di aggiornare la configurazione di rete per consentire la comunicazione con i nuovi indirizzi IP per Azure Data Factory entro l'8 novembre 2020:

   ![Notifica tramite posta elettronica](media/self-hosted-integration-runtime-troubleshoot-guide/email-notification.png)

#### <a name="how-to-determine-if-you-are-impacted-by-this-notification"></a>Come determinare se si è interessati da questa notifica

Questa notifica influisca sugli scenari seguenti:
##### <a name="scenario-1-outbound-communication-from-self-hosted-integration-runtime-running-on-premises-behind-the-corporate-firewall"></a>Scenario 1: comunicazione in uscita da Integration Runtime self-hosted in esecuzione in locale dietro il firewall aziendale
Come determinare se si è interessati:
- L'utente non ha alcun effetto se si definiscono regole del firewall basate sui nomi FQDN usando l'approccio descritto in questo documento: [configurazione del firewall e configurazione dell'elenco Consenti per l'indirizzo IP](data-movement-security-considerations.md#firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway).
- Tuttavia, se si Abilita in modo esplicito l'elenco Consenti per gli IP in uscita nel firewall aziendale.

Azione da intraprendere in caso di conseguenze: inviare una notifica al team dell'infrastruttura di rete per aggiornare la configurazione di rete in modo da usare gli indirizzi IP Data Factory più recenti entro l'8 novembre 2020.  Per scaricare gli indirizzi IP più recenti, vedere il [collegamento di download dell'intervallo IP dei tag di servizio](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

##### <a name="scenario-2-outbound-communication-from-self-hosted-integration-runtime-running-on-an-azure-vm-inside-customer-managed-azure-virtual-network"></a>Scenario 2: comunicazione in uscita da Integration Runtime self-hosted in esecuzione in una macchina virtuale di Azure all'interno della rete virtuale di Azure gestita dal cliente
Come determinare se si è interessati:
- Verificare la presenza di regole NSG in uscita nella rete privata che contiene Integration Runtime indipendenti. Se non sono presenti restrizioni in uscita, non vi è alcun effetto.
- Se sono presenti restrizioni per le regole in uscita, controllare se si usa o meno il tag di servizio. Se si usa il tag di servizio, non è necessario modificare o aggiungere nulla perché i nuovi intervalli IP sono sotto il tag del servizio esistente. 
 ![Verifica destinazione](media/self-hosted-integration-runtime-troubleshoot-guide/destination-check.png)
- Tuttavia, se si Abilita in modo esplicito l'elenco Consenti per gli indirizzi IP in uscita nell'impostazione delle regole di NSG nella rete virtuale di Azure, si è interessati.

Azione da intraprendere in caso di conseguenze: inviare una notifica al team dell'infrastruttura di rete per aggiornare le regole di NSG nella configurazione della rete virtuale di Azure in modo da usare gli indirizzi IP Data Factory più recenti entro l'8 novembre 2020.  Per scaricare gli indirizzi IP più recenti, vedere il [collegamento di download dell'intervallo IP dei tag di servizio](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

##### <a name="scenario-3-outbound-communication-from-ssis-integration-runtime-in-customer-managed-azure-virtual-network"></a>Scenario 3: comunicazione in uscita da SSIS Integration Runtime nella rete virtuale di Azure gestita dal cliente
- Verificare la presenza di regole NSG in uscita nella rete privata che contiene Integration Runtime SSIS. Se non sono presenti restrizioni in uscita, non vi è alcun effetto.
- Se sono presenti restrizioni per le regole in uscita, controllare se si usa o meno il tag di servizio. Se si usa il tag di servizio, non è necessario modificare o aggiungere nulla perché i nuovi intervalli IP sono sotto il tag del servizio esistente.
- Tuttavia, se si Abilita in modo esplicito l'elenco Consenti per l'indirizzo IP in uscita nell'impostazione delle regole di NSG nella rete virtuale di Azure, si è interessati.

Azione da intraprendere in caso di conseguenze: inviare una notifica al team dell'infrastruttura di rete per aggiornare le regole di NSG nella configurazione della rete virtuale di Azure in modo da usare gli indirizzi IP Data Factory più recenti entro l'8 novembre 2020.  Per scaricare gli indirizzi IP più recenti, vedere il [collegamento di download dell'intervallo IP dei tag di servizio](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

### <a name="could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>Non è stato possibile stabilire una relazione di trust per il canale sicuro SSLTLS 

#### <a name="symptoms"></a>Sintomi

Il runtime di integrazione self-hosted non è riuscito a connettersi al servizio ADF.

Controllando il log eventi di o i log delle notifiche client nella tabella CustomLogEvent, viene rilevato il seguente messaggio di errore:

`The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.The remote certificate is invalid according to the validation procedure.`

Come verificare il certificato del server del servizio ADF:

Il modo più semplice consiste nell'aprire l'URL del servizio ADF nel browser, ad esempio, aprire nel https://eu.frontend.clouddatahub.net/ computer in cui è installato e quindi visualizzare le informazioni sul certificato del server:

  ![Verificare il certificato del server del servizio ADF](media/self-hosted-integration-runtime-troubleshoot-guide/server-certificate.png)

  ![Controlla percorso certificato server](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-path.png)

#### <a name="cause"></a>Causa

Due possibili motivi per questo problema:

- La CA radice del certificato del server del servizio ADF non è attendibile nel computer in cui è installato il server di Azure. 
- Si sta usando il proxy nel proprio ambiente e il certificato del server del servizio ADF viene sostituito dal proxy, mentre il certificato server sostituito non è considerato attendibile dal computer in cui è installato.

#### <a name="resolution"></a>Risoluzione

- Per il motivo 1, verificare che il certificato del server ADF e la relativa catena di certificati siano considerati attendibili dal computer in cui è installato.
- Per il motivo 2, considerare attendibile la CA radice sostituita nel computer di una macchina virtuale o configurare il proxy in modo che non sostituisca il certificato del server ADF.

Per informazioni dettagliate su come considerare attendibile un certificato in Windows, vedere [questo articolo](/skype-sdk/sdn/articles/installing-the-trusted-root-certificate) .

#### <a name="additional-info"></a>Informazioni aggiuntive
È in corso il rollup di un nuovo certificato SSL, firmato da DigiCert, verificare se DigiCert Global radice G2 si trova nella CA radice attendibile.

  ![DigiCert Global Root G2](media/self-hosted-integration-runtime-troubleshoot-guide/trusted-root-ca-check.png)

In caso contrario, scaricarlo da [qui](http://cacerts.digicert.com/DigiCertGlobalRootG2.crt ). 


## <a name="self-hosted-ir-sharing"></a>Condivisione del runtime di integrazione self-hosted

### <a name="share-self-hosted-ir-from-a-different-tenant-is-not-supported"></a>La condivisione del runtime di integrazione self-hosted da un tenant diverso non è supportata 

#### <a name="symptoms"></a>Sintomi

È possibile notare che altre Data Factory (in tenant diversi) tentano di condividere il runtime di integrazione self-hosted dall'interfaccia utente di Azure Data Factory, ma non possono condividere il runtime di integrazione self-hosted tra data factory che si trovano in tenant diversi.

#### <a name="cause"></a>Causa

Il runtime di integrazione self-hosted non può essere condiviso tra tenant.



## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla risoluzione dei problemi, provare a usare le risorse seguenti:

*  [Blog di Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Richieste di funzionalità di Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Video di Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Pagina delle domande di Domande e risposte Microsoft](/answers/topics/azure-data-factory.html)
*  [Forum di stack overflow per Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informazioni su Twitter su Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Guida alle prestazioni dei flussi di dati di mapping](concepts-data-flow-performance.md)