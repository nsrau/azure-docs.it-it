---
title: Risoluzione dei problemi relativi al provisioning cloud di Azure AD ConnectAzure AD Connect cloud provisioning troubleshooting
description: In questo articolo viene descritto come risolvere i problemi che potrebbero verificarsi con l'agente di provisioning cloud.
author: billmath
ms.author: billmath
manager: daveba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: e41be4b76245f2567015eb0ede317830120ee61a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75549486"
---
# <a name="cloud-provisioning-troubleshooting"></a>Risoluzione dei problemi relativi al provisioning cloud

Il provisioning del cloud tocca molte cose diverse e ha molte dipendenze diverse. Questa ampia portata può dar luogo a vari problemi. Questo articolo consente di risolvere questi problemi. Introduce le aree tipiche su cui concentrarti, come raccogliere informazioni aggiuntive e le varie tecniche che puoi utilizzare per rintracciare i problemi.


## <a name="common-troubleshooting-areas"></a>Aree comuni per la risoluzione dei problemi

|Nome|Descrizione|
|-----|-----|
|[Problemi dell'agente](#agent-problems)|Verificare che l'agente sia stato installato correttamente e che comunichi con Azure Active Directory (Azure AD).|
|[Problemi di sincronizzazione degli oggetti](#object-synchronization-problems)|Utilizzare i registri di provisioning per risolvere i problemi di sincronizzazione degli oggetti.|
|[Provisioning dei problemi in quarantena](#provisioning-quarantined-problems)|Comprendere i problemi di quarantena del provisioning e come risolverli.|


## <a name="agent-problems"></a>Problemi dell'agente
Alcune delle prime cose che si desidera verificare con l'agente sono:

-  È installato?
-  L'agente è in esecuzione localmente?
-  L'agente è nel portale?
-  L'agente è contrassegnato come integro?

Questi elementi possono essere verificati nel portale di Azure e nel server locale che esegue l'agente.

### <a name="azure-portal-agent-verification"></a>Verifica dell'agente nel portale di Azure

Per verificare che l'agente sia visualizzato da Azure e sia integro, eseguire la procedura seguente.

1. Accedere al portale di Azure.
1. A sinistra selezionare **Azure Active Directory** > **Azure AD Connect**. Al centro, selezionare **Gestisci provisioning (anteprima)**.
1. Nella schermata **Provisioning (anteprima)** di Azure AD selezionare **Controlla tutti gli agenti.**

   ![Esaminare tutti gli agentiReview all agents](media/how-to-install/install7.png)</br>
 
1. Nella schermata Agenti di provisioning locali vengono **visualizzati** gli agenti installati. Verificare che l'agente in questione sia presente e sia contrassegnato come *Integro*.

   ![Schermata Degli agenti di provisioning locali](media/how-to-install/install8.png)</br>

### <a name="verify-the-port"></a>Verificare la porta

Per verificare che Azure sia in ascolto sulla porta 443 e che l'agente possa comunicare con esso, usare lo strumento seguente:To verify that Azure is listening on port 443 and that your agent can communicate with it, use the following tool:

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Questo test verifica che gli agenti possano comunicare con Azure tramite la porta 443.This test verifies that your agents can communicate with Azure over port 443. Aprire un browser e passare all'URL precedente dal server in cui è installato l'agente.

![Verifica della raggiungibilità del porto](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>Nel server locale

Per verificare che l'agente sia in esecuzione, attenersi alla seguente procedura.

1. Nel server in cui è installato l'agente aprire **Servizi** accedendovi o accedendo a **Avvia** > **eseguire** > **Services.msc**.
1. In **Servizi**verificare che **Microsoft Azure AD Connect Agent Updater** e **Microsoft Azure AD Connect Provisioning Agent** siano presenti e che il relativo stato sia *In esecuzione.*

   ![Schermata Servizi](media/how-to-troubleshoot/troubleshoot1.png)

### <a name="common-agent-installation-problems"></a>Problemi comuni di installazione dell'agente

Nelle sezioni seguenti vengono descritti alcuni problemi comuni di installazione degli agenti e le soluzioni tipiche.

#### <a name="agent-failed-to-start"></a>Avvio dell'agente non riuscito

È possibile che venga visualizzato un messaggio di errore che indica:

**Impossibile avviare il servizio 'Agente di provisioning di Microsoft Azure AD Connect'. Verificare di disporre di privilegi sufficienti per avviare i servizi di sistema.** 

Questo problema è in genere causato da un criterio di gruppo che ha impedito l'applicazione delle autorizzazioni all'account di accesso del servizio NT locale creato dal programma di installazione (NT SERVICE, AADConnectProvisioningAgent). Queste autorizzazioni sono necessarie per avviare il servizio.

Per risolvere questo problema, attenersi alla seguente procedura.

1. Accedere al server con un account amministratore.
1. Aprire **Servizi** accedendo ad esso o accedendo a **Avvia** > **eseguire** > **Services.msc**.
1. In **Servizi**fare doppio clic su **Agente di provisioning di Microsoft Azure AD Connect**.
1. Nella scheda **Connessione** modificare **Questo account** in un amministratore di dominio. Quindi riavviare il servizio. 

   ![Scheda Accesso](media/how-to-troubleshoot/troubleshoot3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>Timeout dell'agente o certificato non valido

Quando si tenta di registrare l'agente, è possibile che venga visualizzato il seguente messaggio di errore.

![Messaggio di errore di timeout](media/how-to-troubleshoot/troubleshoot4.png)

Questo problema è in genere causato dall'impossibilità dell'agente di connettersi al servizio di identità ibrido e richiede la configurazione di un proxy HTTP. Per risolvere questo problema, configurare un proxy in uscita. 

L'agente di provisioning supporta l'utilizzo di un proxy in uscita. È possibile configurarlo modificando il file di configurazione dell'agente *C:*. Aggiungere le seguenti righe, verso la fine del `</configuration>` file subito prima del tag di chiusura.
Sostituire le `[proxy-server]` `[proxy-port]` variabili e con il nome del server proxy e i valori della porta.

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

#### <a name="agent-registration-fails-with-security-error"></a>Registrazione dell'agente non riesce con errore di sicurezza

Quando si installa l'agente di provisioning cloud, è possibile che venga visualizzato un messaggio di errore.

Questo problema è in genere causato dall'agente non è in grado di eseguire gli script di registrazione di PowerShell a causa di criteri di esecuzione di PowerShell locali.

Per risolvere questo problema, modificare i criteri di esecuzione di PowerShell nel server. È necessario che i criteri Computer e Utente siano impostati come *Undefined* o *RemoteSigned*. Se sono impostati come *Unrestricted*, verrà visualizzato questo errore. Per ulteriori informazioni, vedere Criteri di esecuzione di [PowerShell](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6). 

### <a name="log-files"></a>File di log

Per impostazione predefinita, l'agente genera messaggi di errore minimi e informazioni sull'analisi dello stack. È possibile trovare questi registri di traccia nella cartella *C:*

Per raccogliere ulteriori dettagli per la risoluzione dei problemi relativi agli agenti, attenersi alla seguente procedura.

1. Arrestare il servizio **Microsoft Azure AD Connect Provisioning Agent.**
1. Creare una copia del file di configurazione originale: *C:*.
1. Sostituire la `<system.diagnostics>` sezione esistente con la sezione seguente e tutti i messaggi di traccia verranno inviati al file *ProvAgentTrace.log*.

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
1. Avviare il servizio **Microsoft Azure AD Connect Provisioning Agent**.
1. Utilizzare il comando seguente per inserire i problemi di file e debug. 
    ```
    Get-Content “C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log” -Wait
    ```
## <a name="object-synchronization-problems"></a>Problemi di sincronizzazione degli oggetti

La sezione seguente contiene informazioni sulla risoluzione dei problemi relativi alla sincronizzazione degli oggetti.

### <a name="provisioning-logs"></a>Log di provisioning

Nel portale di Azure è possibile usare i log di provisioning per individuare e risolvere i problemi di sincronizzazione degli oggetti. Per visualizzare i registri, selezionare **Registri**.

![Pulsante Log](media/how-to-troubleshoot/log1.png)

I log di provisioning forniscono numerose informazioni sullo stato degli oggetti sincronizzati tra l'ambiente Active Directory locale e Azure.

![Schermata Registri di provisioning](media/how-to-troubleshoot/log2.png)

È possibile utilizzare le caselle di riepilogo a discesa nella parte superiore della pagina per filtrare la visualizzazione a zero in su problemi specifici, ad esempio le date. Fare doppio clic su un singolo evento per visualizzare ulteriori informazioni.

![Informazioni sulla casella di riepilogo a discesa Registri di provisioning](media/how-to-troubleshoot/log3.png)

Queste informazioni forniscono i passaggi dettagliati e la posizione del problema di sincronizzazione. In questo modo, è possibile individuare il punto esatto del problema.


## <a name="provisioning-quarantined-problems"></a>Provisioning dei problemi in quarantena

Il provisioning cloud monitora l'integrità della configurazione e inserisce gli oggetti non integri in uno stato di quarantena. Se la maggior parte o tutte le chiamate effettuate sul sistema di destinazione hanno esito negativo in modo coerente a causa di un errore, ad esempio credenziali di amministratore non valide, il processo di provisioning viene contrassegnato come in quarantena.

![Stato di quarantena](media/how-to-troubleshoot/quarantine1.png)

Selezionando lo stato, è possibile visualizzare informazioni aggiuntive sulla quarantena. È inoltre possibile ottenere il codice di errore e il messaggio.

![Informazioni sullo stato della quarantena](media/how-to-troubleshoot/quarantine2.png)

### <a name="resolve-a-quarantine"></a>Risolvere una quarantena

- Usare il portale di Azure per riavviare il processo di provisioning. Nella pagina di configurazione dell'agente selezionare **Riavvia provisioning**.

  ![Riavviare il provisioning](media/how-to-troubleshoot/quarantine3.png)

- Utilizzare Microsoft Graph per [riavviare il processo](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http)di provisioning. Avrai il pieno controllo su ciò che riavvierai. È possibile scegliere di deselezionare:
  - Escrows, per riavviare il contatore di deposito a garanzia che si accumula verso lo stato di quarantena.
  - Quarantena, per rimuovere l'applicazione dalla quarantena.
  - Filigrane. 
  
  Usare la richiesta seguente:
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="next-steps"></a>Passaggi successivi 

- [Cos'è il provisioning?](what-is-provisioning.md)
- [Che cos'è il provisioning cloud di Azure AD Connect?](what-is-cloud-provisioning.md)



