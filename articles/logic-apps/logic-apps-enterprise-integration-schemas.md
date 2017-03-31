---
title: Schemi per la convalida XML - App per la logica di Azure | Microsoft Docs
description: Convalida dei documenti XML con gli schemi per le app per la logica di Azure ed Enterprise Integration Pack
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
ms.author: estfan
translationtype: Human Translation
ms.sourcegitcommit: 7884032fd4b5a72ca7e2f2dc2830b0a7c2220e94
ms.openlocfilehash: 5d8ddd4449d3b160dbca7496c801e75dcf4a44e5


---
# <a name="validate-xml-with-schemas-for-azure-logic-apps-and-the-enterprise-integration-pack"></a>Convalida XML con gli schemi per le app per la logica di Azure ed Enterprise Integration Pack

È possibile usare gli schemi per confermare che i documenti XML ricevuti siano validi e avere i dati previsti in un formato predefinito. Gli schemi vengono usati inoltre per convalidare i messaggi scambiati in uno scenario B2B.

## <a name="add-a-schema"></a>Aggiungere uno schema

1. Nel Portale di Azure fare clic su **More services** (Altri servizi).

    ![Portale di Azure, "More services" (Altri servizi)](media/logic-apps-enterprise-integration-schemas/overview-11.png)

2. Nella casella di ricerca del filtro immettere **integrazione**, quindi selezionare **Account di integrazione** dall'elenco dei risultati.

    ![Casella di ricerca del filtro](media/logic-apps-enterprise-integration-schemas/overview-21.png)

3. Selezionare l'**account di integrazione** in cui si desidera aggiungere lo schema.

    ![Elenco degli account di integrazione](media/logic-apps-enterprise-integration-schemas/overview-31.png)

4. Selezionare il riquadro **Schemi**.

    ![Esempio di account di integrazione, "Schemi"](media/logic-apps-enterprise-integration-schemas/schema-11.png)

### <a name="add-a-schema-file-smaller-than-2-mb"></a>Aggiungere un file di schema di dimensioni inferiori a 2 MB

1. Nel pannello **Schemi** visualizzato nei passaggi precedenti, selezionare **Aggiungi**.

    ![Pannello Schemi, "Aggiungi"](media/logic-apps-enterprise-integration-schemas/schema-21.png)

2. Immettere un nome per lo schema. Caricare il file di schema selezionando l'icona della cartella accanto alla casella di testo **Schema**. Al termine del processo di caricamento, scegliere **OK**.

    ![Screenshot di "Aggiungi schema", con "File piccolo" evidenziato](media/logic-apps-enterprise-integration-schemas/schema-31.png)

### <a name="add-a-schema-file-larger-than-2-mb-up-to-8-mb-maximum"></a>Aggiungere un file di schema di dimensioni maggiori di 2 MB (fino a un massimo di 8 MB)

La procedura dipende dal livello di accesso del contenitore BLOB, ovvero **Pubblico** o **Nessun accesso anonimo**.

**Per determinare il livello di accesso**

1.  Aprire **Azure Storage Explorer** (Esplora archivi di Azure). 

2.  In **Contenitori Blob**, selezionare il contenitore di blob desiderato. 

3.  Selezionare **Sicurezza**, **Livello di accesso**.

Se il livello di accesso di sicurezza del BLOB è **Pubblico**, seguire questa procedura.

![Azure Storage Explorer con evidenziate le opzioni "Contenitori BLOB", "Sicurezza" e "Pubblico"](media/logic-apps-enterprise-integration-schemas/blob-public.png)

1. Caricare lo schema nell'account di archiviazione e copiare l'URI.

    ![Account di archiviazione con URI evidenziato](media/logic-apps-enterprise-integration-schemas/schema-blob.png)

2. In **Aggiungi schema** selezionare **File grande**, quindi specificare l'URI nella casella di testo **URI del contenuto**.

    ![Schemi con evidenziati il pulsante "Aggiungi" e l'opzione "Large file" (File grande)](media/logic-apps-enterprise-integration-schemas/schema-largefile.png)

Se il livello di accesso di sicurezza BLOB è **Nessun accesso anonimo**, seguire questa procedura.

![Azure Storage Explorer, con evidenziate le opzioni "Contenitori BLOB", "Sicurezza" e "No anonymous access" (Nessun accesso anonimo)](media/logic-apps-enterprise-integration-schemas/blob-1.png)

1. Caricare lo schema nell'account di archiviazione.

    ![Account di archiviazione](media/logic-apps-enterprise-integration-schemas/blob-3.png)

2. Generare una firma di accesso condiviso per lo schema.

    ![Account di archiviazione con evidenziata la scheda delle firme di accesso condiviso](media/logic-apps-enterprise-integration-schemas/blob-2.png)

3. In **Aggiungi schema**, selezionare **File grande**, quindi specificare l'URI della firma di accesso condiviso nella casella di testo **URI del contenuto**.

    ![Schemi con evidenziati il pulsante "Aggiungi" e l'opzione "Large file" (File grande)](media/logic-apps-enterprise-integration-schemas/schema-largefile.png)

4. Nel pannello **Schemi** dell'account di integrazione dovrebbe essere visualizzato lo schema appena aggiunto.

    ![Account di integrazione EIP con evidenziati il pannello "Schemi" e il nuovo schema](media/logic-apps-enterprise-integration-schemas/schema-41.png)

## <a name="edit-schemas"></a>Modificare gli schemi

1. Selezionare il riquadro **Schemi**.

2. Selezionare lo schema da modificare quando il pannello **Schemi** si apre.

3. Nel pannello **Schemi** selezionare **Modifica**.

    ![Pannello Schemi](media/logic-apps-enterprise-integration-schemas/edit-12.png)

4. Selezionare il file di schema che si desidera modificare, quindi selezionare **Apri**.

    ![Aprire il file di schema da modificare](media/logic-apps-enterprise-integration-schemas/edit-31.png)

In Azure viene visualizzato il messaggio che lo schema è stato caricato correttamente.

## <a name="delete-schemas"></a>Eliminare gli schemi

1. Selezionare il riquadro **Schemi**.

2. Selezionare lo schema da eliminare quando il pannello **Schemi** si apre.

3. Nel pannello **Schemi** selezionare **Elimina**.

    ![Pannello Schemi](media/logic-apps-enterprise-integration-schemas/delete-12.png)

4. Per confermare che si desidera eliminare lo schema selezionato scegliere **Sì**.

    ![Messaggio di conferma "Elimina schema"](media/logic-apps-enterprise-integration-schemas/delete-21.png)

    Nel pannello **Schemi** si aggiorna l'elenco degli schemi, che non include più lo schema che è stato eliminato.

    ![Account di integrazione con il riquadro "Schemi" evidenziato](media/logic-apps-enterprise-integration-schemas/delete-31.png)

## <a name="next-steps"></a>Passaggi successivi
* [Altre informazioni su Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Informazioni su Enterprise Integration Pack").  




<!--HONumber=Feb17_HO1-->


