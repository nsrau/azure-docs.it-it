---
title: Panoramica degli schemi ed Enterprise Integration Pack | Documentazione Microsoft
description: Informazioni su come usare gli schemi con Enterprise Integration Pack e le app per la logica
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: 56c5846c-5d8c-4ad4-9652-60b07aa8fc3b
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: 2ad6ad4dcab9a7296dfc6fb6227c510daee85222


---
# <a name="learn-about-schemas-and-the-enterprise-integration-pack"></a>Informazioni sugli schemi ed Enterprise Integration Pack
## <a name="why-use-a-schema"></a>Perché usare uno schema
È possibile usare gli schemi per confermare che i documenti XML ricevuti siano validi, con i dati previsti in un formato predefinito. Gli schemi vengono usati per convalidare i messaggi scambiati in uno scenario B2B.

## <a name="add-a-schema"></a>Aggiungere uno schema
Nel portale di Azure:  

1. Selezionare **Altri servizi**.  
   ![Screenshot del portale di Azure con "Altri servizi" evidenziato](media/logic-apps-enterprise-integration-schemas/overview-11.png)    
2. Nella casella di ricerca del filtro immettere **integrazione**, quindi selezionare **Account di integrazione** dall'elenco dei risultati.     
   ![Screenshot della casella di ricerca del filtro](media/logic-apps-enterprise-integration-schemas/overview-21.png)  
3. Selezionare l'**account di integrazione** a cui aggiungere lo schema.    
   ![Screenshot dell'elenco di account di integrazione](media/logic-apps-enterprise-integration-schemas/overview-31.png)  
4. Selezionare il riquadro **Schemi**.  
   ![Screenshot dell'account di integrazione di EIP con riquadro "Schemi" evidenziato](media/logic-apps-enterprise-integration-schemas/schema-11.png)  

### <a name="add-a-schema-file-less-than-2-mb"></a>Aggiungere un file di schema di dimensioni minori di 2 MB
1. Nel pannello **Schemi** visualizzato nei passaggi precedenti selezionare **Aggiungi**.  
   ![Screenshot del pannello Schemi con il pulsante "Aggiungi" evidenziato](media/logic-apps-enterprise-integration-schemas/schema-21.png)  
2. Immettere un nome per lo schema. Per caricare il file di schema selezionare l'icona della cartella accanto alla casella di testo **Schema**. Al termine del processo di caricamento, selezionare **OK**.    
   ![Screenshot di "Aggiungi schema", con "File piccolo"](media/logic-apps-enterprise-integration-schemas/schema-31.png)  

### <a name="add-a-schema-file-larger-than-2-mb-up-to-a-maximum-of-8-mb"></a>Aggiungere un file di schema di dimensioni maggiori di 2 MB (fino a un massimo di 8 MB)
La procedura dipende dal livello di accesso del contenitore BLOB, ovvero **Pubblico** o **Nessun accesso anonimo**. Per determinare il livello di accesso, in **Azure Storage Explorer** selezionare il contenitore BLOB da usare in **Contenitori BLOB**. Selezionare **Sicurezza**, quindi selezionare la scheda **Livello di accesso**.

1. Se il livello di accesso di sicurezza del BLOB è **Pubblico**, seguire questa procedura.  
   ![Screenshot di Azure Storage Explorer, con evidenziate le opzioni "Contenitori BLOB", "Sicurezza" e "Pubblico"](media/logic-apps-enterprise-integration-schemas/blob-public.png)  
   
    a. Caricare lo schema nella risorsa di archiviazione e copiare l'URI.  
    ![Screenshot dell'account di archiviazione con URI evidenziato](media/logic-apps-enterprise-integration-schemas/schema-blob.png)  
   
    b. In **Aggiungi schema** selezionare **File grande**, quindi specificare l'URI nella casella di testo **URI del contenuto**.  
    ![Screenshot degli schemi con evidenziati il pulsante "Aggiungi" e l'opzione "File grande"](media/logic-apps-enterprise-integration-schemas/schema-largefile.png)  
2. Se il livello di accesso di sicurezza BLOB è **Nessun accesso anonimo**, seguire questa procedura.  
   ![Screenshot di Azure Storage Explorer, con evidenziate le opzioni "Contenitori BLOB", "Sicurezza" e "Nessun accesso anonimo"](media/logic-apps-enterprise-integration-schemas/blob-1.png)  
   
    a. Caricare lo schema nella risorsa di archiviazione.  
    ![Screenshot dell'account di archiviazione](media/logic-apps-enterprise-integration-schemas/blob-3.png)
   
    b. Generare una firma di accesso condiviso per lo schema.  
    ![Screenshot dell'account di archiviazione con evidenziata la scheda delle firme di accesso condiviso](media/logic-apps-enterprise-integration-schemas/blob-2.png)
   
    c. In **Aggiungi schema**, selezionare **File grande**, quindi specificare l'URI della firma di accesso condiviso nella casella di testo **URI del contenuto**.  
    ![Screenshot degli schemi con evidenziati il pulsante "Aggiungi" e l'opzione "File grande"](media/logic-apps-enterprise-integration-schemas/schema-largefile.png)  
3. Nel pannello **Schemi** dell'account di integrazione EIP dovrebbe essere ora visualizzato lo schema appena aggiunto.  
   ![Screenshot dell'account di integrazione EIP con evidenziati il pannello "Schemi" e il nuovo schema](media/logic-apps-enterprise-integration-schemas/schema-41.png)

## <a name="edit-schemas"></a>Modificare gli schemi
1. Selezionare il riquadro **Schemi**.  
2. Selezionare lo schema da modificare dal pannello **Schemi** visualizzato.
3. Nel pannello **Schemi** selezionare **Modifica**.  
   ![Screenshot del pannello Schemi](media/logic-apps-enterprise-integration-schemas/edit-12.png)    
4. Selezionare il file di schema da modificare usando la finestra di dialogo di selezione file visualizzata.
5. Selezionare **Apri** nel selettore di file.  
   ![Screenshot del selettore di file](media/logic-apps-enterprise-integration-schemas/edit-31.png)  
6. Si riceve una notifica che indica l'esito positivo del caricamento.  

## <a name="delete-schemas"></a>Eliminare gli schemi
1. Selezionare il riquadro **Schemi**.  
2. Selezionare lo schema da eliminare dal pannello **Schemi** visualizzato.  
3. Nel pannello **Schemi** selezionare **Elimina**.
   ![Screenshot del pannello Schemi](media/logic-apps-enterprise-integration-schemas/delete-12.png)  
4. Per confermare la scelta, selezionare **Sì**.  
   ![Screenshot del messaggio di conferma "Elimina schema"](media/logic-apps-enterprise-integration-schemas/delete-21.png)  
5. Si noti infine che l'elenco di schemi nel pannello **Schemi** viene aggiornato e lo schema eliminato non viene più visualizzato.  
   ![Screenshot dell'account di integrazione di EIP con riquadro "Schemi" evidenziato](media/logic-apps-enterprise-integration-schemas/delete-31.png)    

## <a name="next-steps"></a>Passaggi successivi
* [Altre informazioni su Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Informazioni su Enterprise Integration Pack").  




<!--HONumber=Jan17_HO3-->


