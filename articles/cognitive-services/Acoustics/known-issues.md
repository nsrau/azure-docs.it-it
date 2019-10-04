---
title: Problemi noti con il plug-in Project Acoustics
titlesuffix: Azure Cognitive Services
description: Durante l'utilizzo della versione Designer Preview di Project Acoustics possono verificarsi i problemi noti descritti di seguito.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 37084480423de90f50beced187eda202b39f8bf1
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933058"
---
# <a name="project-acoustics-known-issues"></a>Problemi noti dei progetti acustici
Durante l'utilizzo della versione Designer Preview di Project Acoustics possono verificarsi i problemi noti descritti di seguito.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Quando si rinomina una scena, i parametri acustici vanno persi

Se si rinomina una scena, tutti i parametri acustici che appartengono a tale scena non verranno trasferiti automaticamente alla nuova scena. Ma continueranno a esistere nel vecchio file di asset. Cercare il file **SceneName_AcousticParameters.asset** all'interno della directory **Editor** accanto al file della scena. Rinominare il file in modo da riflettere il nuovo nome della scena.

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

