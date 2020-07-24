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
ms.openlocfilehash: 4bb54db8a18baa237f1c32c223b5f609638c7523
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87080625"
---
# <a name="connect-to-windows-virtual-desktop-with-the-web-client"></a>Connettersi al desktop virtuale Windows con il client Web

>[!IMPORTANT]
>Questo contenuto si applica all'aggiornamento di Primavera 2020 con gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se si usa la versione Autunno 2019 di Desktop virtuale Windows senza gli oggetti Azure Resource Manager, vedere [questo articolo](./virtual-desktop-fall-2019/connect-web-2019.md).
>
> L'aggiornamento di Primavera 2020 di Desktop virtuale Windows è attualmente disponibile in anteprima pubblica. Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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

In un browser, passare alla versione integrata in Azure Resource Manager del client Web Desktop virtuale Windows all'indirizzo <https://rdweb.wvd.microsoft.com/arm/webclient> e accedere con l'account utente.

>[!NOTE]
>Se si usa la versione Fall 2019 di Desktop virtuale Windows senza l'integrazione Azure Resource Manager, connettersi alle risorse all'indirizzo <https://rdweb.wvd.microsoft.com/webclient>.

>[!NOTE]
>Se è già stato eseguito l'accesso con un account Azure Active Directory diverso da quello che si vuole usare per Desktop virtuale Windows, è necessario disconnettersi o usare una finestra del browser privata.

Dopo l'accesso verrà visualizzato un elenco di risorse. Per avviare le risorse, è possibile selezionarle come si fa in una normale app nella scheda **Tutte le risorse**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come usare il client Web, vedere [Introduzione al client Web](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client).
