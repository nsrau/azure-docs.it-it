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
ms.openlocfilehash: 3894bf046ed4ee3f068e43dbc5bc5b7f2a1002b4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681041"
---
In questa sezione viene descritto come eseguire le seguenti operazioni di modifica per il peering diretto.

### <a name="add-direct-peering-connections"></a>Aggiungere connessioni di peering direttoAdd Direct peering connections
1. Selezionare il pulsante **: Aggiungi connessioni** e configurare una nuova connessione peering.
    > [!div class="mx-imgBorder"]
    > ![Visualizzazione delle risorse peering](../media/setup-direct-modify-addconnection.png)

1. Compilare il modulo **Connessione peering diretta** e selezionare **Salva**. Per assistenza con la configurazione di una connessione peering, esaminare i passaggi nella sezione "Creare ed eseguire il provisioning di un peering diretto".
    > [!div class="mx-imgBorder"]
    > ![Modulo Connessione peering diretto](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>Rimuovere le connessioni di peering direttoRemove Direct peering connections

La rimozione di una connessione non è attualmente supportata nel portale di Azure.Removing a connection isn't currently supported in the Azure portal. Per ulteriori informazioni, [contattare Microsoft peering](mailto:peeringexperience@microsoft.com).

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Aggiornare o ridurre la larghezza di banda nelle connessioni attiveUpgrade or downgrade bandwidth on Active connections
1. Selezionare una connessione di peering che si desidera modificare e quindi selezionare **...**  >  **Modifica connessione**.
    > [!div class="mx-imgBorder"]
    > ![Modifica connessione](../media/setup-direct-modify-editconnection.png)

1. Modificare la larghezza di banda spostando il dispositivo di scorrimento e quindi selezionare **Salva**.
    > [!div class="mx-imgBorder"]
    > ![Modificare la larghezza di banda](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4-or-ipv6-session-information-on-active-connections"></a>Aggiungere informazioni sulla sessione IPv4 o IPv6 nelle connessioni attive
1. Selezionare una connessione di peering che si desidera modificare e quindi selezionare **...**  >  **Modificare la connessione** come illustrato nel passaggio 1.
1. Immettere **Il prefisso IPv4** della sessione o le informazioni sul **prefisso IPv6** della sessione e selezionare **Salva**.

### <a name="remove-ipv4-or-ipv6-session-information-on-active-connections"></a>Rimuovere le informazioni sulla sessione IPv4 o IPv6 nelle connessioni attive
La rimozione del **prefisso IPv4** della sessione o delle informazioni sul **prefisso IPv6** della sessione non è attualmente supportata nel portale. Per ulteriori informazioni, [contattare Microsoft peering](mailto:peeringexperience@microsoft.com).
