---
title: Comandi personalizzati crittografia dei dati inattivi
titleSuffix: Azure Cognitive Services
description: Comandi personalizzati crittografia dei dati inattivi.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2020
ms.author: sausin
ms.openlocfilehash: 83b6e6be8764a86c41bd9156cc96f8a594dbe1e9
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294309"
---
# <a name="custom-commands-encryption-of-data-at-rest"></a>Crittografia dei dati inattivi per i comandi personalizzati

I comandi personalizzati crittografano automaticamente i dati quando vengono salvati in modo permanente nel cloud. La crittografia del servizio Custom Commands protegge i dati e consente di soddisfare gli impegni di sicurezza e conformità dell'organizzazione.

> [!NOTE]
> Il servizio comandi personalizzati non abilita automaticamente la crittografia per le risorse LUIS associate all'applicazione. Se necessario, è necessario abilitare la crittografia per la risorsa LUIS da [qui](./../LUIS/luis-encryption-of-data-at-rest.md).

## <a name="about-cognitive-services-encryption"></a>Informazioni sulla crittografia di servizi cognitivi
I dati vengono crittografati e decrittografati usando la crittografia [AES a 256 bit](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) conforme allo standard [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) . La crittografia e la decrittografia sono trasparenti, ovvero la crittografia e l'accesso vengono gestite per l'utente. I dati sono protetti per impostazione predefinita e non è necessario modificare il codice o le applicazioni per sfruttare i vantaggi della crittografia.

## <a name="about-encryption-key-management"></a>Informazioni sulla gestione delle chiavi di crittografia

Quando si usano comandi personalizzati, il servizio di riconoscimento vocale archivia i dati seguenti nel cloud:
* JSON di configurazione dietro l'applicazione comandi personalizzati
* Chiave di stima e creazione di LUIS

Per impostazione predefinita, la sottoscrizione USA chiavi di crittografia gestite da Microsoft. Tuttavia, è anche possibile gestire la sottoscrizione con le proprie chiavi di crittografia. Le chiavi gestite dal cliente (CMK), note anche come Bring your own key (BYOK), offrono una maggiore flessibilità per creare, ruotare, disabilitare e revocare i controlli di accesso. È anche possibile controllare le chiavi di crittografia usate per proteggere i dati.


> [!IMPORTANT]
> Le chiavi gestite dal cliente sono solo risorse disponibili create dopo il 27 giugno 2020. Per usare CMK con servizi vocali, è necessario creare una nuova risorsa di riconoscimento vocale. Dopo aver creato la risorsa, è possibile usare Azure Key Vault per configurare l'identità gestita.

Per richiedere la possibilità di usare chiavi gestite dal cliente, compilare e inviare il modulo di richiesta della chiave gestita dal cliente. Saranno richiesti circa 3-5 giorni lavorativi per ricevere informazioni sullo stato della richiesta. A seconda della richiesta, è possibile che venga inserita in una coda e approvata quando lo spazio diventa disponibile. Una volta approvate per l'uso di CMK con servizi vocali, è necessario creare una nuova risorsa vocale dalla portale di Azure.
   > [!NOTE]
   > **Le chiavi gestite dal cliente (CMK) sono supportate solo per i comandi personalizzati.**
   >
   >  **Riconoscimento vocale personalizzato e la voce personalizzata supportano ancora solo BYOS (Bring your own Storage).**  [Altre informazioni](speech-encryption-of-data-at-rest.md)
   >
   > Se si usa la risorsa vocale specificata per accedere a questi servizi, è necessario soddisfare le esigenze di conformità configurando in modo esplicito BYOS.


## <a name="customer-managed-keys-with-azure-key-vault"></a>Chiavi gestite dal cliente con Azure Key Vault

È necessario utilizzare Azure Key Vault per archiviare le chiavi gestite dal cliente. È possibile creare chiavi personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure usare le API Azure Key Vault per generare chiavi. La risorsa vocale e l'insieme di credenziali delle chiavi devono trovarsi nella stessa area e nello stesso tenant Azure Active Directory (Azure AD), ma possono trovarsi in sottoscrizioni diverse. Per ulteriori informazioni su Azure Key Vault, vedere [che cos'è Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

