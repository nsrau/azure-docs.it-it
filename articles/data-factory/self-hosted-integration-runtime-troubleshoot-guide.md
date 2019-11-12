---
title: Risolvere i problemi del runtime di integrazione self-hosted in Azure Data Factory
description: Informazioni su come risolvere i problemi del runtime di integrazione self-hosted in Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/07/2019
ms.author: abnarain
ms.openlocfilehash: 9adbc3d7d30aeb8c7cb2b89c326ac2b39a2e8d2b
ms.sourcegitcommit: 6dec090a6820fb68ac7648cf5fa4a70f45f87e1a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2019
ms.locfileid: "73907282"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Risolvere i problemi del runtime di integrazione self-hosted

Questo articolo illustra i metodi comuni per la risoluzione dei problemi per il runtime di integrazione self-hosted in Azure Data Factory.

## <a name="common-errors-and-resolutions"></a>Errori e soluzioni comuni

### <a name="error-message-self-hosted-integration-runtime-is-unable-to-connect-to-cloud-service"></a>Messaggio di errore: il runtime di integrazione self-hosted non riesce a connettersi al servizio cloud.

- **Sintomo**: 

    ![Problema di connessione IR self-hosted](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

- **Motivo**: il runtime di integrazione self-hosted non è in grado di connettersi al servizio Data Factory (back-end). La maggior parte delle volte non è causata a causa di impostazioni di rete nel firewall.

- **Risoluzione**: 

    1. Verificare che il servizio Windows "Integration Runtime servizio" sia in esecuzione.
    
        ![Stato di esecuzione del servizio IR self-hosted](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
    2. Se il servizio Windows come illustrato in [1] è in esecuzione, seguire le istruzioni seguenti in base alle esigenze:

        1. Se "proxy" non è configurato nel runtime di integrazione self-hosted (le impostazioni predefinite non sono configurazioni proxy), eseguire il comando di PowerShell seguente nel computer in cui è installato il runtime di integrazione self-hosted: 
            
            ```powershell
            (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
            ```
            > [!NOTE] 
            > L'URL del servizio può variare in base alla posizione del data factory. È possibile trovare l'URL del servizio in ADF UI-> Connections-> runtime di integrazione-> modificare i nodi IR-> indipendenti > visualizzare gli URL del servizio.
            
            Di seguito è riportata la risposta prevista:
            
            ![Risposta comando di PowerShell](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
            Se la risposta è diversa, seguire le istruzioni seguenti in base alle esigenze:
            
            * Se si verifica l'errore "Impossibile risolvere il nome remoto", si verifica un problema con DNS. Per risolvere il problema di risoluzione DNS, contatta il team di rete. 
            * Se viene ricevuto un errore "certificato SSL/TLS non attendibile", verificare che il certificato per "https://wu2.frontend.clouddatahub.net/" sia attendibile nel computer, installare il certificato pubblico usando Cert Manager, che dovrebbe attenuare questo problema.
            * Controllare il Visualizzatore eventi di Windows > (registri) > registri applicazioni e servizi: > Integration Runtime per qualsiasi errore, principalmente causato da DNS, da una regola del firewall e dalle impostazioni di rete della società (chiusura forzata della connessione). Per questo problema, coinvolgere il team di rete per ulteriori risoluzione dei problemi, perché ogni azienda ha impostazioni di rete personalizzate.

        2. Se il "proxy" è stato configurato nel runtime di integrazione self-hosted, verificare se il server proxy è in grado di accedere all'endpoint di servizio. Per un comando di esempio, fare riferimento a [questa](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies).    
                
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
            
            ![Risposta comando PowerShell 2](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

            > [!NOTE] 
            > Considerazioni sul proxy:
            > * Controllare se il server proxy richiede l'elenco elementi consentiti. In tal caso, includere [questi domini](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network) nell'elenco elementi consentiti.
            > * Controllare che il certificato TLS/SSL per "wu2.frontend.clouddatahub.net/" sia attendibile nel server proxy.
            > * Se si utilizza l'autenticazione di Active Directory nel proxy, modificare l'account del servizio per l'account utente che può accedere al proxy come "servizio Integration Runtime".

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>Messaggio di errore: il nodo del runtime di integrazione self-hosted o il sistema di gestione delle attività logiche è nello stato inattivo/"in esecuzione (limitato)"

- **Motivo**: è possibile che venga visualizzato un nodo IR self-hosted in stato inattivo, come illustrato nella schermata seguente:

    ![Nodo IR self-hosted non attivo](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

    Questa situazione si verifica quando i nodi non sono in grado di comunicare tra loro. 

- **Risoluzione**: 

    Accedere alla macchina virtuale ospitata nel nodo e aprire visualizzazione eventi, sotto il Integration Runtime registri applicazioni e servizi->, filtrare tutti i log degli errori. 

     1. Se il log degli errori contiene: 
    
        **Log degli errori**: System. ServiceModel. EndpointNotFoundException: Impossibile connettersi a NET. TCP://xxxxxxx.bwld.com: 8060/ExternalService. svc/WorkerManager. Il tentativo di connessione è stato eseguito per un intervallo di tempo pari a 00:00:00.9940994. Codice di errore TCP 10061: non è stato possibile connettersi perché il computer di destinazione ha rifiutato attivamente 10.2.4.10:8060.  ---> System .NET. Sockets. SocketException: non è stato possibile connettersi perché il computer di destinazione ha rifiutato attivamente 10.2.4.10:8060
    
           in System .NET. Sockets. Socket. DoConnect (EndPoint endPointSnapshot, SocketAddress socketAddress)
           
           in System .NET. Sockets. Socket. Connect (EndPoint remoteEP)
           
           in System. ServiceModel. Channels. SocketConnectionInitiator. Connect (Uri URI, timeout TimeSpan)
    
        **Soluzione:** avviare la riga di comando: telnet 10.2.4.10 8060
        
        Se viene riportato un errore, contattare il responsabile IT per ottenere assistenza nella risoluzione di questo problema. Dopo aver eseguito il telnet, contattare il supporto tecnico Microsoft se si verificano ancora problemi per lo stato del nodo IR.
        
        ![Errore della riga di comando](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
     2. Se il log degli errori contiene:
     
        **Log degli errori:** Impossibile connettersi al gestore del ruolo di lavoro: NET. TCP://xxxxxx: 8060/ExternalService. svc/non esistono voci DNS per l'host azranlcir01r1. Questo host non è noto come dettaglio eccezione: System. ServiceModel. EndpointNotFoundException: non esistono voci DNS per l'host xxxxx. ---> System .NET. Sockets. SocketException: tale host non è noto in System .NET. DNS. funzione getaddrinfo (nome stringa) in System .NET. DNS. InternalGetHostByName (stringa hostName, Boolean includeIPv6) in System .NET. DNS. GetHostEntry (String hostNameOrAddress ) in System. ServiceModel. Channels. DnsCache. Resolve (Uri URI)---fine della traccia dello stack dell'eccezione interna---traccia dello stack del server: in System. ServiceModel. Channels. DnsCache. Resolve (Uri URI) 
    
        **Soluzione:** Una delle due azioni seguenti può contribuire a risolvere il problema:
         1. Inserire tutti i nodi nello stesso dominio.
         2. Aggiungere IP al mapping dell'host in tutti i file host della macchina virtuale ospitata.


## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla risoluzione dei problemi, provare a usare le risorse seguenti:

*  [Blog di Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Richieste di funzionalità Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Video su Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Forum Stack Overflow per Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informazioni su Twitter sui Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Guida alle prestazioni dei flussi di dati di mapping di ADF](concepts-data-flow-performance.md)
