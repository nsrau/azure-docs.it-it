---
title: Registrare un nuovo dispositivo dal portale di Azure - Azure IoT Edge | Microsoft Docs
description: Usare il portale di Azure per registrare un nuovo dispositivo IoT Edge e recuperare la stringa di connessione
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/03/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 6414f694296ce1f5a8b65ccab30cceaf2172dee7
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2019
ms.locfileid: "53974897"
---
# <a name="register-a-new-azure-iot-edge-device-from-the-azure-portal"></a>Registrare un nuovo dispositivo Azure IoT Edge dal portale di Azure

Per poter usare i dispositivi IoT con Azure IoT Edge, è necessario registrarli nell'hub IoT. Dopo la registrazione di un dispositivo, si riceve una stringa di connessione che può essere usata per configurare il dispositivo per i carichi di lavoro Edge.

Questo articolo illustra come registrare un nuovo dispositivo IoT Edge usando il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) nella sottoscrizione di Azure.

## <a name="create-a-device"></a>Creare un dispositivo

Nel portale di Azure i dispositivi IoT Edge vengono creati e gestiti separatamente da quelli che si connettono all'hub IoT ma non sono abilitati per Edge.

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'hub IoT.
2. Selezionare **IoT Edge** dal menu.
3. Selezionare **Aggiungi un dispositivo IoT Edge**.
4. Specificare un ID descrittivo del dispositivo. Usare le impostazioni predefinite per la generazione automatica delle chiavi di autenticazione e la connessione del nuovo dispositivo all'hub.
5. Selezionare **Salva**.

## <a name="view-all-devices"></a>Visualizzare tutti i dispositivi

Tutti i dispositivi abilitati per Edge che si connettono all'hub IoT sono elencati nella pagina **IoT Edge**.

## <a name="retrieve-the-connection-string"></a>Recuperare la stringa di connessione

Quando si è pronti per configurare il dispositivo, è necessaria la stringa di connessione che collega il dispositivo fisico alla relativa identità nell'hub IoT.

1. Dalla pagina **IoT Edge** nel portale fare clic sull'ID del dispositivo nell'elenco dei dispositivi Edge.
2. Copiare il valore di **Stringa di connessione (chiave primaria)** o **Stringa di connessione (chiave secondaria)**.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [distribuire moduli in un dispositivo con il portale di Azure](how-to-deploy-modules-portal.md)
