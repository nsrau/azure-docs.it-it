---
title: Configurare il metodo di routing del traffico Geografico tramite Gestione traffico di Azure | Microsoft Docs
description: Questo articolo descrive come configurare il metodo di routing del traffico Geografico tramite Gestione traffico di Azure
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
ms.date: 03/22/2017
ms.author: kumud
ms.openlocfilehash: 7b49e2a4eef5a966f1ef2aa283a3089bb5b73734
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>Configurare il metodo di routing del traffico Geografico tramite Gestione traffico

Il metodo di routing del traffico Geografico consente di indirizzare il traffico a specifici endpoint in base alla posizione geografica da cui ha origine la richiesta. In questa esercitazione viene illustrato come creare un profilo di Gestione traffico con questo metodo di routing e come configurare gli endpoint in modo da ricevere il traffico da aree geografiche specifiche.

## <a name="create-a-traffic-manager-profile"></a>Creazione di un profilo di Gestione traffico

1. Da un browser accedere al [portale di Azure](http://portal.azure.com). Se non si ha già di un account, è possibile iscriversi per ottenere una [versione di valutazione gratuita della durata di un mese](https://azure.microsoft.com/free/).
2. Fare clic su **Crea una risorsa** > **Rete** > **Profilo di Gestione traffico** > **Crea**.
4. In **Crea profilo di Gestione traffico**:
    1. Specificare un nome per il profilo. Questo nome deve essere univoco all'interno della zona trafficmanager.net. Per accedere al profilo di Gestione traffico, usare il nome DNS <profilename>.trafficmanager.net.
    2. Selezionare il metodo di routing **Geografico**.
    3. Selezionare la sottoscrizione in cui si desidera creare il profilo.
    4. Usare un gruppo di risorse esistente oppure crearne uno nuovo per posizionare il profilo. Se si sceglie di creare un nuovo gruppo di risorse, usare il menu a discesa **Località del gruppo di risorse** per specificare la località del gruppo di risorse. Questa impostazione indica la località del gruppo di risorse e non ha alcun impatto sul profilo di Gestione traffico che viene distribuito a livello globale.
    5. Dopo aver fatto clic su **Crea**, viene creato il profilo di Gestione traffico, che viene poi distribuito a livello globale.

![Creare un profilo di Gestione traffico](./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png)

## <a name="add-endpoints"></a>Aggiungere endpoint

1. Cercare il nome del profilo di Gestione traffico creato nella barra di ricerca del portale e fare clic sul risultato visualizzato.
2. Passare a **Impostazioni** -> **Endpoint** nel pannello di Gestione traffico.
3. Fare clic su **Aggiungi** per visualizzare **Aggiungi endpoint**.
3. Fare clic su **Aggiungi** e completare come segue il pannello **Aggiungi endpoint** visualizzato:
4. Selezionare **Tipo** in base al tipo di endpoint che si desidera aggiungere. Per i profili di routing geografico usati nell'ambiente di produzione è consigliabile usare i tipi di endpoint annidati che contengono un profilo figlio con più di un endpoint. Per altre informazioni, vedere le [domande frequenti sui metodi di routing del traffico](traffic-manager-FAQs.md).
5. Indicare un **nome** con il quale si desidera identificare l'endpoint.
6. Determinati campi di questa pagina dipendono dal tipo di endpoint che si vuole aggiungere:
    1. Se si aggiunge un endpoint di Azure, selezionare **Tipo di risorsa di destinazione** e **Destinazione** in base alla risorsa a cui si desidera indirizzare il traffico
    2. Se si aggiunge un endpoint **esterno**, indicare il **nome di dominio completo (FQDN)** per l'endpoint.
    3. Se si aggiunge un **endpoint annidato**, selezionare la **risorsa di destinazione** che corrisponde al profilo figlio che si desidera usare e specificare il **numero minimo di endpoint figlio**.
7. Nella sezione Mapping geografico usare l'elenco a discesa per aggiungere le aree da cui si desidera inviare il traffico a questo endpoint. È necessario aggiungere almeno un'area ed è possibile mappare più aree.
8. Ripetere questo passaggio per tutti gli endpoint che si desidera aggiungere al profilo

![Aggiungere un endpoint di Gestione traffico](./media/traffic-manager-geographic-routing-method/add-traffic-manager-endpoint.png)

## <a name="use-the-traffic-manager-profile"></a>Usare il profilo di Gestione traffico
1.  Nella barra di ricerca del portale cercare il nome del **profilo di Gestione traffico** creato nella sezione precedente e fare clic sul profilo di Gestione traffico nei risultati visualizzati.
2. Fare clic su **Panoramica**.
3. Il **Profilo di Gestione traffico** visualizza il nome DNS del profilo di Gestione traffico appena creato. Questo nome può essere usato da qualsiasi client (ad esempio raggiungendolo tramite un Web browser) che deve essere indirizzato all'endpoint corretto in base al tipo di routing.  Nel caso del routing geografico, Gestione traffico analizza l'indirizzo IP di origine della richiesta in arrivo e determina l'area da cui viene generato. Se l'area è mappata a un endpoint, il traffico viene indirizzato in quella posizione. Se l'area non è mappata a un endpoint, Gestione traffico restituisce una risposta query NODATA.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sul [Metodo di routing del traffico geografico ](traffic-manager-routing-methods.md#geographic).
- Informazioni su come [testare le impostazioni di Gestione traffico](traffic-manager-testing-settings.md).
