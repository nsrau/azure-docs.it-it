---
title: Informazioni su come creare un contratto AS2 per Enterprise Integration Pack | Documentazione Microsoft
description: Informazioni su come creare un contratto AS2 per Enterprise Integration Pack | App per la logica di Azure
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: 
ms.assetid: c9b7e1a9-4791-474c-855f-988bd7bf4b7f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 47a1c5653d84a55224c18222bc547e1c863ea442
ms.openlocfilehash: ea256557a38b3ce6cb457d195fa4b48cfd8879b8


---
# <a name="enterprise-integration-with-as2"></a>Enterprise Integration con AS2
## <a name="create-an-as2-agreement"></a>Creare un contratto AS2
Per usare le funzionalità aziendali nelle app per la logica, è prima di tutto necessario creare contratti. 

### <a name="heres-what-you-need-before-you-get-started"></a>Di seguito sono riportati i prerequisiti necessari per iniziare
* Un [account di integrazione](../logic-apps/logic-apps-enterprise-integration-accounts.md) definito nella sottoscrizione di Azure  
* Almeno due [partner](logic-apps-enterprise-integration-partners.md) già definiti nell'account di integrazione  

> [!NOTE]
> Quando si crea un contratto, il contenuto del file del contratto deve corrispondere al tipo di contratto.    
> 
> 

Dopo aver [creato un account di integrazione](../logic-apps/logic-apps-enterprise-integration-accounts.md) e [aggiunto i partner](logic-apps-enterprise-integration-partners.md), è possibile creare un contratto seguendo questa procedura:  

### <a name="from-the-azure-portal-home-page"></a>Nella home page del portale di Azure
Dopo aver eseguito l'accesso al [portale di Azure](http://portal.azure.com "portale di Azure"):  

