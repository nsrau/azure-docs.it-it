---
title: Come garantire la convalida del database di Azure per MySQL-crittografia dei dati
description: Informazioni su come convalidare la crittografia del database di Azure per MySQL-crittografia dei dati tramite la chiave gestita dei clienti.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 04/28/2020
ms.openlocfilehash: e7b747bd2babeeccb210bb30c3eb28f22d4befe7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90905799"
---
# <a name="validating-data-encryption-for-azure-database-for-mysql"></a>Convalida della crittografia dei dati per database di Azure per MySQL

Questo articolo illustra come verificare che la crittografia dei dati tramite la chiave gestita dal cliente per database di Azure per MySQL funzioni come previsto.

## <a name="check-the-encryption-status"></a>Verificare lo stato della crittografia

### <a name="from-portal"></a>Dal portale

1. Se si desidera verificare che la chiave del cliente venga utilizzata per la crittografia, attenersi alla seguente procedura:

    * Nella portale di Azure passare al **Azure Key Vault**  ->  **chiavi**
    * Selezionare la chiave usata per la crittografia del server.
    * Impostare lo stato **della chiave su** **No**.
  
       Dopo un certo periodo di tempo (**circa 15 minuti**), lo **stato** del database di Azure per il server MySQL non dovrebbe essere **accessibile**. Tutte le operazioni di I/O eseguite sul server avranno esito negativo e la convalida del server è effettivamente crittografata con la chiave Customers e la chiave non è attualmente valida.
    
       Per rendere **disponibile** il server per, è possibile rivalidare la chiave. 
    
    * Impostare lo stato della chiave nella Key Vault su **Sì**.
    * In **crittografia dati**server selezionare **revalidate Key**.
    * Dopo che la riconvalida della chiave ha avuto esito positivo, lo **stato** del server diventa **disponibile**.

2. Nel portale di Azure, se è possibile assicurarsi che sia impostata la chiave di crittografia, i dati vengono crittografati con la chiave Customers utilizzata nel portale di Azure.

  :::image type="content" source="media/concepts-data-access-and-security-data-encryption/byok-validate.png" alt-text="Panoramica dei criteri di accesso":::

### <a name="from-cli"></a>Dall'interfaccia della riga di comando

1. È possibile usare il comando *AZ CLI* per convalidare le risorse chiave usate per il database di Azure per il server MySQL.

    ```azurecli-interactive
   az mysql server key list --name  '<server_name>'  -g '<resource_group_name>'
    ```

    Per un server senza set di crittografia dei dati, questo comando restituisce un set vuoto [].

### <a name="azure-audit-reports"></a>Report di controllo di Azure

È inoltre possibile esaminare i [report di controllo](https://servicetrust.microsoft.com) che forniscono informazioni sulla conformità con gli standard di protezione dei dati e i requisiti normativi.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla crittografia dei dati, vedere [crittografia dei dati di database di Azure per MySQL con chiave gestita dal cliente](concepts-data-encryption-mysql.md).