---
title: includere file
description: includere file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/28/2020
ms.author: cherylmc
ms.openlocfilehash: f37876a0b6b7e8b520b5b6428381ebaf7b5b5c24
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93042586"
---
È possibile usare lo stesso pacchetto di configurazione del client VPN in ogni computer client Windows, a condizione che la versione corrisponda all'architettura del client. Per l'elenco dei sistemi operativi client supportati, vedere la sezione relativa alle connessioni da punto a sito di [Domande frequenti sul gateway VPN](../articles/vpn-gateway/vpn-gateway-vpn-faq.md#P2S).

>[!NOTE]
>È necessario avere diritti di amministratore per il computer client Windows da cui ci si vuole connettere.
>

Per configurare il client VPN Windows nativo per l'autenticazione del certificato, usare questa procedura:

1. Selezionare i file di configurazione del client VPN corrispondenti all'architettura del computer Windows. Per un'architettura con processore a 64 bit, scegliere il pacchetto di installazione "VpnClientSetupAmd64". Per un'architettura con processore a 32 bit, scegliere il pacchetto di installazione "VpnClientSetupX86". 
1. Fare doppio clic sul pacchetto per installarlo. Se viene visualizzato un popup SmartScreen, fare clic su **altre informazioni** e quindi su **Esegui comunque** .
1. Nel computer client passare a impostazioni di **rete** e fare clic su **VPN** . La connessione VPN viene visualizzata con il nome della rete virtuale a cui si connette.
1. Prima di tentare di connettersi, verificare che nel computer client sia installato un certificato client. Quando si usa il tipo di autenticazione del certificato di Azure nativo, è necessario un certificato client per l'autenticazione.