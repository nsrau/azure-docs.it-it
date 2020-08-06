---
title: Risolvere i problemi relativi al runtime di integrazione self-hosted in Azure Data Factory
description: Informazioni su come risolvere i problemi relativi al runtime di integrazione self-hosted in Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 08/05/2020
ms.author: abnarain
ms.openlocfilehash: 49d173e0d0f2b96c385b4325335483d25e9a7c2d
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/05/2020
ms.locfileid: "87800714"
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

    ![Invia log](media/self-hosted-integration-runtime-troubleshoot-guide/send-logs.png)

1. È possibile scegliere i log da inviare. Per il runtime di integrazione *self-hosted*, è possibile caricare i log relativi all'attività non riuscita o a tutti i log nel nodo IR self-hosted. Per il runtime di integrazione *condiviso*, è possibile caricare solo i log relativi alle attività non riuscite.

    ![Selezione dei log](media/self-hosted-integration-runtime-troubleshoot-guide/choose-logs.png)

1. Quando si caricano i log, tenere un record dell'ID del report, se è necessaria ulteriore assistenza per risolvere il problema.

    ![Carica log](media/self-hosted-integration-runtime-troubleshoot-guide/upload-logs.png)

> [!NOTE]
> La visualizzazione del log e il caricamento delle richieste verranno eseguiti in tutte le istanze del runtime di integrazione self-hosted online. Verificare che tutte le istanze del runtime di integrazione Self-Hosted siano online in caso di log mancanti. 


## <a name="self-hosted-ir-general-failure-or-error"></a>Errore generale del runtime di integrazione self-hosted o errore

### <a name="tlsssl-certificate-issue"></a>Problema relativo al certificato TLS/SSL

#### <a name="symptoms"></a>Sintomi

Quando si prova ad abilitare il certificato TLS/SSL (avanzato) dal runtime di integrazione **self-hosted Configuration Manager**  ->  **accesso remoto dalla rete Intranet**, dopo aver selezionato il certificato TLS/SSL, viene visualizzato l'errore seguente:

`Remote access settings are invalid. Identity check failed for outgoing message. The expected DNS identity of the remote endpoint was ‘abc.microsoft.com’ but the remote endpoint provided DNS claim ‘microsoft.com’. If this is a legitimate remote endpoint, you can fix the problem by explicitly specifying DNS identity ‘microsoft.com’ as the Identity property of EndpointAddress when creating channel proxy.`

Nel caso precedente, l'utente usa il certificato con "microsoft.com" come ultimo elemento.

#### <a name="cause"></a>Causa

Si tratta di un problema noto in WCF: la convalida TLS/SSL di WCF controlla solo gli ultimi DNSName nella SAN. 

#### <a name="resolution"></a>Risoluzione

Il certificato con caratteri jolly è supportato nel runtime di integrazione self-hosted Azure Data Factory V2. Questo problema si verifica in genere perché il certificato SSL non è corretto. L'ultimo DNSName nella SAN deve essere valido. Attenersi alla procedura seguente per verificarlo. 
1.  Aprire Management Console, controllare il nome del *soggetto* e il *nome alternativo del soggetto* dai dettagli del certificato. Nel caso precedente, ad esempio, l'ultimo elemento nel *nome alternativo del soggetto*, ovvero "DNS Name = Microsoft.com.com", non è legittimo.
2.  Per rimuovere il nome DNS errato, contattare la società di emissione del certificato.

### <a name="concurrent-jobs-limit-issue"></a>Numero massimo di processi simultanei

#### <a name="symptoms"></a>Sintomi

Quando si tenta di aumentare il limite dei processi simultanei dall'interfaccia utente di Azure Data Factory, si blocca come *aggiornamento* per sempre.
Il valore max dei processi simultanei è stato impostato su 24 e si desidera aumentare il numero in modo che i processi possano essere eseguiti più velocemente. Il valore minimo che è possibile immettere è 3 e il valore massimo che è possibile immettere è 32. È stato aumentato il valore da 24 a 32 e viene visualizzato il pulsante *Aggiorna* nell'interfaccia utente che si è bloccato durante l' *aggiornamento* , come si può vedere di seguito. Dopo l'aggiornamento, il cliente ha ancora visto il valore 24 e non è mai stato aggiornato a 32.

