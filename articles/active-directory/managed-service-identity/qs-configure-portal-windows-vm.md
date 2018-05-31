---
title: Come configurare l'Identità del servizio gestito in una macchina virtuale di Azure tramite il portale di Azure
description: Istruzioni dettagliate per la configurazione dell'Identità del servizio gestito in una macchina virtuale di Azure, tramite il portale di Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: daveba
ms.openlocfilehash: 62b8504f5c10f338539d263bb231cf96eb405ba6
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
ms.locfileid: "33930341"
---
# <a name="configure-a-vm-managed-service-identity-msi-using-the-azure-portal"></a>Configurare un'Identità del servizio gestito della macchina virtuale tramite il portale di Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identità del servizio gestito offre servizi di Azure con un'identità gestita automaticamente in Azure Active Directory. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

Questo articolo illustra come abilitare e disabilitare l'identità assegnata dal sistema per una macchina virtuale usando il portale di Azure. L'assegnazione e la rimozione delle identità assegnate dall'utente dalle macchine virtuali di Azure non sono attualmente supportate tramite il portale di Azure.

> [!NOTE]
> Attualmente le operazioni di identità assegnate dall'utente non sono supportate tramite il portale di Azure. Ricontrollare in seguito per aggiornamenti. 

## <a name="prerequisites"></a>prerequisiti

- Se non si ha familiarità con l'identità del servizio gestita, vedere la [sezione sulla panoramica](overview.md).
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.

## <a name="managed-service-identity-during-creation-of-an-azure-vm"></a>Identità del servizio gestita durante la creazione di una macchina virtuale di Azure

Attualmente, la creazione di VM tramite il portale di Azure non supporta operazioni di identità del servizio gestita. In alternativa, prima di creare una VM, vedere uno dei seguenti articoli di guida introduttiva sulla creazione di VM:

- [Creare una macchina virtuale Windows con il portale di Azure](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Creare una macchina virtuale Linux con il portale di Azure](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

Procedere quindi alla sezione successiva per informazioni dettagliate sull'abilitazione dell'identità del servizio gestita nella VM.

## <a name="enable-managed-service-identity-on-an-existing-azure-vm"></a>Abilitare l'identità del servizio gestita in una macchina virtuale di Azure esistente

Per abilitare l'identità assegnata dal sistema in una macchina virtuale di cui è stato originariamente eseguito il provisioning senza di essa:

1. Accedere al [portale di Azure](https://portal.azure.com) usando un account associato alla sottoscrizione di Azure che contiene la VM. Assicurarsi anche che l'account appartenga a un ruolo che fornisce le autorizzazioni di scrittura nella VM, ad esempio "Collaboratore macchine virtuali".

2. Passare alla macchina virtuale desiderata e selezionare la pagina "Configurazione".

3. Abilitare l'identità assegnata dal sistema nella macchina virtuale selezionando l'opzione "Sì" in "Identità del servizio gestita", quindi fare clic su **Salva**. Il completamento di questa operazione può richiedere circa 60 secondi:

    > [!NOTE]
    > Attualmente le operazioni di aggiunta di identità assegnate dall'utente a una macchina virtuale non sono supportate tramite il portale di Azure.

   ![Schermata della pagina Configurazione](../media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

## <a name="remove-managed-service-identity-from-an-azure-vm"></a>Rimuovere l'identità del servizio gestita da una macchina virtuale di Azure

Se si dispone di una macchina virtuale per cui non è più necessaria un'identità del servizio gestita:

1. Accedere al [portale di Azure](https://portal.azure.com) usando un account associato alla sottoscrizione di Azure che contiene la VM. Assicurarsi anche che l'account appartenga a un ruolo che fornisce le autorizzazioni di scrittura nella VM, ad esempio "Collaboratore macchine virtuali".

2. Passare alla macchina virtuale desiderata e selezionare la pagina "Configurazione".

3. Disabilitare l'identità assegnata dal sistema nella macchina virtuale selezionando l'opzione "No" in "Identità del servizio gestita", quindi fare clic su Salva. Il completamento di questa operazione può richiedere circa 60 secondi:

    > [!NOTE]
    > Attualmente le operazioni di aggiunta di identità assegnate dall'utente a una macchina virtuale non sono supportate tramite il portale di Azure.

   ![Schermata della pagina Configurazione](../media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)  

## <a name="related-content"></a>Contenuti correlati

- Per una panoramica dell'identità del servizio gestita, vedere le [informazioni generali](overview.md).

## <a name="next-steps"></a>Passaggi successivi

- Tramite il portale di Azure, concedere all'Identità del servizio gestito della macchina virtuale di Azure l'[accesso a un'altra risorsa di Azure](howto-assign-access-portal.md).

