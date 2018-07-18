---
title: Configurare l'identità del servizio gestito in un set di scalabilità di macchine virtuali di Azure tramite il portale di Azure
description: Istruzioni dettagliate per la configurazione dell'identità del servizio gestito in un set di scalabilità di macchine virtuali di Azure tramite il portale di Azure.
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
ms.openlocfilehash: 1ba9f827abeb0c0cf6430089e1fb504288550737
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2018
ms.locfileid: "37900464"
---
# <a name="configure-a-virtual-machine-scale-set-managed-service-identity-msi-using-the-azure-portal"></a>Configurare un'identità del servizio gestita (MDI) in un set di scalabilità di macchine virtuali tramite il portale di Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identità del servizio gestito offre servizi di Azure con un'identità gestita automaticamente in Azure Active Directory. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

Questo articolo illustra come abilitare e disabilitare l'identità assegnata dal sistema per un set di scalabilità di macchine virtuali usando il portale di Azure. L'assegnazione e la rimozione delle identità assegnate dall'utente da un set di scalabilità di macchine virtuali di Azure non sono attualmente supportate tramite il portale di Azure.

> [!NOTE]
> Attualmente le operazioni di identità assegnate dall'utente non sono supportate tramite il portale di Azure. Ricontrollare in seguito per aggiornamenti.

## <a name="prerequisites"></a>prerequisiti


- Se non si ha familiarità con l'identità del servizio gestita, vedere la [sezione sulla panoramica](overview.md).
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.

## <a name="managed-service-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Abilitare l'identità del servizio gestita durante la creazione di un set di scalabilità di macchine virtuali di Azure

Attualmente, la creazione di VM tramite il portale di Azure non supporta operazioni di identità del servizio gestita. Fare invece riferimento all'articolo seguente della guida introduttiva relativo alla creazione del set di scalabilità di macchine virtuali di Azure per creare un set di scalabilità di macchine virtuali di Azure:

- [Creare un set di scalabilità di macchine virtuali nel portale di Azure](../../virtual-machine-scale-sets/quick-create-portal.md)  

Procedere quindi alla sezione successiva per informazioni dettagliate sull'abilitazione dell'identità del servizio gestito nel set di scalabilità di macchine virtuali.

## <a name="enable-managed-service-identity-on-an-existing-azure-vmms"></a>Abilitare l'identità del servizio gestita in un set di scalabilità di macchine virtuali di Azure esistente

Per abilitare l'identità assegnata dal sistema in una macchina virtuale di cui è stato originariamente eseguito il provisioning senza di essa:

1. Accedere al [portale di Azure](https://portal.azure.com) usando un account associato alla sottoscrizione di Azure che contiene il set di scalabilità di macchine virtuali.

2. Passare al set di scalabilità di macchine virtuali desiderato.

3. Abilitare l'identità assegnata dal sistema nella macchina virtuale selezionando l'opzione "Sì" in "Identità del servizio gestita", quindi fare clic su **Salva**. Il completamento di questa operazione può richiedere circa 60 secondi:

   [![Screenshot della pagina Configurazione](../media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png)](../media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png#lightbox)  

## <a name="remove-managed-service-identity-from-an-azure-virtual-machine-scale-set"></a>Rimuovere l'identità del servizio gestita da un set di scalabilità di macchine virtuali di Azure

Se si dispone di un set di scalabilità di macchine virtuali per cui non è più necessaria un'identità del servizio gestito:

1. Accedere al [portale di Azure](https://portal.azure.com) usando un account associato alla sottoscrizione di Azure che contiene il set di scalabilità di macchine virtuali. Assicurarsi anche che l'account appartenga a un ruolo che fornisce le autorizzazioni di scrittura nel set di scalabilità di macchine virtuali.

2. Passare al set di scalabilità di macchine virtuali desiderato.

3. Disabilitare l'identità assegnata dal sistema nella macchina virtuale selezionando l'opzione "No" in "Identità del servizio gestita", quindi fare clic su Salva. Il completamento di questa operazione può richiedere circa 60 secondi:

   ![Schermata della pagina Configurazione](../media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)  

## <a name="related-content"></a>Contenuti correlati

- Per una panoramica dell'identità del servizio gestita, vedere le [informazioni generali](overview.md).

## <a name="next-steps"></a>Passaggi successivi

- Tramite il portale di Azure concedere all'identità del servizio gestito del set di scalabilità di macchine virtuali di Azure l'[accesso a un'altra risorsa di Azure](howto-assign-access-portal.md).

Usare la sezione dei commenti seguente per fornire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.
