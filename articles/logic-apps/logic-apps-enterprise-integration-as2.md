---
title: Creare un contratto AS2 nelle app per la logica di Azure | Documentazione Microsoft
description: Creare un contratto AS2 per Enterprise Integration Pack | App per la logica di Azure
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: c9b7e1a9-4791-474c-855f-988bd7bf4b7f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 6a947ea997bbcfe1b6b28c7cbb49911836750e6a
ms.openlocfilehash: a490b89c5420aecdfb3f79289979faab9a4630e9


---
# <a name="enterprise-integration-with-as2"></a>Enterprise Integration con AS2
Per usare le funzionalità aziendali nelle app per la logica, è prima di tutto necessario creare contratti.

## <a name="prerequisites"></a>Prerequisiti
* Un [account di integrazione](../logic-apps/logic-apps-enterprise-integration-accounts.md) definito nella sottoscrizione di Azure.  
* Almeno due [partner](logic-apps-enterprise-integration-partners.md) già definiti nell'account di integrazione.  

> [!NOTE]
> Quando si crea un contratto, il contenuto del file del contratto deve corrispondere al tipo di contratto.    

Dopo aver creato un account di integrazione e aggiunto i partner, è possibile creare un contratto usando le procedure contenute nelle sezioni seguenti.  

## <a name="create-an-agreement"></a>Creare un contratto

