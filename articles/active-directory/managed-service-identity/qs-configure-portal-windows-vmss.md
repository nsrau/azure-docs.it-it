---
title: Configurare un'identità del servizio gestita in un set di scalabilità di macchine virtuali di Azure tramite il portale di Azure
description: Istruzioni dettagliate per la configurazione di un'identità del servizio gestita in un set di scalabilità di macchine virtuali di Azure tramite il portale di Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: cbe2e3d9f60ced5c707ce5a701a5aac937ccc072
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/24/2018
ms.locfileid: "42887990"
---
# <a name="configure-a-virtual-machine-scale-set-managed-service-identity-using-the-azure-portal"></a>Configurare un'identità del servizio gestita in un set di scalabilità di macchine virtuali tramite il portale di Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identità del servizio gestito offre servizi di Azure con un'identità gestita automaticamente in Azure Active Directory. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

Questo articolo illustra come abilitare e disabilitare l'identità assegnata dal sistema e dall'utente per un set di scalabilità di macchine virtuali usando il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha familiarità con l'identità del servizio gestita, vedere la [sezione sulla panoramica](overview.md).
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.
- Per eseguire le operazioni di gestione illustrate in questo articolo, l'account deve avere l'assegnazione di ruolo seguente:
    - [Collaboratore macchina virtuale](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) per abilitare e rimuovere l'identità gestita assegnata dal sistema da un set di scalabilità di macchine virtuali.

## <a name="system-assigned-identity"></a>Identità assegnata dal sistema 

Questa sezione illustra come abilitare e disabilitare l'identità assegnata dal sistema in un set di scalabilità di macchine virtuali usando il portale di Azure.

### <a name="enable-system-assigned-identity-during-creation-of-a-virtual-machine-scale-set"></a>Abilitare l'identità assegnata dal sistema durante la creazione di un set di scalabilità di macchine virtuali

Attualmente il portale di Azure non supporta l'abilitazione dell'identità assegnata dal sistema durante la creazione di un set di scalabilità di macchine virtuali. In alternativa, consultare la seguente guida introduttiva per creare prima un set di scalabilità di macchine virtuali e quindi proseguire con la sezione successiva per informazioni dettagliate sull'abilitazione dell'identità assegnata dal sistema in un set di scalabilità di macchine virtuali:

- [Creare un set di scalabilità di macchine virtuali nel portale di Azure](../../virtual-machine-scale-sets/quick-create-portal.md)  

### <a name="enable-system-assigned-identity-on-an-existing-virtual-machine-scale-set"></a>Abilitare l'identità assegnata dal sistema in un set di scalabilità di macchine virtuali esistente

Per abilitare l'identità assegnata dal sistema in un set di scalabilità di macchine virtuali di cui è stato originariamente eseguito il provisioning senza tale identità:

1. Accedere al [portale di Azure](https://portal.azure.com) usando un account associato alla sottoscrizione di Azure che contiene il set di scalabilità di macchine virtuali.

2. Passare al set di scalabilità di macchine virtuali desiderato.

3. In **Assegnata dal sistema**, **Stato**, selezionare **Abilita** e quindi fare clic su **Salva**:

   [![Screenshot della pagina Configurazione](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png)](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png#lightbox)  

### <a name="remove-system-assigned-identity-from-a-virtual-machine-scale-set"></a>Rimuovere un'identità assegnata dal sistema da un set di scalabilità di macchine virtuali

Se è disponibile un set di scalabilità di macchine virtuali per cui non è più necessaria un'identità assegnata dal sistema:

1. Accedere al [portale di Azure](https://portal.azure.com) usando un account associato alla sottoscrizione di Azure che contiene il set di scalabilità di macchine virtuali. Assicurarsi anche che l'account appartenga a un ruolo che fornisce le autorizzazioni di scrittura nel set di scalabilità di macchine virtuali.

2. Passare al set di scalabilità di macchine virtuali desiderato.

3. In **Assegnata dal sistema**, **Stato**, selezionare **Disabilita** e quindi fare clic su **Salva**:

   ![Schermata della pagina Configurazione](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)

## <a name="user-assigned-identity"></a>Identità assegnata dall'utente

Questa sezione illustra come aggiungere e rimuovere un'identità assegnata dall'utente da un set di scalabilità di macchine virtuali usando il portale di Azure.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Assegnare un'identità assegnata all'utente durante la creazione di un set di scalabilità di macchine virtuali

Attualmente il portale di Azure non supporta l'assegnazione di un'identità assegnata dall'utente durante la creazione di un set di scalabilità di macchine virtuali. In alternativa, consultare la seguente guida introduttiva per creare prima un set di scalabilità di macchine virtuali e quindi proseguire con la sezione successiva per informazioni dettagliate sull'associazione di un'identità assegnata dall'utente a un set di scalabilità di macchine virtuali:

- [Creare un set di scalabilità di macchine virtuali nel portale di Azure](../../virtual-machine-scale-sets/quick-create-portal.md)

### <a name="assign-a-user-assigned-identity-to-an-existing-virtual-machine-scale-set"></a>Associare l'identità assegnata dall'utente a un set di scalabilità di macchine virtuali esistente

1. Accedere al [portale di Azure](https://portal.azure.com) usando un account associato alla sottoscrizione di Azure che contiene il set di scalabilità di macchine virtuali.
2. Passare al set di scalabilità di macchine virtuali desiderato e fare clic su **Identità**, **Assegnata dall'utente** e quindi **\+Aggiungi**.

   ![Aggiungere l'identità assegnata dall'utente al set di scalabilità di macchine virtuali](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vmss-screenshot1.png)

3. Fare clic sull'identità assegnata dall'utente da aggiungere al set di scalabilità di macchine virtuali e quindi su **Aggiungi**.
   
   ![Aggiungere l'identità assegnata dall'utente al set di scalabilità di macchine virtuali](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-identity-from-a-virtual-machine-scale-set"></a>Rimuovere un'identità assegnata dall'utente da un set di scalabilità di macchine virtuali

1. Accedere al [portale di Azure](https://portal.azure.com) usando un account associato alla sottoscrizione di Azure che contiene la VM.
2. Passare al set di scalabilità di macchine virtuali desiderato e selezionare **Identità**, **Assegnata dall'utente** e il nome dell'identità assegnata dall'utente da eliminare e quindi fare clic su **Rimuovi** (scegliere **Sì** nel riquadro di conferma).

   ![Rimuovere un'identità assegnata dall'utente da un set di scalabilità di macchine virtuali](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vmss-screenshot.png)


## <a name="related-content"></a>Contenuti correlati

- Per una panoramica dell'identità del servizio gestita, vedere le [informazioni generali](overview.md).

## <a name="next-steps"></a>Passaggi successivi

- Tramite il portale di Azure, concedere all'identità del servizio gestita del set di scalabilità di macchine virtuali l'[accesso a un'altra risorsa di Azure](howto-assign-access-portal.md).

Usare la sezione dei commenti seguente per fornire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.
