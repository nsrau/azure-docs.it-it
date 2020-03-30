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
ms.openlocfilehash: d9ff01cf0180dae7f75d9753ba889d0caddad937
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775342"
---
In questa sezione viene descritto come eseguire le operazioni di modifica seguenti per il peering diretto:This section describes how to perform the following modification operations for Direct peering:

### <a name="add-direct-peering-connections"></a>Aggiungere connessioni di peering direttoAdd Direct peering connections
1. Fare clic sul pulsante **Aggiungi connessioni** nella parte superiore e configurare una nuova connessione peering.
    > [!div class="mx-imgBorder"]
    > ![Visualizzazione delle risorse peering](../media/setup-direct-modify-addconnection.png)
1. Compilare il modulo **Connessione peering diretta** e fare clic su **Salva**. Per assistenza con la configurazione di una connessione peering, vedere i passaggi descritti nella sezione precedente Creare ed eseguire il provisioning di un peering diretto.
    > [!div class="mx-imgBorder"]
    > ![Visualizzazione delle risorse peering](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>Rimuovere le connessioni di peering direttoRemove Direct peering connections

La rimozione di una connessione non è attualmente supportata nel portale. Contattare [Microsoft peering](mailto:peeringexperience@microsoft.com).

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Aggiornare o ridurre la larghezza di banda nelle connessioni attiveUpgrade or downgrade bandwidth on Active connections
1. Fare clic su una connessione di peering che si desidera modificare e quindi, fare clic su **...**  >  **Pulsante Modifica connessione.**
    > [!div class="mx-imgBorder"]
    > ![Modifica connessione peering](../media/setup-direct-modify-editconnection.png)
1. Modificare la larghezza di banda come illustrato di seguito e quindi fare clic su **Salva**.
    > [!div class="mx-imgBorder"]
    > ![Connessione peering Modifica larghezza di banda](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Aggiungere una sessione IPv4/IPv6 su connessioni attive.
1. Fare clic su una connessione di peering che si desidera modificare e quindi, fare clic su **...**  >  **Pulsante Modifica connessione** come mostrato sopra.
1. Aggiungere il **prefisso IPv4** della sessione o le informazioni sul **prefisso IPv6** della sessione e fare clic su **Salva**.

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Rimuovere la sessione IPv4/IPv6 nelle connessioni attive.
    Removing a **Session IPv4 prefix** or **Session IPv6 prefix** info is not currently supported on portal. Contact [Microsoft peering](mailto:peeringexperience@microsoft.com).
