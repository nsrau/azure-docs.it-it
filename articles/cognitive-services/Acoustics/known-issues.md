---
title: Problemi noti con il plug-in Project Acoustics
titlesuffix: Azure Cognitive Services
description: Durante l'utilizzo della versione Designer Preview di Project Acoustics possono verificarsi i problemi noti descritti di seguito.
services: cognitive-services
author: kylestorck
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: kylestorck
ms.openlocfilehash: 2f7f7db31debd7663be44b79c42ab151a96ae7e5
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55869042"
---
# <a name="known-issues"></a>Problemi noti
Durante l'utilizzo della versione Designer Preview di Project Acoustics possono verificarsi i problemi noti descritti di seguito.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Quando si rinomina una scena, i parametri acustici vanno persi

Se si rinomina una scena, i parametri acustici che appartengono alla scena non vengono trasferiti automaticamente alla nuova scena. Tuttavia, saranno ancora presenti nel vecchio file di asset. Cercare il file **SceneName_AcousticParameters.asset** all'interno della directory **Editor** accanto al file della scena. Rinominare il file in modo da riflettere il nuovo nome della scena.

## <a name="runtime-voxels-are-a-different-size-than-scene-preview-voxels"></a>I voxel in fase di esecuzione sono di dimensioni diverse rispetto ai voxel dell’anteprima scena

Se si esegue un'operazione **Calculate** (Calcola) nella scheda **Probes** (Probe) e si visualizzano i voxel, quindi si effettua un bake e si visualizzano i voxel in fase di esecuzione per la stessa scena, i voxel sono di dimensioni diverse. I voxel mostrati pre-bake sono quelli usati nella simulazione. I voxel mostrati in fase di esecuzione vengono usati per l'interpolazione tra i punti di probe. Questo può causare un'incoerenza per cui in fase di esecuzione vengono visualizzati aperti portali che effettivamente non lo sono.

## <a name="unity-crashes-when-closing-project"></a>Arresto anomalo di Unity alla chiusura del progetto

Nelle versioni più recenti di Unity (2018.2+) esiste un bug noto per cui alla chiusura del progetto l'applicazione si arresta in modo anomalo. Questo caso è segnalato in [questo problema di Unity](https://issuetracker.unity3d.com/issues/crash-on-assetdatabase-getassetimporterversions-when-closing-a-specific-unity-project).

## <a name="trouble-deploying-to-android"></a>Problemi durante la distribuzione in Android
Per usare Project Acoustics in Android, modificare la destinazione della compilazione in Android. In alcune versioni di Unity esiste un bug che incide sulla distribuzione dei plug-in audio. Assicurarsi di non usare una versione interessata da [questo bug](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player).

## <a name="i-get-an-error-that-could-not-find-metadata-file-systemsecuritydll"></a>Errore "Could not find metadata file System.Security.dll" (Impossibile trovare il file di metadati System.Security.dll)

Verificare che la versione del runtime di scripting nelle impostazioni del lettore sia impostata su **.NET 4.x Equivalent** e riavviare Unity.

## <a name="im-having-authentication-problems-when-connecting-to-azure"></a>Problemi di autenticazione quando ci si connette ad Azure

Verificare di aver usato le credenziali corrette per l'account di Azure, che l'account supporti il tipo di nodo richiesto nel bake e che l'orologio di sistema sia preciso.

## <a name="canceling-a-bake-leaves-the-bake-tab-in-deleting-state"></a>Se si annulla un bake, la scheda Bake rimane nello stato "eliminazione"
Al momento del completamento o annullamento di un processo, Project Acoustics elimina tutte le relative risorse di Azure e l'operazione può richiedere fino a 5 minuti.

## <a name="next-steps"></a>Passaggi successivi
* Iniziare a [integrare l'acustica nei progetti Unity](getting-started.md)

