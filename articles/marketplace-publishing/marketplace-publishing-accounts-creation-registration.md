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
   ms.date="08/01/2016"
   ms.author="hascipio"/>

# Creare un account di Microsoft Developer
In questo articolo viene illustrata la creazione e la registrazione dell'account necessario per diventare un Microsoft Developer approvato per Azure Marketplace.

## 1\. Creare un account Microsoft
Per avviare il processo di pubblicazione, è necessario creare un account Microsoft. Questo account verrà usato per registrarsi a **Microsoft Developer Center** e al **portale di pubblicazione di Azure**. È consigliabile utilizzare un solo account Microsoft per le offerte in Azure Marketplace. L’account non deve essere specifico per servizi oppure offerte.

L'indirizzo che costituisce il nome utente deve trovarsi nel proprio dominio ed essere controllato dal team IT. Tutte le attività correlate alla pubblicazione devono essere eseguite tramite questo account.

  >[AZURE.WARNING] Parole come **"Azure"** e **"Microsoft"** non sono supportate per la registrazione di account Microsoft. Evitare di utilizzare queste parole per completare il processo di registrazione e creazione dell’account.

### Istruzioni

1. Creare un elenco di distribuzione (DL) o un gruppo di sicurezza (SG) all'interno del dominio della società. L'uso di un lista di distribuzione consente a più utenti di ricevere notifiche tramite posta elettronica che sono importanti per la segnalazione di informazioni relative ai proventi. Inoltre, garantisce che la proprietà dell'account Microsoft possa essere trasferita e che non sia legata a un singolo utente. Seguire le istruzioni riportate di seguito.

    1. Aggiungere il proprio team di implementazione al DL
    2. Assicurarsi che la lista di distribuzione o il gruppo di archiviazione sia un indirizzo di posta elettronica attivo e sia in grado di ricevere messaggi di posta elettronica in quanto le informazioni sui pagamenti, le informazioni fiscali e le segnalazioni saranno indirizzati tramite questo account.
    3. Si consiglia di usare qualcosa di simile a marketplace@partnercompany.com come indirizzo di posta elettronica per la lista di distribuzione o il gruppo di archiviazione.

