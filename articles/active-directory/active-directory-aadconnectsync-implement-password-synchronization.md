<properties
	pageTitle="Servizio di sincronizzazione di Azure AD Connect: Implementare la sincronizzazione password | Microsoft Azure"
	description="Informazioni necessarie per comprendere il funzionamento della sincronizzazione password e per abilitarla nell'ambiente corrente."
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
	ms.topic="article"
	ms.date="10/13/2015"
	ms.author="markusvi;andkjell"/>


# Servizio di sincronizzazione Azure AD Connect: Implementare la sincronizzazione della password

Con la sincronizzazione password si consente agli utenti di usare la password con cui accedono all'istanza locale di Active Directory anche per accedere ad Azure Active Directory.

Questo argomento include le informazioni necessarie per comprendere il funzionamento della sincronizzazione password e spiega come abilitarla nell'ambiente corrente.

## Informazioni sulla sincronizzazione password

La sincronizzazione password è una funzionalità del servizio di sincronizzazione Azure Active Directory Connect (Azure AD Connect) che sincronizza le password utente dall'istanza locale di Active Directory ad Azure Active Directory (Azure AD). Questa funzionalità consente agli utenti di accedere ai propri servizi di Azure Active Directory (ad esempio, Office 365, Microsoft Intune e CRM Online) usando la stessa password con cui accedono alla rete locale.

> [AZURE.NOTE]Per maggiori dettagli sui Servizi di dominio Active Directory configurati per FIPS e per la sincronizzazione password, vedere l'articolo sulla mancata sincronizzazione password in sistemi compatibili con FIPS.

## Disponibilità della sincronizzazione password

Tutti i clienti di Azure Active Directory sono idonei a eseguire la sincronizzazione password. Per informazioni sulla compatibilità di questa e di altre funzionalità come l'autenticazione federata, vedere di seguito.

## Funzionamento della sincronizzazione password

La sincronizzazione password è un'estensione della funzionalità di sincronizzazione della directory implementata dal relativo servizio Azure AD Connect. Essa pertanto richiede la configurazione della sincronizzazione della directory tra Active Directory locale e Azure Active Directory.

Servizi di dominio Active Directory archivia le password come valore hash che rappresenta la password utente effettiva. L'hash delle password non può essere usato per accedere alla rete locale. Inoltre, è progettato in modo da non poter essere decrittografato per ottenere l'accesso alla password non crittografata dell'utente. Per sincronizzare una password, il servizio di sincronizzazione Azure AD Connect estrae l'hash della password utente dall'istanza locale di Active Directory. A tale hash viene applicata un'elaborazione di sicurezza aggiuntiva prima della sincronizzazione con il servizio di autenticazione di Azure Active Directory. L'effettivo flusso di dati del processo di sincronizzazione password è simile alla sincronizzazione degli dati utente come il nome visualizzato o gli indirizzi di posta elettronica.

Le password vengono sincronizzate con una frequenza maggiore rispetto alla finestra standard di sincronizzazione della directory per altri attributi. Le password vengono sincronizzate per i singoli utenti e in genere in ordine cronologico. Quando la password di un utente viene sincronizzata da Active Directory locale al cloud, la password esistente nel cloud viene sovrascritta.

La prima volta che si abilita la funzionalità di sincronizzazione password, viene eseguita una sincronizzazione iniziale delle password di tutti gli utenti interni all'ambito tra l'istanza locale di Active Directory e Azure Active Directory. Non è possibile definire esplicitamente il set di utenti le cui password verranno sincronizzate con il cloud. Successivamente, quando un utente locale cambia la password, la funzionalità di sincronizzazione password rileva e sincronizza la password modificata, nella maggior parte dei casi in pochi minuti. La funzionalità effettua inoltre automaticamente nuovi tentativi di sincronizzazione in caso di sincronizzazioni non riuscite. Se si verifica un errore durante un tentativo di sincronizzazione di una password, l'errore viene registrato nel visualizzatore eventi.

La sincronizzazione di una password non incide in alcun modo sugli utenti attualmente connessi. Se anche un utente connesso a un servizio cloud cambia la relativa password locale, la sessione del servizio cloud prosegue senza interruzioni. Tuttavia, non appena il servizio cloud richiede all'utente di ripetere l'autenticazione, è necessario specificare la nuova password. A questo punto, l'utente deve immettere la nuova password, ossia la password recentemente sincronizzata da Active Directory locale al cloud.

## Considerazioni relative alla sicurezza

Quando si sincronizzano le password degli utenti, la versione decrittografata non viene mai esposta alla funzionalità di sincronizzazione, ad Azure Active Directory o a qualsiasi servizio associato.

Inoltre, nell'istanza locale di Active Directory non è prevista l'archiviazione della password in un formato con crittografia reversibile. Per la trasmissione tra Active Directory locale e Azure Active Directory viene usato un digest dell'hash della password di Active Directory. Il digest dell'hash della password non può essere usato per accedere alle risorse nell'ambiente locale del cliente.

## Considerazioni relative ai criteri password

L'abilitazione della sincronizzazione password influisce su due tipi di criteri password:

1. Criteri di complessità delle password
2. Criteri di scadenza delle password

### Criteri di complessità delle password

