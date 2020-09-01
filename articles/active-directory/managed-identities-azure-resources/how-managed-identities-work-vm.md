---
title: Come funzionano le identità gestite per le risorse di Azure le macchine virtuali di Azure
description: Descrizione di come funzionano le identità gestite per le risorse di Azure con le macchine virtuali di Azure.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.custom: mvc
ms.date: 06/11/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: b93f45b05e6d7773afc2f750fd1a9a034c01ca1e
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2020
ms.locfileid: "89178672"
---
# <a name="how-managed-identities-for-azure-resources-work-with-azure-virtual-machines"></a>Come funzionano le identità gestite per le risorse di Azure con le macchine virtuali di Azure

Le identità gestite per le risorse di Azure offrono ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice.

Questo articolo illustra come funzionano le identità gestite con le macchine virtuali di Azure.


## <a name="how-it-works"></a>Funzionamento

Internamente, le identità gestite sono entità servizio di un tipo speciale, che possono essere usate solo con le risorse di Azure. Quando l'identità gestita viene eliminata, l'entità servizio corrispondente viene automaticamente rimossa.
Inoltre, quando viene creata un'identità assegnata dall'utente o dal sistema, il provider di risorse di Identità gestita emette un certificato internamente per tale identità. 

Il codice può usare un'identità gestita per richiedere token di accesso per i servizi che supportano l'autenticazione di Azure AD. Azure gestisce le credenziali usate dall'istanza del servizio in sequenza. 

Il diagramma seguente illustra il funzionamento delle identità del servizio gestite con macchine virtuali di Azure:

![Identità del servizio gestite e macchine virtuali di Azure](media/how-managed-identities-work-vm/data-flow.png)

|  Proprietà    | Identità gestita assegnata dal sistema | Identità gestita assegnata dall'utente |
|------|----------------------------------|--------------------------------|
| Creazione |  Creata come parte di una risorsa di Azure (ad esempio, una macchina virtuale di Azure o Servizio app di Azure). | Creata come risorsa di Azure autonoma. |
| Ciclo di vita | Ciclo di vita condiviso con la risorsa di Azure con cui viene creata l'identità gestita. <br/> Quando viene eliminata la risorsa padre, viene eliminata anche l'identità gestita. | Ciclo di vita indipendente. <br/> Dev'essere eliminato in modo esplicito. |
| Condivisione tra risorse di Azure | Non può essere condivisa. <br/> Può essere associata solo a una singola risorsa di Azure. | Può essere condivisa. <br/> La stessa identità gestita assegnata dall'utente può essere associata a più risorse di Azure. |
| Casi d'uso comuni | Carichi di lavoro che sono contenuti all'interno di una singola risorsa di Azure. <br/> Carichi di lavoro per cui sono necessarie identità indipendenti. <br/> Ad esempio, un'applicazione che viene eseguita in una singola macchina virtuale | Carichi di lavoro che vengono eseguiti in più risorse e che possono condividere una singola identità. <br/> Carichi di lavoro che richiedono l'autorizzazione preliminare per una risorsa protetta come parte di un flusso di provisioning. <br/> Carichi di lavoro in cui le risorse vengono riciclate frequentemente, ma le autorizzazioni devono rimanere coerenti. <br/> Ad esempio, un carico di lavoro in cui più macchine virtuali devono accedere alla stessa risorsa |

## <a name="system-assigned-managed-identity"></a>Identità gestita assegnata dal sistema

1. Azure Resource Manager riceve una richiesta per abilitare l'identità gestita assegnata dal sistema in una macchina virtuale.

2. Azure Resource Manager crea un'entità servizio in Azure AD per l'identità della macchina virtuale. L'entità servizio viene creata nel tenant di Azure AD considerata attendibile dalla sottoscrizione.

3. Azure Resource Manager configura l'identità nella macchina virtuale aggiornando l'endpoint dell'identità del servizio metadati dell'istanza di Azure con l'ID client e il certificato dell'entità servizio.

