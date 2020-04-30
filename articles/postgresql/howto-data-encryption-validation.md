---
title: Come garantire la convalida del database di Azure per PostgreSQL-crittografia dei dati
description: Informazioni su come convalidare la crittografia del database di Azure per PostgreSQL-crittografia dei dati tramite la chiave gestita dei clienti.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 4672a92ceba5dc52c717f76a705d0fa508ab41fd
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82515469"
---
# <a name="validating-data-encryption-for-azure-database-for-postgresql"></a>Convalida della crittografia dei dati per database di Azure per PostgreSQL

Questo articolo illustra come verificare che la crittografia dei dati tramite la chiave gestita dal cliente per database di Azure per PostgreSQL funzioni come previsto.

## <a name="check-the-encryption-status"></a>Verificare lo stato della crittografia

### <a name="from-portal"></a>Dal portale

1. Se si desidera verificare che la chiave del cliente venga utilizzata per la crittografia, attenersi alla seguente procedura:

    * Nella portale di Azure passare al **Azure Key Vault** -> **chiavi**
    * Selezionare la chiave usata per la crittografia del server.
    * Impostare lo stato **della chiave su** **No**.
  
       Dopo un certo periodo di tempo (**~ 15 min**), lo **stato** del server del database di Azure per PostgreSQL non dovrebbe essere **accessibile**. Tutte le operazioni di I/O eseguite sul server avranno esito negativo e la convalida del server è effettivamente crittografata con la chiave Customers e la chiave non è attualmente valida.
    
        Per rendere **disponibile** il server per, è possibile rivalidare la chiave. 
    
    * Impostare lo stato della chiave nella Key Vault su **Sì**.
    * In **crittografia dati**server selezionare **revalidate Key**.
    * Dopo che la riconvalida della chiave ha avuto esito positivo, lo **stato** del server diventa **disponibile**

2. Nel portale di Azure, se è possibile assicurarsi che sia impostata la chiave di crittografia, i dati vengono crittografati con la chiave Customers utilizzata nel portale di Azure.

  ![Panoramica dei criteri di accesso](media/concepts-data-access-and-security-data-encryption/byok-validate.png)

### <a name="from-cli"></a>Dall'interfaccia della riga di comando

1. È possibile usare il comando *AZ CLI* per convalidare le risorse chiave usate per il database di Azure per il server PostgreSQL.

    ```azurecli-interactive
   az postgres server key list --name  '<server_name>'  -g '<resource_group_name>'
    ```

    Per un server senza set di crittografia dei dati, questo comando restituirà un set vuoto [].

### <a name="azure-audit-reports"></a>Report di controllo di Azure

È inoltre possibile esaminare i [report di controllo](https://servicetrust.microsoft.com) che forniscono informazioni sulla conformità con gli standard di protezione dei dati e i requisiti normativi.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla crittografia dei dati, vedere [crittografia dei dati a server singolo di database di Azure per PostgreSQL con chiave gestita dal cliente](concepts-data-encryption-postgresql.md).