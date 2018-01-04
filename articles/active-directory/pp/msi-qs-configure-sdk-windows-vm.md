---
title: Come configurare un file MSI assegnati dall'utente per una macchina virtuale di Azure utilizzando un SDK di Azure
description: "Passaggio da istruzioni dettagliate per la configurazione di un utente assegnato gestiti servizio identità (MSI) per una macchina virtuale di Azure, utilizzando un SDK di Azure."
services: active-directory
documentationcenter: 
author: bryanla
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 19b6179803b8de4102818c1522b00e6b4881d0f0
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/22/2017
---
# <a name="configure-a-user-assigned-managed-service-identity-msi-for-a-vm-using-an-azure-sdk"></a>Configurare un utente assegnato gestiti servizio identità (MSI) per una macchina virtuale con un SDK di Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Identità del servizio gestito fornisce servizi di Azure con un'identità gestita in Azure Active Directory. È possibile utilizzare questa identità di autenticazione ai servizi che supportano l'autenticazione di Azure AD, senza la necessità di credenziali nel codice. 

In questo articolo informazioni su come attivare e rimuovere un file MSI assegnati dall'utente per una macchina virtuale di Azure, utilizzando un SDK di Azure.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

## <a name="azure-sdks-with-msi-support"></a>Azure SDK con supporto per l'identità del servizio gestito 

Azure supporta più piattaforme di programmazione tramite una serie di [Azure SDK](https://azure.microsoft.com/downloads). Alcuni SDK sono stati aggiornati per supportare l'identità del servizio gestito e forniscono esempi che ne illustrano l'utilizzo. L'elenco viene aggiornato quando è disponibile supporto aggiuntivo:

| SDK | Esempio |
| --- | ------ | 
| Python | [Creare una macchina virtuale con l'identità del servizio gestito abilitata](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Creare una macchina virtuale di Azure con un'identità del servizio gestito](https://azure.microsoft.com/resources/samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>Passaggi successivi

- Articoli correlati in "Configurare MSI per una macchina virtuale di Azure", per informazioni su come configurare un file MSI assegnati dall'utente in una macchina virtuale di Azure, vedere.

Usare la sezione dei commenti seguente per fornire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.