Quando viene creata una nuova risorsa di riconoscimento vocale e usata per eseguire il provisioning di comandi personalizzati, i dati vengono sempre crittografati usando le chiavi gestite da Microsoft. Non è possibile abilitare le chiavi gestite dal cliente nel momento in cui viene creata la risorsa. Le chiavi gestite dal cliente vengono archiviate in Azure Key Vault e l'insieme di credenziali delle chiavi deve essere sottoposto a provisioning con criteri di accesso che concedono le autorizzazioni chiave all'identità gestita associata alla risorsa Servizi cognitivi. L'identità gestita è disponibile solo dopo la creazione della risorsa usando il piano tariffario richiesto per CMK.

L'abilitazione delle chiavi gestite dal cliente consentirà anche un' [identità gestita](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)assegnata dal sistema, una funzionalità di Azure ad. Una volta abilitata l'identità gestita assegnata dal sistema, questa risorsa verrà registrata con Azure Active Directory. Dopo la registrazione, all'identità gestita verrà concesso l'accesso al Key Vault selezionato durante l'installazione della chiave gestita dal cliente. 

> [!IMPORTANT]
> Se si disabilitano le identità gestite assegnate dal sistema, l'accesso all'insieme di credenziali delle chiavi verrà rimosso e i dati crittografati con le chiavi del cliente non saranno più accessibili. Tutte le funzionalità che dipendono da questi dati smetteranno di funzionare.

> [!IMPORTANT]
> Le identità gestite attualmente non supportano gli scenari tra directory. Quando si configurano le chiavi gestite dal cliente nel portale di Azure, un'identità gestita viene assegnata automaticamente dietro le quinte. Se successivamente si sposta la sottoscrizione, il gruppo di risorse o la risorsa da una directory Azure AD a un'altra, l'identità gestita associata alla risorsa non viene trasferita al nuovo tenant, quindi le chiavi gestite dal cliente potrebbero non funzionare più. Per altre informazioni, vedere **trasferimento di una sottoscrizione tra Azure ad directory** nelle [domande frequenti e problemi noti relativi alle identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories).  

## <a name="configure-azure-key-vault"></a>Configurare Azure Key Vault

Con le chiavi gestite dal cliente è necessario impostare due proprietà nell'insieme di credenziali delle chiavi, **eliminare temporaneamente** e non **ripulire**. Queste proprietà non sono abilitate per impostazione predefinita, ma possono essere abilitate tramite PowerShell o l'interfaccia della riga di comando di Azure in un insieme di credenziali delle chiavi nuovo o esistente.

> [!IMPORTANT]
> Se non si dispone dell' **eliminazione** temporanea e non si **ripuliscono** le proprietà abilitate e si elimina la chiave, non sarà possibile recuperare i dati nella risorsa del servizio cognitive.

Per informazioni su come abilitare queste proprietà in un insieme di credenziali delle chiavi esistente, vedere le sezioni **Abilitazione dell'eliminazione temporanea** e **Abilitazione della protezione dall'eliminazione** in uno degli articoli seguenti:

