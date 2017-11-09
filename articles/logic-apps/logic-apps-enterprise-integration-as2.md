---
title: Messaggi AS2 per l'integrazione aziendale B2B - App per la logica di Azure | Documentazione Microsoft
description: Scambiare messaggi AS2 per l'integrazione aziendale B2B con App per la logica di Azure
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
ms.date: 06/08/2017
ms.author: LADocs; mandia
ms.openlocfilehash: 91b2f16611b88aa4b9395ca301d88042065ad9dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="exchange-as2-messages-for-enterprise-integration-with-logic-apps"></a>Scambiare messaggi AS2 per l'integrazione aziendale con le app per la logica di Azure

Per poter scambiare messaggi AS2 con App per la logica di Azure, è necessario creare un contratto AS2 e archiviarlo nell'account di integrazione. Di seguito viene illustrata la procedura per la creazione di un contratto AS2.

## <a name="before-you-start"></a>Prima di iniziare

Sono necessari gli elementi seguenti:

* Un [account di integrazione](../logic-apps/logic-apps-enterprise-integration-accounts.md) già definito e associato alla sottoscrizione di Azure.
* Almeno due [partner](logic-apps-enterprise-integration-partners.md) già definiti nell'account di integrazione e configurati con il qualificatore AS2 in **Identità di business**.

> [!NOTE]
> Quando si crea un contratto, il contenuto del file del contratto deve corrispondere al tipo di contratto.    

Dopo aver [creato un account di integrazione](../logic-apps/logic-apps-enterprise-integration-accounts.md) e [aggiunto i partner](logic-apps-enterprise-integration-partners.md), è possibile creare un contratto AS2 attenendosi alla procedura seguente.

## <a name="create-an-as2-agreement"></a>Creare un contratto AS2

