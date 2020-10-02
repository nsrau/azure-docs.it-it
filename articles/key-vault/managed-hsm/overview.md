---
title: Panoramica del modulo di protezione hardware gestito - Modulo di protezione hardware gestito di Azure | Microsoft Docs
description: Il modulo di protezione hardware gestito di Azure è un servizio cloud che protegge le chiavi crittografiche per le applicazioni cloud.
services: key-vault
tags: azure-resource-manager
ms.service: key-vault
ms.topic: overview
ms.custom: mvc
ms.date: 09/15/2020
ms.author: mbaldwin
author: msmbaldwin
ms.openlocfilehash: 9eee3d5bc53ebe40ba4462f394ffe30cea6b70fa
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90998380"
---
# <a name="what-is-azure-key-vault-managed-hsm-preview"></a>Che cos'è il modulo di protezione hardware gestito di Azure Key Vault (anteprima)?

Il modulo di protezione hardware gestito di Azure Key Vault è un servizio cloud completamente gestito, a disponibilità elevata, a tenant singolo e conforme agli standard che consente di proteggere le chiavi crittografiche per le applicazioni cloud tramite moduli di protezione hardware convalidati in base agli standard **FIPS 140-2 livello 3**.  

## <a name="why-use-managed-hsm"></a>Perché usare il modulo di protezione hardware gestito?

### <a name="fully-managed-highly-available-single-tenant-hsm-as-a-service"></a>Modulo di protezione hardware come servizio completamente gestito, a disponibilità elevata e a tenant singolo

- **Completamente gestito**: le attività di provisioning, configurazione, applicazione di patch e manutenzione del modulo di protezione hardware sono gestite dal servizio. 
- **A disponibilità elevata e con resilienza della zona** (dove sono supportate le zone di disponibilità): ogni cluster di moduli di protezione hardware è costituito da più partizioni HSM che si estendono su almeno due zone di disponibilità. In caso di guasto dell'hardware, verrà eseguita automaticamente la migrazione delle partizioni membro del cluster di moduli di protezione hardware in nodi integri.
- **A tenant singolo**: ogni istanza di modulo di protezione hardware gestito è dedicata a un singolo cliente ed è costituita da un cluster di più partizioni HSM. Ogni cluster di moduli di protezione hardware usa un dominio di sicurezza specifico del cliente, che isola tramite crittografia il cluster di moduli di protezione hardware di ciascun cliente.


### <a name="access-control-enhanced-data-protection--compliance"></a>Controllo di accesso, protezione avanzata dei dati e conformità

- **Gestione centralizzata delle chiavi**: le chiavi con valore elevato di importanza critica nell'intera organizzazione vengono gestite in un'unica posizione. Con le autorizzazioni granulari per chiave è possibile controllare l'accesso a ogni chiave in base al principio dei privilegi di accesso minimi.
- **Controllo di accesso isolato**: il modello di controllo degli accessi in base al ruolo locale del modulo di protezione hardware gestito consente agli amministratori designati del cluster di moduli di protezione hardware di avere il controllo completo su tali moduli e nemmeno gli amministratori del gruppo di gestione, della sottoscrizione o del gruppo di risorse possono aggirare questo controllo.
- **Moduli di protezione hardware convalidati in base agli standard FIPS 140-2 livello 3**: è possibile proteggere i dati e soddisfare i requisiti di conformità con i moduli di protezione hardware convalidati in base agli standard FIPS (Federal Information Protection Standard) 140-2 livello 3. I moduli di protezione hardware gestiti usano la famiglia di moduli Marvell LiquidSecurity.
- **Monitoraggio e controllo**: integrazione completa con Monitoraggio di Azure. È possibile ottenere i log completi di tutte le attività tramite Monitoraggio di Azure e usare Azure Log Analytics per l'analisi e gli avvisi.

### <a name="integrated-with-azure-and-microsoft-paassaas-services"></a>Integrazione con i servizi PaaS/SaaS di Azure e Microsoft 

- È possibile generare chiavi (o importarle tramite [BYOK](hsm-protected-keys-byok.md)) e usarle per crittografare i dati inattivi in servizi di Azure come [Archiviazione di Azure](../../storage/common/encryption-customer-managed-keys.md), [Azure SQL](../../azure-sql/database/transparent-data-encryption-byok-overview.md) e [Azure Information Protection](/azure/information-protection/byok-price-restrictions).

### <a name="uses-same-api-and-management-interfaces-as-key-vault"></a>Uso delle stesse interfacce API e di gestione di Key Vault

- È possibile eseguire facilmente la migrazione delle applicazioni esistenti che usano un insieme di credenziali (multi-tenant) per usare i moduli di protezione hardware gestiti.
- È possibile usare gli stessi modelli di sviluppo e distribuzione di applicazioni per tutte le applicazioni, indipendentemente dalla soluzione di gestione delle chiavi in uso: insiemi di credenziali multi-tenant o moduli di protezione hardware gestiti a tenant singolo.

### <a name="import-keys-from-your-on-premise-hsms"></a>Importare le chiavi dai moduli di protezione hardware locali

- È possibile generare chiavi con protezione HSM nel modulo di protezione hardware locale e importarle in modo sicuro nel modulo di protezione hardware gestito.

## <a name="next-steps"></a>Passaggi successivi
- Vedere [Avvio rapido: Effettuare il provisioning di un modulo di protezione hardware gestito e attivarlo tramite l'interfaccia della riga di comando di Azure](quick-create-cli.md) per creare e attivare un modulo di protezione hardware gestito
- Vedere le [procedure consigliate per l'uso del modulo di protezione hardware gestito di Azure Key Vault](best-practices.md)