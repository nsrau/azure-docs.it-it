<properties
	pageTitle="Servizio di sincronizzazione Azure AD Connect: Riferimento alle funzioni | Microsoft Azure"
	description="Riferimento delle espressioni di provisioning dichiarativo nel servizio di sincronizzazione Azure AD Connect."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="StevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/04/2016"
	ms.author="markusvi"/>


# Servizio di sincronizzazione Azure AD Connect: Riferimento alle funzioni


Le funzioni in Azure Active Directory Sync vengono usate per modificare il valore di un attributo durante la sincronizzazione. <br> La sintassi delle funzioni viene espressa nel formato seguente: <br> `<output type> FunctionName(<input type> <position name>, ..)`

Se una funzione è in overload e accetta più sintassi, verranno elencate tutte quelle valide.<br> Le funzioni sono fortemente tipizzate e verificano che il tipo passato corrisponda al tipo documentato.<br> Se il tipo non corrisponde, viene generato un errore.

I tipi vengono espressi con la sintassi seguente:

- **bin**: binario
- **bool**: booleano
- **dt**: data/ora UTC
- **enum**: enumerazione di costanti note
- **exp**: espressione per cui è prevista la restituzione di un valore booleano
- **mvbin**: binario multivalore
- **mvstr**: riferimento multivalore
- **num**: numerico
- **ref**: riferimento a valore singolo
- **str**: stringa a valore singolo
- **var**: variante di (quasi) tutti gli altri tipi
- **void**: non restituisce un valore



## Riferimento alle funzioni

----------
**Conversione:**

