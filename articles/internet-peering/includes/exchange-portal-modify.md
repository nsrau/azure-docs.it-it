---
title: includere il file
titleSuffix: Azure
description: includere file
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e06b5261ca6923e158c818d236a30cf6ebff189b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81680955"
---
Questa sezione descrive come eseguire le operazioni di modifica seguenti per il peering diretto.

### <a name="add-exchange-peering-connections"></a>Aggiungere connessioni peering di Exchange

1. Selezionare il pulsante **+ Aggiungi connessioni** e configurare una nuova connessione peering.
    > [!div class="mx-imgBorder"]
    > ![Visualizzazione risorse peering](../media/setup-exchange-modify-addconnection.png)
1. Compilare il modulo di **connessione peering di Exchange** e selezionare **Salva**. Per informazioni sulla configurazione di una connessione di peering, vedere la procedura descritta nella sezione "creazione e provisioning di un peering diretto".
    > [!div class="mx-imgBorder"]
    > ![Modulo di connessione peering di Exchange](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>Rimuovere le connessioni peering di Exchange

1. Selezionare una connessione di peering che si vuole eliminare e quindi selezionare **...**  >  **Elimina connessione**.
    > [!div class="mx-imgBorder"]
    > ![Pulsante Elimina connessione](../media/setup-exchange-modify-deleteconnection.png)
1. Immettere l'ID risorsa nella casella **Conferma eliminazione** e selezionare **Elimina**.
    > [!div class="mx-imgBorder"]
    > ![Conferma eliminazione](../media/setup-exchange-modify-deleteconnectionconfirm.png)

### <a name="add-an-ipv4-or-ipv6-session-on-active-connections"></a>Aggiungere una sessione IPv4 o IPv6 in connessioni attive

1. Selezionare una connessione di peering che si vuole modificare e quindi selezionare **...**  >  **Modifica connessione**.
    > [!div class="mx-imgBorder"]
    > ![Pulsante Modifica connessione](../media/setup-exchange-modify-editconnection.png)
1. Aggiungere le informazioni sull'indirizzo **IPv4** o **IPv6** e selezionare **Salva**.
    > [!div class="mx-imgBorder"]
    > ![Modifiche della connessione di peering](../media/setup-exchange-modify-editconnectionsettings.png)

### <a name="remove-an-ipv4-or-ipv6-session-on-active-connections"></a>Rimuovere una sessione IPv4 o IPv6 in connessioni attive

La rimozione di una sessione IPv4 o IPv6 da una connessione esistente non è attualmente supportata nel portale. Per ulteriori informazioni, contattare il [peering Microsoft](mailto:peeringexperience@microsoft.com).