Quando si abilita la sincronizzazione password, i criteri di complessità delle password configurati nell'istanza locale di Active Directory sostituiscono gli eventuali criteri di complessità definiti nel cloud per gli utenti sincronizzati. Questo significa che è possibile usare qualsiasi password valida nell'ambiente dell'istanza locale di Active Directory del cliente per accedere ai servizi di Azure Active Directory.


> [AZURE.NOTE]Le password degli utenti create direttamente nel cloud restano soggette ai criteri password definiti nel cloud.

### Criteri di scadenza delle password

Se un utente è incluso nell'ambito della sincronizzazione delle password, la password dell'account cloud viene impostata su "*Non scade mai*". Può pertanto accadere che la password di un utente scada nell'ambiente locale, ma che l'utente possa continuare ad accedere ai servizi cloud usando la password scaduta.

La password cloud verrà aggiornata la volta successiva che l'utente cambia la password nell'ambiente locale.


## Sostituzione delle password sincronizzate

Un amministratore può reimpostare manualmente la password di un utente mediante Azure Active Directory PowerShell.

In questo caso, la password sincronizzata dell'utente verrà sostituita dalla nuova password, a cui si applicheranno tutti i criteri password definiti nel cloud.

Se l'utente cambia di nuovo la password locale, la nuova password verrà sincronizzata con il cloud e sostituirà la password aggiornata manualmente.


## Preparazione per la sincronizzazione password

Il tenant di Azure Active Directory deve essere abilitato per la sincronizzazione della directory prima di poter essere abilitato per la sincronizzazione password.


## Abilitazione della sincronizzazione password

È possibile abilitare la sincronizzazione password eseguendo la configurazione guidata di Azure AD Connect.

Nella finestra di dialogo **Funzionalità facoltative** selezionare "**Sincronizzazione password**".

![Funzionalità facoltative][1]


> [AZURE.NOTE]Questo processo attiva una sincronizzazione completa. I cicli di sincronizzazione completa in genere hanno una durata maggiore rispetto agli altri cicli di sincronizzazione.


## Gestione della sincronizzazione password

È possibile monitorare lo stato della sincronizzazione password mediante il registro eventi del computer che esegue Azure AD Connect.


### Determinazione dello stato della sincronizzazione password

È possibile determinare per quali utenti le password sono state sincronizzate correttamente esaminando gli eventi che soddisfano i seguenti criteri:

| Source| ID evento |
| --- | --- |
| Sincronizzazione della directory| 656|
| Sincronizzazione della directory| 657|

Gli eventi con ID 656 forniscono un report delle richieste di modifica password elaborate:

![Event ID 656][2]

Gli eventi corrispondenti con ID 657 forniscono il risultato per queste richieste:

![Event ID 657][3]

Negli eventi gli oggetti interessati sono identificati dal relativo valore anchor e dal valore DN. Il valore anchor corrisponde al valore **ImmutableId** restituito per un utente dal cmdlet Get-MsoUser.

Oltre agli identificatori di oggetto, **Event ID 656** fornisce la data in cui la password dell'utente è stata modificata nell'istanza locale di Active Directory:

![Richiesta di modifica della password][4]

Event ID 657, oltre agli identificatori di oggetto di origine, include un campo Result per indicare lo stato di sincronizzazione dell'oggetto utente in questione.

In un evento Event ID 657 una password sincronizzata correttamente viene indicata dal valore Success per l'attributo Result. Quando un tentativo di sincronizzazione password ha esito negativo, il valore dell'attributo Result è Failure:

![Risultato della modifica della password][5]

### Attivare una sincronizzazione completa di tutte le password
Se è stata modificata la configurazione del filtro, sarà necessario attivare una sincronizzazione completa di tutte le password, per assicurare che le password degli utenti attualmente inclusi nell'ambito siano sincronizzate.

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


## Disabilitazione della sincronizzazione password

È possibile disabilitare la sincronizzazione password eseguendo di nuovo la configurazione guidata di Azure AD Connect. Quando richiesto dalla procedura guidata, deselezionare la casella di controllo "Sincronizzazione password".


> [AZURE.NOTE]Questo processo attiva una sincronizzazione completa. I cicli di sincronizzazione completa in genere hanno una durata maggiore rispetto agli altri cicli di sincronizzazione.

Dopo l'esecuzione della configurazione guidata, il tenant non sincronizzerà più le password. Le nuove modifiche relative alle password non verranno sincronizzate con il cloud. Gli utenti le cui password erano state precedentemente sincronizzate potranno continuare ad accedere con tali password finché non le modificano manualmente nel cloud.



## Risorse aggiuntive

* [Servizio di sincronizzazione Azure AD Connect: Personalizzazione delle opzioni di sincronizzazione](active-directory-aadconnectsync-whatis.md)
* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)

<!--Image references-->
[1]: ./media/active-directory-aadsync-implement-password-synchronization/IC759788.png
[2]: ./media/active-directory-aadsync-implement-password-synchronization/IC662504.png
[3]: ./media/active-directory-aadsync-implement-password-synchronization/IC662505.png
[4]: ./media/active-directory-aadsync-implement-password-synchronization/IC662506.png
[5]: ./media/active-directory-aadsync-implement-password-synchronization/IC662507.png

<!---HONumber=Oct15_HO3-->