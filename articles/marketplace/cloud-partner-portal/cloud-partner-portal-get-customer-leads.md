---
title: Ottenere lead per Azure| Microsoft Docs
description: "Questo articolo illustra che tutte le informazioni sulla generazione lead di Microsoft e la modalità di connessione del sistema CRM per integrare la pipeline di vendita con il portale cloud per i partner."
services: cloud-partner-portal
documentationcenter: 
author: Bigbrd
manager: hamidm
ms.service: cloud-partner-portal
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: brdi
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 6207695ede6c01f9423ddde7467d2a9b1ed35bd3
ms.lasthandoff: 04/18/2017


---

# <a name="get-customer-leads"></a>Ottenere lead 
I clienti costituiscono il fulcro di qualsiasi attività valida. Nella trasformazione odierna delle acquisizioni di prodotti, gli addetti al marketing devono concentrarsi sulla connessione diretta con i clienti e la creazione di una relazione. Ecco perché la generazione di lead di alta qualità è uno strumento fondamentale per il ciclo di vendita.
Il portale cloud per i partner consente all'utente di ricevere informazioni di contatto del cliente a livello di codice **immediatamente** dopo che il cliente esprime interesse o distribuisce il prodotto. In questo documento, verranno esaminate tutte le informazioni necessarie sui lead generati, sulla modalità di connessione al sistema CRM e di integrazione nelle pipeline di vendita.

