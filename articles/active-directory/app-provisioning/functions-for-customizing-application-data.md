---
title: Scrivere espressioni per i mapping degli attributi in Azure Active Directory
description: Informazioni su come usare i mapping di espressioni per trasformare i valori degli attributi in un formato accettabile durante il provisioning automatizzato di oggetti SaaS in Azure Active Directory.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 02/05/2020
ms.author: kenwith
ms.openlocfilehash: d454ab3ad382c6237ab9f8c215473801285ba3c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88235673"
---
# <a name="how-to-write-expressions-for-attribute-mappings-in-azure-ad"></a>Procedura: scrivere espressioni per i mapping degli attributi in Azure AD

Quando si configura il provisioning in un'applicazione SaaS, come mapping degli attributi è possibile specificare il mapping di espressioni. Per questo tipo di mapping è necessario scrivere un'espressione analoga a uno script, che permette di trasformare i dati utente in formati più idonei all'applicazione SaaS.

## <a name="syntax-overview"></a>Panoramica della sintassi

La sintassi per le espressioni per i mapping degli attributi è simile a quella delle funzioni di Visual Basic for Applications (VBA).

* L'intera espressione deve essere definita in termini di funzioni, che sono costituite da un nome seguito da argomenti racchiusi tra parentesi:  <br>
  *FunctionName ( `<<argument 1>>` , `<<argument N>>` )*
* È possibile annidare le funzioni in altre funzioni. Ad esempio: <br> *Funzioneuno (Funzionedue ( `<<argument1>>` ))*
* È possibile passare tre tipi diversi di argomenti nelle funzioni:
  
  1. Attributi, che devono essere racchiusi tra parentesi quadre. Ad esempio: [NomeAttributo]
  2. Costanti di stringa, che devono essere racchiuse tra virgolette doppie. Ad esempio: "Stati Uniti"
  3. Altre funzioni. Ad esempio: Funzioneuno ( `<<argument1>>` , funzionedue ( `<<argument2>>` ))
