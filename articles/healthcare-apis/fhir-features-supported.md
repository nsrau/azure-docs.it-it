---
title: Funzionalità di FHIR supportate in Azure-API di Azure per FHIR
description: Questo articolo illustra le funzionalità della specifica FHIR implementate nell'API di Azure per FHIR
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/07/2019
ms.author: cavoeg
ms.openlocfilehash: 3aea2322129c383a385168c54001464da5dae276
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95520085"
---
# <a name="features"></a>Funzionalità

API di Azure per FHIR offre una distribuzione completamente gestita del server Microsoft FHIR per Azure. Il server è un'implementazione dello standard [FHIR](https://hl7.org/fhir) . Questo documento elenca le funzionalità principali del server FHIR.

## <a name="fhir-version"></a>Versione di FHIR

Ultima versione supportata: `4.0.1`

Le versioni precedenti sono attualmente supportate anche: `3.0.2`

## <a name="rest-api"></a>API REST

| API                            | Supportato-PaaS | Supportato-OSS (SQL) | Supportato-OSS (Cosmos DB) | Commento                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| leggere                           | Sì       | Sì       | Sì       |                                                     |
| VREAD                          | Sì       | Sì       | Sì       |                                                     |
| update                         | Sì       | Sì       | Sì       |                                                     |
| aggiornamento con blocco ottimistico | Sì       | Sì       | Sì       |                                                     |
| aggiornamento (condizionale)           | Sì       | Sì       | Sì       |                                                     |
| patch                          | No        | No        | No        |                                                     |
| eliminare                         | Sì       | Sì       | Sì       |                                                     |
| Elimina (condizionale)           | No        | No        | No        |                                                     |
| history                        | Sì       | Sì       | Sì       |                                                     |
| create                         | Sì       | Sì       | Sì       | Supporto per POST/PUT                               |
| Crea (condizionale)           | Sì       | Sì       | Sì       | [#1382](https://github.com/microsoft/fhir-server/issues/1382) problema |
| ricerca                         | Partial   | Partial   | Partial   | Vedere di seguito                                           |
| ricerca concatenata                 | No        | Sì       | No        |                                           |
| ricerca inversa concatenata         | No        | No        | No        |                                            |
| capabilities                   | Sì       | Sì       | Sì       |                                                     |
| o batch                          | Sì       | Sì       | Sì       |                                                     |
| transaction                    | No        | Sì       | No        |                                                     |
| paging                         | Partial   | Partial   | Partial   | `self` e `next` sono supportati                     |
| intermediari                 | No        | No        | No        |                                                     |

## <a name="search"></a>Ricerca

Sono supportati tutti i tipi di parametro di ricerca. 

| Tipo di parametro di ricerca | Supportato-PaaS | Supportato-OSS (SQL) | Supportato-OSS (Cosmos DB) | Commento |
|-----------------------|-----------|-----------|-----------|---------|
| Numero                | Sì       | Sì       | Sì       |         |
| Date/DateTime         | Sì       | Sì       | Sì       |         |
| string                | Sì       | Sì       | Sì       |         |
| token                 | Sì       | Sì       | Sì       |         |
| Informazioni di riferimento             | Sì       | Sì       | Sì       |         |
| Composite             | Sì       | Sì       | Sì       |         |
| Quantity              | Sì       | Sì       | Sì       |         |
| URI                   | Sì       | Sì       | Sì       |         |
| Speciali               | No        | No        | No        |         |


| Modificatori             | Supportato-PaaS | Supportato-OSS (SQL) | Supportato-OSS (Cosmos DB) | Comment |
|-----------------------|-----------|-----------|-----------|---------|
|`:missing`             | Sì       | Sì       | Sì       |         |
|`:exact`               | Sì       | Sì       | Sì       |         |
|`:contains`            | Sì       | Sì       | Sì       |         |
|`:text`                | Sì       | Sì       | Sì       |         |
|`:in` token          | No        | No        | No        |         |
|`:below` token       | No        | No        | No        |         |
|`:above` token       | No        | No        | No        |         |
|`:not-in` token      | No        | No        | No        |         |
|`:[type]` riferimento  | No        | No        | No        |         |
|`:below` URI         | Sì       | Sì       | Sì       |         |
|`:not`                 | No        | No        | No        |         |
|`:above` URI         | No        | No        | No        | [#158](https://github.com/Microsoft/fhir-server/issues/158) problema |

| Parametro di ricerca comune | Supportato-PaaS | Supportato-OSS (SQL) | Supportato-OSS (Cosmos DB) | Comment |
|-------------------------| ----------| ----------| ----------|---------|
| `_id`                   | Sì       | Sì       | Sì       |         |
| `_lastUpdated`          | Sì       | Sì       | Sì       |         |
| `_tag`                  | Sì       | Sì       | Sì       |         |
| `_profile`              | Sì       | Sì       | Sì       |         |
| `_security`             | Sì       | Sì       | Sì       |         |
| `_text`                 | No        | No        | No        |         |
| `_content`              | No        | No        | No        |         |
| `_list`                 | Sì       | Sì       | Sì       |         |
| `_has`                  | No        | No        | No        |         |
| `_type`                 | Sì       | Sì       | Sì       |         |
| `_query`                | No        | No        | No        |         |
| `_filter`               | No        | No        | No        |         |

| Parametri dei risultati della ricerca | Supportato-PaaS | Supportato-OSS (SQL) | Supportato-OSS (Cosmos DB) | Commento |
|-------------------------|-----------|-----------|-----------|---------|
| `_sort`                 | Partial        | Partial   | Partial        |   `_sort=_lastUpdated` è supportato       |
| `_count`                | Sì       | Sì       | Sì       | `_count` è limitato a 100 caratteri. Se è impostato su un valore superiore a 100, verrà restituito solo 100 e nel bundle verrà restituito un avviso. |
| `_include`              | Sì       | Sì       | Sì       |Gli elementi inclusi sono limitati a 100. L'inclusione in PaaS e OSS in Cosmos DB non include: iterate support.|
| `_revinclude`           | Sì       | Sì       | Sì       | Gli elementi inclusi sono limitati a 100. L'inclusione in PaaS e OSS in Cosmos DB non include: iterate support.|
| `_summary`              | Partial   | Partial   | Partial   | `_summary=count` è supportato |
| `_total`                | Partial   | Partial   | Partial   | _total = non e _total = accurate      |
| `_elements`             | Sì       | Sì       | Sì       |         |
| `_contained`            | No        | No        | No        |         |
| `containedType`         | No        | No        | No        |         |
| `_score`                | No        | No        | No        |         |

## <a name="extended-operations"></a>Operazioni estese

Tutte le operazioni supportate che estendono l'API RESTful.

| Tipo di parametro di ricerca | Supportato-PaaS | Supportato-OSS (SQL) | Supportato-OSS (Cosmos DB) | Commento |
|------------------------|-----------|-----------|-----------|---------|
| $export (intero sistema) | Sì       | Sì       | Sì       |         |
| Paziente/$export        | Sì       | Sì       | Sì       |         |
| Gruppo/$export          | Sì       | Sì       | Sì       |         |

## <a name="persistence"></a>Persistenza

Il server Microsoft FHIR dispone di un modulo di persistenza innestabile (vedere [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) ).

Attualmente il codice open source del server FHIR include un'implementazione per [Azure Cosmos DB](../cosmos-db/index-overview.md) e [database SQL](https://azure.microsoft.com/services/sql-database/).

Cosmos DB è un database multimodello distribuito a livello globale (API SQL, API MongoDB e così via). Supporta diversi [livelli di coerenza](../cosmos-db/consistency-levels.md). Il modello di distribuzione predefinito configura il server FHIR con `Strong` coerenza, ma i criteri di coerenza possono essere modificati (in genere attenuati) su una richiesta in base alla richiesta utilizzando l' `x-ms-consistency-level` intestazione della richiesta.

## <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

Il server FHIR USA [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) per il controllo di accesso. In particolare, viene applicato il controllo degli accessi in base al ruolo (RBAC), se il `FhirServer:Security:Enabled` parametro di configurazione è impostato su `true` e tutte le richieste (eccetto `/metadata` ) al server FHIR devono avere l' `Authorization` intestazione della richiesta impostata su `Bearer <TOKEN>` . Il token deve contenere uno o più ruoli come definito nell' `roles` attestazione. Sarà consentita una richiesta se il token contiene un ruolo che consente l'azione specificata sulla risorsa specificata.

Attualmente, le azioni consentite per un determinato ruolo vengono applicate a *livello globale* nell'API.

## <a name="service-limits"></a>Limiti del servizio

* [**Unità richiesta (UR)**](https://docs.microsoft.com/azure/cosmos-db/concepts-limits) : è possibile configurare fino a 10.000 UR nel portale per l'API di Azure per FHIR. Sarà necessario un minimo di 400 ur o 10 UR/GB, a seconda del valore maggiore. Se sono necessarie più di 10.000 UR, è possibile inserire un ticket di supporto per aumentare il numero di unità. Il valore massimo disponibile è 1 milione.

* Connessioni e **istanze** **simultanee** : per dafault, sono disponibili cinque connessioni simultanee in due istanze del cluster (per un totale di 10 richieste simultanee). Se ritieni di avere bisogno di più richieste simultanee, Apri un ticket di supporto con i dettagli sulle tue esigenze.

* **Dimensioni bundle** : ogni bundle è limitato a 500 di elementi.

* **Dimensioni dati** : i dati e i documenti devono essere leggermente inferiori a 2 MB.

## <a name="performance-expectations"></a>Aspettative sulle prestazioni

Le prestazioni del sistema dipendono dal numero di UR, dalle connessioni simultanee e dal tipo di operazioni eseguite (Put, post e così via). Di seguito sono riportati alcuni intervalli generali di ciò che è possibile aspettarsi in base alle UR configurate. In generale, le prestazioni vengono ridimensionate in modo lineare con un aumento delle UR:

| numero di ur | Risorse/sec |
|----------|---------------|
| 400      | 5-10          |
| 1\.000    | 100-150       |
| 10,000   | 225-400       |
| 100,000  | 2500-4000   |

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state illustrate le funzionalità di FHIR supportate nell'API di Azure per FHIR. Successivamente, distribuire l'API di Azure per FHIR.
 
>[!div class="nextstepaction"]
>[Distribuire l'API di Azure per FHIR](fhir-paas-portal-quickstart.md)
