---
title: Come configurare un'identità del servizio gestita in una macchina virtuale di Azure tramite il portale di Azure
description: Istruzioni dettagliate per la configurazione dell'identità del servizio gestita in una macchina virtuale di Azure, tramite il portale di Azure.
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
ms.date: 09/19/2017
ms.author: daveba
ms.openlocfilehash: 552fce2ffd8b6bd786010da82e702ee98c3f8647
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/24/2018
ms.locfileid: "42888557"
---
# <a name="configure-a-vm-managed-service-identity-using-the-azure-portal"></a>Configurare un'identità del servizio gestita della macchina virtuale tramite il portale di Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identità del servizio gestito offre servizi di Azure con un'identità gestita automaticamente in Azure Active Directory. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

Questo articolo illustra come abilitare e disabilitare l'identità assegnata dal sistema e dall'utente per una macchina virtuale (VM) di Azure usando il portale di Azure. 

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha familiarità con l'identità del servizio gestita, vedere la [sezione sulla panoramica](overview.md).
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.
- Per eseguire le operazioni di gestione illustrate in questo articolo, l'account deve avere l'assegnazione di ruolo seguente:
    - [Collaboratore macchina virtuale](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) per abilitare e rimuovere l'identità gestita assegnata dal sistema da una macchina virtuale di Azure.

## <a name="system-assigned-identity"></a>Identità assegnata dal sistema

Questa sezione illustra come abilitare e disabilitare l'identità assegnata dal sistema in una macchina virtuale di Azure usando il portale di Azure.

### <a name="enable-system-assigned-identity-during-creation-of-a-vm"></a>Abilitare l'identità assegnata dal sistema durante la creazione di una macchina virtuale

Attualmente il portale di Azure non supporta l'abilitazione dell'identità assegnata dal sistema durante la creazione di una macchina virtuale. In alternativa, consultare una delle seguenti guide introduttive per creare prima una macchina virtuale e quindi proseguire con la sezione successiva per informazioni dettagliate sull'abilitazione dell'identità assegnata dal sistema alla macchina virtuale:

- [Creare una macchina virtuale Windows con il portale di Azure](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Creare una macchina virtuale Linux con il portale di Azure](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

### <a name="enable-system-assigned-identity-on-an-existing-vm"></a>Abilitare l'identità assegnata dal sistema in una macchina virtuale esistente

Per abilitare l'identità assegnata dal sistema in una macchina virtuale di cui è stato originariamente eseguito il provisioning senza di essa:

1. Accedere al [portale di Azure](https://portal.azure.com) usando un account associato alla sottoscrizione di Azure che contiene la VM.

2. Passare alla macchina virtuale desiderata e selezionare **Identità**.

3. In **Assegnata dal sistema**, **Stato**, selezionare **Abilita** e quindi fare clic su **Salva**:

   ![Schermata della pagina Configurazione](../managed-service-identity/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

### <a name="remove-system-assigned-identity-from-a-vm"></a>Rimuovere l'identità assegnata dal sistema da una macchina virtuale

Se si dispone di una macchina virtuale per cui non è più necessaria un'identità del servizio gestita:

1. Accedere al [portale di Azure](https://portal.azure.com) usando un account associato alla sottoscrizione di Azure che contiene la VM. 

2. Passare alla macchina virtuale desiderata e selezionare **Identità**.

3. In **Assegnata dal sistema**, **Stato**, selezionare **Disabilita** e quindi fare clic su **Salva**:

   ![Schermata della pagina Configurazione](../managed-service-identity/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)

## <a name="user-assigned-identity"></a>Identità assegnata dall'utente

 Questa sezione illustra come aggiungere e rimuovere un'identità assegnata dall'utente da una macchina virtuale usando il portale di Azure.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-vm"></a>Assegnare un'identità assegnata dall'utente durante la creazione di una macchina virtuale

Attualmente il portale di Azure non supporta l'assegnazione di un'identità assegnata dall'utente durante la creazione di una macchina virtuale. In alternativa, consultare una delle seguenti guide introduttive per creare prima una macchina virtuale e quindi proseguire con la sezione successiva per informazioni dettagliate sull'associazione di un'identità assegnata dall'utente alla macchina virtuale:

- [Creare una macchina virtuale Windows con il portale di Azure](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Creare una macchina virtuale Linux con il portale di Azure](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)

### <a name="assign-a-user-assigned-identity-to-an-existing-vm"></a>Assegnare un'identità assegnata dall'utente a una macchina virtuale esistente

1. Accedere al [portale di Azure](https://portal.azure.com) usando un account associato alla sottoscrizione di Azure che contiene la VM.
2. Passare alla macchina virtuale desiderata e fare clic su **Identità**, **Assegnata dall'utente** e quindi **\+Aggiungi**.

   ![Aggiungere l'identità assegnata dall'utente alla macchina virtuale](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot1.png)

3. Fare clic sull'identità assegnata dall'utente da aggiungere alla macchina virtuale e quindi su **Aggiungi**.

    ![Aggiungere l'identità assegnata dall'utente alla macchina virtuale](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-identity-from-a-vm"></a>Rimuovere un'identità assegnata dall'utente da una macchina virtuale

1. Accedere al [portale di Azure](https://portal.azure.com) usando un account associato alla sottoscrizione di Azure che contiene la VM.
2. Passare alla macchina virtuale desiderata e selezionare **Identità**, **Assegnata dall'utente** e il nome dell'identità assegnata dall'utente da eliminare e quindi fare clic su **Rimuovi** (scegliere **Sì** nel riquadro di conferma).

   ![Rimuovere un'identità assegnata dall'utente da una macchina virtuale](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vm-screenshot.png)

## <a name="related-content"></a>Contenuti correlati

- Per una panoramica dell'identità del servizio gestita, vedere le [informazioni generali](overview.md).

## <a name="next-steps"></a>Passaggi successivi

- Tramite il portale di Azure concedere all'identità del servizio gestita del set di scalabilità di macchine virtuali l'[accesso a un'altra risorsa di Azure](howto-assign-access-portal.md).

