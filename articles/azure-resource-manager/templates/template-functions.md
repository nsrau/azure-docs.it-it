---
title: Funzioni di modello
description: Vengono descritte le funzioni da utilizzare in un modello di gestione risorse di Azure per recuperare valori, lavorare con stringhe e valori numerici, e recuperare informazioni sulla distribuzione.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 1d2789e59c091b4e6c39be48b83fe610a592abe1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156260"
---
# <a name="arm-template-functions"></a>Funzioni del modello ARM

Questo articolo descrive tutte le funzioni che è possibile usare in un modello di Azure Resource Manager (ARM). Per informazioni sull'utilizzo delle funzioni nel modello, vedere [Sintassi](template-expressions.md)del modello .

Per creare funzioni personalizzate, vedere [Funzioni definite dall'utente](template-syntax.md#functions).

La maggior parte delle funzioni funziona allo stesso modo quando viene distribuita in un gruppo di risorse, una sottoscrizione, un gruppo di gestione o un tenant. Alcune funzioni non possono essere utilizzate in tutti gli ambiti. Sono annotati negli elenchi qui sotto.

<a id="array" aria-hidden="true" />
<a id="coalesce" aria-hidden="true" />
<a id="concatarray" aria-hidden="true" />
<a id="contains" aria-hidden="true" />
<a id="createarray" aria-hidden="true" />
<a id="empty" aria-hidden="true" />
<a id="first" aria-hidden="true" />
<a id="intersection" aria-hidden="true" />
<a id="json" aria-hidden="true" />
<a id="last" aria-hidden="true" />
<a id="length" aria-hidden="true" />
<a id="min" aria-hidden="true" />
<a id="max" aria-hidden="true" />
<a id="range" aria-hidden="true" />
<a id="skip" aria-hidden="true" />
<a id="take" aria-hidden="true" />
<a id="union" aria-hidden="true" />

## <a name="array-and-object-functions"></a>Funzioni di array e di oggetto

Resource Manager include numerose funzioni per gestire gli array e gli oggetti.

* [Matrice](template-functions-array.md#array)
* [coalesce](template-functions-array.md#coalesce)
* [concat](template-functions-array.md#concat)
* [Contiene](template-functions-array.md#contains)
* [createArray (matrice)](template-functions-array.md#createarray)
* [Vuoto](template-functions-array.md#empty)
* [Prima](template-functions-array.md#first)
* [Intersezione](template-functions-array.md#intersection)
* [Json](template-functions-array.md#json)
* [Ultima](template-functions-array.md#last)
* [length](template-functions-array.md#length)
* [Minimo](template-functions-array.md#min)
* [Massimo](template-functions-array.md#max)
* [Gamma](template-functions-array.md#range)
* [skip](template-functions-array.md#skip)
* [take](template-functions-array.md#take)
* [Unione](template-functions-array.md#union)

<a id="equals" aria-hidden="true" />
<a id="less" aria-hidden="true" />
<a id="lessorequals" aria-hidden="true" />
<a id="greater" aria-hidden="true" />
<a id="greaterorequals" aria-hidden="true" />

## <a name="comparison-functions"></a>Funzioni di confronto

Resource Manager include numerose funzioni per l'esecuzione di confronti nei modelli.

* [equals](template-functions-comparison.md#equals)
* [Meno](template-functions-comparison.md#less)
* [lessOrEquals](template-functions-comparison.md#lessorequals)
* [greater](template-functions-comparison.md#greater)
* [greaterOrEquals](template-functions-comparison.md#greaterorequals)

<a id="deployment" aria-hidden="true" />
<a id="parameters" aria-hidden="true" />
<a id="variables" aria-hidden="true" />

## <a name="deployment-value-functions"></a>Funzioni dei valori della distribuzione

Gestione risorse fornisce le funzioni seguenti per ottenere i valori dalle sezioni del modello e i valori relativi alla distribuzione:

* [Distribuzione](template-functions-deployment.md#deployment)
* [Ambiente](template-functions-deployment.md#environment)
* [Parametri](template-functions-deployment.md#parameters)
* [Variabili](template-functions-deployment.md#variables)

<a id="and" aria-hidden="true" />
<a id="bool" aria-hidden="true" />
<a id="if" aria-hidden="true" />
<a id="not" aria-hidden="true" />
<a id="or" aria-hidden="true" />

## <a name="logical-functions"></a>Funzioni logiche

Resource Manager fornisce le funzioni seguenti per utilizzare le condizioni logiche:

* [E](template-functions-logical.md#and)
* [bool](template-functions-logical.md#bool)
* [if](template-functions-logical.md#if)
* [not](template-functions-logical.md#not)
* [O](template-functions-logical.md#or)

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

* [aggiungi](template-functions-numeric.md#add)
* [copyIndex](template-functions-numeric.md#copyindex)
* [div](template-functions-numeric.md#div)
* [Galleggiante](template-functions-numeric.md#float)
* [Int](template-functions-numeric.md#int)
* [Minimo](template-functions-numeric.md#min)
* [Massimo](template-functions-numeric.md#max)
* [Mod](template-functions-numeric.md#mod)
* [mul](template-functions-numeric.md#mul)
* [Sub](template-functions-numeric.md#sub)

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

* [extensionResourceId (idEstensione)extensionResourceId](template-functions-resource.md#extensionresourceid)
* [listAccountSas](template-functions-resource.md#list)
* [listKeys](template-functions-resource.md#listkeys)
* [listSecrets](template-functions-resource.md#list)
* [l'elenco di indirizzi](template-functions-resource.md#list)
* [Provider](template-functions-resource.md#providers)
* [Riferimento](template-functions-resource.md#reference)
* [resourceGroup](template-functions-resource.md#resourcegroup) - può essere usato solo nelle distribuzioni in un gruppo di risorse.resourceGroup - can only be used in deployments to a resource group.
* [resourceId](template-functions-resource.md#resourceid) - può essere utilizzato in qualsiasi ambito, ma i parametri validi cambiano a seconda dell'ambito.
* [sottoscrizione](template-functions-resource.md#subscription) - può essere usato solo nelle distribuzioni in un gruppo di risorse o in una sottoscrizione.subscription - can only be used in deployments to a resource group or subscription.
* [subscriptionResourceId (informazioni in stato inabbonamentoResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId (informazioni in base al nome in com](template-functions-resource.md#tenantresourceid)

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
* [concat](template-functions-string.md#concat)
* [Contiene](template-functions-string.md#contains)
* [dataUri (informazioni in base all'](template-functions-string.md#datauri)
* [dataUriToString](template-functions-string.md#datauritostring)
* [Vuoto](template-functions-string.md#empty)
* [endsWith](template-functions-string.md#endswith)
* [Prima](template-functions-string.md#first)
* [Formato](template-functions-string.md#format)
* [Guid](template-functions-string.md#guid)
* [indiceDi](template-functions-string.md#indexof)
* [Ultima](template-functions-string.md#last)
* [lastIndexOf](template-functions-string.md#lastindexof)
* [length](template-functions-string.md#length)
* [Newguid](template-functions-string.md#newguid)
* [padLeft](template-functions-string.md#padleft)
* [Sostituire](template-functions-string.md#replace)
* [skip](template-functions-string.md#skip)
* [diviso](template-functions-string.md#split)
* [Startswith](template-functions-string.md#startswith)
* [Stringa](template-functions-string.md#string)
* [substring](template-functions-string.md#substring)
* [take](template-functions-string.md#take)
* [Tolower](template-functions-string.md#tolower)
* [Toupper](template-functions-string.md#toupper)
* [Tagliare](template-functions-string.md#trim)
* [uniqueString](template-functions-string.md#uniquestring)
* [Uri](template-functions-string.md#uri)
* [uriComponent](template-functions-string.md#uricomponent)
* [uriComponentToString](template-functions-string.md#uricomponenttostring)
* [utcNow](template-functions-string.md#utcnow)

## <a name="next-steps"></a>Passaggi successivi

* Per una descrizione delle sezioni di un modello ARM, vedere [Authoring ARM templates](template-syntax.md)
* Per unire più modelli, vedere [Uso di modelli collegati con Gestione risorse di Azure](linked-templates.md)
* Per scorrere un numero specificato di volte durante la creazione di un tipo di risorsa, vedere [Creare più istanze di risorse in Azure Resource Manager.To](copy-resources.md)iterate a specified number of times when creating a type of resource, see Create multiple instances of resources in Azure Resource Manager.
* Per informazioni su come distribuire il modello creato, vedere [Distribuire un'applicazione con modelli ARMTo](deploy-powershell.md) see how to deploy the template you've created, see Deploy an application with ARM templates
