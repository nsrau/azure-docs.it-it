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
ms.openlocfilehash: e06b5261ca6923e158c818d236a30cf6ebff189b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680955"
---
In questa sezione viene descritto come eseguire le seguenti operazioni di modifica per il peering diretto.

### <a name="add-exchange-peering-connections"></a>Aggiungere connessioni di peering di Exchange

1. Selezionare il pulsante **: Aggiungi connessioni** e configurare una nuova connessione peering.
    > [!div class="mx-imgBorder"]
    > ![Visualizzazione delle risorse peering](../media/setup-exchange-modify-addconnection.png)
1. Compilare il modulo **Connessione peering di Exchange** e selezionare **Salva**. Per assistenza con la configurazione di una connessione peering, esaminare i passaggi nella sezione "Creare ed eseguire il provisioning di un peering diretto".
    > [!div class="mx-imgBorder"]
    > ![Modulo Connessione peering di Exchange](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>Rimuovere le connessioni di peering di Exchange

1. Selezionare una connessione di peering che si desidera eliminare e quindi selezionare **...**  >  **Eliminare la connessione**.
    > [!div class="mx-imgBorder"]
    > ![Pulsante Elimina connessione](../media/setup-exchange-modify-deleteconnection.png)
1. Immettere l'ID risorsa nella casella **Conferma eliminazione** e selezionare **Elimina**.
    > [!div class="mx-imgBorder"]
    > ![Conferma eliminazione](../media/setup-exchange-modify-deleteconnectionconfirm.png)

### <a name="add-an-ipv4-or-ipv6-session-on-active-connections"></a>Aggiungere una sessione IPv4 o IPv6 su connessioni attive

1. Selezionare una connessione di peering che si desidera modificare e quindi selezionare **...**  >  **Modifica connessione**.
    > [!div class="mx-imgBorder"]
    > ![Pulsante Modifica connessione](../media/setup-exchange-modify-editconnection.png)
1. Aggiungere informazioni **sull'indirizzo IPv4** o **IPv6** e selezionare **Salva**.
    > [!div class="mx-imgBorder"]
    > ![Modifiche di connessione peering](../media/setup-exchange-modify-editconnectionsettings.png)

### <a name="remove-an-ipv4-or-ipv6-session-on-active-connections"></a>Rimuovere una sessione IPv4 o IPv6 su connessioni attive

La rimozione di una sessione IPv4 o IPv6 da una connessione esistente non è attualmente supportata nel portale. Per ulteriori informazioni, [contattare Microsoft peering](mailto:peeringexperience@microsoft.com).