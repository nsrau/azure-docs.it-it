---
title: Connettersi all'anteprima del Desktop virtuale Windows con Windows 7 e Windows 10 - Azure
description: Come connettersi al servizio di anteprima di Desktop virtuale Windows con Windows 7 o Windows 10.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/27/2019
ms.author: helohr
ms.openlocfilehash: 8b5a649d767dae9ae9c0fb077f550451a7f83601
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58630374"
---
# <a name="connect-with-windows-7-and-windows-10"></a>Connettersi con Windows 7 e Windows 10

> Si applica a Windows 7 e Windows 10.

È disponibile un client scaricabile che consente di accedere alle risorse di anteprima di Desktop virtuale Windows da dispositivi che eseguono Windows 7 e Windows 10.

> [!IMPORTANT]
> Non usare **RemoteApp e Desktop. le connessioni (RADC)** oppure **connessione Desktop remoto (MSTSC)** per accedere alle risorse di Desktop virtuale Windows, poiché Windows Desktop virtuale non supporta i due client.

## <a name="install-the-client"></a>Installazione del client

[Scaricare](https://go.microsoft.com/fwlink/?linkid=2068602) e installare il client al computer locale. Ciò richiede diritti di amministratore.

## <a name="subscribe-to-a-feed"></a>Sottoscrivere un feed

Ottiene l'elenco delle risorse disponibili per l'utente dal proprio computer locale tramite la sottoscrizione al feed specificato dall'amministratore.

Per sottoscrivere un feed:

1. Avviare il client dall'elenco di tutte le app, cercare per **Desktop remoto**.
1. Selezionare **"Subscribe"** nella pagina principale per connettersi al servizio e recuperare le risorse.
1. **Accedi** con l'account utente quando richiesto.

Dopo l'autenticazione, viene visualizzato un elenco di risorse disponibili.

È possibile avviare le risorse da uno dei due metodi.

- Dalla pagina principale del client, fare doppio clic su una risorsa di per avviarla.
- Avviare una risorsa, come si farebbe normalmente le altre App dal Menu Start.
  - È anche possibile cercare le app nella barra di ricerca.

## <a name="update-the-client"></a>Aggiornare il client

Quando è disponibile una nuova versione del client, riceve una notifica dal client e il centro operativo Windows. Selezionare la notifica per avviare il processo di aggiornamento.
