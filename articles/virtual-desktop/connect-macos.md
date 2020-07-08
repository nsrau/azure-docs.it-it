---
title: Connettersi a desktop virtuale Windows da macOS-Azure
description: Come connettersi al desktop virtuale di Windows tramite il client macOS.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f2b4696886c898eec5895c4ccb59347cf2a49f03
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85208995"
---
# <a name="connect-with-the-macos-client"></a>Connettersi con il client macOS

> Si applica a: macOS 10,12 o versione successiva

>[!IMPORTANT]
>Questo contenuto si applica all'aggiornamento di Primavera 2020 con gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se si usa la versione Autunno 2019 di Desktop virtuale Windows senza gli oggetti Azure Resource Manager, vedere [questo articolo](./virtual-desktop-fall-2019/connect-macos-2019.md).
>
> L'aggiornamento di Primavera 2020 di Desktop virtuale Windows è attualmente disponibile in anteprima pubblica. Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

È possibile accedere alle risorse di desktop virtuali Windows dai dispositivi macOS con il client scaricabile. Questa guida illustra come configurare il client di.

## <a name="install-the-client"></a>Installare il client

Per iniziare, [scaricare](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12)   e installare il client nel dispositivo MacOS.

## <a name="subscribe-to-a-feed"></a>Effettuare la sottoscrizione a un feed

Sottoscrivi il feed fornito dall'amministratore per ottenere l'elenco di risorse gestite disponibili nel tuo dispositivo macOS.

Per sottoscrivere un feed:

1. Selezionare **Aggiungi area di lavoro** nella pagina principale per connettersi al servizio e recuperare le risorse.
2. Immettere l'URL del feed. Può essere un URL o un indirizzo e-mail:
   - Nel caso di un URL, usa quello fornito dall'amministratore. In genere l'URL è <https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery>.
   - Nel caso dell'e-mail, immetti il tuo indirizzo. In tal caso indichi al client di cercare un URL associato al tuo indirizzo e-mail, se l'amministratore ha configurato il server in questo modo.
3. Selezionare **Aggiungi**.
4. Accedi con l'account utente quando richiesto.

Dopo aver eseguito l'accesso, verrà visualizzato un elenco delle risorse disponibili.

Una volta sottoscritto un feed, il relativo contenuto verrà aggiornato automaticamente a intervalli regolari. È possibile che vengano aggiunte, cambiate o rimosse risorse in base alle modifiche apportate dall'amministratore.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul client macOS, vedere la documentazione [introduttiva per il client MacOS](/windows-server/remote/remote-desktop-services/clients/remote-desktop-mac/) .
