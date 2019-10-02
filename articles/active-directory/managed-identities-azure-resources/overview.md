---
title: Identità gestite per le risorse di Azure
description: Panoramica delle identità gestite per le risorse di Azure.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: overview
ms.custom: mvc
ms.date: 09/26/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 596da9cfe0e914183bd3b2603ffa1047f1d9352b
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310016"
---
# <a name="what-is-managed-identities-for-azure-resources"></a>Informazioni sulle identità gestite per le risorse di Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Una difficoltà comune durante la creazione di applicazioni cloud è rappresentata dalla gestione delle credenziali presenti nel codice per l'autenticazione ai servizi cloud. Proteggere le credenziali è un'attività importante. In teoria, le credenziali non sono mai presenti nelle workstation di sviluppo e non vengono verificate nel controllo del codice sorgente. Azure Key Vault consente di archiviare in modo sicuro le credenziali, i segreti e altre chiavi, ma è necessario autenticare il codice in Key Vault per recuperarle. 

La funzionalità delle identità gestite per le risorse di Azure in Azure Active Directory (Azure AD) consente di risolvere questo problema. La funzionalità offre ai servizi di Azure un'identità gestita automaticamente in Azure AD. È possibile usare l'identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD, incluso Key Vault, senza inserire le credenziali nel codice.

La funzionalità delle identità gestite per le risorse di Azure è gratuita con le sottoscrizioni di Azure AD per Azure. Non sono previsti costi aggiuntivi.

> [!NOTE]
> Identità gestite per le risorse di Azure è il nuovo nome per il servizio precedentemente noto come identità del servizio gestita.

## <a name="terminology"></a>Terminologia

I termini seguenti vengono usati in tutta la documentazione relativa alle identità gestite per le risorse di Azure:

- **ID client**: un identificatore univoco generato da Azure AD che viene associato a un'entità servizio e applicazione durante il provisioning iniziale.
- **ID entità servizio**: l'ID dell'oggetto entità servizio per l'identità gestita che viene usato per concedere l'accesso basato sul ruolo a una risorsa di Azure.
- **Servizio metadati dell'istanza di Azure**: un endpoint REST accessibile a tutte le macchine virtuali IaaS create tramite Azure Resource Manager. L'endpoint è disponibile a un indirizzo IP non instradabile noto (169.254.169.254) a cui è possibile accedere solo dalla macchina virtuale.

## Funzionamento delle identità gestite per le risorse di Azure<a name="how-does-it-work"></a>

Sono disponibili due tipi di identità gestite:

- Un'**identità gestita assegnata dal sistema** viene abilitata direttamente in un'istanza del servizio di Azure. In caso di abilitazione dell'identità, Azure crea un'identità per l'istanza nel tenant di Azure AD considerato attendibile dalla sottoscrizione dell'istanza. Dopo la creazione dell'identità, viene effettuato il provisioning delle credenziali nell'istanza. Il ciclo di vita di un'identità assegnata dal sistema viene direttamente associato all'istanza del servizio di Azure in cui l'identità è abilitata. Se l'istanza viene eliminata, Azure pulisce automaticamente le credenziali e l'identità in Azure AD.
- Un'**identità gestita assegnata dall'utente** viene creata come risorsa di Azure autonoma. Tramite un processo di creazione, Azure crea un'identità nel tenant di Azure AD considerato attendibile dalla sottoscrizione in uso. Dopo la creazione, l'identità può essere assegnata a una o più istanze del servizio di Azure. Il ciclo di vita di un'identità assegnata dall'utente viene gestito separatamente dal ciclo di vita delle istanze del servizio di Azure a cui l'identità è assegnata.

Internamente, le identità gestite sono entità servizio di un tipo speciale, bloccate per essere usate solo con le risorse di Azure. Quando l'identità gestita viene eliminata, l'entità servizio corrispondente viene automaticamente rimossa. 

Il codice può usare un'identità gestita per richiedere token di accesso per i servizi che supportano l'autenticazione di Azure AD. Azure gestisce le credenziali usate dall'istanza del servizio in sequenza. 

Il diagramma seguente illustra il funzionamento delle identità del servizio gestite con macchine virtuali di Azure:

![Identità del servizio gestite e macchine virtuali di Azure](media/overview/msi-vm-vmextension-imds-example.png)

