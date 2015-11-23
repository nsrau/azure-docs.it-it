<properties
	pageTitle="Servizio di sincronizzazione Azure AD Connect: Informazioni sulle espressioni di provisioning dichiarativo | Microsoft Azure"
	description="Informazioni sulle espressioni di provisioning dichiarativo."
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
	ms.date="11/10/2015"
	ms.author="markusvi;andkjell"/>


# Servizio di sincronizzazione Azure AD Connect: Informazioni sulle espressioni di provisioning dichiarativo

Il servizio di sincronizzazione Azure AD Connect si basa sul provisioning dichiarativo introdotto per la prima volta in Forefront Identity Manager 2010 per permettere di implementare la logica di business di integrazione completa delle identità senza dovere scrivere codice.

Una parte essenziale del provisioning dichiarativo è il linguaggio delle espressioni usato nei flussi di attributi. Il linguaggio usato è un subset di Microsoft® Visual Basic®, Applications Edition (VBA). Questo linguaggio viene usato in Microsoft Office e verrà riconosciuto anche dagli utenti con esperienza in VBScript. Il linguaggio delle espressioni di provisioning dichiarativo usa solo funzioni e non è un linguaggio strutturato, né include metodi o istruzioni. Le funzioni verranno invece annidate per esprimere flussi di programmi.

