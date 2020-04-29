---
title: includere il file
description: File di inclusione
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 03/19/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: a00291182059506aeab9cde965fa4cbd5177ecf7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80132254"
---
Se un utente non dispone dell'accesso richiesto per l'applicazione di tag, è possibile assegnare il ruolo di **collaboratore Tag** all'utente. Per altre informazioni, vedere [esercitazione: concedere a un utente l'accesso alle risorse di Azure usando RBAC e il portale di Azure](../articles/role-based-access-control/quickstart-assign-role-user-portal.md).

1. Per visualizzare i tag per una risorsa o un gruppo di risorse, cercare i tag esistenti nella panoramica. Se in precedenza non sono stati applicati tag, l'elenco è vuoto.

   ![Visualizzare i tag per la risorsa o il gruppo di risorse](./media/resource-manager-tag-resources/view-tags.png)

1. Per aggiungere un tag, selezionare **Fare clic qui per aggiungere i tag**.

1. Specificare un nome e valore.

   ![Aggiungere un tag](./media/resource-manager-tag-resources/add-tag.png)

1. Continuare ad aggiungere i tag in base alle esigenze. Al termine scegliere **Salva**.

   ![Salvare i tag](./media/resource-manager-tag-resources/save-tags.png)

1. I tag vengono ora visualizzati nella panoramica.

   ![Visualizzare i tag](./media/resource-manager-tag-resources/view-new-tags.png)

1. Per aggiungere o eliminare un tag, selezionare **Modifica**.

1. Per eliminare un tag, selezionare l'icona Cestino. Selezionare quindi **Salva**.

   ![Eliminare un tag](./media/resource-manager-tag-resources/delete-tag.png)

Per eseguire l'assegnazione in blocco dei tag a più risorse:

1. Da qualsiasi elenco di risorse selezionare la casella di controllo per le risorse a cui si vuole assegnare il tag. Selezionare quindi **Assegna tag**.

   ![Selezionare più risorse](./media/resource-manager-tag-resources/select-multiple-resources.png)

1. Aggiungere nomi e valori. Al termine scegliere **Salva**.

   ![Selezionare Assegna](./media/resource-manager-tag-resources/select-assign.png)

Per visualizzare tutte le risorse con un tag:

1. Nel menu portale di Azure cercare i **tag**. Selezionarlo dalle opzioni disponibili.

   ![Ricerca in base al tag](./media/resource-manager-tag-resources/find-tags-general.png)

1. Selezionare il tag per la visualizzazione delle risorse.

   ![Selezionare il tag](./media/resource-manager-tag-resources/select-tag.png)

1. Vengono visualizzate tutte le risorse con tale tag.

   ![Visualizzare le risorse in base al tag](./media/resource-manager-tag-resources/view-resources-by-tag.png)
