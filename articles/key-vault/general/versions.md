---
title: Versioni Azure Key Vault
description: Le diverse versioni di Azure Key Vault
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.date: 06/30/2019
ms.author: mbaldwin
ms.openlocfilehash: 58c5fdcfef5e866d0c69d65412582c0ed649f7c6
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86230826"
---
# <a name="key-vault-versions"></a>Versioni di Key Vault

Ecco le novità con Azure Key Vault. Vengono inoltre annunciate nuove funzionalità e miglioramenti nel [canale Key Vault degli aggiornamenti di Azure](https://azure.microsoft.com/updates/?category=security&query=Key%20vault).

## <a name="june-2020"></a>Giugno 2020

Il monitoraggio di Azure per Key Vault è ora disponibile in anteprima.  Monitoraggio di Azure offre un monitoraggio completo degli insiemi di credenziali delle chiavi grazie a una visualizzazione unificata delle richieste di Key Vault, delle prestazioni, degli errori e della latenza. Per ulteriori informazioni, vedere [monitoraggio di Azure per Key Vault (anteprima).](../../azure-monitor/insights/key-vault-insights-overview.md)

## <a name="may-2020"></a>Maggio 2020

Key Vault "Bring your own key" (BYOK) è ora disponibile a livello generale. Vedere la [specifica Azure Key Vault BYOK](../keys/byok-specification.md)e informazioni su come [importare chiavi HSM protette Key Vault (BYOK)](../keys/hsm-protected-keys-byok.md).

## <a name="march-2020"></a>Marzo 2020

Gli endpoint privati sono ora disponibili in anteprima. Il servizio di collegamento privato di Azure consente di accedere a Azure Key Vault e ai servizi cliente/partner ospitati di Azure tramite un endpoint privato nella rete virtuale.  Informazioni su come [integrare Key Vault con collegamento privato di Azure](private-link-service.md).

## <a name="2019"></a>2019

- Rilascio degli SDK di Azure Key Vault di prossima generazione. Per esempi relativi all'uso, vedere le guide introduttive di Azure Key Vault Secret per [Python](../secrets/quick-create-python.md), [.NET](../secrets/quick-create-net.md), [Java](../secrets/quick-create-java.md)e [Node.js](../secrets/quick-create-node.md)
- Nuovi criteri di Azure per gestire i certificati di Key Vault. Vedere le [definizioni predefinite dei criteri di Azure per Key Vault](../policy-samples.md).
- Azure Key Vault estensione della macchina virtuale ora disponibile a livello generale.  Vedere [Key Vault estensione macchina virtuale per Linux](../../virtual-machines/extensions/key-vault-linux.md) e [Key Vault estensione macchina virtuale per Windows](../../virtual-machines/extensions/key-vault-windows.md).
- Gestione dei segreti basati su eventi per Azure Key Vault ora disponibile in griglia di eventi di Azure. Per ulteriori informazioni, vedere [lo schema di griglia di eventi per gli eventi nel Azure Key Vault] (.. /.. /Event-Grid/Event-schema-Key-Vault.MD] e informazioni su come [ricevere e rispondere alle notifiche di Key Vault con griglia di eventi di Azure](event-grid-tutorial.md).

## <a name="2018"></a>2018

Nuove funzionalità e integrazioni rilasciate quest'anno:

- Integrazione con funzioni di Azure. Per uno scenario di esempio basato su [funzioni di Azure](../../azure-functions/index.yml) per le operazioni dell'insieme di credenziali delle chiavi, vedere [automatizzare la rotazione di un segreto](../secrets/tutorial-rotation.md). 
- [Integrazione con Azure Databricks](/azure/databricks/scenarios/store-secrets-azure-key-vault). Con questa operazione, Azure Databricks supporta ora due tipi di ambiti segreti: supportato da Azure Key Vault e databricks. Per altre informazioni, vedere [creare un ambito Secret con supporto di Azure Key Vault](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope)
- [Endpoint del servizio rete virtuale per Azure Key Vault](overview-vnet-service-endpoints.md).

## <a name="2016"></a>2016

Nuove funzionalità rilasciate quest'anno:

- Chiavi dell'account di archiviazione gestito. La funzionalità chiavi dell'account di archiviazione ha aggiunto una più facile integrazione con archiviazione di Azure. Per altre informazioni, vedere l'argomento introduttivo [Chiavi dell'account di archiviazione Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-storage-keys).
- Eliminazione temporanea. La funzionalità di eliminazione temporanea migliora la protezione dei dati degli insiemi di credenziali delle chiavi e degli oggetti di Key Vault. Per altre informazioni, vedere l'argomento introduttivo [Panoramica di eliminazione temporanea di Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).

## <a name="2015"></a>2015

Nuove funzionalità rilasciate quest'anno:
- Gestione dei certificati. Aggiunta come funzionalità alla versione GA 2015-06-01 del 26 settembre 2016.

La disponibilità generale (versione 2015-06-01) è stata annunciata il 24 giugno 2015. In questa versione sono state apportate le modifiche seguenti: 
- Eliminare una chiave: il campo "use" è stato rimosso.
- Ottenere informazioni su un campo chiave: "use" rimosso.
- Importare una chiave in un insieme di credenziali: il campo "use" è stato rimosso.
- Ripristinare una chiave: il campo "use" è stato rimosso.     
- "RSA_OAEP" è stato modificato in "RSA-OAEP" per gli algoritmi RSA. Vedere [informazioni su chiavi, segreti e certificati](about-keys-secrets-certificates.md).    
 
La seconda versione di anteprima (versione 2015-02-01-Preview) è stata annunciata il 20 aprile 2015. Per altre informazioni, vedere il post di blog [REST API Update](https://docs.microsoft.com/archive/blogs/kv/rest-api-update) (Aggiornamento API REST). Sono state aggiornate le attività seguenti:
 
- Elencare le chiavi in un insieme di credenziali - aggiunta del supporto per la paginazione all'operazione.
- Elencare le versioni di una chiave - aggiunta dell'operazione per elencare le versioni di una chiave.  
- Elencare i segreti in un insieme di credenziali - aggiunta del supporto per la paginazione.
- Elencare le versioni di un segreto - aggiunta di un'operazione per elencare le versioni di un segreto.  
- Tutte le operazioni - aggiunta del timestamp di creazione/aggiornamento agli attributi.  
- Creare un segreto - aggiunta di Content-Type ai segreti.
- Creare una chiave - aggiunta di tag come informazioni facoltative.
- Creare un segreto - aggiunta di tag come informazioni facoltative.
- Aggiornare una chiave - aggiunta di tag come informazioni facoltative.
- Aggiornare un segreto - aggiunta di tag come informazioni facoltative.
- Modifica delle dimensioni massime dei segreti da 10 K a 25 kB. Vedere [Informazioni su chiavi, segreti e certificati](about-keys-secrets-certificates.md).    
 
## <a name="2014"></a>2014
 
La prima versione di anteprima (versione 2014-12-08-Preview) è stata annunciata l'8 gennaio 2015.  
 
## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su chiavi, segreti e certificati](about-keys-secrets-certificates.md)
- [Chiavi](../keys/index.yml)
- [Segreti](../secrets/index.yml)
- [Certificati](../certificates/index.yml)