Per altre informazioni dettagliate, vedere i [riferimenti per il linguaggio Visual Basic, Applications Edition per Office 2013](https://msdn.microsoft.com/library/gg264383.aspx).

Gli attributi sono fortemente tipizzati. Una funzione che prevede di ricevere un attributo String a valore singolo non accetterà attributi multivalore o attributi di tipo diverso. È applicata anche la distinzione maiuscole/minuscole. Se per i nomi delle funzioni e i nomi degli attributi non viene rispettata correttamente la distinzione lettere maiuscole/minuscole, verrà generato un errore.

## Definizioni e identificatori del linguaggio

- I nomi delle funzioni sono seguiti dagli argomenti racchiusi tra parentesi: FunctionName(argomento 1,argomento N).
- Gli attributi sono identificati da parentesi quadre: [attributeName].
- I parametri sono identificati dal segno di percentuale: %ParameterName%
- Le costanti di stringa sono racchiuse tra virgolette: ad esempio "Contoso" (Nota: è necessario usare le virgolette semplici "" e non quelle inglesi “”)
- I valori numerici sono espressi senza virgolette e devono essere decimali. Ai valori esadecimali viene aggiunto un prefisso di tipo &H. Ad esempio, 98052, &HFF
- I valori booleani sono espressi con costanti: True, False.
- Le costanti predefinite sono espresse solo tramite il relativo nome: NULL, CRLF, IgnoreThisFlow

### Functions
Il provisioning dichiarativo usa molte funzioni per abilitare la possibilità di trasformare i valori di attributo. Tali valori possono essere nidificati in modo che il risultato di una funzione venga passato a un'altra funzione.

Le funzioni possono anche agire su un attributo multivalore. In questo caso la funzione agirà su ogni singolo valore e applicherà la stessa funzione a ogni valore. Ad esempio `Trim([proxyAddresses])` esegue un trimming di ogni valore nell'attributo proxyAddress.

L'elenco completo delle funzioni è disponibile nel [riferimento di funzione](active-directory-aadconnectsync-functions-reference.md).

### Parametri

Un parametro è definito da un connettore o da un amministratore tramite PowerShell. I parametri includeranno in genere valori diversi nei sistemi specifici, ad esempio il nome del dominio in cui si trova l'utente. Possono essere usati nei flussi di attributi.

Active Directory Connector fornisce i parametri seguenti per le regole di sincronizzazione in entrata:

| Nome parametro | Commento |
| --- | --- |
| Domain.Netbios | Formato Netbios del dominio attualmente importato, ad esempio FABRIKAMSALES |
| Domain.FQDN | Formato FQDN del dominio attualmente importato, ad esempio sales.fabrikam.com |
| Domain.LDAP | Formato LDAP del dominio attualmente importato, ad esempio DC=sales,DC=fabrikam,DC=com |
| Forest.Netbios | Formato Netbios del nome di foresta attualmente importato, ad esempio FABRIKAMCORP |
| Forest.FQDN | Formato FQDN del nome di foresta attualmente importato, ad esempio fabrikam.com |
| Forest.LDAP | Formato LDAP del nome di foresta attualmente importato, ad esempio DC=fabrikam,DC=com |

Il sistema fornisce il seguente parametro, usato per ottenere l'identificatore del connettore attualmente in esecuzione:

`Connector.ID`

L'esempio seguente popolerà il dominio dell'attributo metaverse con il nome netbios del dominio in cui si trova l'utente:

`domain <- %Domain.Netbios%`

### Operatori

È possibile usare gli operatori seguenti:

- **Confronto**: <, <=, <>, =, >, >=
- **Matematici**: +, -, *, -
- **Stringa**: & (concatenazione)
- **Logici**: && (AND), || (OR)
- **Ordine di valutazione**: ( )

Gli operatori vengono valutati da sinistra a destra e hanno la stessa priorità di valutazione. Ad esempio, * (moltiplicazione) non viene valutato prima di - (sottrazione). L'operazione 2*(5+3) è diversa da 2*5+3. Le parentesi ( ) vengono usate per modificare l'ordine di valutazione quando un ordine da sinistra a destra non è appropriato.

## Scenari comuni

### Lunghezza degli attributi

Per impostazione predefinita, gli attributi di stringa sono configurati come indicizzabili e la lunghezza massima è di 448 caratteri. Se si usano attributi di stringa che potrebbero avere una lunghezza maggiore, assicurarsi di includere il codice seguente nel flusso di attributi:

`attributeName <- Left([attributeName],448)`

### Modifica di userPrincipalSuffix

L'attributo userPrincipalName in Active Directory non è sempre noto agli utenti e potrebbe non essere adatto come ID di accesso. L'installazione guidata del servizio di sincronizzazione Azure AD Connect consente di scegliere un attributo diverso, ad esempio mail. In alcuni casi è però necessario calcolare l'attributo. Ad esempio, l'azienda Contoso ha due directory Azure AD, una per la produzione e una per i test. L'azienda desidera che gli utenti nel proprio tenant di prova modifichino il suffisso nell'ID di accesso.

`userPrincipalName <- Word([userPrincipalName],1,"@") & "@contosotest.com"`

In questa espressione è necessario selezionare tutti gli elementi che si trovano a sinistra del primo segno @ (Word) e concatenarli con una stringa fissa.

### Convertire un multivalore in un valore singolo

Alcuni attributi in Active Directory sono di tipo multivalore nello schema, anche se compaiono come valore singolo in Utenti e computer di Active Directory, ad esempio l'attributo description.

`description <- IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`

Se l'attributo ha un valore in questa espressione, si seleziona il primo elemento (Item) dell'attributo, si rimuovono gli spazi iniziali e finali (Trim) e quindi si mantengono i primi 448 caratteri (Left) della stringa.

## Concetto avanzato

### Confronto tra NULL e IgnoreThisFlow

Per le regole di sincronizzazione in entrata, è necessario usare sempre la costante **NULL**. Ciò indica che il flusso non ha valori da fornire e che un'altra regola può fornire un valore. Se nessuna regola ha fornito valori, l'attributo metaverse verrà rimosso.

Sono disponibili due costanti diverse da usare per le regole di sincronizzazione in uscita: NULL e IgnoreThisFlow. Entrambe indicano che il flusso di attributi non fornisce alcun valore, ma presentano differenze a livello di ciò che si verifica se nessun'altra regola fornisce valori. Se nella directory connessa è presente un valore esistente, una costante NULL eseguirà in modo temporaneo una eliminazione sull'attributo, rimuovendolo, mentre IgnoreThisFlow manterrà il valore esistente.

#### ImportedValue

La funzione ImportedValues è diversa dalle altre funzioni, poiché il relativo nome di attributo deve essere racchiuso tra virgolette invece che tra parentesi quadre: ImportedValue("proxyAddresses").

In genere, durante la sincronizzazione un attributo userà il valore previsto, anche se non è stato ancora esportato o se è stato ricevuto un errore durante l'esportazione ("livello massimo"). Una sincronizzazione in entrata presupporrà che un attributo che non ha ancora raggiunto una directory connessa la raggiungerà prima o poi. In alcuni casi è importante sincronizzare solo un valore confermato dalla directory connessa e in questo caso verrà usata la funzione ImportedValue ("livello di importazione ologrammi e delta").

Un esempio di questa situazione è disponibile nella regola di sincronizzazione in entrata inclusa nelle impostazioni comuni di Active Directory ed Exchange relative agli utenti. Nella configurazione ibrida di Exchange il valore aggiunto da Exchange Online deve essere sincronizzato solo se ne è stata confermata l'esportazione corretta:

`proxyAddresses <- RemoveDuplicates(Trim(ImportedValues("proxyAddresses")))`

Per l'elenco completo delle funzioni, vedere [Servizio di sincronizzazione Azure AD Connect: Riferimento alle funzioni](active-directory-aadconnectsync-functions-reference.md)


## Risorse aggiuntive

* [Servizio di sincronizzazione Azure AD Connect: Personalizzazione delle opzioni di sincronizzazione](active-directory-aadconnectsync-whatis.md)
* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)

<!--Image references-->

<!---HONumber=Nov15_HO3-->