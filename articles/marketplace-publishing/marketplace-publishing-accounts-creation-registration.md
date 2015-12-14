<properties
   pageTitle="Processo di creazione e registrazione dell’account di pubblicazione | Microsoft Azure"
   description="Sono disponibili istruzioni per la creazione di un account venditore Microsoft in modo che, dopo l'approvazione, un utente possa vendere diversi tipi di offerte in Azure Marketplace."
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
   ms.date="11/17/2015"
   ms.author="hascipio"/>

# Creazione di un account del venditore Microsoft
In questo articolo viene illustrata la creazione e la registrazione dell’account necessario per diventare un venditore Microsoft approvato per Azure Marketplace.

## 1\. Creare un account Microsoft
> [AZURE.WARNING]Per completare il processo di pubblicazione, è necessario creare un account Microsoft. Questo account verrà utilizzato per la registrazione e l’accesso sia al portale di pubblicazione, sia al Dashboard venditori. È consigliabile utilizzare un solo account Microsoft per le offerte in Azure Marketplace. Essi non devono essere specifici per servizi oppure offerte.

L'indirizzo che costituisce il nome utente deve trovarsi nel dominio dell'editore e deve essere controllato dal team IT, ad esempio publishing@example.com). Pagamenti, informazioni fiscali e report verranno indirizzati a questo account.

  >[AZURE.WARNING]Parole come "Azure", "Microsoft" non sono supportate per la registrazione di account Microsoft. Evitare di utilizzare queste parole per completare il processo di registrazione e creazione dell’account.

1. Creare un elenco di distribuzione (DL) o un gruppo di sicurezza (SG) all'interno del dominio della società.
  - Aggiungere il proprio team di implementazione al DL
  - Il DL deve essere attivo per la ricezione di posta elettronica di conferma.
  - Utilizzare marketplace@example.com come indirizzo di posta elettronica per il DL.
  - Questa operazione deve essere completata nei sistemi interni.
2. Aprire una nuova sessione in incognito in Chrome o InPrivate Browsing in Internet Explorer per assicurarsi di non essere connessi a un account esistente.
3. Registrare un account Microsoft tramite la posta elettronica della lista di distribuzione (DL).
 - È possibile effettuare la registrazione ad un account Microsoft all'indirizzo [https://signup.live.com/signup.aspx](https://signup.live.com/signup.aspx).
 - Utilizzare marketplace@example.com come indirizzo di posta elettronica.
 - L’ID dell'account Microsoft è ora marketplace@example.com.

    <!--[drawing][img-msalive]-->

4. Per la registrazione, utilizzare un numero di telefono valido. Il sistema invierà un codice di verifica come SMS o come chiamata automatica se è necessaria la verifica dell'identità.
5. Verificare l'indirizzo di posta elettronica inviato al DL.

    <!--[drawing][img-email]-->

6. A questo punto si è pronti per utilizzare il nuovo account Microsoft nel Dashboard venditori.

> [AZURE.IMPORTANT]L’utilizzo della lista di distribuzione consente a più utenti di ricevere notifiche tramite posta elettronica che sono importanti per la segnalazione di informazioni relative ai proventi. Inoltre, garantisce che la proprietà dell'account Microsoft possa essere trasferita e che non sia legata a un singolo utente.

## 2\. Creare un account per il Dashboard venditori
Il Dashboard venditori Microsoft viene utilizzato per registrare le informazioni della società una sola volta. La persona che effettua la registrazione deve essere un rappresentante della società valido e deve fornire le informazioni personali come metodo per convalidare la propria identità. La persona che esegue la registrazione deve utilizzare un account Microsoft condiviso per la società e questo account deve essere utilizzato nel portale di pubblicazione di Azure. Assicurarsi che l'azienda non possieda già un account per il Dashboard venditori prima di tentare di crearne uno. Durante il processo, Microsoft raccoglierà le informazioni sul conto bancario, i dati fiscali e le informazioni sull'indirizzo dell'azienda. Tali informazioni possono generalmente essere ottenute da contatti finanziari o aziendali.

> [AZURE.IMPORTANT]I seguenti componenti del profilo del venditore devono essere completati per l'avanzamento attraverso le diverse fasi della creazione e della distribuzione dell’offerta.


| Profilo del venditore | Per iniziare la bozza | Staging | Pubblica modello gratuito e di soluzione | Pubblica offerta commerciale |
|----|----|----|----|----|
|Registrazione della società | Necessario | Necessario | Necessario | Necessario |
|ID del profilo fiscale | Facoltativo | Facoltativo | Facoltativo | Necessario |
|Conto bancario | Facoltativo | Facoltativo | Facoltativo | Necessario |


