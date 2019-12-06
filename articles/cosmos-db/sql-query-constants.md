---
title: Costanti SQL in Azure Cosmos DB
description: Informazioni sulla modalità di utilizzo delle costanti di query SQL in Azure Cosmos DB per rappresentare un valore di dati specifico
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tisande
ms.openlocfilehash: cca62c358037dbe99fd16746ee081b1540161df2
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873421"
---
# <a name="azure-cosmos-db-sql-query-constants"></a>Azure Cosmos DB costanti di query SQL  

 Una costante, nota anche come valore letterale o scalare, è un simbolo che rappresenta un valore di dati specifico. Il formato di una costante dipende dal tipo di dati del valore che rappresenta.  
  
 **Tipi di dati scalari supportati:**  
  
|**Tipo**|**Ordine dei valori**|  
|-|-|  
|**Undefined**|Singolo valore: **non definito**|  
|**Null**|Singolo valore: **null**|  
|**Boolean**|Valori: **false**, **true**.|  
|**Number**|Un numero a virgola mobile e precisione doppia, standard IEEE 754.|  
|**Stringa**|Una sequenza di zero o più caratteri Unicode. Le stringhe devono essere racchiuse tra virgolette singole o doppie.|  
|**Array**|Una sequenza di zero o più elementi. Ogni elemento può essere un valore di qualsiasi tipo di dati scalari, ad eccezione di **undefined**.|  
|**Object**|Un set non ordinato di zero o più coppie nome/valore. Il nome è una stringa Unicode, il valore può essere di qualsiasi tipo di dati scalare, tranne **Undefined**.|  
  
## <a name="bk_syntax"></a>Sintassi
  
```sql  
<constant> ::=  
   <undefined_constant>  
     | <null_constant>   
     | <boolean_constant>   
     | <number_constant>   
     | <string_constant>   
     | <array_constant>   
     | <object_constant>   
  
<undefined_constant> ::= undefined  
  
<null_constant> ::= null  
  
<boolean_constant> ::= false | true  
  
<number_constant> ::= decimal_literal | hexadecimal_literal  
  
<string_constant> ::= string_literal  
  
<array_constant> ::=  
    '[' [<constant>][,...n] ']'  
  
<object_constant> ::=   
   '{' [{property_name | "property_name"} : <constant>][,...n] '}'  
  
```  
  
##  <a name="bk_arguments"></a> Argomenti
  
* `<undefined_constant>; Undefined`  
  
  Rappresenta un valore non definito di tipo Undefined.  
  
* `<null_constant>; null`  
  
  Rappresenta un valore **null** di tipo **Null**.  
  
* `<boolean_constant>`  
  
  Rappresenta una costante di tipo Boolean.  
  
* `false`  
  
  Rappresenta un valore **false** di tipo booleano.  
  
* `true`  
  
  Rappresenta un valore **true** di tipo booleano.  
  
* `<number_constant>`  
  
  Rappresenta una costante.  
  
* `decimal_literal`  
  
  I valori letterali decimali sono numeri rappresentati usando la notazione decimale o la notazione scientifica.  
  
* `hexadecimal_literal`  
  
  I valori letterali esadecimali sono numeri rappresentati usando il prefisso "0x" seguito da una o più cifre esadecimali.  
  
* `<string_constant>`  
  
  Rappresenta una costante di tipo String.  
  
* `string _literal`  
  
  I valori letterali stringa sono stringhe Unicode rappresentate da una sequenza di zero o più caratteri Unicode o sequenze di escape. I valori letterali stringa sono racchiusi tra virgolette singole (apostrofo: ') o virgolette doppie (virgolette inglesi: ").  
  
  Sono consentite le sequenze di escape seguenti:  
  
|**Sequenza di escape**|**Descrizione**|**Carattere Unicode**|  
|-|-|-|  
|\\'|apostrofo (')|U + 0027|  
|\\"|virgolette doppie (")|U + 0022|  
|\\\ |barra rovesciata (\\)|U + 005C|  
|\\/|barra (/)|U + 002F|  
|\b|BACKSPACE|U + 0008|  
|\f|avanzamento carta|U + 000C|  
|\n|avanzamento riga|U + 000A|  
|\r|ritorno a capo|U + 000D|  
|\t|TAB|U + 0009|  
|\uXXXX|Carattere Unicode definito da 4 cifre esadecimali.|U + XXXX|  

## <a name="next-steps"></a>Passaggi successivi

- [Esempi relativi a Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Dati del documento modello](modeling-data.md)