1.  Accedere al [Portale di Azure](http://portal.azure.com "Portale di Azure").  

2.  Fare clic su **Altri servizi** nel menu a sinistra. Nella casella di ricerca, digitare **integrazione** come filtro. Nell'elenco dei risultati selezionare **Account di integrazione**.

    > [!TIP]
    > Se **Altri servizi** non viene visualizzato, potrebbe essere necessario espandere il menu. Nella parte superiore del menu compresso, selezionare **Visualizza menu**.

    ![Altri servizi, filtro su "integrazione", selezionare "Account di integrazione"](./media/logic-apps-enterprise-integration-agreements/overview-1.png)

3. Nel pannello **Account di integrazione** visualizzato selezionare l'account di integrazione nel quale creare il contratto.
Se non viene visualizzato alcun account di integrazione, [crearne prima uno](../logic-apps/logic-apps-enterprise-integration-accounts.md "Tutte le informazioni sugli account di integrazione").  

    ![Selezionare l'account di integrazione in cui si vuole creare il contratto.](./media/logic-apps-enterprise-integration-overview/overview-3.png)

4. Selezionare il riquadro **Accordi**. Se non è presente il riquadro dei contratti, aggiungerlo.

    ![Selezionare il riquadro "Contratti"](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)

5. Nel pannello Contratti visualizzato, selezionare **Aggiungi**.

    ![Selezionare "Aggiungi"](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)

6. In **Aggiungi**, digitare un **nome** per il contratto. In **Tipo di contratto**selezionare **AS2**. Selezionare il **Partner host**, l'**Identità host**, il **Partner guest**, e l'**Identità guest** per il contratto.

    ![Fornire i dettagli relativi al contratto](./media/logic-apps-enterprise-integration-agreements/agreement-3.png)  

    | Proprietà | Descrizione |
    | --- | --- |
    | Nome |Nome del contratto |
    | Tipo di contratto | Deve essere AS2 |
    | Host Partner (Partner host) |Un contratto prevede un partner host e un partner guest. Il partner host rappresenta l'organizzazione che configura il contratto. |
    | Host Identity (Identità host) |Un identificatore per il partner host |
    | Guest Partner (Partner guest) |Un contratto prevede un partner host e un partner guest. Il partner guest rappresenta l'organizzazione che intrattiene attività commerciali con il partner host. |
    | identità guest |Un identificatore per il partner guest |
    | Receive Settings (Impostazioni di ricezione) |Queste proprietà si applicano a tutti i messaggi ricevuti da un contratto. |
    | Send Settings (Impostazioni di invio) |Queste proprietà si applicano a tutti i messaggi inviati da un contratto. |

## <a name="configure-how-your-agreement-handles-received-messages"></a>Configurare il modo in cui il contratto riceve i messaggi

Dopo aver impostato le proprietà del contratto, è possibile configurare il modo in cui il contratto identifica e gestisce i messaggi in arrivo ricevuti dal partner tramite il presente contratto.

1.  In **Aggiungi**, selezionare **Impostazioni di ricezione**.
Configurare queste proprietà in base al contratto con il partner con cui si scambiano i messaggi. Per le descrizioni delle proprietà, vedere la tabella in questa sezione.

    ![Configurare "Impostazioni di ricezione"](./media/logic-apps-enterprise-integration-agreements/agreement-4.png)

2. Facoltativamente, è possibile eseguire l'override delle proprietà dei messaggi in arrivo selezionando **Ignora proprietà del messaggio**.

3. Selezionare **Il messaggio deve essere firmato** per richiedere la firma di tutti i messaggi in arrivo. Per convalidare la firma dei messaggi, selezionare un **certificato pubblico del partner guest** nell'elenco [Certificato](../logic-apps/logic-apps-enterprise-integration-certificates.md). Se non si dispone del certificato, crearne uno.

4.  Per richiedere la crittografia di tutti i messaggi in arrivo, selezionare **Il messaggio deve essere crittografato**. Per decrittografare i messaggi in arrivo, dall'elenco **Certificato** selezionare un [certificato privato del partner host](../logic-apps/logic-apps-enterprise-integration-certificates.md). Se non si dispone del certificato, crearne uno.

5. Per richiedere la compressione dei messaggi, selezionare **Il messaggio deve essere compresso**.

6. Per inviare una notifica sulla ricezione del messaggio (MDN) per i messaggi ricevuti, selezionare **Invia notifica sulla ricezione del messaggio**.

7. Per inviare notifiche sulla ricezione del messaggio firmate per i messaggi ricevuti, selezionare **Invia notifica sulla ricezione del messaggio firmata**.

8. Per inviare notifiche asincrone sulla ricezione del messaggio per i messaggi ricevuti, selezionare **Invia notifica sulla ricezione del messaggio asincrona**.

9. Al termine, assicurarsi di salvare le impostazioni selezionando **OK**.

Il contratto è pronto per gestire i messaggi in arrivo conformi alle impostazioni selezionate.

| Proprietà | Descrizione |
| --- | --- |
| Override message properties |Indica che è possibile eseguire l'override delle proprietà nei messaggi ricevuti. |
| Il messaggio deve essere firmato |Richiede la firma digitale dei messaggi. Configurare il certificato pubblico del partner guest per la verifica della firma.  |
| Il messaggio deve essere crittografato |Richiede la crittografia dei messaggi. I messaggi non crittografati vengono rifiutati. Configurare il certificato privato del partner host per la decrittografia dei messaggi.  |
| Il messaggio deve essere compresso |Richiede la compressione dei messaggi. I messaggi non compressi vengono rifiutati. |
| Testo MDN |Notifica sulla ricezione del messaggio (MDN) predefinita da inviare al mittente del messaggio. |
| Send MDN (Invia MDN) |Richiede l'invio della MDN. |
| Send signed MDN (Invia MDN firmato) |Richiede la firma delle MDN. |
| MIC Algorithm (Algoritmo MIC) |Selezionare l'algoritmo da usare per firmare i messaggi. |
| Send asynchronous MDN (Invia MDN asincrono) | Richiede l'invio asincrono dei messaggi. |
| URL | Specificare l'URL al quale inviare le MDN. |

## <a name="configure-how-your-agreement-sends-messages"></a>Configurare il modo in cui il contratto invia messaggi

È possibile configurare il modo in cui il contratto identifica e gestisce i messaggi in uscita inviati ai partner tramite il presente contratto.

1.  In **Aggiungi**, selezionare **Impostazioni di avvio**.
Configurare queste proprietà in base al contratto con il partner con cui si scambiano i messaggi. Per le descrizioni delle proprietà, vedere la tabella in questa sezione.

    ![Impostare le proprietà "Impostazioni di invio"](./media/logic-apps-enterprise-integration-agreements/agreement-51.png)

2. Per inviare messaggi firmati al partner, selezionare **Abilita firma del messaggio**. Per firmare i messaggi, nell'elenco **Algoritmo per il controllo di integrità delle credenziali**, selezionare l'*algoritmo per il controllo di integrità delle credenziali del certificato privato del partner host*. Dall'elenco **Certificato**, selezionare un [certificato privato del partner host](../logic-apps/logic-apps-enterprise-integration-certificates.md) esistente.

3. Per inviare messaggi crittografati al partner, selezionare **Abilita crittografia messaggio**. Per crittografare i messaggi, nell'elenco **Algoritmo di crittografia** selezionare l'*algoritmo del certificato pubblico del partner guest*.
Dall'elenco **Certificato**, selezionare un [certificato pubblico del partner guest](../logic-apps/logic-apps-enterprise-integration-certificates.md) esistente.

4. Per comprimere il messaggio, selezionare **Abilita compressione messaggio**.

5. Per espandere l'intestazione HTTP del tipo di contenuto in una singola riga, selezionare **Espandi intestazioni HTTP**.

6. Per ricevere notifiche sincrone sulla ricezione del messaggio per i messaggi inviati, selezionare **Richiedi notifica sulla ricezione del messaggio**.

7. Per ricevere notifiche sulla ricezione del messaggio firmate per i messaggi inviati, selezionare **Richiedi notifica sulla ricezione del messaggio firmata**.

8. Per ricevere notifiche asincrone sulla ricezione del messaggio per i messaggi inviati, selezionare **Richiedi notifica sulla ricezione del messaggio asincrona**. Se si seleziona questa opzione, immettere l'URL a cui inviare le MDN.

9. Per richiedere il non ripudio della ricezione, selezionare **Abilita rilevamento messaggi (NRR)**.  

10. Per specificare il formato di algoritmo da usare per il controllo di integrità delle credenziali o la firma nelle intestazioni in uscita del messaggio AS2 o della notifica sulla ricezione del messaggio, selezionare il **formato di algoritmo SHA2**.  

11. Al termine, assicurarsi di salvare le impostazioni selezionando **OK**.

Il contratto è pronto per gestire i messaggi in uscita conformi alle impostazioni selezionate.

| Proprietà | Descrizione |
| --- | --- |
| Enable message signing (Abilita la firma dei messaggi) |Richiede la firma di tutti i messaggi inviati dal contratto. |
| MIC Algorithm (Algoritmo MIC) |L'algoritmo da usare per firmare i messaggi. Configura algoritmo per il controllo di integrità delle credenziali del certificato privato del partner host per la firma dei messaggi. |
| Certificate |Selezionare il certificato da usare per firmare i messaggi. Configura il certificato privato del partner host per la firma dei messaggi. |
| Enable message encryption (Abilita la crittografia dei messaggi) |Richiede la crittografia di tutti i messaggi inviati da questo contratto. Configura l'algoritmo del certificato pubblico del partner guest per la crittografia dei messaggi. |
| Algoritmo di crittografia |Algoritmo di crittografia da usare per la crittografia dei messaggi. Configura il certificato pubblico del partner guest per la crittografia dei messaggi. |
| Certificate |Certificato da usare per crittografare i messaggi. Configura il certificato privato del partner guest per crittografare i messaggi. |
| Abilita compressione messaggio |Richiede la compressione di tutti i messaggi inviati da questo contratto. |
| Espandi intestazioni HTTP |Posiziona l'intestazione HTTP del tipo di contenuto in una singola riga. |
| Richiedi MDN |Richiede un MDN per tutti i messaggi inviati da questo contratto. |
| Richiedi MDN firmato |Richiede la firma di tutti gli MDN che vengono inviati a questo contratto. |
| Richiedi MDN asincrono |Richiede l'invio di MDN asincroni a questo contratto. |
| URL |Specificare l'URL al quale inviare le MDN. |
| Enable NRR (Attiva NRR) |Richiede il non ripudio della ricezione (NRR), un attributo di comunicazione che fornisce prova di ricezione dei dati come inviati. |
| Formato di algoritmo SHA2 |Selezionare il formato di algoritmo da usare per il controllo di integrità delle credenziali o la firma nelle intestazioni in uscita del messaggio AS2 o della notifica sulla ricezione del messaggio |

## <a name="find-your-created-agreement"></a>Individuare il contratto creato

1.  Dopo aver impostato tutte le proprietà del contratto, scegliere **OK** nel pannello **Aggiungi** per completare la creazione del contratto e tornare al pannello dell'account di integrazione.

    Il contratto appena aggiunto viene visualizzato nell'elenco **Contratti**.

2.  È anche possibile visualizzare i contratti nella panoramica dell'account di Integrazione. Nel pannello dell'account di integrazione, selezionare **Panoramica**, quindi selezionare il riquadro **Contratti**. 

    ![Scegliere il riquadro Contratti per visualizzare tutti i contratti](./media/logic-apps-enterprise-integration-agreements/agreement-6.png)

## <a name="view-the-swagger"></a>Visualizzare il file Swagger
Vedere i [dettagli del file Swagger](/connectors/as2/). 

## <a name="next-steps"></a>Passaggi successivi
* [Altre informazioni su Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Informazioni su Enterprise Integration Pack")  