* Eventuali barre rovesciate ( \ ) o virgolette ( " ) da inserire nella costante di stringa dovranno essere precedute dal simbolo di barra rovesciata ( \ ) come carattere di escape. Ad esempio: "nome società: \\ " Contoso \\ ""
* La sintassi fa distinzione tra maiuscole e minuscole, che deve essere presa in considerazione durante la digitazione come stringhe in una funzione rispetto a copia, incollando tali stringhe direttamente da qui. 

## <a name="list-of-functions"></a>Elenco di funzioni

[Append](#append) &nbsp; &nbsp; Accoda &nbsp; &nbsp; [BitAnd](#bitand) &nbsp; &nbsp; BitAnd &nbsp; &nbsp; [CBool](#cbool) &nbsp; &nbsp; CBool &nbsp; &nbsp; [COALESCE](#coalesce) &nbsp; &nbsp; &nbsp; &nbsp; [ConvertToBase64](#converttobase64) &nbsp; &nbsp; ConvertToBase64 &nbsp; &nbsp; [ConvertToUTF8Hex](#converttoutf8hex) &nbsp; &nbsp; ConvertToUTF8Hex &nbsp; &nbsp; [Numero](#count) &nbsp; &nbsp; di &nbsp; &nbsp; [CStr](#cstr) &nbsp; &nbsp; CStr &nbsp; &nbsp; [DateFromNum](#datefromnum) &nbsp; [FormatDateTime](#formatdatetime) &nbsp; &nbsp; FormatDateTime &nbsp; &nbsp; [Guid](#guid) &nbsp; &nbsp; GUID &nbsp; &nbsp; [IIF](#iif) &nbsp; &nbsp; IIf &nbsp; &nbsp; [InStr](#instr) &nbsp; &nbsp; InStr &nbsp; &nbsp; [IsNull](#isnull) &nbsp; &nbsp; IsNull &nbsp; &nbsp; [IsNullOrEmpty](#isnullorempty) &nbsp; &nbsp; IsNullOrEmpty &nbsp; &nbsp; [Presenza](#ispresent) &nbsp; &nbsp; di &nbsp; &nbsp; [Stringa](#isstring) &nbsp; &nbsp; di &nbsp; &nbsp; [Elemento](#item) &nbsp; &nbsp; di &nbsp; &nbsp; [Aggiungi](#join) &nbsp; &nbsp; a &nbsp; &nbsp; [Left](#left) &nbsp; &nbsp; A &nbsp; sinistra &nbsp; [Mid](#mid) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [NormalizeDiacritics](#normalizediacritics) [not](#not) &nbsp; &nbsp; &nbsp; &nbsp; [RemoveDuplicates](#removeduplicates) &nbsp; &nbsp; &nbsp; &nbsp; [Replace](#replace) &nbsp; &nbsp; &nbsp; &nbsp; [SelectUniqueValue](#selectuniquevalue) &nbsp; &nbsp; &nbsp; &nbsp; [SingleAppRoleAssignment](#singleapproleassignment) &nbsp; &nbsp; &nbsp; &nbsp; [Split](#split) &nbsp; &nbsp; &nbsp; &nbsp; [StripSpaces](#stripspaces) &nbsp; &nbsp; &nbsp; &nbsp; [Switch](#switch) &nbsp; &nbsp; &nbsp; &nbsp; [ToLower](#tolower) &nbsp; &nbsp; &nbsp; &nbsp; [ToUpper](#toupper) &nbsp; &nbsp; &nbsp; &nbsp; [Word](#word)

---
### <a name="append"></a>Accoda

**Funzione**<br>  Append(source, suffix)

**Descrizione:**<br>  Accetta un valore di stringa di origine e aggiunge un suffisso alla fine del valore.

**Parametri**<br> 

| Nome | Obbligatorio/Ripetuto | Type | Note |
| --- | --- | --- | --- |
| **source** |Obbligatoria |string |In genere è il nome dell'attributo dell'oggetto di origine. |
| **suffix** |Obbligatoria |string |Stringa da aggiungere alla fine del valore di origine. |

---
### <a name="bitand"></a>BitAnd
**Funzione**<br> BitAnd (value1, value2)

**Descrizione:**<br>  Questa funzione converte entrambi i parametri nella rappresentazione binaria e imposta un bit su:

0: se il valore di uno o entrambi i bit corrispondenti in value1 e value2 è 0                                                  
1: se entrambi i bit corrispondenti sono pari a 1.                                    

In altre parole, restituisce 0 in tutti i casi tranne quando i bit corrispondenti di entrambi i parametri sono pari a 1.

**Parametri**<br> 

| Nome | Obbligatorio/Ripetuto | Type | Note |
| --- | --- | --- | --- |
| **value1** |Obbligatoria |num |Valore numerico che deve essere unire con and con value2|
| **Value2** |Obbligatoria |num |Valore numerico che deve essere unire con and con value1|

**Esempio:**<br>
BitAnd(&HF, &HF7)                                                                                
11110111 e 00000111 = 00000111, quindi BitAnd restituisce 7, il valore binario 00000111

---
### <a name="cbool"></a>CBool
**Funzione**<br> CBool (espressione)

**Descrizione:**<br> CBool restituisce un valore booleano basato sull'espressione valutata. Se l'espressione restituisce un valore diverso da zero, CBool restituisce true; in caso contrario, restituisce false.

**Parametri**<br> 

| Nome | Obbligatorio/Ripetuto | Type | Note |
| --- | --- | --- | --- |
| **expression** |Obbligatoria | expression | Qualsiasi espressione valida |

**Esempio:**<br>
CBool ([attribute1] = [attribute2])                                                                    
Restituisce True se entrambi gli attributi hanno lo stesso valore.

---
### <a name="coalesce"></a>Coalesce
**Funzione**<br> COALESCE (source1, source2,..., defaultValue)

**Descrizione:**<br> Restituisce il primo valore di origine che non è NULL. Se tutti gli argomenti sono NULL e defaultValue è presente, viene restituito defaultValue. Se tutti gli argomenti sono NULL e defaultValue non è presente, COALESCE restituisce NULL.

**Parametri**<br> 

| Nome | Obbligatorio/Ripetuto | Type | Note |
| --- | --- | --- | --- |
| **source1 … sourceN** | Obbligatoria | string |Obbligatorio, numero variabile di volte. In genere è il nome dell'attributo dell'oggetto di origine. |
| **defaultValue** | Facoltativo | String | Valore predefinito da utilizzare quando tutti i valori di origine sono NULL. Può essere una stringa vuota ("").

---
### <a name="converttobase64"></a>ConvertToBase64
**Funzione**<br> ConvertToBase64 (origine)

**Descrizione:**<br>  La funzione ConvertToBase64 converte una stringa in una stringa Base 64 Unicode.

**Parametri**<br> 

| Nome | Obbligatorio/Ripetuto | Type | Note |
| --- | --- | --- | --- |
| **source** |Obbligatoria |string |Stringa da convertire in base 64|

**Esempio:**<br>
ConvertToBase64("Hello world!")                                                                                                        
 Restituisce "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Funzione**<br> ConvertToUTF8Hex (origine)

**Descrizione:**<br>  La funzione ConvertToUTF8Hex converte una stringa in un valore con codifica esadecimale UTF8.

**Parametri**<br> 

| Nome | Obbligatorio/Ripetuto | Type | Note |
| --- | --- | --- | --- |
| **source** |Obbligatoria |string |Stringa da convertire in esadecimale UTF8|

**Esempio:**<br>
ConvertToUTF8Hex("Hello world!")                                                                                                         
 Restituisce 48656C6C6F20776F726C6421

---
### <a name="count"></a>Conteggio
**Funzione**<br> Conteggio (attributo)

**Descrizione:**<br>  La funzione Count restituisce il numero di elementi in un attributo multivalore.

**Parametri**<br> 

| Nome | Obbligatorio/Ripetuto | Type | Note |
| --- | --- | --- | --- |
| **attributo** |Obbligatoria |Attributo |Attributo multivalore che avrà elementi conteggiati|

---
### <a name="cstr"></a>CStr
**Funzione**<br> CStr (valore)

**Descrizione:**<br> La funzione CStr converte un valore in un tipo di dati String.

**Parametri**<br> 

| Nome | Obbligatorio/Ripetuto | Type | Note |
| --- | --- | --- | --- |
| **value** |Obbligatoria | numeric, Reference o Boolean | può essere un valore numerico, un attributo di riferimento o un valore booleano. |

**Esempio:**<br>
CStr([dn])                                                            
Restituisce "CN = Joe, DC = contoso, DC = com"

---
### <a name="datefromnum"></a>DateFromNum
**Funzione**<br> DateFromNum (valore)

**Descrizione:**<br>  La funzione DateFromNum converte un valore nel formato di data di Active Directory in un tipo di data/ora.

**Parametri**<br> 

| Nome | Obbligatorio/Ripetuto | Type | Note |
| --- | --- | --- | --- |
| **value** |Obbligatoria | Data | Data di annuncio da convertire nel tipo DateTime |

**Esempio:**<br>
DateFromNum([lastLogonTimestamp])                                                                                                   
DateFromNum(129699324000000000)                                                            
 Restituisce un valore di data/ora che rappresenta 2012-01-01 23:00:00

---
### <a name="formatdatetime"></a>FormatDateTime
**Funzione**<br>  FormatDateTime(source, inputFormat, outputFormat)

**Descrizione:**<br>  Accetta una stringa data in un formato e la converte in un formato diverso.

**Parametri**<br> 

| Nome | Obbligatorio/Ripetuto | Type | Note |
| --- | --- | --- | --- |
| **source** |Obbligatoria |string |In genere è il nome dell'attributo dell'oggetto di origine. |
| **inputFormat** |Obbligatoria |string |Formato previsto del valore source. Per i formati supportati, vedere [/DotNet/standard/base-types/Custom-date-and-Time-Format-Strings](/dotnet/standard/base-types/custom-date-and-time-format-strings). |
| **outputFormat** |Obbligatoria |string |Formato della data di output. |

---
### <a name="guid"></a>Guid
**Funzione**<br> Guid()

**Descrizione:**<br> La funzione Guid genera un nuovo GUID casuale

---
### <a name="iif"></a>IIF
**Funzione**<br> IIF (Condition, valueIfTrue, valueIfFalse)

**Descrizione:**<br>  La funzione IIF restituisce uno dei possibili valori di un set, in base a una condizione specificata.

**Parametri**<br> 

| Nome | Obbligatorio/Ripetuto | Type | Note |
| --- | --- | --- | --- |
| **condizione** |Obbligatoria |Variabile o espressione |Qualsiasi valore o espressione che possa restituire true o false. |
| **valueIfTrue** |Obbligatoria |Variabile o stringa | se la condizione restituisce true, il valore restituito. |
| **valueIfFalse** |Obbligatoria |Variabile o stringa |se la condizione restituisce false, il valore restituito.|

**Esempio:**<br>
IIF ([Country] = "USA", [paese], [reparto])

---
### <a name="instr"></a>InStr
**Funzione**<br> InStr (value1, value2, Start, compareType)

**Descrizione:**<br>  La funzione InStr trova la prima occorrenza di una sottostringa in una stringa.

**Parametri**<br> 

| Nome | Obbligatorio/Ripetuto | Type | Note |
| --- | --- | --- | --- |
| **value1** |Obbligatoria |string |Stringa da cercare |
| **Value2** |Obbligatoria |string |Stringa da trovare |
| **start** |Facoltativo |Intero |Posizione iniziale per trovare la sottostringa|
| **compareType** |Facoltativo |Enumerazione |Può essere vbTextCompare o vbBinaryCompare |

**Esempio:**<br>
InStr("The quick brown fox","quick")                                                                             
 Restituisce 5

InStr("repEated","e",3,vbBinaryCompare)                                                                                  
 Restituisce 7

---
### <a name="isnull"></a>IsNull
**Funzione**<br> IsNull (espressione)

**Descrizione:**<br>  Se l'espressione restituisce Null, la funzione IsNull restituisce True.  Per un attributo, Null è espresso dall'assenza dell'attributo.

**Parametri**<br> 

| Nome | Obbligatorio/Ripetuto | Type | Note |
| --- | --- | --- | --- |
| **expression** |Obbligatoria |expression |Espressione da valutare |

**Esempio:**<br>
IsNull([displayName])                                                                                                
Restituisce true se l'attributo non è presente.

---
### <a name="isnullorempty"></a>IsNullorEmpty
**Funzione**<br> IsNullOrEmpty (espressione)

**Descrizione:**<br>  Se l'espressione è Null o una stringa vuota, la funzione IsNullOrEmpty restituisce True.  Per un attributo, verrà restituito True se l'attributo è assente oppure se è presente, ma è una stringa vuota.
La funzione inversa di questa funzione è denominata IsPresent.

**Parametri**<br> 

| Nome | Obbligatorio/Ripetuto | Type | Note |
| --- | --- | --- | --- |
| **expression** |Obbligatoria |expression |Espressione da valutare |

**Esempio:**<br>
IsNullOrEmpty ([displayName])                                               
Restituisce true se l'attributo non è presente o è una stringa vuota

---
### <a name="ispresent"></a>IsPresent
**Funzione**<br> Presenza (espressione)

**Descrizione:**<br>  Se l'espressione restituisce una stringa non Null e non vuota, la funzione IsPresent restituisce True.  La funzione inversa di questa funzione è denominata IsNullOrEmpty.

**Parametri**<br> 

| Nome | Obbligatorio/Ripetuto | Type | Note |
| --- | --- | --- | --- |
| **expression** |Obbligatoria |expression |Espressione da valutare |

**Esempio:**<br>
Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])

