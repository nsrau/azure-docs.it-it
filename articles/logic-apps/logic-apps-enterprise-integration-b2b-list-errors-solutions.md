---
title: 'Elenco di errori e soluzioni per le app per la logica B2B: servizio App di Azure | Microsoft Docs'
description: Elenco di errori e soluzioni per le app per la logica B2B
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/02/2017
ms.author: LADocs; padmavc
ms.translationtype: Human Translation
ms.sourcegitcommit: 80be19618bd02895d953f80e5236d1a69d0811af
ms.openlocfilehash: 1865d75f1b4c2aa18d5a3130f639572d19563b3e
ms.contentlocale: it-it
ms.lasthandoff: 06/07/2017


---

# <a name="logic-apps-b2b-list-of-errors-and-solutions"></a>Elenco di errori e soluzioni per le app per la logica B2B  
Questo articolo consente di risolvere gli errori che si possono verificare negli scenari con le app per la logica B2B e suggerisce le azioni appropriate per la correzione degli errori.


## <a name="agreement-resolution"></a>Risoluzione dell'accordo

### <a name="no-agreement-found"></a>* Non sono stati trovati accordi 

|   |   |  
|---|---|
| Descrizione dell'errore | Non sono stati trovati accordi con parametri di risoluzione del contratto|    
| Azione utente | L'accordo deve essere aggiunto all'account di integrazione con le identità di business concordate.</br> Le identità di business devono corrispondere agli ID dei messaggi di input|  
|   |   |

### <a name="-no-agreement-found-with-identities"></a>* Non sono stati trovati accordi con le identità

|   |   | 
|---|---|
| Descrizione dell'errore | Non sono stati trovati accordi con le identità: 'AS2Identity'::'Partner1' e 'AS2Identity'::'Partner3'| 
| Azione utente | Configurata un'intestazione AS2-From o AS2-To non valida per l'accordo. </br> Correggere l'intestazione AS2-From o AS2-To del messaggio AS2 o l'accordo in modo da assicurare la corrispondenza degli ID AS2 presenti nelle intestazioni del messaggio AS2 con le configurazioni dell'accordo |
|   |   |     

## <a name="as2"></a>AS2

### <a name="-missing-as2-message-headers"></a>* Intestazioni del messaggio AS2 mancanti  

|   |   |  
|---|---|
| Descrizione dell'errore| Intestazioni AS2 non valide. Una delle intestazioni "AS2-To" o "AS2-From" è vuota| 
| Azione utente | Ricevuto un messaggio AS2 che non contiene l'intestazione AS2-From o AS2-To o entrambe le intestazioni. </br> Controllare le intestazioni AS2-From e AS2-To del messaggio AS2 e correggerle in base alla configurazione dell'accordo |
|  |  | 


### <a name="-missing-as2-message-body-and-headers"></a>* Corpo e intestazioni del messaggio AS2 mancanti    

|   |   |  
|---|---|
| Descrizione dell'errore| Il contenuto della richiesta è null o vuoto | 
| Azione utente | Ricevuto un messaggio AS2 che non contiene il corpo del messaggio |
|  |  | 

### <a name="-as2-message-decryption-failure"></a>* Errore di decrittografia del messaggio AS2

|   |   | 
|---|---|
| Descrizione dell'errore |  [processed/Error: decryption-failed] | 
| Azione utente | Aggiungere @base64ToBinary a AS2Message prima dell'invio al partner 
```java
            "HTTP": {
                "inputs": {
                    "body": "@base64ToBinary(body('Encode_to_AS2_message')?['AS2Message']?['Content'])",
                    "headers": "@body('Encode_to_AS2_message')?['AS2Message']?['OutboundHeaders']",
                    "method": "POST",
                    "uri": "xxxxx.xxx"
                },
                
``` 

### <a name="-mdn-decryption-failure"></a>* Errore di decrittografia della notifica sulla ricezione del messaggio

