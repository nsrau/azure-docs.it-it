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
ms.openlocfilehash: 6ba090065b18a44cc1f01a62eefb5dcf52bcf356
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213266"
---
# <a name="configure-a-vm-managed-service-identity-using-the-azure-portal"></a>Configurare un'identità del servizio gestita della macchina virtuale tramite il portale di Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identità del servizio gestito offre servizi di Azure con un'identità gestita automaticamente in Azure Active Directory. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

Questo articolo illustra come abilitare e disabilitare l'identità assegnata dal sistema per una macchina virtuale usando il portale di Azure. L'assegnazione e la rimozione delle identità assegnate dall'utente dalle macchine virtuali di Azure non sono attualmente supportate tramite il portale di Azure.

> [!NOTE]
> Attualmente le operazioni di identità assegnate dall'utente non sono supportate tramite il portale di Azure. Ricontrollare in seguito per aggiornamenti. 

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha familiarità con l'identità del servizio gestita, vedere la [sezione sulla panoramica](overview.md).
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.
- Per eseguire le operazioni di gestione in questo articolo, l'account deve avere l'assegnazione di ruolo seguente:
    - [Collaboratore macchina virtuale](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) per abilitare e rimuovere l'identità gestita assegnata dal sistema da una macchina virtuale di Azure.

## <a name="managed-service-identity-during-creation-of-an-azure-vm"></a>Identità del servizio gestita durante la creazione di una macchina virtuale di Azure

Attualmente, la creazione di VM tramite il portale di Azure non supporta operazioni di identità del servizio gestita. In alternativa, prima di creare una VM, vedere uno dei seguenti articoli di guida introduttiva sulla creazione di VM:

- [Creare una macchina virtuale Windows con il portale di Azure](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Creare una macchina virtuale Linux con il portale di Azure](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

Procedere quindi alla sezione successiva per informazioni dettagliate sull'abilitazione dell'identità del servizio gestita nella VM.

## <a name="enable-managed-service-identity-on-an-existing-azure-vm"></a>Abilitare l'identità del servizio gestita in una macchina virtuale di Azure esistente

Per abilitare l'identità assegnata dal sistema in una macchina virtuale di cui è stato originariamente eseguito il provisioning senza di essa:

1. Accedere al [portale di Azure](https://portal.azure.com) usando un account associato alla sottoscrizione di Azure che contiene la VM.

2. Passare alla macchina virtuale desiderata e selezionare la pagina "Configurazione".

3. Abilitare l'identità assegnata dal sistema nella macchina virtuale selezionando l'opzione "Sì" in "Identità del servizio gestita", quindi fare clic su **Salva**. Il completamento di questa operazione può richiedere circa 60 secondi:

   > [!NOTE]
   > Attualmente le operazioni di aggiunta di identità assegnate dall'utente a una macchina virtuale non sono supportate tramite il portale di Azure.

   ![Schermata della pagina Configurazione](../managed-service-identity/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

## <a name="remove-managed-service-identity-from-an-azure-vm"></a>Rimuovere l'identità del servizio gestita da una macchina virtuale di Azure

Se si dispone di una macchina virtuale per cui non è più necessaria un'identità del servizio gestita:

1. Accedere al [portale di Azure](https://portal.azure.com) usando un account associato alla sottoscrizione di Azure che contiene la VM. 

2. Passare alla macchina virtuale desiderata e selezionare la pagina "Configurazione".

3. Disabilitare l'identità assegnata dal sistema nella macchina virtuale selezionando l'opzione "No" in "Identità del servizio gestita", quindi fare clic su Salva. Il completamento di questa operazione può richiedere circa 60 secondi:

    > [!NOTE]
    > Attualmente le operazioni di aggiunta di identità assegnate dall'utente a una macchina virtuale non sono supportate tramite il portale di Azure.

   ![Schermata della pagina Configurazione](../managed-service-identity/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)  

## <a name="related-content"></a>Contenuti correlati

- Per una panoramica dell'identità del servizio gestita, vedere le [informazioni generali](overview.md).

## <a name="next-steps"></a>Passaggi successivi

- Tramite il portale di Azure concedere all'identità del servizio gestita del set di scalabilità di macchine virtuali l'[accesso a un'altra risorsa di Azure](howto-assign-access-portal.md).

