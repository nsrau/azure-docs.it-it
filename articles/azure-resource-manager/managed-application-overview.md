---
title: Panoramica delle applicazioni gestite di Azure | Microsoft Docs
description: Illustra i concetti relativi alle applicazioni gestite di Azure
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/09/2017
ms.author: gauravbh; tomfitz
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 51732474e7983827988f950d344d36745564dfd2
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---

# <a name="azure-managed-applications-overview"></a>Panoramica delle applicazioni gestite di Azure

Azure consente ai fornitori di offrire soluzioni ai clienti di tutto il mondo. Microsoft Azure Marketplace è una raccolta costituita da centinaia di modelli complessi e a più risorse, da fornitori primari e di terze parti. I clienti possono distribuire e iniziare a usare in pochi minuti le applicazioni PaaS e SaaS. Anche se offre un approccio ottimale per distribuire rapidamente un'offerta, la gestione e l'aggiornamento della soluzione rimane comunque a carico del cliente. Per i fornitori non sono disponibili funzionalità per addebitare ai clienti l'uso di un'applicazione oltre a quanto specificato nella fattura per l'immagine della macchina virtuale. I fornitori, inoltre, non possono in alcun modo impedire ai clienti di modificare risorse cruciali delle applicazioni e bloccare l'accesso alla proprietà intellettuale che costituisce un'applicazione. Le applicazioni gestite di Azure offrono una soluzione per questi problemi. 

Un'applicazione gestita è simile a un modello di soluzione del Marketplace, con una differenza essenziale. In un'applicazione gestita le risorse vengono sottoposte a provisioning in un gruppo di risorse gestito dal fornitore. Il gruppo di risorse è presente nella sottoscrizione del cliente, ma un'identità del tenant del fornitore ha accesso al gruppo di risorse.

## <a name="advantages-of-managed-applications"></a>Vantaggi delle applicazioni gestite

Le applicazioni gestite consentono ai provider di servizi gestiti (MSP), ai fornitori di software indipendenti (ISV) e ai team IT centrali aziendali di offrire soluzioni attraverso il Marketplace o il catalogo di servizi. Sebbene i clienti distribuiscano le applicazioni gestite nelle proprie sottoscrizioni, non devono occuparsi della gestione, degli aggiornamenti o della manutenzione. I fornitori gestiscono e supportano queste applicazioni. Di conseguenza, i clienti non devono sviluppare una conoscenza approfondita delle caratteristiche delle applicazioni per gestirle. I clienti possono acquisire automaticamente gli aggiornamenti delle applicazioni senza doversi preoccupare di individuare, diagnosticare e risolvere eventuali problemi.

Per i fornitori e i provider, le applicazioni gestite creano un canale non solo per vendere infrastrutture e software attraverso il Marketplace, ma anche per offrire servizi e supporto operativo ai clienti di Azure. Ciò consente ai fornitori di addebitare i costi ai clienti usando il sistema di fatturazione di Azure e di usare i modelli per gestire il ciclo di vita delle applicazioni distribuite. Queste soluzioni sono indipendenti e "sigillate" per il cliente, quindi i fornitori possono offrire un servizio di qualità elevata. Questo approccio presenta vantaggi non solo per i fornitori PaaS e SaaS ma anche per i team delle piattaforme centrali aziendali e gli integratori di sistemi che vogliono creare pacchetti delle proprie soluzioni e rivenderli.

## <a name="managed-application-types"></a>Tipi di applicazioni gestite
Sono disponibili due categorie di applicazioni gestite di Azure: catalogo di servizi e Marketplace.
 
### <a name="service-catalog"></a>Catalogo di servizi  

Il catalogo di servizi consente alle organizzazioni di creare per i propri utenti un catalogo di soluzioni approvate per Azure. Gestire un catalogo di soluzioni di questo tipo è utile per i team IT centrali delle aziende. Consente di garantire la conformità con determinati standard aziendali e di offrire soluzioni ottimali per l'organizzazione. I team sono in grado di controllare, aggiornare e gestire le applicazioni. Consente ai dipendenti dell'organizzazione di individuare facilmente la vasta gamma di applicazioni consigliate e approvate dal reparto IT. I clienti vedono solo le applicazioni gestite del catalogo di servizi che hanno creato o che altri utenti dell'organizzazione hanno condiviso con loro.
 
Per informazioni sulla pubblicazione di un'applicazione gestita del catalogo di servizi, vedere [Creare e pubblicare un'applicazione gestita del catalogo di servizi](managed-application-publishing.md).
 