- [Come usare l'eliminazione temporanea con PowerShell](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell).
- [Come usare l'eliminazione temporanea con l'interfaccia](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli)della riga di comando.

Con la crittografia di archiviazione di Azure sono supportate solo le chiavi RSA della dimensione 2048. Per ulteriori informazioni sulle chiavi, vedere **Key Vault chiavi** in [informazioni su Azure Key Vault chiavi, segreti e certificati](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys).

## <a name="enable-customer-managed-keys-for-your-speech-resource"></a>Abilitare le chiavi gestite dal cliente per la risorsa vocale

Per abilitare le chiavi gestite dal cliente nel portale di Azure, attenersi alla procedura seguente:

1. Passare alla risorsa di riconoscimento vocale.
1. Nel pannello **Impostazioni** per la risorsa vocale fare clic su **crittografia**. Selezionare l'opzione **chiavi gestite dal cliente** , come illustrato nella figura seguente.

 ![Screenshot che illustra come selezionare le chiavi gestite dal cliente](media/custom-commands/select-cmk.png)

## <a name="specify-a-key"></a>Specificare una chiave

Dopo aver abilitato le chiavi gestite dal cliente, sarà possibile specificare una chiave da associare alla risorsa Servizi cognitivi.

### <a name="specify-a-key-as-a-uri"></a>Specificare una chiave da un URI

Per specificare una chiave come URI, attenersi alla procedura seguente:

1. Per individuare l'URI della chiave nella portale di Azure, passare all'insieme di credenziali delle chiavi e selezionare l'impostazione **chiavi** . Selezionare la chiave desiderata, quindi fare clic sulla chiave per visualizzarne le versioni. Selezionare una versione della chiave per visualizzare le impostazioni per tale versione.
1. Copiare il valore del campo **identificatore chiave** , che fornisce l'URI.

    ![Screenshot che mostra l'URI della chiave di Key Vault](../media/cognitive-services-encryption/key-uri-portal.png)

1. Nelle impostazioni di **crittografia** per il servizio riconoscimento vocale, scegliere l'opzione **immettere l'URI del tasto** .
1. Incollare l'URI copiato nel campo **URI chiave** .

1. Specificare la sottoscrizione che contiene l'insieme di credenziali delle chiavi.
1. Salvare le modifiche.

### <a name="specify-a-key-from-a-key-vault"></a>Specificare una chiave da un insieme di credenziali delle chiavi

Per specificare una chiave da un insieme di credenziali delle chiavi, assicurarsi innanzitutto di avere un insieme di credenziali delle chiavi contenente una chiave. Per specificare una chiave da un insieme di credenziali delle chiavi, seguire questa procedura:

1. Scegliere l'opzione **Selezionare la chiave dall'insieme di credenziali delle chiavi**.
1. Selezionare l'insieme di credenziali delle chiavi contenente la chiave che si vuole usare.
1. Selezionare la chiave dall'insieme di credenziali delle chiavi.

   ![Screenshot che mostra l'opzione della chiave gestita dal cliente](media/custom-commands/configure-cmk-fromKV.png)

1. Salvare le modifiche.

## <a name="update-the-key-version"></a>Aggiornare la versione della chiave

Quando si crea una nuova versione di una chiave, aggiornare la risorsa vocale per usare la nuova versione. A tale scopo, seguire questa procedura:

1. Passare alla risorsa vocale e visualizzare le impostazioni di **crittografia** .
1. Immettere l'URI per la nuova versione della chiave. In alternativa, è possibile selezionare di nuovo l'insieme di credenziali delle chiavi e la chiave per aggiornare la versione.
1. Salvare le modifiche.

## <a name="use-a-different-key"></a>Usare una chiave diversa

Per modificare la chiave usata per la crittografia, attenersi alla procedura seguente:

1. Passare alla risorsa vocale e visualizzare le impostazioni di **crittografia** .
1. Immettere l'URI della nuova chiave. In alternativa, è possibile selezionare l'insieme di credenziali delle chiavi e scegliere una nuova chiave.
1. Salvare le modifiche.

## <a name="rotate-customer-managed-keys"></a>Ruotare le chiavi gestite dal cliente

È possibile ruotare una chiave gestita dal cliente in Azure Key Vault in base ai criteri di conformità. Quando la chiave viene ruotata, è necessario aggiornare la risorsa vocale per usare il nuovo URI della chiave. Per informazioni su come aggiornare la risorsa per usare una nuova versione della chiave nella portale di Azure, vedere [aggiornare la versione della chiave](#update-the-key-version).

La rotazione della chiave non attiva la nuova crittografia dei dati nella risorsa. Non sono necessarie altre azioni da parte dell'utente.

## <a name="revoke-access-to-customer-managed-keys"></a>Revocare l'accesso alle chiavi gestite dal cliente

Per revocare l'accesso alle chiavi gestite dal cliente, usare PowerShell o l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere [PowerShell per Azure Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault//) o [Interfaccia della riga di comando per Azure Key Vault](https://docs.microsoft.com/cli/azure/keyvault). La revoca dell'accesso blocca in modo efficace l'accesso a tutti i dati nella risorsa Servizi cognitivi, in quanto la chiave di crittografia non è accessibile dai servizi cognitivi.

## <a name="disable-customer-managed-keys"></a>Disabilitare le chiavi gestite dal cliente

Quando si disabilitano le chiavi gestite dal cliente, la risorsa vocale viene quindi crittografata con le chiavi gestite da Microsoft. Per disabilitare le chiavi gestite dal cliente, attenersi alla seguente procedura:

1. Passare alla risorsa vocale e visualizzare le impostazioni di **crittografia** .
1. Deselezionare la casella di controllo accanto all'impostazione **Usa una chiave personalizzata** .

## <a name="next-steps"></a>Passaggi successivi

* [Modulo di richiesta della chiave gestita dal cliente](https://aka.ms/cogsvc-cmk)
* [Altre informazioni su Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
* [Che cosa sono le identità gestite](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)



