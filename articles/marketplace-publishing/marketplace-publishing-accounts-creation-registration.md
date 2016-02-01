<properties
   pageTitle="Processo di creazione e registrazione dell’account di pubblicazione | Microsoft Azure"
   description="Istruzioni per la creazione di un account Microsoft Developer per consentire all'utente, dopo l'approvazione, di vendere diversi tipi di offerte in Azure Marketplace."
   services="Azure Marketplace"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/07/2016"
   ms.author="hascipio"/>

# Creare un account di Microsoft Developer
In questo articolo viene illustrata la creazione e la registrazione dell'account necessario per diventare un Microsoft Developer approvato per Azure Marketplace.

## 1\. Creare un account Microsoft
> [AZURE.WARNING]Per avviare il processo di pubblicazione, è necessario creare un account Microsoft. Questo account verrà usato per registrarsi e per accedere a **Microsoft Developer Center** e al **portale di pubblicazione di Azure**. È consigliabile utilizzare un solo account Microsoft per le offerte in Azure Marketplace. L’account non deve essere specifico per servizi oppure offerte.

L'indirizzo che costituisce il nome utente deve trovarsi nel proprio dominio ed essere controllato dal team IT, ad esempio publishing@example.com). Pagamenti, informazioni fiscali e report verranno indirizzati a questo account.

  >[AZURE.WARNING]Parole come "Azure", "Microsoft" non sono supportate per la registrazione di account Microsoft. Evitare di utilizzare queste parole per completare il processo di registrazione e creazione dell’account.

### Istruzioni

1. Creare un elenco di distribuzione (DL) o un gruppo di sicurezza (SG) all'interno del dominio della società.
  - Aggiungere il proprio team di implementazione al DL
  - Il DL deve essere attivo per la ricezione di posta elettronica di conferma.
  - Usare marketplace@example.com come indirizzo di posta elettronica per la lista di distribuzione.
  - Questa operazione deve essere completata nei sistemi interni.
2. Aprire una nuova sessione in incognito in Chrome o InPrivate Browsing in Internet Explorer per assicurarsi di non essere connessi a un account esistente.
3. Registrare un account Microsoft tramite la posta elettronica della lista di distribuzione (DL).
 - È possibile registrarsi per un account Microsoft all'indirizzo [https://signup.live.com/signup.aspx](https://signup.live.com/signup.aspx).
 - Usare marketplace@example.com come indirizzo di posta elettronica.
 - L'ID dell'account Microsoft è ora marketplace@example.com.
4. Per la registrazione, utilizzare un numero di telefono valido. Il sistema invierà un codice di verifica come SMS o come chiamata automatica se è necessaria la verifica dell'identità.
5. Verificare l'indirizzo di posta elettronica inviato al DL.
6. A questo punto si è pronti per usare il nuovo account Microsoft nel Microsoft Developer Center.

> [AZURE.TIP]L’utilizzo della lista di distribuzione consente a più utenti di ricevere notifiche tramite posta elettronica che sono importanti per la segnalazione di informazioni relative ai proventi. Inoltre, garantisce che la proprietà dell'account Microsoft possa essere trasferita e che non sia legata a un singolo utente.

## 2\. Creare l'account Microsoft Developer Center
Microsoft Developer Center viene usato per registrare le informazioni della società una sola volta. La persona che effettua la registrazione deve essere un rappresentante della società valido e deve fornire le informazioni personali come metodo per convalidare la propria identità. La persona che esegue la registrazione deve usare un account Microsoft condiviso per la società ** e questo account deve essere usato anche nel portale di pubblicazione di Azure**. Assicurarsi che l'azienda non possieda già un account Microsoft Developer Center prima di provare a crearne uno. Durante il processo, Microsoft raccoglierà informazioni sull'indirizzo dell'azienda, sul conto bancario e sui dati fiscali. Tali informazioni possono generalmente essere ottenute da contatti finanziari o aziendali.

> [AZURE.IMPORTANT]I seguenti componenti del profilo sviluppatore devono essere completati per l'avanzamento attraverso le diverse fasi della creazione e della distribuzione dell'offerta.