|  Proprietà    | Identità gestita assegnata dal sistema | Identità gestita assegnata dall'utente |
|------|----------------------------------|--------------------------------|
| Creazione |  Creata come parte di una risorsa di Azure (ad esempio, una macchina virtuale di Azure o servizio App di Azure) | Creata come risorsa di Azure autonoma |
| Ciclo di vita | Ciclo di vita condiviso con la risorsa di Azure con cui viene creata l'identità gestita. <br/> Quando viene eliminata la risorsa padre, viene eliminata anche l'identità gestita. | Ciclo di vita indipendente. <br/> Dev'essere eliminato in modo esplicito. |
| Condivisione tra risorse di Azure | Non può essere condivisa. <br/> Può essere associata solo a una singola risorsa di Azure. | Può essere condivisa <br/> La stessa identità gestita assegnata dall'utente può essere associata a più risorse di Azure. |
| Casi d'uso comuni | Carichi di lavoro che sono contenuti all'interno di una singola risorsa di Azure <br/> Carichi di lavoro per cui sono necessarie identità indipendenti. <br/> Ad esempio, un'applicazione che viene eseguita in una singola macchina virtuale | Carichi di lavoro che vengono eseguiti in più risorse e che possono condividere una singola identità. <br/> Carichi di lavoro che richiedono l'autorizzazione preliminare per una risorsa protetta come parte di un flusso di provisioning. <br/> Carichi di lavoro in cui le risorse vengono riciclate frequentemente, ma le autorizzazioni devono rimanere coerenti. <br/> Ad esempio, un carico di lavoro in cui più macchine virtuali devono accedere alla stessa risorsa | 

### <a name="how-a-system-assigned-managed-identity-works-with-an-azure-vm"></a>Funzionamento di un'identità gestita assegnata dal sistema con una macchina virtuale di Azure

1. Azure Resource Manager riceve una richiesta per abilitare l'identità gestita assegnata dal sistema in una macchina virtuale.

2. Azure Resource Manager crea un'entità servizio in Azure AD per l'identità della macchina virtuale. L'entità servizio viene creata nel tenant di Azure AD considerata attendibile dalla sottoscrizione.

3. Azure Resource Manager configura l'identità nella macchina virtuale aggiornando l'endpoint dell'identità del servizio metadati dell'istanza di Azure con l'ID client e il certificato dell'entità servizio.

4. Quando la VM ha un'identità, è possibile usare le informazioni dell'entità servizio per concedere alla VM l'accesso alle risorse di Azure. Per chiamare Azure Resource Manager, usare il controllo degli accessi in base al ruolo in Azure AD per assegnare il ruolo appropriato all'entità servizio della VM. Per chiamare Key Vault, concedere al codice l'accesso a un segreto specifico o a una chiave specifica in Key Vault.

5. Il codice in esecuzione nella macchina virtuale può richiedere un token all'endpoint servizio metadati dell'istanza di Azure, accessibile solo dall'interno della macchina virtuale: `http://169.254.169.254/metadata/identity/oauth2/token`
    - Il parametro della risorsa specifica il servizio a cui viene inviato il token. Per l'autenticazione in Azure Resource Manager, usare `resource=https://management.azure.com/`.
    - Il parametro della versione API specifica la versione del servizio metadati dell'istanza. Usare api-version=2018-02-01 o una versione successiva.

6. Viene effettuata una chiamata ad Azure AD per richiedere un token di accesso come specificato nel Passaggio 5, usando l'ID client e il certificato di cui è stata eseguita la configurazione nel Passaggio 3. Azure AD restituisce un token di accesso JSON Web.

7. Il codice invia il token di accesso in una chiamata a un servizio che supporta l'autenticazione di Azure AD.

### <a name="how-a-user-assigned-managed-identity-works-with-an-azure-vm"></a>Funzionamento di un'identità gestita assegnata dall'utente con una macchina virtuale di Azure

1. Azure Resource Manager riceve una richiesta per creare un'identità gestita assegnata dall'utente.

2. Azure Resource Manager crea un'entità servizio in Azure AD per l'identità gestita assegnata dall'utente. L'entità servizio viene creata nel tenant di Azure AD considerata attendibile dalla sottoscrizione.

3. Azure Resource Manager riceve una richiesta di configurazione dell'identità gestita assegnata dall'utente in una macchina virtuale e aggiorna l'endpoint dell'identità del servizio metadati dell'istanza di Azure con l'ID client e il certificato dell'entità servizio dell'identità gestita assegnata dall'utente.

4. Dopo la creazione dell'identità gestita assegnata dall'utente, usare le informazioni dell'entità servizio per concedere all'identità l'accesso alle risorse di Azure. Per chiamare Azure Resource Manager, usare il controllo degli accessi in base al ruolo in Azure AD per assegnare il ruolo appropriato all'entità servizio dell'identità assegnata dall'utente. Per chiamare Key Vault, concedere al codice l'accesso a un segreto specifico o a una chiave specifica in Key Vault.

   > [!Note]
   > È possibile eseguire questo passaggio anche prima del Passaggio 3.

