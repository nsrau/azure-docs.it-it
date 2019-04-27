---
title: Panoramica dei tag FQDN per Firewall di Azure
description: Informazioni sui tag FQDN in Firewall di Azure
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 4/23/2019
ms.author: victorh
ms.openlocfilehash: 7a412589f4e86f2a49d07f2d01ca34bf30fd528b
ms.sourcegitcommit: a95dcd3363d451bfbfea7ec1de6813cad86a36bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62738786"
---
# <a name="fqdn-tags-overview"></a>Panoramica dei tag FQDN

Un tag FQDN rappresenta un gruppo di nomi di dominio completi (FQDN) associati a servizi Microsoft noti. È possibile usare un tag FQDN nelle regole delle applicazioni per consentire il necessario passaggio del traffico in uscita attraverso il firewall.

Ad esempio, per consentire manualmente il passaggio del traffico di Windows Update attraverso il firewall, è necessario creare più regole dell'applicazione in base alla documentazione Microsoft. Con i tag FQDN, è possibile creare una regola dell'applicazione e includere il tag **Windows Update** permettendo al traffico diretto agli endpoint di Microsoft Windows Update di passare attraverso il firewall.

Non è possibile creare tag FQDN personalizzati, né è possibile specificare quali FQDN sono inclusi all'interno di un tag. Microsoft gestisce gli FQDN inclusi nel tag FQDN e aggiorna quest'ultimo in caso di modifica degli FQDN. 

<!--- screenshot of application rule with a FQDN tag.-->

La tabella seguente mostra i tag FQDN attuali che è possibile usare. Microsoft gestisce questi tag e periodicamente ne aggiungerà altri.

## <a name="current-fqdn-tags"></a>Tag FQDN correnti

|Tag FQDN  |DESCRIZIONE  |
|---------|---------|
|Windows Update     |Consente l'accesso in uscita a Microsoft Update come descritto in [Come configurare un firewall per gli aggiornamenti software](https://technet.microsoft.com/library/bb693717.aspx).|
|Diagnostica per Windows|Consente l'accesso in uscita a tutti gli [endpoint di Diagnostica Windows](https://docs.microsoft.com/windows/privacy/configure-windows-diagnostic-data-in-your-organization#endpoints).|
|Microsoft Active Protection Service (MAPS)|Consente l'accesso in uscita a [MAPS](https://cloudblogs.microsoft.com/enterprisemobility/2016/05/31/important-changes-to-microsoft-active-protection-service-maps-endpoint/).|
|Ambiente del servizio app|Consente l'accesso in uscita al traffico della piattaforma Ambiente del servizio app. Questo tag non copre gli endpoint di archiviazione ed SQL specifici del cliente creati da Ambiente del servizio app. Tali endpoint devono essere abilitati tramite [Endpoint di servizio](../virtual-network/tutorial-restrict-network-access-to-resources.md) o aggiunti manualmente.<br><br>Per altre informazioni sull'integrazione di Firewall di Azure con l'ambiente del servizio app, vedere [Blocco di un ambiente del servizio app](../app-service/environment/firewall-integration.md#configuring-azure-firewall-with-your-ase).|
|Backup di Azure|Consente l'accesso in uscita ai servizi Backup di Azure.|
|HDInsight di Azure<br>(Anteprima)|Consente l'accesso in uscita per il traffico di piattaforma HDInsight. Questo tag non copre il traffico di archiviazione o SQL specifiche del cliente da HDInsight. Abilitare le using [gli endpoint di servizio](../virtual-network/tutorial-restrict-network-access-to-resources.md) oppure aggiungerli manualmente.|

> [!NOTE]
> Quando si seleziona il tag FQDN nella regola per un'applicazione, il campo protocol:port deve essere impostato su **https**.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come distribuire un Firewall di Azure, vedere [esercitazione: Distribuire e configurare Firewall di Azure tramite il portale di Azure](tutorial-firewall-deploy-portal.md).