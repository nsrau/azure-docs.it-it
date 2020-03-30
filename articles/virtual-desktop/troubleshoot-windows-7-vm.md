---
title: Macchine virtuali Windows 7 Desktop virtuale di Windows - Azure
description: Come risolvere i problemi per le macchine virtuali (VM) di Windows 7 in un ambiente Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 09/23/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a2ff3f6fa9896e45ecd6ab40d40d46a046edf1cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127382"
---
# <a name="troubleshoot-windows-7-virtual-machines-in-windows-virtual-desktop"></a>Risolvere i problemi relativi alle macchine virtuali Windows 7 in Desktop virtuale Windows

Usare questo articolo per risolvere i problemi riscontrati durante la configurazione delle macchine virtuali (VM) host sessione Desktop virtuale di Windows.Use this article to troubleshoot issues you're having've when configuring the Windows Virtual Desktop session host virtual machines (VMs).

## <a name="known-issues"></a>Problemi noti

Windows 7 su Desktop virtuali di Windows non supporta le seguenti funzionalità:

- Applicazioni virtualizzate (RemoteApps)Virtualized applications (RemoteApps)
- Reindirizzamento fuso orario
- Ridimensionamento DPI automatico

Windows Virtual Desktop può virtualizzare solo desktop completi per Windows 7.

Sebbene il ridimensionamento DPI automatico non sia supportato, è possibile modificare manualmente la risoluzione nella macchina virtuale facendo clic con il pulsante destro del mouse sull'icona nel client Desktop remoto e selezionando **Risoluzione**.

## <a name="error-cant-access-the-remote-desktop-user-group"></a>Errore: Impossibile accedere al gruppo Utente Desktop remoto

Se Windows Virtual Desktop non riesce a trovare le credenziali dell'utente nel gruppo Utenti Desktop remoto, è possibile che venga visualizzato uno dei seguenti messaggi di errore:

- "Questo utente non è un membro del gruppo Utenti desktop remoto"
- "È necessario disporre delle autorizzazioni per accedere tramite Servizi Desktop remoto"

Per correggere questo errore, aggiungere l'utente al gruppo Utente Desktop remoto:

1. Aprire il portale di Azure.
2. Selezionare la macchina virtuale in cui è stato visualizzato il messaggio di errore.
3. Selezionare **Esegui un comando**.
4. Eseguire il comando `<username>` seguente con sostituito dal nome dell'utente che si desidera aggiungere:
   
   ```cmd
   net localgroup "Remote Desktop Users" <username> /add
   ```