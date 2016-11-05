---
title: Trasferimento di proprietà di una sottoscrizione di Azure | Microsoft Docs
description: Come trasferire una sottoscrizione di Azure a un altro utente e alcune domande frequenti sul processo
services: ''
documentationcenter: ''
author: genlin
manager: stevenpo
editor: ''
tags: billing,top-support-issue

ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: genli

---
# <a name="transferring-ownership-of-an-azure-subscription"></a>Trasferimento di proprietà di una sottoscrizione di Azure
Se:

* È necessario trasferire la proprietà della propria sottoscrizione di Azure ad altri.
* Si vuole cambiare l'account usato per accedere ad Azure. È stato usato l'account Microsoft personale, ma si voleva invece usare quello aziendale o dell'istituto di istruzione.
* Si vuole spostare la sottoscrizione di Azure da una directory a un'altra.
* Azure e Office 365 sono in tenant diversi e si vogliono consolidare.

Ora è possibile eseguire facilmente queste operazioni nel Centro account di Microsoft Azure per le sottoscrizioni con pagamento in base al consumo, MSDN, Action Pack o BizSpark.  È stata aggiunta la possibilità di trasferire la sottoscrizione a un altro utente. In altre parole, ora è possibile modificare l'amministratore dell'account in qualsiasi sottoscrizione con pagamento in base al consumo, MSDN, Action Pack o BizSpark di cui si è proprietari, indipendentemente dal paese in cui si opera. Ora il trasferimento degli acquisti di Azure Marketplace è supportato anche per questi tipi di sottoscrizione.

> [!NOTE]
> Per modificare la sottoscrizione passando a un'offerta diversa, vedere [Trasferire la sottoscrizione di Azure a un'altra offerta](billing-how-to-switch-azure-offer.md) . Se in qualsiasi punto dell'articolo sono necessarie altre informazioni, è possibile [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.
> 
> 

## <a name="how-to-transfer-ownership-of-an-azure-subscription"></a>Trasferimento della proprietà di una sottoscrizione di Azure
> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Transfer-an-Azure-subscription/player]
> 
> 

