---
title: Abilitare l'arresto delle macchine virtuali al termine della connessione Azure Lab Services | Microsoft Docs
description: Informazioni su come abilitare o disabilitare l'arresto automatico delle macchine virtuali al termine di una connessione desktop remota.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 0c5c22d5e4a9d66413e37cce095f5497915bd122
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445713"
---
# <a name="enable-automatic-shutdown-of-vms-on-disconnect"></a>Abilitare l'arresto automatico delle macchine virtuali al termine della connessione
Questo articolo spiega come abilitare o disabilitare l'arresto automatico delle macchine virtuali lab **Windows 10** (modello o studente) al termine di una connessione desktop remota. È inoltre possibile specificare per quanto tempo le macchine virtuali devono attendere che l'utente si riconnetta prima di arrestarsi automaticamente.

Un amministratore dell'account lab può configurare questa impostazione per l'account in cui vengono creati i lab. Per altre informazioni, vedere [Configurare l'arresto automatico delle macchine virtuali tramite l'impostazione di disconnessione per un account lab](how-to-configure-lab-accounts.md). Il proprietario del lab può eseguire l'override dell'impostazione quando crea un lab o dopo la creazione. 

## <a name="configure-when-creating-a-lab"></a>Configurare quando si crea un lab
Nella pagina con il passaggio 3 della procedura guidata per la creazione di un lab, è possibile abilitare o disabilitare questa funzionalità e specificare anche per quanto tempo la macchina virtuale deve attendere la riconnessione dell'utente prima di arrestarsi automaticamente. 

![Configurare al momento della creazione del lab](./media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

## <a name="configure-after-the-lab-is-created"></a>Configurare dopo la creazione del lab
È possibile configurare questa impostazione nella pagina **Impostazioni**, come illustrato nell'immagine seguente: 

![Configurare dopo la creazione del lab](./media/how-to-enable-shutdown-disconnect/configure-lab-automatic-shutdown.png)

> [!WARNING]
> Se si arresta il sistema operativo Windows in una macchina virtuale prima di disconnettere una sessione RDP con la macchina virtuale, la funzionalità di arresto automatico non funzionerà correttamente.  

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Dashboard per lab per le classi](use-dashboard.md)