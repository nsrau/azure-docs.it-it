
<properties
	pageTitle="Utilizzo degli attributi per creare regole avanzate per l'appartenenza al gruppo in anteprima di Azure Active Directory | Microsoft Azure"
	description="Procedura per creare regole avanzate per l'appartenenza dinamica ai gruppi, inclusi i parametri e gli operatori supportati per le regole delle espressioni."
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
	ms.date="09/12/2016"
	ms.author="curtand"/>


# Utilizzo degli attributi per creare regole avanzate per l'appartenenza al gruppo in anteprima di Azure Active Directory

Il portale di Azure offre la possibilità di creare regole avanzate per consentire appartenenze dinamiche basate su attributi più complesse per i gruppi in anteprima di Azure Active Directory (Azure AD). [Funzionalità disponibili nell'anteprima](active-directory-preview-explainer.md) Questo articolo descrive in dettaglio la sintassi e gli attributi delle regole per creare queste regole avanzate.

## Per creare la regola avanzata

1.  Accedere al [portale di Azure](https://portal.azure.com) con un account di amministratore globale per la directory.

2.  Selezionare **Altri servizi**, immettere **Utenti e gruppi** nella casella di testo e quindi selezionare **Invio**.

  ![Apertura di Gestione utenti](./media/active-directory-groups-dynamic-membership-azure-portal/search-user-management.png)

3.  Nel pannello **Utenti e gruppi** selezionare **Tutti i gruppi**.

  ![Apertura del pannello Gruppi](./media/active-directory-groups-dynamic-membership-azure-portal/view-groups-blade.png)

4. Nel pannello **Utenti e gruppi - Tutti i gruppi** selezionare il comando **Aggiungi**.

  ![Aggiungere un nuovo gruppo](./media/active-directory-groups-dynamic-membership-azure-portal/add-group-type.png)

5. Nel pannello **Gruppo** immettere un nome e una descrizione per il nuovo gruppo. Selezionare un **Tipo di appartenenza** di **Dynamic User** (Utente dinamico) o **Dynamic Device** (Dispositivo dinamico), a seconda che si intenda creare una regola per gli utenti o per i dispositivi e quindi selezionare **Add dynamic query** (Aggiungi query dinamica). Per gli attributi usati per le regole di dispositivo, vedere [Uso degli attributi per creare regole per gli oggetti dispositivo](#using-attributes-to-create-rules-for-device-objects).

  ![Aggiungere una regola di appartenenza dinamica](./media/active-directory-groups-dynamic-membership-azure-portal/add-dynamic-group-rule.png)

6. Nel pannello **Dynamic membership rules** (Regole di appartenenza dinamiche) immettere la regola nella casella **Add dynamic membership advanced rule** (Aggiungi regola avanzata di appartenenza dinamica), premere Invio e quindi selezionare **Crea** nella parte inferiore del pannello.

7. Selezionare **Crea** nel pannello **Gruppo** per creare il gruppo.

## Creazione del corpo di una regola avanzata

La regola avanzata che è possibile creare per le appartenenze dinamiche ai gruppi è essenzialmente un'espressione binaria composta da tre parti che genera un risultato di tipo true o false. Di seguito sono elencate le tre parti:

- Parametro sinistro
- Operatore binario
- Costante destra

Una regola avanzata completa simile alla seguente: (parametroSinistro operatoreBinario "CostanteDestra"), dove le parentesi di apertura e chiusura sono obbligatorie per racchiudere l'intera espressione binaria, le virgolette doppie sono obbligatorie per la costante a destra e la sintassi del parametro a sinistra è utente.proprietà. Una regola avanzata può includere più espressioni binarie separate dagli operatori logici -and, -or e -not.

Di seguito sono riportati alcuni esempi di regola avanzata con il formato corretto:

- (user.department -eq "Vendite") -or (user.department -eq "Marketing")
- (user.department -eq "Vendite") -and -not (user.jobTitle -contains "SDE")

Per l'elenco completo dei parametri supportati e degli operatori delle regole di espressione, vedere le sezioni riportate di seguito. Per gli attributi usati per le regole di dispositivo, vedere [Uso degli attributi per creare regole per gli oggetti dispositivo](#using-attributes-to-create-rules-for-device-objects).

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

1. Eseguire i passaggi da 1 a 5 in [Per creare la regola avanzata](#to-create-the-advanced-rule) e selezionare **Dynamic User** (Utente dinamico) come **Tipo di appartenenza**.

2. Nel pannello **Dynamic membership rules** (Regole di appartenenza dinamica) immettere la regola con la sintassi seguente:

	Dipendenti diretti per *Dipendenti diretti per {obectID\_of\_manager}*. Ecco un esempio di regola valida per dipendenti diretti:

					Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863”

	dove "62e19b97-8b3d-4d4a-a106-4ce66896a863" è il parametro objectID del manager. L'ID oggetto è disponibile in Azure AD nella **scheda Profilo** della pagina utente dell'utente che rappresenta il manager.

3. Quando si salva questa regola, tutti gli utenti che soddisfano la regola verranno aggiunta come membri del gruppo. Possono essere necessari alcuni minuti per il popolamento iniziale del gruppo.


## Uso degli attributi per creare regole per gli oggetti dispositivo

È anche possibile creare una regola che consenta di selezionare gli oggetti dispositivo per l'appartenenza a un gruppo. È possibile usare gli attributi del dispositivo seguenti:

| Proprietà | Valori consentiti | Uso |
|----------------------|---------------------------------|------------------------------------------------------|
| displayName | Qualsiasi valore stringa | (device.displayName - eq "Iphone di Rob") |
| deviceOSType | Qualsiasi valore stringa | (device.deviceOSType -eq "IOS") |
| deviceOSVersion | Qualsiasi valore stringa | (device.OSVersion -eq "9.1") |
| isDirSynced | true false null | (device.isDirSynced -eq "true") |
| isManaged | true false null | (device.isManaged -eq "false") |
| isCompliant | true false null | (device.isCompliant -eq "true") |


## Informazioni aggiuntive
Questi articoli forniscono informazioni aggiuntive sui gruppi in Azure Active Directory.

* [Vedere i gruppi esistenti](active-directory-groups-view-azure-portal.md)
* [Creare un nuovo gruppo e aggiunta di membri](active-directory-groups-create-azure-portal.md)
* [Gestire le impostazioni di un gruppo](active-directory-groups-settings-azure-portal.md)
* [Gestire le appartenenze di un gruppo](active-directory-groups-membership-azure-portal.md)
* [Gestire le regole dinamiche per gli utenti in un gruppo](active-directory-groups-dynamic-membership-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->