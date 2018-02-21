---
title: Piano per l'elaborazione dei pagamenti di Azure - Requisiti di accesso
description: Requisito 7 di PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: ac3afee9-0471-465d-a115-67488a1635a6
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 5a3c9eac552fb96309cfa791a2e72a7102662e60
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2017
---
# <a name="access-requirements-for-pci-dss-compliant-environments"></a>Requisiti di accesso per ambienti conformi a PCI DSS 
## <a name="pci-dss-requirement-7"></a>Requisito 7 di PCI DSS

**Limitare l'accesso ai dati dei titolari di carte al personale autorizzato per motivi professionali**

> [!NOTE]
> Questi requisiti sono definiti dal [Payment Card Industry (PCI) Security Standards Council](https://www.pcisecuritystandards.org/pci_security/) nell'ambito di [PCI Data Security Standard (DSS) versione 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Per informazioni sulle procedure di test e indicazioni per ogni requisito, vedere lo standard PCI DSS.

Per assicurarsi che i dati critici siano accessibili solo al personale autorizzato, devono essere presenti sistemi e processi per limitare l'accesso in base alle informazioni necessarie e alle responsabilità professionali.

A seconda delle informazioni necessarie, vengono concessi i diritti di accesso solo alla quantità minima di dati e privilegi necessari per eseguire un processo.

## <a name="pci-dss-requirement-71"></a>Requisito 7.1 di PCI DSS

**7.1** Limitare l'accesso ai componenti di sistema e ai dati dei titolari di carte solo alle persone che hanno bisogno di tale accesso per motivi professionali.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Azure applica i criteri ISMS esistenti relativi all'accesso del personale di Azure ai componenti di sistema di Azure, alla verifica dell'efficacia del controllo di accesso, alla specifica dell'accesso amministrativo JIT, alla revoca dell'accesso quando non è più necessario e alla garanzia che solo il personale autorizzato per motivi professionali abbia accesso all'ambiente della piattaforma Azure. L'accesso di Azure agli ambienti dei clienti è soggetto a severe limitazioni e consentito solo su approvazione del cliente.<br /><br />Sono state stabilite procedure che limitano l'accesso fisico al data center a dipendenti, fornitori, terzisti e visitatori autorizzati. La verifica di sicurezza e la registrazione sono obbligatorie per il personale che richiede l'accesso temporaneo alla struttura interna del data center. I log di accesso fisico vengono esaminati ogni trimestre dai team di Azure. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | È responsabilità dei clienti limitare l'accesso ai componenti di sistema e ai dati dei titolari di carte solo alle persone che hanno bisogno di tale accesso per motivi professionali. I clienti devono anche stabilire limiti e restrizioni per l'accesso al portale di gestione di Azure, oltre a specificare gli account e i ruoli con l'autorizzazione necessaria per creare, modificare o eliminare i servizi PaaS.|



### <a name="pci-dss-requirement-711"></a>Requisito 7.1.1 di PCI DSS

**7.1.1** Definire le esigenze di accesso per ogni ruolo,tra cui:
- Componenti di sistema e risorse dati necessari a ogni ruolo per accedere alla propria funzione lavorativa
- Livello di privilegio necessario (ad esempio, utente, amministratore e così via) per accedere alle risorse

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | I clienti sono responsabili della definizione e della documentazione di un processo di approvazione degli ID utente, della definizione dei privilegi minimi, della limitazione dell'accesso ai dati di titolari di carte, dell'uso di ID univoci, della separazione dei compiti e della revoca dell'accesso utente quando non è più necessario.|



### <a name="pci-dss-requirement-712"></a>Requisito 7.1.2 di PCI DSS

**7.1.2** Limitare l'accesso agli ID utente con privilegi ai privilegi minimi necessari per adempiere alle responsabilità professionali.

**Responsabilità:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure ha adottato criteri di sicurezza aziendali applicabili, inclusi criteri di sicurezza delle informazioni. I criteri sono stati approvati, pubblicati e comunicati a Microsoft Azure. In base ai criteri di sicurezza delle informazioni di Microsoft Azure l'accesso agli asset di Microsoft Azure deve essere concesso in base a una motivazione aziendale, con l'autorizzazione del proprietario dell'asset e secondo i principi "solo quando necessario" e "privilegi minimi". I criteri soddisfano anche i requisiti per il ciclo di vita della gestione degli accessi, inclusi provisioning degli accessi, autorizzazione di accesso, rimozione dell'autenticazione per i diritti di accesso e verifiche di accesso periodiche. |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore crea tre account durante la distribuzione: admin, sqladmin ed edna (l'utente predefinito connesso all'app Web durante l'esecuzione demo). I ruoli utente sono limitati ai compiti in base allo scenario demo documentato.|



### <a name="pci-dss-requirement-713"></a>Requisito 7.1.3 di PCI DSS

**7.1.3** Assegnare l'accesso in base alla classificazione e funzione lavorativa del singolo dipendente.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore crea tre account durante la distribuzione: admin, sqladmin ed edna (l'utente predefinito connesso all'app Web durante l'esecuzione demo). I ruoli utente sono limitati ai compiti in base allo scenario demo documentato.|



### <a name="pci-dss-requirement-714"></a>Requisito 7.1.4 di PCI DSS

**7.1.4** Richiedere l'approvazione documentata delle parti autorizzate con la specifica dei privilegi obbligatori.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | È responsabilità dei clienti limitare l'accesso ai componenti di sistema e ai dati dei titolari di carte solo alle persone che hanno bisogno di tale accesso per motivi professionali. I clienti devono anche stabilire limiti e restrizioni per l'accesso al portale di gestione di Azure, oltre a specificare gli account e i ruoli con l'autorizzazione necessaria per creare, modificare o eliminare i servizi PaaS.|



## <a name="pci-dss-requirement-72"></a>Requisito 7.2 di PCI DSS

**7.2** Stabilire un sistema di controllo di accesso per i componenti dei sistemi, che limiti l'accesso in base alle informazioni necessarie a un utente e sia impostato su "Nega tutto" se non specificamente consentito.
Questo sistema di controllo di accesso deve includere quanto segue:
- 7.2.1 Copertura di tutti i componenti di sistema.
- 7.2.2 Assegnazione dei privilegi ai singoli individui in base alla classificazione e funzione lavorativa.
- 7.2.3 Impostazione "Nega tutto" predefinita.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | Contoso Webstore usa Azure Active Directory per limitare l'accesso solo agli utenti designati. Per altre informazioni, vedere [PCI Guidance - Identity Management](payment-processing-blueprint.md#identity-management) (Linee guida per PCI - Gestione delle identità).|



## <a name="pci-dss-requirement-73"></a>Requisito 7.3 di PCI DSS

**7.3** Assicurarsi che i criteri di sicurezza e le procedure operative per la limitazione dell'accesso ai dati di titolari di carte siano documentati, applicati e noti a tutte le parti interessate.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(progetto&nbsp;PCI&#8209;DSS)** | La documentazione di Contoso Webstore contiene un caso d'uso e una descrizione relativa a chi usa i dati del titolare della carta e a come vengono usati tali dati.|




