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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774562"
---
Questa sezione descrive come eseguire le operazioni di modifica seguenti per il peering diretto:

### <a name="add-exchange-peering-connections"></a>Aggiungere connessioni peering di Exchange

1. Fare clic sul pulsante **+ Aggiungi connessioni** nella parte superiore e configurare una nuova connessione peering.
    > [!div class="mx-imgBorder"]
    > visualizzazione della risorsa peering ![](../media/setup-exchange-modify-addconnection.png)
1. Compilare il modulo di **connessione peering di Exchange** e fare clic su **Salva**. Per informazioni sulla configurazione di una connessione di peering, vedere la sezione precedente "creazione e provisioning di un peering diretto".
    > [!div class="mx-imgBorder"]
    > visualizzazione della risorsa peering ![](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>Rimuovere le connessioni peering di Exchange

1. Fare clic su una connessione di peering che si desidera eliminare e quindi fare clic sul pulsante **...**  > **Elimina connessione** .
    > [!div class="mx-imgBorder"]
    > ![eliminazione connessione peering](../media/setup-exchange-modify-deleteconnection.png)
1. Immettere l'ID risorsa nella casella **Conferma eliminazione** , come illustrato nelle caselle evidenziate, quindi fare clic su **Elimina**.
    > [!div class="mx-imgBorder"]
    > DeleteConfirm connessione peering ![](../media/setup-exchange-modify-deleteconnectionconfirm.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Aggiungi sessione IPv4/IPv6 in connessioni attive

1. Fare clic su una connessione di peering che si vuole modificare e quindi fare clic sul pulsante **...**  > **Modifica connessione** .
    > [!div class="mx-imgBorder"]
    > ![modifica connessione peering](../media/setup-exchange-modify-editconnection.png)
1. Aggiungere l' **indirizzo IPv4** o le informazioni sull' **indirizzo IPv6** e fare clic su **Salva**.
    > [!div class="mx-imgBorder"]
    > ![modifica connessione peering](../media/setup-exchange-modify-editconnectionsettings.png)

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Rimuovere la sessione IPv4/IPv6 in connessioni attive

La rimozione di una sessione IPv4/IPv6 da una connessione esistente non è attualmente supportata nel portale. Contattare il [peering Microsoft](mailto:peeringexperience@microsoft.com).