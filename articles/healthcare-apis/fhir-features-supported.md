---
title: Funzionalità di FHIR supportate in Azure-API di Azure per FHIR
description: Questo articolo illustra le funzionalità della specifica FHIR implementate nell'API di Azure per FHIR
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: afb4026a7865f2cc8f831d8d1d7b1d332014d310
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90007571"
---
# <a name="features"></a>Funzionalità

API di Azure per FHIR offre una distribuzione completamente gestita del server Microsoft FHIR per Azure. Il server è un'implementazione dello standard [FHIR](https://hl7.org/fhir) . Questo documento elenca le funzionalità principali del server FHIR.

## <a name="fhir-version"></a>Versione di FHIR

Ultima versione supportata: `4.0.1`

Le versioni precedenti sono attualmente supportate anche: `3.0.2`

## <a name="rest-api"></a>API REST

| API                            | Supportato-PaaS | Supportato-OSS (SQL) | Supportato-OSS (Cosmos DB) | Commento                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| read                           | Sì       | Sì       | Sì       |                                                     |
| VREAD                          | Sì       | Sì       | Sì       |                                                     |
| update                         | Sì       | Sì       | Sì       |                                                     |
| aggiornamento con blocco ottimistico | Sì       | Sì       | Sì       |                                                     |
| aggiornamento (condizionale)           | Sì       | Sì       | Sì       |                                                     |
| patch                          | No        | No        | No        |                                                     |
| eliminare                         | Sì       | Sì       | Sì       |                                                     |
| Elimina (condizionale)           | No        | No        | No        |                                                     |
| create                         | Sì       | Sì       | Sì       | Supporto per POST/PUT                               |
| Crea (condizionale)           | Sì       | Sì       | Sì       |                                                     |
| ricerca                         | Partial   | Partial   | Partial   | Vedere di seguito                                           |
| ricerca concatenata                 | No        | Sì       | No        |                                           |
| ricerca inversa concatenata         | No        | No        | No        |                                            |
| capabilities                   | Sì       | Sì       | Sì       |                                                     |
| o batch                          | Sì       | Sì       | Sì       |                                                     |
| transaction                    | No        | Sì       | No        |                                                     |
| history                        | Sì       | Sì       | Sì       |                                                     |
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
| Quantità              | Sì       | Sì       | Sì       |         |
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

| Operazioni di ricerca       | Supportato-PaaS | Supportato-OSS (SQL) | Supportato-OSS (Cosmos DB) | Commento |
|-------------------------|-----------|-----------|-----------|---------|
| `_filter`               | No        | No        | No        |         |
| `_sort`                 | Partial        | Partial   | Partial        |   `_sort=_lastUpdated` è supportato       |
| `_score`                | No        | No        | No        |         |
| `_count`                | Sì       | Sì       | Sì       |         |
| `_summary`              | Parziale   | Partial   | Partial   | `_summary=count` è supportato |
| `_include`              | No        | Sì       | No        |         |
| `_revinclude`           | No        | Sì       | No        | Gli elementi inclusi sono limitati a 100. |
| `_contained`            | No        | No        | No        |         |
| `_elements`             | Sì        | Sì        | Sì        |         |

## <a name="extended-operations"></a>Operazioni estese

Tutte le operazioni supportate che estendono l'API RESTful.

| Tipo di parametro di ricerca | Supportato-PaaS | Supportato-OSS (SQL) | Supportato-OSS (Cosmos DB) | Commento |
|-----------------------|-----------|-----------|-----------|---------|
| $export (intero sistema)                | Sì       | Sì       | Sì       |         |
| Paziente/$export         | Sì       | Sì       | Sì       |         |
| Gruppo/$export               | Sì       | Sì       | Sì       |         |

## <a name="persistence"></a>Persistenza

Il server Microsoft FHIR dispone di un modulo di persistenza innestabile (vedere [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) ).

Attualmente il codice open source del server FHIR include un'implementazione per [Azure Cosmos DB](../cosmos-db/index-overview.md) e [database SQL](https://azure.microsoft.com/services/sql-database/).

Cosmos DB è un database multimodello distribuito a livello globale (API SQL, API MongoDB e così via). Supporta diversi [livelli di coerenza](../cosmos-db/consistency-levels.md). Il modello di distribuzione predefinito configura il server FHIR con `Strong` coerenza, ma i criteri di coerenza possono essere modificati (in genere attenuati) su una richiesta in base alla richiesta utilizzando l' `x-ms-consistency-level` intestazione della richiesta.

## <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

Il server FHIR USA [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) per il controllo di accesso. In particolare, viene applicato il controllo di accesso Role-Based (RBAC), se il `FhirServer:Security:Enabled` parametro di configurazione è impostato su `true` e tutte le richieste (eccetto `/metadata` ) al server FHIR devono avere l' `Authorization` intestazione della richiesta impostata su `Bearer <TOKEN>` . Il token deve contenere uno o più ruoli come definito nell' `roles` attestazione. Sarà consentita una richiesta se il token contiene un ruolo che consente l'azione specificata sulla risorsa specificata.

Attualmente, le azioni consentite per un determinato ruolo vengono applicate a *livello globale* nell'API.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state illustrate le funzionalità di FHIR supportate nell'API di Azure per FHIR. Successivamente, distribuire l'API di Azure per FHIR.
 
>[!div class="nextstepaction"]
>[Distribuire l'API di Azure per FHIR](fhir-paas-portal-quickstart.md)
