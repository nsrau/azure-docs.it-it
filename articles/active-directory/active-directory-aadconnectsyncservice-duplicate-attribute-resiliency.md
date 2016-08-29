<properties
	pageTitle="Sincronizzazione delle identità e resilienza degli attributi duplicati | Microsoft Azure"
	description="Nuovo comportamento relativo alla gestione di oggetti con conflitti di UPN o ProxyAddress durante la sincronizzazione della directory con Azure AD Connect."
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
	ms.date="08/16/2016"
	ms.author="markusvi"/>  



# Sincronizzazione delle identità e resilienza degli attributi duplicati
La resilienza degli attributi duplicati è una funzionalità di Azure Active Directory che consente di eliminare i problemi causati dai conflitti tra **UserPrincipalName** e **ProxyAddress** durante l'esecuzione di uno degli strumenti di sincronizzazione di Microsoft.

In genere questi due attributi devono essere univoci in tutti gli oggetti **User**, **Group** o **Contact** in una directory di Azure Active Directory specificata.

> [AZURE.NOTE] Solo gli oggetti User possono avere UPN.

Il nuovo comportamento abilitato da questa funzionalità si trova nella parte cloud della pipeline di sincronizzazione, quindi è indipendente dal client e rilevante per qualsiasi prodotto di sincronizzazione di Microsoft, tra cui Azure AD Connect, DirSync e i connettori di tipo MIM. In questo documento si usa il termine generico "client di sincronizzazione" per rappresentare uno qualsiasi di questi prodotti.

## Comportamento attuale
Se viene eseguito un tentativo di effettuare il provisioning di un nuovo oggetto con un valore UPN o ProxyAddress che viola il vincolo di univocità, Azure Active Directory blocca la creazione di tale oggetto. Analogamente, se un oggetto viene aggiornato con un valore UPN o ProxyAddress non univoco, l'aggiornamento non riesce. Il tentativo di provisioning o aggiornamento viene ripetuto dal client di sincronizzazione a ogni ciclo di esportazione e continua a non riuscire fino a quando il conflitto non viene risolto. A ogni tentativo viene generato un messaggio di posta elettronica di segnalazione dell'errore e il client di sincronizzazione registra un errore.

## Comportamento con resilienza degli attributi duplicati
Invece di causare un errore di provisioning o di aggiornamento di un oggetto a causa di un attributo duplicato, Azure Active Directory "mette in quarantena" l'attributo duplicato che violerebbe il vincolo di univocità. Se questo attributo è obbligatorio per il provisioning, ad esempio UserPrincipalName, il servizio assegna un valore segnaposto. Il formato di questi valori temporanei è "***<PrefissoOriginale>+<NumeroDi4Cifre>@<DominioTenantIniziale>.onmicrosoft***". Se l'attributo non è obbligatorio, ad esempio **ProxyAddress**, Azure Active Directory mette semplicemente in quarantena l'attributo in conflitto e procede alla creazione o all'aggiornamento dell'oggetto.

Dopo aver messo in quarantena l'attributo, vengono inviate informazioni sul conflitto nello stesso messaggio di posta elettronica di segnalazione dell'errore usato nel comportamento precedente. Queste informazioni vengono però visualizzate nella segnalazione dell'errore una sola volta, al momento dell'inserimento in quarantena, e non continuano a essere registrate nei messaggi di posta elettronica futuri. Poiché l'esportazione di questo oggetto è riuscita, il client di sincronizzazione non registra un errore e non prova a ripetere l'operazione di creazione o aggiornamento nei cicli di sincronizzazione successivi.

Per supportare questo comportamento è stato aggiunto un nuovo attributo alle classi di oggetti User, Group e Contact: **DirSyncProvisioningErrors**

Questo è un attributo multivalore usato per archiviare gli attributi in conflitto che violerebbero il vincolo di univocità se fossero aggiunti normalmente. In Azure Active Directory è stata abilitata un'attività timer in background che viene eseguita ogni ora per la ricerca dei conflitti di attributi duplicati già risolti, che vengono quindi rimossi automaticamente dalla quarantena.

### Abilitazione della resilienza degli attributi duplicati
La funzione può essere abilitata scaricando ed eseguendo la versione più recente del modulo Azure Active Directory PowerShell:

`Set-MsolDirSyncFeature -Feature DuplicateUPNResiliency -Enable $true`  

`Set-MsolDirSyncFeature -Feature DuplicateProxyAddressResiliency -Enable $true`  

Attualmente, la resilienza degli attributi UPN e ProxyAddress può essere abilitata e disabilitata singolarmente. Questo comportamento verrà modificato in futuro ed entrambi gli attributi verranno abilitati per tutte le directory di Azure AD e non potranno essere disabilitati.

