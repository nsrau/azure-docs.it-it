---
title: Supporto Linux desktop virtuale di Windows - Azure
description: Breve panoramica del supporto Linux per Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 967fd1fa182b7c8e581fd74cc287c5a6ba0e4038
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127837"
---
# <a name="linux-support"></a>Supporto di Linux

È possibile utilizzare Linux SDK per Windows Virtual Desktop per creare un client Windows Virtual Desktop autonomo. È inoltre possibile utilizzarlo per abilitare il supporto di Windows Virtual Desktop nell'applicazione client. Questa guida rapida spiegherà cos'è Linux SDK e come iniziare a usarlo.

## <a name="what-is-the-linux-sdk"></a>Che cos'è Linux SDK?

È possibile utilizzare le API SDK per recuperare feed di risorse, connettersi a sessioni di applicazioni desktop o remote e usare molti dei reindirizzamenti supportati dai client di prima parte.

> [!NOTE]
> L'SDK è attualmente in fase di sviluppo. Questo documento verrà aggiornato con le istruzioni per accedere all'SDK quando sarà disponibile.

### <a name="supported-linux-distributions"></a>Distribuzioni Linux supportate

L'SDK è compatibile con la maggior parte dei sistemi operativi basati su Ubuntu 18.04 o versioni successive. Se hai una distribuzione Linux diversa, possiamo lavorare con te per capire come supportare al meglio le tue esigenze.

### <a name="feature-support"></a>Supporto delle funzionalità

L'SDK supporta più connessioni a sessioni di applicazioni desktop e remote. Sono supportati i seguenti reindirizzamenti:

| Reindirizzamento       | Supportato |
| :---------------- | :-------: |
| Tastiera          | &#10004;  |
| Mouse             | &#10004;  |
| Audio in          | &#10004;  |
| Uscita audio         | &#10004;  |
| Appunti (testo)  | &#10004;  |
| Appunti (immagine) | &#10004;  |
| Appunti (file)  | &#10004;  |
| Smart card         | &#10004;  |
| Unità/cartella      | &#10004;  |

L'SDK supporta anche più configurazioni di visualizzazione del monitor, purché i monitor selezionati per la sessione siano contigui.

Questo documento verrà aggiornato man mano che verrà aggiunto il supporto per nuove funzionalità e reindirizzamenti. Se si desidera suggerire nuove funzionalità e altri miglioramenti, visitare la [pagina UserVoice](https://go.microsoft.com/fwlink/?linkid=2116523).

## <a name="get-started-with-the-linux-sdk"></a>Introduzione all'SDK di Linux

Prima di poter sviluppare un client Linux per Windows Virtual Desktop, è necessario eseguire le operazioni seguenti:

1. Creare e distribuire un ambiente Windows Virtual Desktop per il test o l'utilizzo in produzione.
2. Testare i client di primo parte disponibili per acquisire familiarità con l'esperienza utente di Windows Virtual Desktop.

## <a name="next-steps"></a>Passaggi successivi

Consulta la nostra documentazione per i seguenti clienti:

- [Client desktop di Windows](connect-windows-7-and-10.md)
- [Client Web](connect-web.md)
- [Client Android](connect-android.md)
- [Client macOS](connect-macos.md)
- [Client iOS](connect-ios.md)
