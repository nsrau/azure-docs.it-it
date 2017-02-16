---
title: Creare un server Analysis Services in Azure | Microsoft Docs
description: Informazioni su come creare un server Analysis Services in Azure.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 7f560216-8a9a-4d06-852e-48cf24deab19
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 01/20/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 147c27d12b87379ff02b871ceabd8154e53b6c1a
ms.openlocfilehash: f189e19b5b843d6a7d056c8230bc06d85e568202


---
# <a name="create-an-analysis-services-server"></a>Creare un server Analysis Services
Questo articolo illustra la creazione di una nuova risorsa server Analysis Services nella sottoscrizione di Azure.

## <a name="before-you-begin"></a>Prima di iniziare
Per iniziare, è necessario:

* **Sottoscrizione di Azure**: visitare la pagina [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) per creare un account.
* **Azure Active Directory**: la sottoscrizione deve essere associata a un tenant Azure Active Directory. Inoltre è necessario essere registrati ad Azure con un account nell'Azure Active Directory specifica. Gli account Microsoft non sono supportati. Per altre informazioni, vedere [Autenticazione utente](analysis-services-overview.md#secure).
* **Gruppo di risorse**: usare un gruppo di risorse già esistente o [crearne uno nuovo](../azure-resource-manager/resource-group-overview.md).

> [!NOTE]
> La creazione di un server Analysis Services può dare luogo a un nuovo servizio fatturabile. Per altre informazioni, vedere [Azure Analysis Services Prezzi](https://azure.microsoft.com/pricing/details/analysis-services/).
> 
> 

## <a name="create-an-analysis-services-server"></a>Creare un server Analysis Services
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **+ Nuovo** > **Intelligence e analisi** > **Analysis Services**.
3. Nel pannello **Analysis Services** compilare i campi obbligatori e quindi fare clic su **Crea**.
   
    ![Creazione del server](./media/analysis-services-create-server/aas-create-server-blade.png)
   
   * **Nome server**: digitare un nome univoco usato per fare riferimento al server.
   * **Sottoscrizione**: selezionare la sottoscrizione a cui il server addebita le fatture.
   * **Gruppo di risorse**: si tratta di contenitori progettati per facilitare la gestione di una raccolta di risorse di Azure. Per altre informazioni, vedere [Gruppi di risorse](../azure-resource-manager/resource-group-overview.md).
   * **Località**: località del data center di Azure che ospita il server. Selezionare una località vicina alla base di utenti più estesa.
   * **Piano tariffario**: selezionare un piano tariffario. Sono supportati modelli tabulari fino a 100 GB. È comunque possibile modificare il piano tariffario in un secondo momento.
4. Fare clic su **Crea**.

La creazione richiede in genere meno di un minuto e spesso solo alcuni secondi. Se si seleziona **Add to Portal** (Aggiungi al portale), spostarsi nel portale per vedere il nuovo server. In alternativa, passare a **More services** (Altri servizi) > **Analysis Services** per vedere se il server è pronto. Se il server non viene visualizzato, aggiornare l'elenco.

 ![Dashboard](./media/analysis-services-create-server/aas-create-server-dashboard.png)

## <a name="automate-server-creation"></a>Automatizzare la creazione di server
È possibile automatizzare il provisioning immediato del server grazie ai file modello di Azure Resource Manager. Guardare il video seguente per saperne di più.

>[!VIDEO https://channel9.msdn.com/series/Azure-Analysis-Services/AzureAnalysisServicesAutomation/player]
>
>


## <a name="next-steps"></a>Passaggi successivi
Dopo aver creato un server, è possibile distribuirvi un modello usando SSDT o SSMS, come descritto in [Deploy a model](analysis-services-deploy.md) (Distribuire un modello).

Se un modello distribuito sul server si connette a origini dati locali, è necessario installare un gateway dati locale in un computer della rete, come descritto in [On-premises data gateway](analysis-services-gateway.md) (Gateway dati locale).




<!--HONumber=Nov16_HO4-->


