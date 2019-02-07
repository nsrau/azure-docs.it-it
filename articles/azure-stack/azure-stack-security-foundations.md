---
title: Comprendere i controlli di sicurezza di Azure Stack
description: Scopri i controlli di sicurezza applicati da Azure Stack come amministratore del servizio
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: efa97c18a63954239475338c85f2145b8c6c6ac6
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/06/2019
ms.locfileid: "55767270"
---
# <a name="azure-stack-infrastructure-security-posture"></a>Comportamento di sicurezza di Azure Stack dell'infrastruttura

*Si applica a: Sistemi integrati di Azure Stack*

Considerazioni sulla sicurezza e conformità alle normative sono tra i driver principali per l'uso di cloud ibridi. Azure Stack è progettato per questi scenari. Questo articolo illustra i controlli di sicurezza in atto per Azure Stack.

Coesistono di due livelli di comportamento di sicurezza in Azure Stack. Il primo livello è l'infrastruttura di Azure Stack, che include i componenti hardware fino a Azure Resource Manager. Il primo livello include i portali Tenant e l'amministratore. Il secondo livello è costituito da carichi di lavoro creati, distribuiti e gestiti dai tenant. Il secondo livello include gli elementi, ad esempio le macchine virtuali e siti web di servizi App.

## <a name="security-approach"></a>Approccio alla sicurezza

Le condizioni di sicurezza per Azure Stack sono progettata per difendersi contro le moderne minacce ed è stata progettata per soddisfare i requisiti degli standard di conformità principali. Di conseguenza, il comportamento di sicurezza dell'infrastruttura di Azure Stack si basa su due concetti fondamentali:

 - **Presunzione di violazione**  
A partire dal presupposto che il sistema non è già stata soddisfatta, concentrarsi sulla *rilevare e limitare l'impatto delle violazioni* e cercando solo impedire attacchi. 
 - **Avanzata per impostazione predefinita**  
Poiché l'infrastruttura viene eseguito su hardware ben definiti e software, Azure Stack *Abilita, configura e convalida di tutte le funzionalità di sicurezza* per impostazione predefinita.

Perché Azure Stack viene recapitato come sistema integrato, il comportamento di sicurezza dell'infrastruttura di Azure Stack è definito da Microsoft. Proprio come in Azure, i tenant sono responsabili della definizione del comportamento di sicurezza di carichi di lavoro tenant. Questo documento fornisce una conoscenza di base sulle condizioni di sicurezza dell'infrastruttura di Azure Stack.

## <a name="data-at-rest-encryption"></a>Dati di crittografia dei dati inattivi
Tutti i dati dell'infrastruttura e tenant di Azure Stack vengono crittografati a riposo mediante BitLocker. Questa crittografia protegge da fisica perdita o furto di componenti di archiviazione di Azure Stack. Per altre informazioni, vedere [dati crittografia dei dati inattivi in Azure Stack](azure-stack-security-bitlocker.md).

## <a name="data-in-transit-encryption"></a>Dati nella crittografia di transito
I componenti dell'infrastruttura di Azure Stack comunicano tramite i canali crittografati con TLS 1.2. I certificati di crittografia vengono self-gestiti dall'infrastruttura. 

Tutti gli endpoint esterni dell'infrastruttura, ad esempio gli endpoint REST o il portale di Azure Stack supportano TLS 1.2 per le comunicazioni protette. Per gli endpoint, è necessario specificare i certificati di crittografia, da terze parti o l'autorità di certificazione dell'organizzazione. 

Mentre i certificati autofirmati possono essere utilizzati per questi endpoint esterni, Microsoft raccomanda di utilizzarli. 

## <a name="secret-management"></a>Gestione dei segreti
Infrastruttura di Azure Stack Usa una vasta gamma di informazioni riservate, come le password, per funzionare. La maggior parte di essi vengono ruotata automaticamente di frequente, poiché sono gli account dei servizi Group-Managed, ruotare ogni 24 ore.

I segreti rimanenti che non sono gli account del servizio Group-Managed possono essere ruotati manualmente con uno script nell'Endpoint con privilegi.

## <a name="code-integrity"></a>Integrità del codice
Azure Stack si avvale di Windows Server 2016 più recenti funzionalità di sicurezza. Uno di essi è Windows Defender Device Guard, che fornisce whitelist per le applicazioni e assicura che solo autorizzati esecuzioni del codice all'interno dell'infrastruttura di Azure Stack. 

