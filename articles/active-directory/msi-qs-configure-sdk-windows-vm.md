---
title: "Come configurare una macchina virtuale Azure abilitata per l'identità del servizio gestito usando un Azure SDK"
description: "Istruzioni dettagliate per la configurazione e l'uso dell'identità del servizio gestito (MSI) in una macchina virtuale Azure usando un Azure SDK."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/28/2017
ms.author: bryanla
ms.openlocfilehash: 84fcb0181d635ecd8ebc68b31365fba9d6be21ed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-vm-managed-service-identity-msi-using-an-azure-sdk"></a>Configurare un'identità del servizio gestito (MSI) di macchina virtuale usando un Azure SDK

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Identità del servizio gestito offre servizi Azure con un'identità gestita automaticamente in Azure Active Directory (AD). È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

In questo articolo si apprende come abilitare e rimuovere l'Identità del servizio gestito per una VM di Azure usando un Azure SDK.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-msi-support"></a>Azure SDK con supporto per l'identità del servizio gestito 

Azure supporta più piattaforme di programmazione tramite una serie di [Azure SDK](https://azure.microsoft.com/downloads). Alcuni SDK sono stati aggiornati per supportare l'identità del servizio gestito e forniscono esempi che ne illustrano l'utilizzo. L'elenco viene aggiornato quando è disponibile supporto aggiuntivo:

| SDK | Esempio |
| --- | ------ | 
| .NET   | [Manage resource from an MSI-enabled VM](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) (Gestire le risorse da una macchina virtuale abilitata per l'identità del servizio gestito) |
| Java   | [Manage storage from an MSI-enabled VM](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/) (Gestire l'archiviazione da una macchina virtuale abilitata per l'identità del servizio gestito)|
| Node.js| [Creare una macchina virtuale con l'identità del servizio gestito abilitata](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [Creare una macchina virtuale con l'identità del servizio gestito abilitata](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Creare una macchina virtuale di Azure con un'identità del servizio gestito](https://azure.microsoft.com/resources/samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>Passaggi successivi

- Vedere gli articoli correlati nella configurazione dell'identità del servizio gestito per una macchina virtuale di Azure per imparare a usare anche il portale di Azure, PowerShell, l'interfaccia della riga di comando e i modelli di risorse.

Usare la sezione dei commenti seguente per fornire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.
