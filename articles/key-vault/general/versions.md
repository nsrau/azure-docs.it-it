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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86230826"
---
# <a name="key-vault-versions"></a>Versioni di Key Vault

Ecco le novità di Azure Key Vault. Le nuove funzionalità e i miglioramenti vengono annunciati anche sul [canale di Key Vault di aggiornamenti di Azure](https://azure.microsoft.com/updates/?category=security&query=Key%20vault).

## <a name="june-2020"></a>Giugno 2020

Monitoraggio di Azure per Key Vault è ora disponibile in anteprima.  Monitoraggio di Azure offre il monitoraggio completo degli insiemi di credenziali delle chiavi mettendo a disposizione una vista unificata di richieste, prestazioni, errori e latenza di Key Vault. Per altre informazioni, vedere [Monitoraggio di Azure per Key Vault (anteprima)](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="may-2020"></a>Maggio 2020

Il modello BYOK (Bring Your Own Key) di Key Vault è ora disponibile a livello generale. Vedere la [specifica BYOK di Azure Key Vault](../keys/byok-specification.md) e l'articolo [Importare chiavi con protezione HSM in Key Vault (BYOK)](../keys/hsm-protected-keys-byok.md).

## <a name="march-2020"></a>Marzo 2020

Gli endpoint privati sono ora disponibili in anteprima. Il servizio Collegamento privato di Azure consente di accedere ad Azure Key Vault e ai servizi di clienti/partner ospitati in Azure tramite un endpoint privato nella rete virtuale.  Vedere come [integrare Key Vault con Collegamento privato di Azure](private-link-service.md).

## <a name="2019"></a>2019

- Rilascio della nuova generazione di Azure Key Vault SDK. Per esempi relativi all'uso, vedere gli argomenti di avvio rapido sui segreti di Azure Key Vault per [Python](../secrets/quick-create-python.md), [.NET](../secrets/quick-create-net.md), [Java](../secrets/quick-create-java.md) e [Node.js](../secrets/quick-create-node.md)
- Nuovi criteri di Azure per gestire i certificati degli insiemi di credenziali delle chiavi. Vedere [Definizioni predefinite di Criteri di Azure per Key Vault](../policy-samples.md).
- Estensione macchina virtuale di Azure Key Vault ora disponibile a livello generale.  Vedere [Estensione macchina virtuale di Key Vault per Linux](../../virtual-machines/extensions/key-vault-linux.md) e [Estensione macchina virtuale di Key Vault per Windows](../../virtual-machines/extensions/key-vault-windows.md).
- Gestione dei segreti basati su eventi per Azure Key Vault ora disponibile in Griglia di eventi di Azure. Per altre informazioni, vedere [lo schema di Griglia di eventi per eventi in Azure Key Vault](../../event-grid/event-schema-key-vault.md] e l'articolo [Ricevere e rispondere alle notifiche di Key Vault con Griglia di eventi di Azure](event-grid-tutorial.md).

## <a name="2018"></a>2018

Nuove funzionalità e integrazioni rilasciate quest'anno:

- Integrazione con Funzioni di Azure. Per uno scenario di esempio che sfrutta [Funzioni di Azure](../../azure-functions/index.yml) per le operazioni dell'insieme di credenziali delle chiavi, vedere [Automatizzare la rotazione di un segreto](../secrets/tutorial-rotation.md). 
- [Integrazione con Azure Databricks](/azure/databricks/scenarios/store-secrets-azure-key-vault). Con questa integrazione, Azure Databricks supporta ora due tipi di ambiti dei segreti, con supporto di Azure Key Vault e con supporto di Databricks. Per altre informazioni, vedere [Creare un ambito dei segreti con supporto di Azure Key Vault](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope)
- [Endpoint di servizio di rete virtuale per Azure Key Vault](overview-vnet-service-endpoints.md).

## <a name="2016"></a>2016

Nuove funzionalità rilasciate quest'anno:

- Chiavi gestite di account di archiviazione. La funzionalità delle chiavi di account di archiviazione si integra ancora più facilmente con Archiviazione di Azure. Per altre informazioni, vedere l'argomento introduttivo [Chiavi dell'account di archiviazione Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-storage-keys).
- Eliminazione temporanea. La funzionalità di eliminazione temporanea migliora la protezione dei dati degli insiemi di credenziali delle chiavi e dei relativi oggetti. Per altre informazioni, vedere l'argomento introduttivo [Panoramica di eliminazione temporanea di Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).

## <a name="2015"></a>2015

Nuove funzionalità rilasciate quest'anno:
- Gestione dei certificati. Aggiunta come funzionalità alla versione 2015-06-01 in disponibilità generale il 26 settembre 2016.

La disponibilità generale (versione 2015-06-01) è stata annunciata il 24 giugno 2015. In questa versione sono state apportate le modifiche seguenti: 
- Eliminazione di una chiave: campo "use" rimosso.
- Recupero di informazioni relative a una chiave: campo "use" rimosso.
- Importazione di una chiave in un insieme di credenziali: campo "use" rimosso.
- Ripristino di una chiave: campo "use" rimosso.     
- Sostituzione di "RSA_OAEP" con "RSA-OAEP" per gli algoritmi RSA. Vedere [Informazioni su chiavi, segreti e certificati](about-keys-secrets-certificates.md).    
 
La seconda versione di anteprima (2015-02-01-preview) è stata annunciata il 20 aprile 2015. Per altre informazioni, vedere il post di blog [REST API Update](https://docs.microsoft.com/archive/blogs/kv/rest-api-update) (Aggiornamento API REST). Sono state aggiornate le attività seguenti:
 
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
 
La prima versione di anteprima (2014-12-08-preview) è stata annunciata l'8 gennaio 2015.  
 
## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su chiavi, segreti e certificati](about-keys-secrets-certificates.md)
- [Chiavi](../keys/index.yml)
- [Segreti](../secrets/index.yml)
- [Certificati](../certificates/index.yml)
