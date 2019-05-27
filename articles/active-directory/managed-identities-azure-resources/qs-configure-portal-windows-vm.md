---
title: Come configurare le identità gestite per le risorse di Azure in una macchina virtuale di Azure tramite il portale di Azure
description: Istruzioni dettagliate per la configurazione di identità gestite per le risorse di Azure in una macchina virtuale di Azure tramite il portale di Azure.
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
ms.date: 11/10/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab0a5b021048f0f684473d3f54bbeadf870cd007
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66112851"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-vm-using-the-azure-portal"></a>Configurare le identità gestite per le risorse di Azure in una macchina virtuale tramite il portale di Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Le identità gestite per le risorse di Azure offrono ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

Questo articolo illustra come abilitare e disabilitare le identità gestite assegnate dal sistema e dall'utente per una macchina virtuale (VM) di Azure usando il portale di Azure. 

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha familiarità con le identità gestite per le risorse di Azure, vedere la [sezione sulla panoramica](overview.md).
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.

## <a name="system-assigned-managed-identity"></a>Identità gestita assegnata dal sistema

Questa sezione illustra come abilitare e disabilitare l'identità gestita assegnata dal sistema per una macchina virtuale usando il portale di Azure.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-vm"></a>Abilitare l'identità gestita assegnata dal sistema durante la creazione di una macchina virtuale

Per abilitare l'identità gestita assegnata dal sistema in una macchina virtuale durante la creazione, all'account deve essere assegnato il ruolo [Collaboratore Macchina virtuale](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).  Non sono necessarie altre assegnazioni di ruoli di Azure Active Directory.

- Nella sezione **Identità** della scheda **Gestione** **attivare** l'opzione **Identità del servizio gestita**.  

![Abilitare l'identità assegnata dal sistema durante la creazione di una macchina virtuale](./media/msi-qs-configure-portal-windows-vm/enable-system-assigned-identity-vm-creation.png)

Fare riferimento alle guide introduttive seguenti per creare una macchina virtuale: 

- [Creare una macchina virtuale Windows con il portale di Azure](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine) 
- [Creare una macchina virtuale Linux con il portale di Azure](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)


### <a name="enable-system-assigned-managed-identity-on-an-existing-vm"></a>Abilitare l'identità gestita assegnata dal sistema in una macchina virtuale esistente

Per abilitare l'identità gestita assegnata dal sistema in una macchina virtuale di cui originariamente è stato effettuato il provisioning senza tale identità, all'account deve essere assegnato il ruolo [Collaboratore Macchina virtuale](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).  Non sono necessarie altre assegnazioni di ruoli di Azure Active Directory.

1. Accedere al [portale di Azure](https://portal.azure.com) usando un account associato alla sottoscrizione di Azure che contiene la VM.

2. Passare alla macchina virtuale desiderata e selezionare **Identità**.

3. In **Assegnata dal sistema**, **Stato**, selezionare **Abilita** e quindi fare clic su **Salva**:

   ![Schermata della pagina Configurazione](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

### <a name="remove-system-assigned-managed-identity-from-a-vm"></a>Rimuovere un'identità gestita assegnata dal sistema da una macchina virtuale

Per rimuovere l'identità gestita assegnata dal sistema da una macchina virtuale, all'account deve essere assegnato il ruolo [Collaboratore Macchina virtuale](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).  Non sono necessarie altre assegnazioni di ruoli di Azure Active Directory.

Se si dispone di una macchina virtuale per cui non è più necessaria un'identità gestita assegnata dal sistema:

1. Accedere al [portale di Azure](https://portal.azure.com) usando un account associato alla sottoscrizione di Azure che contiene la VM. 

2. Passare alla macchina virtuale desiderata e selezionare **Identità**.

3. In **Assegnata dal sistema**, **Stato**, selezionare **Disabilita** e quindi fare clic su **Salva**:

   ![Schermata della pagina Configurazione](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)

## <a name="user-assigned-managed-identity"></a>Identità gestita assegnata dall'utente

 Questa sezione illustra come aggiungere e rimuovere un'identità gestita assegnata dall'utente da una macchina virtuale usando il portale di Azure.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-vm"></a>Assegnare un'identità assegnata dall'utente durante la creazione di una macchina virtuale

Per assegnare un'identità assegnata dall'utente a una macchina virtuale, all'account devono essere assegnati i ruoli [Collaboratore Macchina virtuale](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) e [Operatore di identità gestite](/azure/role-based-access-control/built-in-roles#managed-identity-operator). Non sono necessarie altre assegnazioni di ruoli di Azure Active Directory.

Attualmente il portale di Azure non supporta l'assegnazione di un'identità gestita assegnata dall'utente durante la creazione di una macchina virtuale. In alternativa, consultare una delle guide introduttive seguenti per creare prima una macchina virtuale e quindi proseguire con la sezione successiva per informazioni dettagliate sull'associazione di un'identità gestita assegnata dall'utente alla macchina virtuale:

- [Creare una macchina virtuale Windows con il portale di Azure](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Creare una macchina virtuale Linux con il portale di Azure](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-vm"></a>Assegnare un'identità gestita assegnata dall'utente a una VM esistente

Per assegnare un'identità assegnata dall'utente a una macchina virtuale, all'account devono essere assegnati i ruoli [Collaboratore Macchina virtuale](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) e [Operatore di identità gestite](/azure/role-based-access-control/built-in-roles#managed-identity-operator). Non sono necessarie altre assegnazioni di ruoli di Azure Active Directory.

1. Accedere al [portale di Azure](https://portal.azure.com) usando un account associato alla sottoscrizione di Azure che contiene la VM.
2. Passare alla macchina virtuale desiderata e fare clic su **Identità**, **Assegnata dall'utente** e quindi **\+Aggiungi**.

   ![Aggiungere un'identità gestita assegnata dall'utente a una macchina virtuale](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot1.png)

3. Fare clic sull'identità assegnata dall'utente da aggiungere alla macchina virtuale e quindi su **Aggiungi**.

    ![Aggiungere un'identità gestita assegnata dall'utente a una macchina virtuale](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-vm"></a>Rimuovere un'identità gestita assegnata dall'utente da una macchina virtuale

Per rimuovere un'identità assegnata dall'utente da una macchina virtuale, all'account deve essere assegnato il ruolo [Collaboratore Macchina virtuale](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor). Non sono necessarie altre assegnazioni di ruoli di Azure Active Directory.

1. Accedere al [portale di Azure](https://portal.azure.com) usando un account associato alla sottoscrizione di Azure che contiene la VM.
2. Passare alla macchina virtuale desiderata e selezionare **Identità**, **Assegnata dall'utente** e il nome dell'identità gestita assegnata dall'utente da eliminare e quindi fare clic su **Rimuovi** (scegliere **Sì** nel riquadro di conferma).

   ![Rimuovere un'identità gestita assegnata dall'utente da una macchina virtuale](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vm-screenshot.png)

## <a name="next-steps"></a>Passaggi successivi

- Tramite il portale di Azure concedere all'identità gestita della macchina virtuale l'[accesso a un'altra risorsa di Azure](howto-assign-access-portal.md).

