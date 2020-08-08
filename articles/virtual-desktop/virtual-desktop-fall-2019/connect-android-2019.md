---
title: Connettersi a desktop virtuale Windows (classico) da Android-Azure
description: Come connettersi a desktop virtuale Windows (classico) usando il client Android.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b0fadcc592f5dd52bf1b72dc9d5658866ffba7f4
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2020
ms.locfileid: "88008475"
---
# <a name="connect-to-windows-virtual-desktop-classic-with-the-android-client"></a>Connettersi a desktop virtuale Windows (classico) con il client Android

> Si applica a: Android 4,1 e versioni successive, Chromebook con ChromeOS 53 e versioni successive.

>[!IMPORTANT]
>Questo contenuto si applica a Desktop virtuale Windows (versione classica), che non supporta gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se occorre gestire gli oggetti Azure Resource Manager di Desktop virtuale Windows, vedere [questo articolo](../connect-android.md).

È possibile accedere alle risorse di desktop virtuali Windows dal dispositivo Android con il client scaricabile. È anche possibile usare il client Android sui dispositivi Chromebook che supportano il Google Play Store. Questa guida illustra come configurare il client Android.

## <a name="install-the-android-client"></a>Installare il client Android

Per iniziare, [scaricare](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx) e installare il client nel dispositivo Android.

## <a name="subscribe-to-a-feed"></a>Effettuare la sottoscrizione a un feed

Sottoscrivere il feed fornito dall'amministratore per ottenere l'elenco delle risorse gestite a cui è possibile accedere nel dispositivo Android.

Per sottoscrivere un feed:

1. In Connection Center (Centro connessioni) tocca **+** e quindi **Remote Resource Feed** (Feed risorse remote).
2. Immettere l'URL del feed nel campo **feed URL** . L'URL del feed può essere un URL o un indirizzo di posta elettronica.
   - Se si usa un URL, usare quello che l'amministratore ha assegnato, normalmente <https://rdweb.wvd.microsoft.com> .
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