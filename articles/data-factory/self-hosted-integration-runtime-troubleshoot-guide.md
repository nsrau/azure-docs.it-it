---
title: Risolvere i problemi relativi al runtime di integrazione self-hosted in Azure Data FactoryTroubleshoot self-hosted integration runtime in Azure Data Factory
description: Informazioni su come risolvere i problemi di runtime di integrazione self-hosted in Azure Data Factory.Learn how to troubleshoot self-hosted integration runtime issues in Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/07/2019
ms.author: abnarain
ms.openlocfilehash: b8492e8934c782451fb77d5a0ff56b96c34c9a00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75439882"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Risolvere i problemi relativi al runtime di integrazione self-hostedTroubleshoot self-hosted integration runtime

Questo articolo illustra i metodi comuni di risoluzione dei problemi per il runtime di integrazione self-hosted in Azure Data Factory.This article explores common troubleshooting methods for self-hosted integration runtime in Azure Data Factory.

## <a name="common-errors-and-resolutions"></a>Errori comuni e soluzioni

### <a name="error-message-self-hosted-integration-runtime-cant-connect-to-cloud-service"></a>Messaggio di errore: Il runtime di integrazione self-hosted non è in grado di connettersi al servizio cloud

![Problema di connessione IR self-hosted](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>Causa 

Il runtime di integrazione self-hosted non è in grado di connettersi al servizio Data Factory (backend). Questo problema è in genere causato dalle impostazioni di rete nel firewall.

#### <a name="resolution"></a>Risoluzione

1. Verificare se il servizio runtime di integrazione è in esecuzione.
    
   ![Stato di esecuzione del servizio IR self-hosted](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. Se il servizio è in esecuzione, andare al passaggio 3.If the service is running, go go to step 3.

1. Se non è presente alcun proxy configurato nel runtime di integrazione self-hosted (impostazione predefinita), eseguire il comando di PowerShell seguente nel computer in cui è installato il runtime di integrazione self-hosted:

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > L'URL del servizio può variare a seconda del percorso di Data Factory.The service URL may vary, depending on your Data Factory location. È possibile trovare l'URL del servizio in**Connections** > **Ibitavi** > di integrazione delle connessioni **ADF** > Modifica**URL del servizio**di visualizzazione dei**nodi** > IR > **self-hosted**.
            
    Di seguito è riportata la risposta prevista:
            
    ![Risposta del comando PowerShellPowerShell command response](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. Se non si riceve la risposta prevista, utilizzare uno dei seguenti metodi a seconda della situazione:
            
    * Se viene visualizzato il messaggio "Impossibile risolvere il nome remoto", si verifica un problema DNS (Domain Name System). Contattare il team di rete per risolvere il problema.
    * Se viene visualizzato un messaggio "certificato ssl/tls non attendibile", verificare se il certificato per https://wu2.frontend.clouddatahub.net/ è attendibile nel computer e quindi installare il certificato pubblico utilizzando Gestione certificati. Questa azione dovrebbe attenuare il problema.
    * Passare a**Visualizzatore eventi (registri)** > **applicazioni di** >  **Windows** > e registri di servizi In fase di**integrazione** e verificare la presenza di eventuali errori causati da DNS, una regola del firewall o le impostazioni di rete aziendale. (Se si rileva un errore di questo tipo, chiudere con la barra di connessione.) Poiché ogni azienda dispone di impostazioni di rete personalizzate, contattare il team di rete per risolvere questi problemi.

1. Se "proxy" è stato configurato nel runtime di integrazione self-hosted, verificare che il server proxy possa accedere all'endpoint del servizio. Per un comando di esempio, vedere [PowerShell, richieste Web e proxy](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies).    
                
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

Di seguito è riportata la risposta prevista:
            
![Risposta del comando PowerShell 2Powershell command response 2](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> Considerazioni sul proxy:
> * Verificare se il server proxy deve essere inserito nell'elenco Destinatari attendibili. In tal caso, assicurarsi che [questi domini](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network) siano inclusi nell'elenco Destinatari attendibili.
> * Verificare se il certificato TLS/SSL "wu2.frontend.clouddatahub.net/" è considerato attendibile sul server proxy.
> * Se si utilizza l'autenticazione di Active Directory nel proxy, modificare l'account del servizio con l'account utente che può accedere al proxy come "Servizio runtime di integrazione".

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>Messaggio di errore: Nodo di runtime di integrazione self-hosted/SHIR logico è in stato inattivo/ "In esecuzione (limitato)"

#### <a name="cause"></a>Causa 

Il nodo di runtime integrato self-hosted potrebbe avere uno stato **Inattivo,** come illustrato nella schermata seguente:The self-hosted integrated runtime node might have an Inactive status, as shown in the following screenshot:

![Nodo AIR self-hosted inattivo](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

Questo comportamento si verifica quando i nodi non possono comunicare tra loro.

#### <a name="resolution"></a>Risoluzione

1. Accedere alla macchina virtuale ospitata dal nodo. In Runtime**di integrazione** **registri** > applicazioni e servizi aprire Visualizzatore eventi e filtrare tutti i log degli errori.

1. Verificare se un log degli errori contiene il seguente errore: 
    
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
        
1.  Check whether the error log contains the following:

    ```Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)```
    
1. To resolve the issue, try one or both of the following methods:
    - Put all the nodes in the same domain.
    - Add the IP to host mapping in all the hosted VM's host files.


## Next steps

For more help with troubleshooting, try the following resources:

*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory feature requests](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN forum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack overflow forum for Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter information about Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Mapping data flows performance guide](concepts-data-flow-performance.md)
