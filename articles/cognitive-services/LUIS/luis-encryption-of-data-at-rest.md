---
title: Lingua Informazioni sulla crittografia del servizio dei dati inattivi
titleSuffix: Azure Cognitive Services
description: Lingua Informazioni sulla crittografia del servizio dei dati inattivi.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 59e066974f690bda2384504cc27af5aa94b7b75b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372337"
---
# <a name="language-understanding-service-encryption-of-data-at-rest"></a>Lingua Informazioni sulla crittografia del servizio dei dati inattivi

Il servizio Language Understanding crittografa automaticamente i dati quando vengono salvati in modo permanente nel cloud. La crittografia del servizio Language Understanding protegge i dati e consente di soddisfare gli impegni di sicurezza e conformità dell'organizzazione.

## <a name="about-cognitive-services-encryption"></a>Informazioni sulla crittografia di Servizi cognitivi

I dati vengono crittografati e decrittografati utilizzando la crittografia [AES conforme](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) a [FIPS 140-2.](https://en.wikipedia.org/wiki/FIPS_140-2) Crittografia e decrittografia sono trasparenti, il che significa che la crittografia e l'accesso sono gestiti per voi. I dati sono al sicuro per impostazione predefinita e non è necessario modificare il codice o le applicazioni per sfruttare i vantaggi della crittografia.

## <a name="about-encryption-key-management"></a>Informazioni sulla gestione delle chiavi di crittografia

Per impostazione predefinita, la sottoscrizione usa chiavi di crittografia gestite da Microsoft.By default, your subscription uses Microsoft-managed encryption keys. C'è anche un'opzione per gestire l'abbonamento con le proprie chiavi. I tasti gestiti dal cliente (CMK, Customer-managed Key), offrono una maggiore flessibilità per creare, ruotare, disabilitare e revocare i controlli di accesso. È inoltre possibile controllare le chiavi di crittografia utilizzate per proteggere i dati.

## <a name="customer-managed-keys-with-azure-key-vault"></a>Chiavi gestite dal cliente con l'insieme di credenziali delle chiavi di AzureCustomer-managed keys with Azure Key Vault

C'è anche un'opzione per gestire l'abbonamento con le proprie chiavi. Le chiavi gestite dal cliente (CMK), note anche come Bring your own key (BYOK), offrono una maggiore flessibilità per creare, ruotare, disabilitare e revocare i controlli di accesso. È inoltre possibile controllare le chiavi di crittografia utilizzate per proteggere i dati.

È necessario usare l'insieme di credenziali delle chiavi di Azure per archiviare le chiavi gestite dal cliente. È possibile creare chiavi personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure usare le API dell'insieme di credenziali delle chiavi di Azure per generare le chiavi. La risorsa Servizi cognitivi e l'insieme di credenziali delle chiavi devono trovarsi nella stessa area e nello stesso tenant di Azure Active Directory (Azure AD), ma possono trovarsi in sottoscrizioni diverse. Per altre informazioni su Archiviazione delle chiavi di Azure, vedere [Che cos'è l'insieme di](https://docs.microsoft.com/azure/key-vault/key-vault-overview)credenziali delle chiavi di Azure?.

### <a name="customer-managed-keys-for-language-understanding"></a>Chiavi gestite dal cliente per la comprensione della lingua

Per richiedere la possibilità di utilizzare chiavi gestite dal cliente, compilare e inviare il modulo di richiesta della [chiave gestita dal cliente LUIS](https://aka.ms/cogsvc-cmk). Ci vorranno circa 3-5 giorni lavorativi per riascoltare lo stato della tua richiesta. A seconda della richiesta, è possibile essere inseriti in una coda e approvati non appena lo spazio diventa disponibile. Dopo l'approvazione per l'utilizzo di CMK con LUIS, è necessario creare una nuova risorsa di comprensione della lingua dal portale di Azure e selezionare E0 come livello di prezzo. Il nuovo SKU funzionerà come lo SKU F0 già disponibile ad eccezione di CMK. Gli utenti non saranno in grado di eseguire l'aggiornamento da F0 al nuovo SKU E0.

Le risorse E0 sono disponibili solo per il servizio di creazione e modifica e il livello E0 inizialmente sarà supportato solo nell'area Stati Uniti occidentali.

![Immagine dell'abbonamento A LUIS](../media/cognitive-services-encryption/luis-subscription.png)

### <a name="regional-availability"></a>Disponibilità a livello di area

Le chiavi gestite dal cliente sono attualmente disponibili nell'area **Stati Uniti occidentali.**

### <a name="limitations"></a>Limitazioni

Esistono alcune limitazioni quando si utilizza il livello E0 con applicazioni esistenti/create in precedenza:There are some limitations when using the E0 tier with existing/previously created applications:

* La migrazione a una risorsa E0 verrà bloccata. Gli utenti potranno eseguire la migrazione delle app solo alle risorse F0. Dopo aver eseguito la migrazione di una risorsa esistente a F0, è possibile creare una nuova risorsa nel livello E0. Ulteriori informazioni sulla [migrazione sono disponibili qui](https://docs.microsoft.com/azure/cognitive-services/luis/luis-migration-authoring).  
* Lo spostamento delle applicazioni da o verso una risorsa E0 verrà bloccato. Una aggirarsi per questa limitazione consiste nell'esportare l'applicazione esistente e importarla come risorsa E0.
* La funzionalità controllo ortografico Bing non è supportata.
* La registrazione del traffico degli utenti finali è disabilitata se l'applicazione è E0.
* La funzionalità di innesco vocale del servizio Bot di Azure non è supportata per le applicazioni nel livello E0.The Speech priming capability from the Azure Bot service isn't supported for applications in the E0 tier. Questa funzionalità è disponibile tramite il servizio bot di Azure, che non supporta CMK.
* The speech priming capability from the portal requires Azure Blob Storage. Per ulteriori informazioni, vedere [portare il proprio spazio di archiviazione](../Speech-Service/speech-encryption-of-data-at-rest.md#bring-your-own-storage-byos-for-customization-and-logging).

### <a name="enable-customer-managed-keys"></a>Abilitare le chiavi gestite dal clienteEnable customer-managed keys

Una nuova risorsa servizi cognitivi viene sempre crittografata utilizzando chiavi gestite da Microsoft.A new Cognitive Services resource is always encrypted using Microsoft-managed keys. Non è possibile abilitare le chiavi gestite dal cliente al momento della creazione della risorsa. Le chiavi gestite dal cliente vengono archiviate nell'insieme di credenziali delle chiavi di Azure e è necessario eseguire il provisioning dell'insieme di credenziali delle chiavi con criteri di accesso che concedono le autorizzazioni chiave all'identità gestita associata alla risorsa Servizi cognitivi. L'identità gestita è disponibile solo dopo la creazione della risorsa tramite il piano tariffario per CMK.

Per informazioni su come usare le chiavi gestite dal cliente con l'insieme di credenziali delle chiavi di Azure per la crittografia dei servizi cognitivi, vedere:To learn how to use customer-managed keys with Azure Key Vault for Cognitive Services encryption, see:

- [Configurare le chiavi gestite dal cliente con Key Vault per la crittografia di Cognitive Services dal portale di AzureConfigure customer-managed keys with Key Vault for Cognitive Services encryption from the Azure portal](../Encryption/cognitive-services-encryption-keys-portal.md)

L'abilitazione delle chiavi gestite dai clienti consentirà anche un'identità gestita assegnata dal sistema, una funzionalità di Azure AD. Una volta abilitata l'identità gestita assegnata dal sistema, questa risorsa verrà registrata con Azure Active Directory.Once the system assigned managed identity is enabled, this resource will be registered with Azure Active Directory. Dopo la registrazione, all'identità gestita verrà concesso l'accesso all'insieme di credenziali delle chiavi selezionato durante l'impostazione della chiave gestita dal cliente. Ulteriori informazioni sulle [identità gestite](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

> [!IMPORTANT]
> Se si disabilitano le identità gestite assegnate al sistema, l'accesso all'insieme di credenziali delle chiavi verrà rimosso e tutti i dati crittografati con le chiavi del cliente non saranno più accessibili. Tutte le funzioni dipendenti da questi dati smetteranno di funzionare.

> [!IMPORTANT]
> Le identità gestite attualmente non supportano gli scenari tra directory. Quando si configurano chiavi gestite dal cliente nel portale di Azure, un'identità gestita viene assegnata automaticamente sotto le coperte. Se successivamente si sposta la sottoscrizione, il gruppo di risorse o la risorsa da una directory di Azure AD a un'altra, l'identità gestita associata alla risorsa non viene trasferita al nuovo tenant, pertanto le chiavi gestite dal cliente potrebbero non funzionare più. Per altre informazioni, vedere Trasferimento di una sottoscrizione tra directory di Azure AD nelle domande frequenti e problemi noti con le identità gestite per le risorse di Azure.For more information, see **Transferring** a subscription between Azure AD directories in [FAQs and known issues with managed identities for Azure resources.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Archiviare le chiavi gestite dal cliente nell'insieme di credenziali delle chiavi di AzureStore customer-managed keys in Azure Key Vault

Per abilitare le chiavi gestite dal cliente, è necessario usare un insieme di credenziali delle chiavi di Azure per archiviare le chiavi. È necessario attivare entrambe le proprietà **Soft Delete** e **Do Not Purge** nell'insieme di credenziali delle chiavi.

Solo le chiavi RSA di dimensioni 2048 sono supportate con la crittografia di Servizi cognitivi. Per altre informazioni sulle chiavi, vedere **Chiavi dell'insieme di credenziali delle chiavi** in Informazioni su chiavi, segreti e certificati dell'insieme di credenziali delle chiavi di [Azure.](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)

### <a name="rotate-customer-managed-keys"></a>Ruotare le chiavi gestite dal cliente

È possibile ruotare una chiave gestita dal cliente in Archiviazione delle chiavi di Azure in base ai criteri di conformità. Quando la chiave viene ruotata, è necessario aggiornare la risorsa Servizi cognitivi per usare il nuovo URI della chiave. Per informazioni su come aggiornare la risorsa per usare una nuova versione della chiave nel portale di Azure, vedere la sezione relativa **all'aggiornamento della versione della chiave** in [Configurare le chiavi gestite dal cliente per Servizi cognitivi tramite il portale](../Encryption/cognitive-services-encryption-keys-portal.md)di Azure.

La rotazione della chiave non attiva la ricrittografia dei dati nella risorsa. Non è richiesta alcuna ulteriore azione da parte dell'utente.

### <a name="revoke-access-to-customer-managed-keys"></a>Revoca dell'accesso alle chiavi gestite dal cliente

To revoke access to customer-managed keys, use PowerShell or Azure CLI. Per altre informazioni, vedere Azure Key Vault PowerShell o interfaccia della riga [di comando dell'insieme di credenziali delle chiavi](https://docs.microsoft.com/cli/azure/keyvault)di Azure.For more information, see Azure Key Vault [PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) or Azure Key Vault CLI . La revoca dell'accesso blocca in modo efficace l'accesso a tutti i dati nella risorsa Servizi cognitivi, poiché la chiave di crittografia non è accessibile da Servizi cognitivi.

## <a name="next-steps"></a>Passaggi successivi

* [Modulo di richiesta chiave gestita dal cliente del servizio LUIS](https://aka.ms/cogsvc-cmk)
* [Altre informazioni su Azure Key VaultLearn more about Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