---
### <a name="isstring"></a>IsString
**Funzione**<br> Stringa (espressione)

**Descrizione:**<br>  Se l'espressione può essere valutata come tipo stringa, la funzione IsString restituisce True.

**Parametri**<br> 

| Nome | Obbligatorio/Ripetuto | Type | Note |
| --- | --- | --- | --- |
| **expression** |Obbligatoria |expression |Espressione da valutare |

---
### <a name="item"></a>Item
**Funzione**<br> Item (attributo, indice)

**Descrizione:**<br>  La funzione Item restituisce un elemento da una stringa o un attributo multivalore.

**Parametri**<br> 

| Nome | Obbligatorio/Ripetuto | Type | Note |
| --- | --- | --- | --- |
| **attributo** |Obbligatoria |Attributo |Attributo multivalore da cercare |
| **index** |Obbligatoria |Intero | Indice di un elemento nella stringa multivalore|

**Esempio:**<br>
Elemento ([proxyAddresses], 1)

---
### <a name="join"></a>Join
**Funzione**<br>  Join(separator, source1, source2, …)

**Descrizione:**<br> Join() è simile ad Append(), ma può combinare più valori di stringa **source** in un singola stringa e ogni valore sarà separato da una stringa **separator**.

Se uno dei valori di origine è un attributo multivalore, verranno uniti tutti i valori dell'attributo, separati dal valore separatore.

