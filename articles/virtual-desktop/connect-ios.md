---
title: Connettersi a desktop virtuale Windows da iOS-Azure
description: Come connettersi al desktop virtuale di Windows tramite il client iOS.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4239f5271e4df282cf667b4bf1eff5f55f6f6323
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611705"
---
# <a name="connect-with-the-ios-client"></a>Connettersi con il client iOS

> Si applica a: iOS 13,0 o versione successiva. Compatibile con iPhone, iPad e iPod touch.

>[!IMPORTANT]
>Questo contenuto si applica all'aggiornamento di Spring 2020 con Azure Resource Manager oggetti desktop virtuali di Windows. Se si usa la versione 2019 del desktop virtuale di Windows senza Azure Resource Manager oggetti, vedere [questo articolo](./virtual-desktop-fall-2019/connect-ios-2019.md).
>
> L'aggiornamento di Spring 2020 per desktop virtuale di Windows è attualmente disponibile in anteprima pubblica. Questa versione di anteprima viene fornita senza un contratto di servizio e non è consigliabile usarla per carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. 
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

È possibile accedere alle risorse di desktop virtuali Windows dal dispositivo iOS con il client scaricabile. Questa guida illustra come configurare il client iOS.

## <a name="install-the-ios-client"></a>Installare il client iOS

Per iniziare, [scaricare](https://aka.ms/rdios) e installare il client nel dispositivo iOS.

## <a name="subscribe-to-a-feed"></a>Effettuare la sottoscrizione a un feed

Sottoscrivere il feed fornito dall'amministratore per ottenere l'elenco delle risorse gestite a cui è possibile accedere nel dispositivo iOS.

Per sottoscrivere un feed:

1. Nel Centro connessioni toccare **+**, quindi toccare **Aggiungi area di lavoro**.
2. Immettere l'URL del feed nel campo **feed URL** . L'URL del feed può essere un URL o un indirizzo di posta elettronica.
   - Se si usa un URL, usare quello che l'amministratore ha assegnato. In genere, l'URL <https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery>è.
   - Per usare la posta elettronica, immettere l'indirizzo di posta elettronica. Indica al client di cercare un URL associato all'indirizzo di posta elettronica se l'amministratore ha configurato il server in modo.
3. Tocca **Next** (Avanti).
4. Specificare le credenziali quando richiesto.
   - Per **nome utente**, assegnare al nome utente l'autorizzazione per accedere alle risorse.
   - Per **password**, assegnare la password associata al nome utente.
   - È anche possibile che venga richiesto di fornire altri fattori se l'amministratore ha configurato l'autenticazione in questo modo.
5. Toccare **salvare**.

Al termine di questa operazione, il Centro connessioni visualizzerà le risorse remote.

Una volta effettuata la sottoscrizione a un feed, il contenuto del feed verrà aggiornato automaticamente regolarmente. Le risorse possono essere aggiunte, modificate o rimosse in base alle modifiche apportate dall'amministratore.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come usare il client iOS, vedere la documentazione [introduttiva per il client iOS](/windows-server/remote/remote-desktop-services/clients/remote-desktop-ios/) .
