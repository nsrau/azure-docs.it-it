<properties
	pageTitle="Implementazione della sincronizzazione password con il servizio di sincronizzazione Azure AD Connect | Microsoft Azure"
	description="Fornisce informazioni sul funzionamento e sull'abilitazione della sincronizzazione password."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>
<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2016"
	ms.author="markusvi;andkjell"/>


# Implementazione della sincronizzazione password con il servizio di sincronizzazione Azure AD Connect
Questo argomento contiene le indicazioni necessarie per sincronizzare le password utente da un'istanza di Active Directory (AD) locale a un'istanza di Azure Active Directory basata sul cloud (Azure AD).

## Informazioni sulla sincronizzazione password
La probabilità di rimanere bloccati e non poter portare a termine il proprio lavoro perché si ha dimenticato una password è proporzionale al numero di password diverse che si devono ricordare. Più password si devono ricordare, maggiore sarà la probabilità di dimenticarne una. Le domande e le chiamate relative alla reimpostazione della password e ad altri problemi correlati impegnano la maggior parte delle risorse di supporto tecnico.

La sincronizzazione password è una funzionalità che permette di sincronizzare le password utente da un'istanza di Active Directory locale a un'istanza di Azure Active Directory (Azure AD) basata sul cloud. Questa funzionalità consente di accedere ai servizi Azure Active Directory, come Office 365, Microsoft Intune, CRM Online e Servizi di dominio Azure AD, usando la stessa password che si usa per accedere all'istanza locale di Active Directory.

