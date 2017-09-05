---
title: Creare un registro per contenitori Docker privati - Portale di Azure | Microsoft Docs
description: Introduzione alla creazione e gestione dei registri per contenitori Docker privati con il portale di Azure
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: dlepow
tags: 
keywords: 
ms.assetid: 53a3b3cb-ab4b-4560-bc00-366e2759f1a1
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 7fbbb56d775ee96c9a44363a4e41d4fc3c630582
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---

# <a name="create-a-private-docker-container-registry-using-the-azure-portal"></a>Creare un registro per contenitori Docker privati con il portale di Azure
Usare il portale di Azure per creare un registro contenitori e gestirne le impostazioni. È anche possibile creare e gestire registri di contenitori usando i comandi dell'[interfaccia della riga di comando di Azure 2.0](container-registry-get-started-azure-cli.md), [Azure PowerShell](container-registry-get-started-powershell.md) oppure, a livello di codice, con l'[API REST](https://go.microsoft.com/fwlink/p/?linkid=834376) del servizio Registro contenitori.

Per informazioni di base e concetti, vedere la [panoramica](container-registry-intro.md).

## <a name="create-a-container-registry"></a>Creare un registro contenitori
1. Nel [portale di Azure](https://portal.azure.com) fare clic su **+ Nuovo**.
2. Cercare **Registro contenitori di Azure** nel Marketplace.
3. Selezionare **Registro contenitori di Azure** pubblicato da **Microsoft**.
    ![Servizio Registro contenitori in Azure Marketplace](./media/container-registry-get-started-portal/container-registry-marketplace.png)
4. Fare clic su **Crea**. Verrà visualizzato il pannello **Registro contenitori di Azure**.

    ![Impostazioni di Registro contenitori](./media/container-registry-get-started-portal/container-registry-settings.png)
5. Nel pannello **Registro contenitori di Azure** immettere le informazioni seguenti. Al termine dell'operazione fare clic su **Crea**.

    a. **Registry name** (Nome registro): nome di dominio di primo livello univoco globale per il registro. Il nome del registro negli esempi è *myRegistry01*, ma è necessario sostituirlo con un nome univoco personalizzato. Il nome può contenere solo lettere e numeri.

    b. **Resource group** (Gruppo di risorse): selezionare un [gruppo di risorse](../azure-resource-manager/resource-group-overview.md#resource-groups) esistente o specificare il nome di un nuovo gruppo.

    c. **Location** (Posizione): selezionare la posizione di un data center di Azure in cui il servizio è [disponibile](https://azure.microsoft.com/regions/services/), ad esempio **Stati Uniti centro-meridionali**.

    d. **Admin user** (Utente amministratore): consentire eventualmente a un utente amministratore di accedere al registro. È possibile modificare questa impostazione dopo aver creato il registro.

      > [!IMPORTANT]
      > Oltre a consentire l'accesso tramite un account utente amministratore, i registri dei contenitori supportano l'autenticazione basata sulle entità servizio di Azure Active Directory. Per altre informazioni e considerazioni, vedere [Authenticate with the container registry](container-registry-authentication.md) (Eseguire l'autenticazione al registro contenitori).
      >

    e. **Storage account** (Account di archiviazione): usare l'impostazione predefinita per creare un [account di archiviazione](../storage/common/storage-introduction.md) oppure selezionarne uno esistente nella stessa posizione. Archiviazione Premium non è attualmente supportata.

## <a name="manage-registry-settings"></a>Gestire le impostazioni del registro
Dopo aver creato il registro, trovare le impostazioni iniziando dal pannello **Container Registries** (Registri dei contenitori) nel portale. Le impostazioni potrebbero essere ad esempio necessarie per accedere al registro o abilitare o disabilitare l'utente amministratore.

1. Nel pannello **Container Registries** (Registri dei contenitori) fare clic sul nome del registro.

    ![Pannello Container Registries (Registri dei contenitori)](./media/container-registry-get-started-portal/container-registry-blade.png)
2. Per gestire le impostazioni di accesso, fare clic su **Access key** (Chiave di accesso).

    ![Accesso al registro contenitori](./media/container-registry-get-started-portal/container-registry-access.png)
3. Si notino le impostazioni seguenti:

   * **Login server** (Server di accesso): nome completo usato per accedere al registro. In questo esempio si tratta di `myregistry01.azurecr.io`.
   * **Admin user** (Utente amministratore): abilita o disabilita l'account utente amministratore per il registro.
   * **Username** (Nome utente) e **Password**: credenziali dell'account utente amministratore (se abilitato) che può essere usato per accedere al registro. È facoltativamente possibile rigenerare le password. Vengono create due password in modo da mantenere le connessioni al registro usando una password mentre si rigenera l'altra. Per eseguire invece l'autenticazione con un'entità servizio, vedere [Eseguire l'autenticazione con un registro contenitori Docker privato](container-registry-authentication.md).

## <a name="next-steps"></a>Passaggi successivi
* [Effettuare il push della prima immagine tramite l'interfaccia della riga di comando di Docker](container-registry-get-started-docker-cli.md)

