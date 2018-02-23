---
title: Risolvere i problemi di sincronizzazione della password con il servizio di sincronizzazione Azure AD Connect | Microsoft Docs
description: Questo articolo contiene informazioni sulla risoluzione dei problemi di sincronizzazione delle password.
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 0bf07e80e575309fe7fa44661776c23da5db6dce
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2018
---
# <a name="troubleshoot-password-synchronization-with-azure-ad-connect-sync"></a>Risolvere i problemi di sincronizzazione della password con il servizio di sincronizzazione Azure AD Connect
In questo argomento viene descritta la procedura per risolvere i problemi di sincronizzazione della password. Se le password non vengono sincronizzate come previsto, il problema può riguardare un subset di utenti o tutti gli utenti.

Per la distribuzione di Azure Active Directory (Azure AD) Connect versione 1.1.614.0 o successiva, usare l'attività specifica nella procedura guidata per la risoluzione dei problemi di sincronizzazione delle password:

* Se si verifica un problema per cui nessuna password viene sincronizzata, vedere la sezione [Nessuna password viene sincronizzata: risolvere i problemi usando l'attività di risoluzione dei problemi](#no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task).

* Se si verifica un problema relativo a singoli oggetti, vedere la sezione [Un oggetto non sincronizza le password: risolvere i problemi usando l'attività di risoluzione dei problemi](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task).

Per la distribuzione con la versione 1.1.524.0 o successiva, è disponibile un cmdlet di diagnostica che è possibile usare per risolvere i problemi di sincronizzazione delle password:

* Se si verifica un problema per cui nessuna password viene sincronizzata, vedere la sezione [Nessuna password viene sincronizzata: risolvere i problemi tramite il cmdlet di diagnostica](#no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet).

* Se si verifica un problema relativo a singoli oggetti, vedere la sezione [Un oggetto non sincronizza le password: risolvere i problemi tramite il cmdlet di diagnostica](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet).

Per le versioni precedenti della distribuzione di Azure AD Connect:

* Se si verifica un problema per cui nessuna password viene sincronizzata, vedere la sezione [Nessuna password viene sincronizzata: passaggi per la risoluzione manuale dei problemi](#no-passwords-are-synchronized-manual-troubleshooting-steps).

* Se si verifica un problema relativo a singoli oggetti, vedere la sezione [Un oggetto non sincronizza le password: passaggi per la risoluzione manuale dei problemi](#one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps).



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task"></a>Nessuna password viene sincronizzata: risolvere i problemi usando l'attività di risoluzione dei problemi
Per stabilire il motivo per cui nessuna password viene sincronizzata, è possibile usare l'attività di risoluzione dei problemi.

> [!NOTE]
> L'attività di risoluzione dei problemi è disponibile solo per Azure AD Connect versione 1.1.614.0 o successiva.

### <a name="run-the-troubleshooting-task"></a>Eseguire l'attività di risoluzione dei problemi
Per risolvere i problemi per cui nessuna password viene sincronizzata:

1. Aprire una nuova sessione di Windows PowerShell nel server di Azure AD Connect con l'opzione **Esegui come amministratore**.

2. Eseguire `Set-ExecutionPolicy RemoteSigned` o `Set-ExecutionPolicy Unrestricted`.

3. Avviare la procedura guidata di Azure AD Connect.

4. Passare alla pagina **Attività aggiuntive**, selezionare **Risoluzione dei problemi** e fare clic su **Avanti**.

5. Nella pagina Risoluzione dei problemi fare clic su **Avvia** per avviare il menu per la risoluzione dei problemi in PowerShell.

6. Dal menu principale scegliere **Risolvere i problemi di sincronizzazione delle password**.

7. Dal sottomenu scegliere **Password Synchronization does not work at all** (La sincronizzazione delle password non funziona).

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Informazioni sui risultati dell'attività di risoluzione dei problemi
L'attività di risoluzione dei problemi effettua i controlli seguenti:

* Verifica che la funzionalità di sincronizzazione delle password sia abilitata per il tenant di Azure AD.

* Verifica che il server di Azure AD Connect non sia in modalità di gestione temporanea.

* Per ogni istanza locale esistente di Active Directory Connector, corrispondente a una foresta di Active Directory esistente:

   * Verifica che la funzionalità di sincronizzazione delle password sia abilitata.
   
   * Cerca gli eventi heartbeat di sincronizzazione delle password nei log eventi delle applicazioni di Windows.

   * Per ogni dominio di Active Directory nell'istanza locale di Active Directory Connector:

      * Verifica che il dominio sia raggiungibile dal server di Azure AD Connect.

      * Verifica che gli account di Active Directory Domain Services usati dall'istanza locale di Active Directory Connector abbiano il nome utente e la password corretti e le autorizzazioni necessarie per la sincronizzazione delle password.

Il diagramma seguente illustra i risultati del cmdlet per una topologia di Active Directory locale a dominio singolo:

![Output di diagnostica per la sincronizzazione delle password](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/phsglobalgeneral.png)

La parte restante di questa sezione descrive i risultati specifici restituiti dall'attività e i problemi corrispondenti.

#### <a name="password-synchronization-feature-isnt-enabled"></a>La funzionalità di sincronizzazione delle password non è abilitata
Se la sincronizzazione delle password non è stata abilitata tramite la procedura guidata di Azure AD Connect, viene restituito l'errore seguente:

![La sincronizzazione delle password non è abilitata](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/phsglobaldisabled.png)

#### <a name="azure-ad-connect-server-is-in-staging-mode"></a>Il server di Azure AD Connect è in modalità di gestione temporanea
Se il server di Azure AD Connect è in modalità di gestione temporanea, la sincronizzazione delle password è temporaneamente disabilitata e viene restituito l'errore seguente:

![Il server di Azure AD Connect è in modalità di gestione temporanea](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/phsglobalstaging.png)

#### <a name="no-password-synchronization-heartbeat-events"></a>Eventi heartbeat di mancata sincronizzazione delle password
Ogni istanza locale di Active Directory Connector ha uno specifico canale di sincronizzazione delle password. Quando il canale di sincronizzazione delle password è attivo e non vi sono modifiche di password da sincronizzare, nel log eventi delle applicazioni di Windows viene generato un evento heartbeat (EventId 654) ogni 30 minuti. Per ogni istanza locale di Active Directory Connector, il cmdlet cerca gli eventi heartbeat corrispondenti che si sono verificati nelle ultime tre ore. Se la ricerca ha esito negativo, viene restituito l'errore seguente:

![Evento heartbeat di mancata sincronizzazione delle password](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/phsglobalnoheartbeat.png)

#### <a name="ad-ds-account-does-not-have-correct-permissions"></a>L'account di Active Directory Domain Services non ha le autorizzazioni corrette
Se l'account di Active Directory Domain Services usato dall'istanza locale di Active Directory Connector per sincronizzare gli hash delle password non ha le autorizzazioni appropriate, viene restituito l'errore seguente:

![Credenziali non corrette](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/phsglobalaccountincorrectpermission.png)

#### <a name="incorrect-ad-ds-account-username-or-password"></a>La password o il nome utente dell'account di Active Directory Domain Services non è corretto
Se l'account di Active Directory Domain Services usato dall'istanza locale di Active Directory Connector per sincronizzare gli hash delle password ha una password o un nome utente non corretto, viene restituito l'errore seguente:

![Credenziali non corrette](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/phsglobalaccountincorrectcredential.png)



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task"></a>Un oggetto non sincronizza le password: risolvere i problemi usando l'attività di risoluzione dei problemi

Per stabilire il motivo per cui un oggetto non sincronizza le password, è possibile usare l'attività di risoluzione dei problemi.

> [!NOTE]
> L'attività di risoluzione dei problemi è disponibile solo per Azure AD Connect versione 1.1.614.0 o successiva.

### <a name="run-the-diagnostics-cmdlet"></a>Eseguire il cmdlet di diagnostica
Per risolvere i problemi relativi a un oggetto utente specifico:

1. Aprire una nuova sessione di Windows PowerShell nel server di Azure AD Connect con l'opzione **Esegui come amministratore**.

2. Eseguire `Set-ExecutionPolicy RemoteSigned` o `Set-ExecutionPolicy Unrestricted`.

3. Avviare la procedura guidata di Azure AD Connect.

4. Passare alla pagina **Attività aggiuntive**, selezionare **Risoluzione dei problemi** e fare clic su **Avanti**.

5. Nella pagina Risoluzione dei problemi fare clic su **Avvia** per avviare il menu per la risoluzione dei problemi in PowerShell.

6. Dal menu principale scegliere **Risolvere i problemi di sincronizzazione delle password**.

7. Dal sottomenu scegliere **Password is not synchronized for a specific user account** (La password non viene sincronizzata per un oggetto utente specifico).

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Informazioni sui risultati dell'attività di risoluzione dei problemi
L'attività di risoluzione dei problemi effettua i controlli seguenti:

* Esamina lo stato dell'oggetto Active Directory nello spazio di Active Directory Connector, nel metaverse e nello spazio di Azure AD Connector.

* Verifica che siano definite regole di sincronizzazione con la sincronizzazione delle password abilitata e applicata all'oggetto Active Directory.

* Prova a recuperare e visualizzare i risultati dell'ultimo tentativo di sincronizzazione della password per l'oggetto.

Il diagramma seguente illustra i risultati del cmdlet durante la risoluzione dei problemi di sincronizzazione delle password per un singolo oggetto:

![Output di diagnostica per la sincronizzazione delle password: singolo oggetto](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/phssingleobjectgeneral.png)

La parte restante di questa sezione descrive i risultati specifici restituiti dal cmdlet e i problemi corrispondenti.

#### <a name="the-active-directory-object-isnt-exported-to-azure-ad"></a>L'oggetto Active Directory non viene esportato in Azure AD
La sincronizzazione delle password per questo account di Active Directory locale non riesce perché non è presente alcun oggetto corrispondente nel tenant di Azure AD. Viene restituito l'errore seguente:

![Oggetto Azure AD mancante](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/phssingleobjectnotexported.png)

#### <a name="user-has-a-temporary-password"></a>L'utente ha una password temporanea
Azure AD Connect non supporta attualmente la sincronizzazione delle password temporanee con Azure AD. Una password viene considerata temporanea se l'opzione **Cambiamento obbligatorio password all'accesso successivo** è impostata per l'utente di Active Directory locale. Viene restituito l'errore seguente:

![Password temporanea non esportata](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/phssingleobjecttemporarypassword.png)

#### <a name="results-of-last-attempt-to-synchronize-password-arent-available"></a>I risultati dell'ultimo tentativo di sincronizzare la password non sono disponibili
Per impostazione predefinita, Azure AD Connect archivia i risultati dei tentativi di sincronizzazione delle password per sette giorni. Se per l'oggetto Active Directory selezionato non sono disponibili risultati, viene restituito l'avviso seguente:

![Output di diagnostica per un singolo oggetto: cronologia di mancata sincronizzazione delle password](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/phssingleobjectnohistory.png)



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Nessuna password viene sincronizzata: risolvere i problemi tramite il cmdlet di diagnostica
Per stabilire il motivo per cui nessuna password viene sincronizzata, è possibile usare il cmdlet `Invoke-ADSyncDiagnostics`.

> [!NOTE]
> Il cmdlet `Invoke-ADSyncDiagnostics` è disponibile solo per Azure AD Connect versione 1.1.524.0 o successiva.

### <a name="run-the-diagnostics-cmdlet"></a>Eseguire il cmdlet di diagnostica
Per risolvere i problemi per cui nessuna password viene sincronizzata:

1. Aprire una nuova sessione di Windows PowerShell nel server di Azure AD Connect con l'opzione **Esegui come amministratore**.

2. Eseguire `Set-ExecutionPolicy RemoteSigned` o `Set-ExecutionPolicy Unrestricted`.

3. Eseguire `Import-Module ADSyncDiagnostics`.

4. Eseguire `Invoke-ADSyncDiagnostics -PasswordSync`.



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Un oggetto non sincronizza le password: risolvere i problemi tramite il cmdlet di diagnostica
Per stabilire il motivo per cui un oggetto non sincronizza le password è possibile usare il cmdlet `Invoke-ADSyncDiagnostics`.

> [!NOTE]
> Il cmdlet `Invoke-ADSyncDiagnostics` è disponibile solo per Azure AD Connect versione 1.1.524.0 o successiva.

### <a name="run-the-diagnostics-cmdlet"></a>Eseguire il cmdlet di diagnostica
Per risolvere i problemi per cui nessuna password viene sincronizzata per un utente:

1. Aprire una nuova sessione di Windows PowerShell nel server di Azure AD Connect con l'opzione **Esegui come amministratore**.

2. Eseguire `Set-ExecutionPolicy RemoteSigned` o `Set-ExecutionPolicy Unrestricted`.

3. Eseguire `Import-Module ADSyncDiagnostics`.

4. Eseguire il cmdlet seguente:
   ```
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName <Name-of-AD-Connector> -DistinguishedName <DistinguishedName-of-AD-object>
   ```
   Ad esempio: 
   ```
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName "contoso.com" -DistinguishedName "CN=TestUserCN=Users,DC=contoso,DC=com"
   ```



## <a name="no-passwords-are-synchronized-manual-troubleshooting-steps"></a>Nessuna password viene sincronizzata: passaggi per la risoluzione manuale dei problemi
Per stabilire il motivo per cui nessuna password viene sincronizzata, seguire questi passaggi:

1. Il server di connessione è in [modalità di gestione temporanea](active-directory-aadconnectsync-operations.md#staging-mode)? Un server in modalità di gestione temporanea non sincronizza le password.

2. Eseguire lo script nella sezione [Ottenere lo stato delle impostazioni di sincronizzazione password](#get-the-status-of-password-sync-settings). Fornisce una panoramica della configurazione della sincronizzazione password.  

    ![Output dello script di PowerShell dalle impostazioni di sincronizzazione password](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/psverifyconfig.png)  

3. Se la funzionalità non è abilitata in Azure AD o se lo stato del canale di sincronizzazione non è abilitato, eseguire l'Installazione guidata di Connect. Selezionare **Personalizzazione delle opzioni di sincronizzazione** e deselezionare l'opzione di sincronizzazione delle password. Questa modifica disabilita temporaneamente la funzionalità. Eseguire quindi di nuovo la procedura guidata e riabilitare la sincronizzazione password. Eseguire di nuovo lo script per verificare che la configurazione sia corretta.

4. Esaminare il log eventi per cercare eventuali errori. Cercare gli eventi seguenti che potrebbero indicare un problema:
    * Origine: ID "Sincronizzazione della directory": 0, 611, 652, 655 Se vengono visualizzati eventi di questo tipo, c'è un problema di connettività. Il messaggio del log eventi contiene informazioni relative alla foresta in cui è presente un problema. Per altre informazioni, vedere [Problemi di connettività](#connectivity problem).

5. Se non viene visualizzato alcun heartbeat o non si sono trovate altre soluzioni al problema, eseguire lo script riportato in [Attivare una sincronizzazione completa di tutte le password](#trigger-a-full-sync-of-all-passwords). Eseguire lo script una sola volta.

6. Vedere la sezione [Risolvere i problemi relativi a un oggetto che non sincronizza le password](#one-object-is-not-synchronizing-passwords).

### <a name="connectivity-problems"></a>Problemi di connettività

La connettività con Azure AD è presente?

L'account dispone delle autorizzazioni necessarie per leggere gli hash delle password in tutti i domini? Se si è installato Connect usando le impostazioni rapide, le autorizzazioni dovrebbero già essere corrette. 

Se invece si è usata l'installazione personalizzata, impostare manualmente le autorizzazioni eseguendo queste le operazioni:
    
1. Per trovare l'account usato dall'istanza di Active Directory Connector, avviare **Synchronization Service Manager**. 
 
2. Passare a **Connettori** e cercare la foresta di Active Directory locale per cui risolvere i problemi. 
 
3. Selezionare il connettore e quindi fare clic su **Proprietà**. 
 
4. Passare a **Connetti a Foresta Active Directory**.  
    
    ![Account usato da Active Directory Connector](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/connectoraccount.png)  
    Prendere nota del nome utente e del dominio in cui si trova l'account.
    
5. Avviare **Utenti e computer di Active Directory** e verificare che l'account trovato in precedenza disponga delle autorizzazioni seguenti impostate nella radice di tutti i domini della foresta:
    * Replica modifiche directory
    * Replica modifiche directory - Tutto

6. I controller di dominio sono raggiungibili da Azure AD Connect? Se il server Connect non riesce a connettersi a tutti i controller di dominio, configurare **Only use preferred domain controller** (Usare solo controller di dominio preferito).  
    
    ![Controller di dominio usato da Active Directory Connector](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/preferreddc.png)  
    
7. Tornare a **Synchronization Service Manager** e **Configure Directory Partition** (Configurare la partizione della directory). 
 
8. Selezionare il dominio in **Select directory partitions** (Selezionare le partizioni della directory), selezionare la casella di controllo **Only use preferred domain controller** (Usare solo controller di dominio preferito) e quindi fare clic su **Configura**. 

9. Nell'elenco immettere i controller di dominio che Connect deve usare per la sincronizzazione delle password. Lo stesso elenco viene usato anche per importare ed esportare. Eseguire questi passaggi per tutti i domini.

10. Se lo script mostra che non sono stati generati heartbeat, eseguire lo script riportato in [Attivare una sincronizzazione completa di tutte le password](#trigger-a-full-sync-of-all-passwords).

## <a name="one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps"></a>Un oggetto non sincronizza le password: passaggi per la risoluzione manuale dei problemi
È possibile risolvere facilmente i problemi di sincronizzazione password esaminando lo stato di un oggetto.

1. In **Utenti e computer di Active Directory** cercare l'utente e verificare che la casella di controllo **Cambiamento obbligatorio password all'accesso successivo** sia deselezionata.  

    ![Password per la produttività di Active Directory](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/adprodpassword.png)  

    Se la casella di controllo è selezionata, chiedere all'utente di accedere e modificare la password. Le password temporanee non vengono sincronizzate con Azure AD.

2. Se in Active Directory la password sembra corretta, seguire l'utente nel motore di sincronizzazione. Seguendo l'utente da Active Directory locale ad Azure AD, è possibile verificare se viene generato un errore descrittivo per l'oggetto.

    a. Avviare [Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md).

    b. Fare clic su **Connectors**(Connettori).

    c. Selezionare l'istanza di **Active Directory Connector** in cui si trova l'utente.

    d. Selezionare **Search Connector Space**(Cerca spazio connettore).

    e. Nella casella **Ambito** selezionare **DN or Anchor** (DN o ancoraggio) e quindi immettere il nome distinto completo dell'utente per il quale si devono risolvere i problemi.

    ![Cercare l'utente nello spazio connettore con nome distinto](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/searchcs.png)  

    f. Trovare l'utente e fare clic su **Proprietà** per visualizzare tutti gli attributi. Se l'utente non è incluso nei risultati della ricerca, verificare le [regole di filtro](active-directory-aadconnectsync-configure-filtering.md) e accertarsi di seguire le istruzioni in [Applicare e verificare le modifiche](active-directory-aadconnectsync-configure-filtering.md#apply-and-verify-changes) per visualizzare l'utente in Connect.

    g. Per visualizzare i dettagli della sincronizzazione della password dell'oggetto per la settimana precedente, fare clic su **Log**.  

    ![Dettagli del log dell'oggetto](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/csobjectlog.png)  

    Se il log dell'oggetto è vuoto, Azure AD Connect non è stato in grado di leggere l'hash della password da Active Directory. Continuare la risoluzione dei problemi con [Errori di connettività](#connectivity-errors). Se viene visualizzato un valore diverso da **success**, fare riferimento alla tabella [Log di sincronizzazione delle password](#password-sync-log).

    h. Selezionare la scheda **Lineage** (Derivazione) e verificare che almeno una regola di sincronizzazione nella colonna **PasswordSync** (Sincronizzazione password) sia impostata su **True**. Nella configurazione predefinita il nome della regola di sincronizzazione è **In from AD - User AccountEnabled**.  

    ![Informazioni sulla derivazione relative a un utente](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/cspasswordsync.png)  

    i. Fare clic su **Metaverse Object Properties** (Proprietà dell'oggetto Metaverse) per visualizzare un elenco di attributi utente.  

    ![Informazioni del metaverse](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/mvpasswordsync.png)  

    Verificare che non sia presente alcun attributo **cloudFiltered**. Assicurarsi che gli attributi di dominio (domainFQDN e domainNetBios) abbiano i valori previsti.

    j. Fare clic sulla scheda **Connettori**. Assicurarsi che i connettori per Active Directory locale e Azure AD siano visualizzati.

    ![Informazioni del metaverse](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/mvconnectors.png)  

    k. Selezionare la riga che rappresenta Azure AD, fare clic su **Proprietà** e quindi sulla scheda **Lineage** (Derivazione). L'oggetto spazio connettore deve avere una regola in uscita con la colonna **PasswordSync** (Sincronizzazione password) impostata su **True**. Nella configurazione predefinita il nome della regola di sincronizzazione è **Out to AAD - User Join**.  

    ![Finestra di dialogo Proprietà dell'oggetto spazio connettore](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/cspasswordsync2.png)  

### <a name="password-sync-log"></a>Log di sincronizzazione delle password
I valori possibili per la colonna dello stato sono i seguenti:

| Status | DESCRIZIONE |
| --- | --- |
| Success |La password è stata sincronizzata. |
| FilteredByTarget |La password è impostata su **Richiedi modifica della password all'accesso successivo**. La password non è stata sincronizzata. |
| NoTargetConnection |Nessun oggetto in metaverse o nello spazio connettore di Azure AD |
| SourceConnectorNotPresent |Nessun oggetto trovato nello spazio connettore di Active Directory locale. |
| TargetNotExportedToDirectory |L'oggetto nello spazio connettore di Azure AD non è stato ancora esportato. |
| MigratedCheckDetailsForMoreInfo |La voce di log è stata creata prima della compilazione 1.0.9125.0 e viene visualizzata nello stato precedente. |
| Tipi di errore |Il servizio ha restituito un errore sconosciuto. |
| Sconosciuto |Si è verificato un errore durante il tentativo di elaborare un batch di hash delle password.  |
| MissingAttribute |Gli attributi specifici (ad esempio hash Kerberos) richiesti da Azure AD Domain Services non sono disponibili. |
| RetryRequestedByTarget |Gli attributi specifici (ad esempio hash Kerberos) richiesti da Azure AD Domain Services non erano disponibili in precedenza. Viene effettuato un tentativo di risincronizzare l'hash della password dell'utente. |

## <a name="scripts-to-help-troubleshooting"></a>Script per facilitare la risoluzione dei problemi

### <a name="get-the-status-of-password-sync-settings"></a>Ottenere lo stato delle impostazioni di sincronizzazione password
```
Import-Module ADSync
$connectors = Get-ADSyncConnector
$aadConnectors = $connectors | Where-Object {$_.SubType -eq "Windows Azure Active Directory (Microsoft)"}
$adConnectors = $connectors | Where-Object {$_.ConnectorTypeName -eq "AD"}
if ($aadConnectors -ne $null -and $adConnectors -ne $null)
{
    if ($aadConnectors.Count -eq 1)
    {
        $features = Get-ADSyncAADCompanyFeature -ConnectorName $aadConnectors[0].Name
        Write-Host
        Write-Host "Password sync feature enabled in your Azure AD directory: "  $features.PasswordHashSync
        foreach ($adConnector in $adConnectors)
        {
            Write-Host
            Write-Host "Password sync channel status BEGIN ------------------------------------------------------- "
            Write-Host
            Get-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector.Name
            Write-Host
            $pingEvents =
                Get-EventLog -LogName "Application" -Source "Directory Synchronization" -InstanceId 654  -After (Get-Date).AddHours(-3) |
                    Where-Object { $_.Message.ToUpperInvariant().Contains($adConnector.Identifier.ToString("D").ToUpperInvariant()) } |
                    Sort-Object { $_.Time } -Descending
            if ($pingEvents -ne $null)
            {
                Write-Host "Latest heart beat event (within last 3 hours). Time " $pingEvents[0].TimeWritten
            }
            else
            {
                Write-Warning "No ping event found within last 3 hours."
            }
            Write-Host
            Write-Host "Password sync channel status END ------------------------------------------------------- "
            Write-Host
        }
    }
    else
    {
        Write-Warning "More than one Azure AD Connectors found. Please update the script to use the appropriate Connector."
    }
}
Write-Host
if ($aadConnectors -eq $null)
{
    Write-Warning "No Azure AD Connector was found."
}
if ($adConnectors -eq $null)
{
    Write-Warning "No AD DS Connector was found."
}
Write-Host
```

#### <a name="trigger-a-full-sync-of-all-passwords"></a>Attivare una sincronizzazione completa di tutte le password
> [!NOTE]
> Eseguire questo script una sola volta. Se è necessario eseguirlo più volte, il problema è dovuto a un'altra causa. Per risolverlo, contattare il supporto tecnico Microsoft.

È possibile attivare una sincronizzazione completa di tutte le password usando lo script seguente:

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
$aadConnector = "<CASE SENSITIVE AAD CONNECTOR NAME>"
Import-Module adsync
$c = Get-ADSyncConnector -Name $adConnector
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1
$c.GlobalParameters.Remove($p.Name)
$c.GlobalParameters.Add($p)
$c = Add-ADSyncConnector -Connector $c
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $false
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $true
```

## <a name="next-steps"></a>Passaggi successivi
* [Implementazione della sincronizzazione password con il servizio di sincronizzazione Azure AD Connect](active-directory-aadconnectsync-implement-password-synchronization.md)
* [Servizio di sincronizzazione Azure AD Connect: Comprendere e personalizzare la sincronizzazione](active-directory-aadconnectsync-whatis.md)
* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)
