
---
title: Uso dei certificati con Enterprise Integration Pack | Documentazione Microsoft
description: Informazioni su come usare i certificati con Enterprise Integration Pack e le App per la logica
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: 4cbffd85-fe8d-4dde-aa5b-24108a7caa7d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: a1baafa9a2b4552ad711615e5e0abb0180a0d562
ms.openlocfilehash: e6c8ed2fe6ff574fb40991d7060865ea08887df9


---
# <a name="learn-about-certificates-and-enterprise-integration-pack"></a>Informazioni sui certificati ed Enterprise Integration Pack
## <a name="overview"></a>Overview
Enterprise Integration impiega i certificati per proteggere le comunicazioni B2B. È possibile usare due tipi di certificati nelle app di Enterprise Integration:

* Certificati pubblici, che devono essere acquistati da un'autorità di certificazione (CA).
* Certificati privati, che si possono rilasciare personalmente. Sono talvolta definiti certificati autofirmati.

## <a name="what-are-certificates"></a>Che cosa sono i certificati?
I certificati sono documenti digitali che verificano l'identità dei partecipanti nelle comunicazioni elettroniche e proteggono le comunicazioni elettroniche stesse.

## <a name="why-use-certificates"></a>Perché usare i certificati?
In alcuni casi è necessario garantire la riservatezza delle comunicazioni B2B. Enterprise Integration usa i certificati per proteggere queste comunicazioni in due modi:

* Crittografando il contenuto dei messaggi
* Apponendo una firma digitale ai messaggi  

## <a name="how-do-you-upload-certificates"></a>Come caricare i certificati?
### <a name="public-certificates"></a>Certificati pubblici
Per usare un *certificato pubblico* nelle app per la logica con funzionalità B2B, è necessario prima caricare il certificato nell'account di integrazione. Per usare un *certificato autofirmato*, bisogna prima caricarlo nell' [insieme di credenziali delle chiavi di Azure](../key-vault/key-vault-get-started.md "Informazioni sull'insieme di credenziali delle chiavi").

Dopo che è stato caricato, un certificato diventa disponibile per proteggere i messaggi B2B quando se ne definiscono le proprietà nei [contratti](logic-apps-enterprise-integration-agreements.md) creati.  

Ecco i passaggi dettagliati per caricare i certificati pubblici nell'account di integrazione dopo l'accesso al portale di Azure:

1. Selezionare **Altri servizi** e immettere **integrazione** nella casella di ricerca Filtro. Selezionare **Account di integrazione** nell'elenco dei risultati.     
![Selezionare Esplora](media/logic-apps-enterprise-integration-certificates/overview-1.png)  
2. Selezionare l'account di integrazione a cui aggiungere il certificato.  
![Selezionare l'account di integrazione a cui aggiungere il certificato](media/logic-apps-enterprise-integration-certificates/overview-3.png)  
3. Selezionare il riquadro **Certificati** .  
![Selezionare il riquadro Certificati](media/logic-apps-enterprise-integration-certificates/certificate-1.png)
4. Selezionare il pulsante **Aggiungi** nel pannello **Certificati** che si apre.   
![Selezionare il pulsante Aggiungi](media/logic-apps-enterprise-integration-certificates/certificate-2.png)
5. Immettere un **nome** per il certificato e quindi selezionare il tipo di certificato **pubblico** nell'elenco a discesa.  
6. Selezionare l'icona della cartella sul lato destro della casella di testo **Certificato**. Quando viene visualizzato il selettore file, individuare e selezionare il file del certificato da caricare nell'account di integrazione.
7. Selezionare il certificato e poi **OK** nel selettore file. Questa operazione convalida e carica il certificato nell'account di integrazione.
8. Infine, di nuovo nel pannello **Aggiungi certificato**, selezionare il pulsante **OK**.  
![Selezionare il pulsante OK](media/logic-apps-enterprise-integration-certificates/certificate-3.png)  
9. Selezionare il riquadro **Certificati** . Viene visualizzato il certificato appena aggiunto.  
![Vedere il nuovo certificato](media/logic-apps-enterprise-integration-certificates/certificate-4.png)  

### <a name="private-certificates"></a>Certificati privati
Nell'account di integrazione è possibile caricare anche certificati privati seguendo questa procedura:  

1. [Caricare la chiave privata in Key Vault](../key-vault/key-vault-get-started.md "Informazioni su Key Vault") e specificare un **nome chiave**. 
   
   > [!TIP]
   > È necessario autorizzare App per la logica a eseguire operazioni su Key Vault. È possibile concedere l'accesso all'entità servizio App per la logica usando questo comando di PowerShell: `Set-AzureRmKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`  
   > 
   > 

Dopo avere eseguito il passaggio precedente, aggiungere un certificato privato all'account di integrazione.

Ecco i passaggi dettagliati per caricare i certificati privati nell'account di integrazione dopo l'accesso al portale di Azure:  
 
1. Selezionare l'account di integrazione a cui aggiungere il certificato e selezionare il riquadro **Certificati**.  
![Selezionare il riquadro Certificati](media/logic-apps-enterprise-integration-certificates/certificate-1.png)  
2. Selezionare il pulsante **Aggiungi** nel pannello **Certificati** che si apre.   
![Selezionare il pulsante Aggiungi](media/logic-apps-enterprise-integration-certificates/certificate-2.png)
3. Immettere un **nome** per il certificato e selezionare il tipo di certificato **privato** nell'elenco a discesa.   
4. Selezionare l'icona della cartella sul lato destro della casella di testo **Certificato**. Quando viene visualizzato il selettore file,trovare il certificato pubblico corrispondente da caricare nell'account di integrazione.   
   
   > [!Note]
   > Quando si aggiunge un certificato privato, è importante aggiungere il certificato pubblico corrispondente da visualizzare nelle impostazioni di ricezione e invio dell'[accordo AS2](logic-apps-enterprise-integration-as2.md) per la firma e la crittografia dei messaggi.
   > 
   >   

5. Selezionare il **gruppo di risorse**, l'**insieme di credenziali delle chiavi** e il **nome chiave** e fare clic su **OK**.  
![Add certificate](media/logic-apps-enterprise-integration-certificates/privatecertificate-1.png)  
6. Selezionare il riquadro **Certificati** . Viene visualizzato il certificato appena aggiunto.
![Vedere il nuovo certificato](media/logic-apps-enterprise-integration-certificates/privatecertificate-2.png)  



* [Creare un contratto B2B](logic-apps-enterprise-integration-agreements.md)  
* [Altre informazioni sull'insieme di credenziali delle chiavi](../key-vault/key-vault-get-started.md "Informazioni sull'insieme di credenziali delle chiavi")  




<!--HONumber=Jan17_HO4-->


