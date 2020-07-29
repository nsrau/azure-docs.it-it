---
title: Connettersi a desktop virtuale Windows da Android-Azure
description: Come connettersi al desktop virtuale di Windows tramite il client Android.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/25/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 40a907cdeb4934b4518fb6b1923d69a004f14d8d
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87291479"
---
# <a name="connect-to-windows-virtual-desktop-with-the-android-client"></a>Connettersi al desktop virtuale Windows con il client Android

> Si applica a: Android 4,1 e versioni successive, Chromebook con ChromeOS 53 e versioni successive.

>[!IMPORTANT]
>Questo contenuto si applica al desktop virtuale di Windows con Azure Resource Manager oggetti desktop virtuali di Windows. Se si usa desktop virtuale di Windows (classico) senza Azure Resource Manager oggetti, vedere [questo articolo](./virtual-desktop-fall-2019/connect-android-2019.md).

>[!NOTE]
> La possibilità di accedere alle risorse di desktop virtuali Windows dal client Android è attualmente disponibile in anteprima.

È possibile accedere alle risorse di desktop virtuali Windows dal dispositivo Android con il client scaricabile. È anche possibile usare il client Android sui dispositivi Chromebook che supportano il Google Play Store. Questa guida illustra come configurare il client Android.

## <a name="install-the-android-client"></a>Installare il client Android

Per iniziare, [scaricare](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx) e installare il client nel dispositivo Android.

## <a name="subscribe-to-a-feed"></a>Effettuare la sottoscrizione a un feed

Sottoscrivere il feed fornito dall'amministratore per ottenere l'elenco delle risorse gestite a cui è possibile accedere nel dispositivo Android.

Per sottoscrivere un feed:

1. In Connection Center (Centro connessioni) tocca **+** e quindi **Remote Resource Feed** (Feed risorse remote).
2. Immettere l'URL del feed nel campo **feed URL** . L'URL del feed può essere un URL o un indirizzo di posta elettronica.
   - Se si usa un URL, usare quello che l'amministratore ha assegnato, normalmente <https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery> .
   - Nel caso dell'e-mail, immetti il tuo indirizzo. Il client eseguirà la ricerca di un URL associato all'indirizzo di posta elettronica se l'amministratore ha configurato il server in modo.
3. Toccare **AVANTI**.
4. Specifica le credenziali quando richiesto.
   - Per **nome utente**, assegnare al nome utente l'autorizzazione per accedere alle risorse.
   - Per **password**, assegnare la password associata al nome utente.
   - È anche possibile che venga richiesto di fornire altri fattori se l'amministratore ha configurato l'autenticazione in questo modo.

Dopo la sottoscrizione, il Centro connessioni visualizzerà le risorse remote.

Una volta sottoscritto un feed, il relativo contenuto verrà aggiornato automaticamente a intervalli regolari. È possibile che vengano aggiunte, cambiate o rimosse risorse in base alle modifiche apportate dall'amministratore.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come usare il client Android, vedere Introduzione al [client Android](/windows-server/remote/remote-desktop-services/clients/remote-desktop-android/).
