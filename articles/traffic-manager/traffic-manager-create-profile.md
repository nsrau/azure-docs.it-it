---
title: Creare un profilo di Gestione traffico in Azure | Microsoft Docs
description: Questo articolo descrive come creare un profilo di Gestione traffico
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/21/2017
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: 58194b71b22b63f7d4a2a6bf0f4e66f456a96d03
ms.lasthandoff: 03/22/2017

---

# <a name="create-a-traffic-manager-profile"></a>Creare un profilo di Gestione traffico

Questo articolo descrive come sia possibile creare un profilo con tipo di routing **Priorità** per instradare gli utenti a due endpoint di App Web di Azure. Con il tipo di routing **Priorità** tutto il traffico viene instradato al primo endpoint mentre il secondo viene mantenuto come backup. Gli utenti possono essere così instradati al secondo endpoint se il primo diventa non integro.

In questo articolo, due endpoint di App Web di Azure creati in precedenza vengono associati a questo nuovo profilo di Gestione traffico. Per altre informazioni su come creare endpoint di App Web di Azure, visitare la [pagina della documentazione di App Web di Azure](https://docs.microsoft.com/azure/app-service-web/). È possibile aggiungere qualsiasi endpoint che ha un nome DNS ed è raggiungibile sulla rete Internet pubblica. In questo articolo vengono usati endpoint di App Web di Azure a titolo di esempio.

### <a name="create-a-traffic-manager-profile"></a>Creare un profilo di Gestione traffico
1. Da un browser accedere al [portale di Azure](http://portal.azure.com). Se non si ha già di un account, è possibile iscriversi per ottenere una [versione di valutazione gratuita della durata di un mese](https://azure.microsoft.com/free/). 
2. Nel menu **Hub** fare clic su **Nuovo** > **Reti** > **Vedi tutto**, fare clic su **Profilo di Gestione traffico** per aprire il pannello **Crea profilo di Gestione traffico** e quindi fare clic su **Crea**.
3. Nel pannello **Crea profilo di Gestione traffico** procedere come segue:
    1. In **Nome** specificare un nome per il profilo. Questo nome deve essere univoco all'interno della zona trafficmanager.net e determina il nome DNS <name>,trafficmanager.net che viene usato per accedere al profilo di Gestione traffico.
    2. In **Metodo di routing** selezionare il metodo di routing **Priorità**.
    3. In **Sottoscrizione** selezionare la sottoscrizione in cui si vuole creare il profilo.
    4. In **Gruppo di risorse** creare un nuovo gruppo di risorse in cui aggiungere il profilo.
    5. In **Località del gruppo di risorse** selezionare la località del gruppo di risorse. Questa impostazione indica la località del gruppo di risorse e non ha alcun impatto sul profilo di Gestione traffico che sarà distribuito a livello globale.
    6. Fare clic su **Crea**.
    7. Una volta completata la distribuzione globale del profilo di Gestione traffico, il profilo sarà elencato nel rispettivo gruppo di risorse come una delle risorse.

    ![Creare un profilo di Gestione traffico](./media/traffic-manager-create-profile/Create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Aggiungere endpoint di Gestione traffico

1. Nella barra di ricerca del portale cercare il nome del **Profilo di Gestione traffico** creato nella sezione precedente e fare clic sul profilo di gestione traffico nei risultati visualizzati.
2. Nel pannello **Profilo di Gestione traffico** fare clic su **Endpoint** nella sezione **Impostazioni**.
3. Nel pannello **Endpoint** visualizzato fare clic su **Aggiungi**.
4. Nel pannello **Aggiungi endpoint** procedere come segue:
    1. In **Tipo** fare clic su **Endpoint di Azure**.
    2. Specificare un **Nome** con il quale si vuole identificare questo endpoint.
    3. In **Tipo di risorsa di destinazione** fare clic su **Servizio app**.
    4. In **Risorsa di destinazione** fare clic su **Scegliere un servizio app** per visualizzare l'elenco delle App Web nella stessa sottoscrizione. Nel pannello **Risorsa** visualizzato selezionare il servizio App che si vuole aggiungere come primo endpoint.
    5. In **Priorità** selezionare **1**. In questo modo tutto il traffico viene instradato a questo endpoint se è integro.
    6. Mantenere deselezionata l'opzione **Aggiungi come disabilitato**.
    7. Fare clic su **OK**
5.    Ripetere i passaggi 3 e 4 per il successivo endpoint di App Web di Azure. Assicurarsi di aggiungerlo con il valore di **Priorità** impostato su **2**.
6.    Una volta completata l'aggiunta di entrambi gli endpoint, essi vengono visualizzati nel pannello **Profilo di gestione traffico** insieme al relativo stato di monitoraggio **Online**.

    ![Aggiungere un endpoint di Gestione traffico](./media/traffic-manager-create-profile/add-traffic-manager-endpoint.png)

## <a name="use-the-traffic-manager-profile"></a>Usare il profilo di Gestione traffico
1.    Nella barra di ricerca del portale cercare il nome del **Profilo di Gestione traffico** creato nella sezione precedente. Fare clic sul profilo di Gestione traffico nei risultati visualizzati.
2. Nel pannello **Profilo di Gestione traffico** fare clic su **Informazioni generali**.
3. Il pannello **Profilo di Gestione traffico** visualizza il nome DNS del profilo di Gestione traffico appena creato. Questo nome può essere usato da qualsiasi client (ad esempio raggiungendolo tramite un Web browser) per essere instradato all'endpoint corretto in base al tipo di routing. In questo caso, tutte le richieste vengono instradate al primo endpoint e se Gestione traffico rileva che tale endpoint non è integro, viene eseguito automaticamente il failover del traffico sull'endpoint successivo.

## <a name="delete-the-traffic-manager-profile"></a>Eliminare il profilo di Gestione traffico
Quando non sono più necessari, eliminare il gruppo di risorse e il profilo di Gestione traffico creati. A tale scopo selezionare il gruppo di risorse nel pannello **Profilo di Gestione traffico** e fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui [tipi di routing](traffic-manager-routing-methods.md).
- Altre informazioni sui [tipi di endpoint](traffic-manager-endpoint-types.md).
- Altre informazioni sul [monitoraggio degli endpoint](traffic-manager-monitoring.md).




