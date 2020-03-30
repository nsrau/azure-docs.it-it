---
title: Connettersi a Windows Virtual Desktop da Android - AzureConnect to Windows Virtual Desktop from Android - Azure
description: Come connettersi a Windows Virtual Desktop utilizzando il client Android.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: d2990c82efbcdac7d453f920301787b8c83db1e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295369"
---
# <a name="connect-with-the-android-client"></a>Connettersi con il client Android

> Si applica a: Android 4.1 e versioni successive, Chromebook con ChromeOS 53 e versioni successive.

>[!NOTE]
> La possibilità di accedere alle risorse di Windows Virtual Desktop dal client Android è attualmente disponibile in anteprima.

Puoi accedere alle risorse di Windows Virtual Desktop dal tuo dispositivo Android con il nostro client scaricabile. Puoi anche utilizzare il client Android sui dispositivi Chromebook che supportano Google Play Store. Questa guida ti dirà come configurare il client Android.

## <a name="install-the-android-client"></a>Installare il client Android

Per iniziare, [scarica](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx) e installa il client sul tuo dispositivo Android.

## <a name="subscribe-to-a-feed"></a>Effettuare la sottoscrizione a un feed

Sottoscrivi il feed fornito dall'amministratore per ottenere l'elenco delle risorse gestite a cui puoi accedere sul tuo dispositivo Android.

Per abbonarsi a un feed:

1. Nel Centro connessioni **+** toccare e quindi **Feed risorse remoto**.
2. Inserisci l'URL del feed nel campo **URL feed.** L'URL del feed può essere un URL o un indirizzo email.
   - Se si utilizza un URL, utilizzare quello <https://rdweb.wvd.microsoft.com>che l'amministratore ti ha dato, normalmente .
   - Per utilizzare l'indirizzo e-mail, immettere l'indirizzo di posta elettronica. Il client cercherà un URL associato al tuo indirizzo email se il tuo amministratore ha configurato il server in questo modo.
3. Toccare **AVANTI**.
4. Specificare le credenziali quando richiesto.
   - Per **Nome utente**, assegnare al nome utente l'autorizzazione per accedere alle risorse.
   - Per **Password**, fornire la password associata al nome utente.
   - Potrebbe anche essere richiesto di fornire ulteriori fattori se l'amministratore ha configurato l'autenticazione in questo modo.

Dopo la sottoscrizione, il Centro connessioni dovrebbe visualizzare le risorse remote.

Una volta sottoscritto un feed, il contenuto del feed verrà aggiornato automaticamente su base regolare. Le risorse possono essere aggiunte, modificate o rimosse in base alle modifiche apportate dall'amministratore.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su come utilizzare il client Android, vedere [Introduzione al client Android](/windows-server/remote/remote-desktop-services/clients/remote-desktop-android/).
