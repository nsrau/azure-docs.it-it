---
title: Usare chiavi univoche in Azure Cosmos DB
description: Informazioni su come usare chiavi univoche nel database Azure Cosmos DB
author: aliuy
ms.author: andrl
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.reviewer: sngun
ms.openlocfilehash: 20e5c96110f07d8eaec218ed167c87a48fd65782
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54037379"
---
# <a name="unique-keys-in-azure-cosmos-db"></a>Chiavi univoche in Azure Cosmos DB

Le chiavi univoche consentono di aggiungere un livello di integrità dei dati in un contenitore Cosmos. Creare criteri di chiave univoca quando si crea un contenitore Cosmos. Con le chiavi univoche è possibile garantire l'univocità di uno o più valori all'interno di una partizione logica (è possibile garantire l'univocità per [chiave di partizione](partition-data.md)). Dopo aver creato un contenitore con criteri di chiave univoca, non è possibile creare o aggiornare elementi duplicati in una partizione logica, come specificato dal vincolo di chiave univoca. La chiave di partizione combinata con la chiave univoca garantisce l'univocità di un elemento all'interno dell'ambito del contenitore.

Si consideri ad esempio un contenitore Cosmos con indirizzo di posta elettronica come vincolo di chiave univoca e `CompanyID` come chiave di partizione. Tramite la configurazione dell'indirizzo di posta elettronica dell'utente come chiave univoca si garantisce che ogni elemento abbia un indirizzo di posta elettronica univoco all'interno di un determinato elemento `CompanyID`. Non è possibile creare due elementi con indirizzi di posta elettronica duplicati e con lo stesso valore di chiave di partizione.  

Se si vuole consentire agli utenti di creare più elementi con lo stesso indirizzo di posta elettronica, ma non con la stessa combinazione di nome, cognome e indirizzo di posta elettronica, è possibile aggiungere altri percorsi ai criteri di chiave univoca. Anziché creare una chiave univoca basata sull'indirizzo di posta elettronica, è anche possibile crearne una composta da nome, cognome e indirizzo di posta elettronica (chiave univoca composta). In questo caso è consentita ogni combinazione univoca dei tre valori all'interno di un determinato elemento `CompanyID`. Il contenitore può ad esempio contenere elementi con i valori seguenti, in cui ogni elemento rispetta il vincolo di chiave univoca.

|CompanyID|Nome|Cognome|Indirizzo di posta elettronica|
|---|---|---|---|
|Contoso|Gaby|Duperre|gaby@contoso.com |
|Contoso|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Ivan|Duperre|gaby@fabrikam.com|
|Fabrikam|   |Duperre|gaby@fabraikam.com|
|Fabrikam|   |   |gaby@fabraikam.com|

Se si prova a inserire un altro elemento con una qualsiasi delle combinazioni elencate nella tabella precedente, verrà visualizzato un messaggio di errore per segnalare che il vincolo di chiave univoca non è stato soddisfatto. Il messaggio restituito sarà "Resource with specified ID or name already exists" (Una risorsa con l'ID specificato esiste già) oppure "Resource with specified ID, name, or unique index already exists" (Una risorsa con l'ID, il nome o l'indice univoco specificato esiste già).  

## <a name="defining-a-unique-key"></a>Definizione di una chiave univoca

È possibile definire chiavi univoche solo quando si crea un contenitore Cosmos. Una chiave univoca ha come ambito una partizione logica. Nell'esempio precedente, se il contenitore viene partizionato in base al CAP, risulteranno elementi duplicati in ogni partizione logica. Durante la creazione di chiavi univoche, prendere in considerazione le proprietà seguenti:

* Non è possibile aggiornare un contenitore esistente per l'uso di una chiave univoca diversa. In altre parole, una volta creato un contenitore con criteri di chiave univoca, non è possibile modificarli.

* Se si intende impostare una chiave univoca per un contenitore esistente, è necessario creare un nuovo contenitore con il vincolo di chiave univoca e usare l'utilità di migrazione dati appropriata per spostare i dati dal contenitore esistente al nuovo contenitore. Per i contenitori SQL usare l'[Utilità di migrazione dati](import-data.md) per spostare i dati. Per i contenitori MongoDB usare [mongoimport.exe o mongorestore.exe](mongodb-migrate.md) per spostare i dati.

* I criteri di chiave univoca possono avere un massimo di 16 valori di percorso (ad esempio, /firstName, /lastName, /address/zipCode). I singoli criteri di chiave univoca possono avere un massimo di 10 vincoli o combinazioni di vincoli di chiave univoca e i percorsi combinati per ogni vincolo di indice univoco non devono superare i 60 byte. Nell'esempio precedente, nome, cognome e indirizzo di posta elettronica rappresentano un unico vincolo e vengono usati tre dei 16 possibili percorsi.

* Quando un contenitore include criteri di chiave univoca, i costi delle unità richieste (UR) per creare, aggiornare ed eliminare un elemento sono leggermente superiori.

* Le chiavi univoche di tipo sparse non sono supportate. Se non sono definiti alcuni valori di percorso univoci, questi vengono considerati come valori Null facenti parte del vincolo di univocità. Di conseguenza, può essere presente un solo elemento con valore Null per soddisfare il vincolo.

* I nomi delle chiavi univoche distinguono tra maiuscole e minuscole. Si consideri ad esempio un contenitore con il vincolo di chiave univoca impostato su /address/zipcode. Se i dati includono un campo denominato ZipCode, Cosmos DB inserisce "null" come chiave univoca, perché "zipcode" non è uguale a "ZipCode". A causa di questa distinzione tra maiuscole e minuscole, tutti gli altri record con ZipCode non potranno essere inseriti, in quanto i "null" duplicati violeranno il vincolo di chiave univoca.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulle [partizioni logiche](partition-data.md)