[CBool](#cbool) &nbsp;&nbsp;&nbsp;&nbsp; [CDate](#cdate) &nbsp;&nbsp;&nbsp;&nbsp; [CGuid](#cguid) &nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp; [ConvertFromBase64](#convertfrombase64) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToBase64](#converttobase64) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertFromUTF8Hex](#convertfromutf8hex) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToUTF8Hex](#converttoutf8hex) &nbsp;&nbsp;&nbsp;&nbsp; [CNum](#cnum) &nbsp;&nbsp;&nbsp;&nbsp; [CRef](#cref) &nbsp;&nbsp;&nbsp;&nbsp; [CStr](#cstr) &nbsp;&nbsp;&nbsp;&nbsp; [StringFromGuid](#StringFromGuid) &nbsp;&nbsp;&nbsp;&nbsp; [StringFromSid](#stringfromsid)

**Data/Ora:**

[DateAdd](#dateadd) &nbsp;&nbsp;&nbsp;&nbsp; [DateFromNum](#datefromnum) &nbsp;&nbsp;&nbsp;&nbsp; [FormatDateTime](#formatdatetime) &nbsp;&nbsp;&nbsp;&nbsp; [Now](#now) &nbsp;&nbsp;&nbsp;&nbsp; [NumFromDate](#numfromdate)

**Directory**

[DNComponent](#dncomponent) &nbsp;&nbsp;&nbsp;&nbsp; [DNComponentRev](#dncomponentrev) &nbsp;&nbsp;&nbsp;&nbsp; [EscapeDNComponent](#escapedncomponent)

**Ispezione:**

[IsBitSet](#isbitset) &nbsp;&nbsp;&nbsp;&nbsp; [IsDate](#isdate) &nbsp;&nbsp;&nbsp;&nbsp; [IsEmpty](#isempty) &nbsp;&nbsp;&nbsp;&nbsp; [IsGuid](#isguid) &nbsp;&nbsp;&nbsp;&nbsp; [IsNull](#isnull) &nbsp;&nbsp;&nbsp;&nbsp; [IsNullOrEmpty](#isnullorempty) &nbsp;&nbsp;&nbsp;&nbsp; [IsNumeric](#isnumeric) &nbsp;&nbsp;&nbsp;&nbsp; [IsPresent](#ispresent) &nbsp;&nbsp;&nbsp;&nbsp; [IsString](#isstring)

**Matematiche:**

[BitAnd](#bitand) &nbsp;&nbsp;&nbsp;&nbsp; [BitOr](#bitor) &nbsp;&nbsp;&nbsp;&nbsp; [RandomNum](#randomnum)

**A più valori**

[Contains](#contains) &nbsp;&nbsp;&nbsp;&nbsp; [Count](#count) &nbsp;&nbsp;&nbsp;&nbsp; [Item](#item) &nbsp;&nbsp;&nbsp;&nbsp; [ItemOrNull](#itemornull) &nbsp;&nbsp;&nbsp;&nbsp; [Join](#join) &nbsp;&nbsp;&nbsp;&nbsp; [RemoveDuplicates](#removeduplicates) &nbsp;&nbsp;&nbsp;&nbsp; [Split](#split)

**Flusso di programmi:**

[Error](#error) &nbsp;&nbsp;&nbsp;&nbsp; [IIF](#iif) &nbsp;&nbsp;&nbsp;&nbsp; [Switch](#switch)


**Text**

[GUID](#guid) &nbsp;&nbsp;&nbsp;&nbsp; [InStr](#instr) &nbsp;&nbsp;&nbsp;&nbsp; [InStrRev](#instrrev) &nbsp;&nbsp;&nbsp;&nbsp; [LCase](#lcase) &nbsp;&nbsp;&nbsp;&nbsp; [Left](#left) &nbsp;&nbsp;&nbsp;&nbsp; [Len](#len) &nbsp;&nbsp;&nbsp;&nbsp; [LTrim](#ltrim) &nbsp;&nbsp;&nbsp;&nbsp; [Mid](#mid) &nbsp;&nbsp;&nbsp;&nbsp; [PadLeft](#padleft) &nbsp;&nbsp;&nbsp;&nbsp; [PadRight](#padright) &nbsp;&nbsp;&nbsp;&nbsp; [PCase](#pcase) &nbsp;&nbsp;&nbsp;&nbsp; [Replace](#replace) &nbsp;&nbsp;&nbsp;&nbsp; [ReplaceChars](#replacechars) &nbsp;&nbsp;&nbsp;&nbsp; [Right](#right) &nbsp;&nbsp;&nbsp;&nbsp; [RTrim](rtrim) &nbsp;&nbsp;&nbsp;&nbsp; [Trim](#trim) &nbsp;&nbsp;&nbsp;&nbsp; [UCase](#ucase) &nbsp;&nbsp;&nbsp;&nbsp; [Word](#word)

----------
### BitAnd

**Descrizione:**<br> La funzione BitAnd imposta i bit specificati su un valore.

**Sintassi:**<br> `num BitAnd(num value1, num value2)`

- value1, value2: valori numerici da unire con AND

**Osservazioni:**<br> Questa funzione converte entrambi i parametri nella rappresentazione binaria e imposta un bit su:

- 0: se il valore di uno o entrambi i bit corrispondenti nella *maschera* e nel *flag* sono pari a 0
- 1: se entrambi i bit corrispondenti sono pari a 1.

In altre parole, restituisce 0 in tutti i casi tranne quando i bit corrispondenti di entrambi i parametri sono pari a 1.

**Esempio:**<br> `BitAnd(&HF, &HF7)`<br> Restituisce 7 perché gli esadecimali "F" E "F7" restituiscono questo valore.

----------
### BitOr

**Descrizione:**<br> La funzione BitOr imposta i bit specificati su un valore.

**Sintassi:** <br> `num BitOr(num value1, num value2)`

- value1, value2: valori numerici da unire con OR

**Osservazioni:** <br> Questa funzione converte entrambi i parametri nella rappresentazione binaria e imposta un bit su 1 se il valore di uno o entrambi i bit corrispondenti nella maschera e nel flag sono pari a 1 e su 0 se entrambi i bit corrispondenti sono pari a 0. <br> In altre parole, restituisce 1 in tutti i casi tranne dove i bit corrispondenti di entrambi i parametri sono pari a 0.

----------
### CBool

**Descrizione:**<br> La funzione CBool restituisce un valore booleano basato sull'espressione valutata

**Sintassi:** <br> `bool CBool(exp Expression)`

**Osservazioni:**<br> Se l'espressione viene valutata in un valore diverso da zero, CBool restituisce True. In caso contrario, restituisce False.


**Esempio:**<br> `CBool([attrib1] = [attrib2])` <br>

Restituisce True se entrambi gli attributi hanno lo stesso valore.




----------
### CDate

**Descrizione:**<br> La funzione CDate restituisce una data/ora UTC da una stringa. DateTime non è un attributo nativo in Sync, ma viene usato da alcune funzioni.

**Sintassi:**<br> `dt CDate(str value)`

- Value: una stringa con una data, un'ora e facoltativamente un fuso orario

**Osservazioni:**<br> La stringa restituita è sempre espressa in UTC.

**Esempio:**<br> `CDate([employeeStartTime])` <br> Restituisce un valore di data/ora basato sull'ora di inizio del dipendente

`CDate("2013-01-10 4:00 PM -8")` <br> Restituisce un valore di data/ora che rappresenta "2013-01-11 12:00 AM"




----------
### CGuid

**Descrizione:**<br> La funzione CGuid converte la rappresentazione di stringa di un GUID nella rappresentazione binaria.

**Sintassi:**<br> `bin CGuid(str GUID)GUID`

- Stringa formattata con questo schema: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx o {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}




----------
### Contiene

**Descrizione:**<br> La funzione Contains trova una stringa all'interno di un attributo multivalore

**Sintassi:**<br> `num Contains (mvstring attribute, str search)` - distinzione maiuscole/minuscole<br> `num Contains (mvstring attribute, str search, enum Casetype)`<br> `num Contains (mvref attribute, str search)` - distinzione maiuscole/minuscole

- attribute: l'attributo multivalore da cercare.<br>
- search: stringa da trovare nell'attributo.<br>
- Casetype: CaseInsensitive o CaseSensitive.<br>

Restituisce l'indice nell'attributo multivalore in cui è stata trovata la stringa. Se la stringa non viene trovata, restituisce 0.


**Osservazioni:**<br> Per gli attributi di stringa multivalore, viene effettuata la ricerca di sottostringhe nei valori.<br> Per gli attributi di riferimento, la stringa cercata deve corrispondere esattamente al valore per essere considerata una corrispondenza.

**Esempi:**<br> `IIF(Contains([proxyAddresses],”SMTP:”)>0,[proxyAddresses],Error(“No primary SMTP address found.”))`<br> Se l'attributo proxyAddresses include un indirizzo di posta elettronica primario (indicato da "SMTP:") viene restituito l'attributo proxyAddress. In caso contrario viene restituito un errore.




----------
### ConvertFromBase64

**Descrizione:**<br> La funzione ConvertFromBase64 converte il valore con codifica Base 64 specificato in una stringa normale.

**Sintassi:**<br> `str ConvertFromBase64(str source)` - presuppone Unicode per la codifica <br> `str ConvertFromBase64(str source, enum Encoding)`

- source: stringa con codifica Base 64<br>
- Encoding: Unicode, ASCII, UTF8

**Esempio**<br> `ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`<br> `ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Entrambi gli esempi restituiscono "*Hello world!*"




----------
### ConvertFromUTF8Hex

**Descrizione:**<br> la funzione ConvertFromUTF8Hex converte il valore con codifica hex UTF8 specificato in una stringa.

**Sintassi:**<br> `str ConvertFromUTF8Hex(str source)`

- source: stringa con codifica a 2 byte UTF8

**Osservazioni:**<br> la differenza tra questa funzione e ConvertFromBase64(,UTF8) consiste nel fatto che il risultato può essere usato per l'attributo DN.<br> Questo formato viene utilizzato da Azure Active Directory come DN.

**Esempio:**<br> `ConvertFromUTF8Hex("48656C6C6F20776F726C6421")`<br> Restituisce "*Hello world!*"




----------
### ConvertToBase64

**Descrizione:** <br> La funzione ConvertToBase64 converte una stringa in una stringa Base 64 Unicode.<br> Converte il valore di una matrice di interi nella rappresentazione di stringa equivalente in cifre con codifica Base 64.

**Sintassi:** <br> `str ConvertToBase64(str source)`

**Esempio:** <br> `ConvertToBase64("Hello world!")` <br> Restituisce "SABlAGwAbABvACAAdwBvAHIAbABkACEA"




----------
### ConvertToUTF8Hex

**Descrizione:**<br> La funzione ConvertToUTF8Hex converte una stringa in un valore con codifica hex UTF8.

**Sintassi:**<br> `str ConvertToUTF8Hex(str source)`

**Osservazioni:**<br> Il formato di output di questa funzione viene usato da Azure Active Directory come formato dell'attributo DN.

**Esempio:** <br> `ConvertToUTF8Hex("Hello world!")` <br> Restituisce 48656C6C6F20776F726C6421




----------
### Conteggio

**Descrizione:**<br> La funzione Count restituisce il numero di elementi in un attributo multivalore

**Sintassi:** <br> `num Count(mvstr attribute)`




----------
### CNum

**Descrizione:** <br> La funzione CNum accetta una stringa e restituisce un tipo di dati numerico.

**Sintassi:** <br> `num CNum(str value)`




----------
### CRef

**Descrizione:** <br> Converte una stringa in un attributo di riferimento

**Sintassi:** <br> `ref CRef(str value)`

**Esempio:** <br> `CRef(“CN=LC Services,CN=Microsoft,CN=lcspool01, CN=Pools,CN=RTC Service,” & %Forest.LDAP%)`




----------
### CStr

**Descrizione:** <br> La funzione CStr esegue la conversione in un tipo di dati stringa.

**Sintassi:** <br> `str CStr(num value)` <br> `str CStr(ref value)` <br> `str CStr(bool value)` <br>

- value: può essere un valore numerico, un attributo di riferimento o un valore booleano.

**Esempio:** <br> `CStr([dn]) <br>` Può restituire "cn=Joe,dc=contoso,dc=com"




----------
### DateAdd

**Descrizione:** <br> Restituisce un valore Date contenente una data alla quale è stato aggiunto un intervallo di tempo specificato.

**Sintassi:** <br> `dt DateAdd(str interval, num value, dt date)`

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

**Esempio:** <br> `DateAdd("m", 3, CDate("2001-01-01"))` <br> Aggiunge 3 mesi e restituisce un valore data/ora che rappresenta "2001-04-01"




----------
### DateFromNum

**Descrizione:** <br> La funzione DateFromNum converte un valore nel formato di data di Active Directory in un tipo DateTime.

**Sintassi:** <br> `dt DateFromNum(num value)`

**Esempio:** <br> `DateFromNum([lastLogonTimestamp])` <br> `DateFromNum(129699324000000000)` <br> Restituisce un valore di data/ora che rappresenta 2012-01-01 23:00:00




----------
### DNComponent

**Descrizione:** <br> La funzione DNComponent restituisce il valore di un componente DN specificato, a partire da sinistra.

**Sintassi:** <br> `str DNComponent(ref dn, num ComponentNumber)`

- dn: attributo di riferimento da interpretare
- ComponentNumber: componente nel DN da restituire

**Esempio:** <br> `DNComponent([dn],1)` <br> Se dn è "cn=Joe,ou=…, verrà restituito Joe




----------
### DNComponentRev

**Descrizione:** <br> La funzione DNComponentRev restituisce il valore di un componente DN specificato, a partire da destra (dalla fine).

**Sintassi:** <br> `str DNComponentRev(ref dn, num ComponentNumber)` <br> `str DNComponentRev(ref dn, num ComponentNumber, enum Options)`

- dn: attributo di riferimento da interpretare
- ComponentNumber: componente nel DN da restituire
- Options: DC - Ignora tutti i componenti con "dc"

**Esempio:** <br> `If dn is “cn=Joe,ou=Atlanta,ou=GA,ou=US, dc=contoso,dc=com” then DNComponentRev([dn],3)` <br> `DNComponentRev([dn],1,”DC”)` <br> Restituisce US.




----------
### Errore

**Descrizione:** <br> La funzione Error viene usata per restituire un errore personalizzato.

**Sintassi:** <br> `void Error(str ErrorMessage)`

**Esempio:** <br> `IIF(IsPresent([accountName]),[accountName],Error(“AccountName is required”))` <br> Se l'attributo accountName non è presente, viene generato un errore nell'oggetto.




----------
### EscapeDNComponent

**Descrizione:** <br> La funzione EscapeDNComponent accetta un componente di un DN e inserisce un carattere di escape per consentirne la rappresentazione in LDAP.

**Sintassi:** <br> `str EscapeDNComponent(str value)`

**Esempio:** <br> `EscapeDNComponent(“cn=” & [displayName]) & “,” & %ForestLDAP%` <br> Verifica che sia possibile creare l'oggetto in una directory LDAP, anche se l'attributo displayName include caratteri che devono essere preceduti da un carattere di escape in LDAP.




----------
### FormatDateTime

**Descrizione:** <br> La funzione FormatDateTime viene usata per formattare un valore data/ora in una stringa con un formato specificato

**Sintassi:** <br> `str FormatDateTime(dt value, str format)`

- value: valore nel formato data/ora <br>
- format: stringa che rappresenta il formato in cui effettuare la conversione.

**Osservazioni:** <br> I valori possibili per il formato sono disponibili qui: [Formati di data/ora definiti dall'utente (funzione Format)](http://msdn2.microsoft.com/library/73ctwf33(VS.90).aspx)

**Esempio:** <br>

`FormatDateTime(CDate(“12/25/2007”),”yyyy-mm-dd”)` <br> Restituisce "2007-12-25".

`FormatDateTime(DateFromNum([pwdLastSet]),”yyyyMMddHHmmss.0Z”)` <br> Può restituire "20140905081453.0Z"




----------
### GUID

**Descrizione:** <br> La funzione GUID genera un nuovo GUID casuale

**Sintassi:** <br> `str GUID()`




----------
### IIF

**Descrizione:** <br> La funzione IIF restituisce uno dei possibili valori di un set, in base a una condizione specificata.

**Sintassi:** <br> `var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

- condition: qualsiasi valore o espressione che possa restituire true o false.
- valueIfTrue: valore che verrà restituito se la condizione restituisce true.
- valueIfFalse: valore che verrà restituito se la condizione restituisce false.

**Esempio:** <br> `IIF([employeeType]=“Intern”,”t-“&[alias],[alias])` <br> Restituisce l'alias di un utente, aggiungendo "t-" all'inizio se l'utente è un interno. In caso contrario restituisce l'alias dell'utente invariato.




----------
### InStr

**Descrizione:** <br> La funzione InStr trova la prima occorrenza di una sottostringa in una stringa

**Sintassi:** <br>

`num InStr(str stringcheck, str stringmatch)` <br> `num InStr(str stringcheck, str stringmatch, num start)` <br> `num InStr(str stringcheck, str stringmatch, num start , enum compare)`

- stringcheck: stringa da cercare <br>
- stringmatch: stringa da trovare <br>
- start: posizione iniziale per la ricerca della sottostringa <br>
- compare: vbTextCompare o vbBinaryCompare

**Osservazioni:** <br> Restituisce la posizione in cui è stata trovata la sottostringa o 0 se non viene trovata.

**Esempio:** <br> `InStr("The quick brown fox","quick")` <br> Restituisce 5

`InStr("repEated","e",3,vbBinaryCompare)` <br> Restituisce 7




----------
### InStrRev

**Descrizione:** <br> La funzione InStrRev trova l'ultima occorrenza di una sottostringa in una stringa

**Sintassi:** <br> `num InstrRev(str stringcheck, str stringmatch)` <br> `num InstrRev(str stringcheck, str stringmatch, num start)` <br> `num InstrRev(str stringcheck, str stringmatch, num start, enum compare)`

- stringcheck: stringa da cercare <br>
- stringmatch: stringa da trovare <br>
- start: posizione iniziale per la ricerca della sottostringa <br>
- compare: vbTextCompare o vbBinaryCompare

**Osservazioni:** <br> Restituisce la posizione in cui è stata trovata la sottostringa o 0 se non viene trovata.

**Esempio:** <br> `InStrRev("abbcdbbbef","bb")` <br> Restituisce 7.




----------
### IsBitSet

**Descrizione:** <br> La funzione IsBitSet verifica se un bit è o non è impostato

**Sintassi:** <br> `bool IsBitSet(num value, num flag)`

- value: valore numerico valutato. flag: valore numerico contenente il bit da valutare

**Esempio:** <br> `IsBitSet(&HF,4)` <br> Restituisce True perché il bit "4" è impostato nel valore esadecimale "F"




----------
### IsDate

**Descrizione:** <br> La funzione IsDate restituisce True se l'espressione può essere valutata come tipo DateTime.

**Sintassi:** <br> `bool IsDate(var Expression)`

**Osservazioni:** <br> Usata per determinare se CDate() riuscirà.




----------
###IsEmpty

**Descrizione:** <br> La funzione IsEmpty restituisce True se l'attributo è presente in CS o MV, ma restituisce una stringa vuota.

**Sintassi:** <br> `bool IsEmpty(var Expression)`




----------
###IsGuid

**Descrizione:** <br> La funzione IsGuid restituisce true se la stringa può essere convertita in un GUID.

**Sintassi:** <br> `bool IsGuid(str GUID)`

**Osservazioni:** <br> Un GUID viene definito come stringa in base a uno degli schemi seguenti: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx or {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

Usata per determinare se CGuid() riuscirà.

**Esempio:** <br> `IIF(IsGuid([strAttribute]),CGuid([strAttribute]),NULL)` <br> Se StrAttribute ha un formato GUID, restituisce una rappresentazione binaria. In caso contrario restituisce Null.




----------
###IsNull

**Descrizione:** <br> La funzione IsNull restituisce true se l'espressione restituisce Null.

**Sintassi:** <br> `bool IsNull(var Expression)`

**Osservazioni:** <br> Per un attributo, Null è espresso dall'assenza dell'attributo.

**Esempio:** <br> `IsNull([displayName])` <br> Restituisce True se l'attributo non è presente in CS o MV.




----------
###IsNullOrEmpty

**Descrizione:** <br> La funzione IsNullOrEmpty restituisce True se l'espressione è Null o una stringa vuota.

**Sintassi:** <br> `bool IsNullOrEmpty(var Expression)`

**Osservazioni:** <br> Per un attributo, verrà restituito True se l'attributo è assente oppure se è presente, ma è una stringa vuota.<br> La funzione inversa di questa funzione è denominata IsPresent.

**Esempio:** <br> `IsNull([displayName])` <br> Restituisce True se l'attributo non è presente oppure è una stringa vuota in CS o MV.




----------
### IsNumeric

**Descrizione:** <br> La funzione IsNumeric restituisce un valore booleano che indica se un'espressione può essere valutata come tipo di numero.

**Sintassi:** <br> `bool IsNumeric(var Expression)`

**Osservazioni:** <br> Usata per determinare se CNum() riuscirà ad analizzare l'espressione.




----------
### IsString

**Descrizione:** <br> La funzione IsString restituisce True se l'espressione può essere valutata come tipo stringa.

**Sintassi:** <br> `bool IsString(var expression)`

**Osservazioni:** <br> Usata per determinare se CStr() riuscirà ad analizzare l'espressione.




----------
### IsPresent

**Descrizione:** <br> La funzione IsPresent restituisce true se l'espressione viene valutata in una stringa non Null e non vuota.

**Sintassi:** <br> `bool IsPresent(var expression)`

**Osservazioni:** <br> La funzione inversa di questa funzione è denominata IsNullOrEmpty.

**Esempio:** <br>

`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`




----------
### Item

**Descrizione:** <br> La funzione Item restituisce un elemento da una stringa o un attributo multivalore.

**Sintassi:** <br> `var Item(mvstr attribute, num index)`

- attribute: attributo multivalore <br>
- index: indice di un elemento nella stringa multivalore.

**Osservazioni:** <br> La funzione Item usata con la funzione Contains è utile perché quest'ultima restituisce l'indice di un elemento nell'attributo multivalore.

Genera un errore se l'indice non è compreso nell'intervallo.

**Esempio:** <br> `Mid(Item([proxyAddress],Contains([proxyAddress], ”SMTP:”)),6)` <br> Restituisce l'indirizzo di posta elettronica principale.




----------
### ItemOrNull

**Descrizione:** <br> La funzione ItemOrNull restituisce un elemento da una stringa o un attributo multivalore.

**Sintassi:** <br> `var ItemOrNull(mvstr attribute, num index)`

- attribute: attributo multivalore <br>
- index: indice di un elemento nella stringa multivalore.

**Osservazioni:** <br> La funzione ItemOrNull usata con la funzione Contains è utile perché quest'ultima restituisce l'indice di un elemento nell'attributo multivalore.

Restituisce un valore Null se l'indice non è compreso nell'intervallo.




----------
### Join

**Descrizione:** <br> La funzione Join accetta una stringa multivalore e restituisce una stringa a valore singolo con il separatore specificato inserito tra ogni elemento.

**Sintassi:** <br> `str Join(mvstr attribute)` <br> `str Join(mvstr attribute, str Delimiter)`

- attribute: attributo multivalore contenente stringhe da unire. <br>
- delimiter: qualsiasi stringa usata per separare le sottostringhe nella stringa restituita. Se omessa, viene usato uno spazio (" "). Se Delimiter è una stringa di lunghezza zero ("") o Nothing, tutti gli elementi nell'elenco vengono concatenati senza delimitatori.

**Osservazioni**<br> Esiste un'analogia tra le funzioni Join e Split. La funzione Join accetta una matrice di stringhe e le unisce usando una stringa delimitatore per restituire una singola stringa. La funzione Split accetta una stringa e la separa in corrispondenza del delimitatore per restituire una matrice di stringhe. Tuttavia, la differenza principale consiste nel fatto che Join può concatenare stringhe con qualsiasi stringa delimitatore, mentre Split può separare stringhe usando unicamente un delimitatore di un solo carattere.

**Esempio:** <br> `Join([proxyAddresses],”,”)` <br> Può restituire: "SMTP:john.doe@contoso.com,smtp:jd@contoso.com”




----------
### LCase

**Descrizione:** <br> La funzione LCase converte tutti i caratteri in una stringa in lettere minuscole.

**Sintassi:** <br> `str LCase(str value)`

**Esempio:** <br> `LCase(“TeSt”)` <br> Restituisce "test".




----------
### Left

**Descrizione:** <br> La funzione Left restituisce un numero di caratteri specificato a partire da sinistra di una stringa.

**Sintassi:** <br> `str Left(str string, num NumChars)`

- string: stringa dalla quale restituire i caratteri <br>
- NumChars: numero che identifica il numero di caratteri da restituire dall'inizio (sinistra) della stringa

**Osservazioni:** <br> Stringa contenente i primi caratteri numChars in string:

- Se numChars = 0, restituisce una stringa vuota.
- Se numChars < 0, restituisce una stringa di input.
- Se string è Null, restituisce una stringa vuota.

Se string contiene un numero di caratteri inferiore al numero specificato in numChars, viene restituita una stringa identica a string (ovvero contenente tutti i caratteri nel parametro 1).

**Esempio:** <br> `Left(“John Doe”, 3)` <br> Restituisce "Joh".




----------
### Len

**Descrizione:** <br> La funzione Len restituisce il numero di caratteri in una stringa.

**Sintassi:** <br> `num Len(str value)`

**Esempio:** <br> `Len(“John Doe”)` <br> Restituisce 8.




----------
### LTrim

**Descrizione:** <br> La funzione LTrim rimuove gli spazi vuoti iniziali da una stringa.

**Sintassi:** <br> `str LTrim(str value)`

**Esempio:** <br> `LTrim(“ Test ”)` <br> Restituisce "Test ".




----------
### Mid

**Descrizione:** <br> La funzione Mid restituisce un numero di caratteri specificato a partire da una posizione specificata di una stringa.

**Sintassi:** <br> `str Mid(str string, num start, num NumChars)`

- string: stringa dalla quale restituire i caratteri <br>
- start: numero che identifica la posizione in una stringa dalla quale iniziare a restituire caratteri
- NumChars: numero che identifica il numero di caratteri da restituire dalla posizione nella stringa


**Osservazioni:** <br> Restituisce i caratteri specificati in numChars a partire dalla posizione start nella stringa.<br> Stringa contenente i caratteri specificati in numChars a partire dalla posizione start nella stringa:

- Se numChars = 0, restituisce una stringa vuota.
- Se numChars < 0, restituisce una stringa di input.
- Se start è > della lunghezza della stringa, restituisce la stringa di input.
- Se start <= 0, restituisce la stringa di input.
- Se string è Null, restituisce una stringa vuota.

Se nella stringa non ci sono caratteri numChar rimanenti dalla posizione start, vengono restituiti quanti più caratteri possibile.

**Esempio:** <br>

`Mid(“John Doe”, 3, 5)` <br> Restituisce "hn Do".

`Mid(“John Doe”, 6, 999)` <br> Restituisce "Doe".




----------
### Now

**Descrizione:** <br> La funzione Now restituisce un valore data/ora che specifica la data e l'ora correnti, in base alla data e ora di sistema del computer.

**Sintassi:** <br> `dt Now()`




----------
### NumFromDate

**Descrizione:** <br> La funzione NumFromDate restituisce una data nel formato di AD.

**Sintassi:** <br> `num NumFromDate(dt value)`


**Esempio:** <br> `NumFromDate(CDate("2012-01-01 23:00:00"))` <br> Restituisce 129699324000000000




----------
### PadLeft

**Descrizione:** <br> La funzione PadLeft aggiunge a sinistra di una stringa il carattere di riempimento specificato, fino alla lunghezza indicata.

**Sintassi:** <br> `str PadLeft(str string, num length, str padCharacter)`

- string: stringa da riempire. <br>
- length: intero che rappresenta la lunghezza della stringa desiderata. <br>
- padCharacter: stringa costituita da un singolo carattere da usare come carattere di riempimento



----------
### Osservazioni

- Se la lunghezza di string è minore di length, padCharacter viene aggiunto ripetutamente all'inizio (sinistra) di string finché avrà una lunghezza uguale a length.
- PadCharacter può essere uno spazio, ma non un valore null.
- Se la lunghezza di string è uguale a o maggiore di length, la stringa viene restituita invariata.
- Se string ha una lunghezza maggiore di o uguale a length, viene restituita una stringa identica a string.
- Se la lunghezza di string è minore di length, viene restituita una nuova stringa con la lunghezza desiderata contenente il valore di string con l'aggiunta di padCharacter.
- Se la stringa è Null, la funzione restituisce una stringa vuota.

**Esempio:** <br> `PadLeft(“User”, 10, “0”)` <br> Restituisce "000000User".




----------
### PadRight

**Descrizione:** <br> La funzione PadRight aggiunge a destra di una stringa il carattere di riempimento specificato, fino alla lunghezza indicata.

**Sintassi:** <br> `str PadRight(str string, num length, str padCharacter)`

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


**Esempio:** <br> `PadRight(“User”, 10, “0”)` <br> Restituisce "User000000".




----------
### PCase

**Descrizione:** <br> La funzione PCase converte in maiuscolo il primo carattere di ogni parola delimitata da spazi contenuta in una stringa, mentre tutti gli altri caratteri vengono convertiti in minuscolo.

**Sintassi:** <br> `String PCase(string)`

**Esempio:** <br> `PCase(“TEsT”)` <br> Restituisce "Test".




----------
### RandomNum

**Descrizione:** <br> La funzione RandomNum restituisce un numero casuale compreso in un intervallo specificato.

**Sintassi:** <br> `num RandomNum(num start, num end)`

- start: numero che identifica il limite inferiore del valore casuale da generare <br>
- end: numero che identifica il limite superiore del valore casuale da generare

**Esempio:** <br> `Random(100,999)` <br> Restituisce 734.




----------
### RemoveDuplicates

**Descrizione:** <br> La funzione RemoveDuplicates accetta una stringa multivalore e verifica che ogni valore sia univoco.

**Sintassi:** <br> `mvstr RemoveDuplicates(mvstr attribute)`

**Esempio:** <br> `RemoveDuplicates([proxyAddresses])` <br> Restituisce un attributo proxyAddress purificato in cui sono stati rimossi tutti i valori duplicati.




----------
### Replace

**Descrizione:** <br> La funzione Replace sostituisce tutte le occorrenze di una stringa in un'altra stringa.

**Sintassi:** <br> `str Replace(str string, str OldValue, str NewValue)`

- string: stringa in cui sostituire i valori. <br>
- OldValue: stringa da cercare e sostituire. <br>
- NewValue: stringa oggetto della sostituzione.


**Osservazioni:** <br> La funzione riconosce i moniker speciali seguenti:

- \\n - Nuova riga
- \\r - Ritorno a capo
- \\t - Tabulazione


**Esempio:** <br>

`Replace([address],”\r\n”,”, “)` <br> Sostituisce CRLF con una virgola e uno spazio e può generare "One Microsoft Way, Redmond, WA, USA"




----------
### ReplaceChars

**Descrizione:** <br> La funzione ReplaceChars sostituisce tutte le occorrenze dei caratteri trovati nella stringa ReplacePattern.

**Sintassi:** <br> `str ReplaceChars(str string, str ReplacePattern)`

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


**Esempio:** <br> '%ReplaceString% = ':,Å:A,Ä:A,Ö:O,å:a,ä:a,ö,o'

`ReplaceChars(”Räksmörgås”,%ReplaceString%)` <br> Restituisce Raksmorgas

`ReplaceChars(“O’Neil”,%ReplaceString%)` <br> Restituisce "ONeil", viene definita la rimozione della virgoletta singola.




----------
### Right

**Descrizione:** <br> La funzione Right restituisce un numero di caratteri specificato a partire da destra (fine) di una stringa.

**Sintassi:** <br> `str Right(str string, num NumChars)`

- string: stringa dalla quale restituire i caratteri
- NumChars: numero che identifica il numero di caratteri da restituire dalla fine (destra) della stringa

**Osservazioni:** <br> I caratteri NumChars vengono restituiti dall'ultima posizione di string.

Stringa contenente gli ultimi caratteri numChars in string:

- Se numChars = 0, restituisce una stringa vuota.
- Se numChars < 0, restituisce una stringa di input.
- Se string è Null, restituisce una stringa vuota.

Se string contiene un numero di caratteri inferiore al numero specificato in NumChars, viene restituita una stringa identica a string.

**Esempio:** <br> `Right(“John Doe”, 3)` <br> Restituisce "Doe".




----------
### RTrim

**Descrizione:** <br> La funzione RTrim rimuove gli spazi vuoti finali da una stringa.

**Sintassi:** <br> `str RTrim(str value)`

**Esempio:** <br> `RTrim(“ Test ”)` <br> Restituisce " Test".




----------
### Split

**Descrizione:** <br> La funzione Split accetta una stringa con valori separati da delimitatore e la converte in una stringa multivalore.


**Sintassi:** <br> `mvstr Split(str value, str delimiter)` <br? `mvstr Split(str value, str delimiter, num limit)`

- value: stringa con un carattere delimitatore da separare.
- delimiter: singolo carattere da usare come delimitatore.
- limit: numero massimo di valori che saranno restituiti.

**Esempio:** <br> `Split(“SMTP:john.doe@contoso.com,smtp:jd@contoso.com”,”,”)` <br> Restituisce una stringa multivalore con 2 elementi utili per l'attributo proxyAddress




----------
### StringFromGuid

**Descrizione:** <br> La funzione StringFromGuid accetta un GUID binario e lo converte in una stringa

**Sintassi:** <br> `str StringFromGuid(bin GUID)`




----------
### StringFromSid

**Descrizione:** <br> La funzione StringFromSid converte una matrice di byte o una matrice di byte multivalore contenente un ID di sicurezza in una stringa o una stringa multivalore.

**Sintassi:** <br> `str StringFromSid(bin ObjectSID)` <br> `mvstr StringFromSid(mvbin ObjectSID)`




----------
### Switch

**Descrizione:** <br> La funzione Switch viene usata per restituire un singolo valore basato sulle condizioni valutate.

**Sintassi:** <br> `var Switch(exp expr1, var value1[, exp expr2, var value … [, exp expr, var valueN]])`

- expr: espressione di tipo variant da valutare.
- value: valore da restituire se l'espressione corrispondente è True.

**Osservazioni:** <br> L'elenco di argomenti della funzione Switch è costituito da coppie di espressioni e valori. Le espressioni vengono valutate da sinistra a destra e viene restituito il valore associato alla prima espressione valutata in True. Se le parti non vengono accoppiate correttamente, si verifica un errore di runtime.

Ad esempio, se expr1 è True, Switch restituisce value1. Se expr-1 è False, ma expr-2 è True, Switch restituisce value-2 e così via.

Switch restituisce Nothing se: - Nessuna delle espressioni è True. - La prima espressione True ha un valore corrispondente Null.

Switch valuta tutte le espressioni, anche se ne restituisce una sola. Per questo motivo, è opportuno considerare attentamente gli effetti collaterali indesiderati. Ad esempio, se la valutazione di un'espressione restituisce un errore di divisione per zero, viene generato un errore.

Value può anche essere la funzione Error che restituirà una stringa personalizzata.

**Esempio:** <br> `Switch([city] = "London", "English", [city] = "Rome", "Italian", [city] = "Paris", "French", True, Error(“Unknown city”))` <br> Restituisce la lingua parlata in alcune città importanti. In caso contrario, restituisce un errore.




----------
### Trim

**Descrizione:** <br> La funzione Trim rimuove gli spazi vuoti iniziali e finali da una stringa.

**Sintassi:** <br> `str Trim(str value)` <br> `mvstr Trim(mvstr value)`

**Esempio:** <br> `Trim(“ Test ”)` <br> Restituisce "Test".

`Trim([proxyAddresses])` <br> Rimuove gli spazi iniziali e finali per ogni valore nell'attributo proxyAddress.




----------
### UCase

**Descrizione:** <br> La funzione UCase converte tutti i caratteri in una stringa in lettere maiuscole.

**Sintassi:** <br> `str UCase(str string)`

**Esempio:** <br> `UCase(“TeSt”)` <br> Restituisce "TEST".




----------
### Word

**Descrizione:** <br> La funzione Word restituisce una parola contenuta in una stringa, in base ai parametri che descrivono i delimitatori da usare e al numero della parola da restituire.

**Sintassi:** <br> `str Word(str string, num WordNumber, str delimiters)`

- string: stringa dalla quale restituire una parola.
- WordNumber: numero che identifica il numero di parole da restituire.
- delimiters: stringa che rappresenta uno o più delimitatori da usare per identificare le parole

**Osservazioni:** <br> Ogni stringa di caratteri contenuta in string con valori separati da uno dei caratteri specificati in delimiters viene identificata come una parola:

- Se number è < 1, restituisce una stringa vuota.
- Se string è Null, restituisce una stringa vuota.

Se la stringa contiene meno delle parole specificate in number o se non contiene alcuna parola identificata da delimeters, viene restituita una stringa vuota.


**Esempio:** <br> `Word(“The quick brown fox”,3,” “)` <br> Restituisce "brown"

`Word(“This,string!has&many seperators”,3,”,!&#”)` <br> Restituirà "has"


## Risorse aggiuntive

* [Informazioni sulle espressioni di provisioning dichiarativo](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)
* [Servizio di sincronizzazione Azure AD Connect: Personalizzazione delle opzioni di sincronizzazione](active-directory-aadconnectsync-whatis.md)
* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)


<!--Image references-->

<!---HONumber=AcomDC_0107_2016-->