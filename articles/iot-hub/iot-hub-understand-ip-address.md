---
title: Informazioni sull'indirizzo IP dell'hub Internet delle cose | Microsoft Docs
description: Informazioni su come eseguire una query sull'indirizzo IP dell'hub Internet e sulle relative proprietà. È possibile modificare l'indirizzo IP dell'hub Internet in determinati scenari, ad esempio il ripristino di emergenza o il failover a livello di area.
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: 9c9d02eeead4ccbed372e9a55d9b551da83caa39
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642293"
---
# <a name="understanding-the-ip-address-of-your-iot-hub"></a>Informazioni sull'indirizzo IP dell'hub Internet delle cose

L'indirizzo IP dell'hub Internet è un endpoint pubblico con carico bilanciato per l'hub e non un indirizzo IP dedicato. L'indirizzo è determinato dall'intervallo di indirizzi di rete dell'area di Azure in cui viene distribuito. Questo indirizzo IP è soggetto a modifiche senza preavviso. Gli aggiornamenti della rete per il Data Center, il ripristino di emergenza del Data Center o il failover a livello di area di un hub delle cose possono modificare l'indirizzo IP dell'hub. Vedere [disponibilità elevata e ripristino di emergenza dell'hub](iot-hub-ha-dr.md) Internet per altri dettagli sul failover e sulla disponibilità dell'hub Azure.

L'indirizzo IP dell'hub Internet viene modificato dopo un failover in un'altra area. È possibile testare questa funzionalità seguendo l'esercitazione [eseguire un failover manuale di un hub](tutorial-manual-failover.md)Internet.

## <a name="discover-your-iot-hub-ip-address"></a>Individuare l'indirizzo IP dell'hub Internet

È possibile individuare l'indirizzo IP dell'hub Internet delle cose usando una ricerca DNS inversa su CNAME ([*nome-Hub-* Internet]. Azure-Devices.NET). È possibile usare **nslookup** per verificare l'indirizzo IP di un'istanza dell'hub Internet delle cose:

```cmd/sh
nslookup {YourIoTHubName}.azure-devices.net
```

Questo indirizzo IP può cambiare senza preavviso. In uno scenario di failover o di ripristino di emergenza è necessario eseguire il polling dell'indirizzo IP dell'hub Internet nell'area secondaria.

## <a name="outbound-firewall-rules-for-iot-hub"></a>Regole del firewall in uscita per l'hub Internet

Provare a creare regole del firewall e filtrare in base al nome host o al dominio dell'hub Internet. Se è possibile consentire il traffico in uscita solo a indirizzi specifici, eseguire regolarmente il polling dell'indirizzo IP dell'hub Internet e aggiornare le regole del firewall.

