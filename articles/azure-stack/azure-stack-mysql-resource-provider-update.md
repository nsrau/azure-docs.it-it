---
title: Utilizzo di database forniti dal componente Adapter MySQL in AzureStack | Documenti Microsoft
description: Come creare e gestire i database MySQL il provisioning utilizzando il Provider di risorse MySQL Adapter
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
ms.date: 06/26/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 0a900d75315fd0015633c036877faef84c48d65b
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37031834"
---
# <a name="create-mysql-databases"></a>Creare i database MySQL

È possibile creare e gestire i database nel portale per gli utenti self-servizi. Un utente di Azure Stack deve avere una sottoscrizione con un'offerta che include il servizio di database MySQL.

## <a name="test-your-deployment-by-creating-a-mysql-database"></a>Testare la distribuzione tramite la creazione di un database MySQL

1. Accedere al portale per gli utenti dello Stack di Azure.
2. Selezionare **+ nuova** > **dati e archiviazione** > **Database MySQL** > **aggiungere**.
3. Sotto **Create MySQL Database**, immettere il nome del Database e configurare altre impostazioni come richiesto per l'ambiente.

    ![Creare un test di database MySQL](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. Sotto **Create Database**, selezionare **SKU**. Sotto **selezionare uno SKU MySQL**, selezionare l'unità SKU per il database.

    ![Selezionare uno SKU di MySQL](./media/azure-stack-mysql-rp-deploy/mysql-select-a-sku.png)

    >[!Note]
    >Come server di hosting che vengono aggiunti allo Stack di Azure, sono state assegnate uno SKU. I database vengono creati nel pool di server in un'unità SKU di hosting.

5. Sotto **account di accesso**, selezionare ***Configura le impostazioni obbligatorie***.
6. Sotto **selezionare un account di accesso**, è possibile scegliere un account di accesso o selezionare **+ crea un nuovo account di accesso** per impostare un nuovo account di accesso.  Immettere un **accesso al Database** nome e **Password**, quindi selezionare **OK**.

    ![Creare un nuovo accesso al database](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    >[!NOTE]
    >La lunghezza del nome di account di accesso del Database non può superare i 32 caratteri 5.7 MySQL. Nelle versioni precedenti, non può superare i 16 caratteri.

7. Selezionare **crea** per completare l'installazione del database.

Dopo aver distribuito il database, prendere nota del **stringa di connessione** sotto **Essentials**. È possibile utilizzare questa stringa in qualsiasi applicazione che deve accedere al database MySQL.

![Ottenere la stringa di connessione per il database MySQL](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

## <a name="update-the-administrative-password"></a>Aggiornare la password amministrativa

È possibile modificare la password può essere modificato nell'istanza del server MySQL.

1. Selezionare **risorse amministrative** > **server di Hosting MySQL**. Selezionare il server di hosting.
2. Sotto **impostazioni**, selezionare **Password**.
3. Sotto **Password**, immettere la nuova password e quindi selezionare **salvare**.

![Aggiornare la password dell'amministratore](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="next-steps"></a>Passaggi successivi

[Gestire il provider di risorse MySQL](azure-stack-mysql-resource-provider-maintain.md)