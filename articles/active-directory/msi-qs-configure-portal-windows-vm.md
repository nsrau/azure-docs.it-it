---
title: "Come configurare l'Identità del servizio gestito in una macchina virtuale di Azure tramite il portale di Azure"
description: "Istruzioni dettagliate per la configurazione dell'Identità del servizio gestito in una macchina virtuale di Azure, tramite il portale di Azure."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: bryanla
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 9406ba2bbbea41f4677cd0d5aaddf16b0f4f26c8
ms.contentlocale: it-it
ms.lasthandoff: 09/14/2017

---

# <a name="configure-a-vm-managed-service-identity-msi-using-the-azure-portal"></a>Configurare un'Identità del servizio gestito della macchina virtuale tramite il portale di Azure

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Identità del servizio gestito offre servizi di Azure con un'identità gestita automaticamente in Azure Active Directory. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

In questo articolo si apprende come abilitare e rimuovere l'Identità del servizio gestito per una macchina virtuale Windows di Azure tramite il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Abilitare l'Identità del servizio gestito durante la creazione di una macchina virtuale di Azure

Al momento della redazione del presente documento, l'abilitazione dell'Identità del servizio gestito durante la creazione di una macchina virtuale nel portale di Azure non è supportata. Al contrario, consultare la guida rapida [Creare una macchina virtuale Windows con il portale di Azure](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine) per informazioni dettagliate sulla creazione di una macchina virtuale. Procedere quindi alla sezione successiva per informazioni dettagliate sull'abilitazione dell'Identità del servizio gestito.

## <a name="enable-msi-on-an-existing-azure-vm"></a>Abilitare l'Identità del servizio gestito in una macchina virtuale di Azure esistente

Se si dispone di una macchina virtuale su cui è stato originariamente è stato eseguito il provisioning senza un'Identità del servizio gestito:

1. Accedere al [portale di Azure](https://portal.azure.com) usando un account associato alla sottoscrizione di Azure in cui si desidera distribuire la macchina virtuale.

2. Passare alla macchina virtuale desiderata.

2. Fare clic sulla pagina "Configurazione", abilitare l'Identità del servizio gestito nella macchina virtuale selezionando l'opzione "Sì" in "Managed service identity" (Identità del servizio gestito), quindi fare clic su **Salva**. Il completamento di questa operazione può richiedere circa 60 secondi:

   ![Schermata della pagina Configurazione](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

## <a name="remove-msi-from-an-azure-vm"></a>Rimuovere l'Identità del servizio gestito da una macchina virtuale di Azure

Se si dispone di una macchina virtuale per cui non è più necessaria un'Identità del servizio gestito:

1. Accedere al [portale di Azure](https://portal.azure.com) usando un account associato alla sottoscrizione di Azure in cui si desidera distribuire la macchina virtuale.

2. Passare alla macchina virtuale desiderata.

3. Fare clic sulla pagina "Configurazione", rimuovere l'Identità del servizio gestito dalla macchina virtuale selezionando l'opzione "No" in "Managed service identity" (Identità del servizio gestito), quindi fare clic su **Salva**. Il completamento di questa operazione può richiedere circa 60 secondi:

   ![Schermata della pagina Configurazione](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)  

## <a name="related-content"></a>Contenuti correlati

- Per una panoramica dell'Identità del servizio gestito, vedere [Panoramica dell'Identità del servizio gestito](msi-overview.md).
- Questo articolo è stato adattato dalla guida rapida [Creare una macchina virtuale Windows con il portale di Azure](../virtual-machines/windows/quick-create-portal.md), modificata per includere le istruzioni specifiche dell'Identità di servizio gestito. 

## <a name="next-steps"></a>Passaggi successivi

- Tramite il portale di Azure, concedere all'Identità del servizio gestito della macchina virtuale di Azure l'[accesso a un'altra risorsa di Azure](msi-howto-assign-access-portal.md).

Usare la sezione dei commenti seguente per fornire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.

