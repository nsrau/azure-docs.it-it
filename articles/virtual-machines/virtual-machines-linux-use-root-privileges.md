---
title: Usare i privilegi root in macchine virtuali Linux | Microsoft Docs
description: Informazioni su come usare i privilegi root in una macchina virtuale Linux in Azure.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: timlt
editor: ''
tags: azure-service-management,azure-resource-manager

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/13/2016
ms.author: szark

---
# Uso di privilegi root su Linux in Macchine virtuali di Azure
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Per impostazione predefinita, l'utente `root` è disabilitato nelle macchine virtuali Linux in Azure. Gli utenti possono eseguire comandi con privilegi elevati usando il comando `sudo`. L'esperienza può tuttavia variare in base alla modalità usata per il provisioning del sistema.

1. **Chiave SSH e password o solo password**: il provisioning della macchina virtuale è stato effettuato con un certificato (file `.CER`) o una chiave SSH e con una password oppure solo con un nome utente e una password. In questo caso `sudo` richiederà la password dell'utente prima di eseguire il comando.
2. **Solo chiave SSH**: il provisioning della macchina virtuale è stato effettuato con un certificato (file `.cer`, `.pem` o `.pub`) o una chiave SSH, ma senza password. In questo caso `sudo` **non** richiederà la password dell'utente prima di eseguire il comando.

## Chiave SSH e password o solo password
Accedere alla macchina virtuale Linux usando l'autenticazione con chiave SSH o password, quindi eseguire i comandi tramite `sudo`, ad esempio:

    # sudo <command>
    [sudo] password for azureuser:

In questo caso, all'utente verrà richiesta una password. Dopo avere immesso la password `sudo` eseguirà il comando con privilegi `root`.

È anche possibile abilitare sudo senza password modificando il file `/etc/sudoers.d/waagent`, ad esempio:

    #/etc/sudoers.d/waagent
    azureuser ALL = (ALL) NOPASSWD: ALL

Questa modifica permetterà all'utente azureuser di usare sudo senza password.

## Solo chiave SSH
Accedere alla macchina virtuale Linux usando l'autenticazione con chiave SSH, quindi eseguire i comandi tramite `sudo`, ad esempio:

    # sudo <command>

In questo caso, all'utente **non** verrà richiesta una password. Dopo aver premuto `<enter>`, `sudo` eseguirà il comando con privilegi `root`.

<!---HONumber=AcomDC_0713_2016-->