---
title: Panoramica di Attestazione di Azure
description: Panoramica di Attestazione di Microsoft Azure, una soluzione per l'attestazione di ambienti TEE (Trusted Execution Environment)
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.custom: references_regions
ms.openlocfilehash: ad164f8af3e5506ae5ac9121010b99303286dd1f
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89320597"
---
# <a name="microsoft-azure-attestation-preview"></a>Attestazione di Microsoft Azure (anteprima)

Attestazione di Microsoft Azure (anteprima) è una soluzione per attestare gli ambienti TEE (Trusted Execution Environment) come le enclavi [Intel® Software Guard Extensions](https://www.intel.com/content/www/us/en/architecture-and-technology/software-guard-extensions.html) (SGX) e le enclavi [Virtualization-based Security](/windows-hardware/design/device-experiences/oem-vbs) (VBS). L'attestazione di enclavi è un processo per verificare che un'enclave sia sicura e attendibile.

L'attestazione è un processo per dimostrare che siano state create istanze corrette dei binari software in una piattaforma attendibile. Le relying party remote possono quindi avere la certezza che solo tale software previsto venga eseguito su hardware attendibile. Attestazione di Azure è un servizio e framework unificato rivolto ai clienti per l'attestazione.

Attestazione di Azure rende disponibili paradigmi di sicurezza all'avanguardia, ad esempio il [confidential computing di Azure](../confidential-computing/overview.md) e la protezione delle reti perimetrali intelligenti. I clienti chiedono di avere la possibilità di verificare in modo indipendente la posizione di un computer, la postura di una macchina virtuale (VM) in tale computer e l'ambiente in cui sono in esecuzione le enclavi nella VM. Attestazione di Azure risponde a queste e a molte altre richieste dei clienti.

Attestazione di Azure riceve evidenze dalle entità di calcolo, le converte in un set di attestazioni, le convalida in base a criteri configurabili e genera prove crittografiche per le applicazioni basate su attestazioni (ad esempio, relying party e autorità di controllo).

## <a name="use-cases"></a>Casi d'uso

Attestazione di Azure fornisce servizi di attestazione completi per più ambienti e casi d'uso distinti.

### <a name="sgx-attestation"></a>Attestazione SGX

SGX fa riferimento all'isolamento di livello hardware, supportato in determinati modelli di CPU Intel. SGX consente l'esecuzione del codice in raggruppamenti purificati noti come enclavi SGX. Le autorizzazioni per l'accesso e la memoria vengono quindi gestite dall'hardware per garantire una superficie di attacco minima con isolamento appropriato.

Le applicazioni client possono essere progettate per sfruttare le enclavi SGX delegando le attività sensibili alla sicurezza da eseguire al loro interno. Tali applicazioni possono quindi usare Attestazione di Azure per stabilire periodicamente la relazione di trust nell'enclave e la relativa capacità di accedere a dati sensibili.

### <a name="vbs-attestation"></a>Attestazione VBS

VBS è un'architettura basata su software per una protezione della memoria dell'enclave basata su Hyper-V. Impedisce al codice dell'amministratore host, nonché agli amministratori dei servizi locali e cloud, di accedere ai dati in un'enclave VBS o di influire sulla sua esecuzione.

In modo simile alla tecnologia SGX, Attestazione di Azure supporta la convalida di enclavi VBS rispetto a criteri configurati e l'emissione di una dichiarazione di certificazione come prova di validità.

### <a name="open-enclave"></a>Open Enclave
[Open Enclave](https://openenclave.io/sdk/) (OE) è una raccolta di librerie destinate alla creazione di una singola astrazione unificata per enclavi che consente agli sviluppatori di creare applicazioni basate su TEE. Offre un modello di app sicuro universale che riduce al minimo le specifiche della piattaforma. Microsoft lo considera come un primo passo essenziale per la democratizzazione di tecnologie di enclave basate su hardware, ad esempio SGX, e l'aumento della loro diffusione in Azure.

OE standardizza i requisiti specifici per la verifica dell'evidenza di un'enclave. Per questo motivo, OE si qualifica come consumer di attestazione perfettamente idoneo per Attestazione di Azure.

## <a name="azure-attestation-can-run-in-a-tee"></a>Attestazione di Azure è eseguibile in un ambiente TEE

Attestazione di Azure è fondamentale per gli scenari di confidential computing ed esegue le azioni seguenti:

- Verifica se l'evidenza dell'enclave è valida.
- Valuta l'evidenza dell'enclave rispetto a criteri definiti dal cliente.
- Gestisce e archivia criteri specifici del tenant.
- Genera e firma un token usato dalle relying party per interagire con l'enclave.

Attestazione di Azure si basa su due tipi di ambienti:
- Attestazione di Azure in esecuzione in un ambiente TEE abilitato per SGX.
- Attestazione di Azure in esecuzione in un ambiente non TEE.

I clienti di Attestazione di Azure hanno espresso un requisito per cui Microsoft deve operare al di fuori della TCB (Trusted Computing Base). Il motivo è impedire a entità Microsoft, come amministratori di VM, amministratori host e sviluppatori Microsoft, di modificare le richieste di attestazione, i criteri e i token emessi da Attestazione di Azure. Attestazione di Azure è inoltre un servizio progettato per l'esecuzione in ambienti TEE, in cui funzionalità come la convalida delle offerte, la generazione di token e la forma di token vengono spostate in un'enclave SGX.

## <a name="why-use-azure-attestation"></a>Perché usare Attestazione di Azure

Attestazione di Azure rappresenta la scelta ideale per l'attestazione di ambienti TEE, perché offre i vantaggi seguenti: 

- Framework unificato per l'attestazione di più ambienti TEE, ad esempio enclavi SGX e VBS
- Servizio multi-tenant che consente la configurazione di criteri e provider di attestazione personalizzati per limitare la generazione di token
- Offre provider predefiniti in grado di create attestazioni senza configurazione da parte degli utenti
- Protegge i dati in uso con l'implementazione in un'enclave SGX
- Servizio a disponibilità elevata che offrirà un Contratto di servizio (SLA)

## <a name="business-continuity-and-disaster-recovery-bcdr-support"></a>Supporto di continuità aziendale e ripristino di emergenza

Il servizio di [continuità aziendale e ripristino di emergenza](/azure/best-practices-availability-paired-regions) per Attestazione di Azure consente di ridurre le interruzioni dei servizi derivanti da problemi significativi di disponibilità o da eventi di emergenza in un'area.

Di seguito sono riportate le aree attualmente supportate dal servizio di continuità aziendale e ripristino di emergenza
- Stati Uniti orientali 2 = > abbinata a Stati Uniti centrali.
- Stati Uniti centrali = > abbinata a Stati Uniti orientali 2.

I cluster distribuiti in due aree operano in modo indipendente in circostanze normali. Nel caso di errori o interruzioni in un'area, si verifica quanto segue:

- Il servizio di continuità aziendale e ripristino di emergenza di Attestazione di Azure fornirà il failover immediato per cui i clienti non devono eseguire altri interventi per il ripristino
- Il servizio [Gestione traffico di Azure](../traffic-manager/index.yml) per l'area rileverà che il probe di integrità è danneggiato e sposterà l'endpoint in un'area abbinata
- Le connessioni esistenti non funzioneranno e riceveranno un errore interno del server o problemi di timeout
- Tutte le operazioni del piano di controllo verranno bloccate. I clienti non saranno in grado di creare provider di attestazioni né di aggiornare i criteri nell'area primaria
- Tutte le chiamate a operazioni del piano dati, incluse le attestazioni, continueranno a funzionare nell'area primaria

## <a name="next-steps"></a>Passaggi successivi
- Informazioni sui [concetti di base di Attestazione di Azure](basic-concepts.md)
- [Come creare e firmare un criterio di attestazione](author-sign-policy.md)
- [Configurare Attestazione di Azure con PowerShell](quickstart-powershell.md)

