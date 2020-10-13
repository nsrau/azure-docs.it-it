---
title: Connettersi a desktop virtuale Windows (classico) da macOS-Azure
description: Come connettersi a desktop virtuale Windows (classico) usando il client macOS.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a0d2bd6a07cfc9e5ebe205322550c9e898022846
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88008441"
---
# <a name="connect-to-windows-virtual-desktop-classic-with-the-macos-client"></a>Connettersi a desktop virtuale Windows (classico) con il client macOS

> Si applica a: macOS 10,12 o versione successiva

>[!IMPORTANT]
>Questo contenuto si applica a Desktop virtuale Windows (versione classica), che non supporta gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se occorre gestire gli oggetti Azure Resource Manager di Desktop virtuale Windows, vedere [questo articolo](../connect-macos.md).

È possibile accedere alle risorse di desktop virtuali Windows dai dispositivi macOS con il client scaricabile. Questa guida illustra come configurare il client di.

## <a name="install-the-client"></a>Installare il client

Per iniziare, [scaricare](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) e installare il client nel dispositivo MacOS.

## <a name="subscribe-to-a-feed"></a>Effettuare la sottoscrizione a un feed

Sottoscrivi il feed fornito dall'amministratore per ottenere l'elenco di risorse gestite disponibili nel tuo dispositivo macOS.

Per sottoscrivere un feed:

1. Selezionare **Aggiungi area di lavoro** nella pagina principale per connettersi al servizio e recuperare le risorse.
2. Immetti un valore in Feed URL (URL del feed). Può essere un URL o un indirizzo e-mail:
   - Nel caso di un URL, usa quello fornito dall'amministratore. In genere l'URL è <https://rdweb.wvd.microsoft.com>.
   - Nel caso dell'e-mail, immetti il tuo indirizzo. In tal caso indichi al client di cercare un URL associato al tuo indirizzo e-mail, se l'amministratore ha configurato il server in questo modo.
3. Selezionare **Aggiungi**.
4. Accedi con l'account utente quando richiesto.

Dopo aver eseguito l'accesso, verrà visualizzato un elenco delle risorse disponibili.

Una volta sottoscritto un feed, il relativo contenuto verrà aggiornato automaticamente a intervalli regolari. È possibile che vengano aggiunte, cambiate o rimosse risorse in base alle modifiche apportate dall'amministratore.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul client macOS, vedere la documentazione [introduttiva per il client MacOS](/windows-server/remote/remote-desktop-services/clients/remote-desktop-mac/) .