---
title: Modelli di query comuni di Gemelli digitali di Azure | Microsoft Docs
description: Informazioni sui modelli comuni di query delle API di gestione di Gemelli digitali di Azure.
author: dsk-2015
manager: philmea
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 1/7/2019
ms.author: dkshir
ms.openlocfilehash: ff8638042fa10c939ff9c5fa7af99a660fcdc753
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60924737"
---
# <a name="how-to-query-azure-digital-twins-apis-for-common-tasks"></a>Come eseguire una query delle API di Gemelli digitali di Azure per trovare le attività comuni

Questo articolo illustra i modelli di query per poter eseguire scenari comuni per l'istanza di Gemelli digitali di Azure. Ciò presuppone che l'istanza di Gemelli digitali sia già in esecuzione. È possibile usare qualsiasi client REST, ad esempio Postman. 

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]


## <a name="queries-for-spaces-and-types"></a>Query per spazi e tipi

Questa sezione illustra le query per ottenere altre informazioni sugli spazi di cui è stato effettuato il provisioning. Creare richieste HTTP GET autenticate con le query di esempio, sostituendo i segnaposto con i propri valori di configurazione. 

- Ottenere spazi che sono nodi radice.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?$filter=ParentSpaceId eq null
    ```

- Ottenere uno spazio in base al nome e includere dispositivi, sensori, valori calcolati e valori dei sensori. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?name=Focus Room A1&includes=fullpath,devices,sensors,values,sensorsvalues
    ```

- Ottenere gli spazi e le relative informazioni di sensore/dispositivo, il cui padre è l'ID dello spazio specificato, e che si trovano ai livelli da due a cinque [relativi lo spazio specificato](how-to-navigate-apis.md#api-navigation). 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?spaceId=YOUR_SPACE_ID&includes=fullpath,devices,sensors,values,sensorsvalues&traverse=Down&minLevel=1&minRelative=true&maxLevel=5&maxRelative=true
    ```

- Ottenere lo spazio con l'ID specificato e includere i valori calcolati e quelli dei sensori.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?ids=YOUR_SPACE_ID&includes=Values,sensors,SensorsValues
    ```

- Ottenere le chiavi delle proprietà per un determinato spazio.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/propertykeys?spaceId=YOUR_SPACE_ID
    ```

- Ottenere gli spazi con la chiave della proprietà denominata *AreaInSqMeters* il cui valore è 30. È anche possibile eseguire operazioni di stringa, ad esempio ottenere spazi contenenti la chiave della proprietà con `name = X contains Y`.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?propertyKey=AreaInSqMeters&propertyValue=30
    ```

- Ottenere tutti i nomi con il nome *Temperature* e le dipendenze e le ontologie associate.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/types?names=Temperature&includes=space,ontologies,description,fullpath
    ```


## <a name="queries-for-roles-and-role-assignments"></a>Query per i ruoli e le assegnazioni di ruoli

Questa sezione illustra alcune query per ottenere altre informazioni sui ruoli e le relative assegnazioni. 

- Ottenere tutti i ruoli supportati da Gemelli digitali di Azure.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/roles
    ```

- Ottenere tutte le assegnazioni di ruolo nell'istanza di Gemelli digitali. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=down
    ```

- Ottenere le assegnazioni di ruolo in un percorso specifico.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/A_SPATIAL_PATH
    ```

## <a name="queries-for-devices"></a>Query per i dispositivi

Questa sezione illustra alcuni esempi di come è possibile usare le API di gestione per ottenere informazioni specifiche sui dispositivi. Tutte le chiamate API devono essere richieste HTTP GET autenticate.

- Ottenere tutti i dispositivi.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices
    ```

- Trovare gli stati di tutti i dispositivi.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/devices/statuses
    ```

- Ottenere un dispositivo specifico.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID
    ```

- Ottenere tutti i dispositivi collegati allo spazio radice.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?maxLevel=1
    ```

- Ottenere tutti i dispositivi collegati agli spazi ai livelli da 2 a 4.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4
    ```

- Ottenere tutti i dispositivi direttamente collegati a un ID spazio specifico.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID
    ```

- Ottenere tutti i dispositivi collegati a uno spazio specifico e ai discendenti.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down
    ```

- Ottenere tutti i dispositivi collegati ai discendenti di uno spazio, escluso tale spazio.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true
    ```

- Ottenere tutti i dispositivi collegati agli elementi figlio diretti di uno spazio.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true
    ```

- Ottenere tutti i dispositivi collegati a uno dei predecessori di uno spazio.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Up&maxLevel=-1&maxRelative=true
    ```

- Ottenere tutti i dispositivi collegati ai discendenti di uno spazio di livello pari o inferiore a 5.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&maxLevel=5
    ```

- Ottenere tutti i dispositivi collegati agli spazi allo stesso livello dello spazio con ID *YOUR_SPACE_ID*.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true
    ```

- Ottenere la stringa di connessione dell'hub IoT per il dispositivo.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID?includes=ConnectionString
    ```

- Ottenere il dispositivo con l'ID hardware specifico, inclusi i sensori collegati.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?hardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=sensors
    ```

- Ottenere i sensori per tipi di dati specifici, in questo caso *Motion* e *Temperature*.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/sensors?dataTypes=Motion,Temperature
    ```

## <a name="queries-for-matchers-and-user-defined-functions"></a>Query per matcher e funzioni definite dall'utente 

- Ottenere tutti i matcher di cui è stato effettuato il provisioning e i relativi ID.

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers
    ```

- Ottenere i dettagli su un matcher specifico, inclusi gli spazi e la funzione definita dall'utente associata.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID?includes=description, conditions, fullpath, userdefinedfunctions, space
    ```

- Confrontare un matcher con un sensore e abilitare la registrazione per scopi di debug. La restituzione di questo messaggio HTTP GET indica se il matcher e il sensore appartengono al tipo di dati. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID/evaluate/YOUR_SENSOR_ID?enableLogging=true
    ```

- Ottenere l'ID delle funzioni definite dall'utente. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions
    ```

- Ottenere i contenuti di una funzione definita dall'utente specifica 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions/YOUR_USER_DEFINED_FUNCTION_ID/contents
    ```


## <a name="queries-for-users"></a>Query per gli utenti

Questa sezione illustra alcune query delle API di esempio per la gestione degli utenti in Gemelli digitali di Azure. Creare una richiesta HTTP GET sostituendo i segnaposto con i propri valori di configurazione. 

- Ottenere tutti gli utenti. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users
    ```

- Ottenere un utente specifico.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users/ANY_USER_ID
    ```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come eseguire l'autenticazione con l'API di gestione, vedere [Connettersi alle API ed eseguire l'autenticazione](./security-authenticating-apis.md).

Per visualizzare tutti gli endpoint delle API, vedere [Come usare Swagger per Gemelli digitali](./how-to-use-swagger.md).
