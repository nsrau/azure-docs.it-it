---
title: Crittografia dei dati inattivi di Language Understanding Service
titleSuffix: Azure Cognitive Services
description: Microsoft offre chiavi di crittografia gestite da Microsoft e consente inoltre di gestire le sottoscrizioni di servizi cognitivi con chiavi personalizzate, denominate chiavi gestite dal cliente (CMK). Questo articolo illustra la crittografia dei dati inattivi per Language Understanding (LUIS) e come abilitare e gestire CMK.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: ce6561652801d52e5600ddc63e573070281da3f2
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078130"
---
# <a name="language-understanding-service-encryption-of-data-at-rest"></a>Crittografia dei dati inattivi di Language Understanding Service

Il servizio Language Understanding crittografa automaticamente i dati quando vengono salvati in modo permanente nel cloud. La crittografia del servizio Language Understanding protegge i dati e consente di soddisfare gli impegni di sicurezza e conformità dell'organizzazione.

## <a name="about-cognitive-services-encryption"></a>Informazioni sulla crittografia di servizi cognitivi

I dati vengono crittografati e decrittografati usando la crittografia [AES a 256 bit](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) conforme allo standard [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) . La crittografia e la decrittografia sono trasparenti, ovvero la crittografia e l'accesso vengono gestite per l'utente. I dati sono protetti per impostazione predefinita e non è necessario modificare il codice o le applicazioni per sfruttare la crittografia.

## <a name="about-encryption-key-management"></a>Informazioni sulla gestione delle chiavi di crittografia

Per impostazione predefinita, la sottoscrizione usa chiavi di crittografia gestite da Microsoft. È anche possibile gestire la sottoscrizione con chiavi personalizzate denominate chiavi gestite dal cliente (CMK). CMK offre una maggiore flessibilità per creare, ruotare, disabilitare e revocare i controlli di accesso. È anche possibile controllare le chiavi di crittografia usate per proteggere i dati.

## <a name="customer-managed-keys-with-azure-key-vault"></a>Chiavi gestite dal cliente con Azure Key Vault

È anche possibile gestire la sottoscrizione con le proprie chiavi. Le chiavi gestite dal cliente (CMK), note anche come Bring your own key (BYOK), offrono una maggiore flessibilità per creare, ruotare, disabilitare e revocare i controlli di accesso. È anche possibile controllare le chiavi di crittografia usate per proteggere i dati.

È necessario utilizzare Azure Key Vault per archiviare le chiavi gestite dal cliente. È possibile creare chiavi personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure usare le API Azure Key Vault per generare chiavi. La risorsa Servizi cognitivi e l'insieme di credenziali delle chiavi devono trovarsi nella stessa area e nello stesso tenant di Azure Active Directory (Azure AD), ma possono trovarsi in sottoscrizioni diverse. Per ulteriori informazioni su Azure Key Vault, vedere [che cos'è Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

### <a name="customer-managed-keys-for-language-understanding"></a>Chiavi gestite dal cliente per Language Understanding

