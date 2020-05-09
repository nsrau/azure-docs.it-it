---
title: Connettersi a desktop virtuale Windows da macOS-Azure
description: Come connettersi al desktop virtuale di Windows tramite il client macOS.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ca54c60d424722d31d195d10eb15751a2ec6c0eb
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612912"
---
# <a name="connect-with-the-macos-client"></a>Connettersi con il client macOS

> Si applica a: macOS 10,12 o versione successiva

>[!IMPORTANT]
>Questo contenuto si applica all'aggiornamento di Spring 2020 con Azure Resource Manager oggetti desktop virtuali di Windows. Se si usa la versione 2019 del desktop virtuale di Windows senza Azure Resource Manager oggetti, vedere [questo articolo](./virtual-desktop-fall-2019/connect-macos-2019.md).
>
> L'aggiornamento di Spring 2020 per desktop virtuale di Windows è attualmente disponibile in anteprima pubblica. Questa versione di anteprima viene fornita senza un contratto di servizio e non è consigliabile usarla per carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. 
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

È possibile accedere alle risorse di desktop virtuali Windows dai dispositivi macOS con il client scaricabile. Questa guida illustra come configurare il client di.

## <a name="install-the-client"></a>Installare il client

Per iniziare, [scaricare](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) e installare il client nel dispositivo MacOS.

## <a name="subscribe-to-a-feed"></a>Effettuare la sottoscrizione a un feed

Sottoscrivere il feed che l'amministratore ha fornito per ottenere l'elenco delle risorse gestite disponibili nel dispositivo macOS.

Per sottoscrivere un feed:

1. Selezionare **Aggiungi area di lavoro** nella pagina principale per connettersi al servizio e recuperare le risorse.
2. Immetti un valore in Feed URL (URL del feed). Può essere un URL o un indirizzo e-mail:
   - Se si usa un URL, usare quello che l'amministratore ha assegnato. In genere, l'URL <https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery>è.
   - Per usare la posta elettronica, immettere l'indirizzo di posta elettronica. Indica al client di cercare un URL associato all'indirizzo di posta elettronica se l'amministratore ha configurato il server in modo.
3. Selezionare **Aggiungi**.
4. Accedi con l'account utente quando richiesto.

Dopo aver eseguito l'accesso, verrà visualizzato un elenco di risorse disponibili.

Una volta effettuata la sottoscrizione a un feed, il contenuto del feed verrà aggiornato automaticamente regolarmente. Le risorse possono essere aggiunte, modificate o rimosse in base alle modifiche apportate dall'amministratore.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul client macOS, vedere la documentazione [introduttiva per il client MacOS](/windows-server/remote/remote-desktop-services/clients/remote-desktop-mac/) .
