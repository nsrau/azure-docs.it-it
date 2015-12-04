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
	ms.date="11/03/2015"
	ms.author="markusvi;andkjell"/>


# Servizio di sincronizzazione Azure AD Connect: Implementare la sincronizzazione password

Con la sincronizzazione password si consente agli utenti di usare la password con cui accedono all'istanza locale di Active Directory anche per accedere ad Azure Active Directory.

Questo argomento include le informazioni necessarie per comprendere il funzionamento della sincronizzazione password e spiega come abilitarla nell'ambiente corrente.

## Informazioni sulla sincronizzazione password

La sincronizzazione password è una funzionalità del servizio di sincronizzazione Azure Active Directory Connect (Azure AD Connect) che sincronizza le password utente dall'istanza locale di Active Directory ad Azure Active Directory (Azure AD). Questa funzionalità consente agli utenti di accedere ai propri servizi di Azure Active Directory (ad esempio Office 365, Microsoft Intune e CRM Online) usando la stessa password con cui accedono alla rete locale.

> [AZURE.NOTE]Per altre informazioni dettagliate sui Servizi di dominio Active Directory configurati per FIPS e per la sincronizzazione password, vedere [Sincronizzazione password e FIPS](#password-synchronization-and-fips)

### Disponibilità della sincronizzazione password

Tutti i clienti di Azure Active Directory sono idonei a eseguire la sincronizzazione password. Per informazioni sulla compatibilità di questa e di altre funzionalità come l'autenticazione federata, vedere di seguito.

### Funzionamento della sincronizzazione password

La sincronizzazione password è un'estensione della funzionalità di sincronizzazione della directory implementata dal relativo servizio Azure AD Connect. Essa pertanto richiede la configurazione della sincronizzazione della directory tra Active Directory locale e Azure Active Directory.

Servizi di dominio Active Directory archivia le password come valore hash che rappresenta la password utente effettiva. L'hash delle password non può essere usato per accedere alla rete locale. Inoltre, è progettato in modo da non poter essere decrittografato per ottenere l'accesso alla password non crittografata dell'utente. Per sincronizzare una password, il servizio di sincronizzazione Azure AD Connect estrae l'hash della password utente dall'istanza locale di Active Directory. A tale hash viene applicata un'elaborazione di sicurezza aggiuntiva prima della sincronizzazione con il servizio di autenticazione di Azure Active Directory. L'effettivo flusso di dati del processo di sincronizzazione password è simile alla sincronizzazione degli dati utente come il nome visualizzato o gli indirizzi di posta elettronica.

Le password vengono sincronizzate con una frequenza maggiore rispetto alla finestra standard di sincronizzazione della directory per altri attributi. Le password vengono sincronizzate per i singoli utenti e in genere in ordine cronologico. Quando la password di un utente viene sincronizzata da Active Directory locale al cloud, la password esistente nel cloud viene sovrascritta.

La prima volta che si abilita la funzionalità di sincronizzazione password, viene eseguita una sincronizzazione iniziale delle password di tutti gli utenti interni all'ambito tra l'istanza locale di Active Directory e Azure Active Directory. Non è possibile definire esplicitamente il set di utenti le cui password verranno sincronizzate con il cloud. Successivamente, quando un utente locale cambia la password, la funzionalità di sincronizzazione password rileva e sincronizza la password modificata, nella maggior parte dei casi in pochi minuti. La funzionalità effettua inoltre automaticamente nuovi tentativi di sincronizzazione in caso di sincronizzazioni non riuscite. Se si verifica un errore durante un tentativo di sincronizzazione di una password, l'errore viene registrato nel visualizzatore eventi.

La sincronizzazione di una password non incide in alcun modo sugli utenti attualmente connessi. Se anche un utente connesso a un servizio cloud cambia la relativa password locale, la sessione del servizio cloud prosegue senza interruzioni. Tuttavia, non appena il servizio cloud richiede all'utente di ripetere l'autenticazione, è necessario specificare la nuova password. A questo punto, l'utente deve immettere la nuova password, ossia la password recentemente sincronizzata da Active Directory locale al cloud.

### Funzionamento della sincronizzazione password con Servizi di dominio Azure AD

Se si abilita questo servizio in Azure AD, è necessaria l'opzione di sincronizzazione password per ottenere un'esperienza Single Sign-On. Con questo servizio, viene modificato il comportamento per la sincronizzazione password e di conseguenza vengono sincronizzati anche gli hash delle password da Active Directory locale a Servizi di dominio Azure AD. La funzionalità è simile a ADMT (Active Directory Migration Tool, Utilità di migrazione ad Active Directory) e consente ai Servizi di dominio Azure AD di autenticare l'utente con tutti i metodi disponibili in Active Directory locale.

### Considerazioni relative alla sicurezza

Quando si sincronizzano le password degli utenti, la versione decrittografata non viene mai esposta alla funzionalità di sincronizzazione, ad Azure Active Directory o a qualsiasi servizio associato.

Inoltre, nell'istanza locale di Active Directory non è prevista l'archiviazione della password in un formato con crittografia reversibile. Per la trasmissione tra Active Directory locale e Azure Active Directory viene usato un digest dell'hash della password di Active Directory. Il digest dell'hash della password non può essere usato per accedere alle risorse nell'ambiente locale del cliente.

### Considerazioni relative ai criteri password

L'abilitazione della sincronizzazione password influisce su due tipi di criteri password:

1. Criteri di complessità delle password
2. Criteri di scadenza delle password

**Criteri di complessità delle password**

Quando si abilita la sincronizzazione password, i criteri di complessità delle password configurati nell'istanza locale di Active Directory sostituiscono gli eventuali criteri di complessità definiti nel cloud per gli utenti sincronizzati. Questo significa che è possibile usare qualsiasi password valida nell'ambiente dell'istanza locale di Active Directory del cliente per accedere ai servizi di Azure Active Directory.

> [AZURE.NOTE]Le password degli utenti create direttamente nel cloud restano soggette ai criteri password definiti nel cloud.

**Criteri di scadenza delle password**

Se un utente è incluso nell'ambito della sincronizzazione delle password, la password dell'account cloud viene impostata su "*Nessuna scadenza*". Può pertanto accadere che la password di un utente scada nell'ambiente locale, ma che l'utente possa continuare ad accedere ai servizi cloud usando la password scaduta.

La password cloud verrà aggiornata la volta successiva che l'utente cambia la password nell'ambiente locale.

### Sostituzione delle password sincronizzate

Un amministratore può reimpostare manualmente la password di un utente mediante Azure Active Directory PowerShell.

In questo caso, la password sincronizzata dell'utente verrà sostituita dalla nuova password, a cui si applicheranno tutti i criteri password definiti nel cloud.

Se l'utente cambia di nuovo la password locale, la nuova password verrà sincronizzata con il cloud e sostituirà la password aggiornata manualmente.

## Preparazione per la sincronizzazione password


### Abilitazione della sincronizzazione password

Se si usano le impostazioni rapide quando si installa Azure AD Connect, la sincronizzazione password viene abilitata per impostazione predefinita.

Se si usano le impostazioni personalizzate quando si installa Azure AD Connect, la sincronizzazione password viene abilitata nella pagina di accesso dell'utente.
![usersignin](./media/active-directory-aadsync-implement-password-synchronization/usersignin.png)

Se si sceglie di usare **la federazione tramite ADFS**, è possibile abilitare la sincronizzazione password come backup in caso di errore dell'infrastruttura di ADFS. È inoltre possibile abilitarla se si prevede di usare Servizi di dominio Azure AD.

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

Per informazioni sulla sicurezza e su FIPS, vedere il post di blog relativo alla [conformità di FIPS, della crittografia e della sincronizzazione password di AAD](http://blogs.technet.com/b/ad/archive/2014/06/28/aad-password-sync-encryption-and-and-fips-compliance.aspx)

## Gestione della sincronizzazione password

### Risoluzione dei problemi di sincronizzazione password

Avviare **Synchronization Service Manager**, aprire **Connettori**, selezionare il connettore di Active Directory in cui si trova l'utente, selezionare **Cerca spazio connettore** e trovare l'utente.

![csuser](./media/active-directory-aadsync-implement-password-synchronization/cspasswordsync.png)

Dopo aver selezionato l'utente, fare clic sulla scheda **derivazione** e assicurarsi che almeno una regola di sincronizzazione mostri **Sincronizzazione password** come **True**. Nella configurazione predefinita, tale regola di sincronizzazione sarà quella denominata **In from AD - User AccountEnabled**.

Per visualizzare i dettagli di sincronizzazione password per l'oggetto, fare clic sul pulsante **Log** nella parte inferiore della pagina. Nella pagina verrà visualizzata la cronologia dello stato di sincronizzazione password dell'utente relativa alla settimana precedente.

![log oggetto](./media/active-directory-aadsync-implement-password-synchronization/csobjectlog.png)

La colonna dello stato può contenere i valori seguenti che indicano il problema e il motivo per cui una password non è stata sincronizzata.

| Stato | Descrizione |
| ---- | ----- |
| Esito positivo | La password è stata sincronizzata. |
| SourceConnectorNotPresent | Nessun oggetto trovato nello spazio connettore di Active Directory locale |
| NoTargetConnection | Nessun oggetto in metaverse o nello spazio connettore di Azure AD |
| TargetNotExportedToDirectory | L'oggetto nello spazio connettore di Azure AD non è stato ancora esportato |


### Attivare una sincronizzazione completa di tutte le password
Non è obbligatorio forzare una sincronizzazione completa di tutte le password, ma se per qualche motivo è necessario eseguire questa procedura, di seguito è riportato il relativo comando PowerShell.

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




## Risorse aggiuntive

* [Servizio di sincronizzazione Azure AD Connect: Personalizzazione delle opzioni di sincronizzazione](active-directory-aadconnectsync-whatis.md)
* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=Nov15_HO4-->