**Parametri**<br> 

| Nome | Obbligatorio/Ripetuto | Type | Note |
| --- | --- | --- | --- |
| **separator** |Obbligatoria |string |Stringa usata per separare i valori di origine quando sono concatenati in una stringa. Può essere "" se non sono necessari separatori. |
| **source1 … sourceN** |Obbligatorio per un numero variabile di volte |string |Valori stringa da unire. |

---
### <a name="left"></a>Sinistra
**Funzione**<br> Left (stringa, NumChars)

**Descrizione:**<br>  La funzione Left restituisce un numero di caratteri specificato a partire da sinistra di una stringa. Se numChars = 0, restituisce una stringa vuota.
Se numChars < 0, restituisce una stringa di input.
Se string è Null, restituisce una stringa vuota.
Se string contiene un numero di caratteri inferiore al numero specificato in numChars, viene restituita una stringa identica a string (ovvero contenente tutti i caratteri nel parametro 1).

**Parametri**<br> 

| Nome | Obbligatorio/Ripetuto | Type | Note |
| --- | --- | --- | --- |
| **Stringa** |Obbligatoria |Attributo | Stringa da cui restituire i caratteri |
| **NumChars** |Obbligatoria |Intero | Numero che identifica il numero di caratteri da restituire dall'inizio (sinistra) della stringa|

**Esempio:**<br>
Left ("John Doe", 3)                                                            
Restituisce "Joh"

---
### <a name="mid"></a>Mid
**Funzione**<br>  Mid(source, start, length)

**Descrizione:**<br>  Restituisce una sottostringa del valore source. Una sottostringa è una stringa che contiene solo alcuni caratteri della stringa di origine.

**Parametri**<br> 

| Nome | Obbligatorio/Ripetuto | Type | Note |
| --- | --- | --- | --- |
| **source** |Obbligatoria |string |Corrisponde in genere al nome dell'attributo. |
| **start** |Obbligatoria |numero intero |Indice nella stringa **source** che indica il punto di inizio della sottostringa. L'indice del primo carattere della stringa sarà pari a 1, quello del secondo carattere a 2 e così via. |
| **length** |Obbligatoria |numero intero |Lunghezza della sottostringa. Se la lunghezza eccede la stringa **source**, la funzione restituirà una sottostringa dall'indice **start** fino alla fine della stringa **source**. |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Funzione**<br> NormalizeDiacritics(source)

**Descrizione:**<br> Richiede un argomento stringa. Restituisce la stringa, ma con i caratteri diacritici sostituiti dagli equivalenti caratteri non diacritici. Viene in genere usata per convertire i nomi e i cognomi contenenti caratteri diacritici (accenti) in valori validi che possono essere usati in vari ID utente, ad esempio nomi dell'entità utente, nomi di account SAM e indirizzi di posta elettronica.

