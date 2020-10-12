---
title: Gestire server-portale di Azure-database di Azure per PostgreSQL-server flessibile
description: Informazioni su come gestire un server di database di Azure per PostgreSQL-flessibile dal portale di Azure.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 13d78110f50d6ce72b8525914ed0e91dfcadd2cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90938502"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-using-the-azure-portal"></a>Gestire un database di Azure per PostgreSQL-server flessibile usando il portale di Azure

> [!IMPORTANT]
> Il server flessibile di Database di Azure per PostgreSQL è disponibile in anteprima

Questo articolo illustra come gestire il database di Azure per PostgreSQL-server flessibile. Le attività di gestione includono il ridimensionamento di calcolo e archiviazione, la reimpostazione della password amministratore e la visualizzazione dei dettagli del server.

## <a name="sign-in"></a>Accesso

Accedere al [portale di Azure](https://portal.azure.com). Passare alla risorsa server flessibile nel portale di Azure.

## <a name="scale-compute-and-storage"></a>Ridimensionare le risorse di calcolo e archiviazione

Dopo la creazione del server è possibile applicare la scalabilità tra i diversi [piani tariffari](https://azure.microsoft.com/pricing/details/postgresql/) in base alle esigenze. È anche possibile aumentare o ridurre la quantità di risorse di calcolo e memoria aumentando o diminuendo vcore.

> [!NOTE]
> Non è possibile ridimensionare l'archiviazione fino a un valore inferiore.

1. Selezionare il server nella portale di Azure. Selezionare **calcolo e archiviazione**, che si trova nella sezione **Impostazioni** .
2. È possibile modificare il **livello di calcolo** , **vCore**, lo **spazio di archiviazione** per aumentare il numero di risorse del server usando un livello di calcolo superiore o aumentare il numero di risorse all'interno dello stesso livello aumentando l'archiviazione o Vcore al valore desiderato.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-manage-server-portal/scale-server.png" alt-text="ridimensionamento del server flessibile di archiviazione":::

> [!Important]
> - Non è possibile ridimensionare l'archiviazione.
> - Il ridimensionamento di Vcore causa un riavvio del server.

3. Selezionare **OK** per salvare le modifiche.

## <a name="reset-admin-password"></a>Reimposta password amministratore

È possibile modificare la password del ruolo amministratore utilizzando la portale di Azure.

1. Selezionare il server nella portale di Azure. Nella finestra **Panoramica** selezionare **Reimposta password**.
2. Immettere una nuova password e confermarla. Nella casella di testo vengono visualizzati i requisiti di complessità delle password.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-manage-server-portal/reset-password.png" alt-text="ridimensionamento del server flessibile di archiviazione":::

3. Selezionare **Save (Salva** ) per salvare la nuova password.

## <a name="delete-a-server"></a>Eliminazione di un server

Se non è più necessario, è possibile eliminare il server.

1. Selezionare il server nella portale di Azure. Nella finestra **Panoramica** selezionare **Elimina**.
2. Digitare il nome del server nella casella di input per confermare che si desidera eliminare il server.

   :::image type="content" source="./media/howto-manage-server-portal/delete-server.png" alt-text="ridimensionamento del server flessibile di archiviazione":::

   > [!IMPORTANT]
   > L'eliminazione di un server è irreversibile.

  > [!div class="mx-imgBorder"]
  > ![eliminare il server flessibile](./media/howto-manage-server-portal/delete-server.png)  

3. Selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sui concetti di backup e ripristino](concepts-backup-restore.md)
- [Ottimizzazione e monitoraggio del server](concepts-monitoring.md)
