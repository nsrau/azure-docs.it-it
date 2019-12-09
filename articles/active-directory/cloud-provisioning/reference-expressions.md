---
title: Espressioni e informazioni di riferimento sulle funzioni del provisioning cloud di Azure AD Connect
description: reference
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d250377e15b957c10322dbba9ca587dd58944ad
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794977"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Scrittura di espressioni per il mapping degli attributi in Azure Active Directory
Quando si configura il provisioning cloud, come mapping degli attributi è possibile specificare il mapping di espressioni. 

Il mapping di espressioni consente di personalizzare gli attributi usando un'espressione di tipo script.  In questo modo è possibile trasformare i dati locali in un valore nuovo o diverso.  Ad esempio, è possibile scegliere di combinare due attributi in uno singolo, perché questo attributo singolo viene usato da una delle applicazioni cloud.

Nel documento seguente verranno trattate le espressioni di tipo script usate per trasformare i dati.  Questa è solo una parte del processo.  Successivamente sarà necessario usare questa espressione e inserirla in una richiesta Web per il tenant.  Per altre informazioni, vedere [Trasformazioni](how-to-transformation.md)

## <a name="syntax-overview"></a>Panoramica della sintassi
La sintassi per le espressioni per i mapping degli attributi è simile a quella delle funzioni di Visual Basic for Applications (VBA).

* L'intera espressione deve essere definita in termini di funzioni, che sono costituite da un nome seguito da argomenti racchiusi tra parentesi: <br>
  *FunctionName(`<<argument 1>>`,`<<argument N>>`)*
* È possibile annidare le funzioni in altre funzioni. Ad esempio: <br> *FunctionOne(FunctionTwo(`<<argument1>>`))*
* È possibile passare tre tipi diversi di argomenti nelle funzioni:
  
  1. Attributi, che devono essere racchiusi tra parentesi quadre. Ad esempio: [NomeAttributo]
  2. Costanti di stringa, che devono essere racchiuse tra virgolette doppie. Ad esempio:  "Stati Uniti"
  3. Altre funzioni. Ad esempio:  FunctionOne(`<<argument1>>`, FunctionTwo(`<<argument2>>`))
