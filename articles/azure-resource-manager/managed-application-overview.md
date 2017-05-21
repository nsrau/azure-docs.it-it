---
title: Panoramica di Applicazione gestita di Azure | Microsoft Docs
description: Illustra i concetti relativi ad Applicazione gestita di Azure
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/11/2017
ms.author: gauravbh; tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: a4348a4d2348d744c03ad3a89d0548526fa2f9f8
ms.contentlocale: it-it
ms.lasthandoff: 05/11/2017


---
# <a name="azure-managed-applications-overview"></a>Panoramica di Applicazione gestita di Azure

Azure offre un Marketplace affidabile in cui i fornitori di software indipendenti e le startup possono proporre le proprie soluzioni ai clienti in tutto il mondo. Azure Marketplace è una raccolta costituita da centinaia di modelli complessi e a più risorse, da fornitori primari e di terze parti. I clienti possono distribuire e iniziare a usare in pochi minuti le applicazioni PaaS e SaaS. Anche se offre un approccio ottimale per distribuire rapidamente un'offerta, la gestione e l'aggiornamento della soluzione rimane comunque a carico del cliente. Per i fornitori non sono disponibili funzionalità per addebitare ai clienti l'uso di un'applicazione oltre a quanto specificato nella fattura per l'immagine della macchina virtuale. I fornitori, inoltre, non possono in alcun modo impedire ai clienti di modificare risorse cruciali delle applicazioni e bloccare l'accesso alla proprietà intellettuale che costituisce un'applicazione. Applicazione gestita di Azure offre una soluzione per questi problemi. 

## <a name="advantages-of-managed-applications"></a>Vantaggi delle applicazioni gestite

Applicazione gestita di Azure offre un ecosistema che consente ai fornitori di rendere disponibili i servizi PaaS o SaaS come applicazioni autosufficienti. I clienti distribuiscono le applicazioni gestite nelle proprie sottoscrizioni, ma i fornitori le possono gestire. Ciò consente ai fornitori di addebitare i costi ai clienti tramite il sistema di fatturazione di Azure e di usare i modelli per gestire il ciclo di vita delle applicazioni distribuite. Consente anche ai clienti di acquisire automaticamente gli aggiornamenti e pagare per ottenere il supporto e la manutenzione. I clienti non devono mantenere o gestire personalmente l'applicazione oppure diagnosticare e risolvere i problemi in caso di errori dell'applicazione.

Un ecosistema di questo tipo in Azure presenta vantaggi non solo per i fornitori PaaS e SaaS ma anche per i team delle piattaforme centrali aziendali e gli integratori di sistemi che vogliono creare pacchetti delle proprie soluzioni e rivenderli.

## <a name="how-managed-applications-work"></a>Funzionamento delle applicazioni gestite
Sono disponibili due esperienze per l'uso delle applicazioni gestite:

1. Il fornitore o il fornitore di software indipendente crea un'applicazione gestita e la rende disponibile per un uso più ampio. 
2. Il cliente o l'utente vuole creare e usare l'applicazione pubblicata. 

Questo articolo illustra entrambe le esperienze. È prima di tutto necessario comprendere il funzionamento delle applicazioni gestite. 

Un'applicazione gestita è simile a una soluzione del Marketplace, con una differenza essenziale. In un'applicazione gestita, le risorse vengono sottoposte a provisioning in un gruppo di risorse gestito dal fornitore di software indipendente/fornitore. Il gruppo di risorse è presente nella sottoscrizione del cliente, ma un utente, un gruppo di utenti o un'applicazione nel tenant del fornitore di software indipendente ha accesso al gruppo di risorse. Per gestire l'applicazione, l'identità del fornitore viene aggiunta a un ruolo Proprietario, Collaboratore, Lettore o a un altro ruolo predefinito di Active Directory. 

## <a name="key-concepts"></a>Concetti chiave

### <a name="managed-resource-group"></a>Gruppo di risorse gestite
Gruppo di risorse in cui vengono create tutte le risorse di Azure sottoposte a provisioning nel modello. Se, ad esempio, l'appliance crea un account di archiviazione, questo gruppo di risorse contiene la risorsa dell'account di archiviazione. Non contiene le risorse dell'appliance.

### <a name="appliance-package"></a>Pacchetto dell'appliance
L'autore crea un pacchetto che contiene i file del modello e il file createUIDefinition. In particolare, contiene i file seguenti:

- **applianceMainTemplate.json**: file modello che definisce tutte le risorse sottoposte a provisioning dall'appliance. Questo file è un file di modello normale usato per creare le risorse.

- **MainTemplate.json**: file modello che definisce la risorsa dell'appliance (Microsoft.Solutions/appliances). Una proprietà essenziale definita in questa risorsa è ManagedResourceGroupId. Questa proprietà indica il gruppo di risorse usato per l'hosting delle risorse effettive definite in applianceMainTemplate.json.

- **createUIDefinition.json**: questo file illustra la modalità di rendering dell'interfaccia utente per i parametri definiti nel modello.

### <a name="authorization"></a>Authorization
L'autore deve specificare le autorizzazioni richieste dal fornitore per gestire le risorse per conto del cliente. Questa autorizzazione è applicabile al gruppo di risorse gestite. Impostare i valori seguenti:

- **PrincipalID**: identificatore di Azure AD per l'utente, il gruppo o l'applicazione da usare per concedere l'accesso al gruppo di risorse gestite. Questo identificatore appartiene al tenant dell'autore.

- **RoleDefinitionID**: identificatore di Azure AD per il ruolo assegnato all'ID entità precedente. Può essere uno dei ruoli RBAC predefiniti disponibili nel tenant dell'autore.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sull'esperienza del fornitore, vedere [Creare e pubblicare un'applicazione gestita di Azure](managed-application-publishing.md).
* Per informazioni sull'esperienza dell'utente, vedere [Utilizzare un'applicazione gestita di Azure](managed-application-consumption.md).
* Per creare un file di definizione dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](managed-application-createuidefinition-overview.md).