1. Accedere a <https://account.windowsazure.com/Subscriptions>. È necessario essere amministratore account per eseguire un trasferimento di proprietà. Per altre informazioni su come trovare l'amministratore account della sottoscrizione, vedere [Domande frequenti](#faq).
2. Selezionare la sottoscrizione da trasferire.
3. Fare clic sull'opzione **Trasferisci sottoscrizione** .
   
   ![Scheda delle sottoscrizioni dell'account Azure](./media/billing-subscription-transfer/image1.png)
4. Seguire le istruzioni per specificare il destinatario.
   
   ![Finestra di dialogo Trasferisci sottoscrizione](./media/billing-subscription-transfer/image2.PNG)
5. Il destinatario riceverà automaticamente un messaggio di posta elettronica con un collegamento per l'accettazione.
   
   ![Messaggio di posta elettronica di trasferimento della sottoscrizione al destinatario](./media/billing-subscription-transfer/image3.png)
6. Il destinatario dovrà fare clic sul collegamento e seguire le istruzioni, inclusa l'immissione delle informazioni sul pagamento.
   
   ![Prima pagina Web del trasferimento della sottoscrizione](./media/billing-subscription-transfer/image4.PNG)
   
   ![Seconda pagina Web del trasferimento della sottoscrizione](./media/billing-subscription-transfer/image5.PNG)
7. Completamento della procedura La sottoscrizione è stata trasferita.

<a id="faq"></a>

## <a name="frequently-asked-questions-(faq)"></a>Domande frequenti (FAQ)
* **È possibile confermare chi sia l'amministratore account della sottoscrizione?**
  
  Per confermare chi sia l'amministratore account della sottoscrizione, procedere come indicato di seguito:
  
  1. Accedere al [portale di Azure](https://portal.azure.com).
  2. Nel menu Hub, selezionare **Sottoscrizione**.
  3. Scegliere la sottoscrizione da controllare e quindi selezionare **Impostazioni**.
  4. Selezionare **Proprietà**. L'amministratore dell'account della sottoscrizione verrà visualizzato nella casella **Amministratore account** .  
* **Il trasferimento della sottoscrizione comporta tempi di inattività del servizio?**
  
  Il trasferimento non incide in alcun modo sul servizio. Viene annullata la sottoscrizione dell'attuale amministratore dell'account e ne viene creata una nuova per l'account del destinatario, associando però i servizi di Azure sottostanti alla nuova sottoscrizione. L'ID sottoscrizione rimane invariato.
* **Come è possibile usare questo meccanismo per cambiare la directory della sottoscrizione?**-   
  Viene creata una sottoscrizione di Azure nella directory a cui appartiene l'Amministratore account. Per modificare la directory, è quindi sufficiente trasferire la sottoscrizione a un account utente nella directory di destinazione. Quando tale utente avrà completato i passaggi per accettare il trasferimento, la sottoscrizione passerà automaticamente alla directory di destinazione.
* **Se si rileva la proprietà della fatturazione di una sottoscrizione da un'altra organizzazione, quest'ultima continuerà ad avere accesso alle risorse del nuovo proprietario?**
  
  Se la sottoscrizione viene trasferita a un altro tenant, gli utenti associati al tenant precedente perderanno l'accesso alla sottoscrizione. Anche se un utente non è più amministratore del servizio o coamministratore, potrebbe avere ancora accesso alla sottoscrizione tramite altri meccanismi di sicurezza, inclusi i seguenti:
  
  * Certificati di gestione che concedono all'utente privilegi di amministratore per le risorse della sottoscrizione. Per altre informazioni, vedere [Creare e caricare un certificato di gestione per Azure](https://msdn.microsoft.com/library/azure/gg551722.aspx)
  * Chiavi di accesso per servizi quali Archiviazione. Per ulteriori informazioni, vedere [Visualizzare, copiare e rigenerare le chiavi di accesso all’archiviazione](storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)
  * Le credenziali di accesso remote per servizi quali macchine virtuali di Azure
  
  L'elenco non è completo. Il destinatario dovrà prendere in considerazione l'aggiornamento degli eventuali segreti associati al servizio se deve limitare l'accesso alle proprie risorse. La maggior parte delle risorse può essere aggiornata come segue:
  
  1. Passare al portale di Azure all'indirizzo [*https://portal.azure.com*](https://portal.azure.com)
  2. Fare clic su Esplora tutto &gt; Tutte le risorse
  3. Selezionare la risorsa. Viene aperto il pannello della risorsa.
  4. Nel pannello della risorsa fare clic su **Impostazioni**. Qui è possibile visualizzare e aggiornare i segreti esistenti.
* **Se si trasferisce la sottoscrizione nel corso del ciclo di fatturazione, il destinatario dovrà pagare il ciclo intero?**
  
  Il mittente è responsabile del pagamento per qualsiasi utilizzo segnalato fino al momento in cui il trasferimento viene completato. Il destinatario è responsabile per l'utilizzo segnalato dal momento del trasferimento in avanti. È possibile che un utilizzo sia avvenuto prima del trasferimento, ma sia stato segnalato successivamente. In questo caso sarà incluso nella fattura del destinatario.
* **Il destinatario ha accesso alla cronologia di utilizzo e di fatturazione?**
  
  Al momento, le sole informazioni rivelate al destinatario riguardano l'importo dell'ultima fattura oppure il saldo attuale se la sottoscrizione è stata trasferita prima dell'emissione della prima fattura. Il resto della cronologia di fatturazione e utilizzo non viene trasferito con la sottoscrizione.
* **L'offerta può essere modificata durante un trasferimento?**
  
  L'offerta deve rimanere invariata. Per modificare l'offerta, è necessario [contattare il Supporto tecnico](http://go.microsoft.com/fwlink/?LinkID=619338).
* **È possibile trasferire una sottoscrizione a un account utente in un altro paese?**
  
  No, al momento questa opzione non è supportata. L'account utente del destinatario deve essere nello stesso paese.
* **Il destinatario può usare un meccanismo di pagamento diverso?**
  
  Sì. In questo caso esistono limitazioni, nel senso che la cronologia di fatturazione della sottoscrizione viene suddivisa tra i due account, ma si ha il vantaggio di poter eseguire questa operazione senza dover [contattare il Supporto tecnico](http://go.microsoft.com/fwlink/?LinkID=619338).
* **Il metodo di pagamento verrà in qualche modo interessato dal trasferimento di una sottoscrizione di Azure?**
  
  Per accettare un trasferimento di sottoscrizione, è necessario specificare un metodo di pagamento con carta di credito o un metodo simile. Ad esempio, se Bob trasferisce una sottoscrizione a Jane e Jane accetta il trasferimento, anche Jane deve specificare un metodo di pagamento che userà per la sottoscrizione. Al termine del trasferimento, a Bob non verrà più addebitato alcun importo per la sottoscrizione che ha trasferito a Jane.

## <a name="next-steps-after-accepting-ownership-of-a-subscription"></a>Passaggi successivi all'accettazione della proprietà di una sottoscrizione
1. A questo punto si è l'amministratore dell'account. Rivedere e aggiornare l’amministratore del servizio e i co-amministratori. Gestire gli amministratori nel [portale di Azure classico](https://manage.windowsazure.com) accedendo alle impostazioni. [Altre informazioni](http://go.microsoft.com/fwlink/?LinkID=533293)
2. Per la sottoscrizione e i servizi è anche usare il controllo degli accessi in base al ruolo. Visitare il [portale di Azure](https://portal.azure.com) [Ulteriori informazioni su RBAC](http://go.microsoft.com/fwlink/?LinkID=544802)
3. Aggiornare le credenziali associate ai servizi della sottoscrizione, inclusi i seguenti:
   * Certificati di gestione che concedono all'utente privilegi di amministratore per le risorse della sottoscrizione. Per altre informazioni, vedere [Create and upload a management certificate for Azure](https://msdn.microsoft.com/library/azure/gg551722.aspx)
   * Chiavi di accesso per servizi quali Archiviazione. Per ulteriori informazioni, vedere [Visualizzare, copiare e rigenerare le chiavi di accesso all’archiviazione](storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)
   * Le credenziali di accesso remote per servizi quali macchine virtuali di Azure
4. Aggiornare gli avvisi di fatturazione per questa sottoscrizione tramite il [Centro Account di Azure](https://account.windowsazure.com/Subscriptions) [Altre informazioni](http://go.microsoft.com/fwlink/?LinkID=533292)
5. Se si lavora con un partner, è consigliabile aggiornare l'ID partner nella sottoscrizione. È possibile farlo nel [Centro Account di Azure](https://account.windowsazure.com/Subscriptions).

> [!NOTE]
> Per altre domande, è possibile [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.
> 
> 

<!---HONumber=Oct16_HO2-->


