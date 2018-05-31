---
title: Informazioni su come gestire un'identità del servizio gestito assegnata dall'utente tramite l'interfaccia della riga di comando di Azure
description: Istruzioni dettagliate su come creare, elencare ed eliminare un'identità del servizio gestita assegnata dall'utente usando interfaccia della riga di comando di Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: 5262914e469bdc07921c3b82e990d544349b5fd4
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
ms.locfileid: "33930667"
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-the-azure-cli"></a>Creare, elencare ed eliminare un'identità del servizio gestita assegnata dall'utente usando interfaccia della riga di comando di Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

L'identità del servizio gestito offre servizi di Azure con un'identità gestita in Azure Active Directory. È possibile usare questa identità per l'autenticazione ai servizi che supportano l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

In questo articolo è illustrato come creare, elencare ed eliminare un'identità assegnata dall'utente usando l'interfaccia della riga di comando di Azure.

## <a name="prerequisites"></a>prerequisiti

- Se non si ha familiarità con l'identità del servizio gestita, vedere la [sezione sulla panoramica](overview.md). **Assicurarsi di conoscere la [differenza tra identità assegnata dal sistema e identità assegnata dall'utente](overview.md#how-does-it-work)**.
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.

- Per eseguire gli esempi di script dell'interfaccia della riga di comando, sono disponibili tre opzioni:

    - Usare [Azure Cloud Shell](../../cloud-shell/overview.md) dal portale di Azure (vedere la sezione successiva).
    - Usare Azure Cloud Shell incorporato tramite il pulsante "Prova", che si trova nell'angolo in alto a destra di ogni blocco di codice.
    - [Installare la versione più recente dell'interfaccia della riga di comando 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 o successiva) se si preferisce usare una console dell'interfaccia della riga di comando locale. Accedere ad Azure tramite `az login`, usando un account associato alla sottoscrizione di Azure in cui si desidera distribuire l'identità assegnata dall'utente.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Creare un'identità gestita assegnata dall'utente 

Per creare un'identità assegnata dall'utente, usare il comando [az identity create](/cli/azure/identity#az-identity-create). Il parametro `-g` specifica il gruppo di risorse in cui creare l'identità assegnata dall'utente, mentre il parametro `-n` ne specifica il nome. Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<USER ASSIGNED IDENTITY NAME>` con valori personalizzati:

> [!IMPORTANT]
> La creazione delle identità assegnate dall'utente supporta solo caratteri alfanumerici e trattino (numeri 0-9, lettere a-z e A-Z, -). Inoltre, il nome può avere al massimo 24 caratteri perché l'assegnazione alla macchina virtuale/set di scalabilità di macchine virtuali funzioni correttamente. Ricontrollare in seguito per aggiornamenti. Per altre informazioni, vedere [Domande frequenti e problemi noti](known-issues.md)

 ```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-identities"></a>Elencare le identità assegnate dall'utente

Per elencare le identità assegnate dall'utente, usare il comando [az identity list](/cli/azure/identity#az-identity-list).  Il parametro `-g` specifica il gruppo di risorse in cui è stata creata l'identità assegnata dall'utente.  Sostituire `<RESOURCE GROUP>` con il proprio valore:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```
Nella risposta json, le identità dell'utente hanno il valore `"Microsoft.ManagedIdentity/userAssignedIdentities"` restituito per la chiave `type`.

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-identity"></a>Eliminare un'identità assegnata dall'utente

Per eliminare un'identità assegnata dall'utente, usare il comando [az identity delete](/cli/azure/identity#az-identity-delete).  Il parametro -n specifica il nome e il parametro -g specifica il gruppo di risorse in cui è stata creata l'identità assegnata dall'utente.  Sostituire i valori dei parametri `<USER ASSIGNED IDENTITY NAME>` e `<RESOURCE GROUP>` con valori personalizzati:

 ```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> L'eliminazione di un'identità assegnata dall'utente non rimuoverà il riferimento proveniente da qualunque risorsa a cui è stato assegnato. Rimuovere quelli da VM/VMSS tramite il comando `az vm/vmss identity remove`

## <a name="related-content"></a>Contenuti correlati

Per un elenco completo dei comandi di identità dell'interfaccia della riga di comando di Azure, vedere [az identity](/cli/azure/identity).

Per informazioni su come assegnare un'identità assegnata dall'utente a una macchina virtuale di Azure vedere [Configurare identità del servizio gestita tramite interfaccia della riga di comando di Azure](qs-configure-cli-windows-vm.md#user-assigned-identity)


 
