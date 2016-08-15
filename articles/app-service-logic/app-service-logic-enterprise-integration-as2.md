<properties 
	pageTitle="Informazioni su come creare un contratto AS2 per Enterprise Integration Pack" 
	description="Informazioni su come creare un contratto AS2 per Enterprise Integration Pack | Servizio app di Microsoft Azure" 
	services="logic-apps" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2016" 
	ms.author="deonhe"/>

# Enterprise Integration con AS2

## Creare un contratto AS2
Per usare le funzionalità aziendali nelle app per la logica, è innanzitutto necessario creare contratti.

### Di seguito sono riportati i prerequisiti necessari per iniziare
- Un [account di integrazione](./app-service-logic-enterprise-integration-accounts.md) definito nella sottoscrizione di Azure
- Almeno due [partner](./app-service-logic-enterprise-integration-partners.md) già definiti nell'account di integrazione

>[AZURE.NOTE]Quando si crea un contratto, il contenuto del file del contratto deve corrispondere al tipo di contratto.


Dopo aver [creato un account di integrazione](./app-service-logic-enterprise-integration-accounts.md) e [aggiunto i partner](./app-service-logic-enterprise-integration-partners.md), è possibile creare un contratto attenendosi alla procedura seguente:

### Nella home page del portale di Azure

Dopo aver eseguito l'accesso al [portale di Azure](http://portal.azure.com "Portale di Azure"):
1. Selezionare **Esplora** dal menu a sinistra.

>[AZURE.TIP]Se non viene visualizzato il collegamento **Esplora**, espandere il menu. A tale scopo, selezionare il collegamento **Mostra menu** nella parte superiore sinistra del menu compresso.

![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. Digitare *integrazione* nella casella di ricerca del filtro e selezionare **Integration Accounts** (Account di integrazione) dall'elenco dei risultati ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. Nel pannello **Account di integrazione** visualizzato, selezionare l'account di integrazione in cui verrà creato il contratto. Se non viene visualizzato alcun account di integrazione, [crearne uno prima](./app-service-logic-enterprise-integration-accounts.md "Tutte le informazioni sugli account di integrazione"). ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)
4.  Selezionare il riquadro **Accordi**. Se non viene visualizzato il riquadro Agreements (Contratti), aggiungerlo. ![](./media/app-service-logic-enterprise-integration-agreements/agreement-1.png)
5. Selezionare il pulsante **Aggiungi** nel pannello Agreements (Contratti) che si apre ![](./media/app-service-logic-enterprise-integration-agreements/agreement-2.png)
6. Immettere un **nome** per il contratto, quindi selezionare il **partner host**, l'**identità host**, il **partner guest** e l'**identità guest** nel pannello Agreements (Contratti) che si apre. ![](./media/app-service-logic-enterprise-integration-agreements/agreement-3.png)

Di seguito sono riportati alcuni dettagli che possono risultare utili quando si configurano le impostazioni del contratto:
  
