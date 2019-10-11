---
title: Problemi noti del plug-in Project Acoustics
titlesuffix: Azure Cognitive Services
description: È possibile che si verifichino i problemi noti seguenti nell'acustica del progetto.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: b71b93f271608d946d964f70dae9eefbef77e87b
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243034"
---
# <a name="project-acoustics-known-issues"></a>Problemi noti dei progetti acustici
Questo articolo descrive i problemi che possono verificarsi quando si usa l'acustica del progetto.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Quando si rinomina una scena, i parametri acustici vanno persi

Se si rinomina una scena, tutti i parametri acustici che appartengono a tale scena non vengono trasferiti automaticamente alla nuova scena. Ma esistono ancora nel vecchio file di asset. Cercare il file *[scenename] _AcousticParameters. asset* nella directory dell' *Editor* accanto al file della scena. Rinominare il file in modo che corrisponda al nuovo nome della scena.

## <a name="deploy-to-android-bug-from-some-unity-versions"></a>Bug da distribuire a Android da alcune versioni di Unity

Alcune versioni di Unity presentano un [bug](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player) nella modalità di distribuzione dei plug-in audio in Android. Assicurarsi che non si stia usando una versione interessata da questo bug.

## <a name="could-not-find-metadata-file-systemsecuritydll-error"></a>Errore "Impossibile trovare il file System di metadati. Security. dll"

Verificare che la **versione del runtime di scripting** nelle impostazioni del **lettore** sia *equivalente a .NET 4. x*e riavviare Unity.

## <a name="authentication-problems-when-connecting-to-azure"></a>Problemi di autenticazione durante la connessione ad Azure

Verificare quanto segue:
- Sono state usate le credenziali corrette per l'account Azure.
- L'account supporta il tipo di nodo richiesto in Bake.
- Il clock di sistema è impostato correttamente.

## <a name="the-bake-tab-still-shows-deleting-after-you-cancel"></a>La scheda Bake Mostra ancora "eliminazione" dopo l'annullamento
Il progetto Acoustics pulisce tutte le risorse di Azure per un processo dopo il completamento o l'annullamento. Questo processo può richiedere fino a 5 minuti.

## <a name="next-steps"></a>Passaggi successivi
* Provare l' [Unity](unity-quickstart.md) o il contenuto di esempio non [reale](unreal-quickstart.md) .
