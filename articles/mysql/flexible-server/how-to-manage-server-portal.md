---
title: Gestire server-portale di Azure-server flessibile per database di Azure per MySQL
description: Informazioni su come gestire un server flessibile di database di Azure per MySQL dalla portale di Azure.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 7a01863b3a0c29e94550be67ca957655cff32660
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90937327"
---
# <a name="manage-an-azure-database-for-mysql---flexible-server-preview-using-azure-portal"></a>Gestire un database di Azure per MySQL-server flessibile (anteprima) con portale di Azure


> [!IMPORTANT]
> Il server flessibile di Database di Azure per MySQL è attualmente disponibile in anteprima pubblica.

Questo articolo illustra come gestire i server flessibili di database di Azure per MySQL (anteprima). Le attività di gestione includono il ridimensionamento di calcolo e archiviazione, la password dell'amministratore del server REST ed eliminare il server.

## <a name="sign-in"></a>Accesso
Accedere al [portale di Azure](https://portal.azure.com). Passare alla risorsa server flessibile nel portale di Azure.

## <a name="scale-compute-and-storage"></a>Ridimensionare le risorse di calcolo e archiviazione

Dopo la creazione del server è possibile applicare la scalabilità tra i diversi [piani tariffari](https://azure.microsoft.com/pricing/details/mysql/) in base alle esigenze. È anche possibile aumentare o ridurre la quantità di risorse di calcolo e memoria aumentando o diminuendo vcore.

1. Selezionare il server nella portale di Azure. Selezionare **calcolo e archiviazione**, che si trova nella sezione **Impostazioni** .

2. È possibile modificare il **livello di calcolo**, **vCore**, lo **spazio di archiviazione** per aumentare il numero di risorse del server usando un livello di calcolo superiore o aumentare le prestazioni nello stesso livello aumentando l'archiviazione o Vcore a un valore desiderato.

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
   > :::image type="content" source="./media/howto-manage-server-portal/reset-password.png" alt-text="Reimposta la password per il server flessibile":::

3. Selezionare **Save (Salva** ) per salvare la nuova password.

## <a name="delete-a-server"></a>Eliminazione di un server

Se non è più necessario, è possibile eliminare il server.

1. Selezionare il server nella portale di Azure. Nella finestra **Panoramica** selezionare **Elimina**.

2. Digitare il nome del server nella casella di input per confermare che si desidera eliminare il server.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/howto-manage-server-portal/delete-server.png" alt-text="eliminare il server flessibile":::

   > [!NOTE]
   > L'eliminazione di un server è irreversibile.

3. Selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi
- [Informazioni su come avviare o arrestare un server](how-to-stop-start-server-portal.md)
- [Informazioni su come ripristinare un server](how-to-restore-server-portal.md)
- [Risolvere i problemi di connessione](how-to-troubleshoot-common-connection-issues.md)

