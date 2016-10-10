
<properties
	pageTitle="Uso di attributi per la creazione di regole avanzate | Microsoft Azure"
	description="Procedura per creare regole avanzate per un gruppo inclusi gli operatori delle regole di espressione e i parametri."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="femila"
	editor=""/>  

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/15/2016"
	ms.author="curtand"/>


# Uso di attributi per la creazione di regole avanzate

Il portale di Azure classico offre la possibilità di creare regole avanzate per consentire appartenenze dinamiche basate su attributi più complesse ai gruppi di Azure Active Directory (Azure AD).

Quando gli attributi di un utente cambiano, il sistema valuta tutte le regole dinamiche del gruppo in una directory per verificare se la modifica degli attributi dell'utente attiverà aggiunte o rimozioni nel gruppo. Se un utente soddisfa una regola in un gruppo, viene aggiunto come membro a tale gruppo. Se non soddisfa più la regola di un gruppo di cui è membro, viene rimosso come membro da tale gruppo.

## Per creare la regola avanzata

1. Nel [portale di Azure classico](https://manage.windowsazure.com) selezionare **Active Directory** e aprire la directory dell'organizzazione.

2. Selezionare la scheda **Gruppi** e aprire il gruppo da modificare.

3. Selezionare la scheda **Configura**, selezionare l'opzione **Regola avanzata** e immettere la regola avanzata nella casella di testo.

## Creazione del corpo di una regola avanzata

La regola avanzata che è possibile creare per le appartenenze dinamiche ai gruppi è essenzialmente un'espressione binaria composta da tre parti che genera un risultato di tipo true o false. Di seguito sono elencate le tre parti:

- Parametro sinistro
- Operatore binario
- Costante destra

Una regola avanzata completa simile alla seguente: (parametroSinistro operatoreBinario "CostanteDestra"), dove le parentesi di apertura e chiusura sono obbligatorie per racchiudere l'intera espressione binaria, le virgolette doppie sono obbligatorie per la costante a destra e la sintassi del parametro a sinistra è utente.proprietà. Una regola avanzata può includere più espressioni binarie separate dagli operatori logici -and, -or e -not. Di seguito sono riportati alcuni esempi di regola avanzata con il formato corretto:

- (user.department -eq "Vendite") -or (user.department -eq "Marketing")
- (user.department -eq "Vendite") -and -not (user.jobTitle -contains "SDE")

Per l'elenco completo dei parametri supportati e degli operatori delle regole di espressione, vedere le sezioni riportate di seguito.

La lunghezza totale del corpo della regola avanzata non può superare i 2048 caratteri.

> [AZURE.NOTE]
Le operazioni di stringa ed espressione regolare non fanno distinzione tra maiuscole e minuscole. È inoltre possibile eseguire controlli Null usando $null come costante, ad esempio user.department -eq $null. Le stringhe contenenti le virgolette " devono essere precedute dal carattere di escape ', ad esempio user.department -eq `"Sales".

## Operatori delle regole di espressione supportati
Nella tabella seguente sono elencati tutti gli operatori delle regole di espressione supportati e la relativa sintassi da usare nel corpo della regola avanzata:

| Operatore | Sintassi |
|-----------------|----------------|
| Non uguale a | -ne |
| Uguale a | -eq |
| Non inizia con | -notStartsWith |
| Inizia con | -startsWith |
| Non contiene | -notContains |
| Contiene | -contains |
| Non corrispondente | -notMatch |
| Corrispondente | -match |


## Correzione degli errori di query
Nella tabella seguente sono elencati errori potenziali e indica come correggerli se si verificano

| Errore di analisi della query | Uso errato | Uso corretto |
|-----------------------|-------------------|-----------------------------|
| Errore: l'attributo non è supportato | (user.invalidProperty -eq "Valore") | (user.department -eq "valore")<br/>La proprietà deve corrispondere a una delle proprietà nell'[elenco di proprietà supportate](#supported-properties). |
| Errore: l'operatore non è supportato sull'attributo. | (user.accountEnabled -contains true) | (user.accountEnabled -eq true)<br/>La proprietà è di tipo booleano. Usare gli operatori supportati (-eq o - ne) per il tipo boolean nell'elenco precedente. |
| Errore: si è verificato un errore di compilazione della query. | (user.department -eq "Vendite") -and (user.department -eq "Marketing")(user.userPrincipalName -match "*@domain.ext") | (user.department -eq "Vendite") -and (user.department -eq "Marketing")<br/>L'operatore logico deve corrispondere a un operatore incluso nel precedente elenco di proprietà supportate. (user.userPrincipalName -match ".*@domain.ext")or(user.userPrincipalName -match "@domain.ext$") Errore nell'espressione regolare. |
| Errore: l'espressione binaria non ha il formato corretto | (user.department –eq "Vendite") (user.department -eq "Vendite")(user.department-eq"Vendite") | (user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")<br/>La query include più errori. La parentesi non si trova nella posizione corretta. |
| Errore: si è verificato un errore sconosciuto durante la configurazione delle appartenenze dinamiche. | (user.accountEnabled -eq "True" AND user.userPrincipalName -contains "alias@domain") | (user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")<br/>La query include più errori. La parentesi non si trova nella posizione corretta. |

## Proprietà supportate
Di seguito sono elencate tutte le proprietà utente che è possibile usare nelle regole avanzate:

### Proprietà di tipo boolean

Operatori consentiti

* -eq


* -ne


| Proprietà | Valori consentiti | Utilizzo |
|----------------|-----------------|--------------------------------|
| accountEnabled | true false | user.accountEnabled -eq true) |
| dirSyncEnabled | true false null | (user.dirSyncEnabled -eq true) |

### Proprietà di tipo stringa

Operatori consentiti

* -eq


* -ne


* -notStartsWith


* -StartsWith


* -contains


* -notContains


* -match


* -notMatch

| Proprietà | Valori consentiti | Uso |
|----------------------------|-------------------------------------------------------------------------------------------------------|-----------------------------------------------------------|
| city | Qualsiasi valore stringa o $null | (user.city -eq "valore") |
| country | Qualsiasi valore stringa o $null | (user.country -eq "valore") |
| department | Qualsiasi valore stringa o $null | (user.department -eq "valore") |
| displayName | Qualsiasi valore stringa. | (user.displayName -eq "valore") |
| facsimileTelephoneNumber | Qualsiasi valore stringa o $null | (user.facsimileTelephoneNumber -eq "valore") |
| givenName | Qualsiasi valore stringa o $null | (user.givenName -eq "valore") |
| jobTitle | Qualsiasi valore stringa o $null | (user.jobTitle -eq "valore") |
| mail | Qualsiasi valore stringa o $null (indirizzo SMTP dell'utente) | (user.mail -eq "valore") |
| mailNickName | Qualsiasi valore stringa (alias di posta dell'utente) | (user.mailNickName -eq "valore") |
| mobile | Qualsiasi valore stringa o $null | (user.mobile -eq "valore") |
| objectId | GUID dell'oggetto utente | (user.objectId -eq "1111111-1111-1111-1111-111111111111") |
| passwordPolicies | Nessuno DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword | (user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName | Qualsiasi valore stringa o $null | (user.physicalDeliveryOfficeName -eq "valore") |
| postalCode | Qualsiasi valore stringa o $null | (user.postalCode -eq "valore") |
| preferredLanguage | Codice ISO 639-1 | (user.preferredLanguage -eq "it-IT") |
| sipProxyAddress | Qualsiasi valore stringa o $null | (user.sipProxyAddress -eq "valore") |
| state | Qualsiasi valore stringa o $null | (user.state -eq "valore") |
| streetAddress | Qualsiasi valore stringa o $null | (user.streetAddress -eq "valore") |
| surname | Qualsiasi valore stringa o $null | (user.surname -eq "valore") |
| telephoneNumber | Qualsiasi valore stringa o $null | (user.telephoneNumber -eq "valore") |
| usageLocation | Codice di paese di due lettere | (user.usageLocation -eq "US") |
| userPrincipalName | Qualsiasi valore stringa. | (user.userPrincipalName -eq "alias@domain") |
| userType | membro guest $null | (user.userType -eq "Membro") |

### Proprietà di tipo insieme String

Operatori consentiti

* -contains


* -notContains

| Proprietà | Valori consentiti | Uso |
|----------------|---------------------------------------|------------------------------------------------------|
| otherMails | Qualsiasi valore stringa. | (user.otherMails -contains "alias@domain") |
| proxyAddresses | SMTP: alias@domain smtp: alias@domain | (user.proxyAddresses -contains "SMTP: alias@domain") |

## Attributi di estensione ed attributi personalizzati
Gli attributi di estensione e gli attributi personalizzati sono supportati nelle regole di appartenenza dinamica.

Gli attributi di estensione vengono sincronizzati dall'istanza locale di Window Server AD e hanno il formato "ExtensionAttributeX", dove X equivale a 1 - 15. Ecco un esempio di regola che usa un attributo di estensione:

(user.extensionAttribute15 -eq "Marketing")

Gli attributi personalizzati vengono sincronizzati dall'istanza locale di Windows Server AD o da un'applicazione SaaS collegata e hanno il formato "user.extension_[GUID]\__[Attributo]", dove [GUID] è l'identificatore univoco in AAD per l'applicazione che ha creato l'attributo in AAD e [Attributo] è il nome dell'attributo creato. Ecco un esempio di regola che usa un attributo personalizzato:

user.extension\_c272a57b722d4eb29bfe327874ae79cb\_\_OfficeNumber

È possibile trovare il nome dell'attributo personalizzato nella directory eseguendo una query sull'attributo nell'utente con Esplora grafico e cercando il nome dell'attributo.

## Regola per i dipendenti diretti
Ora è possibile popolare i membri di un gruppo in base all'attributo di manager di un utente.

**Per configurare un gruppo come gruppo "Manager"**

1. Nel portale di Azure classico, fare clic su **Active Directory** e poi sul nome della directory dell'organizzazione.

2. Selezionare la scheda **Gruppi** e aprire il gruppo da modificare.

3. Selezionare la scheda **Configura** e quindi selezionare **REGOLA AVANZATA**.

4. Digitare la regola con la sintassi seguente:

	Dipendenti diretti per *Dipendenti diretti per {obectID\_of\_manager}*. Ecco un esempio di regola valida per dipendenti diretti:

					Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863”

	dove "62e19b97-8b3d-4d4a-a106-4ce66896a863" è il parametro objectID del manager. L'ID oggetto è disponibile in Azure AD nella **scheda Profilo** della pagina utente dell'utente che rappresenta il manager.

3. Quando si salva questa regola, tutti gli utenti che soddisfano la regola verranno aggiunta come membri del gruppo. Possono essere necessari alcuni minuti per il popolamento iniziale del gruppo.


## Uso degli attributi per creare regole per gli oggetti dispositivo

È anche possibile creare una regola che consenta di selezionare gli oggetti dispositivo per l'appartenenza a un gruppo. È possibile usare gli attributi del dispositivo seguenti:

| Proprietà | Valori consentiti | Uso |
|-------------------------|---------------------------------|-------------------------------------------------------------|
| displayName | Qualsiasi valore stringa | (device.displayName - eq "Iphone di Rob") |
| deviceOSType | Qualsiasi valore stringa | (device.deviceOSType -eq "IOS") |
| deviceOSVersion | Qualsiasi valore stringa | (device.OSVersion -eq "9.1") |
| isDirSynced | true false null | (device.isDirSynced -eq "true") |
| isManaged | true false null | (device.isManaged -eq "false") |
| isCompliant | true false null | (device.isCompliant -eq "true") |
| deviceCategory | Qualsiasi valore stringa | (device.deviceCategory -eq "") |
| deviceManufacturer | Qualsiasi valore stringa | (device.deviceManufacturer -eq "Microsoft") |
| deviceModel | Qualsiasi valore stringa | (device.deviceModel -eq "IPhone 7+") |
| deviceOwnership | Qualsiasi valore stringa | (device.deviceOwnership -eq "") |
| domainName | Qualsiasi valore stringa | (device.domainName -eq "contoso.com") |
| enrollmentProfileName | Qualsiasi valore stringa | (device.enrollmentProfileName -eq "") |
| enrollmentType | Qualsiasi valore stringa | (device.enrollmentType -eq "") |
| isRooted | true false null | (device.deviceOSType -eq "true") |
| managementType | Qualsiasi valore stringa | (device.managementType -eq "") |
| organizationalUnit | Qualsiasi valore stringa | (device.organizationalUnit -eq "") |
| deviceId | un deviceId valido | (device.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d" |

> [AZURE.NOTE]
Impossibile creare le regole di dispositivo usando l'elenco a discesa "regola semplice" nel portale di Azure classico.


## Informazioni aggiuntive
Questi articoli forniscono informazioni aggiuntive su Azure Active Directory.

* [Risoluzione dei problemi di appartenenza dinamica per i gruppi](active-directory-accessmanagement-troubleshooting.md)

* [Gestione dell'accesso alle risorse tramite i gruppi di Azure Active Directory](active-directory-manage-groups.md)

* [Azure Active Directory cmdlets for configuring group settings (Cmdlet di Azure Active Directory per la configurazione delle impostazioni di gruppo)](active-directory-accessmanagement-groups-settings-cmdlets.md)

* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)

* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0928_2016-->