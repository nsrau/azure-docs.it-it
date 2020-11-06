---
title: Configurare le impostazioni del database nell'API di Azure per FHIR
description: Questo articolo descrive come configurare le impostazioni del database nell'API di Azure per FHIR
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/15/2019
ms.author: matjazl
ms.openlocfilehash: 3fe4118a8ecf4479732ba4073b342d3ec9f941b8
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93398199"
---
# <a name="configure-database-settings"></a>Configurare le impostazioni del database 

API di Azure per FHIR usa il database per archiviare i dati. Le prestazioni del database sottostante dipendono dal numero di unità richiesta (UR) selezionate durante il provisioning del servizio o nelle impostazioni del database dopo il provisioning del servizio.

L'API di Azure per FHIR prende in prestito il concetto di ur da Cosmos DB (vedere [unità richiesta in Azure Cosmos DB](../cosmos-db/request-units.md)) quando si configurano le prestazioni del database sottostante. 

È necessario effettuare il provisioning della velocità effettiva per garantire che le risorse di sistema sufficienti siano sempre disponibili per il database. Il numero di ur necessarie per l'applicazione dipende dalle operazioni eseguite. Le operazioni possono variare da semplici letture e scritture a query più complesse. 

> [!NOTE]
> Poiché diverse operazioni utilizzano un numero diverso di UR, viene restituito il numero effettivo di ur utilizzate in ogni chiamata API nell'intestazione della risposta. In questo modo è possibile profilare il numero di ur utilizzate dall'applicazione.

## <a name="update-throughput"></a>Aggiornare la velocità effettiva

Per modificare questa impostazione nel portale di Azure, passare all'API di Azure per FHIR e aprire il pannello del database. Modificare quindi la velocità effettiva con provisioning sul valore desiderato a seconda delle esigenze di prestazioni. È possibile modificare il valore fino a un massimo di 10.000 UR/sec. Se è necessario un valore superiore, contattare il supporto tecnico di Azure.

Se la velocità effettiva del database è superiore a 10.000 UR/sec o se i dati archiviati nel database sono maggiori di 50 GB, l'applicazione client deve essere in grado di gestire i token di continuazione. Viene creata una nuova partizione nel database per ogni aumento della velocità effettiva di 10.000 UR/sec o se la quantità di dati archiviati è superiore a 50 GB. Più partizioni crea una risposta a più pagine in cui l'impaginazione viene implementata usando i token di continuazione.

> [!NOTE] 
> Un valore superiore indica un'API di Azure superiore per la velocità effettiva FHIR e un costo superiore del servizio.

![Cosmos DB di configurazione](media/database/database-settings.png)

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come aggiornare le UR per l'API di Azure per FHIR. Per informazioni sulla configurazione delle chiavi gestite dal cliente come impostazione del database:

>[!div class="nextstepaction"]
>[Configurare le chiavi gestite dal cliente](customer-managed-key.md)

In alternativa, è possibile distribuire un'API di Azure completamente gestita per FHIR:
 
>[!div class="nextstepaction"]
>[Distribuire l'API di Azure per FHIR](fhir-paas-portal-quickstart.md)