---
title: Server in Azure Stack di Hosting MySQL | Documenti Microsoft
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
ms.date: 06/25/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 5522eb1b8b0398aeb6f1b0dd8578b906880b4e89
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938553"
---
# <a name="add-hosting-servers-for-the-mysql-resource-provider"></a>Aggiungere server di hosting per il provider di risorse MySQL

È possibile ospitare un'istanza di MySQL in una macchina virtuale (VM) nella [Azure Stack](azure-stack-poc.md), o in una macchina virtuale all'esterno dell'ambiente dello Stack di Azure, a condizione che il provider di risorse MySQL possa connettersi all'istanza.

## <a name="connect-to-a-mysql-hosting-server"></a>Connettersi a un server di hosting MySQL

Assicurarsi di avere le credenziali per un account con privilegi di amministratore di sistema. Per aggiungere un server di hosting, seguire questi passaggi:

1. Accedere al portale di Azure Stack operatore come un amministratore del servizio.
2. Selezionare **Altri servizi**.
3. Selezionare **risorse amministrative** > **server di Hosting MySQL** > **+ Aggiungi**. Verrà visualizzata la **aggiungere un Server di Hosting MySQL** finestra di dialogo, illustrato nella schermata seguente.

   ![Configurare un server di hosting](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

4. Fornire i dettagli di connessione dell'istanza di MySQL Server.

   * Per **nome del Server di Hosting MySQL**, fornire il nome di dominio completo (FQDN) o un indirizzo IPv4 valido. Non usare il nome breve della macchina virtuale.
   * Non viene fornita un'istanza di MySQL predefinita, pertanto è necessario specificare il **le dimensioni del Server di Hosting in GB**. Immettere una dimensione vicina al valore di capacità del server di database.
   * Mantenere l'impostazione predefinita per **sottoscrizione**.
   * Per **gruppo di risorse**, crearne uno nuovo, o utilizzare un gruppo esistente.

   > [!NOTE]
   > Se l'istanza di MySQL è accessibile da tenant e l'amministratore Gestione risorse di Azure, è possibile inserirlo sotto il controllo del provider di risorse. Tuttavia, l'istanza di MySQL **necessario** essere allocata in modo esclusivo al provider di risorse.

5. Selezionare **SKU** per aprire la **creare SKU** finestra di dialogo.

   ![Creare uno SKU di MySQL](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)

   Lo SKU **nome** deve riflettere le proprietà della SKU in modo che gli utenti possono distribuire i database per lo SKU appropriato.

   >[!IMPORTANT]
   >Caratteri speciali, inclusi gli spazi e i periodi di, non sono supportati in **nome** oppure **livello** quando si crea un'unità SKU per il provider di risorse MySQL.

6. Selezionare **OK** per creare lo SKU.
7. Sotto **aggiungere un Server di Hosting MySQL**, selezionare **crea**.

Quando si aggiungono server, assegnare loro una SKU nuovo o esistente per differenziare le offerte di servizio. Ad esempio, si può avere un'istanza enterprise di MySQL che fornisce un aumento di database e backup automatici. È possibile riservare il server ad alte prestazioni per diversi reparti dell'organizzazione.

## <a name="increase-backend-database-capacity"></a>Aumentare la capacità del database back-end

È possibile aumentare la capacità di database back-end tramite la distribuzione di più server di MySQL nel portale di Azure Stack. Aggiungere questi server a uno SKU di nuovo o esistente. Se si aggiunge un server a una SKU esistente, assicurarsi che le caratteristiche server corrispondono a quelle degli altri server nella SKU.

## <a name="make-mysql-database-servers-available-to-your-users"></a>Rendere disponibili agli utenti di server di database MySQL

Creare i piani e le offerte per rendere disponibili agli utenti di server di database MySQL. Aggiungere il servizio Microsoft.MySqlAdapter al piano, quindi aggiungere il valore predefinito di Quota o creare una nuova Quota.

![Creare i piani e le offerte per i database](./media/azure-stack-mysql-rp-deploy/mysql-new-plan.png)

## <a name="next-steps"></a>Passaggi successivi

[Creare un database MySQL](azure-stack-mysql-resource-provider-databases.md)