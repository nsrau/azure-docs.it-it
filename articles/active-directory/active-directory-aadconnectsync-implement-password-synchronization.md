<properties
	pageTitle="Implementazione della sincronizzazione password con il servizio di sincronizzazione Azure AD Connect | Microsoft Azure"
	description="Fornisce informazioni sul funzionamento e sull'abilitazione della sincronizzazione password."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>
<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/15/2016"
	ms.author="markusvi;andkjell"/>


# Implementazione della sincronizzazione password con il servizio di sincronizzazione Azure AD Connect

La sincronizzazione password permette di usare la password dell'istanza di Active Directory locale per accedere anche ad Azure Active Directory.

Questo argomento fornisce le informazioni necessarie per abilitare la sincronizzazione password nell'ambiente corrente e per risolvere i relativi problemi.


## Informazioni sulla sincronizzazione password

La sincronizzazione password è una funzionalità che permette di sincronizzare le password utente da un'istanza di Active Directory locale a un'istanza di Azure Active Directory (Azure AD) basata sul cloud. Permette di accedere ai servizi di Azure Active Directory, ad esempio Office 365, Microsoft Intune e CRM Online, usando la stessa password con cui si accede alla rete locale. Per usare questa funzionalità, è necessario installare il servizio di sincronizzazione Azure Active Directory Connect (servizio di sincronizzazione Azure AD Connect).