* Eventuali barre rovesciate ( \ ) o virgolette ( " ) da inserire nella costante di stringa dovranno essere precedute dal simbolo di barra rovesciata ( \ ) come carattere di escape. Ad esempio:  "Nome società: \\"Contoso\\""

## <a name="list-of-functions"></a>Elenco di funzioni
| Elenco di funzioni | DESCRIZIONE |
|-----|----|
|[Append](#append)|Accetta un valore di stringa di origine e aggiunge un suffisso alla fine del valore.|
|[BitAnd](#bitand)|La funzione BitAnd imposta i bit specificati su un valore.|
|[CBool](#cbool)|La funzione CBool restituisce un valore booleano basato sull'espressione valutata.|
|[ConvertFromBase64](#convertfrombase64)|La funzione ConvertFromBase64 converte il valore con codifica Base 64 specificato in una stringa normale.|
|[ConvertToBase64](#converttobase64)|La funzione ConvertToBase64 converte una stringa in una stringa Base 64 Unicode. |
|[ConvertToUTF8Hex](#converttoutf8hex)|La funzione ConvertToUTF8Hex converte una stringa in un valore con codifica esadecimale UTF8.|
|[Numero](#count)|La funzione Count restituisce il numero di elementi in un attributo multivalore.|
|[Cstr](#cstr)|La funzione CStr esegue la conversione in un tipo di dati stringa.|
|[DateFromNum](#datefromnum)|La funzione DateFromNum converte un valore nel formato di data di Active Directory in un tipo di data/ora.|
|[DNComponent](#dncomponent)|La funzione DNComponent restituisce il valore di un componente DN specificato, a partire da sinistra.|
|[Error (Errore) (Error (Errore)e)](#error)|La funzione Error viene usata per restituire un errore personalizzato.|
|[FormatDateTime](#formatdatetime) |Accetta una stringa data in un formato e la converte in un formato diverso.| 
|[GUID](#guid)|La funzione Guid genera un nuovo GUID casuale.|           
|[IIF](#iif)|La funzione IIF restituisce uno dei possibili valori di un set, in base a una condizione specificata.|
|[InStr](#instr)|La funzione InStr trova la prima occorrenza di una sottostringa in una stringa.|
|[IsNull](#isnull)|Se l'espressione restituisce Null, la funzione IsNull restituisce True.|
|[IsNullOrEmpty](#isnullorempty)|Se l'espressione è Null o una stringa vuota, la funzione IsNullOrEmpty restituisce True.|         
|[IsPresent](#ispresent)|Se l'espressione restituisce una stringa non Null e non vuota, la funzione IsPresent restituisce True.|    
|[IsString](#isstring)|Se l'espressione può essere valutata come tipo stringa, la funzione IsString restituisce True.|
|[Elemento](#item)|La funzione Item restituisce un elemento da una stringa o un attributo multivalore.|
|[Join](#join) |Join() è simile ad Append(), ma può combinare più valori di stringa **source** in un singola stringa e ogni valore sarà separato da una stringa **separator**.| 
|[Left](#left)|La funzione Left restituisce un numero di caratteri specificato a partire da sinistra di una stringa.|
|[Mid](#mid) |Restituisce una sottostringa del valore source. Una sottostringa è una stringa che contiene solo alcuni caratteri della stringa di origine.|
|[NormalizeDiacritics](#normalizediacritics)|Richiede un argomento stringa. Restituisce la stringa, ma con i caratteri diacritici sostituiti dagli equivalenti caratteri non diacritici.|
|[Not](#not) |Inverte il valore booleano di **source**. Se il valore di **source** è "*True*", restituisce "*False*". In caso contrario, restituisce "*True*".| 
|[RemoveDuplicates](#removeduplicates)|La funzione RemoveDuplicates accetta una stringa multivalore e verifica che ogni valore sia univoco.| 
|[Replace](#replace) |Sostituisce i valori all'interno di una stringa. | 
|[SelectUniqueValue](#selectuniquevalue)|Richiede almeno due argomenti, costituiti da regole di generazione di valori univoci definite tramite espressioni. La funzione valuta ogni regola e controlla quindi il valore generato per verificarne l'univocità nella directory/app di destinazione.| 
|[SingleAppRoleAssignment](#singleapproleassignment)|Restituisce un singolo oggetto appRoleAssignment dall'elenco di tutti gli oggetti appRoleAssignments assegnati a un utente per una determinata applicazione.| 
|[Split](#split)|Divide una stringa in una matrice multi-valore usando il carattere di delimitazione specificato.|
|[StringFromSID](#stringfromsid)|La funzione StringFromSid converte una matrice di byte contenente un ID di sicurezza in una stringa.| 
|[StripSpaces](#stripspaces) |Rimuove tutti i caratteri di spazio (" ") dalla stringa di origine.| 
|[Switch](#switch)|Quando il valore **source** corrisponde a **key**, verrà restituito un parametro **value** per tale oggetto **key**. | 
|[ToLower](#tolower)|Prende un valore della stringa di *origine* e lo converte in caratteri minuscoli usando le regole delle impostazioni cultura specificate.| 
|[ToUpper](#toupper)|Prende un valore della stringa di *origine* e lo converte in caratteri maiuscoli usando le regole delle impostazioni cultura specificate.|
|[Trim](#trim)|La funzione Trim rimuove gli spazi vuoti iniziali e finali da una stringa.|
|[Word](#word)|La funzione Word restituisce una parola contenuta in una stringa, in base ai parametri che descrivono i delimitatori da usare e il numero della parola da restituire.|

---
### <a name="append"></a>Append
**Funzione:**<br> Append(source, suffix)

**Descrizione:**<br> Accetta un valore di stringa di origine e aggiunge un suffisso alla fine del valore.

**Parametri:**<br> 

   | NOME | Obbligatorio/Ripetuto | Type | Note |
   | --- | --- | --- | --- |
   | **source** |Obbligatoria |string |In genere è il nome dell'attributo dell'oggetto di origine. |
   | **suffix** |Obbligatoria |string |Stringa da aggiungere alla fine del valore di origine. |

---
### <a name="bitand"></a>BitAnd
**Descrizione:**  
La funzione BitAnd imposta i bit specificati su un valore.

**Sintassi:**  
`num BitAnd(num value1, num value2)`

* value1, value2: valori numerici da unire con AND

**Osservazioni:**  
Questa funzione converte entrambi i parametri nella rappresentazione binaria e imposta un bit su:

* 0: se il valore di uno o entrambi i bit corrispondenti in *value1* e *value2* è 0
* 1: se entrambi i bit corrispondenti sono pari a 1.

In altre parole, restituisce 0 in tutti i casi tranne quando i bit corrispondenti di entrambi i parametri sono pari a 1.

**Esempio:**  
 
 `BitAnd(&HF, &HF7)`</br>
 Restituisce 7 perché i valori esadecimali "F" E "F7" restituiscono questo valore.

---

### <a name="cbool"></a>CBool
**Descrizione:**  
La funzione CBool restituisce un valore booleano basato sull'espressione valutata.

**Sintassi:**  
`bool CBool(exp Expression)`

**Osservazioni:**  
Se l'espressione restituisce un valore diverso da zero, CBool restituisce True. In caso contrario, restituisce False.

**Esempio:**  
`CBool([attrib1] = [attrib2])`  

Restituisce True se entrambi gli attributi hanno lo stesso valore.

---
### <a name="convertfrombase64"></a>ConvertFromBase64
**Descrizione:**  
La funzione ConvertFromBase64 converte il valore con codifica Base 64 specificato in una stringa normale.

**Sintassi:**  
`str ConvertFromBase64(str source)`: presuppone l'uso di Unicode per la codifica  
`str ConvertFromBase64(str source, enum Encoding)`

* source: Stringa con codifica Base64  
* Encoding: Unicode, ASCII, UTF8

**Esempio**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Entrambi gli esempi restituiscono "*Hello world!* "

---
### <a name="converttobase64"></a>ConvertToBase64
**Descrizione:**  
La funzione ConvertToBase64 converte una stringa in una stringa Base 64 Unicode.  
Converte il valore di una matrice di interi nella rappresentazione di stringa equivalente in cifre con codifica Base 64.

**Sintassi:**  
`str ConvertToBase64(str source)`

**Esempio:**  
`ConvertToBase64("Hello world!")`  
Restituisce "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Descrizione:**  
La funzione ConvertToUTF8Hex converte una stringa in un valore con codifica esadecimale UTF8.

**Sintassi:**  
`str ConvertToUTF8Hex(str source)`

**Osservazioni:**  
Il formato di output di questa funzione viene usato da Azure Active Directory come formato dell'attributo DN.

**Esempio:**  
`ConvertToUTF8Hex("Hello world!")`  
Restituisce 48656C6C6F20776F726C6421

---
### <a name="count"></a>Conteggio
**Descrizione:**  
La funzione Count restituisce il numero di elementi in un attributo multivalore.

**Sintassi:**  
`num Count(mvstr attribute)`

---
### <a name="cstr"></a>CStr
**Descrizione:**  
La funzione CStr esegue la conversione in un tipo di dati stringa.

**Sintassi:**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

* value: può essere un valore numerico, un attributo di riferimento o un valore booleano.

**Esempio:**  
`CStr([dn])`  
Può restituire "cn=Joe,dc=contoso,dc=com"

---
### <a name="datefromnum"></a>DateFromNum
**Descrizione:**  
La funzione DateFromNum converte un valore nel formato di data di Active Directory in un tipo di data/ora.

**Sintassi:**  
`dt DateFromNum(num value)`

**Esempio:**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
Restituisce un valore di data/ora che rappresenta 2012-01-01 23:00:00

---
### <a name="dncomponent"></a>DNComponent
**Descrizione:**  
La funzione DNComponent restituisce il valore di un componente DN specificato, a partire da sinistra.

**Sintassi:**  
`str DNComponent(ref dn, num ComponentNumber)`

* dn: attributo di riferimento da interpretare
* ComponentNumber: componente nel DN da restituire

**Esempio:**  
`DNComponent(CRef([dn]),1)`  
Se dn è "cn=Joe,ou=…," verrà restituito Joe

---
### <a name="error"></a>Tipi di errore
**Descrizione:**  
La funzione Error viene usata per restituire un errore personalizzato.

**Sintassi:**  
`void Error(str ErrorMessage)`

**Esempio:**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
Se l'attributo accountName non è presente, viene generato un errore nell'oggetto.

---
### <a name="formatdatetime"></a>FormatDateTime
**Funzione:**<br> FormatDateTime(source, inputFormat, outputFormat)

**Descrizione:**<br> Accetta una stringa data in un formato e la converte in un formato diverso.

**Parametri:**<br> 

   | NOME | Obbligatorio/Ripetuto | Type | Note |
   | --- | --- | --- | --- |
   | **source** |Obbligatoria |string |In genere è il nome dell'attributo dell'oggetto di origine. |
   | **inputFormat** |Obbligatoria |string |Formato previsto del valore source. Per informazioni sui formati supportati, vedere [https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx). |
   | **outputFormat** |Obbligatoria |string |Formato della data di output. |

---
### <a name="guid"></a>Guid
**Descrizione:**  
La funzione Guid genera un nuovo GUID casuale

**Sintassi:**  
`str Guid()`

---
### <a name="iif"></a>IIF
**Descrizione:**  
La funzione IIF restituisce uno dei possibili valori di un set, in base a una condizione specificata.

**Sintassi:**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

* condition: qualsiasi valore o espressione che possa restituire true o false.
* valueIfTrue: se la condizione restituisce true, il valore restituito.
* valueIfFalse: se la condizione restituisce false, il valore restituito.

**Esempio:**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 Se l'utente è uno stagista, restituisce l'alias di un utente, aggiungendo "t-" all'inizio. In caso contrario restituisce l'alias dell'utente invariato.

---
### <a name="instr"></a>InStr
**Descrizione:**  
La funzione InStr trova la prima occorrenza di una sottostringa in una stringa.

**Sintassi:**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start , enum compare)`

* stringcheck: stringa da cercare
* stringmatch: stringa da trovare
* start: posizione iniziale per la ricerca della sottostringa
* compare: vbTextCompare o vbBinaryCompare

**Osservazioni:**  
Restituisce la posizione in cui è stata trovata la sottostringa o 0 se non viene trovata.

**Esempio:**  
`InStr("The quick brown fox","quick")`  
Restituisce 5

`InStr("repEated","e",3,vbBinaryCompare)`  
Restituisce 7

---
### <a name="isnull"></a>IsNull
**Descrizione:**  
Se l'espressione restituisce Null, la funzione IsNull restituisce True.

**Sintassi:**  
`bool IsNull(var Expression)`

**Osservazioni:**  
Per un attributo, Null è espresso dall'assenza dell'attributo.

**Esempio:**  
`IsNull([displayName])`  
Restituisce True se l'attributo non è presente in CS o MV.

---
### <a name="isnullorempty"></a>IsNullOrEmpty
**Descrizione:**  
Se l'espressione è Null o una stringa vuota, la funzione IsNullOrEmpty restituisce True.

**Sintassi:**  
`bool IsNullOrEmpty(var Expression)`

**Osservazioni:**  
Per un attributo, verrà restituito True se l'attributo è assente oppure se è presente, ma è una stringa vuota.  
La funzione inversa di questa funzione è denominata IsPresent.

**Esempio:**  
`IsNullOrEmpty([displayName])`  
Restituisce True se l'attributo non è presente oppure è una stringa vuota in CS o MV.

---
### <a name="ispresent"></a>IsPresent
**Descrizione:**  
Se l'espressione restituisce una stringa non Null e non vuota, la funzione IsPresent restituisce True.

**Sintassi:**  
`bool IsPresent(var expression)`

**Osservazioni:**  
La funzione inversa di questa funzione è denominata IsNullOrEmpty.

**Esempio:**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="item"></a>Elemento
**Descrizione:**  
La funzione Item restituisce un elemento da una stringa o un attributo multivalore.

**Sintassi:**  
`var Item(mvstr attribute, num index)`

* attribute: attributo multivalore
* index: indice di un elemento nella stringa multivalore.

**Osservazioni:**  
La funzione Item usata con la funzione Contains è utile, perché quest'ultima restituisce l'indice di un elemento nell'attributo multivalore.

Genera un errore se l'indice non è compreso nell'intervallo.

**Esempio:**  
`Mid(Item([proxyAddresses],Contains([proxyAddresses], "SMTP:")),6)`  
Restituisce l'indirizzo di posta elettronica primario.

---
### <a name="isstring"></a>IsString
**Descrizione:**  
Se l'espressione può essere valutata come tipo stringa, la funzione IsString restituisce True.

**Sintassi:**  
`bool IsString(var expression)`

**Osservazioni:**  
Usata per determinare se CStr() riuscirà ad analizzare l'espressione.

---
### <a name="join"></a>Join
**Funzione:**<br> Join(separator, source1, source2, …)

**Descrizione:**<br> Join() è simile ad Append(), ma può combinare più valori di stringa **source** in un singola stringa e ogni valore sarà separato da una stringa **separator**.

Se uno dei valori di origine è un attributo multivalore, verranno uniti tutti i valori dell'attributo, separati dal valore separatore.

**Parametri:**<br> 

   | NOME | Obbligatorio/Ripetuto | Type | Note |
   | --- | --- | --- | --- |
   | **separator** |Obbligatoria |string |Stringa usata per separare i valori di origine quando sono concatenati in una stringa. Può essere "" se non sono necessari separatori. |
   | **source1 … sourceN** |Obbligatorio per un numero variabile di volte |string |Valori stringa da unire. |

---
### <a name="left"></a>Left
**Descrizione:**  
La funzione Left restituisce un numero di caratteri specificato a partire da sinistra di una stringa.

**Sintassi:**  
`str Left(str string, num NumChars)`

* string: stringa dalla quale restituire i caratteri
* NumChars: numero che identifica il numero di caratteri da restituire dall'inizio (sinistra) della stringa

**Osservazioni:**  
Una stringa contenente i primi caratteri numChars della stringa:

* Se numChars = 0, restituisce una stringa vuota.
* Se numChars < 0, restituisce una stringa di input.
* Se string è Null, restituisce una stringa vuota.

Se string contiene un numero di caratteri inferiore al numero specificato in numChars, viene restituita una stringa identica a string (ovvero contenente tutti i caratteri nel parametro 1).

**Esempio:**  
`Left("John Doe", 3)`  
Restituisce `Joh`.

---
### <a name="mid"></a>Mid
**Funzione:**<br> Mid(source, start, length)

**Descrizione:**<br> Restituisce una sottostringa del valore source. Una sottostringa è una stringa che contiene solo alcuni caratteri della stringa di origine.

**Parametri:**<br> 

   | NOME | Obbligatorio/Ripetuto | Type | Note |
   | --- | --- | --- | --- |
   | **source** |Obbligatoria |string |Corrisponde in genere al nome dell'attributo. |
   | **start** |Obbligatoria |numero intero |Indice nella stringa **source** che indica il punto di inizio della sottostringa. L'indice del primo carattere della stringa sarà pari a 1, quello del secondo carattere a 2 e così via. |
   | **length** |Obbligatoria |numero intero |Lunghezza della sottostringa. Se la lunghezza eccede la stringa **source**, la funzione restituirà una sottostringa dall'indice **start** fino alla fine della stringa **source**. |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Funzione:**<br> NormalizeDiacritics(source)

**Descrizione:**<br> Richiede un argomento stringa. Restituisce la stringa, ma con i caratteri diacritici sostituiti dagli equivalenti caratteri non diacritici. Viene in genere usata per convertire i nomi e i cognomi contenenti caratteri diacritici (accenti) in valori validi che possono essere usati in vari ID utente, ad esempio nomi dell'entità utente, nomi di account SAM e indirizzi di posta elettronica.

**Parametri:**<br> 

   | NOME | Obbligatorio/Ripetuto | Type | Note |
   | --- | --- | --- | --- |
   | **source** |Obbligatoria |string | In genere un attributo nome o cognome. |

---
### <a name="not"></a>not
**Funzione:**<br> Not(source)

**Descrizione:**<br> Inverte il valore booleano di **source**. Se il valore di **source** è "*True*", restituisce "*False*". In caso contrario, restituisce "*True*".

**Parametri:**<br> 

   | NOME | Obbligatorio/Ripetuto | Type | Note |
   | --- | --- | --- | --- |
   | **source** |Obbligatoria |Stringa booleana |I valori previsti per **source** sono "True" o "False". |

---
### <a name="removeduplicates"></a>RemoveDuplicates
**Descrizione:**  
La funzione RemoveDuplicates accetta una stringa multivalore e verifica che ogni valore sia univoco.

**Sintassi:**  
`mvstr RemoveDuplicates(mvstr attribute)`

**Esempio:**  
`RemoveDuplicates([proxyAddresses])`  
Restituisce un attributo proxyAddress purificato in cui sono stati rimossi tutti i valori duplicati.

---
### <a name="replace"></a>Replace
**Funzione:**<br> Replace(source, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, template)

**Descrizione:**<br>
Sostituisce i valori all'interno di una stringa. Funziona in modo diverso a seconda dei parametri forniti:

* Se vengono forniti **oldValue** e **replacementValue**:
  
  * Sostituisce tutte le occorrenze di **oldValue** in **source** con **replacementValue**
* Se vengono forniti **oldValue** e **template**:
  
  * Sostituisce tutte le occorrenze di **oldValue** in **template** con il valore **source**
* Se vengono forniti **regexPattern** e **replacementValue**:

  * La funzione applica **regexPattern** alla stringa **source** ed è possibile usare i nomi del gruppo di regex per creare la stringa per **replacementValue**
* Se vengono forniti **regexPattern**, **regexGroupName** e **replacementValue**:
  
  * La funzione applica **regexPattern** alla stringa **source** e sostituisce tutti i valori che corrispondono a **regexGroupName** con **replacementValue**
* Se vengono forniti **regexPattern**, **regexGroupName** e **replacementAttributeName**:
  
  * Se **source** non ha alcun valore, viene restituito **source**
  * Se **source** include un valore, la funzione applica **regexPattern** alla stringa **source** e sostituisce tutti i valori corrispondenti a **regexGroupName** con il valore associato a **replacementAttributeName**

**Parametri:**<br> 

   | NOME | Obbligatorio/Ripetuto | Type | Note |
   | --- | --- | --- | --- |
   | **source** |Obbligatoria |string |In genere è il nome dell'attributo dell'oggetto **source**. |
   | **oldValue** |Facoltativo |string |Valore da sostituire in **source** o **template**. |
   | **regexPattern** |Facoltativo |string |Criterio di espressione regolare per il valore da sostituire in **source**. Se invece si usa **replacementPropertyName**, corrisponde al modello usato per estrarre il valore da **replacementPropertyName**. |
   | **regexGroupName** |Facoltativo |string |Nome del gruppo in **regexPattern**. Solo se si usa **replacementPropertyName**, il valore di questo gruppo verrà estratto come **replacementValue** da **replacementPropertyName**. |
   | **replacementValue** |Facoltativo |string |Nuovo valore con cui sostituire il precedente. |
   | **replacementAttributeName** |Facoltativo |string |Nome dell'attributo da usare per il valore di sostituzione |
   | **template** |Facoltativo |string |Se viene fornito il valore **template**, il valore **oldValue** verrà cercato in template e sostituito con il valore di **source**. |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**Funzione:**<br> SelectUniqueValue(uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, …)

**Descrizione:**<br> Richiede almeno due argomenti, costituiti da regole di generazione di valori univoci definite tramite espressioni. La funzione valuta ogni regola e controlla quindi il valore generato per verificarne l'univocità nella directory/app di destinazione. Il primo valore univoco trovato sarà quello restituito. Se tutti i valori trovati sono già presenti nella destinazione, la voce verrà depositata e il motivo verrà registrato nei log di controllo. Non è previsto alcun limite relativamente al numero di argomenti che è possibile specificare.

> [!NOTE]
> - Si tratta di una funzione di primo livello che non può essere annidata.
> - Questa funzione non può essere applicata agli attributi con precedenza abbinamento.  
> - Questa funzione è destinata a essere usata solo per la creazione di voci. Se viene usata con un attributo, impostare la proprietà **Applica questo mapping** su **Solo durante la creazione dell'oggetto**.
> - Questa funzione è attualmente supportata solo per il "provisioning utenti da Workday ad Active Directory". Non può essere usata con altre applicazioni di provisioning. 


**Parametri:**<br> 

   | NOME | Obbligatorio/Ripetuto | Type | Note |
   | --- | --- | --- | --- |
   | **uniqueValueRule1  … uniqueValueRuleN** |Sono necessari almeno 2 argomenti, nessun limite superiore |string | Elenco delle regole di generazione di valori univoci da valutare. |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Funzione:**<br> SingleAppRoleAssignment([appRoleAssignments])

**Descrizione:**<br> Restituisce un singolo oggetto appRoleAssignment dall'elenco di tutti gli oggetti appRoleAssignments assegnati a un utente per una determinata applicazione. Questa funzione è necessaria per convertire l'oggetto appRoleAssignments in una singola stringa relativa al nome di un ruolo. Si noti che la procedura consigliata è quella di assicurarsi che un solo oggetto appRoleAssignment venga assegnato a un solo utente alla volta. Se vengono assegnati più ruoli, la stringa di ruolo restituita potrebbe non essere prevedibile. 

**Parametri:**<br> 

  | NOME | Obbligatorio/Ripetuto | Type | Note |
  |--- | --- | --- | --- |
  | **[appRoleAssignments]** |Obbligatoria |string |Oggetto **[appRoleAssignments]** . |

---
### <a name="split"></a>Split
**Funzione:**<br> Split(source, delimiter)

**Descrizione:**<br> Divide una stringa in una matrice multi-valore usando il carattere di delimitazione specificato.

**Parametri:**<br> 

   | NOME | Obbligatorio/Ripetuto | Type | Note |
   | --- | --- | --- | --- |
   | **source** |Obbligatoria |string |**source** da aggiornare. |
   | **delimiter** |Obbligatoria |string |Specifica il carattere che verrà usato per dividere la stringa (esempio: ",") |

---
### <a name="stringfromsid"></a>StringFromSid
**Descrizione:**  
La funzione StringFromSid converte una matrice di byte contenente un ID di sicurezza in una stringa.

**Sintassi:**  
`str StringFromSid(bin ObjectSID)`  

---
### <a name="stripspaces"></a>StripSpaces
**Funzione:**<br> StripSpaces(source)

**Descrizione:**<br> Rimuove tutti i caratteri di spazio (" ") dalla stringa di origine.

**Parametri:**<br> 

   | NOME | Obbligatorio/Ripetuto | Type | Note |
   | --- | --- | --- | --- |
   | **source** |Obbligatoria |string |**source** da aggiornare. |

---
### <a name="switch"></a>Switch
**Funzione:**<br> Switch(source, defaultValue, key1, value1, key2, value2, …)

**Descrizione:**<br> Quando il valore **source** corrisponde a **key**, verrà restituito un parametro **value** per tale oggetto **key**. Se il valore del parametro **source** non corrisponde ad alcuna chiave, verrà restituito **defaultValue**.  I parametri **key** e **value** devono essere sempre accoppiati. Le funzioni prevedono sempre un numero pari di parametri.

**Parametri:**<br> 

   | NOME | Obbligatorio/Ripetuto | Type | Note |
   | --- | --- | --- | --- |
   | **source** |Obbligatoria |string |**Source** da aggiornare. |
   | **defaultValue** |Facoltativo |string |Valore predefinito da usare se l'origine non corrisponde ad alcuna chiave. Può essere una stringa vuota (""). |
   | **key** |Obbligatoria |string |Parametro **key** con cui confrontare il valore di **source**. |
   | **value** |Obbligatoria |string |Valore di sostituzione per il valore **source** corrispondente al parametro key. |

---
### <a name="tolower"></a>ToLower
**Funzione:**<br> ToLower (origine, impostazioni cultura)

**Descrizione:**<br> Prende un valore della stringa di *origine* e lo converte in caratteri minuscoli usando le regole delle impostazioni cultura specificate. Se non sono specificate informazioni per le *impostazioni cultura*, verranno usate le impostazioni cultura inglese non dipendenti da paese/area geografica.

**Parametri:**<br> 

   | NOME | Obbligatorio/Ripetuto | Type | Note |
   | --- | --- | --- | --- |
   | **source** |Obbligatoria |string |In genere è il nome dell'attributo dell'oggetto di origine. |
   | **Impostazioni cultura** |Facoltativo |string |Il formato per il nome delle impostazioni cultura basato su RFC 4646 è *languagecode2-country/regioncode2*, in cui *languagecode2* è il codice lingua a due lettere e *country/regioncode2* è il codice di impostazioni cultura secondarie a due lettere. Tra gli esempi sono inclusi ja-JP per Giapponese (Giappone) ed en-US per Inglese (Stati Uniti). Nei casi in cui non è disponibile un codice lingua a due lettere, viene usato un codice a tre lettere derivato da ISO 639-2.|

---

### <a name="toupper"></a>ToUpper
**Funzione:**<br> ToUpper (origine, impostazioni cultura)

**Descrizione:**<br> Prende un valore della stringa di *origine* e lo converte in caratteri maiuscoli usando le regole delle impostazioni cultura specificate. Se non sono specificate informazioni per le *impostazioni cultura*, verranno usate le impostazioni cultura inglese non dipendenti da paese/area geografica.

**Parametri:**<br> 

  | NOME | Obbligatorio/Ripetuto | Type | Note |
  | --- | --- | --- | --- |
  | **source** |Obbligatoria |string |In genere è il nome dell'attributo dell'oggetto di origine. |
  | **Impostazioni cultura** |Facoltativo |string |Il formato per il nome delle impostazioni cultura basato su RFC 4646 è *languagecode2-country/regioncode2*, in cui *languagecode2* è il codice lingua a due lettere e *country/regioncode2* è il codice di impostazioni cultura secondarie a due lettere. Tra gli esempi sono inclusi ja-JP per Giapponese (Giappone) ed en-US per Inglese (Stati Uniti). Nei casi in cui non è disponibile un codice lingua a due lettere, viene usato un codice a tre lettere derivato da ISO 639-2.|

---

### <a name="trim"></a>Trim
**Descrizione:**  
La funzione Trim rimuove gli spazi vuoti iniziali e finali da una stringa.

**Sintassi:**  
`str Trim(str value)`  

**Esempio:**  
`Trim(" Test ")`  
Restituisce "test".

`Trim([proxyAddresses])`  
Rimuove gli spazi iniziali e finali per ogni valore nell'attributo proxyAddress.

---
### <a name="word"></a>Word
**Descrizione:**  
La funzione Word restituisce una parola contenuta in una stringa, in base ai parametri che descrivono i delimitatori da usare e il numero della parola da restituire.

**Sintassi:**  
`str Word(str string, num WordNumber, str delimiters)`

* string: stringa dalla quale restituire una parola.
* WordNumber: numero che identifica il numero di parole da restituire.
* delimiters: stringa che rappresenta uno o più delimitatori da usare per identificare le parole

**Osservazioni:**  
Ogni stringa di caratteri contenuta nella stringa con valori separati da uno dei caratteri specificati nei delimitatori viene identificata come una parola:

* Se number è < 1, restituisce una stringa vuota.
* Se string è Null, restituisce una stringa vuota.

Se la stringa contiene meno delle parole specificate in number o se non contiene alcuna parola identificata da delimiters, viene restituita una stringa vuota.

**Esempio:**  
`Word("The quick brown fox",3," ")`  
Restituisce "brown"

`Word("This,string!has&many separators",3,",!&#")`  
Restituisce "has"

## <a name="examples"></a>Esempi
### <a name="strip-known-domain-name"></a>Rimuovere un nome di dominio noto
Occorre rimuovere un nome di dominio noto dall'indirizzo di posta elettronica di un utente per ottenere il nome utente. <br>
Ad esempio, se il dominio è "contoso.com", è possibile usare l'espressione seguente:

**Espressione:** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**Input/output di esempio:** <br>

* **INPUT** (mail): "john.doe@contoso.com"
* **OUTPUT**: "john.doe"

### <a name="append-constant-suffix-to-user-name"></a>Aggiungere un suffisso costante al nome utente
Se si usa un ambiente sandbox Salesforce, potrebbe essere necessario aggiungere un suffisso a tutti i nomi utente prima di sincronizzarli.

**Espressione:** <br>
`Append([userPrincipalName], ".test")`

**Input/output di esempio:** <br>

* **INPUT** (userPrincipalName): "John.Doe@contoso.com"
* **OUTPUT**: "John.Doe@contoso.com.test"

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Generare un alias utente concatenando parti del nome e del cognome
Occorre generare un alias utente contenente le prime tre lettere del nome e le prime cinque lettere del cognome dell'utente.

**Espressione:** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Input/output di esempio:** <br>

* **INPUT** (givenName): "John"
* **INPUT** (surname): "Doe"
* **OUTPUT**:  "JohDoe"

### <a name="remove-diacritics-from-a-string"></a>Rimuovere i segni diacritici da una stringa
È necessario sostituire i caratteri contenenti accenti con gli equivalenti caratteri non contenenti accenti.

**Espressione:** <br>
NormalizeDiacritics([givenName])

**Input/output di esempio:** <br>

* **INPUT** (givenName): "Zoë"
* **OUTPUT**:  "Zoe"

### <a name="split-a-string-into-a-multi-valued-array"></a>Dividere una stringa in una matrice multivalore
È necessario partire da un elenco di stringhe delimitate da virgole e dividerlo in una matrice che possa essere inserita in un attributo multivalore come l'attributo PermissionSets di Salesforce. In questo esempio un elenco di set di autorizzazioni è stato popolato in extensionAttribute5 in Azure AD.

**Espressione:** <br>
Split([extensionAttribute5], ",")

**Input/output di esempio:** <br>

* **INPUT** (extensionAttribute5): "PermissionSetOne, PermisionSetTwo"
* **OUTPUT**: ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>Eseguire l'output della data come stringa in un formato specifico
Occorre inviare date a un'applicazione SaaS in un formato specifico, <br>
Ad esempio, formattare le date per ServiceNow.

**Espressione:** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Input/output di esempio:**

* **INPUT** (extensionAttribute1): "20150123105347.1Z"
* **OUTPUT**:  "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Sostituire un valore in base a un set di opzioni predefinito

È necessario definire il fuso orario dell'utente in base al codice di stato archiviato in Azure AD. <br>
Se il codice di stato non corrisponde ad alcuna opzione predefinita, usare il valore predefinito "Australia/Sydney".

**Espressione:** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Input/output di esempio:**

* **INPUT** (state): "QLD"
* **OUTPUT**: "Australia/Brisbane"

### <a name="replace-characters-using-a-regular-expression"></a>Sostituire caratteri usando un'espressione regolare
È necessario trovare i caratteri che corrispondono al valore di un'espressione regolare e rimuoverli.

**Espressione:** <br>

Replace([mailNickname], , "[a-zA-Z_]*", , "", , )

**Input/output di esempio:**

* **INPUT** (mailNickname: "john_doe72"
* **OUTPUT**: "72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>Converte il valore userPrincipalName (UPN) generato in caratteri minuscoli
Nell'esempio seguente il valore UPN viene generato concatenando i campi di origine PreferredFirstName e PreferredLastName e la funzione ToLower viene usata con la stringa generata per convertire tutti i caratteri in minuscolo. 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**Input/output di esempio:**

* **INPUT** (PreferredFirstName): "John"
* **INPUT** (PreferredLastName): "Smith"
* **OUTPUT**: "john.smith@contoso.com"

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>Generare un valore univoco per l'attributo userPrincipalName (UPN)
In base al nome, al secondo nome e al cognome dell'utente, è necessario generare un valore per l'attributo UPN e verificarne l'univocità nella directory di AD di destinazione prima di assegnare il valore all'attributo UPN.

**Espressione:** <br>

    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )

**Input/output di esempio:**

* **INPUT** (PreferredFirstName): "John"
* **INPUT** (PreferredLastName): "Smith"
* **OUTPUT**: "John.Smith@contoso.com" se il valore UPN di John.Smith@contoso.com non esiste già nella directory
* **OUTPUT**: "J.Smith@contoso.com" se il valore UPN di John.Smith@contoso.com esiste già nella directory
* **OUTPUT**: "Jo.Smith@contoso.com" se i due valori UPN precedenti esistono già nella directory


## <a name="next-steps"></a>Passaggi successivi 

- [Cos'è il provisioning?](what-is-provisioning.md)
- [Che cos'è il provisioning cloud di Azure AD Connect?](what-is-cloud-provisioning.md)
