<properties
	pageTitle="Servizio di sincronizzazione Azure AD Connect: Informazioni sulle espressioni di provisioning dichiarativo"
	description="Informazioni sulle espressioni di provisioning dichiarativo."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/27/2015"
	ms.author="markusvi"/>


# Servizio di sincronizzazione Azure AD Connect: Informazioni sulle espressioni di provisioning dichiarativo

Il Servizio di sincronizzazione di Azure Active Directory Connect sono basati sul provisioning dichiarativo introdotto per la prima volta in Forefront Identity Manage 2010 per permettere di implementare la logica di business di integrazione completa delle identità senza dovere scrivere codice.

Una parte essenziale del provisioning dichiarativo è il linguaggio delle espressioni usato nei flussi di attributi. Il linguaggio usato è un subset di Microsoft® Visual Basic®, Applications Edition (VBA). Questo linguaggio viene usato in Microsoft Office e verrà riconosciuto anche dagli utenti con esperienza in VBScript. Il linguaggio delle espressioni di provisioning dichiarativo usa solo funzioni e non è un linguaggio strutturato, né include metodi o istruzioni. Le funzioni verranno invece annidate per esprimere flussi di programmi.

Per altri dettagli, vedere [Riferimenti per il linguaggio Visual Basic, Applications Edition per Office 2013](https://msdn.microsoft.com/library/gg264383(v=office.15).aspx).

Gli attributi sono fortemente tipizzati. Una funzione che prevede di ricevere un attributo String a valore singolo non accetterà attributi multivalore o attributi di tipo diverso. È applicata anche la distinzione maiuscole/minuscole. Se per i nomi delle funzioni e i nomi degli attributi non viene rispettata correttamente la distinzione lettere maiuscole/minuscole, verrà generato un errore.





## Definizioni e identificatori del linguaggio

- I nomi delle funzioni sono seguiti dagli argomenti racchiusi tra parentesi: FunctionName(<<argument 1>>,<<argument N>>).
- Gli attributi sono identificati da parentesi quadre: [attributeName].
- I parametri sono identificati dal segno di percentuale: %ParameterName%
- Le costanti di stringa sono racchiuse tra virgolette: ad esempio "Contoso"
- I valori numerici sono espressi senza virgolette e devono essere decimali. Ai valori esadecimali viene aggiunto un prefisso di tipo &H. Ad esempio, 98052, &HFF
- I valori booleani sono espressi con costanti: True, False.
- Le costanti predefinite sono espresse solo tramite il relativo nome: NULL, CRLF, IgnoreThisFlow


## Operatori

È possibile usare gli operatori seguenti:

- **Confronto**: <, <=, <>, =, >, >=
- **Matematici**: +, -, *, -
- **Stringa**: & (concatenazione)
- **Logici**: && (AND), || (OR)
- **Ordine di valutazione**: ( )



Gli operatori sono valutati da sinistra a destra. L'operazione 2*(5+3) è diversa da 2*5+3.<br> Le parentesi ( ) sono usate per cambiare l'ordine di valutazione.





## Parametri

Un parametro è definito da un connettore o da un amministratore tramite PowerShell. I parametri includeranno in genere valori diversi nei sistemi specifici, ad esempio il nome del dominio in cui si trova l'utente. Possono essere usati nei flussi di attributi.

Active Directory Connector fornisce i parametri seguenti per le regole di sincronizzazione in entrata:

 
| Domain.Netbios | Domain.FQDN | Domain.LDAP | | Forest.Netbios | Forest.FQDN | Forest.LDAP |
 

Il sistema fornisce il parametro seguente:

Connector.ID

L'esempio seguente popolerà il dominio dell'attributo metaverse con il nome netbios del dominio in cui si trova l'utente.

domain <- %Domain.Netbios%

## Scenari comuni

### Lunghezza degli attributi

Per impostazione predefinita, gli attributi di stringa sono configurati come indicizzabili e la lunghezza massima è di 448 caratteri. Se si usano attributi di stringa che potrebbero avere una lunghezza maggiore, assicurarsi di includere il codice seguente nel flusso di attributi:

`attributeName <- Left([attributeName],448)`

### Modifica di userPrincipalSuffix

L'attributo userPrincipalName in Active Directory non è sempre noto agli utenti e potrebbe non essere adatto come ID di accesso. La guida all'installazione di AAD Sync permette di scegliere un attributo diverso, ad esempio mail. In alcuni casi è però necessario calcolare l'attributo. Ad esempio, l'azienda Contoso ha due directory AAD, una per la produzione e una per i test. L'azienda vuole che gli utenti nel tenant di test cambi semplicemente il suffisso nell'account di accesso ID.userPrincipalName <- Word([userPrincipalName],1,"@") & "@contosotest.com"

necessario selezionare tutti gli elementi dell'espressione che si trovano a sinistra del primo segno @ (Word) e concatenarli con una stringa fissa.





### Convertire un multivalore in un valore singolo

Alcuni attributi in Active Directory sono di tipo multivalore nello schema, anche se compaiono come valore singolo in Utenti e computer di Active Directory, ad esempio l'attributo description.

Se l'attributo ha un valore in questa espressione, si seleziona il primo elemento (Item) dell'attributo, si rimuovono gli spazi iniziali e finali (Trim) e quindi si mantengono i primi 448 caratteri (Left) della stringa.



## Concetto avanzato

### Confronto tra NULL e IgnoreThisFlow

Per le regole di sincronizzazione in entrata, è necessario usare sempre la costante **NULL**. Ciò indica che il flusso non ha valori da fornire e che un'altra regola può fornire un valore. Se nessuna regola ha fornito valori, l'attributo metaverse verrà rimosso.

Sono disponibili due costanti diverse da usare per le regole di sincronizzazione in uscita: NULL e IgnoreThisFlow. Entrambe indicano che il flusso di attributi non fornisce alcun valore, ma presentano differenze a livello di ciò che si verifica se nessun'altra regola fornisce valori. Se nella directory connessa è presente un valore esistente, una costante NULL eseguirà in modo temporaneo una eliminazione sull'attributo, rimuovendolo, mentre IgnoreThisFlow manterrà il valore esistente.



#### ImportedValue

La funzione ImportedValues è diversa dalle altre funzioni, poiché il relativo nome di attributo deve essere racchiuso tra virgolette invece che tra parentesi quadre: ImportedValue("proxyAddresses").

In genere, durante la sincronizzazione un attributo userà il valore previsto, anche se non è stato ancora esportato o se è stato ricevuto un errore durante l'esportazione ("livello massimo"). Una sincronizzazione in entrata presupporrà che un attributo che non ha ancora raggiunto una directory connessa la raggiungerà prima o poi. In alcuni casi è importante sincronizzare solo un valore confermato dalla directory connessa e in questo caso verrà usata la funzione ImportedValue ("livello di importazione ologrammi e delta").

Un esempio di questa situazione è disponibile nella regola di sincronizzazione in entrata inclusa nelle impostazioni comuni di Active Directory ed Exchange relative agli utenti. Nella configurazione ibrida di Exchange il valore aggiunto da Exchange Online deve essere sincronizzato solo se ne è stata confermata l'esportazione corretta:


`proxyAddresses <- RemoveDuplicates(Trim(ImportedValues(“proxyAddresses”)))`

Per l'elenco completo delle funzioni, vedere [Servizio di sincronizzazione Azure AD Connect: Riferimento alle funzioni](active-directory-aadconnectsync-functions-reference.md)


## Risorse aggiuntive

* [Servizio di sincronizzazione Azure AD Connect: Personalizzazione delle opzioni di sincronizzazione](active-directory-aadconnectsync-whatis.md)
* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)
 
<!--Image references-->

<!---HONumber=August15_HO7-->