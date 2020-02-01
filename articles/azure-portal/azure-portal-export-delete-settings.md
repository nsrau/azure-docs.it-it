---
title: Esportare o eliminare le impostazioni portale di Azure
description: Informazioni su come esportare o eliminare le impostazioni utente, i dashboard privati e le impostazioni personalizzate nel portale di Azure.
services: azure-portal
keywords: ''
author: santhoshsomayajula
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-portal
ms.custom: ''
manager: mtillman
ms.author: mblythe
ms.openlocfilehash: accdfbd939fad73ca7d008450c358d366e7f8b70
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76900786"
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

1. Nell'intestazione del portale selezionare ![icona Impostazioni](media/azure-portal-export-delete-settings/settings-icon.png) **Impostazioni**.

1. Selezionare **Esporta tutte le impostazioni** o **Elimina tutte le impostazioni e i dashboard privati**.

    ![Opzioni di portale di Azure impostazioni e impostazioni](media/azure-portal-export-delete-settings/azure-portal-settings-with-export-delete.png)

      Nella tabella seguente vengono descritte queste azioni.

      | Azione | Description |
      | --- | --- |
      | **Esporta tutte le impostazioni** | Consente di creare un file con *estensione JSON* che contiene le impostazioni utente, ad esempio il tema colori, i Preferiti e i dashboard privati.|
      | **Elimina tutte le impostazioni e i dashboard privati** | Elimina tutti i collegamenti a Dashboard privati e altre impostazioni personalizzate apportate al portale. |

> [!NOTE]
> A causa della natura dinamica delle impostazioni utente e del rischio di danneggiamento dei dati, non è possibile importare le impostazioni dal file con estensione *JSON* .
>
>

## <a name="next-steps"></a>Passaggi successivi

* [Condividere i dashboard di Azure tramite il controllo degli accessi in base al ruolo](azure-portal-dashboard-share-access.md)
* [Aggiunta, rimozione e ridisposizione di Preferiti](azure-portal-add-remove-sort-favorites.md)
