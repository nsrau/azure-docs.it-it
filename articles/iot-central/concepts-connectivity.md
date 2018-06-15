---
title: Connettività dei dispositivi in Azure IoT Central | Microsoft Docs
description: Questo articolo presenta i concetti fondamentali relativi alla connettività dei dispositivi in Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: dc9fe144c2258f33ce59c61ce63c15835cc3fa53
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628335"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Connettività dei dispositivi in Azure IoT Central

Questo articolo presenta i concetti fondamentali relativi alla connettività dei dispositivi in Microsoft Azure IoT Central.

Ogni dispositivo che si connette all'applicazione Azure IoT Central si connette agli [endpoint](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-endpoints) esposti dall'hub IoT che Azure IoT Central usa. L'hub IoT consente connessioni scalabili, sicure e affidabili dai dispositivi connessi.

## <a name="sdk-support"></a>Supporto SDK

Gli SDK di dispositivo di Azure rappresentano il modo più semplice per implementare il codice nei dispositivi che si connettono all'applicazione Azure IoT Central. Attualmente sono disponibili gli SDK seguenti:

- [Azure IoT SDK per C](https://github.com/azure/azure-iot-sdk-c)
- [Azure IoT SDK per Python](https://github.com/azure/azure-iot-sdk-python)
- [Azure IoT SDK per Node.js](https://github.com/azure/azure-iot-sdk-node)
- [Azure IoT SDK per Java](https://github.com/azure/azure-iot-sdk-java)
- [Azure IoT SDK per .NET](https://github.com/azure/azure-iot-sdk-csharp)

Ogni dispositivo si connette usando una stringa di connessione univoca che identifica il dispositivo stesso. Un dispositivo può connettersi solo all'hub IoT in cui è registrato. Quando si crea un dispositivo reale nell'applicazione Azure IoT Central, l'applicazione genera una stringa di connessione da usare.

## <a name="sdk-features-and-iot-hub-connectivity"></a>Funzioni dell'SDK e connettività dell'hub IoT

Tutte le comunicazioni dei dispositivi con l'hub IoT usano le opzioni di connettività dell'hub IoT seguenti:

- [Messaggistica da dispositivo a cloud](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)
- [Dispositivi gemelli](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins)

La tabella seguente riepiloga le corrispondenze tra le funzionalità di dispositivo di Azure IoT Central e le funzionalità dell'hub IoT:

| Azure IoT Central | Hub IoT Azure |
| ----------- | ------- |
| Misura: dati di telemetria | Messaggistica da dispositivo a cloud |
| Proprietà dei dispositivi | Proprietà segnalate del dispositivo gemello |
| Impostazioni | Proprietà desiderate e segnalate nel dispositivo gemello |

Per altre informazioni sull'uso degli SDK di dispositivo, vedere uno degli articoli seguenti con esempi di codice:

- [Connettere un'applicazione client Node.js generica all'applicazione Azure IoT Central](howto-connect-nodejs.md)
- [Connettere un dispositivo Raspberry Pi all'applicazione Azure IoT Central](howto-connect-raspberry-pi-python.md)
- [Connettere un dispositivo DevDiv kit all'applicazione Azure IoT Central](howto-connect-devkit.md).

Il video seguente offre una panoramica di come connettere un dispositivo reale ad Azure IoT Central:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Connect-real-devices-to-Microsoft-IoT-Central/Player]

## <a name="protocols"></a>Protocolli

Gli SDK di dispositivo supportano i protocolli di rete seguenti per la connessione a un hub IoT:

- MQTT
- AMQP
- HTTPS

Per informazioni sulle differenze fra questi protocolli e indicazioni su come sceglierne uno, vedere [Scegliere un protocollo di comunicazione](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols).

Se il dispositivo non può usare i protocolli supportati, è possibile usare Azure IoT Edge per la conversione del protocollo. IoT Edge supporta altri scenari di intelligence di perimetro per scaricare l'elaborazione sul perimetro dall'applicazione Azure IoT Central.

## <a name="security"></a>Sicurezza

Tutti i dati scambiati tra i dispositivi e Azure IoT Central vengono crittografati. L'hub IoT autentica ogni richiesta da un dispositivo che si connette a qualsiasi endpoint dell'hub IoT a contatto con i dispositivi. Per evitare lo scambio di credenziali nella rete, un dispositivo usa i token firmati per l'autenticazione. Per altre informazioni, vedere [Controllare l'accesso all'hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security).

> [!NOTE]
> Attualmente, i dispositivi che si connettono ad Azure IoT Central devono usare i token di firma di accesso condiviso. I certificati X.509 non sono supportati per i dispositivi che si connettono ad Azure IoT Central.

## <a name="next-steps"></a>Passaggi successivi

Ora che si conosce la connettività dei dispositivi in Azure IoT Central, ecco i passaggi successivi suggeriti:

- [Preparare e connettere un dispositivo DevKit](howto-connect-devkit.md)
- [Preparare e connettere un dispositivo Raspberry Pi](howto-connect-raspberry-pi-python.md)
- [Connettere un'applicazione client Node.js generica all'applicazione Azure IoT Central](howto-connect-nodejs.md)