**Parametri**<br> 

| Nome | Obbligatorio/Ripetuto | Type | Note |
| --- | --- | --- | --- |
| **source** |Obbligatoria |string | In genere un attributo nome o cognome. |

---
### <a name="not"></a>Not
**Funzione**<br>  Not(source)

**Descrizione:**<br> Inverte il valore booleano di **source**. Se il valore **source** è "*True*", restituirà "*False*". In caso contrario, restituirà "*True*".

**Parametri**<br> 

| Nome | Obbligatorio/Ripetuto | Type | Note |
| --- | --- | --- | --- |
| **source** |Obbligatoria |Stringa booleana |I valori previsti per **source** sono "True" o "False". |

---
### <a name="numfromdate"></a>NumFromDate
**Funzione**<br> NumFromDate (valore)

**Descrizione:**<br> La funzione NumFromDate converte un valore DateTime nel formato Active Directory necessario per impostare attributi come [accountExpires](/windows/win32/adschema/a-accountexpires). Usare questa funzione per convertire i valori DateTime ricevuti dalle app Cloud HR, come la giornata lavorativa e SuccessFactors, nella relativa rappresentazione AD equivalente. 

**Parametri**<br> 

| Nome | Obbligatorio/Ripetuto | Type | Note |
| --- | --- | --- | --- |
| **value** |Obbligatoria | string | Stringa data/ora nel formato supportato. Per informazioni sui formati supportati, vedere https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx. |

**Esempio:**<br>
* Esempio di giornata lavorativa <br>
  Supponendo che si voglia eseguire il mapping dell'attributo *ContractEndDate* dalla giornata lavorativa nel formato *2020-12-31-08:00* al campo *accountExpires* in ad, di seguito viene illustrato come usare questa funzione e modificare la differenza del fuso orario in modo che corrisponda alle impostazioni locali. 
  `NumFromDate(Join("", FormatDateTime([ContractEndDate], "yyyy-MM-ddzzz", "yyyy-MM-dd"), "T23:59:59-08:00"))`

* Esempio di SuccessFactors <br>
  Se si vuole eseguire il mapping dell'attributo *EndDate* da SuccessFactors, che è nel campo formato *M/d/aaaa hh: mm: SS TT* in *accountExpires* in Active Directory, ecco come è possibile usare questa funzione e modificare la differenza di fuso orario in base alle impostazioni locali.
  `NumFromDate(Join("",FormatDateTime([endDate],"M/d/yyyy hh:mm:ss tt","yyyy-MM-dd"),"T23:59:59-08:00"))`


---
### <a name="removeduplicates"></a>RemoveDuplicates
**Funzione**<br> RemoveDuplicates (attributo)

**Descrizione:**<br>  La funzione RemoveDuplicates accetta una stringa multivalore e verifica che ogni valore sia univoco.

**Parametri**<br> 

| Nome | Obbligatorio/Ripetuto | Type | Note |
| --- | --- | --- | --- |
| **attributo** |Obbligatoria |Attributo multivalore |Attributo multivalore in cui vengono rimossi i duplicati|

**Esempio:**<br>
RemoveDuplicates([proxyAddresses])                                                                                                       
Restituisce un attributo proxyAddress purificato in cui sono stati rimossi tutti i valori duplicati

---
### <a name="replace"></a>Sostituisci
**Funzione**<br> Replace(source, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, template)

**Descrizione:**<br>
 Sostituisce i valori all'interno di una stringa. Funziona in modo diverso a seconda dei parametri forniti:

* Se vengono forniti **oldValue** e **replacementValue**:
  
  * Sostituisce tutte le occorrenze di **OldValue** nell' **origine**  con **replacementValue**
* Se vengono forniti **oldValue** e **template**:
  
  * Sostituisce tutte le occorrenze di **oldValue** in **template** con il valore **source**.
* Se vengono forniti **regexPattern** e **replacementValue**:

  * La funzione applica **regexPattern** alla stringa **source** ed è possibile usare i nomi del gruppo di regex per creare la stringa per **replacementValue**
* Se vengono forniti **regexPattern**, **regexGroupName** e **replacementValue**:
  
  * La funzione applica **regexPattern** alla stringa **source** e sostituisce tutti i valori che corrispondono a **regexGroupName** con **replacementValue**
* Se vengono forniti **regexPattern**, **regexGroupName** e **replacementAttributeName**:
  
  * Se **source** non ha alcun valore, viene restituito **source**
  * Se **source** include un valore, la funzione applica **regexPattern** alla stringa **source** e sostituisce tutti i valori corrispondenti a **regexGroupName** con il valore associato a **replacementAttributeName**

**Parametri**<br> 

