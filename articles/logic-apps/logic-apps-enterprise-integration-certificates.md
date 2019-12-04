---
title: Proteggere i messaggi B2B con certificati
description: Aggiungere certificati per proteggere i messaggi B2B in App per la logica di Azure con Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/17/2018
ms.openlocfilehash: 6c5de6eba000c9052c7eb7b31d75804b9f454607
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790682"
---
# <a name="secure-b2b-messages-with-certificates"></a>Proteggere i messaggi B2B con certificati

Quando è necessario mantenere riservate le comunicazioni B2B, è possibile proteggere tali comunicazioni per le app di integrazione aziendali, in particolare le app per la logica, aggiungendo certificati all'account di integrazione. I certificati sono documenti digitali che verificano le identità dei partecipanti nelle comunicazioni elettroniche e consentono di proteggere le comunicazioni nei modi seguenti:

* Crittografare il contenuto dei messaggi.
* Firmare digitalmente i messaggi. 

È possibile usare questi certificati nelle app di integrazione aziendali:

* [Certificati pubblici](https://en.wikipedia.org/wiki/Public_key_certificate), che è necessario acquistare da un'[autorità di certificazione (CA)](https://en.wikipedia.org/wiki/Certificate_authority) Internet pubblica, ma che non richiedono alcuna chiave. 

* Certificati privati o [*certificati autofirmati*](https://en.wikipedia.org/wiki/Self-signed_certificate), che si creano e rilasciano personalmente, ma che richiedono anche chiavi private. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="upload-a-public-certificate"></a>Caricare un certificato pubblico

Per usare un *certificato pubblico* nelle app per la logica con funzionalità B2B, è necessario prima caricare il certificato nell'account di integrazione. Dopo aver definito le proprietà nei [contratti](logic-apps-enterprise-integration-agreements.md) creati, il certificato diventa disponibile per proteggere i messaggi B2B.

1. Accedere al [portale di Azure](https://portal.azure.com). Nel menu principale di Azure selezionare **Tutte le risorse**. Nella casella di ricerca immettere il nome dell'account di integrazione e quindi selezionare l'account di integrazione desiderato.

   ![Trovare e selezionare l'account di integrazione](media/logic-apps-enterprise-integration-certificates/select-integration-account.png)  

2. In **Componenti** scegliere il riquadro **Certificati**.

   ![Scegliere "Certificati"](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

3. In **Certificati** scegliere **Aggiungi**. In **Aggiungi certificato** specificare questi dettagli per il certificato. Al termine dell'operazione, scegliere **OK**.

   | Proprietà | Value | Description | 
   |----------|-------|-------------|
   | **Nome** | <*certificate-name*> | Nome del certificato, ovvero "publicCert" in questo esempio | 
   | **Tipo di certificato** | Pubblico | Tipo del certificato |
   | **Certificate** | <*certificate-file-name*> | Per trovare e selezionare il file di certificato da caricare, scegliere l'icona della cartella accanto alla casella **Certificato**. |
   ||||

   ![Scegliere "Aggiungi" e specificare i dettagli del certificato](media/logic-apps-enterprise-integration-certificates/public-certificate-details.png)

   Dopo la convalida della selezione, Azure carica il certificato.

   ![Azure visualizza il nuovo certificato](media/logic-apps-enterprise-integration-certificates/new-public-certificate.png) 

## <a name="upload-a-private-certificate"></a>Caricare un certificato privato

Per usare un *certificato privato* nelle app per la logica con funzionalità B2B, è necessario prima caricare il certificato nell'account di integrazione. È necessario avere anche una chiave privata da aggiungere ad [Azure Key Vault](../key-vault/key-vault-get-started.md). 

Dopo aver definito le proprietà nei [contratti](logic-apps-enterprise-integration-agreements.md) creati, il certificato diventa disponibile per proteggere i messaggi B2B.

> [!NOTE]
> Per i certificati privati, assicurarsi di aggiungere un certificato pubblico corrispondente da visualizzare nelle impostazioni di **invio e ricezione** del [contratto AS2](logic-apps-enterprise-integration-as2.md) per la firma e la crittografia dei messaggi.

1. [Aggiungere la chiave privata in Azure Key Vault](../key-vault/certificate-scenarios.md#import-a-certificate) e specificare un **nome chiave**.
   
2. Autorizzare App per la logica di Azure a eseguire operazioni su Azure Key Vault. Per concedere l'accesso all'entità servizio di App per la logica, usare il comando di PowerShell, [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy), ad esempio:

   `Set-AzKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName 
   '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`
 
3. Accedere al [portale di Azure](https://portal.azure.com). Nel menu principale di Azure selezionare **Tutte le risorse**. Nella casella di ricerca immettere il nome dell'account di integrazione e quindi selezionare l'account di integrazione desiderato.

   ![Trovare l'account di integrazione](media/logic-apps-enterprise-integration-certificates/select-integration-account.png) 

4. In **Componenti** scegliere il riquadro **Certificati**.  

   ![Scegliere il riquadro Certificati](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

5. In **Certificati** scegliere **Aggiungi**. In **Aggiungi certificato** specificare questi dettagli per il certificato. Al termine dell'operazione, scegliere **OK**.

   | Proprietà | Value | Description | 
   |----------|-------|-------------|
   | **Nome** | <*certificate-name*> | Nome del certificato, ovvero "privateCert" in questo esempio | 
   | **Tipo di certificato** | Private | Tipo del certificato |
   | **Certificate** | <*certificate-file-name*> | Per trovare e selezionare il file di certificato da caricare, scegliere l'icona della cartella accanto alla casella **Certificato**. Quando si usa un insieme di credenziali delle chiavi per la chiave privata, il file caricato sarà il certificato pubblico. | 
   | **Gruppo di risorse** | <*integration-account-resource-group*> | Gruppo di risorse dell'account di integrazione, ovvero "MyResourceGroup" in questo esempio | 
   | **Insieme di credenziali delle chiavi** | <*key-vault-name*> | Nome dell'insieme di credenziali delle chiavi di Azure |
   | **Nome chiave** | <*key-name*> | Nome della chiave |
   ||||

   ![Scegliere "Aggiungi" e specificare i dettagli del certificato](media/logic-apps-enterprise-integration-certificates/private-certificate-details.png)

   Dopo la convalida della selezione, Azure carica il certificato.

   ![Azure visualizza il nuovo certificato](media/logic-apps-enterprise-integration-certificates/new-private-certificate.png) 

## <a name="next-steps"></a>Passaggi successivi

* [Creare un contratto B2B](logic-apps-enterprise-integration-agreements.md)
