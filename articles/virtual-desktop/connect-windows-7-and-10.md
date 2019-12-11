---
title: Connettersi a desktop virtuale Windows 10 o 7-Azure
description: Come connettersi al desktop virtuale di Windows tramite il client desktop di Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: helohr
ms.openlocfilehash: 745b33efe46c82e3b9358c9c5a2ed13292242db1
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74997344"
---
# <a name="connect-with-the-windows-desktop-client"></a>Connettersi con il client desktop di Windows

> Si applica a: Windows 7, Windows 10 e Windows 10 le aziende

È possibile accedere alle risorse del desktop virtuale di Windows nei dispositivi con Windows 7, Windows 10 e Windows 10, Enterprise usando il client desktop di Windows.

> [!IMPORTANT]
> Desktop virtuale Windows non supporta il client di connessione RemoteApp e desktop (RADC) o il client Connessione Desktop remoto (MSTSC).

## <a name="install-the-windows-desktop-client"></a>Installare il client desktop di Windows

Scegli il client che corrisponde alla tua versione di Windows:

- [Windows a 64 bit](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows a 32 bit](https://go.microsoft.com/fwlink/?linkid=2098960)
- [Windows ARM64](https://go.microsoft.com/fwlink/?linkid=2098961)

Puoi installare il client per l'utente corrente, nel qual caso non sono richiesti diritti di amministratore, oppure l'amministratore può installare e configurare il client in modo che tutti gli utenti del dispositivo possano accedervi.

Dopo l'installazione, il client può essere avviato dal menu Start cercando **Desktop remoto**.

## <a name="subscribe-to-a-feed"></a>Effettuare la sottoscrizione a un feed

Ottenere l'elenco di risorse gestite disponibili sottoscrivendo il feed fornito dall'amministratore. La sottoscrizione rende disponibili le risorse nel computer locale.

Per sottoscrivere un feed:

1. Aprire il client desktop di Windows.
2. Selezionare **sottoscrizione** nella pagina principale per connettersi al servizio e recuperare le risorse.
3. Accedi con l'account utente quando richiesto.

Una volta eseguito correttamente l'accesso, verrà visualizzato un elenco delle risorse a cui è possibile accedere.

È possibile avviare le risorse in base a uno dei due metodi.

- Dalla pagina principale del client fare doppio clic su una risorsa per avviarla.
- Avviare una risorsa come si farebbe normalmente con altre app dal menu Start.
  - È anche possibile cercare le app nella barra di ricerca.

Una volta effettuata la sottoscrizione a un feed, il contenuto del feed viene aggiornato automaticamente regolarmente. Le risorse possono essere aggiunte, modificate o rimosse in base alle modifiche apportate dall'amministratore.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sull'utilizzo del client desktop di Windows, vedere [Introduzione al client desktop di Windows](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/windowsdesktop).