| Nome | Obbligatorio/Ripetuto | Type | Note |
| --- | --- | --- | --- |
| **source** |Obbligatoria |string |In genere il nome dell'attributo dall'oggetto di **origine** . |
| **oldValue** |Facoltativo |String |Valore da sostituire in **source** o **template**. |
| **regexPattern** |Facoltativo |String |Modello Regex per il valore da sostituire in **source**. Se invece si usa **replacementPropertyName**, corrisponde al modello usato per estrarre il valore da **replacementPropertyName**. |
| **regexGroupName** |Facoltativo |String |Nome del gruppo in **regexPattern**. Solo se si usa **replacementPropertyName**, il valore di questo gruppo verrà estratto come **replacementValue** da **replacementPropertyName**. |
| **replacementValue** |Facoltativo |String |Nuovo valore con cui sostituire il precedente. |
| **replacementAttributeName** |Facoltativo |String |Nome dell'attributo da usare per il valore di sostituzione |
| **modello** |Facoltativo |String |Quando viene specificato il valore del **modello** , si cercherà **OldValue** all'interno del modello e lo si sostituirà con il valore **source** . |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**Funzione**<br> SelectUniqueValue(uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, …)

**Descrizione:**<br> Richiede almeno due argomenti, costituiti da regole di generazione di valori univoci definite tramite espressioni. La funzione valuta ogni regola e controlla quindi il valore generato per verificarne l'univocità nella directory/app di destinazione. Il primo valore univoco trovato sarà quello restituito. Se tutti i valori trovati sono già presenti nella destinazione, la voce verrà depositata e il motivo verrà registrato nei log di controllo. Non è previsto alcun limite relativamente al numero di argomenti che è possibile specificare.


 - Si tratta di una funzione di primo livello che non può essere annidata.
 - Questa funzione non può essere applicata agli attributi con precedenza abbinamento.   
 - Questa funzione è destinata a essere usata solo per la creazione di voci. Se viene usata con un attributo, impostare la proprietà **Applica questo mapping** su **Solo durante la creazione dell'oggetto**.
 - Questa funzione è attualmente supportata solo per il "provisioning utenti da Workday ad Active Directory". Non può essere usata con altre applicazioni di provisioning. 


**Parametri**<br> 

| Nome | Obbligatorio/Ripetuto | Type | Note |
| --- | --- | --- | --- |
| **uniqueValueRule1  … uniqueValueRuleN** |Sono necessari almeno 2 argomenti, nessun limite superiore |string | Elenco delle regole di generazione di valori univoci da valutare. |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Funzione**<br> SingleAppRoleAssignment([appRoleAssignments])

**Descrizione:**<br> Restituisce un singolo oggetto appRoleAssignment dall'elenco di tutti gli oggetti appRoleAssignments assegnati a un utente per una determinata applicazione. Questa funzione è necessaria per convertire l'oggetto appRoleAssignments in una singola stringa relativa al nome di un ruolo. Si noti che la procedura consigliata è quella di assicurarsi che un solo oggetto appRoleAssignment venga assegnato a un solo utente alla volta. Se vengono assegnati più ruoli, la stringa di ruolo restituita potrebbe non essere prevedibile. 

**Parametri**<br> 

| Nome | Obbligatorio/Ripetuto | Type | Note |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |Obbligatoria |string |Oggetto **[appRoleAssignments]**. |

---
### <a name="split"></a>Doppia visualizzazione
**Funzione**<br> Split(source, delimiter)

**Descrizione:**<br> Divide una stringa in una matrice multi-valore usando il carattere di delimitazione specificato.

**Parametri**<br> 

| Nome | Obbligatorio/Ripetuto | Type | Note |
| --- | --- | --- | --- |
| **source** |Obbligatoria |string |**Valore source** da aggiornare. |
| **delimiter** |Obbligatoria |string |Specifica il carattere che verrà usato per dividere la stringa (esempio: ",") |

---
### <a name="stripspaces"></a>StripSpaces
**Funzione**<br>  StripSpaces(source)

**Descrizione:**<br>  Rimuove tutti i caratteri di spazio (" ") dalla stringa di origine.

**Parametri**<br> 

| Nome | Obbligatorio/Ripetuto | Type | Note |
| --- | --- | --- | --- |
| **source** |Obbligatoria |string |**Valore source** da aggiornare. |

---
### <a name="switch"></a>Opzione
**Funzione**<br>  Switch(source, defaultValue, key1, value1, key2, value2, …)

**Descrizione:**<br> Se il valore di **source** corrisponde al parametro **key**, verrà restituito un parametro **value** per tale **key**. Se il valore di **source** non corrisponde ad alcuna chiave, verrà restituito un valore **defaultValue**.  I parametri **key** e **value** devono essere sempre accoppiati. Le funzioni prevedono sempre un numero pari di parametri. La funzione non deve essere usata per gli attributi referenziali, ad esempio Manager. 

**Parametri**<br> 

| Nome | Obbligatorio/Ripetuto | Type | Note |
| --- | --- | --- | --- |
| **source** |Obbligatoria |string |**Source** da aggiornare. |
| **defaultValue** |Facoltativo |String |Valore predefinito da usare se l'origine non corrisponde ad alcuna chiave. Può essere una stringa vuota (""). |
| **key** |Obbligatoria |string |Parametro **key** con cui confrontare il valore di **source**. |
| **value** |Obbligatoria |string |Valore di sostituzione per il valore **source** corrispondente al parametro key. |