> [AZURE.NOTE] Per altre informazioni dettagliate sui Servizi di dominio Active Directory configurati per FIPS e per la sincronizzazione password, vedere [Sincronizzazione password e FIPS](#password-synchronization-and-fips)

## Funzionamento della sincronizzazione password

La sincronizzazione password è un'estensione della funzionalità di sincronizzazione della directory implementata dal relativo servizio Azure AD Connect. Richiede quindi la configurazione della sincronizzazione della directory tra l'istanza di Active Directory locale e Azure Active Directory.

Servizi di dominio Active Directory archivia le password come rappresentazione del valore hash della password utente effettiva. Non è possibile usare un hash della password per:

- Accedere alla rete locale.

- Ripristinare la versione in testo normale di una password.

Per sincronizzare la password, il servizio di sincronizzazione Azure AD Connect estrae l'hash della password dall'istanza di Active Directory locale. All'hash della password viene applicata un'elaborazione di sicurezza aggiuntiva prima della sincronizzazione con il servizio di autenticazione di Azure Active Directory. L'effettivo flusso di dati del processo di sincronizzazione password è simile alla sincronizzazione degli dati utente come il nome visualizzato o gli indirizzi di posta elettronica.

Le password vengono sincronizzate:

- In base al singolo utente.

- In ordine cronologico.

- Con una frequenza maggiore rispetto alla finestra di sincronizzazione standard della directory per altri attributi.

La password sincronizzata sovrascrive la password esistente nel cloud.

Quando si abilita la funzionalità di sincronizzazione password per la prima volta, viene eseguita una sincronizzazione iniziale delle password di tutti gli utenti nell'ambito. Non è possibile definire in modo esplicito un subset delle password utente da sincronizzare.

Quando si modifica una password locale, la password aggiornata viene sincronizzata, spesso in pochi minuti. La funzionalità effettua inoltre automaticamente nuovi tentativi di sincronizzazione in caso di sincronizzazioni non riuscite. Se si verifica un errore durante un tentativo di sincronizzazione di una password, viene registrato un errore nel visualizzatore eventi.

La sincronizzazione di una password non incide in alcun modo sull'utente attualmente connesso. Se una modifica della password viene sincronizzata mentre si è connessi a un servizio cloud, la sessione del servizio cloud non è immediatamente interessata dall'operazione. Tuttavia, quando il servizio cloud richiede di nuovo l'autenticazione, è necessario specificare la nuova password.

> [AZURE.NOTE] La sincronizzazione delle password è supportata solo per l'utente del tipo di oggetto in Active Directory. Non è supportata per il tipo di oggetto iNetOrgPerson.

### Funzionamento della sincronizzazione password con Servizi di dominio Azure AD

Se si abilita questo servizio in Azure AD, è necessaria l'opzione di sincronizzazione password per ottenere un'esperienza Single Sign-On. Quando questo servizio è abilitato, viene modificato il comportamento della sincronizzazione password e di conseguenza vengono sincronizzati anche gli hash delle password dall'istanza di Active Directory locale a Servizi di dominio Azure AD. La funzionalità è simile all'Utilità di migrazione ad Active Directory (ADMT) e consente a Servizi di dominio Azure AD di autenticare l'utente con tutti i metodi disponibili nell'istanza di Active Directory locale.

### Considerazioni relative alla sicurezza

Quando si sincronizzano le password, la versione in testo normale della password non viene esposta alla funzionalità di sincronizzazione password, ad Azure AD o a qualsiasi servizio associato.

Inoltre, nell'istanza locale di Active Directory non è prevista l'archiviazione della password in un formato con crittografia reversibile. Per la trasmissione tra Active Directory locale e Azure Active Directory viene usato un digest dell'hash della password di Active Directory. Il digest dell'hash della password non può essere usato per accedere alle risorse nell'ambiente locale.

### Considerazioni relative ai criteri password

L'abilitazione della sincronizzazione password influisce su due tipi di criteri password:

1. Criteri di complessità delle password
2. Criteri di scadenza delle password

**Criteri di complessità delle password**

Quando si abilita la sincronizzazione password, i criteri di complessità delle password configurati nell'istanza locale di Active Directory sostituiscono gli eventuali criteri di complessità definiti nel cloud per gli utenti sincronizzati. Questo significa che è possibile usare qualsiasi password valida nell'ambiente dell'istanza locale di Active Directory del cliente per accedere ai servizi di Azure Active Directory.

> [AZURE.NOTE] Le password degli utenti create direttamente nel cloud restano soggette ai criteri password definiti nel cloud.

**Criteri di scadenza delle password**

Se un utente è nell'ambito della sincronizzazione password, la password dell'account cloud viene impostata su *Nessuna scadenza*. Ciò significa che è possibile continuare ad accedere ai servizi cloud con una password sincronizzata che è scaduta nell'ambiente locale.

La password cloud viene aggiornata alla modifica successiva della password nell'ambiente locale.

### Sostituzione delle password sincronizzate

Un amministratore può reimpostare manualmente la password tramite PowerShell.

In tal caso, la password sincronizzata viene sostituita dalla nuova password, a cui si applicano tutti i criteri password definiti nel cloud.

Se si modifica nuovamente la password locale, la nuova password viene sincronizzata nel cloud e sostituisce la password aggiornata manualmente.


## Abilitazione della sincronizzazione password

Per abilitare la sincronizzazione password, sono disponibili due opzioni:

- Se si usano le impostazioni rapide quando si installa Azure AD Connect, la sincronizzazione password viene abilitata per impostazione predefinita.

- Se si usano le impostazioni personalizzate quando si installa Azure AD Connect, la sincronizzazione password viene abilitata nella pagina di accesso dell'utente.

<br> ![Abilitazione della sincronizzazione password](./media/active-directory-aadconnectsync-implement-password-synchronization/usersignin.png) <br>

Se si sceglie di usare **Federazione tramite ADFS**, è anche possibile abilitare la sincronizzazione password come backup in caso di errore dell'infrastruttura AD FS. È inoltre possibile abilitarla se si prevede di usare Servizi di dominio Azure AD.

### Sincronizzazione password e FIPS

Se il server è stato bloccato in base a FIPS (Federal Information Processing Standard), MD5 è stato disabilitato. Per abilitarlo per la sincronizzazione delle password, aggiungere la chiave enforceFIPSPolicy in miiserver.exe.config in C:\\Programmi\\Azure AD Sync\\Bin.

```
<configuration>
    <runtime>
        <enforceFIPSPolicy enabled="false"/>
    </runtime>
</configuration>
```

Il nodo di configurazione/runtime si trova alla fine del file di configurazione.

Per informazioni sulla sicurezza e su FIPS, vedere il post di blog relativo alla [conformità di FIPS, della crittografia e della sincronizzazione password di ADD](http://blogs.technet.com/b/ad/archive/2014/06/28/aad-password-sync-encryption-and-and-fips-compliance.aspx)


## Risoluzione dei problemi di sincronizzazione password

**Per risolvere i problemi di sincronizzazione password, seguire questa procedura:**

1. Aprire **Synchronization Service Manager**.

2. Fare clic su **Connectors**.

3. Selezionare l'Active Directory Connector in cui si trova l'utente.

4. Selezionare **Search Connector Space**.

5. Trovare l'utente cercato.

6. Selezionare la scheda **Lineage** e verificare che almeno per una regola di sincronizzazione **PasswordSync** sia **True**. Nella configurazione predefinita il nome della regola di sincronizzazione è **In from AD - User AccountEnabled**.

    ![Informazioni sulla derivazione relative a un utente](./media/active-directory-aadconnectsync-implement-password-synchronization/cspasswordsync.png)

7. È anche necessario [seguire l'utente](active-directory-aadconnectsync-service-manager-ui-connectors.md#follow-an-object-and-its-data-through-the-system) attraverso il metaverse fino allo spazio di Azure AD Connector. Assicurarsi che l'oggetto dello spazio connettore abbia una regola in uscita con **PasswordSync** impostato su **True**. Nella configurazione predefinita il nome della regola di sincronizzazione è **Out to AAD - User Join**.

    ![Proprietà dello spazio connettore di un utente](./media/active-directory-aadconnectsync-implement-password-synchronization/cspasswordsync2.png)

8. Per visualizzare i dettagli di sincronizzazione password per l'oggetto, fare clic sul pulsante **Log...**.<br> Viene creata una pagina con una visualizzazione cronologia dello stato di sincronizzazione password dell'utente relativa alla settimana precedente.

    ![Dettagli del log oggetti](./media/active-directory-aadconnectsync-implement-password-synchronization/csobjectlog.png)

I valori possibili per la colonna dello stato sono i seguenti:

| Stato | Descrizione |
| ---- | ----- |
| Success | La password è stata sincronizzata. |
| FilteredByTarget | La password è impostata su **Richiedi modifica della password all'accesso successivo**. La password non è stata sincronizzata. |
| NoTargetConnection | Nessun oggetto in metaverse o nello spazio connettore di Azure AD |
| SourceConnectorNotPresent | Nessun oggetto trovato nello spazio connettore di Active Directory locale. |
| TargetNotExportedToDirectory | L'oggetto nello spazio connettore di Azure AD non è stato ancora esportato. |
| MigratedCheckDetailsForMoreInfo | La voce di log è stata creata prima della compilazione 1.0.9125.0 e viene visualizzata nello stato precedente. |


## Attivazione di una sincronizzazione completa di tutte le password

In genere, non è necessario forzare una sincronizzazione completa di tutte le password.<br> Tuttavia, se necessario, è possibile attivare una sincronizzazione completa di tutte le password usando lo script seguente:

    $adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
    $aadConnector = "<CASE SENSITIVE AAD CONNECTOR NAME>"
    Import-Module adsync
    $c = Get-ADSyncConnector -Name $adConnector
    $p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter “Microsoft.Synchronize.ForceFullPasswordSync”, String, ConnectorGlobal, $null, $null, $null
    $p.Value = 1
    $c.GlobalParameters.Remove($p.Name)
    $c.GlobalParameters.Add($p)
    $c = Add-ADSyncConnector -Connector $c
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $false
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $true




## Passaggi successivi

* [Servizio di sincronizzazione Azure AD Connect: Personalizzazione delle opzioni di sincronizzazione](active-directory-aadconnectsync-whatis.md)
* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0420_2016-->