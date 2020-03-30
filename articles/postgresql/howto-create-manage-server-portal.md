---
title: Gestire il database di Azure per PostgreSQL - Portale di AzureManage Azure Database for PostgreSQL - Azure portal
description: Informazioni su come gestire un database di Azure per PostgreSQL server dal portale di Azure.Learn how to manage an Azure Database for PostgreSQL server from the Azure portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/20/2019
ms.openlocfilehash: bcddd64afca29ac9fdd5d284fc8f809ff9e2477d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74535691"
---
# <a name="manage-an-azure-database-for-postgresql-server-using-the-azure-portal"></a>Gestire un database di Azure per il server PostgreSQL usando il portale di AzureManage an Azure Database for PostgreSQL server using the Azure portal
Questo articolo illustra come gestire il database di Azure per i server PostgreSQL.This article shows you how to manage your Azure Database for PostgreSQL servers. Le attività di gestione includono il ridimensionamento dell'archiviazione e del calcolo, la reimpostazione della password di amministratore e la visualizzazione dei dettagli del server.

## <a name="sign-in"></a>Accesso
Accedere al [portale](https://portal.azure.com)di Azure .

## <a name="create-a-server"></a>Creare un server
Visitare la [guida introduttiva](quickstart-create-server-database-portal.md) per informazioni su come creare e iniziare a usare un database di Azure per PostgreSQL server.

## <a name="scale-compute-and-storage"></a>Elaborazione e archiviazione della scalabilità

Dopo la creazione del server è possibile scalare tra i livelli Scopo generale e Ottimizzato per la memoria in base alle esigenze. È anche possibile ridimensionare il calcolo e la memoria aumentando o diminuendo i vCore. L'archiviazione può essere ridotta (tuttavia, non è possibile ridurre lo spazio di archiviazione).

### <a name="scale-between-general-purpose-and-memory-optimized-tiers"></a>Scalabilità tra i livelli Scopo generale e Supportato dalla memoria

È possibile scalare da Scopo generale a Ottimizzazione memoria e viceversa. La modifica da e verso il livello Basic dopo la creazione del server non è supportata. 

1. Selezionare il server nel portale di Azure.Select your server in the Azure portal. Selezionare **Piano prezzi**, nella sezione **Impostazioni.**

2. Selezionare **Uso generale** o **Ottimizzato per**la memoria , a seconda dell'impostazione con cui si esegue il ridimensionamento. 

    ![change-pricing-tier](./media/howto-create-manage-server-portal/change-pricing-tier.png)

    > [!NOTE]
    > La modifica dei livelli causa il riavvio del server.

4. Selezionare **OK** per salvare le modifiche.


### <a name="scale-vcores-up-or-down"></a>Scalabilità vCore su o giù

1. Selezionare il server nel portale di Azure.Select your server in the Azure portal. Selezionare **Piano prezzi**, nella sezione **Impostazioni.**

2. Modificare l'impostazione di **vCore** spostando il dispositivo di scorrimento sul valore desiderato.

    ![scale-compute](./media/howto-create-manage-server-portal/scaling-compute.png)

    > [!NOTE]
    > La scalabilità di vCore sciolfa dal riavvio del server.

3. Selezionare **OK** per salvare le modifiche.


### <a name="scale-storage-up"></a>Scalabilità verticale dello spazio di archiviazione

1. Selezionare il server nel portale di Azure.Select your server in the Azure portal. Selezionare **Piano prezzi**, nella sezione **Impostazioni.**

2. Modificare l'impostazione **Archiviazione** spostando il dispositivo di scorrimento verso l'alto fino al valore desiderato.

    ![scale-storage](./media/howto-create-manage-server-portal/scaling-storage.png)

    > [!NOTE]
    > Lo spazio di archiviazione non può essere ridimensionato.

3. Selezionare **OK** per salvare le modifiche.


## <a name="update-admin-password"></a>Aggiornare la password dell'amministratore
È possibile modificare la password del ruolo di amministratore usando il portale di Azure.You can change the administrator role's password using the Azure portal.

1. Selezionare il server nel portale di Azure.Select your server in the Azure portal. Nella finestra **Panoramica** selezionare **Reimposta password**.

   ![cenni preliminari](./media/howto-create-manage-server-portal/overview-reset-password.png)

2. Immettere una nuova password e confermarla. Nella casella di testo verranno richiesti i requisiti di complessità delle password.

   ![reset-password](./media/howto-create-manage-server-portal/reset-password.png)

3. Selezionare **OK** per salvare la nuova password.


## <a name="delete-a-server"></a>Eliminazione di un server

È possibile eliminare il server se non è più necessario. 

1. Selezionare il server nel portale di Azure.Select your server in the Azure portal. Nella finestra **Panoramica** selezionare **Elimina**.

    ![delete](./media/howto-create-manage-server-portal/overview-delete.png)

2. Digitare il nome del server nella casella di input per verificare che si tratti del server che si desidera eliminare.

    ![confirm-delete](./media/howto-create-manage-server-portal/confirm-delete.png)

    > [!NOTE]
    > L'eliminazione di un server è irreversibile.

3. Selezionare **Elimina**.


## <a name="next-steps"></a>Passaggi successivi
- Informazioni sui backup e sul [ripristino del server](howto-restore-server-portal.md)
- Informazioni sulle opzioni di ottimizzazione e monitoraggio nel database di [Azure per PostgreSQLLearn](concepts-monitoring.md) about tuning and monitoring options in Azure Database for PostgreSQL
