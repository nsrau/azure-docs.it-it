---
title: Risolvere i problemi di sincronizzazione della password con il servizio di sincronizzazione Azure AD Connect | Microsoft Docs
description: L&quot;articolo contiene informazioni sulla risoluzione dei problemi di sincronizzazione della password
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: e7ad2cb4c464c7095f704cc137f1c42422fbb40e
ms.openlocfilehash: 4c42821b95d666721b84d4976966b4886e517193
ms.lasthandoff: 03/01/2017


---
# <a name="troubleshoot-password-synchronization-with-azure-ad-connect-sync"></a>Risolvere i problemi di sincronizzazione della password con il servizio di sincronizzazione Azure AD Connect
In questo argomento viene descritta la procedura per risolvere i problemi di sincronizzazione della password. Se le password non vengono sincronizzate come previsto, può essere per un subset di utenti o per tutti gli utenti.

* Se si verifica un problema in cui non viene sincronizzata alcuna password, vedere [Risolvere i problemi in cui non viene sincronizzata alcuna password](#no-passwords-are-synchronized).
* Se si verifica un problema con singoli oggetti, vedere [Risolvere i problemi relativi a un oggetto che non sincronizza le password](#one-object-is-not-synchronizing-passwords).

## <a name="no-passwords-are-synchronized"></a>Le password non sono sincronizzate
Seguire questi passaggi per scoprire il motivo per cui le password non sono sincronizzate:

1. Il server di connessione è in [modalità di gestione temporanea](active-directory-aadconnectsync-operations.md#staging-mode)? Un server in modalità di gestione temporanea non sincronizza le password.
2. Eseguire lo script nella sezione [Ottenere lo stato delle impostazioni di sincronizzazione password](#get-the-status-of-password-sync-settings). Fornisce una panoramica della configurazione della sincronizzazione password.  
![Output dello script di PowerShell dalle impostazioni di sincronizzazione password](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/psverifyconfig.png)  
3. Se la funzionalità non è abilitata in Azure AD o se lo stato di sincronizzazione del canale non è abilitato, eseguire l'installazione guidata di Connect. Selezionare **Personalizzazione delle opzioni di sincronizzazione** e deselezionare l'opzione di sincronizzazione password. Questa modifica disabilita temporaneamente la funzionalità. Eseguire quindi di nuovo la procedura guidata e riabilitare la sincronizzazione password. Eseguire di nuovo lo script per verificare che la configurazione sia corretta.
4. Esaminare il registro eventi per trovare gli errori. Cercare gli eventi seguenti che potrebbero indicare un problema:
    1. Origine: ID "Sincronizzazione della directory": 0, 611, 652, 655 se viene visualizzato uno di questi eventi c'è un problema di connettività. Il messaggio del registro eventi contiene informazioni relative alla foresta in cui è presente il problema. Per altre informazioni sulla connettività client, vedere [Problemi di connettività](#connectivity problem)
5. Se non viene visualizzato alcun heartbeat o se altri elementi non sono stati eseguiti correttamente, eseguire [Trigger a full sync of all passwords](#trigger-a-full-sync-of-all-passwords) (Attivare una sincronizzazione completa di tutte le password). Eseguire questo script solo una volta.
6. Leggere la sezione [Risolvere i problemi relativi a un oggetto che non sincronizza le password](#one-object-is-not-synchronizing-passwords).

### <a name="connectivity-problem"></a>Problema di connettività

1. L'utente dispone della connettività con Azure AD?
2. L'account dispone delle autorizzazioni necessarie per leggere gli hash delle password in tutti i domini? Se è installato Connect con le impostazioni rapide, le autorizzazioni dovrebbero già essere corrette. Se l'utente ha seguito l'installazione personalizzata, è necessario impostare manualmente le autorizzazioni.
    1. Per trovare l'account usato dal connettore di Active Directory, avviare **Synchronization Service Manager**. Passare a **Connettori** e trovare la foresta di Active Directory locale per cui si desidera risolvere i problemi. Selezionare il connettore e fare clic su **Proprietà**. Passare a **Connetti a Foresta Active Directory**.  
    ![Account usato dal connettore di AD](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/connectoraccount.png)  
    Prendere nota del nome utente e del dominio in cui si trova l'account.
    2. Avviare **Utenti e computer di Active Directory**. Verificare che l'account trovato nel passaggio precedente disponga delle autorizzazioni seguenti impostate alla radice di tutti i domini della foresta:
        * Replica modifiche directory
        * Replica modifiche directory - Tutto
3. I controller di dominio sono raggiungibili da Azure AD Connect? Se il server Connect non può connettersi a tutti i controller di dominio, è necessario configurare **Only use preferred domain controller** (Usare solo controller di dominio preferito).  
    ![Controller di dominio usato dal connettore di AD](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/preferreddc.png)  
    Tornare a **Synchronization Service Manager** e **Configure Directory Partition** (Configurare la partizione della directory). Selezionare il dominio in **Select directory partitions** (Selezionare le partizioni della directory), selezionare la casella di controllo **Only use preferred domain controller** (Usare solo controller di dominio preferito) e fare clic su **Configura**. Nell'elenco, immettere i controller di dominio che Connect deve usare per la sincronizzazione delle password. Lo stesso elenco viene usato anche per importare ed esportare. Eseguire questi passaggi per tutti i domini.
4. Se lo script indica che non sono stati ricevuti heartbeat, eseguire lo script disponibile in [Attivare una sincronizzazione completa di tutte le password](#trigger-a-full-sync-of-all-passwords).

## <a name="one-object-is-not-synchronizing-passwords"></a>Un oggetto non sincronizza le password
È possibile risolvere facilmente i problemi di sincronizzazione password esaminando lo stato di un oggetto.

1. Avviare **Utenti e computer di Active Directory**. Trovare l'utente e verificare che **Cambiamento obbligatorio password all'accesso successivo** sia deselezionata.  
![Password per la produttività di Active Directory](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/adprodpassword.png)  
Se è selezionata, chiedere all'utente di accedere e modificare la password. Le password temporanee non vengono sincronizzate con Azure AD.
2. Se in Active Directory non vengono rilevati problemi, il passaggio successivo consiste nel seguire l'utente nel motore di sincronizzazione. Seguendo l'utente da Active Directory locale ad Azure AD, è possibile vedere se è presente un errore descrittivo per l'oggetto.
    1. Avviare **[Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md)**.
    2. Fare clic su **Connectors**(Connettori).
    3. Selezionare l'istanza di **Active Directory Connector** in cui si trova l'utente.
    4. Selezionare **Search Connector Space**(Cerca spazio connettore).
    5. In **Ambito**selezionare **DN or anchor** (DN o ancoraggio). Immettere il DN completo dell'utente per cui si risolvono i problemi.
    ![Ricerca per utente in cs con DN](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/searchcs.png)  
    6. Individuare l'utente che si sta cercando e fare clic su **Proprietà** per visualizzare tutti gli attributi. Se l'utente non è incluso nei risultati della ricerca, verificare le [regole di filtro](active-directory-aadconnectsync-configure-filtering.md) e accertarsi di eseguire [Applica e verifica le modifiche](active-directory-aadconnectsync-configure-filtering.md#apply-and-verify-changes) per visualizzare l'utente in Connect.
    7. Per visualizzare i dettagli della sincronizzazione password dell'oggetto per la settimana precedente, fare clic su **Log...**(Log...).  
    ![Dettagli del log oggetti](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/csobjectlog.png)  
    Se il log oggetti è vuoto, Azure AD Connect non è stato in grado di leggere l'hash della password da Active Directory. Continuare la risoluzione dei problemi con [Errori di connettività](#connectivity-errors). Se viene visualizzato qualsiasi valore diverso da **success**, fare riferimento alla tabella [Log di sincronizzazione delle password](#password-sync-log).
    8. Selezionare la scheda **Lineage** (Derivazione) e verificare che **Password Sync** (Sincronizzazione password) sia impostata su **True** almeno per una regola di sincronizzazione. Nella configurazione predefinita il nome della regola di sincronizzazione è **In from AD - User AccountEnabled**(In entrata da AD - Account utente abilitato).  
    ![Informazioni sulla derivazione relative a un utente](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/cspasswordsync.png)  
    9. Fare clic su **Metaverse Object Properties** (Proprietà dell'oggetto Metaverse). Viene visualizzato un elenco di attributi dell'utente.  
    ![Informazioni su Metaverse](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/mvpasswordsync.png)  
    Verificare che non sia presente alcun attributo **cloudFiltered**. Assicurarsi che gli attributi di dominio (domainFQDN e domainNetBios) abbiano i valori previsti.
    10. Fare clic sulla scheda **Connettori**. Assicurarsi di visualizzare i connettori per AD locale e Azure AD.
    ![Informazioni su Metaverse](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/mvconnectors.png)  
    11. Selezionare la riga che rappresenta Azure AD e fare clic su **Proprietà**. Fare clic sulla scheda **Derivazione**. L'oggetto spazio connettore deve avere una regola in uscita con **Password Sync** (Sincronizzazione password) impostata su **True**. Nella configurazione predefinita il nome della regola di sincronizzazione è **Out to AAD - User Join**(In uscita ad AAD - Aggiunta utente).  
    ![Proprietà dello spazio connettore di un utente](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/cspasswordsync2.png)  

### <a name="password-sync-log"></a>Log di sincronizzazione delle password
I valori possibili per la colonna dello stato sono i seguenti:

| Stato | Description |
| --- | --- |
| Success |La password è stata sincronizzata. |
| FilteredByTarget |La password è impostata su **Richiedi modifica della password all'accesso successivo**. La password non è stata sincronizzata. |
| NoTargetConnection |Nessun oggetto in metaverse o nello spazio connettore di Azure AD |
| SourceConnectorNotPresent |Nessun oggetto trovato nello spazio connettore di Active Directory locale. |
| TargetNotExportedToDirectory |L'oggetto nello spazio connettore di Azure AD non è stato ancora esportato. |
| MigratedCheckDetailsForMoreInfo |La voce di log è stata creata prima della compilazione 1.0.9125.0 e viene visualizzata nello stato precedente. |

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
> Eseguire questo script solo una volta. Se è necessario eseguirlo più volte, il problema è un altro. Contattare il supporto tecnico Microsoft per risolvere il problema.

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
* [Servizio di sincronizzazione Azure AD Connect: Personalizzazione delle opzioni di sincronizzazione](active-directory-aadconnectsync-whatis.md)
* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)

