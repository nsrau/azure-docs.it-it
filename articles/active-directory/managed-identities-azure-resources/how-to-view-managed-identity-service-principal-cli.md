---
title: View service principal of a managed identity - Azure CLI - Azure AD
description: Istruzioni dettagliate su come visualizzare l'entità servizio di un'identità gestita tramite l'interfaccia della riga di comando di Azure.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba60fdfefb5d741e92a56d2dc61eb058ac26b5ba
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224614"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-azure-cli"></a>Visualizzare l'entità servizio di un'identità gestita tramite l'interfaccia della riga di comando di Azure

Le identità gestite per le risorse di Azure offrono ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory. È possibile usare questa identità per l'autenticazione in qualsiasi servizio che supporta l'autenticazione di Azure AD senza dover immettere le credenziali nel codice. 

In questo articolo viene descritto come visualizzare l'entità servizio di un'identità gestita tramite l'interfaccia della riga di comando di Azure.

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha familiarità con le identità gestite per le risorse di Azure, vedere la [sezione sulla panoramica](overview.md).
- Se non si ha già un account Azure, è possibile [registrarsi per ottenerne uno gratuito](https://azure.microsoft.com/free/).
- Abilitare l'[identità assegnata dal sistema nella macchina virtuale](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) o nell'[applicazione](/azure/app-service/overview-managed-identity#adding-a-system-assigned-identity).
- Per eseguire gli esempi di script dell'interfaccia della riga di comando, sono disponibili tre opzioni:
    - Usare [Azure Cloud Shell](../../cloud-shell/overview.md) dal portale di Azure (vedere la sezione successiva).
    - Usare Azure Cloud Shell incorporato tramite il pulsante "Prova", che si trova nell'angolo in alto a destra di ogni blocco di codice.
    - [Installare la versione più recente dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) se si preferisce usare una console dell'interfaccia della riga di comando locale e accedere ad Azure tramite `az login`
 
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="view-the-service-principal"></a>Visualizzare l'entità servizio

Il comando seguente mostra come visualizzare l'entità servizio di una macchina virtuale o un'applicazione in cui è abilitata un'identità gestita. Sostituire `<VM or application name>` con i propri valori. 

```azurecli-interactive
az ad sp list --display-name <VM or application name>
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla gestione delle entità servizio di Azure AD tramite l'interfaccia della riga di comando di Azure, vedere [az ad sp](/cli/azure/ad/sp).


