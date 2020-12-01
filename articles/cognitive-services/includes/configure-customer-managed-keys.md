---
title: Usare il portale di Azure per configurare le chiavi gestite dal cliente
titleSuffix: Cognitive Services
description: Informazioni su come usare la portale di Azure per configurare le chiavi gestite dal cliente con Azure Key Vault. Le chiavi gestite dal cliente consentono di creare, ruotare, disabilitare e revocare i controlli di accesso.
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 05/28/2020
ms.author: egeaney
ms.openlocfilehash: c79846b0a5b675c34e4e7919e9ecd9d591bfefe5
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96356359"
---
## <a name="customer-managed-keys-with-azure-key-vault"></a>Chiavi gestite dal cliente con Azure Key Vault

È necessario utilizzare Azure Key Vault per archiviare le chiavi gestite dal cliente. È possibile creare chiavi personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure usare le API Azure Key Vault per generare chiavi. La risorsa Servizi cognitivi e l'insieme di credenziali delle chiavi devono trovarsi nella stessa area e nello stesso tenant di Azure Active Directory (Azure AD), ma possono trovarsi in sottoscrizioni diverse. Per ulteriori informazioni su Azure Key Vault, vedere [che cos'è Azure Key Vault?](../../key-vault/general/overview.md).

Quando viene creata una nuova risorsa di servizi cognitivi, questo viene sempre crittografato con le chiavi gestite da Microsoft. Non è possibile abilitare le chiavi gestite dal cliente nel momento in cui viene creata la risorsa. Le chiavi gestite dal cliente vengono archiviate in Azure Key Vault e l'insieme di credenziali delle chiavi deve essere sottoposto a provisioning con criteri di accesso che concedono le autorizzazioni chiave all'identità gestita associata alla risorsa Servizi cognitivi. L'identità gestita è disponibile solo dopo la creazione della risorsa usando il piano tariffario richiesto per CMK.

L'abilitazione delle chiavi gestite dal cliente consentirà anche un' [identità gestita](../../active-directory/managed-identities-azure-resources/overview.md)assegnata dal sistema, una funzionalità di Azure ad. Una volta abilitata l'identità gestita assegnata dal sistema, questa risorsa verrà registrata con Azure Active Directory. Dopo la registrazione, all'identità gestita verrà concesso l'accesso al Key Vault selezionato durante l'installazione della chiave gestita dal cliente. 

> [!IMPORTANT]
> Se si disabilitano le identità gestite assegnate dal sistema, l'accesso all'insieme di credenziali delle chiavi verrà rimosso e i dati crittografati con le chiavi del cliente non saranno più accessibili. Tutte le funzionalità che dipendono da questi dati smetteranno di funzionare.

> [!IMPORTANT]
> Le identità gestite attualmente non supportano gli scenari tra directory. Quando si configurano le chiavi gestite dal cliente nel portale di Azure, un'identità gestita viene assegnata automaticamente dietro le quinte. Se successivamente si sposta la sottoscrizione, il gruppo di risorse o la risorsa da una directory Azure AD a un'altra, l'identità gestita associata alla risorsa non viene trasferita al nuovo tenant, quindi le chiavi gestite dal cliente potrebbero non funzionare più. Per altre informazioni, vedere **trasferimento di una sottoscrizione tra Azure ad directory** nelle [domande frequenti e problemi noti relativi alle identità gestite per le risorse di Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

## <a name="configure-azure-key-vault"></a>Configurare Azure Key Vault

Con le chiavi gestite dal cliente è necessario impostare due proprietà nell'insieme di credenziali delle chiavi, **eliminare temporaneamente** e non **ripulire**. Queste proprietà non sono abilitate per impostazione predefinita, ma possono essere abilitate tramite PowerShell o l'interfaccia della riga di comando di Azure in un insieme di credenziali delle chiavi nuovo o esistente.

> [!IMPORTANT]
> Se non si dispone dell' **eliminazione** temporanea e non si **ripuliscono** le proprietà abilitate e si elimina la chiave, non sarà possibile recuperare i dati nella risorsa del servizio cognitive.

Per informazioni su come abilitare queste proprietà in un insieme di credenziali delle chiavi esistente, vedere le sezioni **Abilitazione dell'eliminazione temporanea** e **Abilitazione della protezione dall'eliminazione** in uno degli articoli seguenti:

