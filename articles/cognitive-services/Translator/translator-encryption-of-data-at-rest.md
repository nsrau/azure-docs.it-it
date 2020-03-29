---
title: Traduttore crittografia dei dati inattivi
titleSuffix: Azure Cognitive Services
description: Traduttore crittografia dei dati inattivi.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 44bb223dbc944be1b8769aa2572f1b88b916528b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372238"
---
# <a name="translator-encryption-of-data-at-rest"></a>Traduttore crittografia dei dati inattivi

Translator crittografa automaticamente i dati, che carichi per creare modelli di traduzione personalizzati, quando vengono resi persistenti nel cloud, contribuendo a soddisfare gli obiettivi di sicurezza e conformità dell'organizzazione.

## <a name="about-cognitive-services-encryption"></a>Informazioni sulla crittografia di Servizi cognitivi

I dati vengono crittografati e decrittografati utilizzando la crittografia [AES conforme](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) a [FIPS 140-2.](https://en.wikipedia.org/wiki/FIPS_140-2) Crittografia e decrittografia sono trasparenti, il che significa che la crittografia e l'accesso sono gestiti per voi. I dati sono al sicuro per impostazione predefinita e non è necessario modificare il codice o le applicazioni per sfruttare i vantaggi della crittografia.

## <a name="about-encryption-key-management"></a>Informazioni sulla gestione delle chiavi di crittografia

Per impostazione predefinita, la sottoscrizione usa chiavi di crittografia gestite da Microsoft.By default, your subscription uses Microsoft-managed encryption keys. Se si usa un piano tariffario che supporta le chiavi gestite dal cliente, è possibile visualizzare le impostazioni di crittografia per la risorsa nella sezione **Crittografia** del portale di [Azure,](https://portal.azure.com)come illustrato nell'immagine seguente.

![Visualizzare le impostazioni di crittografia](../media/cognitive-services-encryption/encryptionblade.png)

Per le sottoscrizioni che supportano solo chiavi di crittografia gestite da Microsoft, non sarà disponibile una sezione **Crittografia.For** subscriptions that only support Microsoft-managed encryption keys, you will not have an Encryption section.

## <a name="customer-managed-keys-with-azure-key-vault"></a>Chiavi gestite dal cliente con l'insieme di credenziali delle chiavi di AzureCustomer-managed keys with Azure Key Vault

C'è anche un'opzione per gestire l'abbonamento con le proprie chiavi. Le chiavi gestite dal cliente (CMK), note anche come Bring your own key (BYOK), offrono una maggiore flessibilità per creare, ruotare, disabilitare e revocare i controlli di accesso. È inoltre possibile controllare le chiavi di crittografia utilizzate per proteggere i dati.

> [!IMPORTANT]
> Le chiavi gestite dal cliente sono disponibili per tutti i piani tariffari per il servizio Translator. Per richiedere la possibilità di utilizzare chiavi gestite dal cliente, compilare e inviare il modulo di [richiesta della chiave gestita dal cliente traduttore](https://aka.ms/cogsvc-cmk) Ci vorranno circa 3-5 giorni lavorativi per riascoltare lo stato della richiesta. A seconda della richiesta, è possibile essere inseriti in una coda e approvati non appena lo spazio diventa disponibile. Una volta approvato per l'utilizzo di CMK con il servizio Translator, è necessario creare una nuova risorsa Translator. Dopo aver creato la risorsa Translator, è possibile usare L'insieme di credenziali delle chiavi di Azure per configurare l'identità gestita.

Seguire questi passaggi per abilitare le chiavi gestite dal cliente per Translator:Follow these steps to enable customer-managed keys for Translator:

1. Creare la nuova risorsa Testo traduttore o Servizi cognitivi regionali. Questo non funzionerà con una risorsa globale.
2. Identità gestita abilitata nel portale di Azure e aggiungere le informazioni sulla chiave gestita dal cliente.
3. Creare una nuova area di lavoro in Custom Translator e associare queste informazioni sulla sottoscrizione.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

### <a name="enable-customer-managed-keys"></a>Abilitare le chiavi gestite dal clienteEnable customer-managed keys

È necessario usare l'insieme di credenziali delle chiavi di Azure per archiviare le chiavi gestite dal cliente. È possibile creare chiavi personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure usare le API dell'insieme di credenziali delle chiavi di Azure per generare le chiavi. La risorsa Servizi cognitivi e l'insieme di credenziali delle chiavi devono trovarsi nella stessa area e nello stesso tenant di Azure Active Directory (Azure AD), ma possono trovarsi in sottoscrizioni diverse. Per altre informazioni su Archiviazione delle chiavi di Azure, vedere [Che cos'è l'insieme di](https://docs.microsoft.com/azure/key-vault/key-vault-overview)credenziali delle chiavi di Azure?.

Una nuova risorsa servizi cognitivi viene sempre crittografata utilizzando chiavi gestite da Microsoft.A new Cognitive Services resource is always encrypted using Microsoft-managed keys. Non è possibile abilitare le chiavi gestite dal cliente al momento della creazione della risorsa. Le chiavi gestite dal cliente vengono archiviate nell'insieme di credenziali delle chiavi di Azure e è necessario eseguire il provisioning dell'insieme di credenziali delle chiavi con criteri di accesso che concedono le autorizzazioni chiave all'identità gestita associata alla risorsa Servizi cognitivi. L'identità gestita è disponibile non appena viene creata la risorsa.

Per informazioni su come usare le chiavi gestite dal cliente con l'insieme di credenziali delle chiavi di Azure per la crittografia dei servizi cognitivi, vedere:To learn how to use customer-managed keys with Azure Key Vault for Cognitive Services encryption, see:

- [Configurare le chiavi gestite dal cliente con Key Vault per la crittografia di Cognitive Services dal portale di AzureConfigure customer-managed keys with Key Vault for Cognitive Services encryption from the Azure portal](../Encryption/cognitive-services-encryption-keys-portal.md)

L'abilitazione delle chiavi gestite dai clienti consentirà anche un'identità gestita assegnata dal sistema, una funzionalità di Azure AD. Una volta abilitata l'identità gestita assegnata dal sistema, questa risorsa verrà registrata con Azure Active Directory.Once the system assigned managed identity is enabled, this resource will be registered with Azure Active Directory. Dopo la registrazione, all'identità gestita verrà concesso l'accesso all'insieme di credenziali delle chiavi selezionato durante l'impostazione della chiave gestita dal cliente. Ulteriori informazioni sulle [identità gestite](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

> [!IMPORTANT]
> Se si disabilitano le identità gestite assegnate al sistema, l'accesso all'insieme di credenziali delle chiavi verrà rimosso e tutti i dati crittografati con le chiavi del cliente non saranno più accessibili. Tutte le funzioni dipendenti da questi dati smetteranno di funzionare. Anche tutti i modelli distribuiti verranno annullati. Tutti i dati caricati verranno eliminati da Custom Translator. Se le identità gestite vengono riabilitate, il modello non verrà ridistribuito automaticamente.

> [!IMPORTANT]
> Le identità gestite attualmente non supportano gli scenari tra directory. Quando si configurano chiavi gestite dal cliente nel portale di Azure, un'identità gestita viene assegnata automaticamente sotto le coperte. Se successivamente si sposta la sottoscrizione, il gruppo di risorse o la risorsa da una directory di Azure AD a un'altra, l'identità gestita associata alla risorsa non viene trasferita al nuovo tenant, pertanto le chiavi gestite dal cliente potrebbero non funzionare più. Per altre informazioni, vedere Trasferimento di una sottoscrizione tra directory di Azure AD nelle domande frequenti e problemi noti con le identità gestite per le risorse di Azure.For more information, see **Transferring** a subscription between Azure AD directories in [FAQs and known issues with managed identities for Azure resources.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Archiviare le chiavi gestite dal cliente nell'insieme di credenziali delle chiavi di AzureStore customer-managed keys in Azure Key Vault

Per abilitare le chiavi gestite dal cliente, è necessario usare un insieme di credenziali delle chiavi di Azure per archiviare le chiavi. È necessario attivare entrambe le proprietà **Soft Delete** e **Do Not Purge** nell'insieme di credenziali delle chiavi.

Solo le chiavi RSA di dimensioni 2048 sono supportate con la crittografia di Servizi cognitivi. Per altre informazioni sulle chiavi, vedere **Chiavi dell'insieme di credenziali delle chiavi** in Informazioni su chiavi, segreti e certificati dell'insieme di credenziali delle chiavi di [Azure.](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)

> [!NOTE]
> Se l'intero insieme di credenziali delle chiavi viene eliminato, i dati non verranno più visualizzati e tutti i modelli verranno annullati. Tutti i dati caricati verranno eliminati da Custom Translator. 

### <a name="revoke-access-to-customer-managed-keys"></a>Revoca dell'accesso alle chiavi gestite dal cliente

To revoke access to customer-managed keys, use PowerShell or Azure CLI. Per altre informazioni, vedere Azure Key Vault PowerShell o interfaccia della riga [di comando dell'insieme di credenziali delle chiavi](https://docs.microsoft.com/cli/azure/keyvault)di Azure.For more information, see Azure Key Vault [PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) or Azure Key Vault CLI . La revoca dell'accesso blocca in modo efficace l'accesso a tutti i dati nella risorsa Servizi cognitivi e i modelli non verranno distribuiti, poiché la chiave di crittografia non è accessibile da Servizi cognitivi. Tutti i dati caricati verranno eliminati anche da Custom Translator.


## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni su Azure Key VaultLearn more about Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