---
### <a name="tolower"></a>ToLower
**Funzione**<br> ToLower (origine, impostazioni cultura)

**Descrizione:**<br> Prende un valore della stringa di *origine* e lo converte in caratteri minuscoli usando le regole delle impostazioni cultura specificate. Se non sono specificate informazioni per le *impostazioni cultura*, verranno usate le impostazioni cultura inglese non dipendenti da paese/area geografica.

**Parametri**<br> 

| Nome | Obbligatorio/Ripetuto | Type | Note |
| --- | --- | --- | --- |
| **source** |Obbligatoria |string |In genere è il nome dell'attributo dell'oggetto di origine. |
| **Impostazioni cultura** |Facoltativo |String |Il formato per il nome delle impostazioni cultura basato su RFC 4646 è *languagecode2-country/regioncode2*, in cui *languagecode2* è il codice lingua a due lettere e *country/regioncode2* è il codice di impostazioni cultura secondarie a due lettere. Tra gli esempi sono inclusi ja-JP per Giapponese (Giappone) ed en-US per Inglese (Stati Uniti). Nei casi in cui non è disponibile un codice lingua a due lettere, viene usato un codice a tre lettere derivato da ISO 639-2.|

---
### <a name="toupper"></a>ToUpper
**Funzione**<br> ToUpper (origine, impostazioni cultura)

**Descrizione:**<br> Prende un valore della stringa di *origine* e lo converte in caratteri maiuscoli usando le regole delle impostazioni cultura specificate. Se non sono specificate informazioni per le *impostazioni cultura*, verranno usate le impostazioni cultura inglese non dipendenti da paese/area geografica.

**Parametri**<br> 

| Nome | Obbligatorio/Ripetuto | Type | Note |
| --- | --- | --- | --- |
| **source** |Obbligatoria |string |In genere è il nome dell'attributo dell'oggetto di origine. |
| **Impostazioni cultura** |Facoltativo |String |Il formato per il nome delle impostazioni cultura basato su RFC 4646 è *languagecode2-country/regioncode2*, in cui *languagecode2* è il codice lingua a due lettere e *country/regioncode2* è il codice di impostazioni cultura secondarie a due lettere. Tra gli esempi sono inclusi ja-JP per Giapponese (Giappone) ed en-US per Inglese (Stati Uniti). Nei casi in cui non è disponibile un codice lingua a due lettere, viene usato un codice a tre lettere derivato da ISO 639-2.|

---
### <a name="word"></a>Word
**Funzione**<br> Word (stringa, WordNumber, delimitatori)

**Descrizione:**<br>  La funzione Word restituisce una parola contenuta in una stringa, in base ai parametri che descrivono i delimitatori da usare e il numero della parola da restituire.  Ogni stringa di caratteri contenuta nella stringa con valori separati da uno dei caratteri specificati nei delimitatori viene identificata come una parola:

Se number è < 1, restituisce una stringa vuota.
Se string è Null, restituisce una stringa vuota.
Se la stringa contiene meno delle parole specificate in number o se non contiene alcuna parola identificata da delimiters, viene restituita una stringa vuota.

**Parametri**<br> 

| Nome | Obbligatorio/Ripetuto | Type | Note |
| --- | --- | --- | --- |
| **Stringa** |Obbligatoria |Attributo multivalore |Stringa da cui restituire una parola.|
| **WordNumber** |Obbligatoria | Intero | Numero che identifica il numero di parola da restituire|
| **delimitatori** |Obbligatoria |string| Stringa che rappresenta i delimitatori da usare per identificare le parole|

**Esempio:**<br>
Word ("The Quick Brown Fox", 3, "")                                                                                       
 Restituisce "brown"

Word ("This, String! has&molti separatori", 3, ",! & #")                                                                       
Restituisce "has"

---

## <a name="examples"></a>Esempi
### <a name="strip-known-domain-name"></a>Rimuovere un nome di dominio noto
Occorre rimuovere un nome di dominio noto dall'indirizzo di posta elettronica di un utente per ottenere il nome utente. <br>
 Ad esempio, se il dominio è "contoso.com", è possibile usare l'espressione seguente:

**Espressione** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**Input/output di esempio:** <br>

* **INPUT** (mail): "john.doe@contoso.com"
* **Output**: "John. Doe"

### <a name="append-constant-suffix-to-user-name"></a>Aggiungere un suffisso costante al nome utente
Se si usa un ambiente sandbox Salesforce, potrebbe essere necessario aggiungere un suffisso a tutti i nomi utente prima di sincronizzarli.

**Espressione** <br>
`Append([userPrincipalName], ".test")`

**Input/output di esempio:** <br>

* **INPUT** (userPrincipalName): "John.Doe@contoso.com"
* **Output**: " John.Doe@contoso.com.test "

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Generare un alias utente concatenando parti del nome e del cognome
Occorre generare un alias utente contenente le prime tre lettere del nome e le prime cinque lettere del cognome dell'utente.

**Espressione** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Input/output di esempio:** <br>

