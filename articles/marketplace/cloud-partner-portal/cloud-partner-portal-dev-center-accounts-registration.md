---
title: Come creare un account Microsoft Developer per la pubblicazione in Azure Marketplace | Microsoft Docs
description: Questo articolo illustra i passaggi per creare un account Microsoft Developer per Azure Marketplace e per la pubblicazione.
services: cloud-partner-portal
documentationcenter: 
author: rupeshazure
manager: hamidm
ms.service: cloud-partner-portal
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2017
ms.author: rupesk@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: edad5a8a2acb2767dc59b51429ae3fc581f74947
ms.lasthandoff: 04/18/2017


---
# <a name="create-a-microsoft-developer-account"></a>Creare un account di Microsoft Developer
Questo articolo illustra i passaggi necessari per la creazione dell'account e la registrazione per diventare uno sviluppatore Microsoft approvato per la pubblicazione in Azure Marketplace.

## <a name="1-create-a-microsoft-account"></a>1. Creare un account Microsoft
Per avviare il processo di pubblicazione, è necessario completare la registrazione a **Microsoft Developer Center**. Si userà lo stesso account registrato nel  **[portale Cloud Partner](https://cloudpartner.azure.com/)**  per avviare il processo di pubblicazione. È consigliabile utilizzare un solo account Microsoft per le offerte in Azure Marketplace. L’account non deve essere specifico per servizi oppure offerte.

L'indirizzo che costituisce il nome utente deve trovarsi nel proprio dominio ed essere controllato dal team IT. Tutte le attività correlate alla pubblicazione devono essere eseguite tramite questo account.

> [!WARNING]
> Parole come **"Azure"** e **"Microsoft"** non sono supportate per la registrazione di account Microsoft. Evitare di utilizzare queste parole per completare il processo di registrazione e creazione dell’account.
>
>

### <a name="guidelines-for-company-accounts"></a>Linee guida per gli account aziendali
Quando si crea un account aziendale, seguire queste linee guida se più di una persona dovrà accedere all'account tramite la registrazione con l'account di Microsoft che ha aperto l'account.

> [!Important]
> Importante: per consentire a più utenti di accedere all'account Dev Center, è consigliabile utilizzare Azure Active Directory per assegnare i ruoli ai singoli utenti, che possono accedere all'account accedendo con le proprie credenziali di Azure AD. Per maggiori informazioni, vedere [Gestire gli utenti dell'account](https://msdn.microsoft.com/windows/uwp/publish/manage-account-users).

* Creare l'account di Microsoft usando un indirizzo di posta elettronica appartenente al dominio dell'azienda, ma non a un singolo utente, ad esempio, windowsapps@fabrikam.com.
* Limitare l'accesso a questo account Microsoft al minor numero possibile di sviluppatori.
* Impostare una lista di distribuzione di posta elettronica aziendale che includa tutti gli utenti che hanno necessità di accedere all'account per sviluppatori e aggiungere questo indirizzo email alle informazioni di sicurezza. In questo modo tutti i dipendenti nella lista riceveranno i codici di sicurezza quando necessario e potranno gestire le informazioni di sicurezza dell'account Microsoft. Se non è possibile impostare una lista di distribuzione, il proprietario del singolo account di posta elettronica dovrà accedere e condividere il codice di sicurezza quando richiesto (ad esempio quando viene aggiunta una nuova informazione di sicurezza all'account o quando si deve eseguire l'accesso da un nuovo dispositivo).
* Aggiungere un numero di telefono aziendale che non richieda un'estensione e che sia accessibile ai membri principali del team.
* In generale, gli sviluppatori devono usare dispositivi affidabili per accedere all'account per gli sviluppatori dell'azienda. Tutti i principali membri del team devono avere accesso a questi dispositivi affidabili. Questo riduce la necessità di inviare i codici di sicurezza quando si accede all'account.
* Se si desidera consentire l'accesso all'account a un computer non affidabile, limitare tale accesso a un massimo di cinque sviluppatori. In teoria, questi sviluppatori devono accedere all'account da computer che condividono la stessa area geografica e lo stesso percorso di rete.
* Verificare spesso le informazioni di sicurezza dell'azienda all'indirizzo [https://account.live.com/proofs/Manage](https://account.live.com/proofs/Manage) per accertarsi che siano aggiornate.

L'accesso all'account sviluppatore deve avvenire principalmente da PC affidabili. Questo è fondamentale poiché è previsto un limite al numero di codici generati per ogni account, ogni settimana. Consente inoltre di eseguire un accesso il più trasparente possibile.

Per maggiori informazioni sulla sicurezza e le linee guida dell'account per sviluppatori, fare clic [qui](https://msdn.microsoft.com/windows/uwp/publish/opening-a-developer-account#additional-guidelines-for-company-accounts).

### <a name="instructions"></a>Istruzioni
1. Aprire una nuova sessione in incognito in Chrome o InPrivate Browsing in Internet Explorer per assicurarsi di non essere connessi a un account esistente.
2. Registrare l'indirizzo di posta elettronica (seguendo le linee guida indicate in precedenza, ad esempio windowsapp@fabrikam.com) come account Microsoft usando il collegamento [https://signup.live.com/signup.aspx](https://signup.live.com/signup.aspx). Seguire le istruzioni riportate di seguito.

       A. During registering your account as a Microsoft account, you need to provide a valid phone number for the system to send you an account verification code as a text message or an automated call.

       B. During registering your account as a Microsoft account, you need to provide a valid email id for receiving an automated email for account verification.

    C. Verificare l'indirizzo di posta elettronica inviato al DL.

    D. A questo punto si è pronti per usare il nuovo account Microsoft nel Microsoft Developer Center.

## <a name="2-register-your-account-in-microsoft-developer-center"></a>2. Registrare l'account nel centro per sviluppatori di Microsoft

Microsoft Developer Center viene usato per registrare le informazioni della società una sola volta. La persona che effettua la registrazione deve essere un rappresentante della società valido e deve fornire le informazioni personali come metodo per convalidare la propria identità. La persona che esegue la registrazione deve usare un account Microsoft condiviso per l'azienda, **che deve essere usato anche nel portale Cloud Partner**. Assicurarsi che l'azienda non possieda già un account Microsoft Developer Center prima di provare a crearne uno. Durante il processo, Microsoft raccoglierà informazioni sull'indirizzo dell'azienda, sul conto bancario e sui dati fiscali. Tali informazioni possono generalmente essere ottenute da contatti finanziari o aziendali.

> [!IMPORTANT]
> I seguenti componenti del profilo sviluppatore devono essere completati per l'avanzamento attraverso le diverse fasi della creazione e della distribuzione dell'offerta.
>
>

| Profilo sviluppatore | Per iniziare la bozza | Staging | Pubblica modello gratuito e di soluzione | Pubblica offerta commerciale |
| --- | --- | --- | --- | --- |
| Registrazione della società |Necessario |Necessario |Necessario |Necessario |
| ID del profilo fiscale |Facoltativo |Facoltativo |Facoltativo |Necessario |
| Conto bancario |Facoltativo |Facoltativo |Facoltativo |Necessario |

> [!NOTE]
> Bring Your Own License (BYOL) è supportato solo per le macchine virtuali e viene considerato un’offerta **gratuita**.
>
>

### <a name="register-your-company-account"></a>Registrare l'account della società
Passaggio 1. Aprire una nuova sessione di navigazione in incognito su Chrome o InPrivate Browsing in Internet Explorer per assicurarsi di non essere connessi a un account personale.

Passaggio 2. Passare a [http://dev.windows.com/registration?accountprogram=azure](http://dev.windows.com/registration?accountprogram=azure) per registrarsi come venditore in Dev Center. Prima di procedere, leggere la seguente nota importante.

![disegno][img-verify]

   > [!IMPORTANT]
   > Assicurarsi che l'ID di posta elettronica o la lista di distribuzione (si consiglia l'uso di una lista di distribuzione per eliminare la dipendenza da una singola persona) che sarà usata per la registrazione in Dev Center sia prima registrata come un account Microsoft. In caso contrario, registrarsi mediante questo [collegamento](https://signup.live.com/signup?uaid=e479342fe2824efeb0c3d92c8f961fd3&lic=1). Inoltre, per la registrazione in Dev Center, **non è consentito usare nessun ID di e-mail appartenente al dominio dell'azienda Microsoft, ad esempio @microsoft.com non può essere usato**.
   >
   >

![Accesso a Dev Center](./media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-login.jpg)

Passaggio 3. Completare la "Procedura guidata per la protezione dell'account", che verificherà l'identità tramite il numero di telefono o l'indirizzo di posta elettronica.

Passaggio 4. Nella sezione "Informazioni sull'Account di registrazione" selezionare **il paese/l'area geografica dell'account** nel menu a discesa.

 ![disegno](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_04.png)

   > [!WARNING]
   > **Paesi di origine della vendita:** per vendere i propri servizi in Azure Marketplace, l'entità registrata deve trovarsi in uno dei paesi di origine della vendita approvati. Questa limitazione viene applicata per motivi legati ai proventi e alla tassazione. Questo elenco di paesi verrà ampliato nel prossimo futuro perciò non rimane che attendere. Per altre informazioni, vedere [Criteri di partecipazione a Marketplace](http://go.microsoft.com/fwlink/?LinkID=526833).
   >


Passaggio 5. Selezionare **Azienda** per "Tipo di account" e fare clic sul pulsante **Avanti**.

   > [!IMPORTANT]
   > Per comprendere meglio i tipi di account e quale sia più adatto per le proprie esigenze, visitare la pagina [Tipi di account, aree geografiche e tariffe](https://msdn.microsoft.com/library/windows/apps/jj863494.aspx)
   >
   >

   ![disegno](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_05.png)

Passaggio 6. Immettere il **Nome visualizzato dell'editore**, in genere il nome della società.

   > [!TIP]
   > Il nome visualizzato dell'autore immesso in Dev Center non sarà mostrato in Azure Marketplace quando l'offerta sarà elencata. Deve tuttavia essere compilato per completare il processo di registrazione.
   >


Passaggio 7. Immettere le **informazioni di contatto** per la verifica dell'account.

   > [!IMPORTANT]
   > È necessario fornire informazioni accurate, perché saranno usate nel processo di verifica dell'azienda per essere approvata in Developer Center.
   >


Passaggio 8. Immettere le informazioni di contatto per il **responsabile approvazione aziendale**. Il responsabile approvazione aziendale è la persona che può verificare che si è autorizzati a creare un account in Dev Center per conto dell'organizzazione. Al termine, fare clic su **Avanti** per passare alla **"sezione dei pagamenti"**.

   ![disegno](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_08.png)

Passaggio 9. Immettere le informazioni per il pagamento dell'account. Se si ha un codice promozionale che copre il costo della registrazione, è possibile immettere qui. In caso contrario, fornire le informazioni della carta di credito o di PayPal nei mercati supportati. Al termine, fare clic su **Avanti** per passare alla **"schermata di verifica"**.

  ![disegno](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_09.png)

Passaggio 10. Esaminare le informazioni dell'account e verificare che tutto sia corretto. Leggere quindi e accettare le condizioni di [Microsoft Azure Marketplace - Contratto dell'editore](http://go.microsoft.com/fwlink/?LinkID=699560). Selezionare la casella per indicare di aver letto e accettato queste condizioni.

Passaggio 11. Fare clic su **Fine** per confermare la registrazione. Verrà inviato un messaggio di conferma all'indirizzo di posta elettronica.

Passaggio 12. Se si prevede di pubblicare solo offerte gratuite, fare clic su **Vai a [portale Cloud Partner](https://cloudpartner.azure.com/).** È quindi possibile passare alla sezione 3 di questo documento.

Se si prevede di pubblicare offerte commerciali (ad esempio offerte per macchina virtuale con un modello di fatturazione oraria), fare clic sul collegamento per **aggiornare le informazioni sull'account** dove è necessario completare le informazioni fiscali e bancarie nell'account Developer Center.

Se si preferisce aggiornare le informazioni fiscali e bancarie in un secondo momento, passare alla sezione successiva, ovvero la sezione 3 di questo documento.

> [!IMPORTANT]
> In caso di offerte commerciali, non sarà possibile passare le offerte in produzione senza completare le informazioni fiscali e del conto bancario.
>
>

### <a name="add-tax-and-banking-information"></a>Aggiungere le informazioni fiscali e bancarie
 Per pubblicare offerte commerciali per l'acquisto, è necessario aggiungere anche informazioni sui pagamenti e fiscali e inviarle per la convalida al Developer Center. Se si pubblicheranno solo offerte gratuite (oppure offerte BYOL), non è necessario aggiungere tali informazioni. È possibile aggiungerle in un secondo momento, ma questo richiede del tempo per convalidare le informazioni fiscali. Se si sa che si offriranno offerte commerciali per l'acquisto, è consigliabile aggiungerle non appena possibile.

**Informazioni bancarie**

1. Accedi a [Microsoft Developer Center](http://dev.windows.com/registration?accountprogram=azure) con l'account Microsoft.
2. Fare clic su **Account proventi** nel menu a sinistra, quindi in **Scegli il metodo di pagamento** fare clic su **Conto bancario** o **PayPal**.

   > [!IMPORTANT]
   > Se si dispone di offerte commerciali che i clienti acquistano nel Marketplace, questo è l'account in cui si riceveranno i pagamenti per tali acquisti.
   >
   >
3. Immettere le informazioni di pagamento e fare clic su **Salva** quando si è soddisfatti.

   > [!IMPORTANT]
   > Se è necessario aggiornare o modificare l'account di pagamento, seguire la stessa procedura precedente, sostituendo le informazioni attuali con quelle nuove. La modifica dell'account per il pagamento può ritardare i pagamenti per un massimo di un ciclo di pagamento. Questo ritardo si verifica perché è necessario verificare la modifica dell'account, come è stato fatto per la configurazione iniziale dell'account di pagamento. Dopo aver verificato l'account, verrà comunque pagato per l'intero importo. Ii pagamenti in scadenza per il ciclo pagamento corrente saranno aggiunti a quello successivo.
   >
   >
4. Fare clic su **Avanti**.

**Informazioni fiscali**

1. Accedere a [Microsoft Developer Center](http://dev.windows.com/registration?accountprogram=azure) con l'account Microsoft, se necessario.
2. Fare clic su **Profilo fiscale** nel menu a sinistra.
3. Nella pagina **Configura modulo per la tassazione** selezionare il paese o l'area geografica in cui ha la residenza permanente, quindi selezionare il paese o l'area geografica della cittadinanza principale. Fare clic su **Avanti**.
4. Immettere i dettagli fiscali, quindi fare clic su **Avanti**.

> [!WARNING]
> Non sarà possibile eseguire il push in produzione delle offerte commerciali senza completare le informazioni fiscali e del conto bancario nell'account Microsoft Developer Center.
>
>

In caso di problemi con la registrazione in Developer Center, inoltrare un ticket di supporto come indicato di seguito

1. Passare al collegamento del supporto [https://developer.microsoft.com/windows/support](https://developer.microsoft.com/windows/support)
2. Nella sezione **Contattaci** fare clic sul pulsante **Invia una richiesta** (illustrato nella schermata seguente)

  ![disegno](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_02.png)
3. Scegliere "Help with Dev Center" (Aiuto con Dev Center) come **Tipo di problema** e "Publish and manage apps" (Pubblicare e gestire le app) come **Categoria**. Quindi fare clic sul pulsante "Start email" ("Inizia messaggio di posta elettronica").

  ![disegno](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_03.png)
4. Viene visualizzata una pagina di accesso. Usare qualsiasi account Microsoft per effettuare l'accesso. Se non si dispone di un account Microsoft, crearne una seguendo questo [collegamento](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
5. Inserire i dettagli del problema e inviare il ticket facendo clic sul pulsante **Invia**.

  ![disegno](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_05.png)

## <a name="3-register-your-account-in-the-cloud-partner-portal"></a>3. Registrare l'account nel portale Cloud Partner
Il [portale Cloud Partner](https://cloudpartner.azure.com/) consente di pubblicare e gestire le offerte.

1. Aprire una nuova sessione in incognito in Chrome o InPrivate Browsing in Internet Explorer per assicurarsi di non essere connessi a un account personale.
2. Passare al [portale Cloud Partner](https://cloudpartner.azure.com/).
3. Un nuovo utente che esegue l'accesso al [portale Cloud Partner](https://cloudpartner.azure.com/) per la prima volta, deve accedere con lo stesso ID di posta elettronica con cui è registrato l'account Dev Center. In questo modo l'account Dev Center e l'account del portale Cloud Partner saranno collegati. Successivamente, è possibile aggiungere altri membri dell'azienda che lavorano all'applicazione come collaboratori o proprietari nel portale Cloud Partner seguendo la procedura illustrata.

Se si viene aggiunti come collaboratore/proprietario nel portale Cloud Partner, è possibile accedere con il proprio account.

> [!TIP]
> I criteri di partecipazione sono descritti nel [sito Web di Azure](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
>
>

## <a name="4-steps-to-manage-users-as-owners-or-contributor-in-the-cloud-partner-portal"></a>4. Passaggi per gestire gli utenti come proprietari o collaboratori nel portale Cloud Partner

[Passaggi per gestire gli utenti nel portale Cloud Partner](./cloud-partner-portal-manage-users.md)


## <a name="next-steps"></a>Passaggi successivi
Ora che l'account è stato creato e registrato, è possibile avviare il processo di pubblicazione in Azure Marketplace.

[img-msalive]:media/cloud-partner-portal-create-dev-center-registration/creating-msa-account-msa-live.jpg
[img-email]:media/cloud-partner-portal-create-dev-center-registration/creating-msa-account-msa-verifyemail.jpg
[img-sd-url]:media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-incognito.jpg
[img-signin]:media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-login.jpg
[img-verify]:media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-verify.jpg
[img-sd-top]:media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-personal-acc-details.jpg
[img-sd-info]:media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-personal.jpg

[img-sd-type]:media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-personal-acc-type.jpg

[img-sd-mktg1]:media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-personal-comp-det1.jpg
[img-sd-mktg2]:media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-personal-comp-det2.jpg
[img-sd-addr]:media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-personal-comp-add.jpg
[img-sd-legal]:media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-personal-cmp.jpg
[img-sd-submit]:media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-approval.jpg


[link-msdndoc]: https://msdn.microsoft.com/library/jj552460.aspx
[link-sellerdashboard]: http://sellerdashboard.microsoft.com/
[link-pubportal]: https://publish.windowsazure.com
[link-single-vm]:marketplace-publishing-vm-image-creation.md
[link-single-vm-prereq]:marketplace-publishing-vm-image-creation-prerequisites.md
[link-multi-vm]:marketplace-publishing-solution-template-creation.md
[link-multi-vm-prereq]:marketplace-publishing-solution-template-creation-prerequisites.md
[link-datasvc]:marketplace-publishing-data-service-creation.md
[link-datasvc-prereq]:marketplace-publishing-data-service-creation-prerequisites.md
[link-devsvc]:marketplace-publishing-dev-service-creation.md
[link-devsvc-prereq]:marketplace-publishing-dev-service-creation-prerequisites.md
[link-pushstaging]:marketplace-publishing-push-to-staging.md
