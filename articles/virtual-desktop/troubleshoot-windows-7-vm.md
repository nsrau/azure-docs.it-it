---
title: Risolvere i problemi relativi alle macchine virtuali Windows 7 in desktop virtuale di Windows-Azure
description: Come risolvere i problemi per le macchine virtuali (VM) Windows 7 in un ambiente desktop virtuale di Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 09/23/2019
ms.author: helohr
ms.openlocfilehash: 7fdb46ac7cffc4eb6a791304c7a6b58378806296
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679893"
---
# <a name="troubleshoot-windows-7-virtual-machines-in-windows-virtual-desktop"></a>Risolvere i problemi relativi alle macchine virtuali Windows 7 in desktop virtuale di Windows

Usare questo articolo per risolvere i problemi che si verificano durante la configurazione delle macchine virtuali (VM) host sessione desktop virtuale di Windows.

## <a name="known-issues"></a>Problemi noti

Windows 7 nei desktop virtuali Windows non supporta le funzionalità seguenti:

- Applicazioni virtualizzate (RemoteApps)
- Reindirizzamento del fuso orario
- Ridimensionamento DPI automatico

Desktop virtuale Windows può virtualizzare solo desktop completi per Windows 7.

Mentre la scalabilità DPI automatica non è supportata, è possibile modificare manualmente la risoluzione nella macchina virtuale facendo clic con il pulsante destro del mouse sull'icona nel client Desktop remoto e selezionando **risoluzione**.

## <a name="error-cant-access-the-remote-desktop-user-group"></a>Errore: Non è possibile accedere al gruppo di utenti Desktop remoto

Se il desktop virtuale di Windows non riesce a trovare le credenziali degli utenti nel gruppo di utenti Desktop remoto, potrebbe essere visualizzato uno dei messaggi di errore seguenti:

- "L'utente non è un membro del gruppo di utenti Desktop remoto"
- "È necessario disporre delle autorizzazioni per accedere tramite Servizi Desktop remoto"

Per correggere l'errore, aggiungere l'utente al gruppo di utenti Desktop remoto:

1. Aprire il Portale di Azure.
2. Selezionare la macchina virtuale in cui è stato visualizzato il messaggio di errore.
3. Selezionare **Esegui un comando**.
4. Eseguire il comando seguente con `<username>` sostituito dal nome dell'utente che si vuole aggiungere:
   
   ```cmd
   localgroup "Remote Desktop Users" <username> /add
   ```