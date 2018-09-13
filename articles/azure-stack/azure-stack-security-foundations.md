---
title: Comprendere i controlli di sicurezza di Azure Stack | Microsoft Docs
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
ms.date: 09/12/2018
ms.author: patricka
ms.openlocfilehash: 048a2e8204b3b8776b5a7e0e425dbc5fdf3d504c
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2018
ms.locfileid: "44719019"
---
# <a name="azure-stack-infrastructure-security-posture"></a>Comportamento di sicurezza di Azure Stack dell'infrastruttura

*Si applica a: i sistemi integrati di Azure Stack*

Considerazioni sulla sicurezza e conformità alle normative sono tra i driver principali per l'uso di cloud ibridi. Azure Stack è stato progettato per questi scenari, ed è importante comprendere i controlli già in uso dell'adozione di Azure Stack.

Coesistono di due livelli di comportamento di sicurezza in Azure Stack. Il primo livello è l'infrastruttura di Azure Stack, che include i componenti hardware fino a Azure Resource Manager. Il primo livello include i portali Tenant e l'amministratore. Il secondo livello è costituito da carichi di lavoro creati, distribuiti e gestiti dai tenant. Il secondo livello include gli elementi, ad esempio le macchine virtuali e siti web di servizi App.

## <a name="security-approach"></a>Approccio alla sicurezza

Le condizioni di sicurezza per Azure Stack sono progettata per difendersi contro le moderne minacce ed è stata progettata per soddisfare i requisiti degli standard di conformità principali. Di conseguenza, il comportamento di sicurezza dell'infrastruttura di Azure Stack si basa su due concetti fondamentali:

 - **Presunzione di violazione**  
A partire dal presupposto che il sistema non è già stata soddisfatta, concentrarsi sulla *rilevare e limitare l'impatto delle violazioni* e cercando solo impedire attacchi. 
 - **Avanzata per impostazione predefinita**  
Poiché l'infrastruttura viene eseguito su hardware ben definiti e software, Azure Stack *Abilita, configura e convalida di tutte le funzionalità di sicurezza* per impostazione predefinita.

Perché Azure Stack viene recapitato come sistema integrato, il comportamento di sicurezza dell'infrastruttura di Azure Stack è definito da Microsoft. Proprio come in Azure, i tenant sono responsabili della definizione del comportamento di sicurezza di carichi di lavoro tenant. Questo documento fornisce una conoscenza di base sulle condizioni di sicurezza dell'infrastruttura di Azure Stack.

## <a name="data-at-rest-encryption"></a>Dati di crittografia dei dati inattivi
Tutti i dati dell'infrastruttura e tenant di Azure Stack vengono crittografati a riposo mediante Bitlocker. Questa crittografia protegge da fisica perdita o furto di componenti di archiviazione di Azure Stack. 

## <a name="data-in-transit-encryption"></a>Dati nella crittografia di transito
I componenti dell'infrastruttura di Azure Stack comunicano tramite i canali crittografati con TLS 1.2. I certificati di crittografia vengono self-gestiti dall'infrastruttura. 

Tutti gli endpoint esterni dell'infrastruttura, ad esempio gli endpoint REST o il portale di Azure Stack supportano TLS 1.2 per le comunicazioni protette. Per gli endpoint, è necessario specificare i certificati di crittografia, da terze parti o l'autorità di certificazione dell'organizzazione. 

Mentre i certificati autofirmati possono essere utilizzati per questi endpoint esterni, Microsoft raccomanda di utilizzarli. 

## <a name="secret-management"></a>Gestione dei segreti
Infrastruttura di Azure Stack Usa una vasta gamma di informazioni riservate, come le password, per funzionare. La maggior parte di essi vengono ruotata automaticamente di frequente, poiché sono gli account del servizio gestito di gruppo, che ruota ogni 24 ore.

I segreti rimanenti che non sono gli account del servizio gestito di gruppo possono essere ruotati manualmente con uno script nell'Endpoint con privilegi.

## <a name="code-integrity"></a>Integrità del codice
Azure Stack si avvale di Windows Server 2016 più recenti funzionalità di sicurezza. Uno di essi è Windows Defender Device Guard, che fornisce whitelist per le applicazioni e assicura che solo autorizzati esecuzioni del codice all'interno dell'infrastruttura di Azure Stack. 

Codice autorizzato è firmato da Microsoft o il partner OEM, ed è incluso nell'elenco dei consentiti del software specificato in un criterio definito da Microsoft. In altre parole, può essere eseguito solo il software che è stato approvato per l'esecuzione nell'infrastruttura di Azure Stack. Qualsiasi tentativo di eseguire codice non autorizzato viene bloccato e viene generato un controllo.

Il criterio di Device Guard impedisce inoltre agli agenti di terze parti o software in esecuzione nell'infrastruttura di Azure Stack.

## <a name="credential-guard"></a>Credential Guard
Un'altra funzionalità di sicurezza di Windows Server 2016 in Azure Stack è Windows Defender Credential Guard, che consente di proteggere le credenziali dell'infrastruttura di Azure Stack da Pass-the-Hash e gli attacchi Pass-the-Ticket.

## <a name="antimalware"></a>Antimalware
Ogni componente in Azure Stack (host Hyper-V e macchine virtuali) è protetto con Windows Defender Antivirus.

In scenari connessi, gli aggiornamenti antivirus motore e delle definizioni vengono applicati molte volte al giorno. Negli scenari disconnessi vengono applicati aggiornamenti antimalware come parte degli aggiornamenti mensili di Azure Stack. Visualizzare [aggiornamento di Windows Defender Antivirus in Azure Stack](azure-stack-security-av.md) per altre informazioni.

## <a name="constrained-administration-model"></a>Modello di amministrazione limitata
Amministrazione in Azure Stack è controllata tramite l'utilizzo di tre punti di ingresso, ciascuno con uno scopo specifico: 
1. Il [portale di amministrazione](azure-stack-manage-portals.md) offre un'esperienza di puntamento e clic per operazioni di gestione quotidiane.
2. Azure Resource Manager espone tutte le operazioni di gestione del portale dell'amministratore tramite l'API REST, usato da PowerShell e CLI di Azure. 
3. Per le operazioni di basso livello specifiche, ad esempio dati center integration o supportano scenari, Azure Stack espone un endpoint di PowerShell denominato [Privileged Endpoint](azure-stack-privileged-endpoint.md). L'endpoint espone solo un gruppo di nell'elenco elementi consentiti di cmdlet e viene eseguito il controllo in modo rilevante.

## <a name="network-controls"></a>Controlli di rete
Infrastruttura di Azure Stack viene fornito con diversi livelli di rete List(ACL) di controllo di accesso. Gli ACL impediscono accessi non autorizzati per i componenti dell'infrastruttura e limitano le comunicazioni solo i percorsi necessari per il funzionamento dell'infrastruttura. 

Gli ACL di rete vengono applicati in tre livelli:
1.  Commutatore Top of Rack
2.  Software Defined Network
3.  Firewall del sistema operativo host e macchine Virtuali

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su come eseguire la rotazione i segreti in Azure Stack](azure-stack-rotate-secrets.md)
- [PCI-DSS e i documenti di CSA-CCM per Azure Stack](https://servicetrust.microsoft.com/ViewPage/TrustDocuments)
- [Dipartimento della difesa e NIST documenti per Azure Stack](https://servicetrust.microsoft.com/ViewPage/Blueprint)
