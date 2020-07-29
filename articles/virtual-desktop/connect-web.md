---
title: Connettersi al desktop virtuale Windows con il client Web-Azure
description: Come connettersi a Desktop virtuale Windows usando il client Web.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 09/24/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3fdbd0ddb33785655cada2600acdecdced4aeb2f
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87283436"
---
# <a name="connect-to-windows-virtual-desktop-with-the-web-client"></a>Connettersi al desktop virtuale Windows con il client Web

>[!IMPORTANT]
>Questo contenuto si applica al desktop virtuale di Windows con Azure Resource Manager oggetti desktop virtuali di Windows. Se si usa desktop virtuale di Windows (classico) senza Azure Resource Manager oggetti, vedere [questo articolo](./virtual-desktop-fall-2019/connect-web-2019.md).

Il client Web consente di accedere alle risorse del Desktop virtuale Windows da un Web browser senza il lungo processo di installazione.

>[!NOTE]
>Il client Web attualmente non dispone del supporto per il sistema operativo dei dispositivi mobili.

## <a name="supported-operating-systems-and-browsers"></a>Sistemi operativi e browser supportati

Sebbene dovrebbe funzionare qualsiasi browser compatibile con HTML5, ufficialmente sono supportati i sistemi operativi e i browser seguenti.

| Browser           | Sistema operativo supportato                     | Note               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          | Versione 11 o successive |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | Versione 55 o successiva |
| Google Chrome     | Windows, macOS, Linux, Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>Accedere al feed di risorse remote

In un browser, passare alla versione integrata in Azure Resource Manager del client Web Desktop virtuale Windows all'indirizzo <https://rdweb.wvd.microsoft.com/arm/webclient> e accedere con l'account utente.

>[!NOTE]
>Se si usa desktop virtuale di Windows (versione classica) senza Azure Resource Manager integrazione, connettersi alle risorse in <https://rdweb.wvd.microsoft.com/webclient> .

>[!NOTE]
>Se è già stato eseguito l'accesso con un account Azure Active Directory diverso da quello che si vuole usare per Desktop virtuale Windows, è necessario disconnettersi o usare una finestra del browser privata.

Dopo l'accesso verrà visualizzato un elenco di risorse. Per avviare le risorse, è possibile selezionarle come si fa in una normale app nella scheda **Tutte le risorse**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come usare il client Web, vedere [Introduzione al client Web](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client).
