---
title: File di inclusione
titleSuffix: Azure
description: File di inclusione
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e3e87e59f5b3c95051d9ee53e4b8d87afe9d9ba8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774562"
---
In questa sezione viene descritto come eseguire le operazioni di modifica seguenti per il peering diretto:This section describes how to perform the following modification operations for Direct peering:

### <a name="add-exchange-peering-connections"></a>Aggiungere connessioni di peering di Exchange

1. Fare clic sul pulsante **Aggiungi connessioni** nella parte superiore e configurare una nuova connessione peering.
    > [!div class="mx-imgBorder"]
    > ![Visualizzazione delle risorse peering](../media/setup-exchange-modify-addconnection.png)
1. Compilare il modulo **Connessione peering di Exchange** e fare clic su **Salva**. Per assistenza con la configurazione di una connessione peering, vedere i passaggi descritti nella sezione precedente Creare ed eseguire il provisioning di un peering diretto.
    > [!div class="mx-imgBorder"]
    > ![Visualizzazione delle risorse peering](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>Rimuovere le connessioni di peering di Exchange

1. Fare clic su una connessione di peering che si desidera eliminare e quindi, fare clic su **...**  >  **Pulsante Elimina connessione.**
    > [!div class="mx-imgBorder"]
    > ![Eliminazione connessione peering](../media/setup-exchange-modify-deleteconnection.png)
1. Immettere l'ID risorsa nella casella **Conferma eliminazione** come mostrato nelle caselle evidenziate e fare clic su **Elimina**.
    > [!div class="mx-imgBorder"]
    > ![Eliminazione connessione peering](../media/setup-exchange-modify-deleteconnectionconfirm.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Aggiungi sessione IPv4/IPv6 su connessioni attive

1. Fare clic su una connessione di peering che si desidera modificare e quindi, fare clic su **...**  >  **Pulsante Modifica connessione.**
    > [!div class="mx-imgBorder"]
    > ![Modifica connessione peering](../media/setup-exchange-modify-editconnection.png)
1. Aggiungere informazioni **sull'indirizzo IPv4** o **sull'indirizzo IPv6** e fare clic su **Salva**.
    > [!div class="mx-imgBorder"]
    > ![Modifica connessione peering](../media/setup-exchange-modify-editconnectionsettings.png)

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Rimuovere la sessione IPv4/IPv6 sulle connessioni attive

La rimozione di una sessione IPv4/IPv6 da una connessione esistente non è attualmente supportata nel portale. Contattare [Microsoft peering](mailto:peeringexperience@microsoft.com).