Per informazioni sull'uso delle applicazioni gestite del catalogo di servizi, vedere [Utilizzare un'applicazione gestita del catalogo di servizi](managed-application-consumption.md).
 
### <a name="marketplace"></a>Marketplace

Le applicazioni gestite sono disponibili attraverso il Marketplace nel portale di Azure. Dopo essere state pubblicate dal fornitore, queste applicazioni sono disponibili per tutti gli utenti interni o esterni all'organizzazione. Questo approccio consente ai provider di servizi gestiti, ai fornitori di software indipendenti e agli integratori di sistemi di offrire le proprie soluzioni a tutti i clienti di Azure. I clienti hanno la possibilità di usufruire di soluzioni complesse senza dover investire nella comprensione e gestione delle soluzioni. Attualmente il server di pubblicazione può rendere disponibile l'offerta come applicazione gestita o come modello di soluzione non gestito. I componenti principali della pubblicazione di un'applicazione gestita includono i file di modello, che descrivono le risorse di cui si effettua il provisioning, e il file di definizione dell'interfaccia utente, che descrive la modalità di visualizzazione nel portale degli input richiesti per il provisioning di queste risorse. I file necessari sono contenuti in un file ZIP e vengono caricati attraverso il portale di pubblicazione.
 
Per informazioni sulla pubblicazione di applicazioni gestite nel Marketplace, vedere [Azure Managed Applications in the Marketplace](managed-application-author-marketplace.md) (Applicazioni gestite di Azure nel Marketplace).

Per informazioni sull'uso di un'applicazione gestita dal Marketplace, vedere [Consume Azure managed applications in the Marketplace](managed-application-consume-marketplace.md) (Uso delle applicazioni gestite di Azure nel Marketplace).

## <a name="key-concepts"></a>Concetti chiave

### <a name="managed-resource-group"></a>Gruppo di risorse gestite
Gruppo di risorse in cui vengono create tutte le risorse di Azure sottoposte a provisioning nel modello. Se, ad esempio, l'appliance crea un account di archiviazione, questo gruppo di risorse contiene la risorsa dell'account di archiviazione. Non contiene le risorse dell'appliance.

### <a name="appliance-package"></a>Pacchetto dell'appliance
L'autore crea un pacchetto che contiene i file del modello e il file createUIDefinition. In particolare, contiene i file seguenti:

- **applianceMainTemplate.json**: file modello che definisce tutte le risorse sottoposte a provisioning dall'appliance. Questo file è un file di modello normale usato per creare le risorse.

- **MainTemplate.json**: file modello che definisce la risorsa dell'appliance (Microsoft.Solutions/appliances). Una proprietà essenziale definita in questa risorsa è ManagedResourceGroupId. Questa proprietà indica il gruppo di risorse usato per l'hosting delle risorse effettive definite in applianceMainTemplate.json.

- **applianceCreateUIDefinition.json**: questo file illustra la modalità di rendering dell'interfaccia utente per i parametri definiti nel modello.

### <a name="authorization"></a>Authorization
L'autore deve specificare le autorizzazioni richieste dal fornitore per gestire le risorse per conto del cliente. Questa autorizzazione è applicabile al gruppo di risorse gestite. Impostare i valori seguenti:

- **PrincipalID**: identificatore di Azure AD per l'utente, il gruppo o l'applicazione da usare per concedere l'accesso al gruppo di risorse gestite. Questo identificatore appartiene al tenant dell'autore.

- **RoleDefinitionID**: identificatore di Azure AD per il ruolo assegnato all'ID entità precedente. Può essere uno dei ruoli RBAC predefiniti disponibili nel tenant dell'autore. Per altre informazioni, vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md).

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulla pubblicazione di applicazioni gestite nel Marketplace, vedere [Azure Managed Applications in the Marketplace](managed-application-author-marketplace.md) (Applicazioni gestite di Azure nel Marketplace).
* Per informazioni sull'uso di un'applicazione gestita dal Marketplace, vedere [Consume Azure managed applications in the Marketplace](managed-application-consume-marketplace.md) (Uso delle applicazioni gestite di Azure nel Marketplace).
* Per informazioni sulla pubblicazione di un'applicazione gestita del catalogo di servizi, vedere [Creare e pubblicare un'applicazione gestita di Azure](managed-application-publishing.md).
* Per informazioni sull'uso delle applicazioni gestite del catalogo di servizi, vedere [Utilizzare un'applicazione gestita di Azure](managed-application-consumption.md).
* Per creare un file di definizione dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](managed-application-createuidefinition-overview.md).