> [AZURE.NOTE]Bring Your Own License (BYOL) è supportato solo per le macchine virtuali e viene considerato un’offerta **gratuita**.


### Registrare l'account della società
1. Aprire una nuova sessione in incognito in InPrivate Browsing in Internet Explorer o Chrome per assicurarsi di non essere connessi a un account personale.

2. Visitare [http://sellerdashboard.microsoft.com](http://sellerdashboard.microsoft.com).

    ![disegno][img-sd-url]

3. Accedere con l'account Microsoft di registrazione dell'azienda (ad esempio, marketplace@example.com).

    ![disegno][img-signin]

4. Completare la "Procedura guidata per la protezione dell'account", che verificherà l'identità tramite il numero di telefono o l'indirizzo di posta elettronica.

    ![disegno][img-verify]

5. Passare a **Dettagli account**. In questa schermata sarà necessario immettere le informazioni personali, che verranno utilizzate esclusivamente per la verifica dell'identità. Si tratta di nome e cognome, indirizzo di posta elettronica, indirizzo di residenza e numero di telefono personale. Queste informazioni non verranno condivise al di fuori di Microsoft.

    ![disegno][img-sd-top]

    <!--[drawing][img-sd-info]-->

6. Registrarsi per conto della società, definendo il tipo di account come **Società**, non **Individuale**. Fare clic su **Avanti**.

    ![disegno][img-sd-type]

7. Inserire i dettagli della società. Queste informazioni devono essere accurate: sede centrale e referente verranno tutti controllati da un team di Microsoft.

    ![disegno][img-sd-mktg1]

8. Il nome della società viene utilizzato dal portale di pubblicazione, pertanto dovrebbe essere accurato.

    <!--[drawing][img-sd-mktg2]-->

9. Utilizzare l'indirizzo della sede centrale della società.

    ![disegno][img-sd-addr]

10. Utilizzare un referente che sia accessibile e riconoscibile come un rappresentante della società.

    ![disegno][img-sd-legal]

11. Fare clic su **Invia per approvazione** e l’operazione è terminata.

    ![disegno][img-sd-submit]


Se si prevede di pubblicare solo offerte gratuite, è possibile passare alla sezione 3, “Registrare l'account nel portale di pubblicazione”.

Se si prevede di pubblicare offerte commerciali, è necessario completare le informazioni fiscali e bancarie nell'account del Dashboard venditori.

> [AZURE.IMPORTANT]Non sarà possibile testare correttamente le offerte in Gestione temporanea e non sarà possibile inserire le offerte nella produzione senza completare le informazioni fiscali e del conto bancario.

Se si preferisce aggiornare le informazioni fiscali e bancarie in un secondo momento, è possibile passare alla sezione 3, "Registrare l'account nel portale di pubblicazione" e tornare indietro successivamente tramite l'utilizzo di collegamenti nel portale di pubblicazione.

### Aggiungere le informazioni fiscali e bancarie
 Se si desidera pubblicare offerte commerciali per l'acquisto, è necessario aggiungere anche informazioni sui pagamenti e fiscali e inviarle per la convalida nel Dashboard venditori. Se si pubblicheranno solo offerte gratuite (oppure offerte BYOL), non è necessario aggiungere tali informazioni. È possibile aggiungerle in un secondo momento, ma questo richiede del tempo per convalidare le informazioni fiscali. Se si sa che si offriranno offerte commerciali per l'acquisto, è consigliabile aggiungerle non appena possibile.

1. Accedere al [Dashboard venditori](http://sellerdashboard.microsoft.com) con il proprio account Microsoft.

2. Scegliere la scheda **ACCOUNT**, quindi fare clic su **pagamento e dati fiscali**.

3. Fare clic su **AGGIUNGERE INFORMAZIONI DI PAGAMENTO E DATI FISCALI**.

4. Nella pagina **Scegliere un metodo di pagamento**, in **Nuovo metodo di pagamento** fare clic su **Conto bancario** o **PayPal**.

> [AZURE.IMPORTANT]Se si dispone di offerte commerciali che i clienti acquistano nel Marketplace, questo è l'account in cui si riceveranno i pagamenti per tali acquisti.

5. Immettere i dettagli di un conto bancario o di un account PayPal.

6. Fare clic su **AVANTI**.

7. Nella pagina **Informazioni fiscali**, selezionare il paese o l’area in cui si dispone della residenza permanente, quindi selezionare il paese o la regione in cui dispone della cittadinanza primaria. Fare clic su **AVANTI**.

8. Immettere i propri dettagli fiscali, quindi fare clic su **AVANTI**.

9. Fare clic su **Submit**. Se non si è ancora pronti a inviare le informazioni fiscali per la convalida, è possibile fare clic su **Salva** o su **Salva e chiudi**. È necessario del tempo per convalidare le informazioni fiscali, pertanto si consiglia di inviarle per la convalida non appena possibile.

> [AZURE.WARNING]Non sarà possibile eseguire il push nella produzione delle offerte commerciali senza completare le informazioni fiscali e del conto bancario nell'account del Dashboard venditori.

## 3\. Registrare l'account nel portale di pubblicazione
Il portale di pubblicazione di Azure consente di pubblicare e gestire le offerte. Nel portale di pubblicazione sono disponibili informazioni utili che guideranno attraverso il processo di creazione dell’offerta.

> [AZURE.WARNING]Qui è obbligatorio utilizzare lo stesso account Microsoft della società utilizzato nella registrazione nel Dashboard venditori. È possibile aggiungere altri utenti per l’assistenza una volta creato l'account di editore principale.

1.	Aprire una nuova sessione in incognito in Chrome o InPrivate Browsing in Internet Explorer per assicurarsi di non essere connessi a un account personale.
2.	Visitare [http://publish.windowsazure.com](http://publish.windowsazure.com).
3.	 Accedere con l’account Microsoft di registrazione della società (ad esempio marketplace@example.com)) e sarà possibile aggiungere coamministratori in base alle esigenze.
4.	Leggere e accettare le condizioni del contratto dell’editore (primo accesso al portale di pubblicazione) e l’operazione è terminata.

  >[AZURE.TIP]Vengono descritti i criteri di partecipazione nel [sito Web di Azure](http://azure.microsoft.com/support/legal/marketplace/participation-policies/).

  > In caso di problemi con il completamento della registrazione del venditore, registrare un ticket di supporto come indicato di seguito: 1. Contattare il [Supporto tecnico](http://go.microsoft.com/fwlink?LinkId=272975). 2. Scegliere la **registrazione nel Dashboard venditori e il proprio account**. 3. Scegliere **Registrazione per un account sviluppatore**. 4. Scegliere un metodo di contatto.

### Paesi di origine della vendita

> [AZURE.WARNING]Per vendere i propri servizi in Microsoft Azure Marketplace, l'entità registrata deve trovarsi in uno dei paesi di origine della vendita approvati. Questa limitazione viene applicata per motivi legati ai proventi e alla tassazione. Questo elenco di paesi verrà ampliato nel prossimo futuro perciò non rimane che attendere. Per l'elenco completo, vedere la sezione 1b delle [politiche di partecipazione a Marketplace](http://go.microsoft.com/fwlink/?LinkID=526833).




## Passaggi successivi
Ora che l'account è stato creato e registrato, fare clic sul tipo di elemento (macchina virtuale, servizio di sviluppatore, servizio dati, modello di soluzione) che si desidera pubblicare in Azure Marketplace. Esaminare uno dei seguenti articoli per scoprire come pubblicare la propria offerta:

|| Immagine di macchina virtuale| Servizio sviluppatore | Servizio dati | Modello per la soluzione | |----|-----|-----|-----|-----| |**Passaggio 2: Creare la propria offerta** |[Prerequisiti non tecnici generali](marketplace-publishing-pre-requisites.md)| [Prerequisiti non tecnici generali](marketplace-publishing-pre-requisites.md)| [Prerequisiti non tecnici generali](marketplace-publishing-pre-requisites.md)| [Prerequisiti non tecnici generali](marketplace-publishing-pre-requisites.md)| || [Prerequisiti tecnici per una VM][link-single-vm-prereq] | Prerequisiti tecnici per un servizio per sviluppatori | [Prerequisiti tecnici per un servizio dati](marketplace-publishing-data-service-creation-prerequisites.md) | [Prerequisiti tecnici per un modello di soluzione](marketplace-publishing-solution-template-creation-prerequisites.md) | || [Guida alla pubblicazione di un'immagine di VM][link-single-vm] | Guida alla pubblicazione di un servizio per sviluppatori | [Guida alla pubblicazione di un servizio dati](marketplace-publishing-data-service-creation.md) | [Guida alla pubblicazione di un modello di soluzione](marketplace-publishing-solution-template-creation.md) | || [Guida ai contenuti marketing in Azure Marketplace][link-pushstaging] | [Guida ai contenuti marketing in Azure Marketplace][link-pushstaging] | [Guida ai contenuti marketing in Azure Marketplace][link-pushstaging] | [Guida ai contenuti marketing in Azure Marketplace][link-pushstaging] |

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

[link-msdndoc]: https://msdn.microsoft.com/it-IT/library/jj552460.aspx
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

<!---HONumber=AcomDC_1203_2015-->