<properties
   pageTitle="Trasferimento di una sottoscrizione di Azure | Microsoft Azure"
	description="Come trasferire una sottoscrizione di Azure a un altro utente e alcune domande frequenti sul processo"
	services="billing"
	documentationCenter=""
	authors="curtand"
	manager="msmStevenPo"
	editor=""/>

<tags
   ms.service="billing"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="billing"
	ms.date="08/19/2015"
	ms.author="curtand;ruchic"/>

# Trasferimento di una sottoscrizione di Azure

Se:

- È necessario trasferire la proprietà della propria sottoscrizione di Azure ad altri.
- Si vuole cambiare l'account usato per accedere ad Azure. È stato usato l'account Microsoft personale, ma si voleva invece usare quello aziendale o dell'istituto di istruzione.
- Si vuole spostare la sottoscrizione di Azure da una directory a un'altra.
- Azure e Office 365 sono in tenant diversi e si vogliono consolidare.

Se l'account è negli Stati Uniti, ora è possibile eseguire facilmente queste operazioni nel Centro account di Microsoft Azure per le sottoscrizioni con pagamento in base al consumo. È stata aggiunta la possibilità di trasferire la sottoscrizione a un altro utente. In altre parole, ora è possibile modificare l'amministratore dell'account in qualsiasi sottoscrizione con pagamento in base al consumo di cui si è proprietari.

## Come trasferire una sottoscrizione di Azure

1.  Accedere a <https://account.windowsazure.com/Subscriptions>

2.  Selezionare la sottoscrizione da trasferire.

3.  Fare clic sull'opzione **Trasferisci sottoscrizione**.

    ![Scheda Sottoscrizioni dell'account Azure](./media/billing-subscription-transfer/image1.png)

4.  Seguire le istruzioni per specificare il destinatario.

    ![Finestra di dialogo Trasferisci sottoscrizione](./media/billing-subscription-transfer/image2.PNG)

5.  Il destinatario riceverà automaticamente un messaggio di posta elettronica con un collegamento per l'accettazione.

    ![Messaggio di posta elettronica di trasferimento della sottoscrizione al destinatario](./media/billing-subscription-transfer/image3.png)

6.  Il destinatario dovrà fare clic sul collegamento e seguire le istruzioni, inclusa l'immissione delle informazioni sul pagamento.

    ![Prima pagina Web Trasferimento della sottoscrizione](./media/billing-subscription-transfer/image4.PNG)

    ![Seconda pagina Web Trasferimento della sottoscrizione](./media/billing-subscription-transfer/image5.PNG)

7. Completamento della procedura La sottoscrizione è ora trasferita.

## Domande frequenti

-   **Il trasferimento della sottoscrizione comporta tempi di inattività del servizio?**

    Il trasferimento non incide in alcun modo sul servizio. In pratica, viene annullata la sottoscrizione dell'attuale amministratore account e ne viene creata una nuova con l'account del destinatario, associando però i servizi di Azure sottostanti alla nuova sottoscrizione. L'ID sottoscrizione rimane invariato.

-   **Se si rileva la proprietà della fatturazione di una sottoscrizione da un'altra organizzazione, quest'ultima continuerà ad avere accesso alle risorse del nuovo proprietario?**

    Se la sottoscrizione viene trasferita a un altro tenant, gli utenti associati al tenant precedente perderanno l'accesso alla sottoscrizione. Anche se un utente non è più amministratore del servizio o coamministratore, potrebbe avere ancora accesso alla sottoscrizione tramite altri meccanismi di sicurezza, ad esempio: - Certificati di gestione che concedono all'utente diritti di amministrazione per le risorse della sottoscrizione. Per altre informazioni, vedere [Creare e caricare un certificato di gestione per Azure](https://msdn.microsoft.com/library/azure/gg551722.aspx) - Chiavi di accesso per servizi come Archiviazione. Per altre informazioni, vedere [Visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys) - Credenziali di accesso remoto per servizi come Macchine virtuali di Azure

    L'elenco non è completo. Il destinatario dovrà prendere in considerazione l'aggiornamento degli eventuali segreti associati al servizio se deve limitare l'accesso alle proprie risorse. La maggior parte delle risorse può essere aggiornata come segue:

    1.   Accedere al portale di Azure: [**https://portal.azure.com*](https://portal.azure.com)

    2.    Fare clic su Esplora tutto -&gt; Tutte le risorse

    3.    Selezionare la risorsa. Viene aperto il pannello della risorsa.

    4.    Nel pannello della risorsa fare clic su **Impostazioni**. Qui è possibile visualizzare e aggiornare i segreti esistenti.


-   **Se si trasferisce la sottoscrizione nel corso del ciclo di fatturazione, il destinatario dovrà pagare per il ciclo intero?**

    Il mittente è responsabile del pagamento per qualsiasi utilizzo segnalato fino al momento in cui il trasferimento viene completato. Il destinatario è responsabile per l'utilizzo segnalato dal momento del trasferimento in avanti. È possibile che un utilizzo sia avvenuto prima del trasferimento, ma sia stato segnalato successivamente. In questo caso sarà incluso nella fattura del destinatario.

-   **Il destinatario ha accesso alla cronologia di utilizzo e di fatturazione?**

    In qualsiasi momento, le sole informazioni rivelate al destinatario riguardano l'importo dell'ultima fattura (o il saldo attuale se la sottoscrizione è stata trasferita prima dell'emissione della prima fattura). Il resto della cronologia di utilizzo e di fatturazione non viene trasferito con la sottoscrizione.

-   **L'offerta può essere modificata durante un trasferimento?**

    L'offerta deve rimanere invariata. Per modificare l'offerta, è necessario [contattare il Supporto tecnico](http://go.microsoft.com/fwlink/?LinkID=619338).

-   **È possibile trasferire una sottoscrizione a un account utente in un altro paese?**

    No, al momento questa opzione non è supportata. L'account utente del destinatario deve essere nello stesso paese.

-   **Il destinatario può usare un meccanismo di pagamento diverso?**

    Sì. In effetti è possibile usare questo meccanismo per modificare il metodo di pagamento della sottoscrizione da fattura a carta di credito. È sufficiente effettuare il trasferimento a un altro account di cui si è proprietari e immettere la carta di credito mentre si riceve la sottoscrizione. In questo caso esistono limitazioni, nel senso che la cronologia di fatturazione della sottoscrizione viene suddivisa tra i due account, ma si ha il vantaggio di poter eseguire questa operazione senza dover [contattare il Supporto tecnico](http://go.microsoft.com/fwlink/?LinkID=619338).

<!---HONumber=September15_HO1-->