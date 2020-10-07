---
title: Registro schemi di Azure in Hub eventi (anteprima)
description: Questo articolo fornisce una panoramica del supporto di Registro schemi in Hub eventi di Azure (anteprima).
ms.topic: overview
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: a0cc54f6ada1a5900e817c8e22fc192cd73f6550
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91653571"
---
# <a name="azure-schema-registry-in-event-hubs-preview"></a>Registro schemi di Azure in Hub eventi (anteprima)
In molti scenari di streaming e messaggistica di eventi, il payload dell'evento o del messaggio contiene dati strutturati che vengono serializzati o deserializzati usando un formato basato su schemi come Apache Avro. Sia i mittenti che i destinatari potrebbero voler convalidare l'integrità dei dati con un documento dello schema come con lo schema JSON. Per i formati basati su schemi, rendere lo schema disponibile per il consumer di messaggi è un prerequisito per consentire al consumer di deserializzare i dati. 

Il **Registro schemi di Azure** è una funzionalità di Hub eventi, che fornisce un repository centrale per i documenti dello schema per le applicazioni guidate dagli eventi e incentrate sulla messaggistica. Fornisce alle applicazioni producer e consumer la flessibilità di scambiare dati senza dover gestire e condividere lo schema tra loro, nonché di evolversi a velocità diverse. Il Registro schemi fornisce inoltre un framework di governance semplice per gli schemi riutilizzabili e definisce la relazione tra gli schemi tramite un costrutto di raggruppamento (gruppi di schemi).

> [!NOTE]
> - La funzionalità **Registro schemi** è attualmente disponibile in **anteprima** e non è consigliata per i carichi di lavoro di produzione.
> - La funzionalità è disponibile solo nei livelli **Standard** e **Dedicato**, non nel livello **Basic**.

Con i framework di serializzazione basati su schemi come Apache Avro, l'esternalizzazione dei metadati di serializzazione in schemi condivisi può anche aiutare a ridurre in modo significativo il sovraccarico per messaggio delle informazioni sul tipo e dei nomi dei campi inclusi in ogni set di dati, come avviene con i formati con tag come JSON. Avere schemi archiviati insieme agli eventi e all'interno dell'infrastruttura di gestione degli eventi garantisce che i metadati necessari per la serializzazione/deserializzazione siano sempre raggiungibili e che gli schemi non possano essere posizionati in modo errato. 

## <a name="event-hubs-namespace"></a>Spazio dei nomi di Hub eventi
Uno spazio dei nomi di Hub eventi può ora ospitare gruppi di schemi insieme a hub eventi (o argomenti Kafka). Ospita un registro schemi e può avere più gruppi di schemi. Nonostante sia ospitato in Hub eventi di Azure, il registro schemi può essere usato universalmente con tutti i servizi di messaggistica di Azure e con qualsiasi altro broker di messaggi o eventi. Ognuno di questi gruppi di schemi è un repository a protezione diretta separato per un set di schemi. I gruppi possono essere allineati con una particolare applicazione o un'unità organizzativa. 

## <a name="schema-groups"></a>Gruppi di schemi
Il gruppo di schemi è un gruppo logico di schemi simili in base ai criteri aziendali. Un gruppo di schemi può contenere più versioni di uno schema. L'impostazione di imposizione di compatibilità in un gruppo di schemi può contribuire a garantire che le versioni più recenti dello schema siano compatibili con le versioni precedenti.

Il limite di sicurezza imposto dal meccanismo di raggruppamento contribuisce a garantire che i segreti commerciali non trapelino inavvertitamente attraverso i metadati nelle situazioni in cui lo spazio dei nomi è condiviso tra più partner. Consente inoltre ai proprietari di applicazioni di gestire gli schemi indipendentemente dalle altre applicazioni che condividono lo stesso spazio dei nomi.


## <a name="schemas"></a>Schemi
Gli schemi definiscono il contratto tra i producer e i consumer. Uno schema definito in un registro schemi di Hub eventi consente di gestire il contratto al di fuori dei dati dell'evento, rimuovendo così il sovraccarico del payload. Uno schema ha un nome, un tipo (esempio: record, matrice e così via), una modalità di compatibilità (nessuna, con versioni successive, con versioni precedenti, completa) e un tipo di serializzazione (per ora solo Avro). È possibile creare più versioni di uno schema e recuperare e usare una versione specifica di uno schema. 

