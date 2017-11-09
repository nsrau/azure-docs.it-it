---
title: Scrittura di espressioni per il mapping degli attributi in Azure Active Directory | Documentazione Microsoft
description: Informazioni su come usare i mapping di espressioni per trasformare i valori degli attributi in un formato accettabile durante il provisioning automatizzato di oggetti SaaS in Azure Active Directory.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: b13c51cd-1bea-4e5e-9791-5d951a518943
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: markvi
ms.openlocfilehash: 2811b4d57f69425ef119c88f80b32d24c6c32195
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2017
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Scrittura di espressioni per il mapping degli attributi in Azure Active Directory
Quando si configura il provisioning in un'applicazione SaaS, come mapping degli attributi è possibile specificare il mapping di espressioni. Per questo tipo di mapping è necessario scrivere un'espressione analoga a uno script, che permette di trasformare i dati utente in formati più idonei all'applicazione SaaS.

## <a name="syntax-overview"></a>Panoramica della sintassi
La sintassi per le espressioni per i mapping degli attributi è simile a quella delle funzioni di Visual Basic for Applications (VBA).

* L'intera espressione deve essere definita in termini di funzioni, che sono costituite da un nome seguito da argomenti racchiusi tra parentesi:  <br>
  *NomeFunzione(&lt;&lt;argomento 1&gt;&gt;,&lt;<argument N>&gt;)*
* È possibile annidare le funzioni in altre funzioni. Ad esempio: <br> *FunzioneUno(FunzioneDue(&lt;<argument1>&gt;))*
* È possibile passare tre tipi diversi di argomenti nelle funzioni:
  
  1. Attributi, che devono essere racchiusi tra parentesi quadre. Ad esempio: [NomeAttributo]
  2. Costanti di stringa, che devono essere racchiuse tra virgolette doppie. Ad esempio: "Stati Uniti"
  3. Altre funzioni. Ad esempio: FunzioneUno(<<argument1>>, FunzioneDue(<<argument2>>))
