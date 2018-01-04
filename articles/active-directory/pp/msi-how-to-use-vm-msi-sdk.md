---
title: "L'uso di un utente assegnato gestiti identità del servizio da Azure SDK in una macchina virtuale"
description: Esempi di codice per l'utilizzo di Azure SDK con un file MSI assegnati dall'utente in una macchina virtuale.
services: active-directory
documentationcenter: 
author: BryanLa
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: f9a31a0500a6f5f1c49fc45d5811e28788e6f2b1
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/22/2017
---
# <a name="use-azure-sdks-with-a-user-assigned-managed-service-identity-msi"></a>Usare Azure SDK con un utente assegnato gestiti servizio identità (MSI)

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]Questo articolo fornisce un elenco di esempi di SDK, che illustrano l'utilizzo del supporto di loro rispettivi Azure SDK per MSI assegnati dall'utente.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

> [!IMPORTANT]
> - Tutti gli esempi di codice e script in questo articolo presuppongono che il client sia in esecuzione in una macchina virtuale abilitata per l'identità del servizio gestito. Usare la funzionalità di connessione alla macchina virtuale nel portale di Azure per connettersi in remoto alla macchina virtuale. Per informazioni dettagliate sull'abilitazione di estensione MSI in una macchina virtuale, vedere [configurare una macchina virtuale gestita servizio identità (MSI) usando l'interfaccia CLI di Azure](msi-qs-configure-cli-windows-vm.md), o uno degli articoli variant (tramite PowerShell, il portale di Azure, un modello o un SDK di Azure). 

## <a name="sdk-code-samples"></a>Esempi di codice SDK

| SDK             | Esempio di codice |
| --------------- | ----------- |
| Python          | [Use MSI to authenticate simply from inside a VM](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) (Usare l'identità del servizio gestito per eseguire in modo semplice l'autenticazione in una macchina virtuale) |
| Ruby            | [Manage resources from an MSI-enabled VM](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) (Gestire le risorse da una macchina virtuale abilitata per l'identità del servizio gestito) |

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Azure SDK](https://azure.microsoft.com/downloads/) per l'elenco completo delle risorse di Azure SDK, inclusi i download di librerie, la documentazione e altro ancora.

Usare la sezione dei commenti seguente per fornire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.








