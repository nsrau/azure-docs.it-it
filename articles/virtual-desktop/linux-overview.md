---
title: Supporto Linux per desktop virtuale Windows-Azure
description: Breve panoramica del supporto Linux per desktop virtuale di Windows.
author: Heidilohr
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: bd3cc6c5220e2e84cbbd30b29b8034f53c813f1e
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2020
ms.locfileid: "88008696"
---
# <a name="linux-support"></a>Supporto di Linux

È possibile accedere alle risorse di desktop virtuali Windows dai dispositivi Linux con i client supportati seguenti, forniti dai partner thin client Linux. Microsoft collabora con diversi partner per abilitare i client desktop virtuali Windows supportati in più sistemi operativi e dispositivi basati su Linux. Se si desidera il supporto per desktop virtuali Windows in una piattaforma Linux non elencata qui, inviare una notifica nella [pagina UserVoice](https://remotedesktop.uservoice.com/forums/923035-remote-desktop-support-on-linux).

## <a name="connect-with-your-linux-device"></a>Connettersi con il dispositivo Linux

I seguenti partner hanno approvato i client desktop virtuali Windows per i dispositivi Linux.

|Partner|Documentazione del partner|Supporto per i partner|
|:------|:--------------------|:--------------|
|![Logo di IGEL](./media/partners/igel.png)|[Documentazione del client IGEL](https://www.igel.com/igel-solution-family/windows-virtual-desktop/)|[Supporto di IGEL](https://www.igel.com/support/)|

## <a name="what-is-the-linux-sdk"></a>Che cos'è Linux SDK?

I partner thin client Linux possono usare le API di Linux SDK per desktop virtuali di Windows per recuperare i feed di risorse, connettersi a sessioni di applicazioni desktop o remote e usare molti dei reindirizzamenti supportati dai client della prima parte. L'SDK è compatibile con la maggior parte dei sistemi operativi basati su Ubuntu 18,04 o versioni successive.

### <a name="feature-support"></a>Supporto funzionalità

L'SDK supporta più connessioni a sessioni di applicazioni desktop e remote. Sono supportati i seguenti reindirizzamenti:

| Reindirizzamento       | Supportato |
| :---------------- | :-------: |
| Tastiera          | &#10004;  |
| Mouse             | &#10004;  |
| Audio in          | &#10004;  |
| Audio in uscita         | &#10004;  |
| Appunti (testo)  | &#10004;  |
| Appunti (immagine) | &#10004;  |
| Appunti (file)  | &#10004;  |
| Smart card         | &#10004;  |
| Unità/cartella      | &#10004;  |

L'SDK supporta inoltre più configurazioni di visualizzazione monitor, purché i monitoraggi selezionati per la sessione siano contigui.

Questo documento verrà aggiornato quando si aggiunge il supporto per le nuove funzionalità e i reindirizzamenti. Se si desidera suggerire nuove funzionalità e altri miglioramenti, visitare la [pagina UserVoice](https://go.microsoft.com/fwlink/?linkid=2116523).

## <a name="next-steps"></a>Passaggi successivi

Consultare la documentazione per i client seguenti:

- [Client desktop di Windows](connect-windows-7-10.md)
- [Client Web](connect-web.md)
- [Client Android](connect-android.md)
- [Client macOS](connect-macos.md)
- [Client iOS](connect-ios.md)
