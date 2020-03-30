---
title: Gestire i modelli IoT Plug and Play Preview nel repository Documenti Microsoft'
description: Come gestire i modelli di funzionalità dei dispositivi nel repository usando il portale Azure Certified for IoT, l'interfaccia della riga di comando di Azure e il codice di Visual Studio.How to manage device capability models in the repository using the Azure Certified for IoT portal, the Azure CLI, and Visual Studio code.
author: Philmea
manager: philmea
ms.service: iot-pnp
services: iot-pnp
ms.topic: how-to
ms.date: 12/26/2019
ms.author: philmea
ms.openlocfilehash: 78406175090521af70381f61f4e33dfb6c35ac8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159150"
---
# <a name="manage-models-in-the-repository"></a>Gestire i modelli nel repository

Il repository di modelli IoT Plug and Play Preview archivia i modelli e le interfacce dei dispositivi. Il repository rende i modelli e le interfacce individuabili e utilizzabili dagli sviluppatori di soluzioni.

Esistono tre strumenti che è possibile utilizzare per gestire il repository:

- Il portale Azure Certified for IoT
- Interfaccia della riga di comando di Azure
- Visual Studio Code

## <a name="model-repositories"></a>Archivi modello

Esistono due tipi di repository di modelli per l'archiviazione di interfacce e modelli di funzionalità del dispositivo:There are two types of model repository for storing device capability models and interfaces:

- È disponibile un unico _repository pubblico_ che archivia i modelli e le interfacce di funzionalità dei dispositivi per i dispositivi nel catalogo dei dispositivi Azure Certified [for IoT](https://aka.ms/iotdevcat). Questo repository memorizza anche [le interfacce comuni](./concepts-common-interfaces.md) e i [DPM e le interfacce pubblicate da Microsoft Partners](./howto-onboard-portal.md). Per informazioni su come certificare un dispositivo e aggiungere il relativo modello di funzionalità del dispositivo al repository pubblico, vedere l'esercitazione [Certificare il dispositivo Plug and Play IoT](./tutorial-certification-test.md).
- Esistono più _repository aziendali_. Un repository aziendale viene creato automaticamente per l'organizzazione quando si [esegue l'onboarding al portale Azure Certified for IoT](./howto-onboard-portal.md). È possibile utilizzare il repository aziendale per archiviare i modelli e le interfacce di funzionalità del dispositivo durante lo sviluppo e il test.

## <a name="azure-certified-for-iot-portal"></a>Azure Certified per il portale IoT

Nel portale Azure Certified for IoT è possibile completare le attività seguenti:In the [Azure Certified for IoT portal,](https://preview.catalog.azureiotsolutions.com)you can complete the following tasks:

- Completa il processo di [certificazione per il tuo dispositivo IoT](./tutorial-certification-test.md).
- Trova i modelli di funzionalità dei dispositivi IoT Plug and Play. È possibile utilizzare questi modelli per creare rapidamente dispositivi pronti per [l'IoT e integrarli con le soluzioni](./quickstart-connect-pnp-device-solution-node.md).

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure fornisce comandi per la gestione dei modelli e delle interfacce delle funzionalità dei dispositivi nei repository di modelli pubblici e aziendali IoT Plug and Play.The Azure CLI provides commands for managing device capability models and interfaces in the IoT Plug and Play public and company model repositories. Per altre informazioni, vedere la guida alle procedure per [l'installazione e l'uso dell'interfaccia della](./howto-install-pnp-cli.md) riga di comando di Azure per l'interfaccia della riga di comando di Azure.For more information, see the Install and use the Azure IoT extension for Azure CLI how-to guide.

## <a name="visual-studio-code"></a>Visual Studio Code

Per aprire la visualizzazione **Repository** modelli in Visual Studio Code.

1. Aprire Visual Studio Code , utilizzare **Ctrl , Maiusc , P**, digitare e selezionare Plug and Play **IoT: Apri repository modelli**.

1. È possibile scegliere **di aprire Public Model Repository** o Open Organizational Model **Repository**. Per il repository dei modelli aziendali, è necessario immettere la stringa di connessione del repository dei modelli. Questa stringa di connessione è reperibile nel [portale Microsoft Azure Certified per IoT](https://preview.catalog.azureiotsolutions.com) nella scheda **Stringhe di connessione** per il **repository aziendale**.

1. Viene visualizzata **una** nuova scheda.

    Utilizzare questa visualizzazione per aggiungere, scaricare ed eliminare modelli e interfacce di funzionalità del dispositivo. È possibile utilizzare un filtro per trovare elementi specifici nell'elenco.

1. Per passare dall'archivio dei modelli aziendali a quello pubblico e l'archivio dei modelli pubblici, digitare **Ctrl , Maiusc e P**, digitare e selezionare Plug and Play **IoT: Disconnetti repository modelli**. Quindi utilizzare nuovamente il comando **IoT Plug and Play: Open Model Repository.**

> [!NOTE]
> Nel codice VS, il repository del modello pubblico è di sola lettura. I partner Microsoft possono aggiornare il repository pubblico nel [portale Azure Certified for IoT](https://preview.catalog.azureiotsolutions.com).

## <a name="next-steps"></a>Passaggi successivi

Il passaggio successivo consigliato consiste nell'imparare a [inviare un dispositivo IoT Plug and Play per](tutorial-certification-test.md)la certificazione .
