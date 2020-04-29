---
title: Supporto Linux per desktop virtuale Windows-Azure
description: Breve panoramica del supporto Linux per desktop virtuale di Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8c5de43ed29856451ad67e02a426b07cc34a0d54
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80422424"
---
# <a name="linux-support"></a>Supporto di Linux

I partner possono usare Linux SDK per desktop virtuale Windows per compilare un client desktop virtuale Windows autonomo. È anche possibile usarlo per abilitare il supporto per desktop virtuali Windows nell'applicazione client. Questa guida rapida spiega cosa è Linux SDK e come iniziare a usarlo.

## <a name="connect-with-your-linux-device"></a>Connettersi con il dispositivo Linux

I seguenti partner hanno approvato i client desktop virtuali Windows per i dispositivi Linux.

|Partner|Documentazione del partner|Supporto per i partner|
|:------|:--------------------|:--------------|
|![Logo di IGEL](./media/partners/igel.png)|[Documentazione del client IGEL](https://www.igel.com/igel-solution-family/windows-virtual-desktop/)|[Supporto di IGEL](https://www.igel.com/support/)|

## <a name="what-is-the-linux-sdk"></a>Che cos'è Linux SDK?

È possibile utilizzare le API SDK per recuperare i feed di risorse, connettersi a sessioni di applicazioni desktop o remote e utilizzare molti dei reindirizzamenti supportati dai client di prima entità.

> [!NOTE]
> L'SDK è attualmente in fase di sviluppo. Questo documento verrà aggiornato con le istruzioni per accedere all'SDK quando è disponibile a livello generale.

### <a name="supported-linux-distributions"></a>Distribuzioni Linux supportate

L'SDK è compatibile con la maggior parte dei sistemi operativi basati su Ubuntu 18,04 o versioni successive. Se si dispone di una distribuzione Linux diversa, è possibile collaborare con l'utente per scoprire come supportare al meglio le proprie esigenze.

### <a name="feature-support"></a>Supporto delle funzionalità

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

## <a name="get-started-with-the-linux-sdk"></a>Introduzione a Linux SDK

Prima di poter sviluppare un client Linux per desktop virtuale Windows, è necessario eseguire le operazioni seguenti:

1. Creare e distribuire un ambiente desktop virtuale Windows per l'uso di test o di produzione.
2. Testare i client di terze parti disponibili per acquisire familiarità con l'esperienza utente di desktop virtuali Windows.

## <a name="next-steps"></a>Passaggi successivi

Consultare la documentazione per i client seguenti:

- [Client desktop di Windows](connect-windows-7-and-10.md)
- [Client Web](connect-web.md)
- [Client Android](connect-android.md)
- [Client macOS](connect-macos.md)
- [Client iOS](connect-ios.md)
