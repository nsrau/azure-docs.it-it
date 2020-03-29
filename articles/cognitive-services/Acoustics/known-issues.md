---
title: Problemi noti del plug-in di acustica del progetto
titlesuffix: Azure Cognitive Services
description: È possibile che si verifichino i seguenti problemi noti in Project Acoustics.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "72243034"
---
# <a name="project-acoustics-known-issues"></a>Problemi noti relativi all'acustica del progetto
In questo articolo vengono descritti i problemi che possono verificarsi quando si utilizza l'acustica di progetto.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Quando si rinomina una scena, i parametri acustici vanno persi

Se si rinomina una scena, tutti i parametri acustici che appartengono a tale scena non vengono trasferiti automaticamente nella nuova scena. Ma esistono ancora nel vecchio file di risorse. Cercate il file *[SceneName]_AcousticParameters.asset* nella directory *Editor* accanto al file di scena. Rinominare il file in base al nome della nuova scena.

## <a name="deploy-to-android-bug-from-some-unity-versions"></a>Distribuire-ad Android bug da alcune versioni di Unity

Alcune versioni di Unity hanno un [bug](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player) nel modo in cui distribuiscono plug-in audio per Android. Assicurarsi che non si sta utilizzando una versione interessata da questo bug.

## <a name="could-not-find-metadata-file-systemsecuritydll-error"></a>Errore "Impossibile trovare il file di metadati System.Security.dll"

Assicurarsi che la versione di **Runtime di scripting** nelle impostazioni del **lettore** sia equivalente a *.NET 4.x*e riavviare Unity.

## <a name="authentication-problems-when-connecting-to-azure"></a>Problemi di autenticazione durante la connessione ad AzureAuthentication problems when connecting to Azure

Controllare che:
- Sono stati usate le credenziali corrette per l'account Azure.You used the correct credentials for your Azure account.
- L'account supporta il tipo di nodo richiesto nel riquadro di configurazione.
- L'orologio di sistema è impostato correttamente.

## <a name="the-bake-tab-still-shows-deleting-after-you-cancel"></a>La scheda Cuocere mostra ancora "eliminazione" dopo l'annullamento
Project Acoustics pulisce tutte le risorse di Azure per un processo dopo il completamento o l'annullamento. Questo processo può richiedere fino a 5 minuti.

## <a name="next-steps"></a>Passaggi successivi
* Prova il contenuto di esempio [Unity](unity-quickstart.md) o [Unreal.](unreal-quickstart.md)