![Cos'è Azure AD Connect](./media/active-directory-aadconnectsync-implement-password-synchronization/arch1.png)  

Poiché riduce il numero di password necessarie agli utenti a una sola, la sincronizzazione password consente di:

- Migliorare la produttività degli utenti
- Ridurre i costi del supporto tecnico

Se si decide di usare la[**federazione con AD FS**](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect), è anche possibile abilitare la sincronizzazione password come backup in caso di errore dell'infrastruttura AD FS.

La sincronizzazione password è un'estensione della funzionalità di sincronizzazione della directory implementata dal relativo servizio Azure AD Connect. Per usare la sincronizzazione password nel proprio ambiente, è necessario:

- Installare Azure AD Connect
- Configurare la sincronizzazione delle directory tra l'istanza di Active Directory locale e Azure Active Directory
- Abilitare la sincronizzazione password

Per altre informazioni, vedere [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).

> [AZURE.NOTE] Per altre informazioni dettagliate sui Servizi di dominio Active Directory configurati per FIPS e per la sincronizzazione password, vedere [Sincronizzazione password e FIPS](#password-synchronization-and-fips)

## Funzionamento della sincronizzazione password
Servizi di dominio Active Directory archivia le password come rappresentazione del valore hash della password utente effettiva. Un valore hash è il risultato di una funzione matematica unidirezionale, detta "*algoritmo hash*". Non esiste un metodo per ripristinare la versione in testo normale di una password dal risultato di una funzione unidirezionale. Non è possibile usare l'hash della password per accedere alla rete locale.

Per sincronizzare la password, il servizio di sincronizzazione Azure AD Connect estrae l'hash della password dall'istanza di Active Directory locale. All'hash della password viene applicata un'elaborazione di sicurezza aggiuntiva prima della sincronizzazione con il servizio di autenticazione di Azure Active Directory. Le password vengono sincronizzate per ogni singolo utente e in ordine cronologico.

L'effettivo flusso di dati del processo di sincronizzazione password è simile alla sincronizzazione degli dati utente come il nome visualizzato o gli indirizzi di posta elettronica. Le password vengono tuttavia sincronizzate con una frequenza maggiore rispetto alla finestra di sincronizzazione standard della directory per altri attributi. Il processo di sincronizzazione password viene eseguito ogni 2 minuti. Non è possibile modificare la frequenza del processo. Se si sincronizza una password, la password sincronizzata sovrascrive quella esistente nel cloud.

Quando si abilita la funzionalità di sincronizzazione password per la prima volta, viene eseguita una sincronizzazione iniziale delle password di tutti gli utenti inclusi nell'ambito. Non è possibile definire in modo esplicito un subset delle password utente da sincronizzare.

Quando si modifica una password locale, la password aggiornata viene sincronizzata, spesso in pochi minuti. In caso di sincronizzazioni non riuscite, la funzionalità di sincronizzazione password esegue anche automaticamente nuovi tentativi di sincronizzazione. Se si verifica un errore durante un tentativo di sincronizzazione di una password, viene registrato un errore nel visualizzatore eventi.

La sincronizzazione di una password non incide in alcun modo sull'utente attualmente connesso. Se si modifica una password sincronizzata dopo aver eseguito l'accesso a un servizio cloud, la sessione corrente del servizio cloud non è immediatamente interessata. Tuttavia, quando il servizio cloud richiede di nuovo l'autenticazione, è necessario specificare la nuova password.

> [AZURE.NOTE] La sincronizzazione delle password è supportata solo per l'utente del tipo di oggetto in Active Directory. Non è supportata per il tipo di oggetto iNetOrgPerson.

### Funzionamento della sincronizzazione password con Servizi di dominio Azure AD
È possibile usare la funzionalità di sincronizzazione password per sincronizzare le password locali con [Servizi di dominio Azure AD](../active-directory-domain-services/active-directory-ds-overview.md). Questo scenario consente a Servizi di dominio Azure AD di autenticare gli utenti nel cloud con tutti i metodi disponibili nell'istanza di Active Directory locale. L'esperienza di questo scenario è simile all'uso dell'Utilità di migrazione ad Active Directory (ADMT) in un ambiente locale.

### Considerazioni relative alla sicurezza
Quando si sincronizzano le password, la versione in testo normale della password non viene esposta alla funzionalità di sincronizzazione password, né ad Azure AD o a qualsiasi servizio associato.

Nell'istanza locale di Active Directory non è prevista neppure l'archiviazione della password in un formato con crittografia reversibile. Per la trasmissione tra Active Directory locale e Azure Active Directory viene usato un digest dell'hash della password di Active Directory. Il digest dell'hash della password non può essere usato per accedere alle risorse nell'ambiente locale.

### Considerazioni relative ai criteri password
L'abilitazione della sincronizzazione password influisce su due tipi di criteri password:

1. Criteri di complessità delle password
2. Criteri di scadenza delle password

**Criteri di complessità delle password** Quando si abilita la sincronizzazione password, i criteri di complessità delle password presenti nell'istanza locale di Active Directory sostituiscono i criteri di complessità definiti nel cloud per gli utenti sincronizzati. È possibile usare tutte le password valide di Active Directory locale per accedere ai servizi Azure AD.

> [AZURE.NOTE] Le password degli utenti create direttamente nel cloud restano soggette ai criteri password definiti nel cloud.

**Criteri di scadenza delle password** Se un utente è incluso nell'ambito della sincronizzazione password, la password dell'account cloud viene impostata su "*Non scade mai*". È possibile continuare ad accedere ai servizi cloud usando una password sincronizzata che è in realtà scaduta nell'ambiente locale. La password cloud viene aggiornata alla modifica successiva della password nell'ambiente locale.

### Sostituzione delle password sincronizzate
Un amministratore può reimpostare manualmente la password usando Windows PowerShell.

In tal caso, la password sincronizzata viene sostituita dalla nuova password, a cui si applicano tutti i criteri password definiti nel cloud.

Se si modifica nuovamente la password locale, la nuova password viene sincronizzata nel cloud e sostituisce la password aggiornata manualmente.

## Abilitazione della sincronizzazione password
Se si usano le **Impostazioni rapide** quando si installa Azure AD Connect, la sincronizzazione password viene abilitata automaticamente. Per altri dettagli, vedere [Introduzione alle impostazioni rapide per Azure AD Connect](active-directory-aadconnect-get-started-express.md).

Se si usano le impostazioni personalizzate quando si installa Azure AD Connect, la sincronizzazione password viene abilitata nella pagina di accesso dell'utente. Per altre informazioni, vedere [Installazione personalizzata di Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

![Abilitazione della sincronizzazione password](./media/active-directory-aadconnectsync-implement-password-synchronization/usersignin.png)  

### Sincronizzazione password e FIPS
Se il server è stato bloccato in conformità allo standard FIPS (Federal Information Processing Standard), MD5 è stato disabilitato.

**Per abilitare MD5 per la sincronizzazione password, seguire questa procedura:**

1. Passare a **%programfiles%\\Azure AD Sync\\Bin**.
2. Aprire **miiserver.exe.config**.
3. Passare al nodo **configuration/runtime** alla fine del file.
4. Aggiungere il nodo seguente: `<enforceFIPSPolicy enabled="false"/>`
5. Salvare le modifiche.

Per riferimento, questo elemento di cattura mostra come apparirà:

```
	<configuration>
		<runtime>
			<enforceFIPSPolicy enabled="false"/>
		</runtime>
	</configuration>
```

Per informazioni sulla sicurezza e su FIPS, vedere il post di blog relativo alla [conformità di FIPS, della crittografia e della sincronizzazione password di ADD](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/)

## Risoluzione dei problemi di sincronizzazione password
Se le password non vengono sincronizzate come previsto, può essere per un subset di utenti o per tutti gli utenti.

- Se si verifica un problema con singoli oggetti, vedere [Risolvere i problemi relativi a un oggetto che non sincronizza le password](#troubleshoot-one-object-that-is-not-synchronizing-passwords).
- Se si verifica un problema in cui non viene sincronizzata alcuna password, vedere [Risolvere i problemi in cui non viene sincronizzata alcuna password](#troubleshoot-issues-where-no-passwords-are-synchronized).

### Risolvere i problemi relativi a un oggetto che non sincronizza le password
È possibile risolvere facilmente i problemi di sincronizzazione password esaminando lo stato di un oggetto.

Avviare **Utenti e computer di Active Directory**. Trovare l'utente e verificare che **Cambiamento obbligatorio password all'accesso successivo** sia deselezionata. ![Password per la produttività di Active Directory](./media/active-directory-aadconnectsync-implement-password-synchronization/adprodpassword.png) Se è selezionata, chiedere all'utente di accedere e modificare la password. Le password temporanee non vengono sincronizzate con Azure AD.

Se in Active Directory non vengono rilevati problemi, il passaggio successivo consiste nel seguire l'utente nel motore di sincronizzazione. Seguendo l'utente da Active Directory locale ad Azure AD, è possibile vedere se è presente un errore descrittivo per l'oggetto.

1. Avviare **[Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md)**.
2. Fare clic su **Connectors** (Connettori).
3. Selezionare l'istanza di **Active Directory Connector** in cui si trova l'utente.
4. Selezionare **Search Connector Space** (Cerca spazio connettore).
5. Trovare l'utente cercato.
6. Selezionare la scheda **Lineage** (Derivazione) e verificare che **Password Sync** (Sincronizzazione password) sia impostata su **True** almeno per una regola di sincronizzazione. Nella configurazione predefinita il nome della regola di sincronizzazione è **In from AD - User AccountEnabled** (In entrata da AD - Account utente abilitato). ![Informazioni sulla derivazione relative a un utente](./media/active-directory-aadconnectsync-implement-password-synchronization/cspasswordsync.png)
7. Quindi [seguire l'utente](active-directory-aadconnectsync-service-manager-ui-connectors.md#follow-an-object-and-its-data-through-the-system) nel metaverse fino allo spazio del connettore Azure AD. L'oggetto spazio connettore deve avere una regola in uscita con **Password Sync** (Sincronizzazione password) impostata su **True**. Nella configurazione predefinita il nome della regola di sincronizzazione è **Out to AAD - User Join** (In uscita ad AAD - Aggiunta utente). ![Proprietà dello spazio connettore di un utente](./media/active-directory-aadconnectsync-implement-password-synchronization/cspasswordsync2.png)
8. Per visualizzare i dettagli della sincronizzazione password dell'oggetto per la settimana precedente, fare clic su **Log...** (Log...). ![Dettagli del log oggetti](./media/active-directory-aadconnectsync-implement-password-synchronization/csobjectlog.png)

I valori possibili per la colonna dello stato sono i seguenti:

Stato | Description
---- | -----
Success | La password è stata sincronizzata.
FilteredByTarget | La password è impostata su **Richiedi modifica della password all'accesso successivo**. La password non è stata sincronizzata.
NoTargetConnection | Nessun oggetto in metaverse o nello spazio connettore di Azure AD
SourceConnectorNotPresent | Nessun oggetto trovato nello spazio connettore di Active Directory locale.
TargetNotExportedToDirectory | L'oggetto nello spazio connettore di Azure AD non è stato ancora esportato.
MigratedCheckDetailsForMoreInfo | La voce di log è stata creata prima della compilazione 1.0.9125.0 e viene visualizzata nello stato precedente.

### Risolvere i problemi in cui non viene sincronizzata alcuna password
Iniziare eseguendo lo script nella sezione [Ottenere lo stato delle impostazioni di sincronizzazione password](#get-the-status-of-password-sync-settings). Fornisce una panoramica della configurazione della sincronizzazione password. ![Output dello script di PowerShell dalle impostazioni di sincronizzazione password](./media/active-directory-aadconnectsync-implement-password-synchronization/psverifyconfig.png) Se la funzionalità non è abilitata in Azure AD o se lo stato di sincronizzazione del canale non è abilitato, eseguire l'installazione guidata di Connect. Selezionare **Personalizzazione delle opzioni di sincronizzazione** e deselezionare l'opzione di sincronizzazione password. Questa modifica disabilita temporaneamente la funzionalità. Eseguire quindi di nuovo la procedura guidata e riabilitare la sincronizzazione password. Eseguire di nuovo lo script per verificare che la configurazione sia corretta.

Se lo script indica che non sono stati ricevuti heartbeat, eseguire lo script disponibile in [Attivare una sincronizzazione completa di tutte le password](#trigger-a-full-sync-of-all-passwords). Questo script può essere usato anche per altri scenari in cui la configurazione è corretta, ma non vengono sincronizzate le password.

#### Ottenere lo stato delle impostazioni di sincronizzazione password

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

#### Attivare una sincronizzazione completa di tutte le password
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

## Passaggi successivi

* [Servizio di sincronizzazione Azure AD Connect: Personalizzazione delle opzioni di sincronizzazione](active-directory-aadconnectsync-whatis.md)
* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0824_2016-->