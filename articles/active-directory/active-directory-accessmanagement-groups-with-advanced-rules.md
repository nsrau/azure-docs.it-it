
<properties
	pageTitle="Uso di attributi per la creazione di regole avanzate | Microsoft Azure"
	description="Procedura per creare regole avanzate per un gruppo inclusi gli operatori delle regole di espressione e i parametri."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/17/2015"
	ms.author="curtand"/>


# Uso di attributi per la creazione di regole avanzate
Il portale di Azure offre la flessibilità necessaria per configurare regole avanzate in Azure Active Directory (Azure AD) per consentire appartenenze dinamiche più complesse ai gruppi di Azure AD.

**Per creare la regola avanzata** Nel portale di Azure, nella scheda **Configura** del gruppo selezionare il pulsante di opzione **Regola avanzata** e quindi digitare la regola avanzata nella casella di testo disponibile. È possibile creare le regole avanzate usando le informazioni seguenti.

## Creazione del corpo di una regola avanzata
La regola avanzata che è possibile creare per le appartenenze dinamiche ai gruppi è essenzialmente un'espressione binaria composta da tre parti che genera un risultato di tipo true o false. Di seguito sono elencate le tre parti:

- Parametro sinistro
- Operatore binario
- Costante destra

Una regola avanzata completa simile alla seguente: (parametroSinistro operatoreBinario "CostanteDestra"), dove le parentesi di apertura e chiusura sono obbligatorie per racchiudere l'intera espressione binaria, le virgolette doppie sono obbligatorie per la costante a destra e la sintassi del parametro a sinistra è utente.proprietà. Una regola avanzata può includere più espressioni binarie separate dagli operatori logici -and, -or e -not. Di seguito sono riportati alcuni esempi di regola avanzata con il formato corretto:

- (user.department -eq "Vendite") -or (user.department -eq "Marketing")
- (user.department -eq "Vendite") -and -not (user.jobTitle -contains "SDE")

Per l'elenco completo dei parametri supportati e degli operatori delle regole di espressione, vedere le sezioni riportate di seguito.

