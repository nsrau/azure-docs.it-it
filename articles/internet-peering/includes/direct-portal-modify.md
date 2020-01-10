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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775342"
---
Questa sezione descrive come eseguire le operazioni di modifica seguenti per il peering diretto:

### <a name="add-direct-peering-connections"></a>Aggiungi connessioni peering diretto
1. Fare clic sul pulsante **+ Aggiungi connessioni** nella parte superiore e configurare una nuova connessione peering.
    > [!div class="mx-imgBorder"]
    > visualizzazione della risorsa peering ![](../media/setup-direct-modify-addconnection.png)
1. Compilare il modulo di **connessione peering diretto** e fare clic su **Salva**. Per informazioni sulla configurazione di una connessione di peering, vedere la sezione precedente "creazione e provisioning di un peering diretto".
    > [!div class="mx-imgBorder"]
    > visualizzazione della risorsa peering ![](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>Rimuovere connessioni peering dirette

La rimozione di una connessione non è attualmente supportata nel portale. Contattare il [peering Microsoft](mailto:peeringexperience@microsoft.com).

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Aggiornare o effettuare il downgrade della larghezza di banda nelle connessioni attive
1. Fare clic su una connessione di peering che si vuole modificare e quindi fare clic sul pulsante **...**  > **Modifica connessione** .
    > [!div class="mx-imgBorder"]
    > ![modifica connessione peering](../media/setup-direct-modify-editconnection.png)
1. Modificare la larghezza di banda come illustrato di seguito e quindi fare clic su **Salva**.
    > [!div class="mx-imgBorder"]
    > ![della connessione di peering modificare la larghezza di banda](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Aggiungere la sessione IPv4/IPv6 alle connessioni attive.
1. Fare clic su una connessione di peering che si vuole modificare e quindi fare clic sul pulsante **...**  > **Modifica connessione** , come illustrato in precedenza.
1. Aggiungere le informazioni sul **prefisso IPv6 della sessione** o del **prefisso IPv4** della sessione e fare clic su **Salva**.

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Rimuovere la sessione IPv4/IPv6 in connessioni attive.
    Removing a **Session IPv4 prefix** or **Session IPv6 prefix** info is not currently supported on portal. Contact [Microsoft peering](mailto:peeringexperience@microsoft.com).