1. Accedere al [Portale di Azure](http://portal.azure.com "Portale di Azure").  
2. Selezionare **Altri servizi**, inserire **integrazione** nella casella di ricerca del filtro e quindi selezionare **Account di integrazione** nell'elenco dei risultati.

 ![Selezionare "Account di integrazione" nell'elenco dei risultati della ricerca.](./media/logic-apps-enterprise-integration-agreements/overview-1.png)    
3. Selezionare l'account di integrazione a cui aggiungere il certificato.

 ![Selezionare l'account di integrazione.](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
4. Selezionare il riquadro **Accordi** . Se il riquadro non è visualizzato, aggiungerlo.

 ![Selezionare il riquadro "Contratti".](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)   
5. Nel pannello **Contratti** selezionare **Aggiungi**.

 ![Selezionare "Aggiungi"](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)  
6. Immettere il nome del contratto, selezionare **AS2** nell'elenco **Tipo di contratto** e inserire le informazioni appropriare negli elenchi **Partner host**, **Identità host**, **Partner guest** e **Identità guest**.

 ![Immettere un nome per il contratto.](./media/logic-apps-enterprise-integration-agreements/agreement-3.png)  

 La tabella seguente descrive le proprietà nella finestra di dialogo **Aggiungi**:

    | Proprietà | Descrizione |
    | --- | --- |
    | Host Partner (Partner host) | Un contratto richiede un partner host e un partner guest. Il partner host rappresenta l'organizzazione che sta configurando il contratto. |
    | Host Identity (Identità host) | Un identificatore per il partner host. |
    | Guest Partner (Partner guest) | Un contratto richiede un partner host e un partner guest. Il partner guest rappresenta l'organizzazione che intrattiene attività commerciali con il partner host. |
    | identità guest | Un identificatore per il partner guest. |
    | Receive Settings (Impostazioni di ricezione) | Proprietà che si applicano a tutti i messaggi ricevuti dal contratto. |
    | Send Settings (Impostazioni di invio) | Proprietà che si applicano a tutti i messaggi inviati dal contratto. |

7. Per configurare la gestione dei messaggi ricevuti tramite questo contratto, procedere come segue:

 a. Selezionare **Impostazioni di ricezione**.

 b. Facoltativamente, è possibile eseguire l'override delle proprietà dei messaggi in ingresso selezionando la casella di controllo **Ignora proprietà del messaggio**.

 c. Selezionare la casella di controllo **Il messaggio deve essere firmato** per richiedere la firma di tutti i messaggi in ingresso. Se si seleziona questa opzione, convalidare la firma dei messaggi selezionando il **certificato pubblico del partner guest** nell'elenco **Certificato**.

 d. Per richiedere la firma di tutti i messaggi in ingresso, selezionare la casella di controllo **Il messaggio deve essere firmato**. Se si seleziona questa opzione, decrittografare i messaggi in ingresso selezionando il **certificato privato del partner host** nell'elenco **Certificato**.

 e. Per richiedere la compressione dei messaggi, selezionare la casella di controllo **Il messaggio deve essere compresso**.    

 f. Per inviare una notifica sulla ricezione del messaggio (MDN) per i messaggi ricevuti, selezionare la casella di controllo **Invia notifica sulla ricezione del messaggio**.

 g. Per inviare notifiche sulla ricezione del messaggio firmate per i messaggi ricevuti, selezionare la casella di controllo **Invia notifica sulla ricezione del messaggio firmata**.

 h. Per inviare notifiche asincrone sulla ricezione del messaggio per i messaggi ricevuti, selezionare la casella di controllo **Invia notifica sulla ricezione del messaggio asincrona**.

 ![Impostare le proprietà "Impostazioni di ricezione"](./media/logic-apps-enterprise-integration-agreements/agreement-4.png)  

 La tabella seguente illustra le proprietà delle **Impostazioni di ricezione**:  

 | Proprietà | Descrizione |
 | --- | --- |
 | Override message properties | Indica che è possibile eseguire l'override delle proprietà nei messaggi ricevuti. |
 | Il messaggio deve essere firmato | Richiede la firma digitale dei messaggi. Configurare il certificato pubblico del partner guest per la verifica della firma.  |
 | Il messaggio deve essere crittografato | Richiede la crittografia dei messaggi. I messaggi non crittografati verranno rifiutati. Configurare il certificato privato del partner host per la decrittografia dei messaggi.  |
 | Il messaggio deve essere compresso | Richiede la compressione dei messaggi. I messaggi non compressi verranno rifiutati. |
 | Testo MDN | Notifica sulla ricezione del messaggio (MDN) predefinita da inviare al mittente del messaggio. |
 | Send MDN (Invia MDN) | Richiede l'invio della MDN. |
 | Send signed MDN (Invia MDN firmato) | Richiede la firma delle MDN. |
 | MIC Algorithm (Algoritmo MIC) | |
 | Send asynchronous MDN (Invia MDN asincrono) | Richiede l'invio asincrono dei messaggi. |
 | URL | URL a cui devono essere inviate le MDN. |

8. Per configurare la gestione dei messaggi inviati tramite questo contratto, procedere come segue:

 a. Selezionare **Impostazioni di invio**.  

 b. Per inviare messaggi firmati al partner, selezionare la casella di controllo **Abilita firma del messaggio**. Se si seleziona questa opzione, firmare i messaggi selezionando l'**algoritmo per il controllo di integrità delle credenziali del certificato privato del partner host** nell'elenco **Algoritmo per il controllo di integrità delle credenziali** e il **certificato privato del partner host** nell'elenco **Certificato**.

 c. Per inviare messaggi crittografati al partner, selezionare la casella di controllo **Abilita crittografia messaggio**. Se si seleziona questa opzione, crittografare i messaggi selezionando l'**algoritmo per il controllo del certificato pubblico del partner guest** nell'elenco **Algoritmo di crittografia** e il **certificato pubblico del partner guest** nell'elenco **Certificato**.

 d. Per comprimere il messaggio, selezionare la casella di controllo **Abilita compressione messaggio**.

 e. Per espandere l'intestazione HTTP del tipo di contenuto in una singola riga, selezionare la casella di controllo **Espandi intestazioni HTTP**.

 f. Per ricevere notifiche sincrone sulla ricezione del messaggio per i messaggi inviati, selezionare la casella di controllo **Richiedi notifica sulla ricezione del messaggio**.

 g. Per ricevere notifiche sulla ricezione del messaggio firmate per i messaggi inviati, selezionare la casella di controllo **Richiedi notifica sulla ricezione del messaggio firmata**.

 h. Per ricevere notifiche asincrone sulla ricezione del messaggio per i messaggi inviati, selezionare la casella di controllo **Richiedi notifica sulla ricezione del messaggio asincrona**. Se si seleziona questa opzione, immettere l'URL a cui inviare le MDN.  

 i. Per richiedere il non ripudio della ricezione, selezionare la casella di controllo **Abilita rilevamento messaggi (NRR)**.

 j. Selezionare **OK**.

 ![Impostare le proprietà "Impostazioni di invio"](./media/logic-apps-enterprise-integration-agreements/agreement-5.png)  

 La tabella seguente illustra le proprietà delle **Impostazioni di invio**:  

 | Proprietà | Descrizione |
 | --- | --- |
 | Enable message signing (Abilita la firma dei messaggi) | Richiede la firma di tutti i messaggi inviati dal contratto. |
 | MIC Algorithm (Algoritmo MIC) | Algoritmo da usare per firmare i messaggi. Configura algoritmo per il controllo di integrità delle credenziali del certificato privato del partner host per la firma dei messaggi. |
 | Certificate | Certificato da usare per firmare i messaggi. Configura il certificato privato del partner host per la firma dei messaggi. |
 | Enable message encryption (Abilita la crittografia dei messaggi) | Richiede la crittografia di tutti i messaggi inviati da questo contratto. Configura l'algoritmo del certificato pubblico del partner guest per la crittografia dei messaggi. |
 | Algoritmo di crittografia | Algoritmo di crittografia da usare per la crittografia dei messaggi. Configura il certificato pubblico del partner guest per la crittografia dei messaggi. |
 | Certificate | Certificato da usare per crittografare i messaggi. Configura il certificato privato del partner guest per crittografare i messaggi. |
 | Abilita compressione messaggio | Richiede la compressione di tutti i messaggi inviati da questo contratto. |
 | Espandi intestazioni HTTP | Posiziona l'intestazione HTTP del tipo di contenuto in una singola riga. |
 | Richiedi MDN | Richiede un MDN per tutti i messaggi inviati da questo contratto. |
 | Richiedi MDN firmato | Richiede la firma di tutti gli MDN che vengono inviati a questo contratto. |
 | Richiedi MDN asincrono | Richiede l'invio di MDN asincroni a questo contratto. |
 | URL | URL a cui devono essere inviate le MDN. |
 | Enable NRR (Attiva NRR) | Richiede il non ripudio della ricezione (NRR), un attributo di comunicazione che fornisce prova di ricezione dei dati come inviati. |

## <a name="view-the-agreements-list"></a>Visualizzare l'elenco Contratti
Per visualizzare il nuovo contratto aggiunto, selezionare il riquadro **Contratti** nel pannello **Account di integrazione**.

![Visualizzare l'elenco "Contratti"](./media/logic-apps-enterprise-integration-agreements/agreement-6.png)

## <a name="next-steps"></a>Passaggi successivi
* [Altre informazioni su Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Informazioni su Enterprise Integration Pack")  



<!--HONumber=Feb17_HO1-->