|   |   | 
|---|---|
| Descrizione dell'errore |  [processed/Error: decryption-failed] | 
| Azione utente | Aggiungere @base64ToBinary alla notifica sulla ricezione del messaggio prima dell'invio al partner 
```java
            "Response": {
                "inputs": {
                    "body": "@base64ToBinary(body('Decode_AS2_message')?['OutgoingMDN']?['Content'])",
                    "headers": "@body('Decode_AS2_message')?['OutgoingMDN']?['OutboundHeaders']",
                    "statusCode": 200
                },
                
``` 

### <a name="-missing-signing-certificate"></a>* Certificato di firma mancante

|   |   |  
|---|---|
| Descrizione dell'errore| Certificato di firma non configurato per l'entità AS2. </br> AS2-From: partner1 AS2-To: partner2 | 
| Azione utente | Configurare le impostazioni dell'accordo AS2 con il certificato corretto per la firma |
|  |  | 

## <a name="x12-and-edifact"></a>X12 ed EDIFACT

### <a name="-leading-or-trailing-space-found"></a>* Trovato spazio iniziale o finale    
    
|   |   | 
|---|---|
| Descrizione dell'errore | Errore rilevato durante l'analisi. Il set di transazioni Edifact con ID "123456" contenuto nell'interscambio (senza gruppo) con ID "987654", ID mittente "Partner1", ID ricevitore "Partner2" viene sospeso con i seguenti errori: trovato separatore iniziale o finale |
| Azione utente | Configurare le impostazioni dell'accordo in modo da consentire lo spazio iniziale e finale. </br> Modificare le impostazioni dell'accordo in modo che consentano lo spazio iniziale e finale |
|   |   |

![consentire spazio](./media/logic-apps-enterprise-integration-b2b-list-errors-solutions/leadingandtrailing.png)

### <a name="-duplicate-check-has-enabled-in-the-agreement"></a>* Controllo duplicati abilitato nell'accordo

|   |   | 
|---|---| 
| Descrizione dell'errore | Numero di controllo duplicato |
| Azione utente | Questo errore indica che il messaggio ricevuto contiene numeri di controllo duplicati. </br> Correggere il numero di controllo e inviare nuovamente il messaggio |
|   |   |

### <a name="-missing-schema-in-the-agreement"></a>* Schema mancante nell'accordo

|   |   | 
|---|---| 
| Descrizione dell'errore | Errore rilevato durante l'analisi. Il set di transazioni X12 con ID "564220001" contenuto nel gruppo funzionale con ID "56422", in interscambio con ID "000056422", con ID mittente "12345678       ", ID ricevitore "87654321       " viene sospeso con i seguenti errori: "Il messaggio presenta un tipo di documento non previsto e non è stato risolto in nessuno degli schemi esistenti configurati nell'accordo" |
| Azione utente | Configurare lo schema nelle impostazioni dell'accordo  |
|   |   |

### <a name="-incorrect-schema-in-the-agreement"></a>* Schema non corretto nell'accordo

|   |   | 
|---|---| 
| Descrizione dell'errore | Il messaggio ha un tipo di documento sconosciuto e non è stato risolto in nessuno degli schemi esistenti configurati nell'accordo. |
| Azione utente | Configurare lo schema corretto nelle impostazioni dell'accordo  |
|   |   |

## <a name="flat-file"></a>File flat

### <a name="-input-message-with-no-body"></a>* Messaggio di input senza corpo

|   |   | 
|---|---|
| Descrizione dell'errore | Modello non valido. Impossibile elaborare le espressioni del linguaggio del modello negli input dell'azione "Flat_File_Decoding", riga "1" e colonna "1902". Per la proprietà obbligatoria "content" è previsto un valore ma risulta null. Percorso ".'. |
| Azione utente | Questo errore indica che il messaggio di input non contiene un corpo |
|   |   | 

## <a name="learn-more"></a>Altre informazioni
[Altre informazioni su Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