![Aggiornamento dello stato](media/self-hosted-integration-runtime-troubleshoot-guide/updating-status.png)

#### <a name="cause"></a>Causa

Esiste una limitazione per l'impostazione poiché il valore dipende dal computer logicCore e dalla memoria, è sufficiente modificarlo a un valore più piccolo, ad esempio 24, e visualizzare il risultato.

> [!TIP] 
> - Per informazioni dettagliate sul numero di core per la logica e su come trovare il numero di core per la logica del computer, vedere [questo articolo](https://www.top-password.com/blog/find-number-of-cores-in-your-cpu-on-windows-10/).
> - Per informazioni dettagliate su come calcolare Math. log, vedere [questo articolo](https://www.rapidtables.com/calc/math/Log_Calculator.html).


### <a name="self-hosted-ir-ha-ssl-certificate-issue"></a>Problema relativo al certificato SSL a disponibilità elevata per IR self-hosted

#### <a name="symptoms"></a>Sintomi

Il nodo di lavoro IR self-hosted ha segnalato l'errore seguente:

`Failed to pull shared states from primary node net.tcp://abc.cloud.corp.Microsoft.com:8060/ExternalService.svc/. Activity ID: XXXXX The X.509 certificate CN=abc.cloud.corp.Microsoft.com, OU=test, O=Microsoft chain building failed. The certificate that was used has a trust chain that cannot be verified. Replace the certificate or change the certificateValidationMode. The revocation function was unable to check revocation because the revocation server was offline.`

#### <a name="cause"></a>Causa

Quando si gestiscono i case correlati all'handshake SSL/TLS, potrebbero verificarsi alcuni problemi relativi alla verifica della catena di certificati. 

#### <a name="resolution"></a>Risoluzione

- Ecco un modo rapido e intuitivo per risolvere gli errori di compilazione della catena di certificati X. 509.
 
    1. Esportare il certificato, che deve essere verificato. Passare a Gestisci certificato computer e individuare il certificato che si desidera controllare e fare clic con il pulsante destro del mouse su **tutte le attività**  ->  **Esporta**.
    
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
    4. Controllare se è presente un errore nel file txt di output. È possibile trovare il riepilogo degli errori alla fine del file txt.

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
    1. Quindi verrà aperto lo **strumento di recupero URL** . È possibile verificare i certificati da AIA, CDP e OCSP facendo clic sul pulsante **Recupera** .

        ![Pulsante recupero](media/self-hosted-integration-runtime-troubleshoot-guide/retrieval-button.png)
 
        La catena di certificati può essere compilata correttamente se il certificato da AIA è "verificato" e il certificato da CDP o OCSP è "verificato".

        Se si verifica un errore durante il recupero di AIA, CDP, collaborare con il team di rete per preparare il computer client per la connessione all'URL di destinazione. Sarà sufficiente se è possibile verificare il percorso http o il percorso LDAP.

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
> Indica che la dll **System. ValueTuple** non si trova nella cartella correlata alla GAC oppure in *c:\Programmi\Microsoft Integration Runtime\4.0\Gateway*o nella cartella c:\Programmi\Microsoft *Integration Runtime\4.0\Shared*
> In sostanza, caricherà prima di tutto la dll dalla cartella *GAC* , quindi da *condivisa* e infine da cartella del *gateway* . Pertanto, è possibile inserire la dll in qualsiasi percorso che può essere utile.

![Configurare i filtri](media/self-hosted-integration-runtime-troubleshoot-guide/set-filters.png)

#### <a name="resolution"></a>Risoluzione

È possibile scoprire che la **System.ValueTuple.dll** si trova nella cartella *c:\Programmi\Microsoft Integration Runtime\4.0\Gateway\DataScan* Copiare il **System.ValueTuple.dll** nella cartella C:\Programmi\Microsoft *Integration Runtime\4.0\Gateway* per risolvere il problema.

È possibile usare lo stesso metodo per risolvere altri problemi di file o assembly mancanti.

#### <a name="more-information"></a>Altre informazioni

Il motivo per cui viene visualizzato il System.ValueTuple.dll in *%windir%\Microsoft.NET\assembly* e *%windir%\assembly* è che si tratta di un comportamento .NET. 

Dall'errore riportato di seguito, è possibile vedere chiaramente che l'assembly *System. ValueTuple* non è presente. Questo problema si verifica quando l'applicazione tenta di controllare l'assembly *System.ValueTuple.dll*.
 
`<LogProperties><ErrorInfo>[{"Code":0,"Message":"The type initializer for 'Npgsql.PoolManager' threw an exception.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.TypeInitializationException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[{"Code":0,"Message":"Could not load file or assembly 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.IO.FileNotFoundException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[]}]}]</ErrorInfo></LogProperties>`
 
Per ulteriori informazioni sulla GAC, vedere [questo articolo](https://docs.microsoft.com/dotnet/framework/app-domains/gac).


### <a name="how-to-audit-self-hosted-ir-key-missing"></a>Come controllare la chiave IR self-hosted mancante

#### <a name="symptoms"></a>Sintomi

Il runtime di integrazione self-hosted passa improvvisamente alla modalità offline senza chiave. di seguito viene visualizzato un messaggio di errore nel registro eventi:`Authentication Key is not assigned yet`

![Chiave di autenticazione mancante](media/self-hosted-integration-runtime-troubleshoot-guide/key-missing.png)

#### <a name="cause"></a>Causa

- Il nodo IR indipendente o il runtime di integrazione self-hosted logico nel portale viene eliminato.
- È stata eseguita una disinstallazione pulita.

#### <a name="resolution"></a>Risoluzione

Se nessuna delle cause precedenti si applica, è possibile passare alla cartella: *%ProgramData%\Microsoft\Data Transfer\DataManagementGateway*e verificare se il file denominato **configurazioni** è stato eliminato. Se viene eliminata, seguire le istruzioni riportate [qui](https://www.netwrix.com/how_to_detect_who_deleted_file.html) per controllare chi elimina il file.

![Verifica file di configurazione](media/self-hosted-integration-runtime-troubleshoot-guide/configurations-file.png)


### <a name="cannot-use-self-hosted-ir-to-bridge-two-on-premises-data-stores"></a>Non è possibile usare il runtime di integrazione self-hosted per collegare due archivi dati locali

#### <a name="symptoms"></a>Sintomi

Dopo aver creato l'IRs self-hosted per gli archivi dati di origine e di destinazione, è necessario connettere i due IRs insieme per completare una copia. Se gli archivi dati sono configurati in reti virtuali diversi o non riescono a comprendere il meccanismo del gateway, si verificano errori simili ai seguenti: *Impossibile trovare il driver dell'origine nel*runtime di integrazione di destinazione. *non è possibile accedere all'origine dal runtime di integrazione di destinazione*.
 
#### <a name="cause"></a>Causa

Il runtime di integrazione self-hosted è progettato come nodo centrale di un'attività di copia, non come agente client che deve essere installato per ogni archivio dati.
 
In precedenza, il servizio collegato per ogni archivio dati deve essere creato con lo stesso IR e il runtime di integrazione deve essere in grado di accedere a entrambi gli archivi dati attraverso la rete. Indipendentemente dal fatto che il runtime di integrazione sia installato con l'archivio dati di origine, con l'archivio dati di destinazione o in una terza macchina, se vengono creati due servizi collegati con un altro IRs, ma usati nella stessa attività di copia, verrà usato il runtime di integrazione di destinazione e i driver per entrambi gli archivi dati dovranno essere installati nel computer IR di destinazione.

#### <a name="resolution"></a>Risoluzione

Installare i driver sia per l'origine che per la destinazione nel runtime di integrazione di destinazione e assicurarsi che sia in grado di accedere all'archivio dati di origine.
 
Se il traffico non è in grado di passare attraverso la rete tra due archivi dati (ad esempio, sono configurati in due reti virtuali), non è possibile completare la copia in un'attività anche se è installato il runtime di integrazione. In tal caso, è possibile creare due attività di copia con due IRs, ognuna in uno sfiato: 1 IR da copiare dall'archivio dati 1 all'archivio BLOB di Azure, un'altra per la copia dall'archiviazione BLOB di Azure all'archivio dati 2. Questo potrebbe simulare la necessità di usare il runtime di integrazione per creare un Bridge che connette due archivi dati disconnessi.


### <a name="credential-sync-issue-causes-credential-lost-from-ha"></a>Il problema di sincronizzazione delle credenziali causa la perdita delle credenziali da disponibilità elevata

#### <a name="symptoms"></a>Sintomi

Le credenziali dell'origine dati "XXXXXXXXXX" sono state eliminate dal nodo Integration Runtime corrente con payload "quando si elimina il servizio di collegamento su portale di Azure o l'attività ha il payload errato, creare nuovamente un nuovo servizio di collegamento con le credenziali".

#### <a name="cause"></a>Causa

Il runtime di integrazione self-hosted è compilato in modalità a disponibilità elevata con due nodi, ma non si trovano nello stato di sincronizzazione delle credenziali, il che significa che le credenziali archiviate nel nodo dispatcher non vengono sincronizzate con altri nodi di lavoro. Se si verifica un failover dal nodo Dispatcher al nodo di lavoro ma le credenziali erano presenti solo nel nodo Dispatcher precedente, l'attività non riuscirà quando si tenta di accedere alle credenziali e si verificherà un errore.

#### <a name="resolution"></a>Risoluzione

L'unico modo per evitare questo problema consiste nel verificare che due nodi si trovino nello stato di sincronizzazione delle credenziali. In caso contrario, è necessario riimmettere le credenziali per il nuovo Dispatcher.


### <a name="cannot-choose-the-certificate-due-to-private-key-missing"></a>Non è possibile scegliere il certificato a causa di una chiave privata mancante

#### <a name="symptoms"></a>Sintomi

1.  Importare un file PFX nell'archivio certificati.
2.  Quando si seleziona il certificato tramite l'interfaccia utente di Configuration Manager IR, viene raggiunto l'errore seguente:

    ![Chiave privata mancante](media/self-hosted-integration-runtime-troubleshoot-guide/private-key-missing.png)

#### <a name="cause"></a>Causa

- L'account utente è con privilegi limitati e non può accedere alla chiave privata.
- Il certificato è stato generato come firma ma non come scambio di chiave.

#### <a name="resolution"></a>Risoluzione

1.  Usare un account con privilegi che può accedere alla chiave privata per il funzionamento dell'interfaccia utente.
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
            1. Passare alla cartella di installazione di selfhosted IR, passare alla cartella: *Microsoft Integration Runtime\4.0\Shared*.
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

Dal rilascio del *Integration Runtime 3,0*, il pulsante **registra** in un nodo Integration Runtime esistente è stato rimosso per consentire un ambiente più pulito e sicuro. Se un nodo è stato registrato in alcune Integration Runtime (in linea o meno), per registrarlo di nuovo in un altro Integration Runtime, è necessario disinstallare il nodo precedente, quindi installare e registrare il nodo.

#### <a name="resolution"></a>Risoluzione

1. Passare al pannello di controllo per disinstallare il Integration Runtime esistente.

    > [!IMPORTANT] 
    > Nel processo seguente selezionare Sì. Non conserva i dati durante il processo di disinstallazione.

    ![Eliminare i dati](media/self-hosted-integration-runtime-troubleshoot-guide/delete-data.png)

1. Se non si dispone dell'MSI del programma di installazione di Integration Runtime, passare all' [area download](https://www.microsoft.com/en-sg/download/details.aspx?id=39717) per scaricare la Integration runtime più recente.
1. Installare il file MSI e registrare il Integration Runtime.


### <a name="unable-to-register-the-self-hosted-ir-due-to-localhost"></a>Non è possibile registrare il runtime di integrazione self-hosted a causa di localhost    

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
> *    Verificare se il server proxy deve essere inserito nell'elenco Destinatari attendibili. In tal caso, verificare che [questi domini](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network) siano nell'elenco Destinatari attendibili.
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

Per risolvere il problema relativo alla connettività di rete, è necessario sapere come [raccogliere la traccia di rete](#how-to-collect-netmon-trace), comprendere come utilizzarla e [analizzare la traccia Netmon](#how-to-analyze-netmon-trace) prima di applicare gli strumenti NetMon in casi reali dal runtime di integrazione self-hosted.

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

### <a name="how-to-collect-netmon-trace"></a>Come raccogliere la traccia Netmon

1.  Scaricare gli strumenti NetMon da [questo sito Web](https://www.microsoft.com/en-sg/download/details.aspx?id=4865)e installarli nel computer server (indipendentemente dal server in cui si verifica il problema) e dal client, ad esempio il runtime di integrazione self-hosted.

2.  Creare una cartella, ad esempio, nel percorso seguente: *D:\Netmon*. Verificare che lo spazio disponibile sia sufficiente per salvare il log.

3.  Acquisire le informazioni su IP e porta. 
    1. Avviare un prompt dei comandi.
    2. Selezionare Esegui come amministratore ed eseguire il comando seguente:
       
        ```
        Ipconfig /all >D:\netmon\IP.txt
        netstat -abno > D:\netmon\ServerNetstat.txt
        ```

4.  Acquisire la traccia Netmon (pacchetto di rete).
    1. Avviare un prompt dei comandi.
    2. Selezionare Esegui come amministratore ed eseguire il comando seguente:
        
        ```
        cd C:\Program Files\Microsoft Network Monitor 3
        ```
    3. È possibile usare tre comandi diversi per acquisire la pagina di rete:
        - Opzione A: RoundRobin file Command (verrà acquisito un solo file e sovrascriverà i vecchi log).

            ```
            nmcap /network * /capture /file D:\netmon\ServerConnection.cap:200M
            ```         
        - Opzione B: comando file concatenato (verrà creato un nuovo file se viene raggiunto 200 MB).
        
            ```
            nmcap /network * /capture /file D:\netmon\ServerConnection.chn:200M
            ```          
        - Opzione C: comando file pianificato.

            ```
            nmcap /network * /capture /StartWhen /Time 10:30:00 AM 10/28/2011 /StopWhen /Time 11:30:00 AM 10/28/2011 /file D:\netmon\ServerConnection.chn:200M
            ```  

5.  Premere **CTRL + C** per arrestare l'acquisizione della traccia Netmon.
 
> [!NOTE]
> Se è possibile raccogliere solo la traccia NetMon nel computer client, ottenere l'indirizzo IP del server per facilitare l'analisi della traccia.

### <a name="how-to-analyze-netmon-trace"></a>Come analizzare la traccia Netmon

Quando si prova a usare telnet **8.8.8.8 888** con sopra la traccia Netmon raccolta, si dovrebbe vedere la traccia seguente:

![traccia Netmon 1](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-1.png)

![traccia Netmon 2](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-2.png)
 

Ciò significa che non è stato possibile eseguire la connessione TCP al lato server **8.8.8.8** in base alla porta **888**, quindi vengono visualizzati due pacchetti aggiuntivi **SynReTransmit** . Poiché l'origine **self-HOST2** non è in grado di eseguire la connessione a **8.8.8.8** al primo pacchetto, la connessione verrà mantenuta.

> [!TIP]
> - È possibile fare clic su **Carica filtro**  ->  **standard**  ->  **indirizzi**  ->  **IPv4 indirizzi**.
> - Input **IPv4. Address = = 8.8.8.8** come filtro, quindi fare clic su **applica**. Successivamente, si vedrà solo la comunicazione dal computer locale al **8.8.8.8**di destinazione.

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
*  [Pagina delle domande di Domande e risposte Microsoft](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Forum di stack overflow per Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informazioni su Twitter su Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Guida alle prestazioni dei flussi di dati di mapping](concepts-data-flow-performance.md)
