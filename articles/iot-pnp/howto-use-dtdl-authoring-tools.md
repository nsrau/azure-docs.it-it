---
title: Usare uno strumento per creare e convalidare i modelli DTDL | Microsoft Docs
description: Installare l'editor DTDL per Visual Studio Code o Visual Studio 2019 e usarlo per creare modelli Plug and Play.
author: dominicbetts
ms.author: dobett
ms.date: 09/14/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 59ea0258d6262f652e9ac563bbda5ec89c4c5819
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91580423"
---
# <a name="install-and-use-the-dtdl-authoring-tools"></a>Installare e usare gli strumenti di creazione di DTDL

I modelli [DTDL (Digital Gemini Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) sono file JSON. È possibile usare un'estensione per Visual Studio Code o Visual Studio 2019 per creare e convalidare i file di modello.

## <a name="install-and-use-the-vs-code-extension"></a>Installare e usare l'estensione VS Code

L'estensione DTDL per VS Code aggiunge le funzionalità di creazione di DTDL seguenti:

- Convalida della sintassi DTDL V2.
- IntelliSense, incluso il completamento automatico, per semplificare la sintassi del linguaggio.
- Possibilità di creare interfacce dal riquadro comandi.

Per installare l'estensione DTDL, passare all' [Editor DTDL per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl). È anche possibile cercare **DTDL** nella visualizzazione estensioni vs code.

Dopo aver installato l'estensione, usarla per semplificare la creazione dei file di modello DTDL in Visual Studio Code:

- L'estensione fornisce la convalida della sintassi nei file di modello DTDL, evidenziando gli errori come illustrato nello screenshot seguente:

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-validation.png" alt-text="Convalida di modelli in VS Code":::

- Usare IntelliSense e il completamento automatico quando si modificano i modelli DTDL:

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-intellisense.png" alt-text="Convalida di modelli in VS Code":::

- Creare una nuova interfaccia DTDL. Il comando seguente crea un file JSON con una nuova interfaccia. L'interfaccia include la telemetria di esempio, la proprietà e le definizioni dei comandi.

## <a name="install-and-use-the-visual-studio-extension"></a>Installare e usare l'estensione di Visual Studio

L'estensione DTDL per Visual Studio 2019 aggiunge le funzionalità di creazione di DTDL seguenti:

- Convalida della sintassi DTDL V2.
- IntelliSense, incluso il completamento automatico, per semplificare la sintassi del linguaggio.

Per installare l'estensione DTDL, passare al [supporto per la lingua di DTDL per VS 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16dtdllanguagesupport). È anche possibile cercare **DTDL** in **Gestisci estensioni** in Visual Studio.

Dopo aver installato l'estensione, usarla per semplificare la creazione dei file di modello DTDL in Visual Studio:

- L'estensione fornisce la convalida della sintassi nei file di modello DTDL, evidenziando gli errori come illustrato nello screenshot seguente:

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-validation-2.png" alt-text="Convalida di modelli in VS Code":::

- Usare IntelliSense e il completamento automatico quando si modificano i modelli DTDL:

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-intellisense-2.png" alt-text="Convalida di modelli in VS Code":::

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sulle procedure si è appreso come usare le estensioni DTDL per Visual Studio Code e Visual Studio 2019 per creare e convalidare i file di modello DTDL. Un passaggio successivo suggerito consiste nell'imparare a usare Azure Internet [Explorer con i modelli e i dispositivi](./howto-use-iot-explorer.md).
