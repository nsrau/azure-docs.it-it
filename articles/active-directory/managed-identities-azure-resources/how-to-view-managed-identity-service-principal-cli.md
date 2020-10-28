---
title: Visualizzare l'entità servizio di un'identità gestita-interfaccia della riga di comando di Azure-Azure AD
description: Istruzioni dettagliate su come visualizzare l'entità servizio di un'identità gestita tramite l'interfaccia della riga di comando di Azure.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/30/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: ce3a35562bdef34f44ef1093a3196ea7afb0bd9b
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92892044"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-azure-cli"></a>Visualizzare l'entità servizio di un'identità gestita tramite l'interfaccia della riga di comando di Azure

Le identità gestite per le risorse di Azure offrono ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory. È possibile usare questa identità per l'autenticazione in qualsiasi servizio che supporta l'autenticazione di Azure AD senza dover immettere le credenziali nel codice. 

In questo articolo viene descritto come visualizzare l'entità servizio di un'identità gestita tramite l'interfaccia della riga di comando di Azure.

Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha familiarità con le identità gestite per le risorse di Azure, vedere informazioni [sulle identità gestite per le risorse di Azure](overview.md).

- Abilitare l'[identità assegnata dal sistema nella macchina virtuale](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity) o nell'[applicazione](../../app-service/overview-managed-identity.md#add-a-system-assigned-identity).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="view-the-service-principal"></a>Visualizzare l'entità servizio

Il comando seguente mostra come visualizzare l'entità servizio di una macchina virtuale o un'applicazione in cui è abilitata un'identità gestita. Sostituire `<Azure resource name>` con i propri valori.

```azurecli-interactive
az ad sp list --display-name <Azure resource name>
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla gestione delle entità servizio di Azure AD tramite l'interfaccia della riga di comando di Azure, vedere [az ad sp](/cli/azure/ad/sp).