## <a name="client-sdks"></a>Client SDK
È possibile usare una delle librerie seguenti che includono un serializzatore Avro, che è possibile usare per serializzare e deserializzare i payload che contengono gli identificatori dello schema del registro schemi e i dati con codifica Avro.

- [.NET - Microsoft.Azure.Data.SchemaRegistry.ApacheAvro](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/schemaregistry/Microsoft.Azure.Data.SchemaRegistry.ApacheAvro)
- [Java - azure-data-schemaregistry-avro](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/schemaregistry/azure-data-schemaregistry-avro/)
- [Python - azure-schemaregistry-avroserializer](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/schemaregistry/azure-schemaregistry-avroserializer)
- [JavaScript - @azure/schema-registry-avro](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/schemaregistry/schema-registry-avro)
- [Apache Kafka](https://github.com/Azure/azure-schema-registry-for-kafka/tree/master/avro/samples) - Eseguire serializzatori e deserializzatori Apache Avro integrati in Kafka supportati dal Registro schemi di Azure. Il serializzatore client Apache Kafka del client Java per il Registro schemi di Azure può essere usato in qualsiasi scenario di Apache Kafka e con qualsiasi servizio di distribuzione o servizio cloud basato su Apache Kafka®. 

La figura seguente mostra il flusso di informazioni del registro schemi con Hub eventi: 

:::image type="content" source="./media/schema-registry-overview/flow-diagram.png" alt-text="Diagramma di flusso":::

## <a name="standard-vs-dedicated-limits"></a>Limiti dei livelli Standard e Dedicato
Per i limiti, come ad esempio il numero di gruppi di schemi in uno spazio dei nomi, uguali e diversi per i livelli Standard e Dedicato di Hub eventi, vedere [Limiti del Registro schemi](../azure-resource-manager/management/azure-subscription-service-limits.md#schema-registry-limitations).

## <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo
Quando si accede al registro schemi a livello di codice, è necessario registrare un'applicazione in Azure Active Directory (Azure AD) e aggiungere l'entità di sicurezza dell'applicazione a uno dei ruoli di controllo degli accessi in base al ruolo:

| Ruolo | Descrizione | 
| ---- | ----------- | 
| Proprietario | Lettura, scrittura ed eliminazione di gruppi e schemi del Registro schemi. |
| Collaboratore | Lettura, scrittura ed eliminazione di gruppi e schemi del Registro schemi. |
| [Lettore del Registro schemi (anteprima)](../role-based-access-control/built-in-roles.md#schema-registry-reader-preview) | Lettura ed elenco di gruppi e schemi del Registro schemi. |
| [Collaboratore del Registro schemi (anteprima)](../role-based-access-control/built-in-roles.md#schema-registry-reader-preview) | Lettura, scrittura ed eliminazione di gruppi e schemi del Registro schemi. |

Per istruzioni sulla creazione di una registrazione di un'applicazione con il portale di Azure, vedere [Registrare un'app con Azure AD](../active-directory/develop/quickstart-register-app.md). Prendere nota dell'ID client (ID applicazione), dell'ID tenant e del segreto da usare nel codice. 

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come creare un registro schemi usando il portale di Azure, vedere [Creare un registro schemi di Hub eventi usando il portale di Azure](create-schema-registry.md).
- Vedere gli esempi seguenti della **libreria client Avro del Registro schemi**.
    - [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/schemaregistry/Microsoft.Azure.Data.SchemaRegistry.ApacheAvro/tests/Samples)
    - [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/schemaregistry/azure-data-schemaregistry-avro/src/samples)
    - [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/schemaregistry/schema-registry-avro/samples )
    - [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/schemaregistry/azure-schemaregistry-avroserializer/samples )
    - [Integrazione di Kafka Avro per il Registro schemi di Azure](https://github.com/Azure/azure-schema-registry-for-kafka/tree/master/avro/samples)
