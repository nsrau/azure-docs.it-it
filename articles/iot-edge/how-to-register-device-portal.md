---
title: Registrare un nuovo dispositivo dal portale di Azure - Azure IoT Edge | Microsoft Docs
description: Usare il portale di Azure per registrare un nuovo dispositivo IoT Edge e recuperare la stringa di connessione
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/03/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 7f3d0037bcf0fd33ae23c298679e3157046247cb
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70983537"
---
# <a name="register-a-new-azure-iot-edge-device-from-the-azure-portal"></a>Registrare un nuovo dispositivo Azure IoT Edge dal portale di Azure

Per poter usare i dispositivi IoT con Azure IoT Edge, è necessario registrarli nell'hub IoT. Una volta registrato un dispositivo, viene visualizzata una stringa di connessione che può essere usata per configurare il dispositivo per i carichi di lavoro IoT Edge.

Questo articolo illustra come registrare un nuovo dispositivo IoT Edge usando il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Un [Hub](../iot-hub/iot-hub-create-through-portal.md) di Internet delle cose gratuito o standard nella sottoscrizione di Azure.

## <a name="create-a-device"></a>Crea un dispositivo

Nel portale di Azure i dispositivi IoT Edge vengono creati e gestiti separatamente da quelli che si connettono all'hub IoT ma non sono abilitati per Edge.

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'hub IoT.
2. Selezionare **IoT Edge** dal menu.
3. Selezionare **Aggiungi un dispositivo IoT Edge**.
4. Specificare un ID descrittivo del dispositivo. Usare le impostazioni predefinite per generare automaticamente le chiavi di autenticazione e connettere il nuovo dispositivo all'hub.
5. Selezionare **Salva**.

## <a name="view-all-devices"></a>Visualizzare tutti i dispositivi

Tutti i dispositivi abilitati per Edge che si connettono all'hub IoT sono elencati nella pagina **IoT Edge**.

## <a name="retrieve-the-connection-string"></a>Recuperare la stringa di connessione

Quando si è pronti per configurare il dispositivo, è necessaria la stringa di connessione che collega il dispositivo fisico alla relativa identità nell'hub IoT.

1. Dalla pagina **IOT Edge** nel portale, fare clic sull'ID del dispositivo nell'elenco dei dispositivi IOT Edge.
2. Copiare il valore di **Stringa di connessione (chiave primaria)** o **Stringa di connessione (chiave secondaria)** .

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [distribuire moduli in un dispositivo con il portale di Azure](how-to-deploy-modules-portal.md).
