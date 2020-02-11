---
title: Abilitare l'arresto delle macchine virtuali alla disconnessione Azure Lab Services | Microsoft Docs
description: Informazioni su come abilitare o disabilitare l'arresto automatico delle macchine virtuali quando una connessione Desktop remoto è disconnessa.
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
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77117125"
---
# <a name="enable-automatic-shutdown-of-vms-on-disconnect"></a>Abilita arresto automatico delle macchine virtuali in disconnessione
Questo articolo illustra come abilitare o disabilitare l'arresto automatico delle macchine virtuali di **Windows 10** Lab (modello o studente) dopo la disconnessione di una connessione Desktop remoto. È anche possibile specificare per quanto tempo le macchine virtuali devono attendere che l'utente si riconnetta prima di arrestarsi automaticamente.

Un amministratore dell'account Lab può configurare questa impostazione per l'account Lab in cui vengono creati i Lab. Per altre informazioni, vedere [configurare l'arresto automatico delle macchine virtuali in caso di disconnessione per un account Lab](how-to-configure-lab-accounts.md#automatic-shutdown-of-vms-on-disconnect). In qualità di proprietario del Lab, è possibile eseguire l'override dell'impostazione quando si crea un Lab o dopo la creazione del Lab. 

## <a name="configure-when-creating-a-lab"></a>Configurare quando si crea un Lab
Nella pagina passaggio 3 della procedura guidata per la creazione di Lab è possibile abilitare o disabilitare questa funzionalità e specificare anche per quanto tempo la macchina virtuale deve attendere la riconnessione dell'utente prima di arrestarsi automaticamente. 

![Configurare al momento della creazione del Lab](../media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

## <a name="configure-after-the-lab-is-created"></a>Configurare dopo la creazione del Lab
È possibile configurare questa impostazione nella pagina **Impostazioni** come illustrato nell'immagine seguente: 

![Configurare dopo la creazione del Lab](../media/how-to-enable-shutdown-disconnect/configure-lab-automatic-shutdown.png)

> [!WARNING]
> Se si arresta il sistema operativo Windows in una macchina virtuale prima di disconnettere una sessione RDP alla macchina virtuale, la funzionalità di arresto automatico non funzionerà correttamente.  

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Dashboard per laboratori in aula](use-dashboard.md)