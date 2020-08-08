---
title: Connettere client Web desktop virtuali Windows (classico)-Azure
description: Come connettersi a un desktop virtuale Windows (classico) utilizzando il client Web.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c8a55e35ba89caefc362024fc871ac5eb800447b
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2020
ms.locfileid: "88008424"
---
# <a name="connect-to-windows-virtual-desktop-classic-with-the-web-client"></a>Connettersi al desktop virtuale Windows (classico) con il client Web

>[!IMPORTANT]
>Questo contenuto si applica a Desktop virtuale Windows (versione classica), che non supporta gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se occorre gestire gli oggetti Azure Resource Manager di Desktop virtuale Windows, vedere [questo articolo](../connect-web.md).

Il client Web consente di accedere alle risorse del Desktop virtuale Windows da un Web browser senza il lungo processo di installazione.

>[!NOTE]
>Il client Web attualmente non dispone del supporto per il sistema operativo dei dispositivi mobili.

## <a name="supported-operating-systems-and-browsers"></a>Sistemi operativi e browser supportati

Sebbene dovrebbe funzionare qualsiasi browser compatibile con HTML5, ufficialmente sono supportati i sistemi operativi e i browser seguenti.

| Browser           | Sistema operativo supportato                     | Note               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          |                     |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | Versione 55 o successiva |
| Google Chrome     | Windows, macOS, Linux, Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>Accedere al feed di risorse remote

In un browser passare al client Web Desktop virtuale Windows all'indirizzo <https://rdweb.wvd.microsoft.com/webclient> e accedere con l'account utente.

>[!NOTE]
>Se si usa desktop virtuale di Windows con l'integrazione di Azure Resource Manager, connettersi invece alle risorse <https://rdweb.wvd.microsoft.com/arm/webclient> .

>[!NOTE]
>Se è già stato eseguito l'accesso con un account Azure Active Directory diverso da quello che si vuole usare per Desktop virtuale Windows, è necessario disconnettersi o usare una finestra del browser privata.

Dopo l'accesso verrà visualizzato un elenco di risorse. Per avviare le risorse, è possibile selezionarle come si fa in una normale app nella scheda **Tutte le risorse**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come usare il client Web, vedere [Introduzione al client Web](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client).
