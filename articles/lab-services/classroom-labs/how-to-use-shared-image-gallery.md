---
title: Usa una raccolta di immagini condivise di Azure Lab Services | Microsoft Docs
description: Informazioni su come configurare un account del lab per l'uso di una raccolta di immagini condivise in modo che un utente può condividere un'immagine con altri e un altro utente può usare l'immagine per creare un modello di macchina virtuale nel lab.
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
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: 8d8b6fffe197d4180b091518dcd1615d0e0b9d19
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65412861"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Usa una raccolta di immagini condivise di Azure Lab Services
Questo articolo illustra come amministratore di insegnanti/laboratorio consenta di risparmiare un'immagine di macchina virtuale modello affinché possa essere riutilizzato da altri utenti. Queste immagini vengono salvate in un'istanza di Azure [raccolta di immagini condivise](../../virtual-machines/windows/shared-image-galleries.md). Come primo passaggio, l'amministratore di laboratorio associa una raccolta di immagini condivise esistenti per l'account del lab. Una volta la raccolta di immagini condivise è collegata, creato nell'account di laboratorio lab può salvare immagini alla raccolta di immagini condivise. Altri docenti possono selezionare l'immagine dalla raccolta immagini condivise per creare un modello per le classi. 

## <a name="prerequisites"></a>Prerequisiti
- Creare una raccolta di immagini condivise usando [Azure PowerShell](../../virtual-machines/windows/shared-images.md) oppure [CLI Azure](../../virtual-machines/linux/shared-images.md).
- È stato collegato la raccolta di immagini condivise per l'account del lab. Per istruzioni dettagliate, vedere [come collegare o scollegare condivise raccolta immagini](how-to-attach-detach-shared-image-gallery.md).


## <a name="save-an-image-to-the-shared-image-gallery"></a>Salvare un'immagine alla raccolta di immagini condivise
Dopo che è associata una raccolta di immagini condivise, un docente può salvare o caricare un'immagine alla raccolta di immagini condivise in modo che può essere riutilizzato da altri docenti. Per istruzioni per caricare un'immagine alla raccolta di immagini condivise, vedere [Cenni preliminari sulla raccolta di immagini condivise](../../virtual-machines/windows/shared-images.md). 

> [!NOTE]
> Attualmente, l'interfaccia di utente (UI) di Lab per le classi non supporta il salvataggio di un'immagine di lab alla raccolta di immagini condivise. 

## <a name="use-an-image-from-the-shared-image-gallery"></a>Usare un'immagine dalla raccolta immagini condivise
Una docente può selezionare un'immagine personalizzata disponibile nella raccolta immagini condivise per il modello durante la creazione del nuovo lab.

![Usare l'immagine di macchina virtuale dalla raccolta](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle raccolte di immagini condivise, vedere [raccolta di immagini condivise](../../virtual-machines/windows/shared-image-galleries.md).
