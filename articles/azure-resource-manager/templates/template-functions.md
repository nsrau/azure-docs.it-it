---
title: Funzioni di modello
description: Vengono descritte le funzioni da utilizzare in un modello di gestione risorse di Azure per recuperare valori, lavorare con stringhe e valori numerici, e recuperare informazioni sulla distribuzione.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: a15e7dfdf01a99cd23b216fafcfb44320a716d16
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82231289"
---
# <a name="arm-template-functions"></a>Funzioni modello ARM

Questo articolo descrive tutte le funzioni che è possibile usare in un modello di Azure Resource Manager (ARM). Per informazioni sull'uso delle funzioni nel modello, vedere [sintassi del modello](template-expressions.md).

Per creare funzioni personalizzate, vedere [Funzioni definite dall'utente](template-syntax.md#functions).

La maggior parte delle funzioni funziona allo stesso modo quando viene distribuita in un gruppo di risorse, una sottoscrizione, un gruppo di gestione o un tenant. Alcune funzioni non possono essere usate in tutti gli ambiti. Sono indicati negli elenchi riportati di seguito.

<a id="array" aria-hidden="true" />
<a id="concatarray" aria-hidden="true" />
<a id="contains" aria-hidden="true" />
<a id="createarray" aria-hidden="true" />
<a id="empty" aria-hidden="true" />
<a id="first" aria-hidden="true" />
<a id="intersection" aria-hidden="true" />
<a id="last" aria-hidden="true" />
<a id="length" aria-hidden="true" />
<a id="min" aria-hidden="true" />
<a id="max" aria-hidden="true" />
<a id="range" aria-hidden="true" />
<a id="skip" aria-hidden="true" />
<a id="take" aria-hidden="true" />
<a id="union" aria-hidden="true" />

## <a name="array-functions"></a>Funzioni di matrice

Gestione risorse fornisce diverse funzioni per l'utilizzo di matrici.

* [array](template-functions-array.md#array)
* [Concat](template-functions-array.md#concat)
* [contains](template-functions-array.md#contains)
* [Funzione CreateArray](template-functions-array.md#createarray)
* [empty](template-functions-array.md#empty)
* [prima](template-functions-array.md#first)
* [intersezione](template-functions-array.md#intersection)
* [Ultima](template-functions-array.md#last)
* [length](template-functions-array.md#length)
* [min](template-functions-array.md#min)
* [max](template-functions-array.md#max)
* [intervallo](template-functions-array.md#range)
* [ignorare](template-functions-array.md#skip)
* [take](template-functions-array.md#take)
* [Unione](template-functions-array.md#union)

<a id="coalesce" aria-hidden="true" />
<a id="equals" aria-hidden="true" />
<a id="less" aria-hidden="true" />
<a id="lessorequals" aria-hidden="true" />
<a id="greater" aria-hidden="true" />
<a id="greaterorequals" aria-hidden="true" />

## <a name="comparison-functions"></a>Funzioni di confronto

Resource Manager include numerose funzioni per l'esecuzione di confronti nei modelli.

* [COALESCE](template-functions-comparison.md#coalesce)
* [equals](template-functions-comparison.md#equals)
* [meno](template-functions-comparison.md#less)
* [lessOrEquals](template-functions-comparison.md#lessorequals)
* [greater](template-functions-comparison.md#greater)
* [greaterOrEquals](template-functions-comparison.md#greaterorequals)

<a id="deployment" aria-hidden="true" />
<a id="parameters" aria-hidden="true" />
<a id="variables" aria-hidden="true" />

## <a name="date-functions"></a>Funzioni di data

Gestione risorse fornisce le funzioni seguenti per l'utilizzo delle date.

* [dateTimeAdd](template-functions-date.md#datetimeadd)
* [utcNow](template-functions-date.md#utcnow)

## <a name="deployment-value-functions"></a>Funzioni dei valori della distribuzione

Gestione risorse fornisce le funzioni seguenti per ottenere i valori dalle sezioni del modello e i valori relativi alla distribuzione:

* [distribuzione](template-functions-deployment.md#deployment)
* [ambiente](template-functions-deployment.md#environment)
* [parametri](template-functions-deployment.md#parameters)
* [variabili](template-functions-deployment.md#variables)

<a id="and" aria-hidden="true" />
<a id="bool" aria-hidden="true" />
<a id="if" aria-hidden="true" />
<a id="not" aria-hidden="true" />
<a id="or" aria-hidden="true" />

## <a name="logical-functions"></a>Funzioni logiche

Resource Manager fornisce le funzioni seguenti per utilizzare le condizioni logiche:

* [e](template-functions-logical.md#and)
* [bool](template-functions-logical.md#bool)
* [if](template-functions-logical.md#if)
* [non](template-functions-logical.md#not)
* [o](template-functions-logical.md#or)

<a id="add" aria-hidden="true" />
<a id="copyindex" aria-hidden="true" />
<a id="div" aria-hidden="true" />
<a id="float" aria-hidden="true" />
<a id="int" aria-hidden="true" />
<a id="minint" aria-hidden="true" />
<a id="maxint" aria-hidden="true" />
<a id="mod" aria-hidden="true" />
<a id="mul" aria-hidden="true" />
<a id="sub" aria-hidden="true" />

## <a name="numeric-functions"></a>Funzioni numeriche

Gestione risorse fornisce le funzioni seguenti per usare i numeri interi:

* [add](template-functions-numeric.md#add)
* [copyIndex](template-functions-numeric.md#copyindex)
* [div](template-functions-numeric.md#div)
* [float](template-functions-numeric.md#float)
* [int](template-functions-numeric.md#int)
* [min](template-functions-numeric.md#min)
* [max](template-functions-numeric.md#max)
* [mod](template-functions-numeric.md#mod)
* [Mul](template-functions-numeric.md#mul)
* [Sub](template-functions-numeric.md#sub)

<a id="json" aria-hidden="true" />

## <a name="object-functions"></a>Funzioni oggetto

Gestione risorse fornisce diverse funzioni per l'utilizzo di oggetti.

* [contains](template-functions-object.md#contains)
* [empty](template-functions-object.md#empty)
* [intersezione](template-functions-object.md#intersection)
* [JSON](template-functions-object.md#json)
* [length](template-functions-object.md#length)
* [Unione](template-functions-object.md#union)

<a id="extensionResourceId" aria-hidden="true" />
<a id="listkeys" aria-hidden="true" />
<a id="list" aria-hidden="true" />
<a id="providers" aria-hidden="true" />
<a id="reference" aria-hidden="true" />
<a id="resourcegroup" aria-hidden="true" />
<a id="resourceid" aria-hidden="true" />
<a id="subscription" aria-hidden="true" />
<a id="subscriptionResourceId" aria-hidden="true" />
<a id="tenantResourceId" aria-hidden="true" />

## <a name="resource-functions"></a>Funzioni delle risorse

Gestione risorse fornisce le funzioni seguenti per ottenere i valori delle risorse:

* [extensionResourceId](template-functions-resource.md#extensionresourceid)
* [listAccountSas](template-functions-resource.md#list)
* [listKeys](template-functions-resource.md#listkeys)
* [listSecrets](template-functions-resource.md#list)
* [elenco](template-functions-resource.md#list)
* [provider](template-functions-resource.md#providers)
* [riferimento](template-functions-resource.md#reference)
* [resourceGroup](template-functions-resource.md#resourcegroup) : può essere usato solo nelle distribuzioni in un gruppo di risorse.
* [resourceId](template-functions-resource.md#resourceid) : può essere usato in qualsiasi ambito, ma i parametri validi cambiano a seconda dell'ambito.
* [Subscription](template-functions-resource.md#subscription) -può essere usato solo nelle distribuzioni in un gruppo di risorse o in una sottoscrizione.
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)

<a id="base64" aria-hidden="true" />
<a id="base64tojson" aria-hidden="true" />
<a id="base64tostring" aria-hidden="true" />
<a id="concat" aria-hidden="true" />
<a id="containsstring" aria-hidden="true" />
<a id="datauri" aria-hidden="true" />
<a id="datauritostring" aria-hidden="true" />
<a id="emptystring" aria-hidden="true" />
<a id="endswith" aria-hidden="true" />
<a id="firststring" aria-hidden="true" />
<a id="guid" aria-hidden="true" />
<a id="indexof" aria-hidden="true" />
<a id="laststring" aria-hidden="true" />
<a id="lastindexof" aria-hidden="true" />
<a id="lengthstring" aria-hidden="true" />
<a id="padleft" aria-hidden="true" />
<a id="replace" aria-hidden="true" />
<a id="skipstring" aria-hidden="true" />
<a id="split" aria-hidden="true" />
<a id="startswith" aria-hidden="true" />
<a id="string" aria-hidden="true" />
<a id="substring" aria-hidden="true" />
<a id="takestring" aria-hidden="true" />
<a id="tolower" aria-hidden="true" />
<a id="toupper" aria-hidden="true" />
<a id="trim" aria-hidden="true" />
<a id="uniquestring" aria-hidden="true" />
<a id="uri" aria-hidden="true" />
<a id="uricomponent" aria-hidden="true" />
<a id="uricomponenttostring" aria-hidden="true" />

## <a name="string-functions"></a>Funzioni per i valori stringa

Gestione risorse fornisce le funzioni seguenti per usare le stringhe:

* [base64](template-functions-string.md#base64)
* [base64ToJson](template-functions-string.md#base64tojson)
* [base64ToString](template-functions-string.md#base64tostring)
* [Concat](template-functions-string.md#concat)
* [contains](template-functions-string.md#contains)
* [dataUri](template-functions-string.md#datauri)
* [dataUriToString](template-functions-string.md#datauritostring)
* [empty](template-functions-string.md#empty)
* [endsWith](template-functions-string.md#endswith)
* [prima](template-functions-string.md#first)
* [format](template-functions-string.md#format)
* [GUID](template-functions-string.md#guid)
* [indexOf](template-functions-string.md#indexof)
* [Ultima](template-functions-string.md#last)
* [lastIndexOf](template-functions-string.md#lastindexof)
* [length](template-functions-string.md#length)
* [newGuid](template-functions-string.md#newguid)
* [padLeft](template-functions-string.md#padleft)
* [replace](template-functions-string.md#replace)
* [ignorare](template-functions-string.md#skip)
* [Split](template-functions-string.md#split)
* [startsWith](template-functions-string.md#startswith)
* [string](template-functions-string.md#string)
* [sottostringa](template-functions-string.md#substring)
* [take](template-functions-string.md#take)
* [toLower](template-functions-string.md#tolower)
* [toUpper](template-functions-string.md#toupper)
* [Trim](template-functions-string.md#trim)
* [uniqueString](template-functions-string.md#uniquestring)
* [URI](template-functions-string.md#uri)
* [uriComponent](template-functions-string.md#uricomponent)
* [uriComponentToString](template-functions-string.md#uricomponenttostring)

## <a name="next-steps"></a>Passaggi successivi

* Per una descrizione delle sezioni in un modello ARM, vedere [creazione di modelli ARM](template-syntax.md)
* Per unire più modelli, vedere [Uso di modelli collegati con Gestione risorse di Azure](linked-templates.md)
* Per eseguire l'iterazione di un numero specificato di volte durante la creazione di un tipo di risorsa, vedere [creare più istanze di risorse in Azure Resource Manager](copy-resources.md).
* Per informazioni su come distribuire il modello creato, vedere [distribuire un'applicazione con i modelli ARM](deploy-powershell.md)
