---
title: Informazioni sui controlli di sicurezza di Azure Stack | Documenti Microsoft
description: Come amministratore del servizio informazioni relative ai controlli di sicurezza applicati allo Stack di Azure
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2018
ms.author: mabrigg
ms.openlocfilehash: c1d92f8f2ed9e8ab504afc65bab861e1f7bb3689
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="azure-stack-infrastructure-security-posture"></a>Condizioni di sicurezza di Azure Stack infrastruttura

*Si applica a: Azure Stack integrate di sistemi*

Considerazioni sulla sicurezza e normative di conformità sono tra i driver principali per l'utilizzo di cloud ibridi. Stack Azure è stato progettato per questi scenari ed è importante comprendere i controlli già in uso per l'adozione di Stack di Azure.

Nello Stack di Azure, esistono due livelli di situazione sicurezza coesistono. Il primo livello comprende l'infrastruttura di Azure Stack, che include l'amministratore e i portali Tenant e unisce i componenti hardware completamente fino a Gestione risorse di Azure. Il secondo livello è costituito da carichi di lavoro che i tenant di creare, distribuiscono e gestire e include informazioni quali macchine virtuali o siti web di servizi App.  

## <a name="security-approach"></a>Approccio di sicurezza
Stack di Azure è stato progettato con una condizione di sicurezza per difendersi da minacce moderne ed è stato compilato per soddisfare i requisiti degli standard di conformità principali. Di conseguenza, le condizioni di sicurezza dell'infrastruttura di Azure Stack si basa su due aree:

 - **Si supponga di violazione.**  
A partire dal presupposto che il sistema non è già stata soddisfatta, lo stato attivo su *rilevare e limitare l'impatto delle violazioni* rispetto solo tentando di impedire attacchi. 
 - **Finalizzazione per impostazione predefinita.**  
Poiché l'infrastruttura viene eseguito su hardware ben definiti e software, è *abilitare, configurare e convalidare tutte le funzionalità di sicurezza* per impostazione predefinita.



Poiché Azure Stack viene recapitato come un sistema integrato, le condizioni di sicurezza dell'infrastruttura di Azure Stack sono definita da Microsoft. Come in Azure, i tenant sono responsabili per definire le condizioni di sicurezza dei rispettivi carichi di lavoro tenant. Questo documento fornisce una conoscenza di base in condizioni di sicurezza dell'infrastruttura di Azure Stack.

## <a name="data-at-rest-encryption"></a>Dati a crittografia rest
Tutti i dati dell'infrastruttura e tenant di Azure Stack vengono crittografati a riposo mediante Bitlocker. La crittografia protegge contro fisica perdita o furto dei componenti di archiviazione di Azure Stack. 

## <a name="data-in-transit-encryption"></a>Dati di crittografia di transito
I componenti dell'infrastruttura di Azure Stack comunicano con i canali crittografati con TLS 1.2. Self-gestiti dall'infrastruttura di certificati di crittografia. 

Tutti gli endpoint di infrastruttura esterni, ad esempio gli endpoint REST o il portale di Azure Stack supportano TLS 1.2 per le comunicazioni protette. I certificati di crittografia da una terza parte o dell'organizzazione autorità di certificazione, è obbligatorio per gli endpoint. 

Mentre i certificati autofirmati possono essere utilizzati per questi endpoint esterni, si consiglia fortemente di utilizzarli. 

## <a name="secret-management"></a>Gestione di informazioni segrete
Infrastruttura di Azure Stack utilizza una vasta gamma di informazioni riservate, come le password, per funzionare. La maggior parte di essi vengono ruotata automaticamente di frequente, perché sono gli account del servizio gestito di gruppo, ruotare ogni 24 ore.

I segreti rimanenti che non possono essere ruotati manualmente gli account del servizio gestito di gruppo con uno script nell'Endpoint con privilegi.

## <a name="code-integrity"></a>Integrità del codice
Stack di Azure viene utilizzato il più recente di Windows Server 2016 le funzionalità di sicurezza. Uno di essi è Windows Defender controllo dispositivo, che fornisce whitelist applicazione e garantisce che solo autorizzati codice viene eseguito all'interno dell'infrastruttura di Azure Stack. 

Codice non autorizzato è firmato da Microsoft o il partner OEM ed è incluso nell'elenco dei software consentito che è specificato in un criterio definito da Microsoft. In altre parole, è possibile eseguire solo il software che è stato approvato per l'esecuzione nell'infrastruttura di Azure Stack. Qualsiasi tentativo di eseguire codice non autorizzato è bloccato e viene generato un controllo.

I criteri di controllo dispositivo impedisce inoltre agli agenti di terze parti o software in esecuzione nell'infrastruttura di Azure Stack.

## <a name="credential-guard"></a>Protezione delle credenziali
Un'altra funzionalità di sicurezza di Windows Server 2016 nello Stack di Azure è Guard di credenziali di Windows Defender, viene utilizzato per proteggere le credenziali dell'infrastruttura di Azure Stack da Pass-the-Hash e attacchi Pass-the-Ticket.

## <a name="antimalware"></a>Antimalware
Ogni componente nello Stack di Azure (host Hyper-V e macchine virtuali) è protetto da Windows Defender Antivirus.

## <a name="constrained-administration-model"></a>Modello di amministrazione limitata
Amministrazione nello Stack di Azure viene controllato tramite l'utilizzo di tre punti di ingresso, ciascuno con uno scopo specifico: 
1. Il [portale dell'amministratore](azure-stack-manage-portals.md) offre un'esperienza di mouse e per le operazioni quotidiane di gestione.
2. Gestione risorse di Azure espone tutte le operazioni di gestione del portale di amministrazione tramite un'API REST, usato da PowerShell e CLI di Azure. 
3. Per specifiche operazioni di basso livello, ad esempio dati center integrazione o supportano gli scenari, Stack di Azure espone un endpoint di PowerShell denominato [Endpoint con privilegi](azure-stack-privileged-endpoint.md). L'endpoint espone solo un set consentito di cmdlet e viene ampiamente eseguito il controllo.

## <a name="network-controls"></a>Controlli di rete
Infrastruttura di Azure Stack viene fornito con diversi livelli di List(ACL) di controllo di accesso di rete. Gli ACL impediscono accessi non autorizzati per i componenti dell'infrastruttura e limitare le comunicazioni con infrastruttura solo i percorsi che sono necessari per il funzionamento. 

Gli ACL di rete vengono applicati in tre livelli:
1.  Parte superiore del Rack attiva
2.  Software di rete definite
3.  Firewall del sistema operativo host e macchine Virtuali 


