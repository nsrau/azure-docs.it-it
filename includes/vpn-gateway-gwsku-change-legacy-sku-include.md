---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 2c1a4a1931bc2e38b0bee5f90518b01fdf4767a1
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
Se si usa il modello di distribuzione di Resource Manager, è possibile passare ai nuovi SKU del gateway. Quando si passa da uno SKU del gateway legacy a un nuovo SKU, eliminare il gateway VPN esistente e creare un nuovo gateway VPN.

Flusso di lavoro:

1. Rimuovere eventuali connessioni al gateway di rete virtuale.
2. Eliminare il gateway VPN precedente.
3. Creare il nuovo gateway VPN.
4. Aggiornare i dispositivi VPN locali con il nuovo indirizzo IP del gateway VPN (per connessioni da sito a sito).
5. Aggiornare il valore dell'indirizzo IP del gateway per eventuali gateway di rete locale da rete virtuale a rete virtuale che si connetteranno a questo gateway.
6. Scaricare i nuovi pacchetti di configurazione VPN client per i client P2S che si connettono alla rete virtuale tramite questo gateway VPN.
7. Creare di nuovo eventuali connessioni al gateway di rete virtuale.

Considerazioni:

* Per passare ai nuovi SKU, il gateway VPN deve essere nel modello di distribuzione Resource Manager.
* In caso di gateway VPN classico, è necessario continuare a usare gli SKU legacy precedenti per il gateway, è tuttavia possibile eseguire il ridimensionamento tra gli SKU legacy. Non è possibile passare ai nuovi SKU.
* Si avrà un tempo di inattività della connettività quando si passa da uno SKU legacy a un nuovo SKU.
* Quando si passa a un nuovo SKU del gateway, l'indirizzo IP pubblico del gateway VPN cambia, anche se si specifica lo stesso oggetto indirizzo IP pubblico usato in precedenza.