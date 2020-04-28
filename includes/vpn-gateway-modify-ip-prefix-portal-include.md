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
ms.openlocfilehash: 1199819d274590cc81d0234680f8765f9cc36c0a
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/27/2020
ms.locfileid: "67179983"
---
### <a name="to-modify-local-network-gateway-ip-address-prefixes---no-gateway-connection"></a><a name="noconnection"></a>Per modificare i prefissi degli indirizzi IP del gateway di rete locale senza connessione gateway

#### <a name="to-add-additional-address-prefixes"></a>Per aggiungere altri prefissi degli indirizzi:

1. Nella risorsa Gateway di rete locale, nella sezione **Impostazioni** fare clic su **Configurazione**.
2. Aggiungere lo spazio di indirizzi IP nella casella *Aggiungi intervallo di indirizzi*.
3. Per salvare le impostazioni, fare clic su **Save** .

#### <a name="to-remove-address-prefixes"></a>Per rimuovere prefissi degli indirizzi:

1. Nella risorsa Gateway di rete locale, nella sezione **Impostazioni** fare clic su **Configurazione**.
2. Fare clic su **"..."** nella riga contenente il prefisso da rimuovere.
3. Scegliere **Rimuovi**.
4. Per salvare le impostazioni, fare clic su **Save** .

### <a name="to-modify-local-network-gateway-ip-address-prefixes---existing-gateway-connection"></a><a name="withconnection"></a>Per modificare i prefissi degli indirizzi IP del gateway di rete locale con connessione gateway esistente

Se è disponibile una connessione gateway e si vogliono aggiungere o rimuovere i prefissi di indirizzo IP inclusi nel gateway di rete locale, è necessario seguire questa procedura nell'ordine indicato. Questo comporterà periodi di inattività per la connessione VPN. Quando si modificano i prefissi degli indirizzi IP, non è necessario eliminare il gateway VPN. Occorre rimuovere solo la connessione.

#### <a name="1-remove-the-connection"></a>1. rimuovere la connessione.

1. Nella risorsa Gateway di rete locale, nella sezione **Impostazioni** fare clic su **Connessioni**.
2. Fare clic su **...** nella riga di ogni connessione e quindi fare clic su **Elimina**.
3. Per salvare le impostazioni, fare clic su **Save** .

#### <a name="2-modify-the-address-prefixes"></a>2. modificare i prefissi degli indirizzi.

Per aggiungere altri prefissi degli indirizzi:

1. Nella risorsa Gateway di rete locale, nella sezione **Impostazioni** fare clic su **Configurazione**.
2. Aggiungere lo spazio di indirizzi IP.
3. Per salvare le impostazioni, fare clic su **Save** .

Per rimuovere prefissi degli indirizzi:

1. Nella risorsa Gateway di rete locale, nella sezione **Impostazioni** fare clic su **Configurazione**.
2. Fare clic su **...** nella riga contenente il prefisso che si desidera rimuovere.
3. Scegliere **Rimuovi**.
4. Per salvare le impostazioni, fare clic su **Save** .

#### <a name="3-recreate-the-connection"></a>3. ricreare la connessione.

1. Passare al gateway di rete virtuale relativo alla rete virtuale (non al gateway di rete locale).
2. Nella risorsa Gateway di rete virtuale, nella sezione **Impostazioni** fare clic su **Connessioni**.
3. Fare clic su **+Aggiungi** per aprire il pannello **Aggiungi connessione**.
4. Ricreare la connessione.
5. Fare clic su **OK** per creare la connessione.