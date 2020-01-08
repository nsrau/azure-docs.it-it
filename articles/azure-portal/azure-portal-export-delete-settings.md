---
title: Esportare o eliminare le impostazioni del portale di Azure | Microsoft Docs
description: Informazioni su come esportare o eliminare le impostazioni utente, i dashboard privati e le impostazioni personalizzate nel portale di Azure.
services: azure-portal
keywords: ''
author: santhoshsomayajula
ms.date: 04/08/2019
ms.topic: conceptual
ms.service: azure-portal
ms.custom: ''
manager: mtillman
ms.author: mblythe
ms.openlocfilehash: f033af37985077f4d8df9d541b55764df0c75eda
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75640177"
---
# <a name="export-or-delete-user-settings"></a>Esportare o eliminare le impostazioni utente

È possibile utilizzare le impostazioni e le funzionalità nel portale di Azure per creare un'esperienza personalizzata. Le informazioni sulle impostazioni personalizzate vengono archiviate in Azure. È possibile esportare o eliminare i dati utente seguenti:

* Dashboard privati nell'portale di Azure
* Impostazioni utente come le sottoscrizioni o le directory preferite e l'ultima directory di accesso
* Temi e altre impostazioni personalizzate del portale

È consigliabile esportare ed esaminare le impostazioni prima di eliminarle. La ricompilazione di dashboard o la reimpostazione delle impostazioni personalizzate può richiedere molto tempo.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="export-or-delete-your-portal-settings"></a>Esportare o eliminare le impostazioni del portale

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nell'intestazione del portale selezionare **Impostazioni**.

    ![Screenshot che mostra l'ingranaggio per le impostazioni del portale](media/azure-portal-export-delete-settings/azure-portal-settings-icon.png)

3. Selezionare **Esporta tutte le impostazioni** o **Elimina tutte le impostazioni e i dashboard privati**.

    ![Screenshot che mostra le impostazioni di esportazione ed eliminazione del portale](media/azure-portal-export-delete-settings/azure-portal-export-delete-settings.png)

      Nella tabella seguente vengono descritte queste azioni.

      | Azione | Description |
      | --- | --- |
      | **Esporta tutte le impostazioni** | Consente di creare un file con estensione JSON che contiene le impostazioni utente, ad esempio il tema colori, i Preferiti e i dashboard privati.|
      | **Elimina tutte le impostazioni e i dashboard privati** | Elimina tutti i collegamenti a Dashboard privati e altre impostazioni personalizzate apportate al portale. |

> [!NOTE]
> A causa della natura dinamica delle impostazioni utente e del rischio di danneggiamento dei dati, non è possibile importare le impostazioni dal file con estensione JSON.
>
>


## <a name="next-steps"></a>Passaggi successivi

* [Creare e condividere dashboard di Azure](azure-portal-dashboard-share-access.md)
* [Aggiunta, rimozione e ordinamento di Preferiti](azure-portal-add-remove-sort-favorites.md)
