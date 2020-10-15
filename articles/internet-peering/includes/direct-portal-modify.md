---
title: includere file
titleSuffix: Azure
description: includere file
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 3894bf046ed4ee3f068e43dbc5bc5b7f2a1002b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81681041"
---
Questa sezione descrive come eseguire le operazioni di modifica seguenti per il peering diretto.

### <a name="add-direct-peering-connections"></a>Aggiungi connessioni peering diretto
1. Selezionare il pulsante **+ Aggiungi connessioni** e configurare una nuova connessione peering.
    > [!div class="mx-imgBorder"]
    > ![Visualizzazione risorse peering](../media/setup-direct-modify-addconnection.png)

1. Compilare il modulo di **connessione peering diretto** e selezionare **Salva**. Per informazioni sulla configurazione di una connessione di peering, vedere la procedura descritta nella sezione "creazione e provisioning di un peering diretto".
    > [!div class="mx-imgBorder"]
    > ![Modulo di connessione peering diretto](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>Rimuovere connessioni peering dirette

La rimozione di una connessione non è attualmente supportata nella portale di Azure. Per ulteriori informazioni, contattare il [peering Microsoft](mailto:peeringexperience@microsoft.com).

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Aggiornare o effettuare il downgrade della larghezza di banda nelle connessioni attive
1. Selezionare una connessione di peering che si vuole modificare e quindi selezionare **...**  >  **Modifica connessione**.
    > [!div class="mx-imgBorder"]
    > ![Modifica connessione](../media/setup-direct-modify-editconnection.png)

1. Modificare la larghezza di banda spostando il dispositivo di scorrimento, quindi selezionare **Salva**.
    > [!div class="mx-imgBorder"]
    > ![Modificare la larghezza di banda](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4-or-ipv6-session-information-on-active-connections"></a>Aggiungere informazioni sulla sessione IPv4 o IPv6 sulle connessioni attive
1. Selezionare una connessione di peering che si vuole modificare e quindi selezionare **...**  >  **Modificare la connessione** come illustrato nel passaggio 1.
1. Immettere le informazioni relative al prefisso **IPv4** della sessione o al prefisso **IPv6** della sessione e selezionare **Salva**.

### <a name="remove-ipv4-or-ipv6-session-information-on-active-connections"></a>Rimuovere le informazioni sulla sessione IPv4 o IPv6 sulle connessioni attive
La rimozione delle informazioni sul **prefisso** **IPv4** Session o sul prefisso IPv6 della sessione non è attualmente supportata nel portale. Per ulteriori informazioni, contattare il [peering Microsoft](mailto:peeringexperience@microsoft.com).
