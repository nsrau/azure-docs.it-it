<properties
	pageTitle="Servizio di sincronizzazione Azure AD Connect: Riferimento alle funzioni | Microsoft Azure"
	description="Riferimento delle espressioni di provisioning dichiarativo nel servizio di sincronizzazione Azure AD Connect."
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="StevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/23/2016"
	ms.author="andkjell;markvi"/>  


# Servizio di sincronizzazione Azure AD Connect: Riferimento alle funzioni

In Azure AD Connect le funzioni vengono usate per modificare il valore di un attributo durante la sincronizzazione. La sintassi delle funzioni viene espressa nel formato seguente: `<output type> FunctionName(<input type> <position name>, ..)`

Se una funzione è in overload e accetta più sintassi, verranno elencate tutte quelle valide. Le funzioni sono fortemente tipizzate e verificano che il tipo passato corrisponda al tipo documentato. Se il tipo non corrisponde, viene generato un errore.

I tipi vengono espressi con la sintassi seguente:

- **bin**: binario
- **bool**: booleano
- **dt**: data/ora UTC
- **enum**: enumerazione di costanti note
- **exp**: espressione per cui è prevista la restituzione di un valore booleano
- **mvbin**: binario multivalore
- **mvstr**: stringa multivalore
- **mvref**: riferimento multivalore
- **num**: numerico
- **ref**: riferimento
- **str**: stringa
- **var**: variante di (quasi) tutti gli altri tipi
- **void**: non restituisce un valore

Le funzioni con i tipi **mvbin**, **mvstr** e **mvref** possono funzionare solo con gli attributi multivalore. Le funzioni con **bin**, **str** e **ref** funzionano con gli attributi a valore singolo e multivalore.

## Riferimento alle funzioni

