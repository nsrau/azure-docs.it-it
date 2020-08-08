---
title: Macchine virtuali Windows 7-desktop virtuale Windows (classico)-Azure
description: Come risolvere i problemi per le macchine virtuali (VM) Windows 7 in un ambiente desktop virtuale Windows (classico).
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1d71b4472b16372927db56b20eca4fcbde0cc625
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2020
ms.locfileid: "88005415"
---
# <a name="troubleshoot-windows-7-virtual-machines-in-windows-virtual-desktop-classic"></a>Risolvere i problemi relativi alle macchine virtuali Windows 7 in desktop virtuale di Windows (versione classica)

>[!IMPORTANT]
>Questo contenuto si applica a Desktop virtuale Windows (versione classica), che non supporta gli oggetti Azure Resource Manager di Desktop virtuale Windows.

Usare questo articolo per risolvere i problemi che si verificano durante la configurazione delle macchine virtuali (VM) host sessione desktop virtuale di Windows.

## <a name="known-issues"></a>Problemi noti

Windows 7 nei desktop virtuali Windows non supporta le funzionalità seguenti:

- Applicazioni virtualizzate (RemoteApps)
- Reindirizzamento del fuso orario
- Ridimensionamento DPI automatico

Desktop virtuale Windows può virtualizzare solo desktop completi per Windows 7.

Mentre la scalabilità DPI automatica non è supportata, è possibile modificare manualmente la risoluzione nella macchina virtuale facendo clic con il pulsante destro del mouse sull'icona nel client Desktop remoto e selezionando **risoluzione**.

## <a name="error-cant-access-the-remote-desktop-user-group"></a>Errore: Impossibile accedere al gruppo di utenti Desktop remoto

Se il desktop virtuale di Windows non riesce a trovare le credenziali degli utenti nel gruppo di utenti Desktop remoto, potrebbe essere visualizzato uno dei messaggi di errore seguenti:

- "L'utente non è un membro del gruppo di utenti Desktop remoto"
- "È necessario disporre delle autorizzazioni per accedere tramite Servizi Desktop remoto"

Per correggere l'errore, aggiungere l'utente al gruppo di utenti Desktop remoto:

1. Aprire il portale di Azure.
2. Selezionare la macchina virtuale in cui è stato visualizzato il messaggio di errore.
3. Selezionare **Esegui un comando**.
4. Eseguire il comando seguente con `<username>` sostituito dal nome dell'utente che si desidera aggiungere:

   ```cmd
   net localgroup "Remote Desktop Users" <username> /add
   ```