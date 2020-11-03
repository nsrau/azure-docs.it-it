---
title: Identità gestita nell'area di lavoro di Synapse
description: Articolo che illustra la funzionalità dell'identità gestita nell'area di lavoro di Azure Synapse
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 10/16/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 4100640fd619d9d971ac1c7083eedef8e2125fc3
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92738271"
---
# <a name="azure-synapse-workspace-managed-identity-preview"></a>Identità gestita dell'area di lavoro di Azure Synapse (anteprima)

Questo articolo illustra la funzionalità dell'identità gestita nell'area di lavoro di Azure Synapse.

## <a name="managed-identities"></a>Identità gestite

L'identità gestita per le risorse di Azure è una funzionalità di Azure Active Directory. La funzionalità offre ai servizi di Azure un'identità gestita automaticamente in Azure AD. È possibile usare la funzionalità di identità gestita per eseguire l'autenticazione a qualsiasi servizio che supporti l'autenticazione Azure AD.

Le identità gestite per le risorse di Azure corrispondono al servizio noto in precedenza come identità del servizio gestita. Per altre informazioni, vedere [Identità gestite](../../active-directory/managed-identities-azure-resources/overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="azure-synapse-workspace-managed-identity"></a>Identità gestita dell'area di lavoro di Azure Synapse

Quando si crea l'area di lavoro, viene creata un'identità gestita assegnata dal sistema per l'area di lavoro di Azure Synapse.

>[!NOTE]
>A questa identità gestita dell'area di lavoro verrà fatto riferimento come identità gestita nel resto di questo documento.

Azure Synapse usa l'identità gestita per integrare le pipeline. Il ciclo di vita dell'identità gestita è direttamente collegato all'area di lavoro di Azure Synapse. Se si elimina l'area di lavoro di Azure Synapse, viene cancellata anche l'identità gestita.

L'identità gestita dell'area di lavoro richiede autorizzazioni per eseguire operazioni nelle pipeline. È possibile usare l'ID oggetto o il nome dell'area di lavoro di Azure Synapse per trovare l'identità gestita quando si concedono le autorizzazioni.

## <a name="retrieve-managed-identity-in-azure-portal"></a>Recuperare l'identità gestita nel portale di Azure

È possibile recuperare l'identità gestita nel portale di Azure. Aprire l'area di lavoro di Azure Synapse nel portale di Azure e selezionare **Panoramica** nel riquadro di spostamento a sinistra. L'ID oggetto dell'identità gestita viene visualizzato nella schermata principale.

![ID oggetto dell'identità gestita](./media/synapse-workspace-managed-identity/workspace-managed-identity-1.png)

Le informazioni sull'identità gestita vengono visualizzate anche quando si crea un servizio collegato che supporta l'autenticazione dell'identità gestita da Azure Synapse Studio.

Avviare **Azure Synapse Studio** e selezionare la scheda **Gestisci** nel riquadro di spostamento sinistro. Selezionare quindi **Servizi collegati** e scegliere l'opzione **+ Nuovo** per creare un nuovo servizio collegato.

![Creazione del servizio collegato 1](./media/synapse-workspace-managed-identity/workspace-managed-identity-2.png)

Nella finestra **New linked service** (Nuovo servizio collegato) digitare *Azure Data Lake Storage Gen2*. Selezionare il tipo di risorsa **Azure Data Lake Storage Gen2** nell'elenco seguente e scegliere **Continua**.

![Creazione del servizio collegato 2](./media/synapse-workspace-managed-identity/workspace-managed-identity-3.png)

Nella finestra successiva scegliere **Identità gestita** in **Metodo di autenticazione**. Verranno visualizzati il **nome** e l' **ID oggetto** dell'identità gestita.

![Creazione del servizio collegato 3](./media/synapse-workspace-managed-identity/workspace-managed-identity-4.png)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [Concessione delle autorizzazioni all'identità gestita dell'area di lavoro di Azure Synapse](./how-to-grant-workspace-managed-identity-permissions.md)
