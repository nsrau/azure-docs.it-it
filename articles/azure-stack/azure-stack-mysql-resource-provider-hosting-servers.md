---
title: MySQL che ospita i server in Azure Stack | Microsoft Docs
description: Come aggiungere le istanze di MySQL per il provisioning tramite il Provider di risorse MySQL Adapter
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
ms.date: 07/02/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: bccc2dcad8e326cd29cfe031a95a7c2d0cf5ec7f
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38302314"
---
# <a name="add-hosting-servers-for-the-mysql-resource-provider"></a>Aggiungere server di hosting per il provider di risorse MySQL

È possibile ospitare un'istanza di MySQL in una macchina virtuale (VM) nella [Azure Stack](azure-stack-poc.md), o in una macchina virtuale all'esterno dell'ambiente Azure Stack, a condizione che il provider di risorse MySQL può connettersi all'istanza.

Le versioni di MySQL 5.6 e 5.7 8.0 possono essere utilizzate per i server di hosting. RP MySQL non supporta l'autenticazione caching_sha2_password. che verrà aggiunto nella versione successiva. 8.0 di MySQL Server devono essere configurati per usare mysql_native_password. È supportato anche MariaDB.

## <a name="connect-to-a-mysql-hosting-server"></a>Connettersi a un server di hosting MySQL

Assicurarsi di avere le credenziali per un account con privilegi di amministratore di sistema. Per aggiungere un server di hosting, seguire questa procedura:

1. Accedere al portale di Azure Stack operatore come un amministratore del servizio.
2. Selezionare **Altri servizi**.
3. Selezionare **risorse amministrative** > **server di Hosting MySQL** > **+ Aggiungi**. Verrà visualizzata la **aggiungere un Server di Hosting MySQL** finestra di dialogo, illustrata nella schermata riportata di seguito.

   ![Configurare un server di hosting](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

4. Fornire i dettagli della connessione dell'istanza di MySQL Server.

   * Per la **nome del Server di Hosting MySQL**, fornire il nome di dominio completo (FQDN) o un indirizzo IPv4 valido. Non usare il nome breve della macchina virtuale.
   * Un'istanza di MySQL predefinita non è specificata, quindi è necessario specificare il **le dimensioni del Server di Hosting in GB**. Immettere una dimensione vicina al valore di capacità del server di database.
   * Mantenere l'impostazione predefinita per **sottoscrizione**.
   * Per la **gruppo di risorse**, crearne uno nuovo o usare un gruppo esistente.

   > [!NOTE]
   > Se l'istanza di MySQL è possibile accedere al tenant e l'amministratore di Azure Resource Manager, è possibile inserirlo sotto il controllo del provider di risorse. Tuttavia, l'istanza di MySQL **necessario** essere allocata esclusivamente per il provider di risorse.

5. Selezionare **SKU** per aprire il **SKU creare** finestra di dialogo.

   ![Creare uno SKU MySQL](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)

   Lo SKU **nome** deve riflettere le proprietà dello SKU in modo che gli utenti possono distribuire i relativi database per lo SKU appropriato.

6. Selezionare **OK** per creare lo SKU.
> [!NOTE]
> Gli SKU possono richiedere fino a un'ora siano visibili nel portale. È possibile creare un database fino a quando non lo SKU è distribuito e in esecuzione.

7. Sotto **aggiungere un Server di Hosting MySQL**, selezionare **crea**.

Quando si aggiungono server, assegnarli a uno SKU nuovo o esistente per differenziare le offerte di servizio. Ad esempio, è possibile avere un'istanza di MySQL aziendale che offre un aumento di database e i backup automatici. È possibile riservare il server ad alte prestazioni per reparti diversi all'interno dell'organizzazione.

## <a name="security-considerations-for-mysql"></a>Considerazioni sulla sicurezza per MySQL

Le informazioni seguenti si applicano per la relying Party e il server di hosting MySQL:

* Assicurarsi che tutti i server host siano configurati per la comunicazione con TLS 1.2. Visualizzare [configurazione di MySQL per l'uso di connessioni crittografate](https://dev.mysql.com/doc/refman/5.7/en/using-encrypted-connections.html).
* Impiegare [Transparent Data Encryption](https://dev.mysql.com/doc/mysql-secure-deployment-guide/5.7/en/secure-deployment-data-encryption.html).
* RP MySQL non supporta l'autenticazione caching_sha2_password.

## <a name="increase-backend-database-capacity"></a>Aumentare la capacità del database back-end

È possibile aumentare la capacità di database di back-end distribuendo più server di MySQL nel portale di Azure Stack. Aggiungere questi server a uno SKU nuovo o esistente. Se si aggiunge un server a uno SKU esistente, assicurarsi che le caratteristiche server sono le stesse gli altri server nello SKU.

## <a name="make-mysql-database-servers-available-to-your-users"></a>Rendere disponibili agli utenti i server di database MySQL

Creare i piani e offerte per rendere disponibili agli utenti i server di database MySQL. Aggiungere il servizio Microsoft.MySqlAdapter al piano e creare una nuova quota. MySQL non consente di limitare le dimensioni dei database.

## <a name="next-steps"></a>Passaggi successivi

[Creare un database MySQL](azure-stack-mysql-resource-provider-databases.md)