* **INPUT** (givenName): "John"
* **INPUT** (surname): "Doe"
* **OUTPUT**: "JohDoe"

### <a name="remove-diacritics-from-a-string"></a>Rimuovere i segni diacritici da una stringa
È necessario sostituire i caratteri contenenti accenti con gli equivalenti caratteri non contenenti accenti.

**Espressione** <br>
NormalizeDiacritics([givenName])

**Input/output di esempio:** <br>

* **INPUT** (givenName): "Zoë"
* **OUTPUT**: "Zoe"

### <a name="split-a-string-into-a-multi-valued-array"></a>Dividere una stringa in una matrice multivalore
È necessario partire da un elenco di stringhe delimitate da virgole e dividerlo in una matrice che possa essere inserita in un attributo multivalore come l'attributo PermissionSets di Salesforce. In questo esempio un elenco di set di autorizzazioni è stato popolato in extensionAttribute5 in Azure AD.

**Espressione** <br>
Split([extensionAttribute5], ",")

**Input/output di esempio:** <br>

* **Input** (extensionAttribute5): "PermissionSetOne, PermisionSetTwo"
* **OUTPUT**: ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>Eseguire l'output della data come stringa in un formato specifico
Occorre inviare date a un'applicazione SaaS in un formato specifico, <br>
Ad esempio, formattare le date per ServiceNow.

**Espressione** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Input/output di esempio:**

* **INPUT** (extensionAttribute1): "20150123105347.1Z"
* **OUTPUT**: "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Sostituire un valore in base a un set di opzioni predefinito

È necessario definire il fuso orario dell'utente in base al codice di stato archiviato in Azure AD. <br>
 Se il codice di stato non corrisponde ad alcuna opzione predefinita, usare il valore predefinito "Australia/Sydney".

**Espressione** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Input/output di esempio:**

* **INPUT** (state): "QLD"
* **OUTPUT**: "Australia/Brisbane"

### <a name="replace-characters-using-a-regular-expression"></a>Sostituire caratteri usando un'espressione regolare
È necessario trovare i caratteri che corrispondono al valore di un'espressione regolare e rimuoverli.

**Espressione** <br>

Replace([mailNickname], , "[a-zA-Z_]*", , "", , )

**Input/output di esempio:**

* **INPUT** (mailNickname: "john_doe72"
* **Output**: "72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>Converte il valore userPrincipalName (UPN) generato in caratteri minuscoli
Nell'esempio seguente il valore UPN viene generato concatenando i campi di origine PreferredFirstName e PreferredLastName e la funzione ToLower viene usata con la stringa generata per convertire tutti i caratteri in minuscolo. 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**Input/output di esempio:**

* **INPUT** (PreferredFirstName): "John"
* **INPUT** (PreferredLastName): "Smith"
* **Output**: " john.smith@contoso.com "

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>Generare un valore univoco per l'attributo userPrincipalName (UPN)
In base al nome, al secondo nome e al cognome dell'utente, è necessario generare un valore per l'attributo UPN e verificarne l'univocità nella directory di AD di destinazione prima di assegnare il valore all'attributo UPN.

**Espressione** <br>

```ad-attr-mapping-expr
    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )
```

**Input/output di esempio:**

* **INPUT** (PreferredFirstName): "John"
* **INPUT** (PreferredLastName): "Smith"
* **Output**: " John.Smith@contoso.com " Se il valore UPN di John.Smith@contoso.com non esiste già nella directory
* **Output**: " J.Smith@contoso.com " Se il valore UPN di John.Smith@contoso.com esiste già nella directory
* **Output**: " Jo.Smith@contoso.com " se i due valori UPN precedenti sono già presenti nella directory

### <a name="flow-mail-value-if-not-null-otherwise-flow-userprincipalname"></a>Valore della posta del flusso se non NULL; in caso contrario, Flow userPrincipalName
Si desidera che il flusso dell'attributo mail sia presente. In caso contrario, si vuole invece propagare il valore di userPrincipalName.

**Espressione** <br>
`Coalesce([mail],[userPrincipalName])`

**Input/output di esempio:** <br>

* **Input** (mail): null
* **Input** (userPrincipalName): " John.Doe@contoso.com "
* **Output**: " John.Doe@contoso.com "

## <a name="related-articles"></a>Articoli correlati
* [Automatizzare il provisioning e il deprovisioning utenti in app SaaS](../app-provisioning/user-provisioning.md)
* [Personalizzazione dei mapping degli attributi per il provisioning degli utenti](../app-provisioning/customize-application-attributes.md)
* [Ambito dei filtri per il Provisioning utente](define-conditional-rules-for-provisioning-user-accounts.md)
* [Uso di SCIM per abilitare il provisioning automatico di utenti e gruppi da Azure Active Directory alle applicazioni](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Notifiche relative al provisioning degli account](../app-provisioning/user-provisioning.md)
* [Elenco di esercitazioni pratiche sulla procedura di integrazione delle applicazioni SaaS](../saas-apps/tutorial-list.md)