- [Come usare l'eliminazione temporanea con PowerShell](../../key-vault/general/key-vault-recovery.md).
- [Come usare l'eliminazione temporanea con l'interfaccia](../../key-vault/general/key-vault-recovery.md)della riga di comando.

Con la crittografia di archiviazione di Azure sono supportate solo le chiavi RSA della dimensione 2048. Per ulteriori informazioni sulle chiavi, vedere **Key Vault chiavi** in [informazioni su Azure Key Vault chiavi, segreti e certificati](../../key-vault/general/about-keys-secrets-certificates.md).

## <a name="enable-customer-managed-keys-for-your-resource"></a>Abilitare le chiavi gestite dal cliente per la risorsa

Per abilitare le chiavi gestite dal cliente nel portale di Azure, attenersi alla procedura seguente:

1. Passare alla risorsa Servizi cognitivi.
1. Nel pannello **Impostazioni** della risorsa Servizi cognitivi fare clic su **crittografia**. Selezionare l'opzione **chiavi gestite dal cliente** , come illustrato nella figura seguente.

    ![Screenshot che illustra come selezionare le chiavi gestite dal cliente](../media/cognitive-services-encryption/selectcmk.png)

## <a name="specify-a-key"></a>Specificare una chiave

Dopo aver abilitato le chiavi gestite dal cliente, sarà possibile specificare una chiave da associare alla risorsa Servizi cognitivi.

### <a name="specify-a-key-as-a-uri"></a>Specificare una chiave da un URI

Per specificare una chiave come URI, attenersi alla procedura seguente:

1. Per individuare l'URI della chiave nella portale di Azure, passare all'insieme di credenziali delle chiavi e selezionare l'impostazione **chiavi** . Selezionare la chiave desiderata, quindi fare clic sulla chiave per visualizzarne le versioni. Selezionare una versione della chiave per visualizzare le impostazioni per tale versione.
1. Copiare il valore del campo **identificatore chiave** , che fornisce l'URI.

    ![Screenshot che mostra l'URI della chiave di Key Vault](../media/cognitive-services-encryption/key-uri-portal.png)

1. Nelle impostazioni di **crittografia** per l'account di archiviazione scegliere l'opzione **immettere l'URI del tasto** .
1. Incollare l'URI copiato nel campo **URI chiave** .

   ![Screenshot che illustra come immettere l'URI della chiave](../media/cognitive-services-encryption/ssecmk2.png)

1. Specificare la sottoscrizione che contiene l'insieme di credenziali delle chiavi.
1. Salvare le modifiche.

### <a name="specify-a-key-from-a-key-vault"></a>Specificare una chiave da un insieme di credenziali delle chiavi

Per specificare una chiave da un insieme di credenziali delle chiavi, assicurarsi innanzitutto di avere un insieme di credenziali delle chiavi contenente una chiave. Per specificare una chiave da un insieme di credenziali delle chiavi, seguire questa procedura:

1. Scegliere l'opzione **Selezionare la chiave dall'insieme di credenziali delle chiavi**.
1. Selezionare l'insieme di credenziali delle chiavi contenente la chiave che si vuole usare.
1. Selezionare la chiave dall'insieme di credenziali delle chiavi.

   ![Screenshot che mostra l'opzione della chiave gestita dal cliente](../media/cognitive-services-encryption/ssecmk3.png)

1. Salvare le modifiche.

## <a name="update-the-key-version"></a>Aggiornare la versione della chiave

Quando si crea una nuova versione di una chiave, aggiornare la risorsa Servizi cognitivi per usare la nuova versione. Seguire questa procedura:

1. Passare alla risorsa Servizi cognitivi e visualizzare le impostazioni di **crittografia** .
1. Immettere l'URI per la nuova versione della chiave. In alternativa, è possibile selezionare di nuovo l'insieme di credenziali delle chiavi e la chiave per aggiornare la versione.
1. Salvare le modifiche.

## <a name="use-a-different-key"></a>Usare una chiave diversa

Per modificare la chiave usata per la crittografia, attenersi alla procedura seguente:

1. Passare alla risorsa Servizi cognitivi e visualizzare le impostazioni di **crittografia** .
1. Immettere l'URI della nuova chiave. In alternativa, è possibile selezionare l'insieme di credenziali delle chiavi e scegliere una nuova chiave.
1. Salvare le modifiche.

## <a name="rotate-customer-managed-keys"></a>Ruotare le chiavi gestite dal cliente

È possibile ruotare una chiave gestita dal cliente in Azure Key Vault in base ai criteri di conformità. Quando la chiave viene ruotata, è necessario aggiornare la risorsa Servizi cognitivi per usare il nuovo URI della chiave. Per informazioni su come aggiornare la risorsa per usare una nuova versione della chiave nella portale di Azure, vedere [aggiornare la versione della chiave](#update-the-key-version).

La rotazione della chiave non attiva la nuova crittografia dei dati nella risorsa. Non sono necessarie altre azioni da parte dell'utente.

## <a name="revoke-access-to-customer-managed-keys"></a>Revocare l'accesso alle chiavi gestite dal cliente

Per revocare l'accesso alle chiavi gestite dal cliente, usare PowerShell o l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere [PowerShell per Azure Key Vault](/powershell/module/az.keyvault//) o [Interfaccia della riga di comando per Azure Key Vault](/cli/azure/keyvault). La revoca dell'accesso blocca in modo efficace l'accesso a tutti i dati nella risorsa Servizi cognitivi, in quanto la chiave di crittografia non è accessibile dai servizi cognitivi.

## <a name="disable-customer-managed-keys"></a>Disabilitare le chiavi gestite dal cliente

Quando si disabilitano le chiavi gestite dal cliente, la risorsa Servizi cognitivi viene quindi crittografata con le chiavi gestite da Microsoft. Per disabilitare le chiavi gestite dal cliente, attenersi alla seguente procedura:

1. Passare alla risorsa Servizi cognitivi e visualizzare le impostazioni di **crittografia** .
1. Deselezionare la casella di controllo accanto all'impostazione **Usa una chiave personalizzata** .