| Profilo sviluppatore | Per iniziare la bozza | Staging | Pubblica modello gratuito e di soluzione | Pubblica offerta commerciale |
|----|----|----|----|----|
|Registrazione della società | Necessario | Necessario | Necessario | Necessario |
|ID del profilo fiscale | Facoltativo | Facoltativo | Facoltativo | Necessario |
|Conto bancario | Facoltativo | Facoltativo | Facoltativo | Necessario |


> [AZURE.NOTE]Bring Your Own License (BYOL) è supportato solo per le macchine virtuali e viene considerato un’offerta **gratuita**.


### Registrare l'account della società
1. Aprire una nuova sessione in incognito in InPrivate Browsing in Internet Explorer o Chrome per assicurarsi di non essere connessi a un account personale.

2. Passare a [http://dev.windows.com/registration?accountprogram=azure](http://dev.windows.com/registration?accountprogram=azure)

3. Accedere con l'account Microsoft di registrazione dell'azienda (ad esempio, marketplace@example.com).

    ![disegno][img-signin]

4. Completare la "Procedura guidata per la protezione dell'account", che verificherà l'identità tramite il numero di telefono o l'indirizzo di posta elettronica.

    ![disegno][img-verify]

5. Nella sezione "Informazioni sull'Account di registrazione" selezionare **il paese/l'area geografica dell'account** nel menu a discesa.

    > [AZURE.WARNING]**Paesi di origine della vendita:** per vendere i propri servizi in Azure Marketplace, l'entità registrata deve trovarsi in uno dei paesi di origine della vendita approvati. Questa limitazione viene applicata per motivi legati ai proventi e alla tassazione. Questo elenco di paesi verrà ampliato nel prossimo futuro perciò non rimane che attendere. Per altre informazioni, vedere [Politiche di partecipazione a Microsoft Azure Marketplace](http://go.microsoft.com/fwlink/?LinkID=526833).

6. Selezionare "Tipo di account", **utente singolo** o **aziendale**.

    > [AZURE.IMPORTANT]Per comprendere meglio i tipi di account e quale sia più adatto per le proprie esigenze, visitare la pagina [Tipi di account, aree geografiche e tariffe](https://msdn.microsoft.com/library/windows/apps/jj863494.aspx)

7. Immettere il **Nome visualizzato dell'editore**, in genere il nome della società.

    > [AZURE.TIP]Attualmente, il portale di pubblicazione di Azure non usa il nome visualizzato dell'editore. Deve tuttavia essere compilato per completare il processo di registrazione.

8. Immettere le informazioni di contatto per l'account.

    > [AZURE.IMPORTANT]È necessario fornire informazioni accurate, perché saranno usate nel processo di verifica dell'azienda per essere approvata in Developer Center.

9. Per un account aziendale, è anche necessario fornire informazioni di contatto per il **Responsabile approvazione aziendale**, ovvero la persona che può verificare che si è autorizzati a creare l'account per conto dell'organizzazione. Al termine, fare clic su **Avanti** per passare alla **"sezione dei pagamenti"**.

10. Immettere le informazioni per il pagamento dell'account. Se si ha un codice promozionale che copre il costo della registrazione, è possibile immettere qui. In caso contrario, fornire le informazioni della carta di credito o di PayPal nei mercati supportati. Al termine, fare clic su **Avanti** per passare alla **"schermata di verifica"**.

11. Esaminare le informazioni dell'account e verificare che tutto sia corretto. Leggere quindi e accettare le condizioni di **Microsoft Azure Marketplace - Contratto dell'editore**. Selezionare la casella per indicare di aver letto e accettato queste condizioni.

12. Fare clic su **Fine** per confermare la registrazione. Verrà inviato un messaggio di conferma all'indirizzo di posta elettronica dello sviluppatore.

13. Se si prevede di pubblicare offerte gratis, fare clic sul collegamento per **passare al portale di pubblicazione di Azure Marketplace** e passare direttamente alla sezione 3 di questo documento, [Registrare l'account nel portale di pubblicazione](#3-register-your-account-in-the-publishing-portal).

14. Se si prevede di pubblicare offerte commerciali, fare clic sul collegamento per **aggiornare le informazioni sull'account** dove è necessario completare le informazioni fiscali e bancarie nell'account Developer Center.

> [AZURE.IMPORTANT]Non sarà possibile testare correttamente le offerte in Gestione temporanea e non sarà possibile inserire le offerte nella produzione senza completare le informazioni fiscali e del conto bancario.

Se si preferisce aggiornare le informazioni fiscali e bancarie in un secondo momento, è possibile passare alla sezione 3, [Registrare l'account nel portale di pubblicazione](#3-register-your-account-in-the-publishing-portal) e tornare indietro successivamente usando i collegamenti nel portale di pubblicazione di Azure.

### Aggiungere le informazioni fiscali e bancarie
 Per pubblicare offerte commerciali per l'acquisto, è necessario aggiungere anche informazioni sui pagamenti e fiscali e inviarle per la convalida al Developer Center. Se si pubblicheranno solo offerte gratuite (oppure offerte BYOL), non è necessario aggiungere tali informazioni. È possibile aggiungerle in un secondo momento, ma questo richiede del tempo per convalidare le informazioni fiscali. Se si sa che si offriranno offerte commerciali per l'acquisto, è consigliabile aggiungerle non appena possibile.

**Informazioni bancarie**

1. Accedi a [Microsoft Developer Center](http://dev.windows.com/registration?accountprogram=azure) con l'account Microsoft.

2. Fare clic sull'**account per pagamento** nel menu di sinistra, in **Scegli il metodo di pagamento** fare clic su **Conto bancario** o **PayPal**.

    > [AZURE.IMPORTANT]Se si dispone di offerte commerciali che i clienti acquistano nel Marketplace, questo è l'account in cui si riceveranno i pagamenti per tali acquisti.

3. Immettere le informazioni di pagamento e fare clic su **Salva** quando si è soddisfatti.

    > [AZURE.IMPORTANT]Se è necessario aggiornare o modificare l'account di pagamento, seguire la stessa procedura precedente, sostituendo le informazioni attuali con quelle nuove. La modifica dell'account per il pagamento può ritardare i pagamenti per un massimo di un ciclo di pagamento. Questo ritardo si verifica perché è necessario verificare la modifica dell'account, come è stato fatto per la configurazione iniziale dell'account di pagamento. Dopo aver verificato l'account, verrà comunque pagato per l'intero importo. Ii pagamenti in scadenza per il ciclo pagamento corrente saranno aggiunti a quello successivo.

4. Fare clic su **Avanti**.

**Informazioni fiscali**

1. Accedere a [Microsoft Developer Center](http://dev.windows.com/registration?accountprogram=azure) con l'account Microsoft, se necessario.

2. Fare clic su **Profilo fiscale ** nel menu a sinistra.

3. Nella pagina **Configura modulo per la tassazione** selezionare il paese o l'area geografica in cui ha la residenza permanente, quindi selezionare il paese o l'area geografica della cittadinanza principale. Fare clic su **Avanti**.

4. Immettere i dettagli fiscali, quindi fare clic su **Avanti**.

> [AZURE.WARNING]Non sarà possibile eseguire il push in produzione delle offerte commerciali senza completare le informazioni fiscali e del conto bancario nell'account Microsoft Developer Center.

## 3\. Registrare l'account nel portale di pubblicazione
Il portale di pubblicazione di Azure consente di pubblicare e gestire le offerte. Nel portale di pubblicazione sono disponibili informazioni utili che guideranno attraverso il processo di creazione dell’offerta.

> [AZURE.WARNING]Qui è obbligatorio usare lo stesso account Microsoft della società usato per la registrazione in Microsoft Developer Center . È possibile aggiungere altri utenti per l’assistenza una volta creato l'account di editore principale.

1.	Aprire una nuova sessione in incognito in Chrome o InPrivate Browsing in Internet Explorer per assicurarsi di non essere connessi a un account personale.

2.	Visitare la pagina [http://publish.windowsazure.com](http://publish.windowsazure.com).

3.	Accedere con l'account Microsoft di registrazione della società (ad esempio marketplace@example.com)) per aggiungere coamministratori in base alle esigenze.

  >[AZURE.TIP]I criteri di partecipazione sono descritti nel [sito Web di Azure](http://azure.microsoft.com/support/legal/marketplace/participation-policies/).

  > In caso di problemi con il completamento della registrazione in Developer Center, registrare un ticket di supporto come indicato di seguito: 1. Contattare il [supporto tecnico](https://support.microsoft.com/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&supportregion=it-IT&pesid=15635&ccsid=635847950577064286). 2. Scegliere il **Centro per sviluppatori**. 3. Scegliere **Profilo**. 4. Scegliere un metodo di contatto.






## Passaggi successivi
Ora che l'account è stato creato e registrato, fare clic sul tipo di elemento (macchina virtuale, servizio di sviluppatore, servizio dati, modello di soluzione) che si desidera pubblicare in Azure Marketplace. Esaminare uno dei seguenti articoli per scoprire come pubblicare la propria offerta:

|| Immagine di macchina virtuale | Servizio per gli sviluppatori | Servizio dati | Modello di soluzione |
|----|-----|-----|-----|-----|
|**Passaggio 2: Creare l'offerta** |[Prerequisiti non tecnici generali](marketplace-publishing-pre-requisites.md)| [Prerequisiti non tecnici generali](marketplace-publishing-pre-requisites.md)| [Prerequisiti non tecnici generali](marketplace-publishing-pre-requisites.md)| [Prerequisiti non tecnici generali](marketplace-publishing-pre-requisites.md)|
|| [Prerequisiti tecnici per le immagini VM][link-single-vm-prereq] | Prerequisiti tecnici per il servizio per gli sviluppatori | [Prerequisiti tecnici per il servizio dati](marketplace-publishing-data-service-creation-prerequisites.md) | [Prerequisiti tecnici per il modello di soluzione](marketplace-publishing-solution-template-creation-prerequisites.md) |
|| [Guida alla pubblicazione di immagini VM][link-single-vm] | Guida alla pubblicazione del servizio per gli sviluppatori | [Guida alla pubblicazione del servizio dati](marketplace-publishing-data-service-creation.md) | [Guida alla pubblicazione di un modello di soluzione](marketplace-publishing-solution-template-creation.md) |
|| [Guida ai contenuti di marketing di Azure Marketplace][link-pushstaging] | [Guida ai contenuti di marketing di Azure Marketplace][link-pushstaging] | [Guida ai contenuti di marketing di Azure Marketplace][link-pushstaging] | [Guida ai contenuti di marketing di Azure Marketplace][link-pushstaging] |

## Vedere anche
- [Guida introduttiva: Come pubblicare un'offerta in Azure Marketplace](marketplace-publishing-getting-started.md)

[img-msalive]: media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-live.jpg
[img-email]: media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-verifyemail.jpg
[img-sd-url]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-incognito.jpg
[img-signin]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-login.jpg
[img-verify]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-verify.jpg
[img-sd-top]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-details.jpg
[img-sd-info]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal.jpg
[img-sd-type]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-type.jpg
[img-sd-mktg1]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det1.jpg
[img-sd-mktg2]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det2.jpg
[img-sd-addr]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-add.jpg
[img-sd-legal]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-cmp.jpg
[img-sd-submit]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-approval.jpg

[link-msdndoc]: https://msdn.microsoft.com/library/jj552460.aspx
[link-sellerdashboard]: http://sellerdashboard.microsoft.com/
[link-pubportal]: https://publish.windowsazure.com
[link-single-vm]: marketplace-publishing-vm-image-creation.md
[link-single-vm-prereq]: marketplace-publishing-vm-image-creation-prerequisites.md
[link-multi-vm]: marketplace-publishing-solution-template-creation.md
[link-multi-vm-prereq]: marketplace-publishing-solution-template-creation-prerequisites.md
[link-datasvc]: marketplace-publishing-data-service-creation.md
[link-datasvc-prereq]: marketplace-publishing-data-service-creation-prerequisites.md
[link-devsvc]: marketplace-publishing-dev-service-creation.md
[link-devsvc-prereq]: marketplace-publishing-dev-service-creation-prerequisites.md
[link-pushstaging]: marketplace-publishing-push-to-staging.md

<!---HONumber=AcomDC_0121_2016-->