Per richiedere la possibilità di usare chiavi gestite dal cliente, compilare e inviare il [modulo di richiesta della chiave gestita dal cliente del servizio Luis](https://aka.ms/cogsvc-cmk). Saranno richiesti circa 3-5 giorni lavorativi per ricevere informazioni sullo stato della richiesta. A seconda della richiesta, è possibile che venga inserita in una coda e approvata quando lo spazio diventa disponibile. Una volta approvate per l'uso di CMK con LUIS, è necessario creare una nuova risorsa Language Understanding dal portale di Azure e selezionare E0 come piano tariffario. Il nuovo SKU funzionerà come lo SKU F0 già disponibile ad eccezione di CMK. Gli utenti non saranno in grado di eseguire l'aggiornamento da F0 al nuovo SKU di E0.

![Immagine della sottoscrizione LUIS](../media/cognitive-services-encryption/luis-subscription.png)

### <a name="limitations"></a>Limitazioni

Esistono alcune limitazioni quando si usa il livello E0 con le applicazioni create in precedenza/esistenti:

* La migrazione a una risorsa E0 verrà bloccata. Gli utenti saranno in grado di eseguire la migrazione delle proprie app alle risorse F0. Dopo aver eseguito la migrazione di una risorsa esistente a F0, è possibile creare una nuova risorsa nel livello E0. Altre informazioni sulla [migrazione](https://docs.microsoft.com/azure/cognitive-services/luis/luis-migration-authoring)sono disponibili qui.  
* Lo stato di trasferimento delle applicazioni a o da una risorsa E0 verrà bloccato. Per ovviare a questa limitazione, è possibile esportare l'applicazione esistente e importarla come risorsa E0.
* La funzionalità controllo ortografico Bing non è supportata.
* La registrazione del traffico dell'utente finale è disabilitata se l'applicazione è E0.
* La funzionalità di priming vocale del servizio Azure bot non è supportata per le applicazioni nel livello E0. Questa funzionalità è disponibile tramite il servizio Azure bot, che non supporta CMK.
* La funzionalità di priming vocale del portale richiede l'archiviazione BLOB di Azure. Per altre informazioni, vedere [Bring your own storage](../Speech-Service/speech-encryption-of-data-at-rest.md#bring-your-own-storage-byos-for-customization-and-logging).

### <a name="enable-customer-managed-keys"></a>Abilitare chiavi gestite dal cliente

Una nuova risorsa di servizi cognitivi è sempre crittografata usando chiavi gestite da Microsoft. Non è possibile abilitare le chiavi gestite dal cliente nel momento in cui viene creata la risorsa. Le chiavi gestite dal cliente vengono archiviate in Azure Key Vault e l'insieme di credenziali delle chiavi deve essere sottoposto a provisioning con criteri di accesso che concedono le autorizzazioni chiave all'identità gestita associata alla risorsa Servizi cognitivi. L'identità gestita è disponibile solo dopo la creazione della risorsa usando il piano tariffario per CMK.

Per informazioni su come usare le chiavi gestite dal cliente con Azure Key Vault per la crittografia dei servizi cognitivi, vedere:

- [Configurare chiavi gestite dal cliente con Key Vault per la crittografia dei servizi cognitivi dall'portale di Azure](../Encryption/cognitive-services-encryption-keys-portal.md)

L'abilitazione delle chiavi gestite dal cliente consentirà anche un'identità gestita assegnata dal sistema, una funzionalità di Azure AD. Una volta abilitata l'identità gestita assegnata dal sistema, questa risorsa verrà registrata con Azure Active Directory. Dopo la registrazione, all'identità gestita verrà concesso l'accesso al Key Vault selezionato durante l'installazione della chiave gestita dal cliente. È possibile ottenere altre informazioni sulle [identità gestite](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

> [!IMPORTANT]
> Se si disabilitano le identità gestite assegnate dal sistema, l'accesso all'insieme di credenziali delle chiavi verrà rimosso e i dati crittografati con le chiavi del cliente non saranno più accessibili. Tutte le funzionalità che dipendono da questi dati smetteranno di funzionare.

> [!IMPORTANT]
> Le identità gestite attualmente non supportano gli scenari tra directory. Quando si configurano le chiavi gestite dal cliente nel portale di Azure, un'identità gestita viene assegnata automaticamente dietro le quinte. Se successivamente si sposta la sottoscrizione, il gruppo di risorse o la risorsa da una directory Azure AD a un'altra, l'identità gestita associata alla risorsa non viene trasferita al nuovo tenant, quindi le chiavi gestite dal cliente potrebbero non funzionare più. Per altre informazioni, vedere **trasferimento di una sottoscrizione tra Azure ad directory** nelle [domande frequenti e problemi noti relativi alle identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories).  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Archiviare chiavi gestite dal cliente in Azure Key Vault

Per abilitare le chiavi gestite dal cliente, è necessario usare un Azure Key Vault per archiviare le chiavi. È necessario abilitare l' **eliminazione** temporanea e non **ripulire** le proprietà nell'insieme di credenziali delle chiavi.

Con la crittografia dei servizi cognitivi sono supportate solo le chiavi RSA di dimensioni 2048. Per ulteriori informazioni sulle chiavi, vedere **Key Vault chiavi** in [informazioni su Azure Key Vault chiavi, segreti e certificati](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys).

### <a name="rotate-customer-managed-keys"></a>Ruotare le chiavi gestite dal cliente

È possibile ruotare una chiave gestita dal cliente in Azure Key Vault in base ai criteri di conformità. Quando la chiave viene ruotata, è necessario aggiornare la risorsa Servizi cognitivi per usare il nuovo URI della chiave. Per informazioni su come aggiornare la risorsa per usare una nuova versione della chiave nella portale di Azure, vedere la sezione intitolata **aggiornare la versione della chiave** in [configurare chiavi gestite dal cliente per servizi cognitivi usando il portale di Azure](../Encryption/cognitive-services-encryption-keys-portal.md).

La rotazione della chiave non attiva la nuova crittografia dei dati nella risorsa. Non sono necessarie altre azioni da parte dell'utente.

### <a name="revoke-access-to-customer-managed-keys"></a>Revocare l'accesso alle chiavi gestite dal cliente

Per revocare l'accesso alle chiavi gestite dal cliente, usare PowerShell o l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere [PowerShell per Azure Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault//) o [Interfaccia della riga di comando per Azure Key Vault](https://docs.microsoft.com/cli/azure/keyvault). La revoca dell'accesso blocca in modo efficace l'accesso a tutti i dati nella risorsa Servizi cognitivi, in quanto la chiave di crittografia non è accessibile dai servizi cognitivi.

## <a name="next-steps"></a>Passaggi successivi

* [Modulo di richiesta della chiave gestita dal cliente del servizio LUIS](https://aka.ms/cogsvc-cmk)
* [Altre informazioni su Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