Elenco di funzioni | | | | |  
--------- | --------- | --------- | --------- | --------- | ---------
**Conversione** |  
[CBool](#cbool) | [CDate](#cdate) | [CGuid](#cguid) | [ConvertFromBase64](#convertfrombase64)
[ConvertToBase64](#converttobase64) | [ConvertFromUTF8Hex](#convertfromutf8hex) | [ConvertToUTF8Hex](#converttoutf8hex) | [CNum](#cnum)
[CRef](#cref) | [CStr](#cstr) | [StringFromGuid](#StringFromGuid) | [StringFromSid](#stringfromsid)
**Data/Ora** |  
[DateAdd](#dateadd) | [DateFromNum](#datefromnum) | [FormatDateTime](#formatdatetime) | [Now](#now)
[NumFromDate](#numfromdate) |  
**Directory** |  
[DNComponent](#dncomponent) | [DNComponentRev](#dncomponentrev) | [EscapeDNComponent](#escapedncomponent)
**Versione di valutazione** |  
[IsBitSet](#isbitset) | [IsDate](#isdate) | [IsEmpty](#isempty) | [IsGuid](#isguid)
[IsNull](#isnull) | [IsNullOrEmpty](#isnullorempty) | [IsNumeric](#isnumeric) | [IsPresent](#ispresent) |
[IsString](#isstring) |  
**Matematiche** |  
[BitAnd](#bitand) | [BitOr](#bitor) | [RandomNum](#randomnum)
**A più valori** |  
[Contiene](#contains) | [Numero](#count) | [Elemento](#item) | [ItemOrNull](#itemornull)
[Join](#join) | [RemoveDuplicates](#removeduplicates) | [Split](#split) |
**Flusso del programma** |  
[Errore](#error) | [IIF](#iif) | [Switch](#switch)
**Text** |  
[GUID](#guid) | [InStr](#instr) | [InStrRev](#instrrev) | [LCase](#lcase)
[Left](#left) | [Len](#len) | [LTrim](#ltrim) | [Mid](#mid)
[PadLeft](#padleft) | [PadRight](#padright) | [PCase](#pcase) | [Replace](#replace)
[ReplaceChars](#replacechars) | [Right](#right) | [RTrim](#rtrim) | [Trim](#trim)
[UCase](#ucase) | [Word](#word)

----------
### BitAnd

**Descrizione:** La funzione BitAnd imposta i bit specificati su un valore.

**Sintassi:** `num BitAnd(num value1, num value2)`

- value1, value2: valori numerici da unire con AND

**Osservazioni:** Questa funzione converte entrambi i parametri nella rappresentazione binaria e imposta un bit su:

- 0: se il valore di uno o entrambi i bit corrispondenti nella *maschera* e nel *flag* sono pari a 0
- 1: se entrambi i bit corrispondenti sono pari a 1.

In altre parole, restituisce 0 in tutti i casi tranne quando i bit corrispondenti di entrambi i parametri sono pari a 1.

**Esempio:** `BitAnd(&HF, &HF7)` Restituisce 7 perché i valori esadecimali "F" E "F7" restituiscono questo valore.

----------
### BitOr

**Descrizione:** La funzione BitOr imposta i bit specificati su un valore.

**Sintassi:** `num BitOr(num value1, num value2)`

- value1, value2: valori numerici da unire con OR

**Osservazioni:** Questa funzione converte entrambi i parametri nella rappresentazione binaria e imposta un bit su 1 se il valore di uno o entrambi i bit corrispondenti nella maschera e nel flag è pari a 1 e su 0 se entrambi i bit corrispondenti sono pari a 0. In altre parole, restituisce 1 in tutti i casi tranne dove i bit corrispondenti di entrambi i parametri sono pari a 0.

----------
### CBool

**Descrizione:** La funzione CBool restituisce un valore booleano basato sull'espressione valutata.

**Sintassi:** `bool CBool(exp Expression)`

**Osservazioni:** Se l'espressione restituisce un valore diverso da zero, CBool restituisce True. In caso contrario, restituisce False.

**Esempio:** `CBool([attrib1] = [attrib2])`

Restituisce True se entrambi gli attributi hanno lo stesso valore.

----------
### CDate

**Descrizione:** La funzione CDate restituisce un valore di data/ora UTC da una stringa. DateTime non è un attributo nativo in Sync, ma viene usato da alcune funzioni.

**Sintassi:** `dt CDate(str value)`

- Value: una stringa con una data, un'ora e facoltativamente un fuso orario

**Osservazioni:** La stringa restituita è sempre espressa in UTC.

**Esempio:** `CDate([employeeStartTime])` Restituisce un valore di data/ora basato sull'ora di inizio del dipendente

`CDate("2013-01-10 4:00 PM -8")` Restituisce un valore di data/ora che rappresenta "2013-01-11 12:00 AM"

----------
### CGuid

**Descrizione:** La funzione CGuid converte la rappresentazione di stringa di un GUID nella corrispondente rappresentazione binaria.

**Sintassi:** `bin CGuid(str GUID)`

- Stringa formattata con questo schema: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx o {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

----------
### Contiene

**Descrizione:** La funzione Contains trova una stringa all'interno di un attributo multivalore.

**Sintassi:**`num Contains (mvstring attribute, str search)` - distinzione maiuscole/minuscole`num Contains (mvstring attribute, str search, enum Casetype)` `num Contains (mvref attribute, str search)` - distinzione maiuscole/minuscole

- attribute: l'attributo multivalore da cercare.
- search: stringa da trovare nell'attributo.
- Casetype: CaseInsensitive o CaseSensitive.

Restituisce l'indice nell'attributo multivalore in cui è stata trovata la stringa. Se la stringa non viene trovata, restituisce 0.

**Osservazioni:** Per gli attributi stringa multivalore, viene effettuata la ricerca di sottostringhe nei valori. Per gli attributi di riferimento, la stringa cercata deve corrispondere esattamente al valore per essere considerata una corrispondenza.

**Esempio:** `IIF(Contains([proxyAddresses],"SMTP:")>0,[proxyAddresses],Error("No primary SMTP address found."))` Se l'attributo proxyAddresses include un indirizzo di posta elettronica primario (indicato da "SMTP:") viene restituito l'attributo proxyAddress. In caso contrario, viene restituito un errore.

----------
### ConvertFromBase64

**Descrizione:** La funzione ConvertFromBase64 converte il valore con codifica Base 64 specificato in una stringa normale.

**Sintassi:** `str ConvertFromBase64(str source)` - presuppone Unicode per la codifica `str ConvertFromBase64(str source, enum Encoding)`

- source: stringa con codifica Base 64
- Encoding: Unicode, ASCII, UTF8

**Esempio** `ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")` `ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Entrambi gli esempi restituiscono "*Hello world!*"

----------
### ConvertFromUTF8Hex

**Descrizione:** La funzione ConvertFromUTF8Hex converte il valore con codifica esadecimale UTF8 specificato in una stringa.

**Sintassi:** `str ConvertFromUTF8Hex(str source)`

- source: stringa con codifica a 2 byte UTF8

**Osservazioni:** La differenza tra questa funzione e ConvertFromBase64(,UTF8) è che il risultato può essere usato per l'attributo DN. Questo formato viene utilizzato da Azure Active Directory come DN.

**Esempio:** `ConvertFromUTF8Hex("48656C6C6F20776F726C6421")` Restituisce "*Hello world!*"

----------
### ConvertToBase64

**Descrizione:** La funzione ConvertToBase64 converte una stringa in una stringa Base 64 Unicode. Converte il valore di una matrice di interi nella rappresentazione di stringa equivalente in cifre con codifica Base 64.

**Sintassi:** `str ConvertToBase64(str source)`

**Esempio:** `ConvertToBase64("Hello world!")` Restituisce "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

----------
### ConvertToUTF8Hex

**Descrizione:** La funzione ConvertToUTF8Hex converte una stringa in un valore con codifica esadecimale UTF8.

**Sintassi:** `str ConvertToUTF8Hex(str source)`

**Osservazioni:** Il formato di output di questa funzione viene usato da Azure Active Directory come formato dell'attributo DN.

**Esempio:** `ConvertToUTF8Hex("Hello world!")` Restituisce 48656C6C6F20776F726C6421

----------
### Conteggio

**Descrizione:** La funzione Count restituisce il numero di elementi in un attributo multivalore.

**Sintassi:** `num Count(mvstr attribute)`

----------
### CNum

**Descrizione:** La funzione CNum accetta una stringa e restituisce un tipo di dati numerico.

**Sintassi:** `num CNum(str value)`

----------
### CRef

**Descrizione:** Converte una stringa in un attributo di riferimento.

**Sintassi:** `ref CRef(str value)`

**Esempio:** `CRef("CN=LC Services,CN=Microsoft,CN=lcspool01,CN=Pools,CN=RTC Service," & %Forest.LDAP%)`

----------
### CStr

**Descrizione:** La funzione CStr esegue la conversione in un tipo di dati stringa.

**Sintassi:** `str CStr(num value)` `str CStr(ref value)` `str CStr(bool value)`

- value: può essere un valore numerico, un attributo di riferimento o un valore booleano.

**Esempio:** `CStr([dn])` Può restituire "cn=Joe,dc=contoso,dc=com"

----------
### DateAdd

**Descrizione:** Restituisce un valore Date contenente una data alla quale è stato aggiunto un intervallo di tempo specificato.

**Sintassi:** `dt DateAdd(str interval, num value, dt date)`

- interval: espressione stringa che corrisponde all'intervallo di tempo da aggiungere. La stringa deve avere almeno uno dei valori seguenti:
 - yyyy Year
 - q Quarter
 - m Month
 - y Day of year
 - d Day
 - w Weekday
 - ww Week
 - h Hour
 - n Minute
 - s Second
- value: numero di unità da aggiungere. Può essere positivo (per ottenere date nel futuro) o negativo (per ottenere date nel passato).
- date: data/ora che rappresenta la data alla quale viene aggiunto l'intervallo.

**Esempio:** `DateAdd("m", 3, CDate("2001-01-01"))` Aggiunge 3 mesi e restituisce un valore di data/ora che rappresenta "2001-04-01".

----------
### DateFromNum

**Descrizione:** La funzione DateFromNum converte un valore nel formato di data di Active Directory in un tipo di data/ora.

**Sintassi:** `dt DateFromNum(num value)`

**Esempio:** `DateFromNum([lastLogonTimestamp])` `DateFromNum(129699324000000000)` Restituisce un valore di data/ora che rappresenta 2012-01-01 23:00:00

----------
### DNComponent

**Descrizione:** La funzione DNComponent restituisce il valore di un componente DN specificato, a partire da sinistra.

**Sintassi:** `str DNComponent(ref dn, num ComponentNumber)`

- dn: attributo di riferimento da interpretare
- ComponentNumber: componente nel DN da restituire

**Esempio:** `DNComponent([dn],1)` Se dn è "cn=Joe,ou=…," verrà restituito Joe

----------
### DNComponentRev

**Descrizione:** La funzione DNComponentRev restituisce il valore di un componente DN specificato, a partire da destra (dalla fine).

**Sintassi:** `str DNComponentRev(ref dn, num ComponentNumber)` `str DNComponentRev(ref dn, num ComponentNumber, enum Options)`

- dn: attributo di riferimento da interpretare
- ComponentNumber: componente nel DN da restituire
- Options: DC - Ignora tutti i componenti con "dc="

**Esempio:** Se dn è "cn=Joe,ou=Atlanta,ou=GA,ou=US, dc=contoso,dc=com", `DNComponentRev([dn],3)` `DNComponentRev([dn],1,"DC")` restituiscono entrambi US.

----------
### Errore

**Descrizione:** La funzione Error viene usata per restituire un errore personalizzato.

**Sintassi:** `void Error(str ErrorMessage)`

**Esempio:** `IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))` Se l'attributo accountName non è presente, viene generato un errore nell'oggetto.

----------
### EscapeDNComponent

**Descrizione:** La funzione EscapeDNComponent accetta un componente di un DN e inserisce un carattere di escape per consentirne la rappresentazione in LDAP.

**Sintassi:** `str EscapeDNComponent(str value)`

**Esempio:** `EscapeDNComponent("cn=" & [displayName]) & "," & %ForestLDAP%)` Verifica che sia possibile creare l'oggetto in una directory LDAP, anche se l'attributo displayName include caratteri che devono essere preceduti da un carattere di escape in LDAP.

----------
### FormatDateTime

**Descrizione:** La funzione FormatDateTime viene usata per formattare un valore di data/ora in una stringa con un formato specificato.

**Sintassi:** `str FormatDateTime(dt value, str format)`

- value: valore nel formato di data/ora
- format: stringa che rappresenta il formato in cui effettuare la conversione.

**Osservazioni:** I valori possibili per il formato sono disponibili qui: [Formati di data/ora definiti dall'utente (funzione Format)](http://msdn2.microsoft.com/library/73ctwf33(VS.90).aspx)

**Esempio:**

`FormatDateTime(CDate("12/25/2007"),"yyyy-mm-dd")` Il risultato è "2007-12-25".

`FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")` Può restituire "20140905081453.0Z"

----------
### GUID

**Descrizione:** La funzione GUID genera un nuovo GUID casuale.

**Sintassi:** `str GUID()`

----------
### IIF

**Descrizione:** La funzione IIF restituisce uno dei possibili valori di un set, in base a una condizione specificata.

**Sintassi:** `var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

- condition: qualsiasi valore o espressione che possa restituire true o false.
- valueIfTrue: se la condizione restituisce true, il valore restituito.
- valueIfFalse: se la condizione restituisce false, il valore restituito.

**Esempio:** `IIF([employeeType]="Intern","t-" & [alias],[alias])` Se l'utente è uno stagista, restituisce l'alias di un utente, aggiungendo "t-" all'inizio. In caso contrario. restituisce l'alias dell'utente invariato.

----------
### InStr

**Descrizione:** La funzione InStr trova la prima occorrenza di una sottostringa in una stringa.

**Sintassi:**

`num InStr(str stringcheck, str stringmatch)` `num InStr(str stringcheck, str stringmatch, num start)` `num InStr(str stringcheck, str stringmatch, num start , enum compare)`

- stringcheck: stringa da cercare
- stringmatch: stringa da trovare
- start: posizione iniziale per la ricerca della sottostringa
- compare: vbTextCompare o vbBinaryCompare

**Osservazioni:** Restituisce la posizione in cui è stata trovata la sottostringa o 0 se non viene trovata.

**Esempio:** `InStr("The quick brown fox","quick")` Restituisce 5

`InStr("repEated","e",3,vbBinaryCompare)` Restituisce 7

----------
### InStrRev

**Descrizione:** La funzione InStrRev trova l'ultima occorrenza di una sottostringa in una stringa.

**Sintassi:** `num InstrRev(str stringcheck, str stringmatch)` `num InstrRev(str stringcheck, str stringmatch, num start)` `num InstrRev(str stringcheck, str stringmatch, num start, enum compare)`

- stringcheck: stringa da cercare
- stringmatch: stringa da trovare
- start: posizione iniziale per la ricerca della sottostringa
- compare: vbTextCompare o vbBinaryCompare

**Osservazioni:** Restituisce la posizione in cui è stata trovata la sottostringa o 0 se non viene trovata.

**Esempio:** `InStrRev("abbcdbbbef","bb")` Restituisce 7

----------
### IsBitSet

**Descrizione:** La funzione IsBitSet verifica se un bit è o non è impostato.

**Sintassi:** `bool IsBitSet(num value, num flag)`

- value: valore numerico valutato. flag: valore numerico contenente il bit da valutare

**Esempio:** `IsBitSet(&HF,4)` Restituisce True perché il bit "4" è impostato come valore esadecimale "F"

----------
### IsDate

**Descrizione:** Se l'espressione può essere valutata come tipo di data/ora, la funzione IsDate restituisce True.

**Sintassi:** `bool IsDate(var Expression)`

**Osservazioni:** Usata per determinare se CDate() riuscirà.

----------
### IsEmpty

**Descrizione:** Se l'attributo è presente in CS o MV, ma restituisce una stringa vuota, la funzione IsEmpty restituisce True.

**Sintassi:** `bool IsEmpty(var Expression)`

----------
### IsGuid

**Descrizione:** Se la stringa può essere convertita in un GUID, la funzione IsGuid restituisce True.

**Sintassi:** `bool IsGuid(str GUID)`

**Osservazioni:** Un GUID viene definito come stringa in base a uno degli schemi seguenti: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx o {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

Usata per determinare se CGuid() riuscirà.

**Esempio:** `IIF(IsGuid([strAttribute]),CGuid([strAttribute]),NULL)` Se StrAttribute ha un formato GUID, restituisce una rappresentazione binaria. In caso contrario, restituisce Null.

----------
### IsNull

**Descrizione:** Se l'espressione restituisce Null, la funzione IsNull restituisce True.

**Sintassi:** `bool IsNull(var Expression)`

**Osservazioni:** Per un attributo, Null è espresso dall'assenza dell'attributo.

**Esempio:** `IsNull([displayName])` Restituisce True se l'attributo non è presente in CS o MV.

----------
### IsNullOrEmpty

**Descrizione:** Se l'espressione è Null o una stringa vuota, la funzione IsNullOrEmpty restituisce True.

**Sintassi:** `bool IsNullOrEmpty(var Expression)`

**Osservazioni:** Per un attributo, verrà restituito True se l'attributo è assente oppure se è presente, ma è una stringa vuota. La funzione inversa di questa funzione è denominata IsPresent.

**Esempio:** `IsNullOrEmpty([displayName])` Restituisce True se l'attributo non è presente oppure è una stringa vuota in CS o MV.

----------
### IsNumeric

**Descrizione:** La funzione IsNumeric restituisce un valore booleano che indica se un'espressione può essere valutata come tipo di numero.

**Sintassi:** `bool IsNumeric(var Expression)`

**Osservazioni:** Usata per determinare se CNum() riuscirà ad analizzare l'espressione.

----------
### IsString

**Descrizione:** Se l'espressione può essere valutata come tipo stringa, la funzione IsString restituisce True.

**Sintassi:** `bool IsString(var expression)`

**Osservazioni:** Usata per determinare se CStr() riuscirà ad analizzare l'espressione.

----------
### IsPresent

**Descrizione:** Se l'espressione restituisce una stringa non Null e non vuota, la funzione IsPresent restituisce True.

**Sintassi:** `bool IsPresent(var expression)`

**Osservazioni:** La funzione inversa di questa funzione è denominata IsNullOrEmpty.

**Esempio:** `Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

----------
### Item

**Descrizione:** La funzione Item restituisce un elemento da una stringa o un attributo multivalore.

**Sintassi:** `var Item(mvstr attribute, num index)`

- attribute: attributo multivalore
- index: indice di un elemento nella stringa multivalore.

**Osservazioni:** La funzione Item usata con la funzione Contains è utile, perché quest'ultima restituisce l'indice di un elemento nell'attributo multivalore.

Genera un errore se l'indice non è compreso nell'intervallo.

**Esempio:** `Mid(Item([proxyAddress],Contains([proxyAddress], "SMTP:")),6)` Restituisce l'indirizzo di posta elettronica primario.

----------
### ItemOrNull

**Descrizione:** La funzione ItemOrNull restituisce un elemento da una stringa o un attributo multivalore.

**Sintassi:** `var ItemOrNull(mvstr attribute, num index)`

- attribute: attributo multivalore
- index: indice di un elemento nella stringa multivalore.

**Osservazioni:** La funzione ItemOrNull usata con la funzione Contains è utile, perché quest'ultima restituisce l'indice di un elemento nell'attributo multivalore.

Se l'indice non è compreso nell'intervallo, restituisce un valore Null.

----------
### Join

**Descrizione:** La funzione Join accetta una stringa multivalore e restituisce una stringa a valore singolo con il separatore specificato inserito tra ogni elemento.

**Sintassi:** `str Join(mvstr attribute)` `str Join(mvstr attribute, str Delimiter)`

- attribute: attributo multivalore contenente stringhe da unire.
- delimiter: qualsiasi stringa usata per separare le sottostringhe nella stringa restituita. Se omessa, viene usato uno spazio (" "). Se Delimiter è una stringa di lunghezza zero ("") o Nothing, tutti gli elementi nell'elenco vengono concatenati senza delimitatori.

**Osservazioni:** Esiste un'analogia tra le funzioni Join e Split. La funzione Join accetta una matrice di stringhe e le unisce usando una stringa delimitatore per restituire una singola stringa. La funzione Split accetta una stringa e la separa in corrispondenza del delimitatore per restituire una matrice di stringhe. Tuttavia, la differenza principale consiste nel fatto che Join può concatenare stringhe con qualsiasi stringa delimitatore, mentre Split può separare stringhe usando unicamente un delimitatore di un solo carattere.

**Esempio:** `Join([proxyAddresses],",")` Può restituire: "SMTP:john.doe@contoso.com,smtp:jd@contoso.com"

----------
### LCase

**Descrizione:** La funzione LCase converte tutti i caratteri in una stringa in lettere minuscole.

**Sintassi:** `str LCase(str value)`

**Esempio:** `LCase("TeSt")` Restituisce "test".

----------
### Left

**Descrizione:** La funzione Left restituisce un numero di caratteri specificato a partire da sinistra di una stringa.

**Sintassi:** `str Left(str string, num NumChars)`

- string: stringa dalla quale restituire i caratteri
- NumChars: numero che identifica il numero di caratteri da restituire dall'inizio (sinistra) della stringa

**Osservazioni:** Una stringa contenente i primi caratteri numChars della stringa:

- Se numChars = 0, restituisce una stringa vuota.
- Se numChars < 0, restituisce una stringa di input.
- Se string è Null, restituisce una stringa vuota.

Se string contiene un numero di caratteri inferiore al numero specificato in numChars, viene restituita una stringa identica a string (ovvero contenente tutti i caratteri nel parametro 1).

**Esempio:** `Left("John Doe", 3)` Restituisce "Joh".

----------
### Len

**Descrizione:** La funzione Len restituisce il numero di caratteri in una stringa.

**Sintassi:** `num Len(str value)`

**Esempio:** `Len("John Doe")` Restituisce 8

----------
### LTrim

**Descrizione:** La funzione LTrim rimuove gli spazi vuoti iniziali da una stringa.

**Sintassi:** `str LTrim(str value)`

**Esempio:** `LTrim(" Test ")` Restituisce "Test"

----------
### Mid

**Descrizione:** La funzione Mid restituisce un numero di caratteri specificato a partire da una posizione specificata di una stringa.

**Sintassi:** `str Mid(str string, num start, num NumChars)`

- string: stringa dalla quale restituire i caratteri
- start: numero che identifica la posizione in una stringa dalla quale iniziare a restituire caratteri
- NumChars: numero che identifica il numero di caratteri da restituire dalla posizione nella stringa

**Osservazioni:** Restituisce i caratteri in numChars a partire dalla posizione start nella stringa. Stringa contenente i caratteri specificati in numChars a partire dalla posizione start nella stringa:

- Se numChars = 0, restituisce una stringa vuota.
- Se numChars < 0, restituisce una stringa di input.
- Se start è > della lunghezza della stringa, restituisce la stringa di input.
- Se start <= 0, restituisce la stringa di input.
- Se string è Null, restituisce una stringa vuota.

Se nella stringa non ci sono caratteri numChar rimanenti dalla posizione start, vengono restituiti quanti più caratteri possibile.

**Esempio:** `Mid("John Doe", 3, 5)` Restituisce "hn Do".

`Mid("John Doe", 6, 999)` Restituisce "Doe"

----------
### Now

**Descrizione:** La funzione Now restituisce un valore di data/ora che specifica la data e l'ora correnti, in base alla data e all'ora di sistema del computer.

**Sintassi:** `dt Now()`

----------
### NumFromDate

**Descrizione:** La funzione NumFromDate restituisce una data nel formato di AD.

**Sintassi:** `num NumFromDate(dt value)`

**Esempio:** `NumFromDate(CDate("2012-01-01 23:00:00"))` Restituisce 129699324000000000

----------
### PadLeft

**Descrizione:** La funzione PadLeft aggiunge a sinistra di una stringa il carattere di riempimento specificato, fino alla lunghezza indicata.

**Sintassi:** `str PadLeft(str string, num length, str padCharacter)`

- string: stringa da riempire.
- length: intero che rappresenta la lunghezza della stringa desiderata.
- padCharacter: stringa costituita da un singolo carattere da usare come carattere di riempimento

**Osservazioni:**

- Se la lunghezza di string è minore di length, padCharacter viene aggiunto ripetutamente all'inizio (sinistra) di string finché avrà una lunghezza uguale a length.
- PadCharacter può essere uno spazio, ma non un valore null.
- Se la lunghezza di string è uguale a o maggiore di length, la stringa viene restituita invariata.
- Se string ha una lunghezza maggiore di o uguale a length, viene restituita una stringa identica a string.
- Se la lunghezza di string è minore di length, viene restituita una nuova stringa con la lunghezza desiderata contenente il valore di string con l'aggiunta di padCharacter.
- Se la stringa è Null, la funzione restituisce una stringa vuota.

**Esempio:** `PadLeft("User", 10, "0")` Restituisce "000000User".

----------
### PadRight

**Descrizione:** La funzione PadRight aggiunge a destra di una stringa il carattere di riempimento specificato, fino alla lunghezza indicata.

**Sintassi:** `str PadRight(str string, num length, str padCharacter)`

- string: stringa da riempire.
- length: intero che rappresenta la lunghezza della stringa desiderata.
- padCharacter: stringa costituita da un singolo carattere da usare come carattere di riempimento

**Osservazioni:**

- Se la lunghezza di string è minore di length, padCharacter viene aggiunto ripetutamente alla fine (destra) di string finché avrà una lunghezza uguale a length.
- padCharacter può essere uno spazio, ma non un valore null.
- Se la lunghezza di string è uguale a o maggiore di length, la stringa viene restituita invariata.
- Se string ha una lunghezza maggiore di o uguale a length, viene restituita una stringa identica a string.
- Se la lunghezza di string è minore di length, viene restituita una nuova stringa con la lunghezza desiderata contenente il valore di string con l'aggiunta di padCharacter.
- Se la stringa è Null, la funzione restituisce una stringa vuota.

**Esempio:** `PadRight("User", 10, "0")` Restituisce "User000000".

----------
### PCase

**Descrizione:** La funzione PCase converte in maiuscolo il primo carattere di ogni parola delimitata da spazi contenuta in una stringa, mentre tutti gli altri caratteri vengono convertiti in minuscolo.

**Sintassi:** `String PCase(string)`

**Osservazioni:**

- Questa funzione attualmente non offre una corretta conversione di maiuscole e minuscole in caso di parola interamente in maiuscolo, come un acronimo.

**Esempio:** `PCase("TEsT")` Restituisce "Test".

`PCase(LCase("TEST"))` Restituisce "Test"

----------
### RandomNum

**Descrizione:** La funzione RandomNum restituisce un numero casuale compreso in un intervallo specificato.

**Sintassi:** `num RandomNum(num start, num end)`

- start: numero che identifica il limite inferiore del valore casuale da generare
- end: numero che identifica il limite superiore del valore casuale da generare

**Esempio:** `Random(100,999)` Può restituire 734.

----------
### RemoveDuplicates

**Descrizione:** La funzione RemoveDuplicates accetta una stringa multivalore e verifica che ogni valore sia univoco.

**Sintassi:** `mvstr RemoveDuplicates(mvstr attribute)`

**Esempio:** `RemoveDuplicates([proxyAddresses])` Restituisce un attributo proxyAddress purificato in cui sono stati rimossi tutti i valori duplicati.

----------
### Replace

**Descrizione:** La funzione Replace sostituisce tutte le occorrenze di una stringa in un'altra stringa.

**Sintassi:** `str Replace(str string, str OldValue, str NewValue)`

- string: stringa in cui sostituire i valori.
- OldValue: stringa da cercare e sostituire.
- NewValue: stringa oggetto della sostituzione.

**Osservazioni:** La funzione riconosce i moniker speciali seguenti:

- \\n - Nuova riga
- \\r - Ritorno a capo
- \\t - Tabulazione

**Esempio:** `Replace([address],"\r\n",", ")` Sostituisce CRLF con una virgola e uno spazio e può generare "One Microsoft Way, Redmond, WA, USA"

----------
### ReplaceChars

**Descrizione:** La funzione ReplaceChars sostituisce tutte le occorrenze dei caratteri trovati nella stringa ReplacePattern.

**Sintassi:** `str ReplaceChars(str string, str ReplacePattern)`

- string: stringa in cui sostituire i caratteri.
- ReplacePattern: stringa contenente un dizionario con i caratteri da sostituire.

Il formato è {source1}:{target1},{source2}:{target2},{sourceN},{targetN} dove source è il carattere da trovare e target la stringa con cui sostituirlo.

**Osservazioni:**

- La funzione accetta ogni occorrenza delle origini definite e le sostituisce con le destinazioni.
- Source deve corrispondere esattamente a un carattere (Unicode).
- Source non può essere una stringa vuota e non può essere più lunga di un carattere (errore di analisi).
- Target può contenere più caratteri, ad esempio ö:oe, β:ss.
- Target può essere una stringa vuota, per indicare che il carattere deve essere rimosso.
- Source fa distinzione tra maiuscole e minuscole e deve essere una corrispondenza esatta.
- I caratteri , (virgola) e : (due punti) sono riservati e non possono essere sostituiti usando questa funzione.
- Spazi e altri caratteri vuoti nella stringa ReplacePattern vengono ignorati.

**Esempio:** `%ReplaceString% = ’:,Å:A,Ä:A,Ö:O,å:a,ä:a,ö,o`

`ReplaceChars("Räksmörgås",%ReplaceString%)` Restituisce Raksmorgas

`ReplaceChars("O’Neil",%ReplaceString%)` Restituisce "ONeil". Viene definita la rimozione della virgoletta singola.

----------
### Right

**Descrizione:** La funzione Right restituisce un numero di caratteri specificato a partire dalla destra (fine) di una stringa.

**Sintassi:** `str Right(str string, num NumChars)`

- string: stringa dalla quale restituire i caratteri
- NumChars: numero che identifica il numero di caratteri da restituire dalla fine (destra) della stringa

**Osservazioni:** I caratteri NumChars vengono restituiti dall'ultima posizione della stringa.

Stringa contenente gli ultimi caratteri numChars in string:

- Se numChars = 0, restituisce una stringa vuota.
- Se numChars < 0, restituisce una stringa di input.
- Se string è Null, restituisce una stringa vuota.

Se string contiene un numero di caratteri inferiore al numero specificato in NumChars, viene restituita una stringa identica a string.

**Esempio:** `Right("John Doe", 3)` Restituisce "Doe".

----------
### RTrim

**Descrizione:** La funzione RTrim rimuove gli spazi vuoti finali da una stringa.

**Sintassi:** `str RTrim(str value)`

**Esempio:** `RTrim(" Test ")` Restituisce "test".

----------
### Split

**Descrizione:** La funzione Split accetta una stringa con valori separati da delimitatore e la converte in una stringa multivalore.

**Sintassi:** `mvstr Split(str value, str delimiter)` `mvstr Split(str value, str delimiter, num limit)`

- value: stringa con un carattere delimitatore da separare.
- delimiter: singolo carattere da usare come delimitatore.
- limit: numero massimo di valori che saranno restituiti.

**Esempio:** `Split("SMTP:john.doe@contoso.com,smtp:jd@contoso.com",",")` Restituisce una stringa multivalore con 2 elementi utili per l'attributo proxyAddress.

----------
### StringFromGuid

**Descrizione:** La funzione StringFromGuid accetta un GUID binario e lo converte in una stringa.

**Sintassi:** `str StringFromGuid(bin GUID)`

----------
### StringFromSid

**Descrizione:** La funzione StringFromSid converte una matrice di byte contenente un ID di sicurezza in una stringa.

**Sintassi:** `str StringFromSid(bin ObjectSID)`

----------
### Switch

**Descrizione:** La funzione Switch viene usata per restituire un singolo valore basato sulle condizioni valutate.

**Sintassi:** `var Switch(exp expr1, var value1[, exp expr2, var value … [, exp expr, var valueN]])`

- expr: espressione di tipo variant da valutare.
- value: valore da restituire se l'espressione corrispondente è True.

**Osservazioni:** L'elenco di argomenti della funzione Switch è costituito da coppie di espressioni e valori. Le espressioni vengono valutate da sinistra a destra e viene restituito il valore associato alla prima espressione valutata in True. Se le parti non vengono accoppiate correttamente, si verifica un errore di runtime.

Ad esempio, se expr1 è True, Switch restituisce value1. Se expr-1 è False, ma expr-2 è True, Switch restituisce value-2 e così via.

Switch restituisce Nothing se:

- Nessuna espressione è True.
- La prima espressione True ha un valore corrispondente Null.

Switch valuta tutte le espressioni, anche se ne restituisce una sola. Per questo motivo, è opportuno considerare attentamente gli effetti collaterali indesiderati. Ad esempio, se la valutazione di un'espressione restituisce un errore di divisione per zero, viene generato un errore.

Value può anche essere la funzione Error che restituirà una stringa personalizzata.

**Esempio:** `Switch([city] = "London", "English", [city] = "Rome", "Italian", [city] = "Paris", "French", True, Error("Unknown city"))` Restituisce la lingua parlata in alcune città importanti. In caso contrario, restituisce un errore.

----------
### Trim

**Descrizione:** La funzione Trim rimuove gli spazi vuoti iniziali e finali da una stringa.

**Sintassi:** `str Trim(str value)`

**Esempio:** `Trim(" Test ")` Restituisce "Test".

`Trim([proxyAddresses])` Rimuove gli spazi iniziali e finali per ogni valore nell'attributo proxyAddress.

----------
### UCase

**Descrizione:** La funzione UCase converte tutti i caratteri in una stringa in lettere maiuscole.

**Sintassi:** `str UCase(str string)`

**Esempio:** `UCase("TeSt")` Restituisce "TEST".

----------
### Word

**Descrizione:** La funzione Word restituisce una parola contenuta in una stringa, in base ai parametri che descrivono i delimitatori da usare e il numero della parola da restituire.

**Sintassi:** `str Word(str string, num WordNumber, str delimiters)`

- string: stringa dalla quale restituire una parola.
- WordNumber: numero che identifica il numero di parole da restituire.
- delimiters: stringa che rappresenta uno o più delimitatori da usare per identificare le parole

**Osservazioni:** Ogni stringa di caratteri contenuta nella stringa con valori separati da uno dei caratteri specificati nei delimitatori viene identificata come una parola:

- Se number è < 1, restituisce una stringa vuota.
- Se string è Null, restituisce una stringa vuota.

Se la stringa contiene meno delle parole specificate in number o se non contiene alcuna parola identificata da delimiters, viene restituita una stringa vuota.

**Esempio:** `Word("The quick brown fox",3," ")` Restituisce "brown"

`Word("This,string!has&many separators",3,",!&#")` Restituisce "has"

## Risorse aggiuntive

* [Informazioni sulle espressioni di provisioning dichiarativo](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)
* [Servizio di sincronizzazione Azure AD Connect: Personalizzazione delle opzioni di sincronizzazione](active-directory-aadconnectsync-whatis.md)
* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0824_2016-->