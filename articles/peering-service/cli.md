---
title: Registrare una connessione di anteprima del servizio di peering usando l'interfaccia della riga di comando di Azure
description: Informazioni su come registrare una connessione del servizio di peering usando l'interfaccia della riga di comando di Azure
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/2/2020
ms.author: derekol
ms.openlocfilehash: e7b696ba052b2aca9e14628327c07275845607ad
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94540587"
---
# <a name="register-a-peering-service-connection-by-using-the-azure-cli"></a>Registrare una connessione del servizio di peering usando l'interfaccia della riga di comando di Azure

Il servizio di peering di Azure è un servizio di rete che migliora la connettività dei clienti ai servizi cloud Microsoft, come Microsoft 365, Dynamics 365, i servizi SaaS (Software as a Service), Azure o qualsiasi servizio Microsoft accessibile tramite la rete Internet pubblica. Questo articolo illustra come registrare una connessione al servizio di peering usando l'interfaccia della riga di comando di Azure.

- Questo articolo richiede la versione 2.0.28 o successiva dell'interfaccia della riga di comando di Azure. Eseguire [az version](/cli/azure/reference-index#az_version) per trovare la versione e le librerie dipendenti installate. Per eseguire l'aggiornamento alla versione più recente, eseguire [az upgrade](/cli/azure/reference-index#az_upgrade).

## <a name="prerequisites"></a>Prerequisiti 

È necessario disporre di quanto segue:

### <a name="azure-account"></a>Account Azure

Occorre avere un account Microsoft Azure valido e attivo. Questo account è necessario per configurare la connessione al servizio di peering. Il servizio di peering è una risorsa all'interno delle sottoscrizioni di Azure.

### <a name="connectivity-provider"></a>Provider di connettività

È possibile collaborare con un provider di servizi Internet o Internet Exchange per ottenere il servizio di peering in modo da connettere la propria rete alla rete Microsoft.

Assicurarsi che i provider di connettività siano partner Microsoft.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

- Questo articolo richiede la versione 2.0.28 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.

### <a name="1-select-your-subscription"></a>1. Selezionare la sottoscrizione

Selezionare la sottoscrizione per cui si vuole registrare la connessione al servizio di peering.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

Se non si dispone già di un gruppo di risorse, è necessario crearne uno prima di registrare la connessione al servizio di peering. È possibile creare un gruppo di risorse eseguendo il comando seguente:

```azurecli-interactive
az group create -n MyResourceGroup -l "West US"
```

### <a name="2-register-your-subscription-with-the-resource-provider-and-feature-flag"></a>2. registrare la sottoscrizione con il provider di risorse e il flag funzionalità

Prima di procedere alla procedura di registrazione della connessione al servizio di peering usando l'interfaccia della riga di comando di Azure, registrare la sottoscrizione con il provider di risorse e il flag funzionalità usando l'interfaccia della riga di comando di Azure. I comandi dell'interfaccia della riga di comando di Azure sono specificati qui:

```azurecli-interactive

az feature register --namespace Microsoft.Peering --name AllowPeeringService

```

### <a name="3-register-the-peering-service-connection"></a>3. registrare la connessione al servizio di peering

Registrare la connessione al servizio di peering usando il seguente set di comandi tramite l'interfaccia della riga di comando di Azure. Questo esempio registra la connessione al servizio di peering denominata myPeeringService.

```azurecli-interactive
az peering service create : Create peering service\
  --location -l \
  --name myPeeringService\
  --resource-group -g MyResourceGroup\
  --peering-service-location\
  --peering-service-provider\
  --tags
```

### <a name="4-register-the-prefix"></a>4. registrare il prefisso

Registrare il prefisso fornito dal provider di connettività eseguendo i comandi seguenti tramite l'interfaccia della riga di comando di Azure. Questo esempio registra il prefisso denominato myPrefix.

```azurecli-interactive
az peering service prefix create \
  --name  myPrefix\
  --peering-service-name myPeeringService\
  --resource-group  -g myResourceGroup\
```

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulla connessione al servizio di peering, vedere [connessione al servizio di peering](connection.md).
- Per informazioni sui dati di telemetria delle connessioni al servizio di peering, vedere [Telemetria delle connessioni al servizio di peering](connection-telemetry.md).
- Per misurare i dati di telemetria, vedere [Misurare la telemetria delle connessioni](measure-connection-telemetry.md).
- Per registrare la connessione usando Azure PowerShell, vedere [Registrare una connessione al servizio di peering - Azure PowerShell](powershell.md).
- Per registrare la connessione usando il portale di Azure, vedere [registrare una connessione al servizio di peering portale di Azure](azure-portal.md).
