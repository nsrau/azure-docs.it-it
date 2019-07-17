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
ms.date: 03/20/2019
ms.author: kylsto
ms.openlocfilehash: 53df981564eb177da66b86022ecfc80b25f1c763
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68296541"
---
# <a name="project-acoustics-known-issues"></a>Problemi noti dei progetti acustici
Durante l'utilizzo della versione Designer Preview di Project Acoustics possono verificarsi i problemi noti descritti di seguito.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Quando si rinomina una scena, i parametri acustici vanno persi

Se si rinomina una scena, tutti i parametri acustici che appartengono a tale scena non verranno trasferiti automaticamente alla nuova scena. Ma continueranno a esistere nel vecchio file di asset. Cercare il file **SceneName_AcousticParameters.asset** all'interno della directory **Editor** accanto al file della scena. Rinominare il file in modo da riflettere il nuovo nome della scena.

## <a name="unity-crashes-when-closing-project"></a>Arresto anomalo di Unity alla chiusura del progetto

Nelle versioni più recenti di Unity (2018.2+) esiste un bug noto per cui alla chiusura del progetto l'applicazione si arresta in modo anomalo. Questo caso è segnalato in [questo problema di Unity](https://issuetracker.unity3d.com/issues/crash-on-assetdatabase-getassetimporterversions-when-closing-a-specific-unity-project).

## <a name="deploying-to-android-from-some-unity-versions"></a>Distribuzione in Android da alcune versioni di Unity

Alcune versioni di Unity presentano un bug per la distribuzione di plug-in audio in Android. Assicurarsi che non si stia usando una versione interessata da [questo bug](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player).

## <a name="i-get-an-error-that-could-not-find-metadata-file-systemsecuritydll"></a>Errore "Could not find metadata file System.Security.dll" (Impossibile trovare il file di metadati System.Security.dll)

Verificare che la versione del runtime di scripting nelle impostazioni del lettore sia impostata su **.NET 4.x Equivalent** e riavviare Unity.

## <a name="im-having-authentication-problems-when-connecting-to-azure"></a>Problemi di autenticazione quando ci si connette ad Azure

Verificare di aver usato le credenziali corrette per l'account di Azure, che l'account supporti il tipo di nodo richiesto nel bake e che l'orologio di sistema sia preciso.

## <a name="canceling-a-bake-leaves-the-bake-tab-in-deleting-state"></a>Se si annulla un bake, la scheda Bake rimane nello stato "eliminazione"
Il progetto Acoustics eseguirà la pulizia di tutte le risorse di Azure per un processo al completamento o all'annullamento. Questa operazione può richiedere fino a 5 minuti.

## <a name="next-steps"></a>Passaggi successivi
* Provare l' [Unity](unity-quickstart.md) o il contenuto di esempio non [reale](unreal-quickstart.md)

