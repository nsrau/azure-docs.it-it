---
title: Abilitare l'arresto delle macchine virtuali alla disconnessione di Azure Lab Services . Documenti Microsoft
description: Informazioni su come abilitare o disabilitare l'arresto automatico delle macchine virtuali quando una connessione desktop remoto viene disconnessa.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/13/2019
ms.author: spelluru
ms.openlocfilehash: 68a27a325a0ef02c6eeea9867a21ba0e24ab5321
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77117125"
---
# <a name="enable-automatic-shutdown-of-vms-on-disconnect"></a>Abilitare l'arresto automatico delle macchine virtuali alla disconnessioneEnable automatic shutdown of VMs on disconnect
Questo articolo illustra come abilitare o disabilitare l'arresto automatico delle macchine virtuali lab di **Windows 10** (modello o studente) dopo la disconnessione di una connessione desktop remoto. È inoltre possibile specificare per quanto tempo le macchine virtuali devono attendere la riconnessione dell'utente prima dell'arresto automatico.

Un amministratore dell'account lab può configurare questa impostazione per l'account lab in cui si creano i lab. Per ulteriori informazioni, vedere [Configurare l'arresto automatico delle macchine virtuali alla disconnessione per un account lab.](how-to-configure-lab-accounts.md#automatic-shutdown-of-vms-on-disconnect) In qualità di proprietario del lab, è possibile ignorare l'impostazione durante la creazione di un lab o dopo la creazione del lab. 

## <a name="configure-when-creating-a-lab"></a>Configurare durante la creazione di un labConfigure when creating a lab
Nella pagina passaggio 3 della creazione guidata lab è possibile abilitare o disabilitare questa funzionalità e specificare per quanto tempo la macchina virtuale deve attendere la riconnessione dell'utente prima dell'arresto automatico. 

![Configurare al momento della creazione del lab](../media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

## <a name="configure-after-the-lab-is-created"></a>Configurare dopo la creazione del lab
È possibile configurare questa impostazione nella pagina **Impostazioni** come illustrato nell'immagine seguente: 

![Configurare dopo la creazione del lab](../media/how-to-enable-shutdown-disconnect/configure-lab-automatic-shutdown.png)

> [!WARNING]
> Se si arresta il sistema operativo Windows in una macchina virtuale prima di disconnettere una sessione RDP alla macchina virtuale, la funzionalità di arresto automatico non funzionerà correttamente.  

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Dashboard per i laboratori in aula](use-dashboard.md)