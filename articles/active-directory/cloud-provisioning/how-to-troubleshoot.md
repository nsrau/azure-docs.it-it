---
title: Risoluzione dei problemi di provisioning nel Cloud Azure AD Connect
description: Questo documento descrive come risolvere i problemi che possono verificarsi con l'agente di provisioning cloud.
author: billmath
ms.author: billmath
manager: daveba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 4100886e0a24fa961b9085bd507ae3f4ebfdd6eb
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794237"
---
# <a name="cloud-provisioning-troubleshooting"></a>Risoluzione dei problemi di provisioning nel cloud

Il provisioning nel cloud tocca molti elementi diversi e presenta diverse dipendenze.  Naturalmente, ciò può dare luogo a vari problemi.  Questo documento è stato progettato per iniziare a risolvere questi problemi.  In questo documento vengono presentate le aree tipiche in cui è necessario concentrarsi, come raccogliere informazioni aggiuntive e le varie tecniche che è possibile utilizzare per tenere traccia dei problemi.  


## <a name="common-troubleshooting-areas"></a>Aree comuni per la risoluzione dei problemi

|name|Description|
|-----|-----|
|[Problemi relativi all'agente](#agent-issues)|Verificare che l'agente sia stato installato correttamente e che comunichi con Azure AD.|
|[Problemi di sincronizzazione degli oggetti](#object-synchronization-issues)|Usare i log di provisioning per risolvere i problemi di sincronizzazione degli oggetti.|
|[Problemi di quarantena del provisioning](#provisioning-quarantined-issues)|Informazioni sul problema di provisioning della quarantena e su come risolverli.|


## <a name="agent-issues"></a>Problemi relativi all'agente
Di seguito sono riportate alcune delle prime operazioni da verificare con l'agente:


-  è installato?
-  l'agente è in esecuzione in locale?
-  l'agente è presente nel portale?
-  l'agente è contrassegnato come integro?  

Questi elementi possono essere verificati nella portale di Azure e nel server locale in cui è in esecuzione l'agente.

### <a name="azure-portal-agent-verification"></a>Verifica dell'agente portale di Azure

Per verificare che l'agente sia visualizzato da Azure ed è integro, attenersi alla procedura seguente:

1. Accedere al portale di Azure.
2. A sinistra selezionare **Azure Active Directory**, fare clic su **Azure ad Connect** e al centro selezionare **Gestisci provisioning (anteprima)** .
3.  Nella schermata **Azure ad provisioning (anteprima)** fare clic su **Controlla tutti gli agenti**.
 provisioning di ![Azure AD](media/how-to-install/install7.png)</br>
 
4. Nella **schermata agenti di provisioning locali** vengono visualizzati gli agenti installati.  Verificare che l'agente in questione sia presente ed è contrassegnato come **integro**.
 ![agenti di provisioning](media/how-to-install/install8.png)</br>

### <a name="verify-the-port"></a>Verificare la porta

Per verificare che Azure sia in ascolto sulla porta 443 e che l'agente sia in grado di comunicare con esso, è possibile usare lo strumento seguente:

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Questo test verificherà che gli agenti siano in grado di comunicare con Azure tramite la porta 443.  Aprire un browser e passare all'URL precedente dal server in cui è installato l'agente.
 ![Servizi](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>Sul server locale

Per verificare che l'agente sia in esecuzione, attenersi alla seguente procedura:

1.  Sul server in cui è installato l'agente, aprire **Servizi** passando a tale server oppure avviando Start/Run/Services. msc.
2.  In **Servizi**verificare che **Microsoft Azure ad Connect agent Updater** e **Microsoft Azure ad Connect provisioning Agent** siano presenti e che lo stato sia **in esecuzione**.
 ![Servizi](media/how-to-troubleshoot/troubleshoot1.png)

### <a name="common-agent-installation-issues"></a>Problemi comuni di installazione dell'agente

Di seguito sono riportati alcuni problemi comuni di installazione degli agenti e la risoluzione tipica.

#### <a name="agent-failed-to-start"></a>Impossibile avviare l'agente

Se viene visualizzato un messaggio di errore che indica:

**Impossibile avviare il servizio ' Microsfoft Azure AD Connect provisioning Agent '.  Verificare di disporre di privilegi sufficienti per avviare i servizi di sistema.** 

Questo è in genere causato da criteri di gruppo che impediscono l'applicazione delle autorizzazioni al servizio NT locale "account di accesso" creato dal programma di installazione (NT SERVICE\AADConnectProvisioningAgent) queste autorizzazioni sono necessarie per avviare il servizio.

Per risolvere il problema, attenersi alla procedura seguente:

1.  Accedere al server con un account amministratore
2.  Aprire i **Servizi** passando a Start/Run/Services. msc.
3.  In **Servizi** fare doppio clic su **Microsoft Azure ad Connetti agente di provisioning**
4. Nella scheda impostare "account di accesso" su un amministratore di dominio e riavviare il servizio. 

 ![Services](media/how-to-troubleshoot/troubleshoot3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>Timeout dell'agente o certificato non valido

Se si sta tentando di registrare l'agente, è possibile che vengano visualizzati gli errori seguenti.

 ![Services](media/how-to-troubleshoot/troubleshoot4.png)

Questo problema in genere è dovuto al fatto che l'agente non è in grado di connettersi al servizio di identità ibrido e richiede la configurazione del proxy HTTP.  Per risolvere questo problema, configurare un proxy in uscita. 

L'agente di provisioning supporta l'utilizzo di proxy in uscita. È possibile configurarlo modificando il file di configurazione dell'agente **c:\programmi\microsoft Azure ad Connect provisioning Agent\AADConnectProvisioningAgent.exe.config**. Aggiungere le righe seguenti, verso la fine del file immediatamente prima del tag di chiusura `</configuration>`.
Sostituire le variabili [server proxy] e [proxy-port] con il nome del server proxy e i valori della porta.

```xml
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

#### <a name="agent-registration-fails-with-security-error"></a>La registrazione dell'agente non riesce con errore di sicurezza

Quando si installa l'agente di provisioning cloud, potrebbe essere visualizzato l'errore seguente.

Questo problema si verifica in genere quando l'agente non è in grado di eseguire gli script di registrazione di PowerShell a causa dei criteri di esecuzione di PowerShell locali.

Per risolvere questo problema, modificare i criteri di esecuzione di PowerShell nel server. È necessario disporre di criteri per computer e utenti come "non definiti" o "RemoteSigned". Se è "senza restrizioni", verrà visualizzato questo errore.  Per altre informazioni, vedere [criteri di esecuzione di PowerShell](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6). 

### <a name="log-files"></a>File di log

Per impostazione predefinita, l'agente genera messaggi di errore e informazioni di analisi dello stack minime. È possibile trovare i log di traccia nella cartella: **C:\PROGRAMDATA\MICROSOFT\AZURE ad Connect provisioning Agent\Trace**

Utilizzare la procedura seguente per raccogliere ulteriori dettagli per la risoluzione dei problemi relativi agli agenti.

1. Arrestare il servizio **Microsoft Azure ad connettere l'agente di provisioning**
2. Creare una copia del file di configurazione originale: **c:\programmi\microsoft Azure ad Connect provisioning Agent\AADConnectProvisioningAgent.exe.config**
3. Sostituire la sezione esistente < System. Diagnostics > con il codice seguente e tutti i messaggi di traccia andranno al file **ProvAgentTrace. log**

      ```xml
        <system.diagnostics>
            <sources>
            <source name="AAD Connect Provisioning Agent">
                <listeners>
                <add name="console"/>
                <add name="etw"/>
                <add name="textWriterListener"/>
                </listeners>
            </source>
            </sources>
            <sharedListeners>
            <add name="console" type="System.Diagnostics.ConsoleTraceListener" initializeData="false"/>
            <add name="etw" type="System.Diagnostics.EventLogTraceListener" initializeData="Azure AD Connect Provisioning Agent">
                <filter type="System.Diagnostics.EventTypeFilter" initializeData="All"/>
            </add>
            <add name="textWriterListener" type="System.Diagnostics.TextWriterTraceListener" initializeData="C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log"/>
            </sharedListeners>
        </system.diagnostics>

      ```
4. Avviare il servizio **Microsoft Azure ad connettere l'agente di provisioning**
5. È possibile usare il comando seguente per eseguire il backup del file e i problemi di debug: 
    ```
    Get-Content “C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log” -Wait
    ```
## <a name="object-synchronization-issues"></a>Problemi di sincronizzazione degli oggetti

La sezione seguente contiene informazioni sulla risoluzione dei problemi di sincronizzazione degli oggetti.

### <a name="provisioning-logs"></a>Log di provisioning

Nella portale di Azure è possibile usare i log di provisioning per individuare e risolvere i problemi di sincronizzazione degli oggetti.  Per visualizzare i log, selezionare **log**.
 ![i log di provisioning](media/how-to-troubleshoot/log1.png)

I log di provisioning forniscono un'ampia gamma di informazioni sullo stato degli oggetti sincronizzati tra l'ambiente AD locale e Azure.

 ![Log di provisioning](media/how-to-troubleshoot/log2.png)

È possibile usare gli elenchi a discesa nella parte superiore della pagina per filtrare la visualizzazione in base a problemi specifici, date e così via.  Se si fa doppio clic su un singolo evento, vengono fornite informazioni dettagliate aggiuntive.

 ![Log di provisioning](media/how-to-troubleshoot/log3.png)

Queste informazioni forniranno i passaggi dettagliati e il punto in cui si verifica il problema di sincronizzazione.  In questo modo, è possibile azzerare e individuare il punto esatto del problema.


## <a name="provisioning-quarantined-issues"></a>Provisioning di problemi in quarantena

Il provisioning nel Cloud monitora l'integrità della configurazione e inserisce oggetti non integri in uno stato di "quarantena". Se la maggior parte o tutte le chiamate effettuate sul sistema di destinazione hanno esito negativo a causa di un errore, ad esempio credenziali di amministratore non valide, il processo di provisioning è contrassegnato come in quarantena.

 ![Quarantena](media/how-to-troubleshoot/quarantine1.png)

Facendo clic sullo stato, è possibile visualizzare informazioni aggiuntive sulla quarantena.  È anche possibile ottenere il codice di errore e il messaggio.

 ![Quarantena](media/how-to-troubleshoot/quarantine2.png)

### <a name="resolving-a-quarantine"></a>Risoluzione di una quarantena

- Usare il portale di Azure per riavviare il processo di provisioning. Nella pagina Configurazione agente selezionare **Riavvia provisioning**.

  ![Quarantena](media/how-to-troubleshoot/quarantine3.png)

- Usare Microsoft Graph per [riavviare il processo di provisioning](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http). Si avrà il controllo completo su ciò che viene riavviato. È possibile scegliere di cancellare i riconoscimenti (per riavviare il contatore del deposito per lo stato di quarantena), cancellare la quarantena (per rimuovere l'applicazione dalla quarantena) o cancellare le filigrane. Usare la richiesta seguente:
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="next-steps"></a>Passaggi successivi 

- [Che cos'è il provisioning?](what-is-provisioning.md)
- [Che cos'è Azure AD Connect provisioning cloud?](what-is-cloud-provisioning.md)