* Eventuali barre rovesciate ( \ ) o virgolette ( " ) da inserire nella costante di stringa dovranno essere precedute dal simbolo di barra rovesciata ( \ ) come carattere di escape. Ad esempio: "Nome società: \"Contoso\""

## <a name="list-of-functions"></a>Elenco di funzioni
[Append](#append)&nbsp;&nbsp;&nbsp;&nbsp;[FormatDateTime](#formatdatetime)&nbsp;&nbsp;&nbsp;&nbsp;[Join](#join)&nbsp;&nbsp;&nbsp;&nbsp;[Mid](#mid)&nbsp;&nbsp;&nbsp;&nbsp;[Not](#not)&nbsp;&nbsp;&nbsp;&nbsp;[Replace](#replace)&nbsp;&nbsp;&nbsp;&nbsp;[StripSpaces](#stripspaces)&nbsp;&nbsp;&nbsp;&nbsp;[Switch](#switch)

- - -
### <a name="append"></a>Append
**Funzione:**<br> Append(source, suffix)

**Descrizione:**<br> Accetta un valore di stringa di origine e aggiunge un suffisso alla fine del valore.

**Parametri:**<br> 

| Nome | Obbligatorio/Ripetuto | Tipo | Note |
| --- | --- | --- | --- |
| **source** |Obbligatorio |String |In genere è il nome dell'attributo dell'oggetto di origine. |
| **suffix** |Obbligatorio |String |Stringa da aggiungere alla fine del valore di origine. |

- - -
### <a name="formatdatetime"></a>FormatDateTime
**Funzione:**<br> FormatDateTime(source, inputFormat, outputFormat)

**Descrizione:**<br> Accetta una stringa data in un formato e la converte in un formato diverso.

**Parametri:**<br> 

| Nome | Obbligatorio/Ripetuto | Tipo | Note |
| --- | --- | --- | --- |
| **source** |Obbligatorio |String |In genere è il nome dell'attributo dell'oggetto di origine. |
| **inputFormat** |Obbligatorio |String |Formato previsto del valore source. Per informazioni sui formati supportati, vedere [http://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](http://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx). |
| **outputFormat** |Obbligatorio |String |Formato della data di output. |

- - -
### <a name="join"></a>Join
**Funzione:**<br> Join(separator, source1, source2, …)

**Descrizione:**<br> Join() è simile ad Append(), ma può combinare più valori di stringa **source** in un singola stringa e ogni valore sarà separato da una stringa **separator**.

Se uno dei valori di origine è un attributo con più valori, verranno aggiunti tutti i valori dell'attributo, separati dal valore separatore.

**Parametri:**<br> 

| Nome | Obbligatorio/Ripetuto | Tipo | Note |
| --- | --- | --- | --- |
| **separator** |Obbligatorio |String |Stringa usata per separare i valori di origine quando sono concatenati in una stringa. Può essere "" se non sono necessari separatori. |
| **source1 … sourceN** |Obbligatorio per un numero variabile di volte |String |Valori stringa da unire. |

- - -
### <a name="mid"></a>Mid
**Funzione:**<br> Mid(source, start, length)

**Descrizione:**<br> Restituisce una sottostringa del valore source. Una sottostringa è una stringa che contiene solo alcuni caratteri della stringa di origine.

**Parametri:**<br> 

| Nome | Obbligatorio/Ripetuto | Tipo | Note |
| --- | --- | --- | --- |
| **source** |Obbligatorio |String |Corrisponde in genere al nome dell'attributo. |
| **start** |Obbligatorio |integer |Indice nella stringa **source** che indica il punto di inizio della sottostringa. L'indice del primo carattere della stringa sarà pari a 1, quello del secondo carattere a 2 e così via. |
| **length** |Obbligatorio |integer |Lunghezza della sottostringa. Se la lunghezza eccede la stringa **source**, la funzione restituirà una sottostringa dall'indice **start** fino alla fine della stringa **source**. |

- - -
### <a name="not"></a>not
**Funzione:**<br> Not(source)

**Descrizione:**<br> Inverte il valore booleano di **source**. Se il valore di **source** è "*True*", restituisce "*False*". In caso contrario, restituisce "*True*".

**Parametri:**<br> 

| Nome | Obbligatorio/Ripetuto | Tipo | Note |
| --- | --- | --- | --- |
| **source** |Obbligatorio |Stringa booleana |I valori previsti per **source** sono "True" o "False". |

- - -
### <a name="replace"></a>Replace
**Funzione:**<br> ObsoleteReplace(source, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, template)

**Descrizione:**<br>
Sostituisce i valori all'interno di una stringa. Funziona in modo diverso a seconda dei parametri forniti:

* Se vengono forniti **oldValue** e **replacementValue**:
  
  * Sostituisce tutte le occorrenze di oldValue in source con replacementValue
* Se vengono forniti **oldValue** e **template**:
  
  * Sostituisce tutte le occorrenze di **oldValue** in **template** con il valore **source**
* Se vengono forniti **oldValueRegexPattern**, **oldValueRegexGroupName**, **replacementValue**:
  
  * Sostituisce tutti i valori corrispondenti a oldValueRegexPattern nella stringa source con replacementValue.
* Se vengono forniti **oldValueRegexPattern**, **oldValueRegexGroupName**, **replacementPropertyName**:
  
  * Se è presente un valore per **source**, verrà restituito **source**
  * Se non sono presenti valori per **source**, verranno usati **oldValueRegexPattern** e **oldValueRegexGroupName** per estrarre il valore di sostituzione dalla proprietà con **replacementPropertyName**. Il valore di sostituzione viene restituito come risultato.

**Parametri:**<br> 

| Nome | Obbligatorio/Ripetuto | Tipo | Note |
| --- | --- | --- | --- |
| **source** |Obbligatorio |String |In genere è il nome dell'attributo dell'oggetto di origine. |
| **oldValue** |Facoltativo |String |Valore da sostituire in **source** o **template**. |
| **regexPattern** |Facoltativo |String |Criterio di espressione regolare per il valore da sostituire in **source**. Se invece si usa replacementPropertyName, corrisponde al modello usato per estrarre il valore dalla proprietà di sostituzione. |
| **regexGroupName** |Facoltativo |String |Nome del gruppo in **regexPattern**. Solo se si usa replacementPropertyName, il valore di questo gruppo verrà estratto come replacementValue dalla proprietà di sostituzione. |
| **replacementValue** |Facoltativo |String |Nuovo valore con cui sostituire il precedente. |
| **replacementAttributeName** |Facoltativo |String |Nome dell'attributo da usare per il valore di sostituzione, se non sono presenti valori per source. |
| **template** |Facoltativo |String |Se viene fornito il valore **template**, il valore **oldValue** verrà cercato in template e sostituito con il valore source. |

- - -
### <a name="stripspaces"></a>StripSpaces
**Funzione:**<br> StripSpaces(source)

**Descrizione:**<br> Rimuove tutti i caratteri di spazio (" ") dalla stringa di origine.

**Parametri:**<br> 

| Nome | Obbligatorio/Ripetuto | Tipo | Note |
| --- | --- | --- | --- |
| **source** |Obbligatorio |String |**source** da aggiornare. |

- - -
### <a name="switch"></a>Switch
**Funzione:**<br> Switch(source, defaultValue, key1, value1, key2, value2, …)

**Descrizione:**<br> Quando il valore **source** corrisponde a **key**, verrà restituito un parametro **value** per tale oggetto **key**. Se il valore del parametro **source** non corrisponde ad alcuna chiave, verrà restituito **defaultValue**.  I parametri **key** e **value** devono essere sempre accoppiati. Le funzioni prevedono sempre un numero pari di parametri.

**Parametri:**<br> 

| Nome | Obbligatorio/Ripetuto | Tipo | Note |
| --- | --- | --- | --- |
| **source** |Obbligatorio |String |**Source** da aggiornare. |
| **defaultValue** |Facoltativo |String |Valore predefinito da usare se l'origine non corrisponde ad alcuna chiave. Può essere una stringa vuota (""). |
| **key** |Obbligatorio |String |Parametro **key** con cui confrontare il valore di **source**. |
| **value** |Obbligatorio |String |Valore di sostituzione per il valore **source** corrispondente al parametro key. |

## <a name="examples"></a>esempi
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
`Append([userPrincipalName], ".test"))`

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
* **OUTPUT**: "JohDoe"

### <a name="output-date-as-a-string-in-a-certain-format"></a>Eseguire l'output della data come stringa in un formato specifico
Occorre inviare date a un'applicazione SaaS in un formato specifico, <br>
Ad esempio, formattare le date per ServiceNow.

**Espressione:** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Input/output di esempio:**

* **INPUT** (extensionAttribute1): "20150123105347.1Z"
* **OUTPUT**: "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Sostituire un valore in base a un set di opzioni predefinito
È necessario definire il fuso orario dell'utente in base al codice di stato archiviato in Azure AD. <br>
Se il codice di stato non corrisponde ad alcuna opzione predefinita, usare il valore predefinito "Australia/Sydney".

**Espressione:** <br>

`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Input/output di esempio:**

* **INPUT** (state): "QLD"
* **OUTPUT**: "Australia/Brisbane"

## <a name="related-articles"></a>Articoli correlati
* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)
* [Automatizzare il provisioning e il deprovisioning utenti in app SaaS](active-directory-saas-app-provisioning.md)
* [Personalizzazione dei mapping degli attributi per il Provisioning dell’utente](active-directory-saas-customizing-attribute-mappings.md)
* [Ambito dei filtri per il Provisioning utente](active-directory-saas-scoping-filters.md)
* [Uso di SCIM per abilitare il provisioning automatico di utenti e gruppi da Azure Active Directory alle applicazioni](active-directory-scim-provisioning.md)
* [Notifiche relative al provisioning dell'account](active-directory-saas-account-provisioning-notifications.md)
* [Elenco di esercitazioni pratiche sulla procedura di integrazione delle applicazioni SaaS](active-directory-saas-tutorial-list.md)