|Proprietà|Descrizione|
|----|----|
|Host Partner (Partner host)|Un contratto prevede un partner host e un partner guest. Il partner host rappresenta l'organizzazione che sta configurando il contratto.|
|Host Identity (Identità host)|Un identificatore per il partner host. |
|Guest Partner (Partner guest)|Un contratto prevede un partner host e un partner guest. Il partner guest rappresenta l'organizzazione che intrattiene attività commerciali con il partner host.|
|Guest Identity (Identità guest)|Un identificatore per il partner guest.|
|Receive Settings (Impostazioni di ricezione)|Queste proprietà si applicano a tutti i messaggi ricevuti da un contratto|
|Send Settings (Impostazioni di invio)|Queste proprietà si applicano a tutti i messaggi inviati da un contratto|  
Per continuare:
7. Selezionare **Receive Settings** (Impostazioni di ricezione) per configurare la gestione dei messaggi ricevuti tramite questo contratto.
 
 - Facoltativamente, è possibile ignorare le proprietà del messaggio in ingresso. A tale scopo, selezionare la casella di controllo **Override message properties** (Esegui l'override delle proprietà del messaggio).
  - Selezionare la casella di controllo **Il messaggio deve essere firmato** se si desidera richiedere la firma di tutti i messaggi in ingresso. Se si seleziona questa opzione, sarà anche necessario selezionare il **Certificato** che verrà usato per convalidare la firma dei messaggi.
  - Facoltativamente, è possibile richiedere anche la crittografia dei messaggi. A tale scopo, selezionare la casella di controllo **Il messaggio deve essere crittografato**. È quindi necessario selezionare il **Certificato** che verrà usato per decodificare i messaggi in ingresso.
  - È anche possibile richiedere la compressione dei messaggi. A tale scopo, selezionare la casella di controllo **Il messaggio deve essere compresso**. ![](./media/app-service-logic-enterprise-integration-agreements/agreement-4.png)

Per altre informazioni sull'attivazione delle impostazioni di ricezione, vedere la tabella seguente.

|Proprietà|Descrizione|
|----|----|
|Override message properties (Esegui l'override delle proprietà del messaggio)|Selezionare questa opzione per indicare che è possibile eseguire l'override delle proprietà nei messaggi ricevuti |
|Il messaggio deve essere firmato|Abilitare questa opzione per richiedere la firma digitale dei messaggi.|
|Il messaggio deve essere crittografato|Abilitare questa opzione per richiedere la crittografia dei messaggi. I messaggi non crittografati verranno rifiutati.|
|Il messaggio deve essere compresso|Abilitare questa opzione per richiedere la compressione dei messaggi. I messaggi non compressi verranno rifiutati.|
|Testo MDN|Si tratta di una notifica sulla ricezione del messaggio predefinita da inviare al mittente del messaggio|
|Send MDN (Invia MDN)|Abilitare questa opzione per consentire l'invio delle notifiche sulla ricezione del messaggio.|
|Send signed MDN (Invia MDN firmato)|Abilitare questa opzione per richiedere la firma delle notifiche sulla ricezione del messaggio.|
|MIC Algorithm (Algoritmo MIC)||
|Send asynchronous MDN (Invia MDN asincrono)|Abilitare questa opzione per richiedere l'invio asincrono dei messaggi.|
|URL|Si tratta dell'URL a cui verranno inviati i messaggi.|
Per continuare:
8. Selezionare **Send Settings** (Impostazioni di invio) per configurare la gestione dei messaggi inviati tramite questo contratto. ![](./media/app-service-logic-enterprise-integration-agreements/agreement-5.png)

Per altre informazioni sull'attivazione delle impostazioni di invio, vedere la tabella seguente.

|Proprietà|Descrizione|
|----|----|
|Enable message signing (Abilita la firma dei messaggi)|Selezionare questa casella di controllo per abilitare la firma di tutti i messaggi inviati dal contratto.|
|MIC Algorithm (Algoritmo MIC)|Selezionare l'algoritmo da usare per la firma dei messaggi|
|Certificate|Selezionare il certificato da usare per la firma dei messaggi|
|Enable message encryption (Abilita la crittografia dei messaggi)|Selezionare questa casella di controllo per abilitare la crittografia di tutti i messaggi del contratto.|
|Algoritmo di crittografia|Selezionare l'algoritmo di crittografia da usare nella crittografia dei messaggi|
|Espandi intestazioni HTTP|Selezionare questa casella di controllo per espandere l'intestazione HTTP di tipo contenuto in una singola riga.|
|Richiedi MDN|Selezionare questa casella di controllo per richiedere la notifica sulla ricezione del messaggio per tutti i messaggi inviati dal contratto|
|Richiedi MDN firmato|Abilitare questa opzione per richiedere la firma di tutte le notifiche sulla ricezione del messaggio inviate a questo contratto|
|Richiedi MDN asincrono|Abilitare questa opzione per richiedere l'invio di notifiche sulla ricezione del messaggio a questo contratto|
|URL|L'URL a cui verranno inviate le notifiche sulla ricezione del messaggio|
|Enable NRR (Attiva NRR)|Selezionare questa casella di controllo per attivare il non ripudio della ricevuta (NRR).|
La procedura è quasi completa.
9. Selezionare il riquadro **Agreements** (Contratti) nel pannello Integration Account (Account di integrazione) e verrà visualizzato il nuovo contratto aggiunto. ![](./media/app-service-logic-enterprise-integration-agreements/agreement-6.png)

<!---HONumber=AcomDC_0803_2016-->