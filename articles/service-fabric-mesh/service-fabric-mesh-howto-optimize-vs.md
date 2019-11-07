---
title: Ottimizzare le prestazioni di Visual Studio per i progetti Azure Service Fabric Mesh | Microsoft Docs
description: Questo articolo illustra come ottimizzare le prestazioni di Visual Studio per i progetti Service Fabric Mesh in modo che l'esecuzione del primo debug (F5) sia molto più rapida.
services: service-fabric-mesh
keywords: ottimizzare le prestazioni di debug
author: dkkapur
ms.author: dekapur
ms.date: 11/29/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: chakdan
ms.openlocfilehash: 04aeaa6b008b50789f4380e4bb98beba3957c2e9
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73663425"
---
# <a name="optimize-visual-studio-performance-for-service-fabric-mesh-projects"></a>Ottimizzare le prestazioni di Visual Studio per i progetti Azure Service Fabric Mesh

Questo articolo illustra come ottimizzare le prestazioni di Visual Studio per i progetti Service Fabric Mesh in modo che l'esecuzione del primo debug (F5) sia molto più rapida.  

## <a name="change-visual-studio-settings"></a>Cambiare le impostazioni di Visual Studio
 
In Visual Studio, in **Strumenti** > **Opzioni**  > **Service Fabric Mesh Tools** (Strumenti di Service Fabric Mesh)  > **Generale**, è possibile modificare le impostazioni seguenti:

- **Pull required Docker images on project open** (Esegui il pull delle immagini Docker necessarie all'apertura del progetto) velocizza la prima esecuzione del debug (F5) avviando il processo di download delle immagini durante il caricamento del progetto.  
- **Deploy application on project open** (Distribuisci l'applicazione all'apertura del progetto) può velocizzare la prima esecuzione del debug (F5) avviando il processo di distribuzione dopo l'apertura del progetto.  
- **Remove application on project close** (Rimuovi l'applicazione alla chiusura del progetto) recupera le risorse (CPU, RAM) allocate all'app rimuovendo l'app Mesh quando il progetto viene chiuso.  

Se nella finestra di output di Strumenti di Service Fabric vengono visualizzati messaggi che indicano che Visual Studio sta eseguendo il pull delle immagini, si sta preparando o sta rimuovendo l'applicazione, si riferiscono alle impostazioni descritte sopra. È possibile disattivare queste impostazioni.

## <a name="next-steps"></a>Passaggi successivi

[Esercitazione: eseguire il debug di un'applicazione Azure Service Fabric Mesh in esecuzione nel cluster di sviluppo locale](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)