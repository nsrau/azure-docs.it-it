---
title: Come configurare identità gestite per risorse di Azure in un set di scalabilità di macchine virtuali
description: Istruzioni dettagliate per la configurazione di identità gestite per le risorse di Azure in un set di scalabilità di macchine virtuali tramite il portale di Azure.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57f0ec91bd5c72b593d9b28f7d47f691181a6a0f
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58446314"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-the-azure-portal"></a>Configurare identità gestite per risorse di Azure in un set di scalabilità di macchine virtuali tramite il portale di Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Le identità gestite per le risorse di Azure offrono ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

Questo articolo illustra come eseguire le operazioni relative alle identità gestite per risorse di Azure in un set di scalabilità di macchine virtuali mediante PowerShell:

- Se non si ha familiarità con le identità gestite per le risorse di Azure, vedere la [sezione sulla panoramica](overview.md).
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.
- Per eseguire le operazioni di gestione illustrate in questo articolo, l'account deve avere le seguenti assegnazioni di controllo degli accessi in base al ruolo:

    > [!NOTE]
    > Non sono necessarie altre assegnazioni di ruoli della directory di Azure AD.

    - [Collaboratore Macchina virtuale](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) per abilitare e rimuovere l'identità gestita assegnata dal sistema da un set di scalabilità di macchine virtuali.

## <a name="system-assigned-managed-identity"></a>Identità gestita assegnata dal sistema

Questa sezione descriverà come abilitare e disabilitare un'identità gestita assegnata dal sistema tramite il portale di Azure.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>Abilitare l'identità gestita assegnata dal sistema durante la creazione di un set di scalabilità di macchine virtuali

Attualmente il portale di Azure non supporta l'abilitazione dell'identità gestita assegnata dal sistema durante la creazione di un set di scalabilità di macchine virtuali. In alternativa, consultare la guida introduttiva seguente per creare prima un set di scalabilità di macchine virtuali e quindi proseguire con la sezione successiva per informazioni dettagliate sull'abilitazione dell'identità gestita assegnata dal sistema in un set di scalabilità di macchine virtuali:

- [Creare un set di scalabilità di macchine virtuali nel portale di Azure](../../virtual-machine-scale-sets/quick-create-portal.md)  

### <a name="enable-system-assigned-managed-identity-on-an-existing-virtual-machine-scale-set"></a>Abilitare l'identità gestita assegnata dal sistema in un set di scalabilità di macchine virtuali esistente

Per abilitare l'identità gestita assegnata dal sistema in un set di scalabilità di macchine virtuali di cui è stato originariamente eseguito il provisioning senza tale identità:

1. Accedere al [portale di Azure](https://portal.azure.com) usando un account associato alla sottoscrizione di Azure che contiene il set di scalabilità di macchine virtuali.

2. Passare al set di scalabilità di macchine virtuali desiderato.

3. In **Assegnata dal sistema**, **Stato**, selezionare **Abilita** e quindi fare clic su **Salva**:

   ![Schermata della pagina Configurazione](./media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png) 

### <a name="remove-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Rimuovere un'identità gestita assegnata dal sistema da un set di scalabilità di macchine virtuali

Se è disponibile un set di scalabilità di macchine virtuali per cui non è più necessaria un'identità gestita assegnata dal sistema:

1. Accedere al [portale di Azure](https://portal.azure.com) usando un account associato alla sottoscrizione di Azure che contiene il set di scalabilità di macchine virtuali. Assicurarsi anche che l'account appartenga a un ruolo che fornisce le autorizzazioni di scrittura nel set di scalabilità di macchine virtuali.

2. Passare al set di scalabilità di macchine virtuali desiderato.

3. In **Assegnata dal sistema**, **Stato**, selezionare **Disabilita** e quindi fare clic su **Salva**:

   ![Schermata della pagina Configurazione](./media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)

## <a name="user-assigned-managed-identity"></a>Identità gestita assegnata dall'utente

Questa sezione illustra come aggiungere e rimuovere un'identità gestita assegnata dall'utente da un set di scalabilità di macchine virtuali usando il portale di Azure.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Assegnare un'identità gestita assegnata dall'utente durante la creazione di un set di scalabilità di macchine virtuali

Attualmente il portale di Azure non supporta l'associazione di un'identità gestita assegnata dall'utente durante la creazione di un set di scalabilità di macchine virtuali. In alternativa, consultare la guida introduttiva seguente per creare prima un set di scalabilità di macchine virtuali e quindi proseguire con la sezione successiva per informazioni dettagliate sull'associazione di un'identità gestita assegnata dall'utente a un set di scalabilità di macchine virtuali:

- [Creare un set di scalabilità di macchine virtuali nel portale di Azure](../../virtual-machine-scale-sets/quick-create-portal.md)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Associare l'identità gestita assegnata dall'utente a un set di scalabilità di macchine virtuali esistente

1. Accedere al [portale di Azure](https://portal.azure.com) usando un account associato alla sottoscrizione di Azure che contiene il set di scalabilità di macchine virtuali.
2. Passare al set di scalabilità di macchine virtuali desiderato e fare clic su **Identità**, **Assegnata dall'utente** e quindi **\+Aggiungi**.

   ![Aggiungere l'identità assegnata dall'utente al set di scalabilità di macchine virtuali](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vmss-screenshot1.png)

3. Fare clic sull'identità assegnata dall'utente da aggiungere al set di scalabilità di macchine virtuali e quindi su **Aggiungi**.
   
   ![Aggiungere l'identità assegnata dall'utente al set di scalabilità di macchine virtuali](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Rimuovere un'identità gestita assegnata dall'utente da un set di scalabilità di macchine virtuali

1. Accedere al [portale di Azure](https://portal.azure.com) usando un account associato alla sottoscrizione di Azure che contiene la VM.
2. Passare al set di scalabilità di macchine virtuali desiderato e selezionare **Identità**, **Assegnata dall'utente** e il nome dell'identità gestita assegnata dall'utente da eliminare e quindi fare clic su **Rimuovi** (scegliere **Sì** nel riquadro di conferma).

   ![Rimuovere un'identità assegnata dall'utente da un set di scalabilità di macchine virtuali](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vmss-screenshot.png)


## <a name="next-steps"></a>Passaggi successivi

- Tramite il portale di Azure, concedere all'identità gestita del set di scalabilità di macchine virtuali l'[accesso a un'altra risorsa di Azure](howto-assign-access-portal.md).


