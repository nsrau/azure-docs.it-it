---
title: Connettersi a desktop virtuale Windows 10 o 7-Azure
description: Come connettersi al desktop virtuale di Windows tramite il client desktop di Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 07/16/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0e4f41cb23ec4a9463b983049f5f37d09d1b8c71
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89226710"
---
# <a name="connect-with-the-windows-desktop-client"></a>Connettersi con il client desktop di Windows

> Si applica a: Windows 7, Windows 10 e Windows 10 le aziende

>[!IMPORTANT]
>Questo contenuto si applica a Desktop virtuale Windows con gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se si usa Desktop virtuale Windows (versione classica) senza gli oggetti Azure Resource Manager, vedere [questo articolo](./virtual-desktop-fall-2019/connect-windows-7-10-2019.md).

È possibile accedere alle risorse del desktop virtuale di Windows nei dispositivi con Windows 7, Windows 10 e Windows 10, Enterprise usando il client desktop di Windows. Il client non supporta la finestra 8 o Windows 8.1.

>[!NOTE]
>Il client Windows si imposta automaticamente sul desktop virtuale Windows (classico). Tuttavia, se il client rileva che l'utente dispone anche di Azure Resource Manager risorse, aggiunge automaticamente le risorse o notifica all'utente che sono disponibili.

> [!IMPORTANT]
> Desktop virtuale Windows non supporta il client RADC (RemoteApp and Desktop Connections) o il client Connessione Desktop remoto.

> [!IMPORTANT]
> Desktop virtuale Windows non supporta attualmente il client Desktop remoto di Windows Store.

## <a name="install-the-windows-desktop-client"></a>Installare il client desktop di Windows

Scegli il client che corrisponde alla tua versione di Windows:

- [Windows a 64 bit](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows a 32 bit](https://go.microsoft.com/fwlink/?linkid=2098960)
- [Windows ARM64](https://go.microsoft.com/fwlink/?linkid=2098961)

Puoi installare il client per l'utente corrente, nel qual caso non sono richiesti diritti di amministratore, oppure l'amministratore può installare e configurare il client in modo che tutti gli utenti del dispositivo possano accedervi.

Dopo l'installazione, il client può essere avviato dal menu Start cercando **Desktop remoto**.

## <a name="subscribe-to-a-workspace"></a>Sottoscrivere un'area di lavoro

Esistono due modi per sottoscrivere un'area di lavoro. Il client può provare a individuare le risorse disponibili dall'account aziendale o dell'istituto di istruzione oppure puoi specificare direttamente l'URL in cui si trovano le risorse quando il client non riesce a trovarle. Dopo aver sottoscritto un'area di lavoro, puoi avviare le risorse con uno dei metodi seguenti:

- Passa al Centro connessioni e fai doppio clic su una risorsa per avviarla.
- Puoi anche passare al menu Start e cercare una cartella con il nome dell'area di lavoro oppure immettere il nome della risorsa nella barra di ricerca.

### <a name="subscribe-with-a-user-account"></a>Effettuare la sottoscrizione con un account utente

1. Dalla pagina principale del client selezionare **Sottoscrivi**.
2. Accedi con l'account utente quando richiesto.
3. Le risorse verranno visualizzate nel Centro connessioni e raggruppate in base all'area di lavoro.

### <a name="subscribe-with-a-url"></a>Sottoscrivere un URL

1. Dalla pagina principale del client selezionare **Sottoscrivi con URL**.
2. Immetti l'URL dell'area di lavoro o il tuo indirizzo e-mail:
   - Nel caso dell'**URL dell'area di lavoro**, usa quello fornito dall'amministratore. Se accedi alle risorse da Desktop virtuale Windows, puoi usare uno degli URL seguenti:
     - Desktop virtuale Windows (versione classica): `https://rdweb.wvd.microsoft.com/api/feeddiscovery/webfeeddiscovery.aspx`
     - Desktop virtuale Windows: `https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery`
     - Desktop virtuale Windows (US Gov): `https://rdweb.wvd.azure.us/api/arm/feeddiscovery`
   - Se invece si usa il campo **posta elettronica** , immettere l'indirizzo di posta elettronica. Indica al client di cercare un URL associato all'indirizzo di posta elettronica se l'amministratore ha configurato l' [individuazione della posta elettronica](/windows-server/remote/remote-desktop-services/rds-email-discovery).
3. Selezionare **Avanti**.
4. Accedi con l'account utente quando richiesto.
5. Le risorse verranno visualizzate nel Centro connessioni, raggruppate in base all'area di lavoro.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sull'utilizzo del client desktop di Windows, vedere [Introduzione al client desktop di Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/).