5. Il codice in esecuzione nella macchina virtuale può richiedere un token all'endpoint dell'identità del servizio metadati dell'istanza di Azure, accessibile solo dall'interno della macchina virtuale: `http://169.254.169.254/metadata/identity/oauth2/token`
    - Il parametro della risorsa specifica il servizio a cui viene inviato il token. Per l'autenticazione in Azure Resource Manager, usare `resource=https://management.azure.com/`.
    - Il parametro dell'ID client specifica l'identità per cui viene richiesto il token. Questo valore è necessario per evitare ambiguità quando in una singola macchina virtuale sono presenti più identità assegnate dall'utente.
    - Il parametro relativo alla versione dell'API specifica la versione del servizio metadati dell'istanza di Azure. Usare `api-version=2018-02-01` o versione successiva.

6. Viene effettuata una chiamata ad Azure AD per richiedere un token di accesso come specificato nel Passaggio 5, usando l'ID client e il certificato di cui è stata eseguita la configurazione nel Passaggio 3. Azure AD restituisce un token di accesso JSON Web.
7. Il codice invia il token di accesso in una chiamata a un servizio che supporta l'autenticazione di Azure AD.

## <a name="how-can-i-use-managed-identities-for-azure-resources"></a>Come usare le identità gestite per le risorse di Azure

Per informazioni sull'uso delle identità gestite per l'accesso a diverse risorse di Azure, vedere queste esercitazioni.

> [!NOTE]
> Per altre informazioni sulle identità gestite, tra cui video con procedure dettagliate per diversi scenari supportati, vedere il corso [Implementing Managed Identities for Microsoft Azure Resources](https://www.pluralsight.com/courses/microsoft-azure-resources-managed-identities-implementing) (Implementazione delle identità gestite per le risorse di Microsoft Azure).

Informazioni sull'uso di un'identità gestita con una VM Windows:

* [Accedere ad Azure Data Lake Store](tutorial-windows-vm-access-datalake.md)
* [Accedere ad Azure Resource Manager](tutorial-windows-vm-access-arm.md)
* [Accedere ad Azure SQL](tutorial-windows-vm-access-sql.md)
* [Accedere ad Archiviazione di Azure con una chiave di accesso](tutorial-windows-vm-access-storage.md)
* [Accedere ad Archiviazione di Azure con firme di accesso condiviso](tutorial-windows-vm-access-storage-sas.md)
* [Accedere a risorse non di Azure AD con Azure Key Vault](tutorial-windows-vm-access-nonaad.md)

Informazioni sull'uso di un'identità gestita con una VM Linux:

* [Accedere ad Azure Data Lake Store](tutorial-linux-vm-access-datalake.md)
* [Accedere ad Azure Resource Manager](tutorial-linux-vm-access-arm.md)
* [Accedere ad Archiviazione di Azure con una chiave di accesso](tutorial-linux-vm-access-storage.md)
* [Accedere ad Archiviazione di Azure con firme di accesso condiviso](tutorial-linux-vm-access-storage-sas.md)
* [Accedere a risorse non di Azure AD con Azure Key Vault](tutorial-linux-vm-access-nonaad.md)
* [Accedere a Registro Azure Container](../../container-registry/container-registry-authentication-managed-identity.md)

Informazioni sull'uso di un'identità gestita con altri servizi di Azure:

* [Servizio app di Azure](/azure/app-service/overview-managed-identity)
* [Funzioni di Azure](/azure/app-service/overview-managed-identity)
* [App per la logica di Azure](/azure/logic-apps/create-managed-service-identity)
* [Bus di servizio di Azure](../../service-bus-messaging/service-bus-managed-service-identity.md)
* [Hub eventi di Azure](../../event-hubs/event-hubs-managed-service-identity.md)
* [Gestione API di Azure](../../api-management/api-management-howto-use-managed-service-identity.md)
* [Istanze di Azure Container](../../container-instances/container-instances-managed-identity.md)
* [Attività del Registro Azure Container](../../container-registry/container-registry-tasks-authentication-managed-identity.md)

## Servizi di Azure che supportano questa funzionalità<a name="which-azure-services-support-managed-identity"></a>

Le identità gestite per le risorse di Azure possono essere usate per eseguire l'autenticazione ai servizi che supportano l'autenticazione di Azure AD. Per un elenco dei servizi di Azure che supportano la funzionalità delle identità gestite per le risorse di Azure, vedere [Servizi che supportano le identità gestite per le risorse di Azure](services-support-msi.md).

## <a name="next-steps"></a>Passaggi successivi

È possibile iniziare a usare la funzionalità delle identità gestite per le risorse di Azure con le guide introduttive seguenti:

* [Usare un'identità gestita assegnata dal sistema per una macchina virtuale Windows per accedere a Resource Manager](tutorial-windows-vm-access-arm.md)
* [Usare un'identità gestita assegnata dal sistema per una macchina virtuale Linux per accedere a Resource Manager](tutorial-linux-vm-access-arm.md)
