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
ms.date: 11/29/2018
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: b8eec72666eadf90a401dc8f0adb77df77dbf782
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90969298"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-azure-cli"></a>Visualizzare l'entità servizio di un'identità gestita tramite l'interfaccia della riga di comando di Azure

Le identità gestite per le risorse di Azure offrono ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory. È possibile usare questa identità per l'autenticazione in qualsiasi servizio che supporta l'autenticazione di Azure AD senza dover immettere le credenziali nel codice. 

In questo articolo viene descritto come visualizzare l'entità servizio di un'identità gestita tramite l'interfaccia della riga di comando di Azure.

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha familiarità con le identità gestite per le risorse di Azure, vedere la [sezione sulla panoramica](overview.md).
- Se non si ha già un account Azure, è possibile [registrarsi per ottenerne uno gratuito](https://azure.microsoft.com/free/).
- Abilitare l'[identità assegnata dal sistema nella macchina virtuale](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity) o nell'[applicazione](../../app-service/overview-managed-identity.md#add-a-system-assigned-identity).
- Per eseguire gli script di esempio, sono disponibili due opzioni:
    - Usare il [Azure cloud Shell](../../cloud-shell/overview.md), che è possibile aprire usando il pulsante **prova** nell'angolo superiore destro dei blocchi di codice.
    - Eseguire gli script localmente installando la versione più recente dell'interfaccia della riga di comando di [Azure](/cli/azure/install-azure-cli), quindi accedere ad Azure usando [AZ login](/cli/azure/reference-index#az-login). Usare un account associato alla sottoscrizione di Azure in cui si vogliono creare risorse.   

## <a name="view-the-service-principal"></a>Visualizzare l'entità servizio

Il comando seguente mostra come visualizzare l'entità servizio di una macchina virtuale o un'applicazione in cui è abilitata un'identità gestita. Sostituire `<VM or application name>` con i propri valori. 

```azurecli-interactive
az ad sp list --display-name <VM or application name>
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla gestione delle entità servizio di Azure AD tramite l'interfaccia della riga di comando di Azure, vedere [az ad sp](/cli/azure/ad/sp).
