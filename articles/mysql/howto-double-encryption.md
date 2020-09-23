---
title: Crittografia doppia dell'infrastruttura-portale di Azure-database di Azure per MySQL
description: Informazioni su come configurare e gestire la crittografia doppia dell'infrastruttura per il database di Azure per MySQL.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: eafad5edf9dcac5745986d09060baf7e4278762d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90903980"
---
# <a name="infrastructure-double-encryption-for-azure-database-for-mysql"></a>Crittografia doppia infrastruttura per database di Azure per MySQL

Informazioni su come usare la crittografia doppia per la configurazione e la gestione dell'infrastruttura per il database di Azure per MySQL.

## <a name="prerequisites"></a>Prerequisiti

* È necessario disporre di una sottoscrizione di Azure e avere il ruolo di amministratore di tale sottoscrizione.

## <a name="create-an-azure-database-for-mysql-server-with-infrastructure-double-encryption---portal"></a>Creare un database di Azure per il server MySQL con l'infrastruttura crittografia doppia-portale

Seguire questa procedura per creare un database di Azure per il server MySQL con l'infrastruttura di crittografia doppia da portale di Azure:

1. Nell'angolo in alto a sinistra del portale selezionare **Crea una risorsa** (+).

2. Selezionare **Database** > **Database di Azure per MySQL**. È anche possibile immettere **MySQL** nella casella di ricerca per trovare il servizio.

   :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png" alt-text="Opzione Database di Azure per MySQL":::

3. Fornire le informazioni di base del server. Selezionare **Impostazioni aggiuntive** e abilitare la casella di controllo **crittografia doppia infrastruttura** per impostare il parametro.

    :::image type="content" source="./media/howto-double-encryption/infrastructure-encryption-selected.png" alt-text="Selezioni per database di Azure per MySQL":::

4. Selezionare **Rivedi e crea** per effettuare il provisioning del server.

    :::image type="content" source="./media/howto-double-encryption/infrastructure-encryption-summary.png" alt-text="Riepilogo di database di Azure per MySQL":::

5. Una volta creato il server, è possibile convalidare la crittografia a doppia infrastruttura controllando lo stato nel pannello **Data Encryption** server.

    :::image type="content" source="./media/howto-double-encryption/infrastructure-encryption-validation.png" alt-text="Convalida di database di Azure per MySQL":::

## <a name="create-an-azure-database-for-mysql-server-with-infrastructure-double-encryption---cli"></a>Creare un database di Azure per il server MySQL con l'infrastruttura crittografia doppia-interfaccia della riga di comando

Seguire questa procedura per creare un database di Azure per il server MySQL con l'infrastruttura doppia crittografia dall'interfaccia della riga di comando:

Questo esempio crea un gruppo di risorse denominato `myresourcegroup` nella `westus` posizione.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```
L'esempio seguente crea negli Stati Uniti occidentali un server MySQL 5.7 denominato `mydemoserver` nel gruppo di risorse `myresourcegroup` con l'account di accesso amministratore server `myadmin`. Questo è un server per **utilizzo generico di ** **generazione 4** con **2 vCore**. Verrà abilitata anche la crittografia doppia dell'infrastruttura per il server creato. Sostituire `<server_admin_password>` con il proprio valore.

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 5.7 --infrastructure-encryption <Enabled/Disabled>
```

## <a name="next-steps"></a>Passaggi successivi

 Per altre informazioni sulla crittografia dei dati, vedere [crittografia doppia dell'infrastruttura dati di database di Azure per MySQL](concepts-Infrastructure-double-encryption.md).
