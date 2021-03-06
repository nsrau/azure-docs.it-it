---
title: includere il file
description: includere file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 52084b065ef65a69a6691b6646d1e199f011910d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "67179982"
---
### <a name="to-modify-the-local-network-gateway-ip-address---no-gateway-connection"></a><a name="gwipnoconnection"></a> Per modificare l'indirizzo IP del gateway di rete locale senza connessione gateway

Usare l'esempio per modificare un gateway di rete locale privo di connessione gateway. Quando si modifica questo valore, è anche possibile modificare contemporaneamente i prefissi degli indirizzi.

1. Nella risorsa Gateway di rete locale, nella sezione **Impostazioni** fare clic su **Configurazione**.
2. Nella casella **Indirizzo IP** modificare l'indirizzo IP.
3. Fare clic su **Salva** per salvare le impostazioni.

### <a name="to-modify-the-local-network-gateway-ip-address---existing-gateway-connection"></a><a name="gwipwithconnection"></a> Per modificare l'indirizzo IP del gateway di rete locale con una connessione gateway esistente

Per modificare un gateway di rete locale con una connessione, è necessario in primo luogo rimuovere la connessione. Dopo aver rimosso la connessione sarà possibile modificare l'indirizzo IP del gateway e ricreare una nuova connessione. È anche possibile modificare contemporaneamente i prefissi di indirizzo. Questo comporterà periodi di inattività per la connessione VPN. Quando si modifica l'indirizzo IP del gateway, non è necessario eliminare il gateway VPN. Occorre rimuovere solo la connessione.
 
#### <a name="1-remove-the-connection"></a>1. rimuovere la connessione.

1. Nella risorsa Gateway di rete locale, nella sezione **Impostazioni** fare clic su **Connessioni**.
2. Fare clic su **...** nella riga della connessione e quindi fare clic su **Elimina**.
3. Per salvare le impostazioni, fare clic su **Save** .

#### <a name="2-modify-the-ip-address"></a>2. modificare l'indirizzo IP.

È anche possibile modificare contemporaneamente i prefissi di indirizzo.

1. Nella casella **Indirizzo IP** modificare l'indirizzo IP.
2. Fare clic su **Salva** per salvare le impostazioni.

#### <a name="3-recreate-the-connection"></a>3. ricreare la connessione.

1. Passare al gateway di rete virtuale relativo alla rete virtuale (non al gateway di rete locale).
2. Nella risorsa Gateway di rete virtuale, nella sezione **Impostazioni** fare clic su **Connessioni**.
3. Fare clic su **+Aggiungi** per aprire il pannello **Aggiungi connessione**.
4. Ricreare la connessione.
5. Fare clic su **OK** per creare la connessione.