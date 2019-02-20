---
title: Usare chiavi univoche in Azure Cosmos DB
description: Informazioni su come usare chiavi univoche nel database di Azure Cosmos
author: aliuy
ms.author: andrl
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.reviewer: sngun
ms.openlocfilehash: 3a7133d9c092ab8ad8a4bc585e3b0df2b8ca1234
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2019
ms.locfileid: "55999267"
---
# <a name="unique-key-constraints-in-azure-cosmos-db"></a>Vincoli di chiave univoca in Azure Cosmos DB

Le chiavi univoche aggiungono un livello di integrità dei dati a un contenitore di Azure Cosmos. I criteri di chiave univoca si creano al momento della creazione di un contenitore di Azure Cosmos. Con le chiavi univoche si garantisce l'univocità di uno o più valori all'interno di una partizione logica. Si può anche garantire l'univocità per [chiave di partizione](partition-data.md). 

Dopo aver creato un contenitore con criteri di chiave univoca, non è possibile creare o aggiornare elementi duplicati in una partizione logica, come specificato dal vincolo di chiave univoca. La chiave di partizione combinata con la chiave univoca garantisce l'univocità di un elemento all'interno dell'ambito del contenitore.

Si consideri ad esempio un contenitore di Azure Cosmos con l'indirizzo di posta elettronica come vincolo di chiave univoca e `CompanyID` come chiave di partizione. Quando si configura l'indirizzo di posta elettronica dell'utente con una chiave univoca, ogni elemento ha un indirizzo di posta elettronica univoco all'interno di un determinato `CompanyID`. Non è possibile creare due elementi con indirizzi di posta elettronica duplicati e con lo stesso valore di chiave di partizione. 

Per creare elementi con lo stesso indirizzo di posta elettronica, ma non con la stessa combinazione di nome, cognome e indirizzo di posta elettronica, aggiungere altri percorsi ai criteri di chiave univoca. Anziché creare una chiave univoca basata sull'indirizzo di posta elettronica, è anche possibile crearne una composta da nome, cognome e indirizzo di posta elettronica. Questa chiave è detta chiave univoca composta. In questo caso è consentita ogni combinazione univoca dei tre valori all'interno di un determinato elemento `CompanyID`. 

Il contenitore può ad esempio contenere elementi con i valori seguenti, in cui ogni elemento rispetta il vincolo di chiave univoca.

|CompanyID|Nome|Cognome|Indirizzo di posta elettronica|
|---|---|---|---|
|Contoso|Gaby|Duperre|gaby@contoso.com |
|Contoso|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Ivan|Duperre|gaby@fabrikam.com|
|Fabrikam|   |Duperre|gaby@fabraikam.com|
|Fabrikam|   |   |gaby@fabraikam.com|

Se si cerca di inserire un altro elemento con le combinazioni elencate nella tabella riportata sopra, viene visualizzato un messaggio di errore che indica che il vincolo di chiave univoca non è stato rispettato. Il messaggio restituito è "Resource with specified ID or name already exists" (Una risorsa con l'ID specificato esiste già) oppure "Resource with specified ID, name, or unique index already exists" (Una risorsa con l'ID, il nome o l'indice univoco specificato esiste già). 

## <a name="define-a-unique-key"></a>Definire una chiave univoca

È possibile definire chiavi univoche solo quando si crea un contenitore di Azure Cosmos. Una chiave univoca ha come ambito una partizione logica. Nell'esempio precedente, se il contenitore viene partizionato in base al CAP, risulteranno elementi duplicati in ogni partizione logica. Durante la creazione di chiavi univoche, prendere in considerazione le proprietà seguenti:

* Non è possibile aggiornare un contenitore esistente in modo che usi una chiave univoca diversa. In altre parole, una volta creato un contenitore con criteri di chiave univoca, non è possibile modificarli.

* Per impostare una chiave univoca per un contenitore esistente, creare un nuovo contenitore con il vincolo di chiave univoca. Usare lo strumento di migrazione dei dati appropriato per spostare i dati dal contenitore esistente a quello nuovo. Per i contenitori SQL usare l'[Utilità di migrazione dati](import-data.md) per spostare i dati. Per i contenitori MongoDB usare [mongoimport.exe o mongorestore.exe](mongodb-migrate.md) per spostare i dati.

* I criteri di chiave univoca possono contenere un massimo di 16 valori di percorso. Ad esempio, i valori possono essere /firstName, /lastName e /address/zipCode. I singoli criteri di chiave univoca possono avere un massimo di 10 vincoli o combinazioni di vincoli di chiave univoca. I percorsi combinati di ogni vincolo di chiave univoca non devono superare i 60 byte. Nell'esempio precedente, nome, cognome e indirizzo di posta elettronica rappresentano un unico vincolo, che usa 3 dei 16 possibili percorsi.

* Quando un contenitore include criteri di chiave univoca, i costi delle unità richieste (UR) per creare, aggiornare ed eliminare un elemento sono leggermente superiori.

* Le chiavi univoche di tipo sparse non sono supportate. Se non sono definiti alcuni valori di percorso univoci, questi vengono considerati come valori Null facenti parte del vincolo di univocità. Per questo motivo può essere presente un solo elemento con valore Null per soddisfare il vincolo.

* I nomi delle chiavi univoche distinguono tra maiuscole e minuscole. Si consideri ad esempio un contenitore con il vincolo di chiave univoca impostato su /address/zipcode. Se i dati includono un campo denominato ZipCode, Azure Cosmos DB inserisce "null" come chiave univoca, perché "zipcode" non è uguale a "ZipCode". A causa di questa distinzione tra maiuscole e minuscole, tutti gli altri record con ZipCode non possono essere inseriti, in quanto il valore "null" duplicato viola il vincolo di chiave univoca.

## <a name="next-steps"></a>Passaggi successivi

* [Partizionamento e scalabilità orizzontale in Azure Cosmos DB](partition-data.md)