La lunghezza totale del corpo della regola avanzata non può superare i 255 caratteri.
> [AZURE.NOTE]Le operazioni di stringa ed espressione regolare non fanno distinzione tra maiuscole e minuscole. È inoltre possibile eseguire controlli Null usando $null come costante, ad esempio user.department -eq $null. Le stringhe contenenti le virgolette " devono essere precedute dal carattere di escape ', ad esempio user.department -eq "Sa`"les".

##Operatori delle regole di espressione supportati
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


| Errore di analisi della query | Uso errato | Uso corretto |
|----------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Errore: l'attributo non è supportato | (user.invalidProperty -eq "Valore") | (user.department -eq "valore") La proprietà deve corrispondere a una delle proprietà nell'elenco di proprietà supportate. |
| Errore: l'operatore non è supportato sull'attributo. | (user.accountEnabled -contains true) | (user.accountEnabled -eq true) La proprietà è di tipo booleano. Usare gli operatori supportati (-eq o - ne) per il tipo boolean nell'elenco precedente. |
| Errore: si è verificato un errore di compilazione della query. | (user.department -eq "Vendite") -and (user.department -eq "Marketing")(user.userPrincipalName -match "*@domain.ext") | (user.department -eq "Vendite") -and (user.department -eq "Marketing") L'operatore logico deve corrispondere a un operatore incluso nel precedente elenco di proprietà supportate. (user.userPrincipalName -match ".*@domain.ext") o (user.userPrincipalName -match "@domain.ext$") Errore nell'espressione regolare. |
| Errore: l'espressione binaria non ha il formato corretto | (user.department –eq "Vendite") (user.department -eq "Vendite")(user.department-eq"Vendite") | (user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain") La query include più errori. La parentesi non si trova nella posizione corretta. |
| Errore: si è verificato un errore sconosciuto durante la configurazione delle appartenenze dinamiche. | (user.accountEnabled -eq "True" AND user.userPrincipalName -contains "alias@domain") | (user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain") La query include più errori. La parentesi non si trova nella posizione corretta. |

##Parametri supportati
Di seguito sono elencate tutte le proprietà utente che è possibile usare nelle regole avanzate:

**Proprietà di tipo boolean**

Operatori consentiti

* -eq


* -ne


| Proprietà | Valori consentiti | Utilizzo |
|----------------|-----------------|--------------------------------|
| accountEnabled | true false | user.accountEnabled -eq true) |
| dirSyncEnabled | true false null | (user.dirSyncEnabled -eq true) |

**Proprietà di tipo stringa**

Operatori consentiti

* -eq


* -ne


* -notStartsWith


* -StartsWith


* -contains


* -notContains


* -match


* -notMatch

| Proprietà | Valori consentiti | Utilizzo |
|----------------------------|-------------------------------------------------------------------------------------------------------|-----------------------------------------------------------|
| city | Qualsiasi valore stringa o $null. | (user.city -eq "valore") |
| country | Qualsiasi valore stringa o $null. | (user.country -eq "valore") |
| department | Qualsiasi valore stringa o $null. | (user.department -eq "valore") |
| displayName | Qualsiasi valore stringa. | (user.displayName -eq "valore") |
| facsimileTelephoneNumber | Qualsiasi valore stringa o $null. | (user.facsimileTelephoneNumber -eq "valore") |
| givenName | Qualsiasi valore stringa o $null. | (user.givenName -eq "valore") |
| jobTitle | Qualsiasi valore stringa o $null. | (user.jobTitle -eq "valore") |
| mail | Qualsiasi valore stringa o $null. Indirizzo SMTP dell'utente. | (user.mail -eq "valore") |
| mailNickName | Qualsiasi valore stringa. Alias di posta dell'utente. | (user.mailNickName -eq "valore") |
| mobile | Qualsiasi valore stringa o $null. | (user.mobile -eq "valore") |
| objectId | GUID dell'oggetto utente | (user.objectId -eq "1111111-1111-1111-1111-111111111111") |
| passwordPolicies | Nessuno DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword | (user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName | Qualsiasi valore stringa o $null. | (user.physicalDeliveryOfficeName -eq "valore") |
| postalCode | Qualsiasi valore stringa o $null. | (user.postalCode -eq "valore") |
| preferredLanguage | Codice ISO 639-1 | (user.preferredLanguage -eq "it-IT") |
| sipProxyAddress | Qualsiasi valore stringa o $null. | (user.sipProxyAddress -eq "valore") |
| state | Qualsiasi valore stringa o $null. | (user.state -eq "valore") |
| streetAddress | Qualsiasi valore stringa o $null. | (user.streetAddress -eq "valore") |
| surname | Qualsiasi valore stringa o $null. | (user.surname -eq "valore") |
| telephoneNumber | Qualsiasi valore stringa o $null. | (user.telephoneNumber -eq "valore") |
| usageLocation | Codice di paese di due lettere | (user.usageLocation -eq "US") |
| userPrincipalName | Qualsiasi valore stringa. | (user.userPrincipalName -eq "alias@domain") |
| userType | membro guest $null | (user.userType -eq "Membro") |

**Proprietà di tipo insieme String**

Operatori consentiti

* -contains


* -notContains

| Proprietà | Valori consentiti | Uso |
|----------------|---------------------------------------|------------------------------------------------------|
| otherMails | Qualsiasi valore stringa. | (user.otherMails -contains "alias@domain") |
| proxyAddresses | SMTP: alias@domain smtp: alias@domain | (user.proxyAddresses -contains "SMTP: alias@domain") |

## Regola per i dipendenti diretti
Ora è possibile popolare i membri di un gruppo in base all'attributo di manager di un utente.
Per configurare un gruppo come gruppo "Manager"
--------------------------------------------------------------------------------
1. Nel portale dell'amministratore fare clic sulla scheda **Configura** e quindi selezionare **REGOLA AVANZATA**.
2. Digitare la regola con la sintassi seguente: Dipendenti diretti per *Dipendenti diretti per {IDutente\_di\_manager}*
3. Quando si salva questa regola, tutti gli utenti che soddisfano la regola verranno aggiunta come membri del gruppo. Si noti che possono essere necessari alcuni minuti per il popolamento iniziale del gruppo.


## Informazioni aggiuntive
Questi articoli forniscono informazioni aggiuntive su Azure Active Directory.

* [Risoluzione dei problemi di appartenenza dinamica per i gruppi](active-directory-accessmanagement-troubleshooting.md)

* [Gestione dell'accesso alle risorse tramite i gruppi di Azure Active Directory](active-directory-manage-groups.md)

* [Informazioni su Azure Active Directory](active-directory-whatis.md)

* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=Nov15_HO4-->