4. Quando la VM ha un'identità, è possibile usare le informazioni dell'entità servizio per concedere alla VM l'accesso alle risorse di Azure. Per chiamare Azure Resource Manager, usare il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per assegnare il ruolo appropriato all'entità servizio della macchina virtuale. Per chiamare Key Vault, concedere al codice l'accesso a un segreto specifico o a una chiave specifica in Key Vault.

5. Il codice in esecuzione nella macchina virtuale può richiedere un token all'endpoint servizio metadati dell'istanza di Azure, accessibile solo dall'interno della macchina virtuale: `http://169.254.169.254/metadata/identity/oauth2/token`
    - Il parametro della risorsa specifica il servizio a cui viene inviato il token. Per l'autenticazione in Azure Resource Manager, usare `resource=https://management.azure.com/`.
    - Il parametro della versione API specifica la versione del servizio metadati dell'istanza. Usare api-version=2018-02-01 o una versione successiva.

6. Viene effettuata una chiamata ad Azure AD per richiedere un token di accesso come specificato nel Passaggio 5, usando l'ID client e il certificato di cui è stata eseguita la configurazione nel Passaggio 3. Azure AD restituisce un token di accesso JSON Web.

7. Il codice invia il token di accesso in una chiamata a un servizio che supporta l'autenticazione di Azure AD.

## <a name="user-assigned-managed-identity"></a>Identità gestita assegnata dall'utente

1. Azure Resource Manager riceve una richiesta per creare un'identità gestita assegnata dall'utente.

2. Azure Resource Manager crea un'entità servizio in Azure AD per l'identità gestita assegnata dall'utente. L'entità servizio viene creata nel tenant di Azure AD considerata attendibile dalla sottoscrizione.

3. Azure Resource Manager riceve una richiesta di configurazione dell'identità gestita assegnata dall'utente in una macchina virtuale e aggiorna l'endpoint dell'identità del servizio metadati dell'istanza di Azure con l'ID client e il certificato dell'entità servizio dell'identità gestita assegnata dall'utente.

4. Dopo la creazione dell'identità gestita assegnata dall'utente, usare le informazioni dell'entità servizio per concedere all'identità l'accesso alle risorse di Azure. Per chiamare Azure Resource Manager, usare il controllo degli accessi in base al ruolo di Azure per assegnare il ruolo appropriato all'entità servizio dell'identità assegnata dall'utente. Per chiamare Key Vault, concedere al codice l'accesso a un segreto specifico o a una chiave specifica in Key Vault.

   > [!Note]
   > È possibile eseguire questo passaggio anche prima del Passaggio 3.

5. Il codice in esecuzione nella macchina virtuale può richiedere un token all'endpoint dell'identità del servizio metadati dell'istanza di Azure, accessibile solo dall'interno della macchina virtuale: `http://169.254.169.254/metadata/identity/oauth2/token`
    - Il parametro della risorsa specifica il servizio a cui viene inviato il token. Per l'autenticazione in Azure Resource Manager, usare `resource=https://management.azure.com/`.
    - Il parametro dell'ID client specifica l'identità per cui viene richiesto il token. Questo valore è necessario per evitare ambiguità quando in una singola macchina virtuale sono presenti più identità assegnate dall'utente.
    - Il parametro relativo alla versione dell'API specifica la versione del servizio metadati dell'istanza di Azure. Usare `api-version=2018-02-01` o versione successiva.

6. Viene effettuata una chiamata ad Azure AD per richiedere un token di accesso come specificato nel Passaggio 5, usando l'ID client e il certificato di cui è stata eseguita la configurazione nel Passaggio 3. Azure AD restituisce un token di accesso JSON Web.
7. Il codice invia il token di accesso in una chiamata a un servizio che supporta l'autenticazione di Azure AD.


## <a name="next-steps"></a>Passaggi successivi

È possibile iniziare a usare la funzionalità delle identità gestite per le risorse di Azure con le guide introduttive seguenti:

* [Usare un'identità gestita assegnata dal sistema per una macchina virtuale Windows per accedere a Resource Manager](tutorial-windows-vm-access-arm.md)
* [Usare un'identità gestita assegnata dal sistema per una macchina virtuale Linux per accedere a Resource Manager](tutorial-linux-vm-access-arm.md)
