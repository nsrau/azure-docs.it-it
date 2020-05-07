---
title: Connetti client Web desktop virtuali Windows-Azure
description: Come connettersi al desktop virtuale di Windows tramite il client Web.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 70fd29944d31682de5a6a34f76fdf27e3ff7d3ff
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82607844"
---
# <a name="connect-with-the-web-client"></a>Connettersi con il client Web

>[!IMPORTANT]
>Questo contenuto si applica all'aggiornamento di Spring 2020 con Azure Resource Manager oggetti desktop virtuali di Windows. Se si usa la versione 2019 del desktop virtuale di Windows senza Azure Resource Manager oggetti, vedere [questo articolo](./virtual-desktop-fall-2019/connect-web-2019.md).
>
> L'aggiornamento di Spring 2020 per desktop virtuale di Windows è attualmente disponibile in anteprima pubblica. Questa versione di anteprima viene fornita senza un contratto di servizio e non è consigliabile usarla per carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. 
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Il client Web consente di accedere alle risorse del desktop virtuale di Windows da un Web browser senza il lungo processo di installazione.

>[!NOTE]
>Il client Web attualmente non dispone del supporto del sistema operativo per dispositivi mobili.

## <a name="supported-operating-systems-and-browsers"></a>Sistemi operativi e browser supportati

Anche se qualsiasi browser compatibile con HTML5 dovrebbe funzionare, sono supportati ufficialmente i sistemi operativi e i browser seguenti.

| Browser           | Sistema operativo supportato                     | Note               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          |                     |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | Versione 55 o successiva |
| Google Chrome     | Windows, macOS, Linux, sistema operativo Chrome |                     |

## <a name="access-remote-resources-feed"></a>Accedere al feed di risorse remote

In un browser passare al [client Web desktop virtuale di Windows](https://rdweb.wvd.microsoft.com/arm/webclient) e accedere con l'account utente.

>[!NOTE]
>Se è già stato effettuato l'accesso con un account di Azure Active Directory diverso da quello che si vuole usare per desktop virtuale Windows, è necessario disconnettersi o usare una finestra del browser privata.

Dopo l'accesso, verrà visualizzato un elenco di risorse. È possibile avviare le risorse selezionandola come una normale app nella scheda **tutte le risorse** .

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come usare il client Web, vedere Introduzione al [client Web](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client).
