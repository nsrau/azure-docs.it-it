---
title: "Concetti: flusso di dati nel connettore Internet (anteprima) funzionalità dell'API di Azure per FHIR"
description: Informazioni sul flusso di dati del connettore Internet. Il connettore di gestione delle cose inserisce, normalizza, raggruppa, trasforma e Salva in modo permanente i dati IoMT nell'API di Azure per FHIR.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: punagpal
ms.openlocfilehash: c2d150fcd35bc51478a1d7f4a0407abce1446c06
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87099156"
---
# <a name="iot-connector-preview-data-flow"></a>Flusso di dati del connettore Internet (anteprima)

Questo articolo fornisce una panoramica del flusso di dati nel connettore Internet. Verranno fornite informazioni sulle diverse fasi di elaborazione dei dati all'interno del connettore Internet che trasformano i dati dei dispositivi in risorse di [osservazione](https://www.hl7.org/fhir/observation.html) basate su FHIR.

![Flusso di dati del connettore IoT](media/concepts-iot-data-flow/iot-connector-data-flow.png)

Il diagramma precedente mostra diverse fasi del flusso di dati nel connettore Internet. 

## <a name="ingest"></a>Inserimento
L'inserimento è la prima fase in cui vengono ricevuti i dati del dispositivo nel connettore Internet. L'endpoint di inserimento per i dati del dispositivo è ospitato in un [Hub eventi di Azure](https://docs.microsoft.com/azure/event-hubs/). La piattaforma Hub eventi di Azure supporta la scalabilità elevata e la velocità effettiva con la possibilità di ricevere ed elaborare milioni di messaggi al secondo. Consente inoltre di usare i messaggi in modo asincrono, rimuovendo la necessità di attendere i dispositivi durante l'elaborazione dei dati del dispositivo.

> [!NOTE]
> JSON è l'unico formato supportato in questo momento per i dati del dispositivo.

## <a name="normalize"></a>Normalizzare
Normalize è la fase successiva in cui i dati del dispositivo vengono recuperati dall'hub eventi di Azure precedente ed elaborati con i modelli di mapping dei dispositivi. Questo processo di mapping comporta la trasformazione dei dati del dispositivo in uno schema normalizzato. 

Il processo di normalizzazione non solo semplifica l'elaborazione dei dati in fasi successive, ma offre anche la possibilità di proiettare un messaggio di input in più messaggi normalizzati. Ad esempio, un dispositivo potrebbe inviare più segni vitali per la temperatura del corpo, la frequenza degli impulsi, la pressione del sangue e la frequenza respiratoria in un singolo messaggio. Questo messaggio di input creerebbe quattro risorse FHIR separate. Ogni risorsa rappresenterebbe un segno di vitalità diverso, con il messaggio di input proiettato in quattro diversi messaggi normalizzati.

## <a name="group"></a>Group
Il gruppo è la fase successiva in cui i messaggi normalizzati disponibili dalla fase precedente vengono raggruppati con tre parametri diversi: identità del dispositivo, tipo di misura e periodo di tempo.

Il raggruppamento delle identità e dei tipi di misurazione del dispositivo Abilita l'uso del tipo di misurazione [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData) . Questo tipo fornisce un modo conciso per rappresentare una serie di misurazioni basate sul tempo da un dispositivo in FHIR. E il periodo di tempo controlla la latenza di scrittura delle risorse di osservazione generate dal connettore Internet nell'API di Azure per FHIR.

> [!NOTE]
> Il valore del periodo di tempo è impostato su 15 minuti e non può essere configurato per l'anteprima.

## <a name="transform"></a>Trasformare
Nella fase Transform i messaggi normalizzati raggruppati vengono elaborati tramite i modelli di mapping di FHIR. I messaggi che corrispondono a un tipo di modello vengono trasformati in risorse di osservazione basate su FHIR, come specificato tramite il mapping.

A questo punto, la risorsa del [dispositivo](https://www.hl7.org/fhir/device.html) , insieme alla risorsa del [paziente](https://www.hl7.org/fhir/patient.html) associata, viene recuperata anche dal server FHIR usando l'identificatore del dispositivo presente nel messaggio. Queste risorse vengono aggiunte come riferimento alla risorsa di osservazione da creare.

> [!NOTE]
> Tutti i valori di ricerca Identity vengono memorizzati nella cache una volta risolti per ridurre il carico sul server FHIR. Se si prevede di riutilizzare i dispositivi con più pazienti, è consigliabile creare una risorsa del dispositivo virtuale specifica per il paziente e inviare l'identificatore del dispositivo virtuale nel payload del messaggio. Il dispositivo virtuale può essere collegato alla risorsa effettiva del dispositivo come padre.

Se nel server FHIR non è presente alcuna risorsa dispositivo per un determinato identificatore di dispositivo, il risultato dipende dal valore `Resolution Type` impostato al momento della creazione. Se impostato su `Lookup` , il messaggio specifico viene ignorato e la pipeline continuerà a elaborare altri messaggi in arrivo. Se impostato su `Create` , il connettore Internet creerà un dispositivo Bare-Bones e le risorse del paziente nel server FHIR.  

## <a name="persist"></a>Persist
Dopo che la risorsa FHIR di osservazione è stata generata nella fase Transform, la risorsa viene salvata nell'API di Azure per FHIR. Se la risorsa FHIR è nuova, verrà creata nel server. Se la risorsa FHIR esiste già, verrà aggiornata.

## <a name="next-steps"></a>Passaggi successivi

Fare clic sotto il passaggio successivo per informazioni su come creare modelli di mapping di dispositivi e FHIR.

>[!div class="nextstepaction"]
>[Modelli di mapping del connettore IoT](iot-mapping-templates.md)


FHIR è il marchio registrato di HL7, usato con l'autorizzazione di HL7.
