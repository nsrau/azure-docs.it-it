---
title: Creare la jump box della soluzione Azure VMware
description: Passaggi per creare la jump box della soluzione Azure VMware.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 5d5a5d82cf6e70e7ad55bcfa10c7f85372131c87
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578419"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-access-private-cloud.md -->

1. Nel gruppo di risorse selezionare **+ Aggiungi**, quindi cercare e selezionare **Microsoft Windows 10** e selezionare **Crea**.

   :::image type="content" source="../media/tutorial-access-private-cloud/ss8-azure-w10vm-create.png" alt-text="Aggiungere una nuova macchina virtuale Windows 10 per una jump box." border="true":::

1. Immettere le informazioni obbligatorie nei campi e quindi selezionare **Verifica e crea**. 

   Per altre informazioni sui campi, vedere la tabella seguente.

   | Campo | Valore |
   | --- | --- |
   | **Sottoscrizione** | Il valore è già popolato con la sottoscrizione appartenente al gruppo di risorse. |
   | **Gruppo di risorse** | Il valore è già popolato per il gruppo di risorse corrente, creato nell'esercitazione precedente.  |
   | **Nome macchina virtuale** | Immettere un nome univoco per la VM. |
   | **Area** | Selezionare la posizione geografica della VM. |
   | **Opzioni di disponibilità** | Lasciare selezionato il valore predefinito. |
   | **Immagine** | Selezionare l'immagine della VM. |
   | **Dimensione** | Lasciare il valore predefinito. |
   | **Tipo di autenticazione**  | selezionare **Password**. |
   | **Nome utente** | Immettere il nome utente per l'accesso alla VM. |
   | **Password** | Immettere la password per l'accesso alla VM. |
   | **Conferma password** | Immettere la password per l'accesso alla VM. |
   | **Porte in ingresso pubbliche** | Selezionare **Nessuno**. Se si seleziona Nessuno, è possibile usare l'[accesso JIT](../../security-center/security-center-just-in-time.md#jit-configure) per controllare l'accesso alla macchina virtuale solo quando si vuole accedervi.  |


1. Al termine della convalida, selezionare **Crea** per avviare il processo di creazione della macchina virtuale.

   :::image type="content" source="../media/tutorial-access-private-cloud/ss11-review-create-wjb01.png" alt-text="Aggiungere una nuova macchina virtuale Windows 10 per una jump box." border="true":::