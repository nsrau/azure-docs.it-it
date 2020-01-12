---
title: Crittografia dei dati per database di Azure per MySQL con il portale
description: Informazioni su come configurare e gestire la crittografia dei dati per il database di Azure per MySQL usando portale di Azure
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/10/2020
ms.openlocfilehash: 10af869a631b620c2c75aa69722dc03df15f8539
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75903847"
---
# <a name="data-encryption-for-azure-database-for-mysql-server-using-azure-portal"></a>Crittografia dei dati per il server di database di Azure per MySQL con portale di Azure

In questo articolo si apprenderà come configurare e gestire per usare la portale di Azure per configurare la crittografia dei dati per il database di Azure per MySQL.

## <a name="prerequisites-for-powershell"></a>Prerequisiti di PowerShell

* È necessario disporre di una sottoscrizione di Azure e avere il ruolo di amministratore di tale sottoscrizione.
* È necessario che Azure PowerShell sia installato e in esecuzione.
* Creare una Azure Key Vault e una chiave da usare per la chiave gestita dal cliente.
* Il Key Vault deve avere la proprietà seguente da usare come chiave gestita dal cliente
    * [eliminazione temporanea](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)

        ```azurecli-interactive
        az resource update --id $(az keyvault show --name \ <key_valut_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
        ```
    
    * [Ripulitura protetta](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection)

        ```azurecli-interactive
        az keyvault update --name <key_valut_name> --resource-group <resource_group_name>  --enable-purge-protection true
        ```
* La chiave deve avere gli attributi seguenti da usare per la chiave gestita dal cliente.
    * Nessuna data di scadenza
    * Non disabilitato
    * In grado di eseguire operazioni get, wrap Key e unwrap Key

## <a name="setting-the-right-permissions-for-key-operations"></a>Impostazione delle autorizzazioni appropriate per le operazioni principali

1. Nella Azure Key Vault selezionare i criteri di **accesso** e aggiungere i **criteri di accesso** 

   ![Panoramica dei criteri di accesso](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. Selezionare le **autorizzazioni chiave** selezionare **Get**, **Wrap**, **Unwrap** e l' **entità** che rappresenta il nome del server MySQL.

   ![Panoramica dei criteri di accesso](media/concepts-data-access-and-security-data-encryption/access-policy-warp-unwrap.png)

3. Fare clic su **Save** (Salva) per salvare le impostazioni.

## <a name="setting-data-encryption-for-azure-database-for-mysql"></a>Impostazione della crittografia dei dati per il database di Azure per MySQL

1. Nel **database di Azure per MySQL**selezionare la **crittografia dei dati** per impostare la configurazione della chiave gestita dal cliente.

   ![Impostazione della crittografia dei dati](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. È possibile selezionare un **Key Vault** e una coppia di **chiavi** oppure passare un **identificatore di chiave**.

   ![Impostazione Key Vault](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. Fare clic su **Save** (Salva) per salvare le impostazioni.

4. Per assicurarsi che tutti i file, inclusi i file temporanei, siano crittografati con crittografia completa, è necessario riavviare il server.

## <a name="restoring-or-creating-replica-of-the-server-which-has-data-encryption-enabled"></a>Ripristino o creazione della replica del server in cui è abilitata la crittografia dei dati

Una volta che un database di Azure per MySQL è stato crittografato con la chiave gestita del cliente archiviata nel Key Vault, qualsiasi copia appena creata del server è un'operazione locale o di ripristino geografico oppure un'operazione di replica (locale/tra aree). Per un server MySQL crittografato, è quindi possibile seguire questa procedura per creare un server ripristinato crittografato.

1. Nel server selezionare **Panoramica**, quindi fare clic su **Ripristina**.

   ![Avvio-ripristino](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   In alternativa, per un server con replica abilitata, sotto l'intestazione **Impostazioni** selezionare **replica**

   ![Avvio-replica](media/concepts-data-access-and-security-data-encryption/mysql-replica.png)

2. Al termine dell'operazione di ripristino, il nuovo server creato verrà crittografato con la chiave utilizzata per crittografare il server primario. Tuttavia, le funzionalità e le opzioni del server sono disabilitate e il server è contrassegnato in uno stato **inaccessibile** . Ciò consente di evitare la manipolazione dei dati perché l'identità del nuovo server non è ancora stata autorizzata ad accedere al Key Vault.

   ![Contrassegno server inaccessibile](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. Per correggere lo stato inaccessibile, è necessario rivalidare la chiave nel server ripristinato.

   ![riconvalida server](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   Sarà necessario concedere l'accesso al nuovo server al Key Vault. 

4. Una volta rivalidata la chiave, il server riprende le funzionalità normali.

   ![Server normale ripristinato](media/concepts-data-access-and-security-data-encryption/restore-successful.png)


## <a name="next-steps"></a>Passaggi successivi

 Per altre informazioni sulla crittografia dei dati, vedere [che cos'è la crittografia dei dati di Azure](concepts-data-encryption-mysql.md).

