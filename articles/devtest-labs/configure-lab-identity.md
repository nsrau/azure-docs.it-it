---
title: Configurare un'identità Lab in Azure DevTest Labs
description: Informazioni su come configurare un'identità Lab in Azure DevTest.
ms.topic: article
ms.date: 08/20/2020
ms.openlocfilehash: a652eb5751f9b723911a1c1baaaaf9860febc5b6
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2020
ms.locfileid: "88719687"
---
# <a name="configure-a-lab-identity"></a>Configurare un'identità Lab

Una difficoltà comune durante la creazione di applicazioni cloud è rappresentata dalla gestione delle credenziali presenti nel codice per l'autenticazione ai servizi cloud. Proteggere le credenziali è un'attività importante. In teoria, le credenziali non sono mai presenti nelle workstation di sviluppo e non vengono verificate nel controllo del codice sorgente. Azure Key Vault consente di archiviare in modo sicuro credenziali, segreti e altre chiavi, ma il codice deve eseguire l'autenticazione a Key Vault per recuperarli. 

La funzionalità identità gestite per le risorse di Azure in Azure Active Directory (Azure AD) consente di risolvere questo problema. La funzionalità offre ai servizi di Azure un'identità gestita automaticamente in Azure AD. È possibile usare l'identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD, incluso Key Vault, senza inserire le credenziali nel codice. Altre informazioni sulle [identità gestite in Azure](../active-directory/managed-identities-azure-resources/overview.md). 

Sono disponibili due tipi di identità gestite: 

## <a name="system-assigned-managed-identity"></a>Identità gestita assegnata dal sistema  

Un' **identità gestita assegnata dal sistema**   è abilitata direttamente in un'istanza del servizio di Azure. In caso di abilitazione dell'identità, Azure crea un'identità per l'istanza nel tenant di Azure AD considerato attendibile dalla sottoscrizione dell'istanza. Dopo la creazione dell'identità, viene effettuato il provisioning delle credenziali nell'istanza. Il ciclo di vita di un'identità assegnata dal sistema viene direttamente associato all'istanza del servizio di Azure in cui l'identità è abilitata. Se l'istanza viene eliminata, Azure pulisce automaticamente le credenziali e l'identità in Azure AD. 

### <a name="scenarios-for-using-labs-system-assigned-identity"></a>Scenari per l'uso dell'identità assegnata dal sistema Lab  

Ogni DevTest Labs viene creato con un'identità assegnata dal sistema che rimane valida per tutta la durata del Lab. L'identità assegnata dal sistema viene utilizzata per gli scopi seguenti:  

- Tutte le distribuzioni basate su [Azure Resource Manager](devtest-lab-create-environment-from-arm.md) utilizzate per avviare più macchine virtuali e/o piattaforma come ambiente del servizio verranno eseguite utilizzando l'identità assegnata dal sistema Lab  
- La crittografia del disco per i dischi Lab con una chiave gestita dal cliente è supportata tramite l'identità assegnata dal sistema Lab. Fornendo l'accesso esplicito all'identità del Lab per accedere al set di crittografia del disco, il Lab può crittografare tutti i dischi delle macchine virtuali per conto dell'utente. Altre informazioni su [come abilitare la crittografia del disco](encrypt-disks-customer-managed-keys.md) per i dischi Lab usando una chiave gestita dal cliente.  

### <a name="configure-identity"></a>Configurare l'identità

Questa sezione illustra come configurare i criteri di identità del Lab.

> [!NOTE]
> Per i Lab creati prima della 8/10/2020, l'identità assegnata dal sistema verrà impostata su off. In qualità di proprietario del Lab, è possibile attivarlo, nel caso in cui si intenda usare Labs per le finalità elencate nella sezione precedente.  
>
> Per i nuovi Lab creati dopo 8/10/2020, l'identità assegnata dal sistema Lab è impostata su on per impostazione predefinita e il proprietario del Lab non sarà in grado di disattivare questo per il ciclo di vita del Lab.  

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Cercare **DevTest Labs**.
1. Dall'elenco dei Lab selezionare il Lab desiderato.
1. Selezionare **configurazione e criteri**  ->  **identità (anteprima)**. 

> [!div class="mx-imgBorder"]
> ![Configurare l'identità](./media/configure-lab-identity/configure-identity.png)

## <a name="user-assigned-managed-identity"></a>Identità gestita assegnata dall'utente  

Un'identità gestita assegnata dall'utente viene creata come risorsa di Azure autonoma. Tramite un processo di creazione, Azure crea un'identità nel tenant di Azure AD considerato attendibile dalla sottoscrizione in uso. Dopo la creazione, l'identità può essere assegnata a una o più istanze del servizio di Azure. Il ciclo di vita di un'identità assegnata dall'utente viene gestito separatamente dal ciclo di vita delle istanze del servizio di Azure a cui l'identità è assegnata. 

DevTest Labs supporta le identità assegnate dall'utente per le macchine virtuali e gli ambienti basati su Azure Resource Manager.  Per altre informazioni, vedere gli argomenti seguenti:

- [Aggiungere un'identità assegnata dall'utente per distribuire ambienti Lab Azure Resource Manager](use-managed-identities-environments.md)
- [Aggiungere identità assegnate dall'utente per distribuire macchine virtuali Lab](enable-managed-identities-lab-vms.md)

## <a name="next-steps"></a>Passaggi successivi

Vedere [configurare la gestione dei costi](devtest-lab-configure-cost-management.md)