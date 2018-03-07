---
title: Proteggere i messaggi B2B con certificati in App per la logica di Azure | Microsoft Docs
description: Aggiungere i certificati per proteggere i messaggi B2B con Enterprise Integration Pack
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 4cbffd85-fe8d-4dde-aa5b-24108a7caa7d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 708bdcddede71186c48ae7d4034cc9df0bd61391
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="secure-b2b-messages-with-certificates"></a>Proteggere i messaggi B2B con certificati

In alcuni casi, è necessario mantenere riservata la comunicazione B2B. Per proteggere la comunicazione B2B per le app di integrazione aziendali, in particolare le app per la logica, è possibile aggiungere i certificati all'account di integrazione. I certificati sono documenti digitali che verificano l'identità dei partecipanti nelle comunicazioni elettroniche.
I certificati consentono di proteggere le comunicazioni nei modi seguenti:

* Crittografare il contenuto del messaggio
* Firmare digitalmente i messaggi  

È possibile usare questi certificati nelle app di integrazione aziendali:

* Certificati pubblici, che devono essere acquistati da un'autorità di certificazione (CA).
* Certificati privati, che si possono rilasciare personalmente. Sono talvolta definiti certificati autofirmati.

## <a name="upload-a-public-certificate"></a>Caricare un certificato pubblico

Per usare un *certificato pubblico* nelle app per la logica con funzionalità B2B, è necessario prima caricare il certificato nell'account di integrazione. Dopo aver definito le proprietà nei [contratti](logic-apps-enterprise-integration-agreements.md) creati, il certificato diventa disponibile per proteggere i messaggi B2B.

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nel menu principale di Azure selezionare **Tutti i servizi**. Nella casella di ricerca immettere "integrazione" e quindi selezionare **Account di integrazione**.

   ![Trovare l'account di integrazione](media/logic-apps-enterprise-integration-certificates/overview-1.png)  

3. In **Account di integrazione** selezionare l'account di integrazione nel quale aggiungere il certificato.

   ![Selezionare l'account di integrazione a cui aggiungere il certificato](media/logic-apps-enterprise-integration-certificates/overview-3.png)  

4. Scegliere il riquadro **Certificati**.  

   ![Scegliere "Certificati"](media/logic-apps-enterprise-integration-certificates/certificate-1.png)

5. In **Certificati** scegliere **Aggiungi**.

   ![Selezionare "Aggiungi"](media/logic-apps-enterprise-integration-certificates/certificate-2.png)

6. In **Aggiungi certificato** specificare i dettagli per il certificato.
   
   1. Immettere il **nome** del certificato. Per il tipo di certificato, selezionare **Pubblico**.

   2. Sul lato destro della casella **Certificato** selezionare l'icona della cartella. 
   Trovare e selezionare il file del certificato che si vuole caricare. 
   Al termine dell'operazione, scegliere **OK**.

      ![Caricare un certificato pubblico](media/logic-apps-enterprise-integration-certificates/certificate-3.png)

   Azure carica il certificato dopo aver verificato la selezione.

   ![Vedere il nuovo certificato](media/logic-apps-enterprise-integration-certificates/certificate-4.png) 

## <a name="upload-a-private-certificate"></a>Caricare un certificato privato

Per usare un *certificato privato* nelle app per la logica con funzionalità B2B, è necessario prima caricare il certificato nell'account di integrazione. È necessario avere anche una chiave privata da aggiungere ad [Azure Key Vault](../key-vault/key-vault-get-started.md). 

Dopo aver definito le proprietà nei [contratti](logic-apps-enterprise-integration-agreements.md) creati, il certificato diventa disponibile per proteggere i messaggi B2B.

> [!NOTE]
> Per i certificati privati, assicurarsi di aggiungere un certificato pubblico corrispondente da visualizzare nelle impostazioni di invio e ricezione del [contratto AS2](logic-apps-enterprise-integration-as2.md) per la firma e la crittografia dei messaggi.

1. [Aggiungere la chiave privata in Azure Key Vault](../key-vault/key-vault-get-started.md#add) e specificare un **nome chiave**.
   
2. Autorizzare App per la logica di Azure a eseguire operazioni su Azure Key Vault. Per concedere l'accesso all'entità servizio di App per la logica, usare il comando di PowerShell, [Set-AzureRmKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy), ad esempio:

   `Set-AzureRmKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName 
   '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`
 
3. Accedere al [portale di Azure](https://portal.azure.com).

4. Nel menu principale di Azure selezionare **Tutti i servizi**. Nella casella di ricerca immettere "integrazione" e quindi selezionare **Account di integrazione**.

   ![Trovare l'account di integrazione](media/logic-apps-enterprise-integration-certificates/overview-1.png) 

5. In **Account di integrazione** selezionare l'account di integrazione nel quale aggiungere il certificato.

6. Scegliere il riquadro **Certificati**.  

   ![Scegliere il riquadro Certificati](media/logic-apps-enterprise-integration-certificates/certificate-1.png)

7. In **Certificati** scegliere **Aggiungi**.   

   ![Scegliere il pulsante Aggiungi](media/logic-apps-enterprise-integration-certificates/certificate-2.png)

8. In **Aggiungi certificato** specificare i dettagli per il certificato.
   
   1. Immettere il **nome** del certificato. Per il tipo di certificato, selezionare **Privato**.

   2. Sul lato destro della casella **Certificato** selezionare l'icona della cartella. 
   Trovare e selezionare il file del certificato che si vuole caricare. 
   Selezionare anche **Gruppo di risorse**, **Insieme di credenziali delle chiavi** e **Nome chiave**. 
   Al termine dell'operazione, scegliere **OK**.

      ![Aggiungere il certificato](media/logic-apps-enterprise-integration-certificates/privatecertificate-1.png)

   Azure carica il certificato dopo aver verificato la selezione.

   ![Vedere il nuovo certificato](media/logic-apps-enterprise-integration-certificates/privatecertificate-2.png)

## <a name="next-steps"></a>Passaggi successivi

* [Creare un contratto B2B](logic-apps-enterprise-integration-agreements.md)
