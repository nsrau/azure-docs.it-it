---
title: Connettersi a Windows Virtual Desktop da iOS - AzureConnect to Windows Virtual Desktop from iOS - Azure
description: Come connettersi a Windows Virtual Desktop utilizzando il client iOS.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3bf9ea6c68e4cbbe721705639e6c6416c0e93c9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128259"
---
# <a name="connect-with-the-ios-client"></a>Connettersi con il client iOS

> Si applica a: iOS 13.0 o versioni successive. Compatibile con iPhone, iPad e iPod touch.

Puoi accedere alle risorse di Windows Virtual Desktop dal tuo dispositivo iOS con il nostro client scaricabile. Questa guida ti dirà come configurare il client iOS.

## <a name="install-the-ios-client"></a>Installare il client iOS

Per iniziare, [scarica](https://aka.ms/rdios) e installa il client sul tuo dispositivo iOS.

## <a name="subscribe-to-a-feed"></a>Effettuare la sottoscrizione a un feed

Sottoscrivi il feed fornito dall'amministratore per ottenere l'elenco delle risorse gestite a cui puoi accedere sul tuo dispositivo iOS.

Per abbonarsi a un feed:

1. Nel Centro connessioni **+** toccare e quindi **Aggiungi area di lavoro**.
2. Inserisci l'URL del feed nel campo **URL feed.** L'URL del feed può essere un URL o un indirizzo email.
   - Se utilizzi un URL, usa quello che ti ha dato l'amministratore. In genere, <https://rdweb.wvd.microsoft.com>l'URL è .
   - Per utilizzare l'indirizzo e-mail, immettere l'indirizzo di posta elettronica. Ciò indica al client di cercare un URL associato all'indirizzo di posta elettronica se l'amministratore ha configurato il server in questo modo.
3. Toccare **Avanti**.
4. Specificare le credenziali quando richiesto.
   - Per **Nome utente**, assegnare al nome utente l'autorizzazione per accedere alle risorse.
   - Per **Password**, fornire la password associata al nome utente.
   - Potrebbe anche essere richiesto di fornire ulteriori fattori se l'amministratore ha configurato l'autenticazione in questo modo.
5. Toccare **Salva**.

Dopo di che, il Centro connessioni dovrebbe visualizzare le risorse remote.

Una volta sottoscritto un feed, il contenuto del feed verrà aggiornato automaticamente su base regolare. Le risorse possono essere aggiunte, modificate o rimosse in base alle modifiche apportate dall'amministratore.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come usare il client iOS, vedere la documentazione Introduzione al client iOS.To learn more about how to use the iOS client, check out [the Get started with the iOS client](/windows-server/remote/remote-desktop-services/clients/remote-desktop-ios/) documentation.
