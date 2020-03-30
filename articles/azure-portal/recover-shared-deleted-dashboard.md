---
title: Ripristinare un dashboard eliminato nel portale di Azure . Documenti Microsoft
description: Se si elimina un dashboard pubblicato nel portale di Azure, è possibile ripristinarne il.If you delete a published dashboard in the Azure portal, you can recover the dashboard.
services: azure-portal
author: mgblythe
ms.author: mblythe
ms.date: 01/21/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: af0c72f0bc5dd8f3a3cbae7b82b1ac56447cbb08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77133281"
---
# <a name="recover-a-deleted-dashboard-in-the-azure-portal"></a>Ripristinare un dashboard eliminato nel portale di AzureRecover a deleted dashboard in the Azure portal

Se ci si trova nel cloud di Azure pubblico e si elimina un dashboard _pubblicato_ nel portale di Azure, è possibile ripristinarlo entro 14 giorni dall'eliminazione. Se ci si si si è in un cloud di Azure per enti pubblici o il dashboard non è pubblicato, non è possibile ripristinarlo ed è necessario ricompilarlo. Per ulteriori informazioni sulla pubblicazione di un dashboard, vedere [Pubblicare dashboard.](azure-portal-dashboard-share-access.md#publish-dashboard) Seguire questi passaggi per ripristinare un dashboard pubblicato:Follow these steps to recover a published dashboard:

1. Nel menu del portale di Azure selezionare **Gruppi di**risorse , quindi selezionare il gruppo di risorse in cui è stato pubblicato il dashboard (per impostazione predefinita, i dashboard denominati sono **denominati**).

1. In **Registro attività**espandere l'operazione **Elimina dashboard.** Selezionare la scheda **Cronologia modifiche** , quindi selezionare ** \<\>risorsa eliminata**.

    ![Screenshot della scheda Cronologia modifiche](media/recover-shared-deleted-dashboard/change-history-tab.png)

1. Selezionare e copiare il contenuto del riquadro sinistro, quindi salvarlo in un file di testo con estensione _.json._ Il portale usa il file JSON per ricreare il dashboard.

    ![Screenshot della differenza della cronologia delle modifiche](media/recover-shared-deleted-dashboard/change-history-diff.png)

1. Nel menu del portale di Azure selezionare **Dashboard**, quindi **Carica**.

    ![Screenshot del caricamento del dashboard](media/recover-shared-deleted-dashboard/dashboard-upload.png)

1. Selezionare il file JSON salvato. Il portale ricrea il dashboard con lo stesso nome e gli stessi elementi del dashboard eliminato.

1. Selezionare **Condividi** per pubblicare il dashboard e ristabilire il controllo di accesso appropriato.

    ![Screenshot della condivisione dashboard](media/recover-shared-deleted-dashboard/dashboard-share.png)
