---
title: Identità gestite per le risorse di Azure
description: Panoramica delle identità gestite per le risorse di Azure.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: overview
ms.custom: mvc
ms.date: 10/06/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 728ca38cc3ef3bf989a75d757c69f7ca1993d82d
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91803118"
---
# <a name="what-are-managed-identities-for-azure-resources"></a>Informazioni sulle identità gestite per le risorse di Azure

Uno dei problemi a cui devono far fronte gli sviluppatori riguarda la gestione di segreti e credenziali per proteggere le comunicazioni tra servizi diversi. In Azure le identità gestite eliminano la necessità di gestire le credenziali fornendo un'identità per la risorsa di Azure in Azure AD che è possibile usare per ottenere i token di Azure Active Directory (Azure AD). Ciò consente anche di accedere ad [Azure Key Vault](../../key-vault/general/overview.md), in cui gli sviluppatori possono archiviare le credenziali in modo sicuro. Le identità gestite per le risorse di Azure risolvono questo problema fornendo servizi di Azure con un'identità gestita automaticamente in Azure AD.

In quali casi è possibile usare un'identità gestita?

   > [!VIDEO https://www.youtube.com/embed/5lqayO_oeEo]

Ecco alcuni vantaggi derivanti dall'uso delle identità gestite:

- Non è necessario gestire le credenziali. Le credenziali non sono neanche accessibili.
- È possibile usare le identità gestite per eseguire l'autenticazione con qualsiasi servizio di Azure che supporti l'autenticazione di Azure AD, incluso Azure Key Vault.
- Le identità gestite possono essere usate senza costi aggiuntivi.

> [!NOTE]
> Identità gestite per le risorse di Azure è il nuovo nome per il servizio precedentemente noto come identità del servizio gestita.

## <a name="managed-identity-types"></a>Tipi di identità gestita

Sono disponibili due tipi di identità gestite:

- **Assegnata dal sistema** Alcuni servizi di Azure consentono di abilitare un'identità gestita direttamente in un'istanza del servizio. Quando si abilita un'identità gestita assegnata dal sistema, viene creata un'identità in Azure AD associata al ciclo di vita di quell'istanza del servizio. Quindi quando la risorsa viene eliminata, Azure elimina automaticamente anche l'identità. Per impostazione predefinita, solo questa specifica risorsa di Azure può usare questa identità per richiedere token ad Azure AD.
- **Assegnata dall'utente** È anche possibile creare un'identità gestita come risorsa di Azure autonoma. È possibile [creare un'identità gestita assegnata dall'utente](how-to-manage-ua-identity-portal.md) e assegnarla a una o più istanze di un servizio di Azure. Le identità gestite assegnate dall'utente vengono gestite separatamente rispetto alle risorse che le usano. </br></br>

  > [!VIDEO https://www.youtube.com/embed/OzqpxeD3fG0]

La tabella seguente illustra le differenze tra i due tipi di identità gestite.

|  Proprietà    | Identità gestita assegnata dal sistema | Identità gestita assegnata dall'utente |
|------|----------------------------------|--------------------------------|
| Creazione |  Creata come parte di una risorsa di Azure (ad esempio, una macchina virtuale di Azure o servizio App di Azure) | Creata come risorsa di Azure autonoma |
| Ciclo di vita | Ciclo di vita condiviso con la risorsa di Azure con cui viene creata l'identità gestita. <br/> Quando viene eliminata la risorsa padre, viene eliminata anche l'identità gestita. | Ciclo di vita indipendente. <br/> Dev'essere eliminato in modo esplicito. |
| Condivisione tra risorse di Azure | Non può essere condivisa. <br/> Può essere associata solo a una singola risorsa di Azure. | Può essere condivisa <br/> La stessa identità gestita assegnata dall'utente può essere associata a più risorse di Azure. |
| Casi d'uso comuni | Carichi di lavoro che sono contenuti all'interno di una singola risorsa di Azure <br/> Carichi di lavoro per cui sono necessarie identità indipendenti. <br/> Ad esempio, un'applicazione che viene eseguita in una singola macchina virtuale | Carichi di lavoro che vengono eseguiti in più risorse e che possono condividere una singola identità. <br/> Carichi di lavoro che richiedono l'autorizzazione preliminare per una risorsa protetta come parte di un flusso di provisioning. <br/> Carichi di lavoro in cui le risorse vengono riciclate frequentemente, ma le autorizzazioni devono rimanere coerenti. <br/> Ad esempio, un carico di lavoro in cui più macchine virtuali devono accedere alla stessa risorsa |

>[!IMPORTANT]
>Indipendentemente dal tipo di identità scelto, un'identità gestita è un'entità servizio di un tipo speciale che è possibile usare solo con le risorse di Azure. Quando l'identità gestita viene eliminata, l'entità servizio corrispondente viene automaticamente rimossa.

## <a name="how-can-i-use-managed-identities-for-azure-resources"></a>Come usare le identità gestite per le risorse di Azure

![Ecco alcuni esempi di come uno sviluppatore può usare le identità gestite per ottenere l'accesso a risorse dal codice senza gestire le informazioni sull'autenticazione](media/overview/azure-managed-identities-examples.png)

## <a name="what-azure-services-support-the-feature"></a>Servizi di Azure che supportano questa funzionalità<a name="which-azure-services-support-managed-identity"></a>

Le identità gestite per le risorse di Azure possono essere usate per eseguire l'autenticazione ai servizi che supportano l'autenticazione di Azure AD. Per un elenco dei servizi di Azure che supportano la funzionalità delle identità gestite per le risorse di Azure, vedere [Servizi che supportano le identità gestite per le risorse di Azure](./services-support-managed-identities.md).

## <a name="next-steps"></a>Passaggi successivi

* [Usare un'identità gestita assegnata dal sistema per una macchina virtuale Windows per accedere a Resource Manager](tutorial-windows-vm-access-arm.md)
* [Usare un'identità gestita assegnata dal sistema per una macchina virtuale Linux per accedere a Resource Manager](tutorial-linux-vm-access-arm.md)
* [Come usare le identità gestite nel servizio app e in Funzioni di Azure](../../app-service/overview-managed-identity.md)
* [Come usare identità gestite con Istanze di Azure Container](../../container-instances/container-instances-managed-identity.md)
* [Implementazione delle identità gestite per le risorse di Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-resources-managed-identities-implementing).