---
title: Connettersi al desktop virtuale Windows con il client Web-Azure
description: Come connettersi a Desktop virtuale Windows usando il client Web.
author: Heidilohr
ms.topic: how-to
ms.date: 09/24/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: c8a4b22966c3d4db268e212bb3f2d1bbb78fee74
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89400637"
---
# <a name="connect-to-windows-virtual-desktop-with-the-web-client"></a>Connettersi al desktop virtuale Windows con il client Web

>[!IMPORTANT]
>Questo contenuto si applica a Desktop virtuale Windows con gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se si usa Desktop virtuale Windows (versione classica) senza gli oggetti Azure Resource Manager, vedere [questo articolo](./virtual-desktop-fall-2019/connect-web-2019.md).

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
>
> Se si usa il portale di US Gov, usare <https://rdweb.wvd.azure.us/arm/webclient/index.html> .

>[!NOTE]
>Se è già stato eseguito l'accesso con un account Azure Active Directory diverso da quello che si vuole usare per Desktop virtuale Windows, è necessario disconnettersi o usare una finestra del browser privata.

Dopo l'accesso verrà visualizzato un elenco di risorse. Per avviare le risorse, è possibile selezionarle come si fa in una normale app nella scheda **Tutte le risorse**.

## <a name="using-an-input-method-editor"></a>Uso di un editor del metodo di input

Il client Web supporta l'utilizzo di un input Method Editor (IME) nella sessione remota nella versione **1.0.21.16 o successiva**. Il Language Pack per la tastiera che si vuole usare nella sessione remota deve essere installato nella macchina virtuale host. Per altre informazioni sulla configurazione dei Language Pack nella sessione remota, vedere Aggiungere i [Language Pack a un'immagine multisessione di Windows 10](language-packs.md).

Per abilitare l'input IME utilizzando il client Web:

1. Prima di connettersi alla sessione remota, passare al pannello **Impostazioni** client Web.

2. Impostare l'opzione **Abilita input Method Editor** **su on**.

3. Nel menu a discesa selezionare la tastiera da usare nella sessione remota.

4. Connettersi alla sessione remota.

Il client Web eliminerà la finestra IME locale quando si è concentrati sulla sessione remota. La modifica delle impostazioni di IME quando si è già connessi alla sessione remota non avrà alcun effetto.

>[!NOTE]
>Se il Language Pack non è installato nella macchina virtuale host, per impostazione predefinita la sessione remota utilizzerà la tastiera inglese (Stati Uniti).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come usare il client Web, vedere [Introduzione al client Web](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client).
