---
title: "Come configurare un'identità del servizio gestito assegnata dall'utente per una macchina virtuale di Azure tramite Azure SDK"
description: "Istruzioni dettagliate per la configurazione di un'identità del servizio gestito assegnata dall'utente per una macchina virtuale di Azure tramite Azure SDK."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 097304162b85599acd1f4591091f986a646ebc2a
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2018
---
# <a name="configure-a-user-assigned-managed-service-identity-msi-for-a-vm-using-an-azure-sdk"></a>Configurare un'identità del servizio gestito assegnata dall'utente per una macchina virtuale tramite Azure SDK

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

L'identità del servizio gestito offre servizi di Azure con un'identità gestita in Azure Active Directory. È possibile usare questa identità per l'autenticazione ai servizi che supportano l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

In questo articolo viene descritto come abilitare e rimuovere un'identità del servizio gestito assegnata dall'utente per una macchina virtuale di Azure tramite Azure SDK.

## <a name="prerequisites"></a>prerequisiti

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

## <a name="azure-sdks-with-msi-support"></a>Azure SDK con supporto per l'identità del servizio gestito 

Azure supporta più piattaforme di programmazione tramite una serie di [Azure SDK](https://azure.microsoft.com/downloads). Alcuni SDK sono stati aggiornati per supportare l'identità del servizio gestito e forniscono esempi che ne illustrano l'utilizzo. L'elenco viene aggiornato quando è disponibile supporto aggiuntivo:

| SDK | Esempio |
| --- | ------ | 
| Python | [Creare una macchina virtuale con l'identità del servizio gestito abilitata](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Creare una macchina virtuale di Azure con un'identità del servizio gestito](https://azure.microsoft.com/resources/samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come configurare un'identità del servizio gestito assegnata dall'utente in una macchina virtuale di Azure, vedere gli articoli correlati relativi a questo argomento.

Usare la sezione dei commenti seguente per fornire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.
