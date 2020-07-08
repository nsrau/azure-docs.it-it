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
ms.openlocfilehash: fbb9a6237aafe1c1b7c94d9dd23ad4c3665c0c73
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871461"
---
# <a name="register-a-peering-service-connection-by-using-the-azure-cli"></a>Registrare una connessione del servizio di peering usando l'interfaccia della riga di comando di Azure

Il servizio di peering di Azure è un servizio di rete che migliora la connettività dei clienti ai servizi cloud Microsoft, come Office 365, Dynamics 365, i servizi SaaS (Software as a Service), Azure o qualsiasi servizio Microsoft accessibile tramite la rete Internet pubblica. Questo articolo illustra come registrare una connessione al servizio di peering usando l'interfaccia della riga di comando di Azure.

Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) ora.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, questo articolo richiede l'interfaccia della riga di comando di Azure versione 2.0.28 o successiva. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Prerequisiti 

È necessario disporre di quanto segue:

### <a name="azure-account"></a>Account Azure

Occorre avere un account Microsoft Azure valido e attivo. Questo account è necessario per configurare la connessione al servizio di peering. Il servizio di peering è una risorsa all'interno delle sottoscrizioni di Azure.

### <a name="connectivity-provider"></a>Provider di connettività

È possibile collaborare con un provider di servizi Internet o Internet Exchange per ottenere il servizio di peering in modo da connettere la propria rete alla rete Microsoft.

Assicurarsi che i provider di connettività siano partner Microsoft.

### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. accedere al proprio account Azure e selezionare la sottoscrizione

Per iniziare la configurazione, accedere al proprio account Azure. Se si usa l'opzione Cloud Shell **prova** , l'accesso viene eseguito automaticamente. Usare gli esempi seguenti per facilitare la connessione.

```azurecli-interactive
az login
```

Controllare le sottoscrizioni per l'account.

```azurecli-interactive
az account list
```

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
