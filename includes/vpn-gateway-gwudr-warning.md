---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 97fde67c3ac7649418ed0239a2c7aa4f1a4b3f96
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81274884"
---
Le route definite dall'utente con una destinazione 0.0.0.0/0 e gruppi in GatewaySubnet **non sono supportate**. I gateway creati con questa configurazione verranno bloccati dalla creazione. Per il corretto funzionamento dei gateway è necessario l'accesso ai controller di gestione. La [propagazione della route BGP](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol) deve essere impostata su "Enabled" in GatewaySubnet per garantire la disponibilità del gateway. Se è impostato su disabilitato, il gateway non funzionerà.