2. Aprire una nuova sessione in incognito in Chrome o InPrivate Browsing in Internet Explorer per assicurarsi di non essere connessi a un account esistente.
3. Registrare la lista di distribuzione creata nel passaggio 1 come account Microsoft usando il collegamento [https://signup.live.com/signup.aspx](https://signup.live.com/signup.aspx). Seguire le istruzioni riportate di seguito.

    1. Durante la registrazione dell'account come account Microsoft, è necessario fornire un numero di telefono valido che il sistema userà per inviare un codice di verifica account mediante un SMS o una chiamata automatica.
    2. Durante la registrazione dell'account come account Microsoft è necessario fornire un ID di posta elettronica valido per poter ricevere un messaggio e-mail automatico per la verifica dell'account.

4. Verificare l'indirizzo di posta elettronica inviato al DL.
5. A questo punto si è pronti per usare il nuovo account Microsoft nel Microsoft Developer Center.

## 2\. Creare l'account Microsoft Developer Center
Microsoft Developer Center viene usato per registrare le informazioni della società una sola volta. La persona che effettua la registrazione deve essere un rappresentante della società valido e deve fornire le informazioni personali come metodo per convalidare la propria identità. La persona che esegue la registrazione deve usare un account Microsoft condiviso per la società ** e questo account deve essere usato anche nel portale di pubblicazione di Azure**. Assicurarsi che l'azienda non possieda già un account Microsoft Developer Center prima di provare a crearne uno. Durante il processo, Microsoft raccoglierà informazioni sull'indirizzo dell'azienda, sul conto bancario e sui dati fiscali. Tali informazioni possono generalmente essere ottenute da contatti finanziari o aziendali.

> [AZURE.IMPORTANT] I seguenti componenti del profilo sviluppatore devono essere completati per l'avanzamento attraverso le diverse fasi della creazione e della distribuzione dell'offerta.


| Profilo sviluppatore | Per iniziare la bozza | Staging | Pubblica modello gratuito e di soluzione | Pubblica offerta commerciale |
|----|----|----|----|----|
|Registrazione della società | Necessario | Necessario | Necessario | Necessario |
|ID del profilo fiscale | Facoltativo | Facoltativo | Facoltativo | Necessario |
|Conto bancario | Facoltativo | Facoltativo | Facoltativo | Necessario |


> [AZURE.NOTE] Bring Your Own License (BYOL) è supportato solo per le macchine virtuali e viene considerato un’offerta **gratuita**.


### Registrare l'account della società
1. Aprire una nuova sessione in incognito in InPrivate Browsing in Internet Explorer o Chrome per assicurarsi di non essere connessi a un account personale.

2. Passare a [http://dev.windows.com/registration?accountprogram=azure](http://dev.windows.com/registration?accountprogram=azure) per registrarsi come venditore in Dev Center. Prima di procedere, leggere la seguente nota importante.

    >[AZURE.IMPORTANT] Assicurarsi che l'ID di posta elettronica o la lista di distribuzione (si consiglia l'uso di una lista di distribuzione per eliminare la dipendenza da una singola persona) che sarà usata per la registrazione in Dev Center sia prima registrata come un account Microsoft. In caso contrario, registrarsi mediante questo [collegamento](https://signup.live.com/signup?uaid=e479342fe2824efeb0c3d92c8f961fd3&lic=1). Inoltre, per la registrazione in Dev Center, **non è consentito usare nessun ID di posta elettronica appartenente al dominio dell'azienda Microsoft, ovvero @microsoft.com**.

    ![disegno][img-signin]

3. Completare la "Procedura guidata per la protezione dell'account", che verificherà l'identità tramite il numero di telefono o l'indirizzo di posta elettronica.

    ![disegno][img-verify]

4. Nella sezione "Informazioni sull'Account di registrazione" selezionare **il paese/l'area geografica dell'account** nel menu a discesa.

    ![disegno](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_04.png)

    > [AZURE.WARNING] **Paesi di origine della vendita:** per vendere i propri servizi in Azure Marketplace, l'entità registrata deve trovarsi in uno dei paesi di origine della vendita approvati. Questa limitazione viene applicata per motivi legati ai proventi e alla tassazione. Questo elenco di paesi verrà ampliato nel prossimo futuro perciò non rimane che attendere. Per altre informazioni, vedere [Politiche di partecipazione a Microsoft Azure Marketplace](http://go.microsoft.com/fwlink/?LinkID=526833).

5. Selezionare **Azienda** per "Tipo di account" e fare clic sul pulsante **Avanti**.

    > [AZURE.IMPORTANT] Per comprendere meglio i tipi di account e quale sia più adatto per le proprie esigenze, visitare la pagina [Tipi di account, aree geografiche e tariffe](https://msdn.microsoft.com/library/windows/apps/jj863494.aspx)

    ![disegno](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_05.png)

6. Immettere il **Nome visualizzato dell'editore**, in genere il nome della società.

    > [AZURE.TIP] Il nome visualizzato dell'autore immesso in Dev Center non sarà mostrato in Azure Marketplace quando l'offerta sarà elencata. Deve tuttavia essere compilato per completare il processo di registrazione.

7. Immettere le **informazioni di contatto** per la verifica dell'account.

    > [AZURE.IMPORTANT] È necessario fornire informazioni accurate, perché saranno usate nel processo di verifica dell'azienda per essere approvata in Developer Center.

8. Immettere le informazioni di contatto per il **responsabile approvazione aziendale**. Il responsabile approvazione aziendale è la persona che può verificare che si è autorizzati a creare un account in Dev Center per conto dell'organizzazione. Al termine, fare clic su **Avanti** per passare alla **"sezione dei pagamenti"**.

    ![disegno](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_08.png)

9. Immettere le informazioni per il pagamento dell'account. Se si ha un codice promozionale che copre il costo della registrazione, è possibile immettere qui. In caso contrario, fornire le informazioni della carta di credito o di PayPal nei mercati supportati. Al termine, fare clic su **Avanti** per passare alla **"schermata di verifica"**.

    ![disegno](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_09.png)

10. Esaminare le informazioni dell'account e verificare che tutto sia corretto. Leggere quindi e accettare le condizioni di [Microsoft Azure Marketplace - Contratto dell'editore](http://go.microsoft.com/fwlink/?LinkID=699560). Selezionare la casella per indicare di aver letto e accettato queste condizioni.

11. Fare clic su **Fine** per confermare la registrazione. Verrà inviato un messaggio di conferma all'indirizzo di posta elettronica.

12. Se si prevede di pubblicare offerte gratis, fare clic sul collegamento per **passare al portale di pubblicazione di Azure Marketplace** e passare direttamente alla sezione 3 di questo documento, [Registrare l'account nel portale di pubblicazione](#3-register-your-account-in-the-publishing-portal).

Se si prevede di pubblicare offerte commerciali (ad esempio offerte per macchina virtuale con un modello di fatturazione oraria), fare clic sul collegamento per **aggiornare le informazioni sull'account** dove è necessario completare le informazioni fiscali e bancarie nell'account Developer Center.

Se si preferisce aggiornare le informazioni fiscali e bancarie in un secondo momento, è possibile passare alla sezione successiva, ovvero la sezione 3 di questo documento, [Registrare l'account nel portale di pubblicazione](#3-register-your-account-in-the-publishing-portal) e tornare indietro successivamente usando i collegamenti nel portale di pubblicazione di Azure.

> [AZURE.IMPORTANT] In caso di offerte commerciali, non sarà possibile passare le offerte in produzione senza completare le informazioni fiscali e del conto bancario.

Se si preferisce aggiornare le informazioni fiscali e bancarie in un secondo momento, è possibile passare alla sezione 3, [Registrare l'account nel portale di pubblicazione](#3-register-your-account-in-the-publishing-portal) e tornare indietro successivamente usando i collegamenti nel portale di pubblicazione di Azure.

### Aggiungere le informazioni fiscali e bancarie
 Per pubblicare offerte commerciali per l'acquisto, è necessario aggiungere anche informazioni sui pagamenti e fiscali e inviarle per la convalida al Developer Center. Se si pubblicheranno solo offerte gratuite (oppure offerte BYOL), non è necessario aggiungere tali informazioni. È possibile aggiungerle in un secondo momento, ma questo richiede del tempo per convalidare le informazioni fiscali. Se si sa che si offriranno offerte commerciali per l'acquisto, è consigliabile aggiungerle non appena possibile.

**Informazioni bancarie**

1. Accedi a [Microsoft Developer Center](http://dev.windows.com/registration?accountprogram=azure) con l'account Microsoft.

2. Fare clic sull'**account per pagamento** nel menu di sinistra, in **Scegli il metodo di pagamento** fare clic su **Conto bancario** o **PayPal**.

    > [AZURE.IMPORTANT] Se si dispone di offerte commerciali che i clienti acquistano nel Marketplace, questo è l'account in cui si riceveranno i pagamenti per tali acquisti.

3. Immettere le informazioni di pagamento e fare clic su **Salva** quando si è soddisfatti.

    > [AZURE.IMPORTANT] Se è necessario aggiornare o modificare l'account di pagamento, seguire la stessa procedura precedente, sostituendo le informazioni attuali con quelle nuove. La modifica dell'account per il pagamento può ritardare i pagamenti per un massimo di un ciclo di pagamento. Questo ritardo si verifica perché è necessario verificare la modifica dell'account, come è stato fatto per la configurazione iniziale dell'account di pagamento. Dopo aver verificato l'account, verrà comunque pagato per l'intero importo. Ii pagamenti in scadenza per il ciclo pagamento corrente saranno aggiunti a quello successivo.

4. Fare clic su **Avanti**.

**Informazioni fiscali**

1. Accedere a [Microsoft Developer Center](http://dev.windows.com/registration?accountprogram=azure) con l'account Microsoft, se necessario.

2. Fare clic su **Profilo fiscale ** nel menu a sinistra.

3. Nella pagina **Configura modulo per la tassazione** selezionare il paese o l'area geografica in cui ha la residenza permanente, quindi selezionare il paese o l'area geografica della cittadinanza principale. Fare clic su **Avanti**.

4. Immettere i dettagli fiscali, quindi fare clic su **Avanti**.

> [AZURE.WARNING] Non sarà possibile eseguire il push in produzione delle offerte commerciali senza completare le informazioni fiscali e del conto bancario nell'account Microsoft Developer Center.

In caso di problemi con la registrazione in Developer Center, inoltrare un ticket di supporto come indicato di seguito

1. Passare al collegamento del supporto [https://developer.microsoft.com/windows/support](https://developer.microsoft.com/windows/support)
2. Nella sezione **Contattaci** fare clic sul pulsante **Invia una richiesta** (come illustrato nella schermata riportata di seguito)

    ![disegno](media/marketplace-publishing-accounts-creation-registration/imgAddTax_02.png)

3. Scegliere "Aiuto con Dev Center" come **Tipo di problema** e "Pubblicare e gestire le app" come **Categoria**. Quindi fare clic sul pulsante "Start email" ("Inizia messaggio di posta elettronica").

    ![disegno](media/marketplace-publishing-accounts-creation-registration/imgAddTax_03.png)

4. Viene visualizzata una pagina di accesso. Usare qualsiasi account Microsoft per effettuare l'accesso. Se non si dispone di un account Microsoft, crearne una seguendo questo [collegamento](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
5. Inserire i dettagli del problema e inviare il ticket facendo clic sul pulsante **Invia**.

    ![disegno](media/marketplace-publishing-accounts-creation-registration/imgAddTax_05.png)

## 3\. Registrare l'account nel portale di pubblicazione
Il [portale di pubblicazione](http://publish.windowsazure.com) consente di pubblicare e gestire le offerte.

1. Aprire una nuova sessione in incognito in Chrome o InPrivate Browsing in Internet Explorer per assicurarsi di non essere connessi a un account personale.

2. Visitare la pagina [http://publish.windowsazure.com](http://publish.windowsazure.com).

3. Un nuovo utente che esegue l'accesso al portale di pubblicazione per la prima volta, deve accedere con lo stesso ID di posta elettronica con cui è registrato il suo account Dev Center. In questo modo l'account Dev Center e l'account del portale di pubblicazione saranno collegati. È possibile aggiungere in un secondo momento altri membri dell'azienda che lavorano all'applicazione, ad esempio un coamministratore, nel portale di pubblicazione seguendo la procedura seguente.

Se si viene aggiunti come coamministratore nel portale di pubblicazione, è possibile accedere con l'account di coamministratore.

  > [AZURE.TIP] I criteri di partecipazione sono descritti nel [sito Web di Azure](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).

## Passaggi per aggiungere un coamministratore nel portale di pubblicazione
**Come amministratore**, di seguito sono riportati i passaggi per aggiungere un coamministratore.

>[AZURE.NOTE] **Per i nuovi utenti**, prima di aggiungere un coamministratore nel portale di pubblicazione, assicurarsi di avere creato almeno un'applicazione nel portale di pubblicazione. Questa operazione è necessaria in quanto la scheda **AUTORI** viene visualizzata solo dopo avere creato almeno un'applicazione nel portale di pubblicazione.

1. Assicurarsi che l'ID di posta elettronica del coamministratore sia un account Microsoft (MSA). In caso contrario, registrarlo come MSA mediante questo [collegamento](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
2. Verificare che sia presente almeno un'applicazione nell'account amministratore prima di tentare di aggiungere un coamministratore.
3. Dopo aver completato i passaggi precedenti, eseguire l'accesso al portale di pubblicazione con l'ID di posta elettronica di coamministratore e quindi eseguire la disconnessione.
4. Ora accedere al portale di pubblicazione con l'ID di posta elettronica di amministratore.
5. Passare ad Autore -> selezionare l'account-> Amministratori -> Aggiungi il coamministratore (la schermata è riportata di seguito)

  ![disegno](media/marketplace-publishing-accounts-creation-registration/imgAddAdmin_05.png)

## Passaggi successivi
Dopo avere creato e registrato l'account, assicurarsi di completare o rispettare tutti i prerequisiti non tecnici per pubblicare l'offerta verificando i [prerequisiti non tecnici](marketplace-publishing-pre-requisites.md).

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

<!---HONumber=AcomDC_0803_2016-->