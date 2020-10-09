---
title: Aggiungere la libreria Kinect di Azure al progetto di Visual Studio
description: Informazioni su come aggiungere il pacchetto NuGet di Azure Kinect al progetto di Visual Studio.
author: wes-b
ms.author: wesbarc
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, Sensor, SDK, Visual Studio 2017, Visual Studio 2019, NuGet
ms.openlocfilehash: b0395118481cbaecd5ad0b6a3a6b3e89cc29dfaf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85276715"
---
# <a name="add-azure-kinect-library-to-your-visual-studio-project"></a>Aggiungere la libreria Kinect di Azure al progetto di Visual Studio

Questo articolo illustra il processo di aggiunta del pacchetto NuGet di Azure Kinect al progetto di Visual Studio.

## <a name="install-azure-kinect-nuget-package"></a>Installare il pacchetto NuGet di Azure Kinect

Per installare il pacchetto NuGet di Azure Kinect:

1. È possibile trovare istruzioni dettagliate per l'installazione di un pacchetto NuGet in Visual Studio nella [Guida introduttiva: installare e usare un pacchetto in Visual Studio](https://docs.microsoft.com/nuget/quickstart/install-and-use-a-package-in-visual-studio).
2. Per aggiungere il pacchetto, è possibile usare l'interfaccia utente di gestione pacchetti facendo clic con il pulsante destro del mouse su riferimenti e scegliendo Gestisci pacchetti NuGet da Esplora soluzioni.
3. Scegliere [NuGet.org](https://www.nuget.org) come origine del pacchetto, selezionare scheda Sfoglia e cercare `Microsoft.Azure.Kinect.Sensor` .
4. Selezionare il pacchetto dall'elenco e installare.

## <a name="use-azure-kinect-nuget-package"></a>Usare il pacchetto NuGet di Azure Kinect

Una volta aggiunto il pacchetto, aggiungere il file di intestazione include al codice sorgente, ad esempio:

- `#include <k4a/k4a.h>`
- `#include <k4arecord/record.h>`
- `#include <k4arecord/playback.h>`

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
>[A questo punto è possibile creare la prima applicazione](build-first-app.md)
