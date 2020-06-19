---
title: Risolvere i problemi relativi al runtime di integrazione self-hosted in Azure Data Factory
description: Informazioni su come risolvere i problemi relativi al runtime di integrazione self-hosted in Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/07/2019
ms.author: abnarain
ms.openlocfilehash: 94e214c55a0109beb85cd08ce87303e5bd0f8016
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835428"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Risolvere i problemi relativi al runtime di integrazione self-hosted

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra i metodi più comuni per la risoluzione dei problemi relativi al runtime di integrazione self-hosted in Azure Data Factory.

## <a name="common-errors-and-resolutions"></a>Errori comuni e soluzioni

### <a name="error-message-self-hosted-integration-runtime-cant-connect-to-cloud-service"></a>Messaggio di errore: Il runtime di integrazione self-hosted non può connettersi al servizio cloud

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
    
    ```System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
        
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
               
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
               
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
       
1. If you see this error, run the following on a command line: 

   **telnet 10.2.4.10 8060**.
1. If you receive the following error, contact your IT department for help with fixing this issue. After you can successfully telnet, contact Microsoft Support if you still have issues with the integrative runtime node status.
        
   ![Command-line error](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1.    Check whether the error log contains the following:

    ```Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)```
    
1. To resolve the issue, try one or both of the following methods:
    - Put all the nodes in the same domain.
    - Add the IP to host mapping in all the hosted VM's host files.


## Next steps

For more help with troubleshooting, try the following resources:

*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory feature requests](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A question page](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Stack overflow forum for Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter information about Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Mapping data flows performance guide](concepts-data-flow-performance.md)
