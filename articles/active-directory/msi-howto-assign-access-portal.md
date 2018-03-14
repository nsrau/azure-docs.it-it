---
title: "Come assegnare all'Identità del servizio gestito l'accesso a una risorsa di Azure tramite il portale di Azure"
description: "Istruzioni dettagliate per assegnare a un'Identità del servizio gestito in una risorsa l'accesso a un'altra risorsa tramite il portale di Azure."
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
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: 6522a5ef7f1e1af89ee70ee74041e1e69614ebca
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2018
---
# <a name="assign-a-managed-service-identity-access-to-a-resource-by-using-the-azure-portal"></a>Assegnare a un'Identità del servizio gestito l'accesso a una risorsa tramite il portale di Azure

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Dopo aver configurato una risorsa di Azure con un'Identità del servizio gestito (MSI), è possibile concedere all'identità del servizio gestito l'accesso a un'altra risorsa, analogamente a qualsiasi entità di sicurezza. Questo articolo illustra come concedere a un'identità del servizio gestito della macchina virtuale di Azure o di un set di scalabilità di macchine virtuali di Azure l'accesso a un account di archiviazione di Azure tramite il portale di Azure.

## <a name="prerequisites"></a>prerequisiti

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Usare il controllo degli accessi in base al ruolo per assegnare a un'Identità del servizio gestito l'accesso a un'altra risorsa

Dopo aver abilitato l'identità del servizio gestito in una risorsa di Azure, ad esempio in una [macchina virtuale di Azure](msi-qs-configure-portal-windows-vm.md) o in un [set di scalabilità di macchine virtuali di Azure](msi-qs-configure-portal-windows-vmss.md):

1. Accedere al [portale di Azure](https://portal.azure.com) usando un account associato alla sottoscrizione di Azure in cui si desidera configurare l'Identità del servizio gestito.

2. Passare alla risorsa desiderata in cui si desidera modificare il controllo di accesso. In questo esempio viene concesso alla macchina virtuale di Azure e al set di scalabilità di macchine virtuali di Azure l'accesso a un account di archiviazione e quindi si passa all'account di archiviazione.

3. Per una macchina virtuale di Azure selezionare la pagina **Controllo di accesso (IAM)** della risorsa e quindi **+ Aggiungi**. Specificare quindi il **ruolo**, **assegnare l'accesso a una macchina virtuale** e specificare il **gruppo di risorse** e la **sottoscrizione** corrispondente in cui risiede la risorsa. Nell'area per i criteri di ricerca viene visualizzata la risorsa. Selezionare la risorsa e premere **Salva**. 

   ![Screenshot della pagina Controllo di accesso (IAM)](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  
   Per un set di scalabilità di macchine virtuali di Azure selezionare la pagina **Controllo di accesso (IAM)** della risorsa e quindi **+ Aggiungi**. Specificare un **ruolo** e **assegnare l'accesso**. Nell'area dei criteri di ricerca cercare il set di scalabilità di macchine virtuali. Selezionare la risorsa e premere **Salva**.
   
   ![Schermata del Controllo di accesso (IAM)](./media/msi-howto-assign-access-vmss-portal/assign-access-control-vmss-iam-blade-before.png)  

4. Si apre di nuovo la pagina principale **Controllo di accesso (IAM)** in cui viene visualizzata una nuova voce per l'Identità del servizio gestito della risorsa.

    Macchina virtuale di Azure:

   ![Schermata del Controllo di accesso (IAM)](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-after.png)

    Set di scalabilità di macchine virtuali di Azure:

    ![Schermata del Controllo di accesso (IAM)](./media/msi-howto-assign-access-vmss-portal/assign-access-control-vmss-iam-blade-after.png)

## <a name="troubleshooting"></a>risoluzione dei problemi

Se l'Identità del servizio gestito per la risorsa non viene visualizzata nell'elenco delle identità disponibili, verificare che sia stata abilitata correttamente. In questo caso è possibile tornare alla macchina virtuale di Azure e controllare quanto segue:

- Si esamina la pagina **Configurazione** e si verifica che il valore di **MSI enabled** (Identità del servizio gestito attivata) sia **Sì**.
- Nella pagina **Estensioni** assicurarsi che l'identità del servizio gestito sia stata distribuita correttamente (la pagina **Estensioni** non è disponibile per un set di scalabilità di macchine virtuali di Azure).

Se una delle due opzioni è errata, potrebbe essere necessario ridistribuire l'Identità del servizio gestito nella risorsa o risolvere il problema di distribuzione.

## <a name="related-content"></a>Contenuti correlati

- Per una panoramica dell'Identità di servizio gestito, vedere [Panoramica dell'Identità di servizio gestito](msi-overview.md).
- Per abilitare l'identità del servizio gestito in una macchina virtuale di Azure, vedere [Configurare un'identità del servizio gestito della macchina virtuale di Azure mediante il portale di Azure](msi-qs-configure-portal-windows-vm.md).
- Per abilitare l'identità del servizio gestito in un set di scalabilità di macchine virtuali di Azure, vedere [Configurare un'identità del servizio gestito in un set di scalabilità di macchine virtuali di Azure tramite il portale di Azure](msi-qs-configure-portal-windows-vmss.md)


