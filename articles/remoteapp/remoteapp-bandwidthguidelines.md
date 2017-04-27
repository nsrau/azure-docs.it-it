---
title: 'Larghezza di banda di rete di Azure RemoteApp: linee guida generali | Microsoft Docs'
description: Linee guida di base sulla larghezza di banda di rete per le raccolte e le app Azure RemoteApp.
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 529bf318-ae37-4c14-a11c-43fa703d68a3
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: df3586781ca7ebaaf574a877b96887187add0222
ms.lasthandoff: 03/31/2017


---
# <a name="azure-remoteapp-network-bandwidth---general-guidelines-if-you-cant-test-your-own"></a>Larghezza di banda di rete di Azure RemoteApp: linee guida generali (se non è possibile verificare direttamente)
> [!IMPORTANT]
> Azure RemoteApp verrà sospeso a partire dal 31 agosto 2017. Per i dettagli, vedere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

Se non si dispone del tempo o non si ha la possibilità di eseguire il [test della larghezza di banda della rete](remoteapp-bandwidthtests.md) per Azure RemoteApp, di seguito sono riportate alcune linee guida piuttosto generiche che consentono di stimare la larghezza di banda di rete per utente.

Se il proprio scenario è una combinazione di questi, non è consigliabile un valore minore di (o uguale a) 10 MB/s come larghezza di banda MINIMA per le app moderne connesse a Internet in un ambiente remoto. Questo però, come si è detto, non garantisce un'esperienza utente superiore alla media.

## <a name="complex-powerpoint-simple-powerpoint"></a>Presentazioni complesse, presentazioni semplici
Azure RemoteApp funziona meglio in una LAN a 100 MB. Con un profilo di rete di 10 MB/s, quando l'instabilità oltre 120 ms supera il 5%, l'esperienza utente è di qualità media. Con 1 MB/s la differenza è evidente. Ad esempio, in una presentazione, l'utente potrebbe non vedere affatto le transizioni animate perché vengono ignorati numerosi fotogrammi.

## <a name="internet-explorer-mixed-pdf-pdf-text"></a>Internet Explorer, PDF misto, PDF, testo
Un profilo di rete di 10 MB/s è simile a una LAN in molti aspetti. 1 MB/s offre un'esperienza accettabile, anche se potrebbe verificarsi una certa instabilità quando un utente scorre un documento mentre sono visualizzate immagini sullo schermo.

## <a name="flash-video-youtube"></a>Video Flash (YouTube)
Una LAN da 100 MB/s garantisce un'esperienza ottimale, mentre una larghezza di banda di 10 MB/s è accettabile, nel senso che riesce a stare al passo con la frequenza dei fotogrammi ma l'instabilità aumenta. Con 1 MB/s l'instabilità è molto elevata ed evidente.

## <a name="word-typing-word-remote-input"></a>Digitazione in Word (input remoto in Word)
Si tratta di uno scenario con un utilizzo ridotto della larghezza di banda. Con 256 KB/s l'esperienza ha la stessa qualità di quella offerta da una LAN.

## <a name="learn-more"></a>Altre informazioni
* [Uso previsto della larghezza di banda di rete di Azure RemoteApp](remoteapp-bandwidth.md)
* [Azure RemoteApp - In che modo la larghezza di banda di rete influisce sulla qualità dell'esperienza?](remoteapp-bandwidthexperience.md)
* [Azure RemoteApp: scenari comuni di test relativi all'utilizzo della larghezza di banda di rete](remoteapp-bandwidthtests.md)


