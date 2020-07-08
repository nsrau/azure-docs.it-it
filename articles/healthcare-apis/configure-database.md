---
title: Configurare le impostazioni del database nell'API di Azure per FHIR
description: Questo articolo descrive come configurare le impostazioni del database nell'API di Azure per FHIR
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/15/2019
ms.author: matjazl
ms.openlocfilehash: adc6fdf144927d10f811a00aa33f244cfdc25042
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84870961"
---
# <a name="configure-database-settings"></a>Configurare le impostazioni del database 

API di Azure per FHIR usa il database per archiviare i dati. Le prestazioni del database sottostante dipendono dal numero di unità richiesta (UR) selezionate durante il provisioning del servizio o nelle impostazioni del database dopo il provisioning del servizio.

L'API di Azure per FHIR prende in prestito il concetto di ur da Cosmos DB (vedere [unità richiesta in Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/request-units)) quando si configurano le prestazioni del database sottostante. 

È necessario effettuare il provisioning della velocità effettiva per garantire che le risorse di sistema sufficienti siano sempre disponibili per il database. Il numero di ur necessarie per l'applicazione dipende dalle operazioni eseguite. Le operazioni possono variare da semplici letture e scritture a query più complesse. 

> [!NOTE]
> Poiché diverse operazioni utilizzano un numero diverso di UR, viene restituito il numero effettivo di ur utilizzate in ogni chiamata API nell'intestazione della risposta. In questo modo è possibile profilare il numero di ur utilizzate dall'applicazione.

## <a name="update-throughput"></a>Velocità effettiva aggiornamento
Per modificare questa impostazione nel portale di Azure, passare all'API di Azure per FHIR e aprire il pannello del database. Modificare quindi la velocità effettiva con provisioning sul valore desiderato a seconda delle esigenze di prestazioni. È possibile modificare il valore fino a un massimo di 10.000 UR/sec. Se è necessario un valore superiore, contattare il supporto tecnico di Azure.

> [!NOTE] 
> Un valore superiore indica un'API di Azure superiore per la velocità effettiva FHIR e un costo superiore del servizio.

![Cosmos DB di configurazione](media/database/database-settings.png)

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come aggiornare le UR per l'API di Azure per FHIR. Distribuire quindi un'API di Azure completamente gestita per FHIR:
 
>[!div class="nextstepaction"]
>[Distribuire l'API di Azure per FHIR](fhir-paas-portal-quickstart.md)