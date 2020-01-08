---
title: Ottimizzazione di Visual Studio per Azure Service Fabric mesh
description: Questo articolo illustra come ottimizzare le prestazioni di Visual Studio per i progetti Service Fabric Mesh in modo che l'esecuzione del primo debug (F5) sia molto più rapida.
author: dkkapur
ms.author: dekapur
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: 40c4e9972a3d83a5dd8247bacac12e9d67a15f66
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/26/2019
ms.locfileid: "75497971"
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