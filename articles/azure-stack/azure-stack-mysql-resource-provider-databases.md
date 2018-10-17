---
title: Uso di database forniti da Adapter MySQL relying Party in AzureStack | Microsoft Docs
description: Come creare e gestire i database MySQL, il provisioning utilizzando il Provider di risorse MySQL Adapter
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: 78c4e7c6cdc8cf118a351d9030416318e51c69af
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49365102"
---
# <a name="create-mysql-databases"></a>Creare i database MySQL

È possibile creare e gestire i database nel portale per gli utenti self-servizi. Un utente di Azure Stack deve avere una sottoscrizione con un'offerta che include il servizio di database MySQL.

## <a name="test-your-deployment-by-creating-a-mysql-database"></a>Testare la distribuzione tramite la creazione di un database MySQL

1. Accedere al portale utenti di Azure Stack.
2. Selezionare **+ crea una risorsa** > **Data + Storage** > **MySQL Database** > **Add**.
3. Sotto **Create MySQL Database**, immettere il nome del Database e configurare altre impostazioni come richiesto per l'ambiente.

    ![Creare un test di database MySQL](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. Sotto **Create Database**, selezionare **SKU**. Sotto **selezionare uno SKU MySQL**, selezionare lo SKU per il database.

    ![Selezionare uno SKU MySQL](./media/azure-stack-mysql-rp-deploy/mysql-select-sku.png)

    >[!Note]
    >Come server di hosting vengono aggiunti ad Azure Stack, che vengano loro assegnate uno SKU. I database vengono creati nel pool di server in uno SKU di hosting.

5. Sotto **account di accesso**, selezionare ***configurare le impostazioni necessarie***.
6. Sotto **selezionare un account di accesso**, è possibile scegliere un account di accesso esistente o selezionare **+ crea un nuovo account di accesso** per configurare un nuovo account di accesso.  Immettere un **accesso al Database** nome e **Password**, quindi selezionare **OK**.

    ![Creare un nuovo accesso al database](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    >[!NOTE]
    >La lunghezza del nome di account di accesso del Database non può superare i 32 caratteri MySQL 5.7. Nelle versioni precedenti, non può superare 16 caratteri.

7. Selezionare **Create** per completare l'installazione del database.

Dopo aver distribuito il database, prendere nota del **stringa di connessione** sotto **Essentials**. È possibile usare questa stringa in qualsiasi applicazione che richiede l'accesso al database MySQL.

![Ottenere la stringa di connessione per il database MySQL](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

## <a name="update-the-administrative-password"></a>Aggiornare la password amministrativa

È possibile modificare la password modificandolo nell'istanza del server MySQL.

1. Selezionare **risorse amministrative** > **server di Hosting MySQL**. Selezionare il server di hosting.
2. Sotto **le impostazioni**, selezionare **Password**.
3. Sotto **Password**, immettere la nuova password e quindi selezionare **salvare**.

![Aggiornare la password dell'amministratore](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="next-steps"></a>Passaggi successivi

[Aggiornare il provider di risorse MySQL](azure-stack-mysql-resource-provider-update.md)
