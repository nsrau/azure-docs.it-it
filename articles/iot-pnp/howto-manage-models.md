---
title: Gestisci i modelli di anteprima Plug and Play nel repository | Microsoft Docs '
description: Come gestire i modelli di funzionalità del dispositivo nel repository usando il portale di Azure Certified per gli elementi, l'interfaccia della riga di comando di Azure e Visual Studio Code.
author: YasinMSFT
manager: philmea
ms.service: iot-pnp
services: iot-pnp
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: yahajiza
ms.openlocfilehash: 1b71d8bd0f0417c7dc408c580a1c73ac654743ce
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70932866"
---
# <a name="manage-models-in-the-repository"></a>Gestire i modelli nel repository

Il repository del modello Plug and Play di anteprima archivia i modelli e le interfacce di funzionalità del dispositivo. Il repository rende i modelli e le interfacce individuabili e utilizzabili dagli sviluppatori di soluzioni.

Sono disponibili tre strumenti che è possibile usare per gestire il repository:

- Il portale di Azure Certified per le cose
- Interfaccia della riga di comando di Azure
- Visual Studio Code

## <a name="model-repositories"></a>Repository di modelli

Esistono due tipi di repository di modelli per l'archiviazione di modelli e interfacce per le funzionalità dei dispositivi:

- È disponibile un unico _repository pubblico_ che archivia i modelli e le interfacce di funzionalità del dispositivo per i dispositivi nel catalogo dei dispositivi [Azure Certified per](https://aka.ms/iotdevcat)Internet. Questo repository archivia anche le [interfacce comuni](./concepts-common-interfaces.md) e [DCMS e le interfacce pubblicate dai partner Microsoft](./howto-onboard-portal.md). Per informazioni su come certificare un dispositivo e aggiungere il modello di funzionalità del dispositivo al repository pubblico, vedere l'esercitazione [certificare il dispositivo Plug and Play](./tutorial-certification-test.md).
- Sono presenti più _repository aziendali_. Un repository aziendale viene creato automaticamente per l'organizzazione quando si esegue [l'onboarding nel portale Azure Certified for](./howto-onboard-portal.md)Internet. È possibile usare il repository aziendale per archiviare i modelli e le interfacce di funzionalità del dispositivo durante le fasi di sviluppo e test.

## <a name="azure-certified-for-iot-portal"></a>Portale Azure Certified per le cose

Nel [portale di Azure Certified per](https://preview.catalog.azureiotsolutions.com)l'it è possibile completare le attività seguenti:

- [Completare il processo di certificazione per il dispositivo Internet delle](./tutorial-certification-test.md)cose.
- Trovare i modelli di funzionalità del dispositivo Plug and Play. È possibile usare questi modelli per [creare rapidamente dispositivi pronti per l'uso e integrarli con soluzioni](./quickstart-connect-pnp-device-solution.md).

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure fornisce comandi per la gestione di modelli e interfacce per le funzionalità dei dispositivi nei repository Plug and Play pubblici e dei modelli aziendali. Per altre informazioni, vedere la Guida alle procedure per l' [installazione e l'uso dell'estensione Azure per l'interfaccia della](./howto-install-pnp-cli.md) riga di comando di Azure.

## <a name="visual-studio-code"></a>Visual Studio Code

Per aprire la vista del **repository di modelli** in Visual Studio Code.

1. Aprire Visual Studio Code, premere **CTRL + MAIUSC + P**, digitare e selezionare **plug and Play: Aprire il repository**del modello.

1. È possibile scegliere di **aprire il repository del modello pubblico** o **aprire il repository del modello aziendale**. Per il repository del modello aziendale, è necessario immettere la stringa di connessione del repository del modello. Questa stringa di connessione è reperibile nel [portale Microsoft Azure Certified per IoT](https://preview.catalog.azureiotsolutions.com) nella scheda **Stringhe di connessione** per il **repository aziendale**.

1. Una nuova scheda apre la visualizzazione del **repository del modello** .

    Usare questa visualizzazione per aggiungere, scaricare ed eliminare modelli e interfacce per le funzionalità del dispositivo. È possibile utilizzare un filtro per trovare elementi specifici nell'elenco.

1. Per spostarsi tra il repository del modello aziendale e il repository del modello pubblico, usare **CTRL + MAIUSC + P**, digitare **e selezionare Plug and Play: Repository**del modello di disconnessione. Usare quindi il **plug and Play di Internet delle cose: Aprire di nuovo** il comando modello repository.

> [!NOTE]
> In VS Code, il repository del modello pubblico è di sola lettura. I partner Microsoft possono aggiornare il repository pubblico nel [portale Azure Certified per](https://preview.catalog.azureiotsolutions.com)l'it.

## <a name="next-steps"></a>Passaggi successivi

Il passaggio successivo suggerito consiste nell'apprendere come [inviare un plug and Play dispositivo per la certificazione](tutorial-certification-test.md).
