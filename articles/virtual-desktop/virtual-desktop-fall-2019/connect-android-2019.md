---
title: Connettersi a desktop virtuale Windows da Android-Azure
description: Come connettersi al desktop virtuale di Windows tramite il client Android.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 636a31cdb657ca95ea9ea0da6bb85caa61425e5a
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82614993"
---
# <a name="connect-with-the-android-client"></a>Connettersi con il client Android

> Si applica a: Android 4,1 e versioni successive, Chromebook con ChromeOS 53 e versioni successive.

>[!IMPORTANT]
>Questo contenuto si applica alla versione 2019, che non supporta Azure Resource Manager oggetti desktop virtuali di Windows. Se si sta tentando di gestire Azure Resource Manager oggetti desktop virtuali Windows introdotti nell'aggiornamento di Spring 2020, vedere [questo articolo](../connect-android.md).

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
   - Se si usa un URL, usare quello che l'amministratore ha assegnato, normalmente <https://rdweb.wvd.microsoft.com>.
   - Per usare la posta elettronica, immettere l'indirizzo di posta elettronica. Il client eseguirà la ricerca di un URL associato all'indirizzo di posta elettronica se l'amministratore ha configurato il server in modo.
3. Toccare **Avanti**.
4. Specificare le credenziali quando richiesto.
   - Per **nome utente**, assegnare al nome utente l'autorizzazione per accedere alle risorse.
   - Per **password**, assegnare la password associata al nome utente.
   - È anche possibile che venga richiesto di fornire altri fattori se l'amministratore ha configurato l'autenticazione in questo modo.

Dopo la sottoscrizione, il Centro connessioni visualizzerà le risorse remote.

Una volta effettuata la sottoscrizione a un feed, il contenuto del feed verrà aggiornato automaticamente regolarmente. Le risorse possono essere aggiunte, modificate o rimosse in base alle modifiche apportate dall'amministratore.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come usare il client Android, vedere Introduzione al [client Android](/windows-server/remote/remote-desktop-services/clients/remote-desktop-android/).