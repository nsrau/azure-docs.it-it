---
title: Creare Hub eventi di Azure abilitati per Kafka | Microsoft Docs
description: Creare uno spazio dei nomi di Hub eventi di Azure abilitato per Kafka usando il portale di Azure
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.date: 05/07/2018
ms.author: shvija
ms.openlocfilehash: 7a05a89fdf99efb6470ee9c8695f349cf22b8ebb
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2018
ms.locfileid: "37442128"
---
# <a name="create-kafka-enabled-event-hubs"></a>Creare Hub eventi con supporto per Kafka

Hub eventi di Azure è una piattaforma come servizio (PaaS, Platform as a Service) di streaming di Big Data che inserisce milioni di eventi al secondo e fornisce bassa latenza e velocità effettiva elevata per l'analisi e la visualizzazione in tempo reale.

Hub eventi di Azure per ecosistemi Kafka fornisce un endpoint che consente allo spazio dei nomi di Hub eventi di comprendere in modo nativo il protocollo del messaggio e le API [Apache Kafka](https://kafka.apache.org/intro). Con questa funzionalità è possibile comunicare con gli Hub eventi come si farebbe con gli argomenti Kafka senza modificare i client del protocollo o eseguire i propri cluster. Gli Hub eventi per l'ecosistema Kafka supportano [Apache Kafka versione 1.0.](https://kafka.apache.org/10/documentation.html) e successive.

Questo articolo descrive come creare uno spazio dei nomi di Hub eventi e ottenere la stringa di connessione necessaria per connettere le applicazioni Kafka agli Hub eventi con supporto per Kafka.

## <a name="prerequisites"></a>prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.

## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Creare uno spazio dei nomi di Hub eventi con supporto per Kafka

1. Accedere al [portale di Azure][Azure portal] e fare clic su **Crea una risorsa** nella parte superiore sinistra della schermata.

2. Eseguire la ricerca per gli Hub eventi e selezionare le opzioni illustrate di seguito:
    
    ![Cercare Hub eventi nel portale](./media/event-hubs-create-kafka-enabled/event-hubs-create-event-hubs.png)
 
3. Specificare un nome univoco e abilitare Kafka nello spazio dei nomi. Fare clic su **Crea**.
    
    ![Creare uno spazio dei nomi](./media/event-hubs-create-kafka-enabled/create-kafka-namespace.png)
 
4. Dopo aver creato lo spazio dei nomi, nella scheda **Impostazioni** fare clic su **Criteri di accesso condivisi** per ottenere la stringa di connessione.

    ![Fare clic su Criteri di accesso condivisi](./media/event-hubs-create/create-event-hub7.png)

5. È possibile scegliere il valore predefinito **RootManageSharedAccessKey**, oppure aggiungere un nuovo criterio. Fare clic sul nome del criterio e copiare la stringa di connessione. 
    
    ![Selezionare un criterio](./media/event-hubs-create/create-event-hub8.png)
 
6. Aggiungere questa stringa di connessione alla configurazione dell'applicazione Kafka.

È ora possibile trasmettere gli eventi dalle applicazioni che usano il protocollo Kafka negli Hub eventi.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sugli Hub eventi, visitare i collegamenti seguenti:

* [Trasmettere in Hub eventi dalle applicazioni Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
* [Leggere le informazioni su Hub eventi per l'ecosistema Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Leggere le informazioni su Hub eventi](event-hubs-what-is-event-hubs.md)


[Azure portal]: https://portal.azure.com/