Codice autorizzato viene firmato da Microsoft o il partner OEM. Il codice autorizzato con segno è incluso nell'elenco dei consentiti del software specificato in un criterio definito da Microsoft. In altre parole, può essere eseguito solo il software che è stato approvato per l'esecuzione nell'infrastruttura di Azure Stack. Qualsiasi tentativo di eseguire codice non autorizzato viene bloccato e viene generato un controllo.

Il criterio di Device Guard impedisce inoltre agli agenti di terze parti o software in esecuzione nell'infrastruttura di Azure Stack.

## <a name="credential-guard"></a>Credential Guard
Un'altra funzionalità di sicurezza di Windows Server 2016 in Azure Stack è Windows Defender Credential Guard, che consente di proteggere le credenziali dell'infrastruttura di Azure Stack da Pass-the-Hash e gli attacchi Pass-the-Ticket.

## <a name="antimalware"></a>Antimalware
Ogni componente in Azure Stack (host Hyper-V e macchine virtuali) è protetto con Windows Defender Antivirus.

In scenari connessi, gli aggiornamenti antivirus motore e delle definizioni vengono applicati molte volte al giorno. Negli scenari disconnessi vengono applicati aggiornamenti antimalware come parte degli aggiornamenti mensili di Azure Stack. Per altre informazioni, vedere [aggiornamento di Windows Defender Antivirus in Azure Stack](azure-stack-security-av.md).

## <a name="constrained-administration-model"></a>Modello di amministrazione limitata
Amministrazione in Azure Stack è controllata tramite tre punti di ingresso, ciascuno con uno scopo specifico: 
1. Il [portale di amministrazione](azure-stack-manage-portals.md) offre un'esperienza di puntamento e clic per operazioni di gestione quotidiane.
2. Azure Resource Manager espone tutte le operazioni di gestione del portale dell'amministratore tramite l'API REST, usato da PowerShell e CLI di Azure. 
3. Per le operazioni di basso livello specifiche, ad esempio dati center integration o supportano scenari, Azure Stack espone un endpoint di PowerShell denominato [Privileged Endpoint](azure-stack-privileged-endpoint.md). L'endpoint espone solo un gruppo di nell'elenco elementi consentiti di cmdlet e viene eseguito il controllo in modo rilevante.

## <a name="network-controls"></a>Controlli di rete
Infrastruttura di Azure Stack viene fornito con diversi livelli di rete elenco di controllo di accesso (ACL). Gli ACL impediscono accessi non autorizzati per i componenti dell'infrastruttura e limitano le comunicazioni solo i percorsi necessari per il funzionamento dell'infrastruttura. 

Gli ACL di rete vengono applicati in tre livelli:
1.  Commutatore Top of Rack
2.  Software Defined Network
3.  Firewall del sistema operativo host e macchine Virtuali

## <a name="regulatory-compliance"></a>Conformità alle normative

Azure Stack è stata indicata mediante una valutazione formale con una società di terze parti indipendenti dal controllo. Di conseguenza, documentazione sul modo in cui l'infrastruttura di Azure Stack soddisfa i controlli applicabili da vari standard di conformità principali è disponibile. La documentazione non è una certificazione di Azure Stack a causa di standard, tra cui diversi controlli relative al processo e il personale. Piuttosto, i clienti possono usare questa documentazione per avviare il processo di certificazione.

Le valutazioni includono i seguenti standard:

- [PCI-DSS](https://www.pcisecuritystandards.org/pci_security/) risolve il settore delle carte di pagamento.
- [Matrice di controllo Cloud CSA](https://cloudsecurityalliance.org/group/cloud-controls-matrix/#_overview) viene applicato un mapping completo tra più standard, inclusi fedramp di livello moderato, ISO27001, HIPAA, HITRUST, ITAR, 53 SP800 NIST e ad altri utenti.
- [FedRAMP High](https://www.fedramp.gov/fedramp-releases-high-baseline/) per i clienti del governo.

La documentazione sulla conformità è reperibile nella [Microsoft Service Trust Portal](https://servicetrust.microsoft.com/ViewPage/Blueprint). Le guide di conformità sono una risorsa protetta e richiedono di accedere con le credenziali del servizio cloud di Azure.

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su come eseguire la rotazione i segreti in Azure Stack](azure-stack-rotate-secrets.md)
- [PCI-DSS e i documenti di CSA-CCM per Azure Stack](https://servicetrust.microsoft.com/ViewPage/TrustDocuments)
- [Dipartimento della difesa e NIST documenti per Azure Stack](https://servicetrust.microsoft.com/ViewPage/Blueprint)