## <a name="what-are-these-leads"></a>Cosa sono i lead?
I lead sono i clienti che distribuiscono i prodotti dal Marketplace. Che il prodotto sia elencato nel [Marketplace di Azure](https://azuremarketplace.microsoft.com/) o in [AppSource](https://appsource.com/), l'utente potrà ricevere i lead dei clienti interessati al prodotto.
### <a name="azure-marketplace"></a>Azure Marketplace
1.    Il cliente ha fatto un "Test drive" per l'offerta. I test drive sono un'opportunità accelerata per condividere immediatamente la propria attività con potenziali clienti senza limiti di ingresso. Tutti i test drive genereranno un lead per un cliente interessato a provare il prodotto e ad avere altre informazioni.  Per altre informazioni sui test drive vedere l'articolo sui [test drive del Marketplace di Azure](https://azuremarketplace.azureedge.net/documents/azure-marketplace-test-drive-program.pdf).

    ![Offerta del test drive](./media/cloud-partner-portal-get-customer-leads/test-drive-offer.png)
 

2.    Il cliente accetta di condividere le informazioni dopo aver fatto clic sul pulsante "Get it now" (Ottieni subito). Questo è un lead di interesse iniziale, in cui si condivide un cliente che ha espresso interesse nel ricevere il prodotto ed è la parte iniziale dell'imbuto di acquisizione.
 
    ![Pulsante Get it now (Ottieni subito)](./media/cloud-partner-portal-get-customer-leads/get-it-now-button.png)

3.    Il cliente fa clic su "Acquista" nel [portale di Azure](https://portal.azure.com/) quando acquista ufficialmente il prodotto. Questo è un lead attivo, in cui si condivide un cliente che ha una sottoscrizione di Azure e ha iniziato la distribuzione di prodotti effettivi.

    ![Pulsante con testo Acquista](./media/cloud-partner-portal-get-customer-leads/purchase-button.png) 

### <a name="appsource"></a>AppSource
1.    Il cliente ha fatto un "Test drive" per l'offerta. I test drive sono un'opportunità accelerata per condividere immediatamente la propria attività con potenziali clienti senza limiti di ingresso. Tutti i test drive genereranno un lead per un cliente interessato a provare il prodotto e ad avere altre informazioni.  Per altre informazioni sui test drive vedere l'articolo sui [test drive del Marketplace di AppSource](https://appsource.microsoft.com/blogs/want-to-try-an-app-take-a-test-drive).

    ![Offerta del test drive](./media/cloud-partner-portal-get-customer-leads/test-drive-offer-2.png)

2.    Il cliente accetta di condividere le informazioni dopo aver fatto clic sul pulsante "Get it now" (Ottieni subito). Questo è un lead di interesse iniziale, in cui si condivide un cliente che ha espresso interesse nel ricevere il prodotto ed è la parte iniziale dell'imbuto di acquisizione.

    ![Pulsante Get it now (Ottieni subito)](./media/cloud-partner-portal-get-customer-leads/get-it-now-button-2.png)

3.    Il cliente fa clic sul pulsante "Contact me" (Contattami) sull'offerta. Questo è un lead attivo, in cui si condivide un cliente che ha richiesto attivamente di ricevere altre informazioni sul prodotto.

    ![Contact me (Contattami)](./media/cloud-partner-portal-get-customer-leads/contact-me-image.png)

## <a name="lead-data"></a>Dati lead
Ogni lead che si riceve disporrà di dati passati in questi campi specifici. Poiché si otterranno lead da più passaggi nel percorso di acquisto di ciascun cliente, il modo migliore per gestire i lead è deduplicare e personalizzare i follow-up. In questo modo ogni cliente riceve un messaggio appropriato e si sta creando una relazione univoca ogni volta.

|Nome campo|esempi|
|----------|--------|
|LeadSource| Il formato **Origine**-**Azione**&#124;**Offerta** <br/><br/>**Origini**:<br/>"AzureMarketplace",<br/>"AzurePortal",<br/>"TestDrive", <br/>"SPZA" (acronimo di AppSource)<br/><br/>**Azioni**:<br/>"INS": è l'acronimo per installazione. Si trova nel Marketplace di Azure o in AppSource ogni volta che un cliente preme il pulsante per acquistare il prodotto.<br/><br/>"PLT": sta per Versione di valutazione condotta dal partner. Si trova su AppSource ogni volta che un cliente preme il pulsante Contact me (Contattami).<br/><br/>"DNC": sta per non contattare. Si trova in AppSource ogni volta che un partner che è stato elencato in modo incrociato nella pagina app riceve una richiesta per essere contattato. Viene condiviso l'annuncio che questo cliente è stato elencato nell'app, ma non deve essere contattato.<br/><br/>"Crea": si trova solo nel portale di Azure ed si genera ogni volta che un cliente acquista l'offerta dal proprio account.<br/><br/>"StartTestDrive": riguarda solo i test drive e si genera ogni volta che un cliente avvia il test drive.<br/><br/>**Offerte**:<br/>    "checkpoint.check-point-r77-10sg-byol",<br/>"bitnami.openedxcypress",<br/>"docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145a"<br/> <br/> Questi sono gli identificatori univoci assegnati all'utente e all'offerta specifica. |
|CustomerInfo|    {<br/>"FirstName":"John",<br/>"LastName":"Smith",<br/>"Email":"jsmith@microsoft.com",<br/>"Telefono":"1234567890",<br/>"Paese": "USA",<br/>"Società":"Microsoft",<br/>"Titolo":"CTO"<br/>}<br/><br/>Nota: non tutti i dati saranno disponibili per ogni lead|


Microsoft si è impegnata attivamente nel miglioramento dei lead, pertanto se un campo dati non viene visualizzato ma si desidera visualizzarlo qui [inviare i commenti a Microsoft](mailto:AzureMarketOnboard@microsoft.com).

## <a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>Come connettere il sistema CRM con il portale cloud per i partner 
Per iniziare a ottenere lead, è stato creato il connettore di gestione di lead nel portale cloud per i partner in modo che si possano inserire agevolmente le informazioni di CRM per creare la connessione. A questo punto è possibile sfruttare facilmente i lead generati dal marketplace senza un notevole sforzo di progettazione per integrarli con un sistema esterno.
 
![Connettore di gestione lead](./media/cloud-partner-portal-get-customer-leads/lead-management-connector.png) I lead possono essere scritti in un'ampia gamma di sistemi CRM o direttamente in una tabella di Archiviazione di Azure in cui è possibile gestire i lead come si desidera. Di seguito sono riportate istruzioni dettagliate su come connettere ognuna delle destinazioni possibili di lead: < per farlo usare file di markdown aggiornati come collegamenti da appsource/testdrive! Ripassare le connessioni lead. Solo i campi, non i JSON, sono richiesto in pagine distinte >

*    Dynamics CRM Online: [fare clic qui](./cloud-partner-portal-lead-management-instructions-dynamics.md) per ottenere le istruzioni sulle modalità di configurazione di Dynamics CRM Online per ottenere i lead. 
*    Marketo: [fare clic qui](./cloud-partner-portal-lead-management-instructions-marketo.md) per ottenere le istruzioni per impostare la configurazione lead di Marketo e ottenere i lead.
*    Salesforce: [fare clic qui](./cloud-partner-portal-lead-management-instructions-salesforce.md) per ottenere istruzioni sulle impostazioni dell'istanza Salesforce e ottenere i lead.
*    Tabella di Azure: [fare clic qui](./cloud-partner-portal-lead-management-instructions-azure-table.md) per ottenere le istruzioni per la configurazione dell'account di archiviazione di Azure e ottenere i lead in una tabella di Azure. 

Una volta configurate correttamente la destinazione dei lead e aver premuto Pubblica sull'offerta, verrà convalidata la connessione e inviato un lead di test all'utente. Quando si visualizza l'offerta prima della distribuzione, è inoltre possibile testare la connessione lead quando si tenta di acquisire l'offerta nell'ambiente di anteprima.  È importante assicurarsi che le impostazioni lead siano aggiornate in modo da non perdere i lead e assicurarsi di aggiornare queste connessioni ogni volta che c'è una modifica da parte dell'utente.

## <a name="what-next"></a>Come procedere? 
Una eseguita la configurazione tecnica, è necessario incorporare i lead nelle vendite attuali, nella strategia di marketing e nei processi operativi. L'intento è quello di comprendere meglio il processo complessivo di vendita e di collaborare con l'utente per creare lead di alta qualità e dari sufficienti a garantire il successo dell'utente. Ogni commento su come ottimizzare e migliorare i lead inviati con dati aggiuntivi per contribuire ad avere clienti di successo è apprezzato.
Microsoft è interessata a sapere se l'utente è disposto a [fornire commenti](mailto:AzureMarketOnboard@microsoft.com) e suggerimenti per consentire al team delle vendite di sviluppare i lead del Marketplace in modo ottimale.




