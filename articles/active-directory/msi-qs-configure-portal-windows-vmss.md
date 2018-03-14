---
title: "Configurare l'identità del servizio gestito in un set di scalabilità di macchine virtuali di Azure tramite il portale di Azure"
description: "Istruzioni dettagliate per la configurazione dell'identità del servizio gestito in un set di scalabilità di macchine virtuali di Azure tramite il portale di Azure."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: 4808165aa760be7e397bd3601e958419780e0004
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2018
---
# <a name="configure-an-azure-virtual-machine-scale-set-managed-service-identity-msi-using-the-azure-portal"></a>Configurare un'identità del servizio gestito in un set di scalabilità di macchine virtuali di Azure tramite il portale di Azure

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Identità del servizio gestito offre servizi di Azure con un'identità gestita automaticamente in Azure Active Directory. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

In questo articolo si apprende come abilitare e rimuovere l'identità del servizio gestito per un set di scalabilità di macchine virtuali di Azure tramite il portale di Azure.

## <a name="prerequisites"></a>prerequisiti

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="enable-msi-during-creation-of-azure-virtual-machine-scale-set"></a>Abilitare l'identità del servizio gestito durante la creazione di un set di scalabilità di macchine virtuali di Azure

Al momento della redazione del presente documento, l'abilitazione dell'identità del servizio gestito durante la creazione di un set di scalabilità di macchine virtuali nel portale di Azure non è supportata. Fare invece riferimento all'articolo seguente della guida introduttiva relativo alla creazione del set di scalabilità di macchine virtuali di Azure per creare un set di scalabilità di macchine virtuali di Azure:

- [Creare un set di scalabilità di macchine virtuali nel portale di Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-create-portal.md)  

Procedere quindi alla sezione successiva per informazioni dettagliate sull'abilitazione dell'identità del servizio gestito nel set di scalabilità di macchine virtuali.

## <a name="enable-msi-on-an-existing-azure-vmms"></a>Abilitare l'identità del servizio gestito in un set di scalabilità di macchine virtuali di Azure esistente

Se si dispone di un set di scalabilità di macchine virtuali su cui è stato originariamente eseguito il provisioning senza un'identità del servizio gestito:

1. Accedere al [portale di Azure](https://portal.azure.com) usando un account associato alla sottoscrizione di Azure che contiene il set di scalabilità di macchine virtuali.

2. Passare al set di scalabilità di macchine virtuali desiderato.

3. Fare clic sulla pagina **Configurazione**, abilitare l'identità del servizio gestito nel set di scalabilità di macchine virtuali selezionando l'opzione **Sì** in "Identità del servizio gestito" e quindi fare clic su **Salva**. Il completamento di questa operazione può richiedere circa 60 secondi:

   ![Schermata della pagina Configurazione](./media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png)  

## <a name="remove-msi-from-an-azure-virtual-machine-scale-set"></a>Rimuovere l'identità del servizio gestito da un set di scalabilità di macchine virtuali di Azure

Se si dispone di un set di scalabilità di macchine virtuali per cui non è più necessaria un'identità del servizio gestito:

1. Accedere al [portale di Azure](https://portal.azure.com) usando un account associato alla sottoscrizione di Azure che contiene il set di scalabilità di macchine virtuali. Assicurarsi anche che l'account appartenga a un ruolo che fornisce le autorizzazioni di scrittura nel set di scalabilità di macchine virtuali.

2. Passare al set di scalabilità di macchine virtuali desiderato.

3. Fare clic sulla pagina **Configurazione**, rimuovere l'identità del servizio gestito dal set di scalabilità di macchine virtuali selezionando l'opzione **No** in **Identità del servizio gestito** e quindi fare clic su **Salva**. Il completamento di questa operazione può richiedere circa 60 secondi:

   ![Schermata della pagina Configurazione](./media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)  

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica dell'Identità di servizio gestito, vedere [Panoramica dell'Identità di servizio gestito](msi-overview.md).
- Tramite il portale di Azure concedere all'identità del servizio gestito del set di scalabilità di macchine virtuali di Azure l'[accesso a un'altra risorsa di Azure](msi-howto-assign-access-portal.md).

Usare la sezione dei commenti seguente per fornire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.
