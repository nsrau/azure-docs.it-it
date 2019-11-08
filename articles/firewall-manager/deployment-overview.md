---
title: Panoramica della distribuzione dell'anteprima di Gestione firewall di Azure
description: Informazioni sui passaggi di distribuzione generali necessari per l'anteprima di Gestione firewall di Azure
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: df87e652d2969d4ae12e97a2b455648cf39382c3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488257"
---
# <a name="azure-firewall-manager-preview-deployment-overview"></a>Panoramica della distribuzione dell'anteprima di Gestione firewall di Azure

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Per distribuire l'anteprima di Gestione firewall di Azure è possibile procedere in più modi, ma è consigliabile eseguire il processo generale descritto di seguito.

## <a name="prerequisites"></a>Prerequisiti

> [!IMPORTANT]
> L'anteprima di Gestione firewall di Azure deve essere abilitata in modo esplicito con il comando di PowerShell `Register-AzProviderFeature`.
>Dal prompt dei comandi di PowerShell eseguire questi comandi:
>
>```azure-powershell
>connect-azaccount
>Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
>```
>Sono necessari fino a 30 minuti per completare la registrazione della funzionalità. Eseguire questo comando per controllare lo stato della registrazione:
>
>`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`



## <a name="general-deployment-process"></a>Processo generale di distribuzione

1. Creare un'architettura hub and spoke

   - Creare un hub virtuale protetto con Gestione firewall di Azure e aggiungere connessioni di rete virtuale.<br>*or*<br>
   - Creare un hub di rete WAN virtuale e aggiungere connessioni di rete virtuale.
2. Selezionare provider di sicurezza

   - Operazione eseguita durante la creazione di un hub virtuale protetto.<br>*or*<br>
   - Convertire un hub di rete WAN virtuale esistente in un hub virtuale protetto.
3. Creare un criterio firewall e associarlo all'hub

   - Applicabile solo se si usa Firewall di Azure.
   - I criteri di sicurezza come servizio (SECaaS) di terze parti vengono configurati tramite l'esperienza di gestione dei partner.
4. Configurare le impostazioni della route per instradare il traffico all'hub protetto

   - È possibile instradare facilmente il traffico all'hub protetto per filtrare e accedere senza route definite dall'utente alle reti virtuali spoke usando la pagina delle impostazioni della route dell'hub virtuale protetto.

> [!NOTE]
> - Non è possibile disporre di più di un hub per ogni WAN virtuale per area. Per ottenere questo risultato, è tuttavia possibile aggiungere più WAN virtuali nell'area.
> - Non è possibile avere spazi IP sovrapposti per gli hub in una WAN virtuale.
> - Le connessioni di rete virtuale dell'hub devono trovarsi nella stessa area dell'hub.

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: Proteggere la rete cloud con l'anteprima di Gestione firewall di Azure usando il portale di Azure](secure-cloud-network.md)