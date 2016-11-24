---
title: Creare un registro contenitori nel portale | Documentazione Microsoft
description: Introduzione alla creazione e gestione dei registri di contenitori di Azure con il portale di Azure
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
ms.date: 11/14/2016
ms.author: stevelas
translationtype: Human Translation
ms.sourcegitcommit: aa4b960ed75b5a4702317bf557b4588e7a54fa0e
ms.openlocfilehash: c22fee1a9172eba28d8f841d973704934cdb3ebb

---
# <a name="create-a-container-registry-using-the-azure-portal"></a>Creare un registro contenitori con il portale di Azure
Usare il portale di Azure per creare un registro contenitori e gestirne le impostazioni. È anche possibile creare e gestire registri di contenitori usando i [comandi dell'anteprima dell'interfaccia della riga di comando di Azure 2.0](container-registry-get-started-azure-cli.md) oppure, a livello di codice, con l'[API REST](https://go.microsoft.com/fwlink/p/?linkid=834376) del servizio Registro contenitori.

Per concetti e informazioni di base, vedere [Informazioni su Registro contenitori di Azure](container-registry-intro.md)


> [!NOTE]
> Registro contenitori è attualmente disponibile in anteprima.


## <a name="create-a-container-registry"></a>Creare un registro contenitori
1. Nel [portale](https://portal.azure.com) fare clic su **+ Nuovo**.
2. Cercare **container registry** nel Marketplace.
3. Selezionare **Container Registry (anteprima)** di **Microsoft**. 
    ![Servizio Registro contenitori in Azure Marketplace](./media/container-registry-get-started-portal/container-registry-marketplace.png)
4. Fare clic su **Create**. Verrà visualizzato il pannello **Registro contenitori**.

    ![Impostazioni di Registro contenitori](./media/container-registry-get-started-portal/container-registry-settings.png)
5. Nel pannello **Registro contenitori** immettere le informazioni seguenti. Al termine dell'operazione fare clic su **Crea**.
   
    a. **Registry name** (Nome registro): nome di dominio di primo livello univoco globale per il registro. Il nome del registro negli esempi è *myRegistry01*, ma è necessario sostituirlo con un nome univoco personalizzato. Il nome può contenere solo lettere e numeri.
   
    b. **Resource group** (Gruppo di risorse): selezionare un [gruppo di risorse](../azure-resource-manager/resource-group-overview.md#resource-groups) esistente o specificare il nome di un nuovo gruppo. 
   
    c. **Location** (Percorso): selezionare il percorso di un data center di Azure in cui il servizio è [disponibile](https://azure.microsoft.com/regions/services/), ad esempio **Stati Uniti centro-meridionali**. 
   
    d. **Admin user** (Utente amministratore): consentire eventualmente a un utente amministratore di accedere al registro. È possibile modificare questa impostazione dopo aver creato il registro.
   
   > [!IMPORTANT]
   > Oltre a consentire l'accesso tramite un account utente amministratore, i registri dei contenitori supportano l'autenticazione basata sulle entità servizio di Azure Active Directory. Per altre informazioni e considerazioni, vedere [Authenticate with the container registry](container-registry-authentication.md) (Eseguire l'autenticazione al registro contenitori).
   
    e. **Storage account** (Account di archiviazione): usare l'impostazione predefinita per creare un [account di archiviazione](../storage/storage-introduction.md) oppure selezionarne uno esistente nello stesso percorso.

## <a name="manage-registry-settings"></a>Gestire le impostazioni del registro
Dopo aver creato il registro, trovare le impostazioni iniziando dal pannello **Container Registries** (Registri dei contenitori) nel portale. Le impostazioni potrebbero essere ad esempio necessarie per accedere al registro o abilitare o disabilitare l'utente amministratore.

1. Nel pannello **Container Registries** (Registri dei contenitori) fare clic sul nome del registro.
   
    ![Pannello Container Registries (Registri dei contenitori)](./media/container-registry-get-started-portal/container-registry-blade.png)
2. Per gestire le impostazioni di accesso, fare clic su **Access key** (Chiave di accesso).
   
    ![Accesso al registro contenitori](./media/container-registry-get-started-portal/container-registry-access.png)
3. Si notino le impostazioni seguenti:
   
   * **Login server** (Server di accesso): nome completo usato per accedere al registro. In questo esempio si tratta di `myregistry01-contoso.azurecr.io`.
   * **Admin user** (Utente amministratore): abilita o disabilita l'account utente amministratore per il registro.
   * **Username** (Nome utente) e **Password**: credenziali dell'account utente amministratore (se abilitato) che può essere usato per accedere al registro. È facoltativamente possibile rigenerare la password.

## <a name="next-steps"></a>Passaggi successivi
* [Effettuare il push della prima immagine tramite l'interfaccia della riga di comando di Docker](container-registry-get-started-docker-cli.md)






<!--HONumber=Nov16_HO3-->


