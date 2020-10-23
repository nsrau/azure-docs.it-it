---
title: Configurare il reindirizzamento dei dispositivi-Azure
description: Come configurare il reindirizzamento dei dispositivi per desktop virtuale di Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 09/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 00a3c1d0a2a905e6435b811d5f2611c16a5de502
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92328880"
---
# <a name="configure-device-redirections"></a>Configurare i reindirizzamenti dei dispositivi

La configurazione del reindirizzamento dei dispositivi per l'ambiente desktop virtuale Windows consente di usare stampanti, dispositivi USB, microfoni e altri dispositivi secondari nella sessione remota. Alcuni Reindirizzamento del dispositivo richiedono modifiche alle proprietà Remote Desktop Protocol (RDP) e Criteri di gruppo impostazioni.

## <a name="supported-device-redirections"></a>Reindirizzamento dei dispositivi supportati

Ogni client supporta il reindirizzamento dei dispositivi diversi. Vedere [confrontare i client](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-app-compare) per l'elenco completo dei reindirizzamenti di dispositivi supportati per ogni client.

## <a name="customizing-rdp-properties-for-a-host-pool"></a>Personalizzazione delle proprietà RDP per un pool host

Per ulteriori informazioni sulla personalizzazione delle proprietà RDP per un pool host mediante PowerShell o il portale di Azure, vedere [Proprietà RDP](customize-rdp-properties.md). Per l'elenco completo delle proprietà RDP supportate, vedere [impostazioni del file RDP supportate](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context).

## <a name="setup-device-redirections"></a>Configurare il reindirizzamento del dispositivo

Per configurare il reindirizzamento dei dispositivi, è possibile utilizzare le seguenti proprietà RDP e Criteri di gruppo impostazioni.

### <a name="audio-input-microphone-redirection"></a>Reindirizzamento input audio (microfono)

Impostare la proprietà RDP seguente per configurare il reindirizzamento dell'input audio:

- `audiocapturemode:i:1` Abilita il reindirizzamento dell'input audio.
- `audiocapturemode:i:0` Disabilita il reindirizzamento dell'input audio.

### <a name="audio-output-speaker-redirection"></a>Reindirizzamento di output audio (speaker)

Impostare la proprietà RDP seguente per configurare il reindirizzamento dell'output audio:

- `audiomode:i:0` Abilita il reindirizzamento dell'output audio.
- `audiomode:i:1` o `audiomode:i:2` disabilitare il reindirizzamento dell'output audio.

### <a name="camera-redirection"></a>Reindirizzamento della fotocamera

Impostare la proprietà RDP seguente per configurare il reindirizzamento della fotocamera:

- `camerastoredirect:s:*` reindirizza tutte le fotocamere.
- `camerastoredirect:s:` Disabilita il reindirizzamento della fotocamera.

>[!NOTE]
>Anche se la `camerastoredirect:s:` proprietà è disabilitata, le fotocamere locali possono essere reindirizzate tramite la `devicestoredirect:s:` Proprietà. Per disabilitare completamente il set di reindirizzamento della fotocamera `camerastoredirect:s:` e impostare `devicestoredirect:s:` o definire alcuni subset di dispositivi Plug and Play che non includono fotocamere.

È inoltre possibile reindirizzare fotocamere specifiche utilizzando un elenco delimitato da punti e virgola di interfacce di KSCATEGORY_VIDEO_CAMERA, ad esempio `camerastoredirect:s:\?\usb#vid_0bda&pid_58b0&mi` . 

### <a name="clipboard-redirection"></a>Reindirizzamento degli Appunti

Impostare la proprietà RDP seguente per configurare il reindirizzamento degli Appunti:

- `redirectclipboard:i:1` consente il reindirizzamento degli Appunti.
- `redirectclipboard:i:0` Disabilita il reindirizzamento degli Appunti.

### <a name="com-port-redirections"></a>Reindirizzamenti porta COM

Impostare la proprietà RDP seguente per configurare il reindirizzamento delle porte COM:

- `redirectcomports:i:1` Abilita il reindirizzamento delle porte COM.
- `redirectcomports:i:0` Disabilita il reindirizzamento delle porte COM.

### <a name="usb-redirection"></a>Reindirizzamento USB

Per prima cosa, impostare la proprietà RDP seguente per abilitare il reindirizzamento dei dispositivi USB:

- `usbdevicestoredirect:s:*` Abilita il reindirizzamento del dispositivo USB.
- `usbdevicestoredirect:s:` Disabilita il reindirizzamento dei dispositivi USB.

In secondo luogo, impostare il Criteri di gruppo seguente sul dispositivo locale dell'utente:

- Passare a **Configurazione computer**  >  **criteri** >  **modelli amministrativi**  >  **componenti di Windows**  >  **Servizi Desktop remoto**  >  **Connessione desktop remoto client**  >  **RemoteFX il reindirizzamento del dispositivo USB**.
- Select **consente il reindirizzamento RDP di altri dispositivi USB RemoteFX supportati da questo computer**.
- Selezionare l'opzione **Enabled** , quindi selezionare la casella **Administrators and Users in RemoteFX USB redirector Access Rights** .
- Selezionare **OK**.

### <a name="plug-and-play-device-redirection"></a>Reindirizzamento dei dispositivi Plug and Play

Impostare la proprietà RDP seguente per configurare il reindirizzamento dei dispositivi Plug and Play:

- `devicestoredirect:s:*` consente il reindirizzamento di tutti i dispositivi Plug and Play.
- `devicestoredirect:s:` Disabilita il reindirizzamento dei dispositivi Plug and Play.

È anche possibile selezionare dispositivi Plug and Play specifici usando un elenco delimitato da punti e virgola, ad esempio `devicestoredirect:s:root\*PNP0F08` .

### <a name="local-drive-redirection"></a>Reindirizzamento unità locale

Impostare la proprietà RDP seguente per configurare il reindirizzamento dell'unità locale:

- `drivestoredirect:s:*` consente il reindirizzamento di tutte le unità disco.
- `drivestoredirect:s:` Disabilita il reindirizzamento dell'unità locale.

È inoltre possibile selezionare unità specifiche utilizzando un elenco delimitato da punti e virgola, ad esempio `drivestoredirect:s:C:;E:;` .

### <a name="printer-redirection"></a>Reindirizzamento stampanti

Per configurare il reindirizzamento stampanti, impostare la proprietà RDP seguente:

- `redirectprinters:i:1` consente il reindirizzamento della stampante.
- `redirectprinters:i:0` Disabilita il reindirizzamento della stampante.

### <a name="smart-card-redirection"></a>Reindirizzamento Smart Card

Per configurare il reindirizzamento delle smart card, impostare la proprietà RDP seguente:

- `redirectsmartcards:i:1` Abilita il reindirizzamento smart card.
- `redirectsmartcards:i:0` Disabilita il reindirizzamento delle smart card.
