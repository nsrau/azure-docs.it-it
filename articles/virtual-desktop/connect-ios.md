---
title: Connettersi a desktop virtuale Windows da iOS-Azure
description: Come connettersi al desktop virtuale di Windows tramite il client iOS.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: helohr
ms.openlocfilehash: bfc7efa6e8ead3b53704e3c9bd189b18cb787618
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73605843"
---
# <a name="connect-with-the-ios-client"></a>Connettersi con il client iOS

> Si applica a: iOS 8,0 o versione successiva. Compatibile con iPhone, iPad e iPod touch.

>[!NOTE]
> Il client iOS è attualmente ancora in anteprima.

È possibile accedere alle risorse di desktop virtuali Windows dal dispositivo iOS con il client scaricabile. Questa guida illustra come configurare il client iOS.

## <a name="install-the-ios-beta-client"></a>Installare il client iOS Beta
Per installare il client iOS Beta:

1. Installare l'app [Apple TestFlight](https://apps.apple.com/us/app/testflight/id899247664) nel dispositivo iOS.
2. Sul dispositivo iOS aprire un browser e passare a [aka.ms/rdiosbeta](https://aka.ms/rdiosbeta).
3. Nell'etichetta **passaggio 2: partecipare alla beta**selezionare **Avvia test**.
4. Quando si viene reindirizzati all'app TestFlight, selezionare **accetta**, quindi selezionare **Installa**.

## <a name="subscribe-to-a-feed"></a>Sottoscrivere un feed

Sottoscrivere il feed fornito dall'amministratore per ottenere l'elenco delle risorse gestite a cui è possibile accedere nel dispositivo iOS.

Per sottoscrivere un feed:

1. Nel Centro connessioni toccare **+** e quindi toccare **Aggiungi area di lavoro**.
2. Immettere l'URL del feed nel campo **feed URL** . L'URL del feed può essere un URL o un indirizzo di posta elettronica.
   - Se si usa un URL, usare quello che l'amministratore ha assegnato. L'URL viene in genere <https://rdweb.wvd.microsoft.com>.
   - Per usare la posta elettronica, immettere l'indirizzo di posta elettronica. Indica al client di cercare un URL associato all'indirizzo di posta elettronica se l'amministratore ha configurato il server in modo.
3. Toccare **Avanti**.
4. Specificare le credenziali quando richiesto.
   - Per **nome utente**, assegnare al nome utente l'autorizzazione per accedere alle risorse.
   - Per **password**, assegnare la password associata al nome utente.
   - È anche possibile che venga richiesto di fornire altri fattori se l'amministratore ha configurato l'autenticazione in questo modo.
5. Toccare **Salva**.

Al termine di questa operazione, il Centro connessioni visualizzerà le risorse remote.

Una volta effettuata la sottoscrizione a un feed, il contenuto del feed verrà aggiornato automaticamente regolarmente. Le risorse possono essere aggiunte, modificate o rimosse in base alle modifiche apportate dall'amministratore.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come usare il client iOS Beta, vedere la documentazione [introduttiva per il client iOS](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-ios) .
