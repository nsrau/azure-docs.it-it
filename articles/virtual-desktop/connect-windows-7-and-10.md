---
title: Connettersi all'anteprima del Desktop virtuale Windows da Windows 10 o Windows 7 - Azure
description: Come per la connessione all'anteprima di Desktop virtuale Windows da Windows 10 o Windows 7.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/24/2019
ms.author: helohr
ms.openlocfilehash: b7d7b25d0355f2379b90313f17e2b595234df827
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65145995"
---
# <a name="connect-from-windows-10-or-windows-7"></a>Connettersi da Windows 10 o Windows 7

> Si applica a Windows 7 e Windows 10.

È disponibile un client scaricabile che consente di accedere alle risorse di anteprima di Desktop virtuale Windows da dispositivi che eseguono Windows 7 e Windows 10.

> [!IMPORTANT]
> Non usare **RemoteApp e Desktop. le connessioni (RADC)** oppure **connessione Desktop remoto (MSTSC)** per accedere alle risorse di Desktop virtuale Windows, poiché Windows Desktop virtuale non supporta i due client.

## <a name="install-the-client"></a>Installazione del client

[Scaricare](https://go.microsoft.com/fwlink/?linkid=2068602) e installare il client al computer locale. L'installazione richiede diritti di amministratore.

## <a name="subscribe-to-a-feed"></a>Sottoscrivere un feed

Ottiene l'elenco di risorse gestite disponibili all'utente tramite la sottoscrizione al feed specificato dall'amministratore. La sottoscrizione rende le risorse disponibili nel PC locale.

Per sottoscrivere un feed:

1. Avviare il client dall'elenco di tutte le app, cercare per **Desktop remoto**.
1. Selezionare **"Subscribe"** nella pagina principale per connettersi al servizio e recuperare le risorse.
1. **Accedi** con l'account utente quando richiesto.

Dopo l'autenticazione, viene visualizzato un elenco di risorse disponibili.

È possibile avviare le risorse da uno dei due metodi.

- Dalla pagina principale del client, fare doppio clic su una risorsa di per avviarla.
- Avviare una risorsa, come si farebbe normalmente le altre App dal Menu Start.
  - È anche possibile cercare le app nella barra di ricerca.

Dopo aver sottoscritto un feed, il contenuto del feed viene aggiornato automaticamente a intervalli regolari. Risorse possono essere aggiunto, modificate o rimosso in base alle modifiche apportate dall'amministratore.

## <a name="view-the-details-of-a-feed"></a>Visualizzare i dettagli di un feed

Dopo la sottoscrizione, è possibile visualizzare informazioni aggiuntive relative al feed accedendo il riquadro dei dettagli.

1. Pagina principale del client, selezionare i puntini di sospensione ( **...** ) a destra del nome del feed.
1. Nel menu a discesa, selezionare **dettagli**.
1. Il riquadro dei dettagli viene visualizzato a destra del client.

Il riquadro dei dettagli contiene informazioni utili relative al feed:

- L'URL e il nome utente utilizzato per la sottoscrizione
- Il numero di applicazioni e desktop
- La data/ora dell'ultimo aggiornamento
- Lo stato dell'ultimo aggiornamento

Se necessario, è possibile avviare un aggiornamento manuale facendo clic sulla **aggiornale**.

## <a name="unsubscribe-from-a-feed"></a>Annullare la sottoscrizione da un feed

In questa sezione viene illustrato come annullare la sottoscrizione da un feed. È possibile annullare la sottoscrizione per riattivare la sottoscrizione con un account diverso o rimuovere le risorse dal sistema.

1. Pagina principale del client, selezionare i puntini di sospensione ( **...** ) a destra del nome del feed.
1. Nel menu a discesa, selezionare **Unsubscribe**.
1. Esaminare e selezionare **continuazione** dalla finestra di dialogo.

## <a name="update-the-client"></a>Aggiornare il client

Quando è disponibile una nuova versione del client, riceve una notifica dal client e il centro operativo Windows. Selezionare la notifica per avviare il processo di aggiornamento.