1. Selezionare **Altri servizi** e immettere **integrazione** nella casella di ricerca Filtro. Selezionare **Account di integrazione** nell'elenco dei risultati.    
![](./media/logic-apps-enterprise-integration-agreements/overview-1.png)    
2. Selezionare l'account di integrazione a cui aggiungere il certificato. 
![](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
3. Selezionare il riquadro **Accordi** . Se il riquadro Accordi non viene visualizzato, aggiungerlo.   
![](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)   
4. Selezionare il pulsante **Aggiungi** nel pannello Accordi visualizzato.  
![](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)  
5. Nel pannello Accordi immettere un **nome** per il contratto e selezionare **AS2** in **Tipo di contratto**, **Partner host**, **Identità host**, **Partner guest** e **Identità guest**.  
![](./media/logic-apps-enterprise-integration-agreements/agreement-3.png)  

Di seguito sono riportati alcuni dettagli che possono risultare utili quando si configurano le impostazioni del contratto: 

| Proprietà | Descrizione |
| --- | --- |
| Host Partner (Partner host) |Un contratto prevede un partner host e un partner guest. Il partner host rappresenta l'organizzazione che sta configurando il contratto. |
| Host Identity (Identità host) |Un identificatore per il partner host. |
| Guest Partner (Partner guest) |Un contratto prevede un partner host e un partner guest. Il partner guest rappresenta l'organizzazione che intrattiene attività commerciali con il partner host. |
| identità guest |Un identificatore per il partner guest. |
| Receive Settings (Impostazioni di ricezione) |Queste proprietà si applicano a tutti i messaggi ricevuti da un contratto |
| Send Settings (Impostazioni di invio) |Queste proprietà si applicano a tutti i messaggi inviati da un contratto |

Per continuare:  

1. Selezionare **Receive Settings** (Impostazioni di ricezione) per configurare la gestione dei messaggi ricevuti tramite questo contratto.  
   
   * Facoltativamente, è possibile ignorare le proprietà del messaggio in ingresso. A tale scopo, selezionare **Ignora proprietà del messaggio**.
   * Selezionare **Il messaggio deve essere firmato** se si vuole richiedere la firma di tutti i messaggi in ingresso. Se si seleziona questa opzione, è necessario selezionare il *certificato pubblico del partner guest* per convalidare la firma dei messaggi.
   * Selezionare **Il messaggio deve essere crittografato** se si vuole richiedere la crittografia di tutti i messaggi in ingresso.  Se si seleziona questa opzione, è necessario selezionare il *certificato privato del partner host* per decrittografare i messaggi in ingresso.
   * È anche possibile richiedere la compressione dei messaggi. A tale scopo, selezionare **Il messaggio deve essere compresso**.    
   * Selezionare **Invia notifica sulla ricezione del messaggio** per inviare la notifica sulla ricezione del messaggio sincrona per i messaggi ricevuti.
   * Selezionare **Invia notifica sulla ricezione del messaggio firmata** per inviare la notifica sulla ricezione del messaggio firmata per i messaggi ricevuti.
   * Selezionare **Invia notifica sulla ricezione del messaggio asincrona** per inviare la notifica sulla ricezione del messaggio asincrona per i messaggi ricevuti.     
    ![](./media/logic-apps-enterprise-integration-agreements/agreement-4.png)  

Per altre informazioni sull'attivazione delle impostazioni di ricezione, vedere la tabella seguente.  

| Proprietà | Descrizione |
| --- | --- |
| Override message properties |Selezionare questa opzione per indicare che è possibile eseguire l'override delle proprietà nei messaggi ricevuti |
| Il messaggio deve essere firmato |Abilitare questa opzione per richiedere la firma digitale dei messaggi.  Configurare il certificato pubblico del partner guest per la verifica della firma.  |
| Il messaggio deve essere crittografato |Abilitare questa opzione per richiedere la crittografia dei messaggi. I messaggi non crittografati verranno rifiutati. Configurare il certificato privato del partner host per decrittografare i messaggi.  |
| Il messaggio deve essere compresso |Abilitare questa opzione per richiedere la compressione dei messaggi. I messaggi non compressi verranno rifiutati. |
| Testo MDN |Si tratta di una notifica sulla ricezione del messaggio predefinita da inviare al mittente del messaggio |
| Send MDN (Invia MDN) |Abilitare questa opzione per consentire l'invio delle notifiche sulla ricezione del messaggio. |
| Send signed MDN (Invia MDN firmato) |Abilitare questa opzione per richiedere la firma delle notifiche sulla ricezione del messaggio. |
| MIC Algorithm (Algoritmo MIC) | |
| Send asynchronous MDN (Invia MDN asincrono) |Abilitare questa opzione per richiedere l'invio asincrono dei messaggi. |
| URL |Si tratta dell'URL a cui verranno inviate le notifiche sulla ricezione del messaggio. |

Per continuare:  

1. Selezionare **Impostazioni di invio** per configurare la gestione dei messaggi inviati tramite questo contratto.  

   * Selezionare **Abilita firma del messaggio** per inviare messaggi firmati al partner. Se si seleziona questa opzione, è necessario selezionare l'*algoritmo per il controllo di integrità delle credenziali del certificato privato del partner host* e il *certificato privato del partner host* per la firma dei messaggi.
   * Selezionare **Abilita crittografia messaggio** per inviare messaggi crittografati al partner. Se si seleziona questa opzione, è necessario selezionare l'*algoritmo del certificato pubblico del partner guest* e il *certificato pubblico del partner guest* per la crittografia dei messaggi.
   * Selezionare **Il messaggio deve essere compresso** per comprimere il messaggio. 
   * Selezionare **Espandi intestazioni HTTP** per espandere l'intestazione HTTP del tipo di contenuto in una singola riga. 
   * Selezionare **Richiedi notifica sulla ricezione del messaggio** per ricevere la notifica sulla ricezione del messaggio sincrona per i messaggi inviati.
   * Selezionare **Richiedi notifica sulla ricezione del messaggio firmata** per ricevere la notifica sulla ricezione del messaggio firmata per i messaggi inviati.
   * Selezionare **Richiedi notifica sulla ricezione del messaggio asincrona** per ricevere la notifica sulla ricezione del messaggio asincrona per i messaggi inviati. Se si seleziona questa opzione, è necessario specificare un URL a cui verranno inviate le notifiche sulla ricezione del messaggio.  
   * Selezionare **Abilita rilevamento messaggi (NRR)** per abilitare il rilevamento del non ripudio della ricezione.    
   ![](./media/logic-apps-enterprise-integration-agreements/agreement-5.png)  

Per altre informazioni sull'attivazione delle impostazioni di invio, vedere la tabella seguente.  

| Proprietà | Descrizione |
| --- | --- |
| Enable message signing (Abilita la firma dei messaggi) |Selezionare questa opzione per abilitare la firma di tutti i messaggi inviati dal contratto. |
| MIC Algorithm (Algoritmo MIC) |Selezionare l'algoritmo da usare per la firma dei messaggi. Configurare l'algoritmo per il controllo di integrità delle credenziali del certificato privato del partner host per la firma dei messaggi. |
| Certificate |Selezionare il certificato da usare per la firma dei messaggi. Configurare il certificato privato del partner host per la firma dei messaggi. |
| Enable message encryption (Abilita la crittografia dei messaggi) |Selezionare questa opzione per crittografare tutti i messaggi inviati dal contratto. Configurare l'algoritmo del certificato pubblico del partner guest per la crittografia dei messaggi. |
| Algoritmo di crittografia |Selezionare l'algoritmo di crittografia da usare per la crittografia dei messaggi. Configurare il certificato pubblico del partner guest per la crittografia dei messaggi. |
| Espandi intestazioni HTTP |Selezionare questa opzione per espandere l'intestazione HTTP del tipo di contenuto in una singola riga. |
| Richiedi MDN |Abilitare questa opzione per richiedere una notifica sulla ricezione del messaggio per tutti i messaggi inviati dal contratto. |
| Richiedi MDN firmato |Abilitare questa opzione per richiedere la firma di tutte le notifiche sulla ricezione del messaggio inviate a questo contratto |
| Richiedi MDN asincrono |Abilitare questa opzione per richiedere l'invio di notifiche sulla ricezione del messaggio a questo contratto |
| URL |L'URL a cui verranno inviate le notifiche sulla ricezione del messaggio |
| Enable NRR (Attiva NRR) |Selezionare questa casella di controllo per abilitare il rilevamento del non ripudio della ricezione. |

Selezionare il riquadro **Agreements** (Contratti) nel pannello Integration Account (Account di integrazione) e verrà visualizzato il nuovo contratto aggiunto.  
![](./media/logic-apps-enterprise-integration-agreements/agreement-6.png)

## <a name="next-steps"></a>Passaggi successivi
* [Altre informazioni su Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Informazioni su Enterprise Integration Pack")  


<!--HONumber=Jan17_HO4-->


