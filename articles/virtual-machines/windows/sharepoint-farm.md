---
title: Creare farm di SharePoint Server in Azure | Documentazione Microsoft
description: Creare rapidamente una nuova farm di SharePoint 2013 o SharePoint 2016 in Azure tramite il marketplace del portale di Azure.
services: virtual-machines-windows
documentationcenter: 
author: JoeDavies-MSFT
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 89b124da-019d-4179-86dd-ad418d05a4f2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/30/2016
ms.author: josephd
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 00648ee35962b22fb7eeceaa6d9df7305c801abf
ms.lasthandoff: 03/31/2017


---
# <a name="create-sharepoint-server-farms-using-the-azure-portal-marketplace"></a>Creare farm di SharePoint Server usando il marketplace del portale di Azure

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="sharepoint-2013-farms"></a>Farm SharePoint 2013
Con il marketplace del portale di Microsoft Azure, è possibile creare rapidamente delle farm pre-configurate di SharePoint Server 2013. Ciò consente di risparmiare una notevole quantità di tempo in caso si necessiti di una farm di SharePoint di base o a disponibilità elevata per un ambiente di sviluppo e test o in caso si stia valutando l'opportunità di usare SharePoint Server 2013 come soluzione per la collaborazione all'interno dell'organizzazione.

> [!NOTE]
> L'elemento **SharePoint Farm** nella sezione Marketplace del portale di Azure è stato rimosso. È stato sostituito dagli elementi **SharePoint 2013 non-HA Farm** e **SharePoint 2013 HA Farm**.
>
>

La farm di SharePoint di base è costituita da tre macchine virtuali in questa configurazione.

![sharepointfarm](./media/sharepoint-farm/Non-HAFarm.png)

È possibile usare questa configurazione di farm per un'installazione semplificata per lo sviluppo di app SharePoint o per la prima valutazione di SharePoint 2013.

Per creare la farm di SharePoint (tre server) di base:

1. Fare clic [qui](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/)
2. Fare clic su **Distribuisci**.
3. Nel riquadro **SharePoint 2013 non-HA Farm**, fare clic su **Crea**.
4. Specificare le impostazioni nei passaggi del riquadro **Creare una SharePoint 2013 non-HA Farm** e quindi fare clic su **Crea**.

La farm di SharePoint a disponibilità elevata è costituita da nove macchine virtuali in questa configurazione.

![sharepointfarm](./media/sharepoint-farm/HAFarm.png)

È possibile usare questa configurazione di farm per testare carichi client più elevati, la disponibilità elevata del sito di SharePoint esterno e i gruppi di disponibilità AlwaysOn di SQL Server per una farm di SharePoint. È anche possibile usare questa configurazione per lo sviluppo di app SharePoint in un ambiente a disponibilità elevata.

Per creare la farm di SharePoint a disponibilità elevata (nove server):

1. Fare clic [qui](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/)
2. Fare clic su **Distribuisci**.
3. Nel riquadro **SharePoint 2013 HA Farm**, fare clic su **Crea**.
4. Specificare le impostazioni nei sette passaggi del riquadro di creazione di **SharePoint 2013 HA Farm** e quindi fare clic su **Crea**.

> [!NOTE]
> Non è possibile creare l'elemento **SharePoint 2013 non-HA Farm** o **SharePoint 2013 HA Farm** con una versione di prova gratuita di Azure.
>
>

Il portale di Azure crea entrambe queste farm in una rete virtuale solo cloud con una presenza Web con connessione Internet. Non esiste alcuna connessione VPN o ExpressRoute da sito a sito alla rete dell'organizzazione.

> [!NOTE]
> Quando si creano le farm di SharePoint di base o a disponibilità elevata tramite il portale di Azure, non è possibile specificare un gruppo di risorse esistente. Per superare questa limitazione, creare tali farm con Azure PowerShell. Per altre informazioni, vedere [Creare farm di sviluppo e di testing di SharePoint 2013 con Azure PowerShell](https://technet.microsoft.com/library/mt743093.aspx#powershell).
>
>

## <a name="sharepoint-2016-farms"></a>Farm SharePoint 2016
Vedere [questo articolo](https://technet.microsoft.com/library/mt723354.aspx) per istruzioni sulla creazione della seguente farm di SharePoint Server 2016 a server singolo.

![sharepointfarm](./media/sharepoint-farm/SP2016Farm.png)

## <a name="managing-the-sharepoint-farms"></a>Gestire le farm di SharePoint
È possibile amministrare i server di queste farm tramite connessioni Desktop Remoto. Per altre informazioni, vedere [Accedere alla macchina virtuale](quick-create-portal.md#connect-to-virtual-machine).

Dal sito Amministrazione centrale di SharePoint è possibile configurare i siti personali, le applicazioni di SharePoint e altre funzionalità. Per altre informazioni, vedere [Configurare SharePoint](http://technet.microsoft.com/library/ee836142.aspx).

## <a name="next-steps"></a>Passaggi successivi
* Altre [configurazioni di SharePoint](https://technet.microsoft.com/library/dn635309.aspx) nei servizi dell'infrastruttura di Azure.

