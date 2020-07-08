---
title: Risolvere i problemi relativi al runtime di integrazione self-hosted in Azure Data Factory
description: Informazioni su come risolvere i problemi relativi al runtime di integrazione self-hosted in Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 06/24/2020
ms.author: abnarain
ms.openlocfilehash: e77d621d5699c434e691de0a523e58e49166d8d6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85315056"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Risolvere i problemi relativi al runtime di integrazione self-hosted

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra i metodi più comuni per la risoluzione dei problemi relativi al runtime di integrazione self-hosted in Azure Data Factory.

## <a name="common-errors-and-resolutions"></a>Errori comuni e soluzioni

### <a name="error-message"></a>Messaggio di errore: 

`Self-hosted integration runtime can't connect to cloud service`

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

### <a name="error-message"></a>Messaggio di errore: 
`Self-hosted integration runtime node/ logical SHIR is in Inactive/ "Running (Limited)" state`

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


## <a name="troubleshoot-connectivity-issue"></a>Risolvere il problema di connettività

### <a name="troubleshoot-connectivity-issue-between-self-hosted-ir-and-data-factory-or-self-hosted-ir-and-data-sourcesink"></a>Risolvere i problemi di connettività tra il runtime di integrazione self-hosted e Data Factory o il runtime di integrazione self-hosted e l'origine dati

Per risolvere il problema relativo alla connettività di rete, è necessario sapere come [raccogliere la traccia di rete](#how-to-collect-netmon-trace), comprendere come utilizzarla e [analizzare la traccia Netmon](#how-to-analyze-netmon-trace) prima di applicare gli strumenti NetMon in casi reali dal runtime di integrazione self-hosted.

In alcuni casi, quando si risolvono i problemi di connettività, ad esempio al di sotto di un runtime di integrazione self-hosted e Data Factory: 

![Richiesta HTTP non riuscita](media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png)

O tra il runtime di integrazione self-hosted e l'origine dati/sink, si verificheranno gli errori seguenti:

**Messaggio di errore:**
`Copy failed with error:Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server: ‘IP address’`

**Messaggio di errore:**
`One or more errors occurred. An error occurred while sending the request. The underlying connection was closed: An unexpected error occurred on a receive. Unable to read data from the transport connection: An existing connection was forcibly closed by the remote host. An existing connection was forcibly closed by the remote host Activity ID.`

**Risoluzione:** Quando si verificano problemi sopra indicati, fare riferimento alle istruzioni seguenti per la risoluzione dei problemi:

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


## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla risoluzione dei problemi, provare a usare le risorse seguenti:

*  [Blog di Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Richieste di funzionalità di Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Video di Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Pagina delle domande di Domande e risposte Microsoft](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Forum di stack overflow per Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informazioni su Twitter su Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Guida alle prestazioni dei flussi di dati di mapping](concepts-data-flow-performance.md)