## Identificazione di oggetti con DirSyncProvisioningErrors
Attualmente sono disponibili due metodi per identificare gli oggetti che presentano questi errori a causa di conflitti di proprietà duplicati, ovvero Azure Active Directory PowerShell e il portale di amministrazione di Office 365. In futuro è prevista l'estensione ad altri portali in base alle segnalazioni.

### Azure Active Directory PowerShell
Per i cmdlet di PowerShell in questo argomento si applicano le condizioni seguenti:

- Tutti i cmdlet seguenti fanno distinzione tra maiuscole e minuscole.
- È necessario includere sempre **–ErrorCategory PropertyConflict**. Non sono attualmente disponibili altri tipi di **ErrorCategory**, ma potrebbero essere estesi in futuro.

Per iniziare, eseguire prima di tutto **Connect-MsolService** e immettere le credenziali di amministratore tenant.

Usare quindi i cmdlet e gli operatori seguenti per visualizzare gli errori in modi diversi:

1. [Visualizzare tutto](#see-all)

2. [Per tipo di proprietà](#by-property-type)

3. [Per valore in conflitto](#by-conflicting-value)

4. [Tramite una stringa di ricerca](#using-a-string-search)

5. [Ordinati](#sorted)

6. [Una quantità limitata o tutti](#in-a-limited-quantity-or-all)


#### Visualizzare tutto
Una volta connessi, per visualizzare un elenco generale di errori di provisioning degli attributi nel tenant, eseguire:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict`  

Viene generato un risultato simile al seguente: ![Get-MsolDirSyncProvisioningError](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/1.png "Get-MsolDirSyncProvisioningError")


#### Per tipo di proprietà
Per visualizzare gli errori per tipo di proprietà, aggiungere il flag **-PropertyName** con l'argomento **UserPrincipalName** o **ProxyAddresses**:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName UserPrincipalName`  

Oppure

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName ProxyAddresses`

#### Per valore in conflitto
Per visualizzare gli errori relativi a una proprietà specifica, aggiungere il flag **-PropertyValue**. Quando si aggiunge questo flag, è necessario usare anche **-PropertyName**:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyValue User@domain.com -PropertyName UserPrincipalName`  


#### Tramite una stringa di ricerca
Per eseguire una ricerca di stringhe estesa, usare il flag **-SearchString**. Può essere usato separatamente da tutti i flag precedenti, ad eccezione di **-ErrorCategory PropertyConflict** che è sempre obbligatorio:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SearchString User`

#### Ordinati
È possibile usare due flag per ordinare i risultati di una determinata query:

1.	**SortField**: gli argomenti validi includono DisplayName e UserPrincipalName

2.	**SortDirection**: gli argomenti validi includono Ascending e Descending

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SortField UserPrincipalName -SortDirection Ascending`

#### Una quantità limitata o tutti
1. **MaxResults <Int>** può essere usato per limitare la query a un numero di valori specifico.

2. **All** può essere usato per garantire il recupero di tutti i risultati nel caso sia presente un numero di errori elevato.

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -MaxResults 5`  

## Portale di amministrazione di Office 365

È possibile visualizzare gli errori di sincronizzazione della directory nell'interfaccia di amministrazione di Office 365. Il report nel portale di Office 365 mostra solo gli oggetti **User** che contengono questi errori. Non visualizza informazioni sui conflitti tra **Groups**, **Contacts** o **PublicFolders**.


![Utenti attivi](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/1234.png "Utenti attivi")

Per istruzioni su come visualizzare gli errori di sincronizzazione della directory nell'interfaccia di amministrazione di Office 365, vedere [Identificare gli errori di sincronizzazione della directory in Office 365](https://support.office.com/it-IT/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067).


### Segnalazione dell'errore di sincronizzazione delle identità
Quando un oggetto con un conflitto di attributi duplicati viene gestito con questo nuovo comportamento, nel messaggio di posta elettronica standard di segnalazione dell'errore di sincronizzazione delle identità inviato al contatto per le comunicazioni tecniche del tenant viene inclusa una notifica. Questo comportamento include tuttavia una modifica importante. In passato le informazioni su un conflitto di attributi duplicati sarebbe stato incluso in tutte le segnalazioni degli errori successive, fino alla risoluzione del conflitto. Con questo nuovo comportamento la notifica di errore per un determinato conflitto viene visualizzata solo una volta, nel momento in cui l'attributo in conflitto viene messo in quarantena.

Di seguito è riportato un esempio dell'aspetto della notifica di posta elettronica per un conflitto relativo a ProxyAddress: ![Utenti attivi](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/6.png "Utenti attivi")

## Risoluzione dei conflitti
La strategia di risoluzione dei problemi e le tattiche per risolvere questi errori non dovranno essere diverse dal modo in cui venivano gestiti in passato gli errori relativi agli attributi duplicati. L'unica differenza è che l'attività timer scorre il tenant sul lato del servizio per aggiungere automaticamente l'attributo in questione all'oggetto corretto dopo la risoluzione del conflitto.

L'articolo seguente descrive varie strategie di risoluzione dei problemi e degli errori: [Gli attributi duplicati o non validi impediscono la sincronizzazione delle directory in Office 365 ](https://support.microsoft.com/kb/2647098).

## Problemi noti
Nessuno di questi problemi noti causa perdite di dati o la riduzione delle prestazioni del servizio. Alcuni sono di tipo estetico, altri causano la generazione di errori di attributi duplicati di tipo "*pre-resilienza*" standard invece di mettere in quarantena l'attributo in conflitto e altri ancora causano la richiesta di correzioni manuali aggiuntive per determinati errori.

**Comportamento di base:**

1. L'oggetto User con una configurazione di attributi specifica continua a ricevere errori di esportazione invece di inserire gli attributi in quarantena. Ad esempio:

    a. Viene creato un nuovo utente in AD con un UPN **Joe@contoso.com** e ProxyAddress **smtp:Joe@contoso.com**

    b. Le proprietà di questo oggetto sono in conflitto con un gruppo esistente, in cui ProxyAddress è **SMTP:Joe@contoso.com**.

    c. Al momento dell'esportazione viene generato un errore per un **conflitto di ProxyAddress**, invece di mettere in quarantena gli attributi in conflitto. L'operazione viene ritentata durante ogni ciclo di sincronizzazione successivo, come avveniva prima dell'abilitazione della funzionalità di resilienza.

2. L'attività timer che cerca i conflitti di attributi duplicati risolti confronta solo i conflitti di UPN con altri conflitti di UPN. Questo causa il problema illustrato nel passaggio 4 dello scenario seguente:

    a. **UserA@contoso.com** ha un UPN non univoco a causa della presenza di un altro ProxyAddress dell'oggetto con tale valore.

    b. A UserA viene assegnato un **MOERA UPN** temporaneo, **UserA1234@contoso.onmicrosoft.com** e il valore reale dell'UPN viene messo in quarantena (come previsto).

    c. In un secondo momento viene rimosso ProxyAddress dall'altro oggetto in conflitto.

    d. L'UPN di UserA non viene mai corretto automaticamente, ma deve essere aggiornato manualmente.

3. Se in locale vengono creati due gruppi con lo stesso indirizzo SMTP, uno non riesce a effettuare il provisioning al primo tentativo, con un errore duplicato **ProxyAddress** standard. Il valore duplicato viene tuttavia messo correttamente in quarantena al successivo ciclo di sincronizzazione.

**Cmdlet di PowerShell**:

1. **ImmutableId**/**LastDirSyncTime** non vengono visualizzati per la classe di oggetti User.

2. I flag **SortField** e **SortDirection** non influiscono sui risultati.

3. L'uso del flag **PropertyValue** senza aggiungere il flag **PropertyName** genera un errore ambiguo.

4. Il flag **SearchString** restituisce risultati aggiuntivi se eseguito senza i flag **PropertyValue** e **PropertyName**.

**Report del portale di Office**:

1. Il messaggio di errore dettagliato per due oggetti in un set di conflitti UPN è lo stesso. Ciò indica che per entrambi l'UPN è stato modificato o messo in quarantena, mentre in realtà i dati sono stati modificati solo per uno di essi.

2. Il messaggio di errore dettagliato per un conflitto di UPN mostra il valore displayName errato per un utente il cui UPN è stato modificato o messo in quarantena. Ad esempio:

    a. **User A** viene sincronizzato prima con **UPN = User@contoso.com**.

    b. Viene quindi provata la sincronizzazione di **User B** con **UPN = User@contoso.com**.

    c. L'UPN di **User B** viene modificato in **User1234@contoso.onmicrosoft.com** e **User@contoso.com** viene aggiunto a **DirSyncProvisioningErrors**.

    d. Il messaggio di errore per **User B** indicherà che **User A** ha già **User@contoso.com** come UPN, ma visualizza il valore displayName di **User B**.

3. Il report può visualizzare solo informazioni dettagliate sull'errore per gli utenti con conflitti di **UPN**, non per quelli con errori **ProxyAddress** (ancora non è stato stabilito se ciò è coerente o ambientale).

## Vedere anche

- [Servizio di sincronizzazione Azure AD Connect](active-directory-aadconnectsync-whatis.md)

- [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)

- [Identificare gli errori di sincronizzazione della directory in Office 365](https://support.office.com/it-IT/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)

<!---HONumber=AcomDC_0817_2016-->