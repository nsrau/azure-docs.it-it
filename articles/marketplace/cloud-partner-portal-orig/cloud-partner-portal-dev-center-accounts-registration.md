---
title: Creare un account Microsoft Developer | Azure Marketplace
description: Requisiti e procedure per la creazione di un account di Microsoft Developer.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 4fde5d81fb97bec23fdb46ff53b05874c88d9d67
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935854"
---
<a name="create-a-microsoft-developer-account"></a>Creare un account di Microsoft Developer
====================================

Questo articolo descrive come diventare uno sviluppatore Microsoft approvato per la pubblicazione in Azure Marketplace.

## <a name="create-a-microsoft-account"></a>Creare un account Microsoft

Per avviare il processo di pubblicazione, è necessario completare il processo di registrazione nel **Microsoft Developer Center**. Per iniziare il processo di pubblicazione, si userà lo stesso account registrato nel **[portale Cloud Partner](https://cloudpartner.azure.com/)** .

### <a name="general-account-guidelines"></a>Linee guida per gli account generici

È consigliabile utilizzare un solo account Microsoft per le offerte in Azure Marketplace. L’account non deve essere specifico per particolari offerte o servizi.

L'indirizzo che costituisce il nome utente deve trovarsi nel proprio dominio ed essere controllato dal team IT. Tutte le attività correlate alla pubblicazione devono essere eseguite tramite questo account.

>[!WARNING]
>Parole come "Azure" e "Microsoft" non sono ammesse per la registrazione dell'account Microsoft. Evitare di utilizzare queste parole per completare il processo di registrazione e creazione dell’account.

### <a name="company-account-guidelines"></a>Linee guida per gli account aziendali

Seguire queste linee guida se più di una persona dovrà accedere all'account con le credenziali dell'account Microsoft con cui l'account è stato aperto.

>[!IMPORTANT]
>Per consentire a più utenti di accedere all'account di Developer Center, è consigliabile usare Azure Active Directory per assegnare ruoli ai singoli utenti, che potranno accedere all'account usando le proprie credenziali di Azure AD. Per altre informazioni, vedere [Gestire gli utenti dell'account](https://docs.microsoft.com/windows/uwp/publish/manage-account-users).

-   Creare l'account Microsoft usando un indirizzo di posta elettronica appartenente al dominio dell\'azienda, ma non a un singolo utente. Ad esempio, windowsapps\@fabrikam.com.
-   Limitare l'accesso a questo account Microsoft al minor numero possibile di sviluppatori.
-   Impostare una lista di distribuzione di posta elettronica aziendale che includa tutti gli utenti che hanno necessità di accedere all'account per sviluppatori e aggiungere questo indirizzo email alle informazioni di sicurezza. In questo modo tutti i dipendenti nella lista di distribuzione riceveranno i codici di sicurezza quando necessario e potranno gestire le informazioni di sicurezza dell'account Microsoft. Se non è possibile impostare una lista di distribuzione, il proprietario del singolo account di posta elettronica dovrà accedere e condividere il codice di sicurezza quando richiesto, ad esempio quando viene aggiunta una nuova informazione di sicurezza all'account o quando si deve eseguire l'accesso da un nuovo dispositivo.
-   Aggiungere un numero di telefono aziendale che non richieda un interno e che sia accessibile ai membri principali del team.
-   In generale, gli sviluppatori devono usare dispositivi affidabili per accedere all'account per gli sviluppatori dell'azienda. Tutti i principali membri del team devono avere accesso a questi dispositivi affidabili. Questo riduce la necessità di inviare i codici di sicurezza quando si accede all'account.
-   Se si desidera consentire l'accesso all'account a un computer non affidabile, limitare tale accesso a un massimo di cinque sviluppatori. In teoria, questi sviluppatori devono accedere all'account da computer che condividono la stessa area geografica e lo stesso percorso di rete.
-   Verificare spesso le [informazioni di sicurezza dell'azienda](https://account.live.com/proofs/Manage) per accertarsi che siano aggiornate.

>[!IMPORTANT]
>L'accesso all'account sviluppatore deve avvenire principalmente da PC affidabili. Questo è fondamentale poiché è previsto un limite al numero di codici generati per ogni account, ogni settimana. Consente inoltre di eseguire un accesso il più trasparente possibile.
>
>Per altre informazioni, vedere [Linee guida aggiuntive per gli account aziendali](https://msdn.microsoft.com/windows/uwp/publish/opening-a-developer-account#additional-guidelines-for-company-accounts).

### <a name="to-create-a-microsoft-account"></a>Per creare un account Microsoft

1.  Aprire una nuova sessione in incognito in Chrome o InPrivate Browsing in Internet Explorer per accertarsi di non essere connessi a un account esistente.
2.  Seguendo le linee guida indicate in precedenza, registrare l'indirizzo di posta elettronica come account Microsoft usando questo [collegamento](https://signup.live.com/signup.aspx). Completare i seguenti passaggi per l'iscrizione:

    - Quando si registra l'account come account Microsoft, è necessario fornire un numero di telefono valido che il sistema userà per inviare un codice di verifica mediante SMS o chiamata automatica.
    - Quando si registra l'account come account Microsoft è necessario fornire un ID di posta elettronica valido per poter ricevere un messaggio e-mail automatico per la verifica dell'account.
    - Verificare l'indirizzo di posta elettronica inviato al DL.

    A questo punto si è pronti per usare il nuovo account Microsoft nel Microsoft Developer Center.

## <a name="register-your-account-in-microsoft-developer-center"></a>Registrare l'account nel centro per sviluppatori di Microsoft

Microsoft Developer Center viene usato per registrare le informazioni della società una sola volta. La persona che effettua la registrazione deve essere un rappresentante della società valido e deve fornire le informazioni personali come metodo per convalidare la propria identità. La persona che esegue la registrazione deve usare un account Microsoft condiviso per l'azienda, **che deve essere usato anche nel portale Cloud Partner**. Assicurarsi che l'azienda non possieda già un account Microsoft Developer Center prima di provare a crearne uno. Durante il processo, Microsoft raccoglierà informazioni sull'indirizzo dell'azienda, sul conto bancario e sui dati fiscali. Tali informazioni possono generalmente essere ottenute da contatti finanziari o aziendali.

>[!IMPORTANT]
>I seguenti componenti del profilo sviluppatore devono essere completati per l'avanzamento attraverso le diverse fasi della creazione e della distribuzione dell'offerta.

| Profilo sviluppatore     | Per iniziare la bozza    | Staging       | Pubblica modello gratuito e di soluzione   | Pubblica offerta commerciale   |
|---------------------- |----------------   |-----------    |-------------------------------------  |---------------------  |
| Registrazione della società  | Necessario         | Necessario     | Necessario                             | Necessario             |
| ID del profilo fiscale        | Facoltativo          | Facoltativo      | Facoltativo                              | Necessario             |
| Conto bancario          | Facoltativo          | Facoltativo      | Facoltativo                              | Necessario             |

>[!NOTE]
>Bring Your Own License (BYOL) è supportato solo per le macchine virtuali e viene considerato un’offerta gratuita.

### <a name="register-your-company-account"></a>Registrare l'account della società

1. Aprire una nuova sessione di navigazione in incognito su Chrome o InPrivate Browsing in Internet Explorer per assicurarsi di non essere connessi a un account personale.

2. Andare a [Windows Dev Center](https://dev.windows.com/registration?accountprogram=azure) e registrarsi come venditore. Prima di procedere, leggere la seguente nota importante.

   ![Verifica dell'account Microsoft](./media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-verify.jpg)

    >[!IMPORTANT]
    >Assicurarsi che l'ID di posta elettronica o la lista di distribuzione (si consiglia l'uso di una lista di distribuzione per eliminare la dipendenza da una singola persona) che sarà usata per la registrazione in Dev Center sia prima registrata come un account Microsoft. In caso contrario, registrarsi con questo collegamento. Inoltre, per la registrazione in Dev Center, non possono essere usati ID di posta elettronica appartenenti al dominio aziendale Microsoft.

   ![Accesso a Dev Center](./media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-login.jpg)

3. Eseguire la procedura guidata "Aiutaci a proteggere il tuo account" per verificare l'identità tramite il numero di telefono o l'indirizzo di posta elettronica.

4. In "Informazioni sull'Account di registrazione" scegliere **il paese/l'area geografica dell'account** nell'elenco a discesa e selezionare **Avanti**.

   ![Selezionare un paese o un'area geografica](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_04.png)

    >[!WARNING]
    >"Di origine della vendita" paesi/aree geografiche: Per vendere i propri servizi in Azure Marketplace, l'entità registrata deve trovarsi in uno degli approvati "di origine della vendita" paesi/aree geografiche nell'elenco a discesa. Questa limitazione viene applicata per motivi legati ai proventi e alla tassazione. Per altre informazioni, vedere i criteri di partecipazione a Microsoft Azure Marketplace.

5. Selezionare **Società** come "Tipo di account" e quindi selezionare **Avanti**.

    >[!IMPORTANT]
    >Per comprendere meglio le caratteristiche dei tipi di account disponibili e individuare quello più adatto alle proprie esigenze, visitare la pagina Tipi di account, aree geografiche e tariffe nella schermata successiva.

    ![Tipi di account per i venditori](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_05.png)

6. Immettere il **Nome visualizzato dell'editore**. Si tratta in genere del nome della società.

    >[!NOTE]
    >Il nome visualizzato dell'editore immesso in Dev Center non verrà mostrato in Azure Marketplace dopo che l'offerta sarà inserita in elenco. Tuttavia, questa informazione è necessaria per completare il processo di registrazione.

7. Immettere le **informazioni di contatto** per la verifica dell'account.

    >[!IMPORTANT]
    >È necessario fornire informazioni accurate, perché saranno usate nel processo di verifica per l'approvazione dell'azienda in Developer Center.

8. Immettere le informazioni di contatto per il **responsabile approvazione aziendale**. Il responsabile approvazione aziendale è la persona che può verificare che si è autorizzati a creare un account in Dev Center per conto dell'organizzazione. Dopo aver fornito queste informazioni, selezionare **Avanti** per passare alla **sezione Pagamento**.

    ![Individuare il responsabile approvazione aziendale](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_08.png)

9. Immettere le informazioni di pagamento per l'account. Se si ha un codice promozionale che copre il costo della registrazione, è possibile immettere qui. In caso contrario, fornire le informazioni della carta di credito o di PayPal nei mercati supportati. Selezionare **Avanti** per procedere alla **Verifica** finale.

   ![Registrazione del pagamento](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_09.png)

10. Esaminare le informazioni dell'account e verificare che tutto sia corretto. Leggere e accettare le condizioni del [Contratto per editori di Microsoft Azure Marketplace](https://go.microsoft.com/fwlink/?LinkID=699560). Selezionare la casella per indicare di aver letto e accettato queste condizioni.

11. Selezionare **Fine** per confermare la registrazione. Un messaggio di conferma viene inviato all'indirizzo di posta elettronica.

12. Se si prevede di pubblicare solo offerte gratuite, selezionare [Andare al portale Cloud Partner](https://cloudpartner.azure.com/) e passare direttamente a "Registrare l'account nel portale Cloud Partner" in questo articolo.

### <a name="commercial-offers"></a>Offerte commerciali

Se si prevede di pubblicare offerte commerciali, ad esempio un'offerta di macchina virtuale usando un modello di fatturazione oraria, è necessario fornire le informazioni bancarie e fiscali. A questo scopo, accedere all'account Developer Center e selezionare **Update your account information** (Aggiorna le informazioni dell'account). Seguire le istruzioni nella sezione seguente "Aggiungere informazioni bancarie e fiscali".

>[!IMPORTANT]
>Non sarà possibile effettuare il push di un'offerta commerciale nell'ambiente di produzione senza fornire informazioni bancarie e fiscali.

Se si preferisce definire le informazioni bancarie e fiscali in un secondo momento, si può passare direttamente a "Registrare l'account nel portale Cloud Partner" in questo articolo.

>[!NOTE]
>È opportuno specificare le informazioni bancarie e fiscali al più presto poiché la verifica dei dati fiscali richiede tempo.

### <a name="add-banking-and-tax-information"></a>Aggiungere informazioni bancarie e fiscali

Per poter pubblicare offerte commerciali per l'acquisto, è necessario aggiungere le informazioni bancarie e fiscali e sottoporle a verifica al Developer Center.

**Per aggiungere le informazioni bancarie**

1.  Accedi a [Microsoft Developer Center](https://dev.windows.com/registration?accountprogram=azure) con l'account Microsoft.
2.  Selezionare **Account proventi** nel menu a sinistra, quindi in corrispondenza di **Scegli il metodo di pagamento** fare clic su **Conto bancario** o **PayPal**.

    >[!NOTE]
    >Se si dispone di offerte commerciali che i clienti acquistano nel Marketplace, questo è l'account in cui si riceveranno i pagamenti per tali acquisti.
3.  Immettere le informazioni di pagamento e selezionare **Salva**.

    >[!IMPORTANT]
    >Se si presenta l'esigenza di modificare o cambiare l'account proventi, seguire la procedura precedente e sostituire le informazioni attuali con quelle nuove.
    >
    >La modifica dell'account per il pagamento può ritardare i pagamenti per un massimo di un ciclo di pagamento. Questo ritardo si verifica perché è necessario verificare la modifica dell'account, come è stato fatto per la configurazione iniziale dell'account di pagamento. Dopo aver verificato l'account, verrà comunque pagato per l'intero importo. Ii pagamenti in scadenza per il ciclo pagamento corrente saranno aggiunti a quello successivo.

4.  Selezionare **Avanti**.

**Per aggiungere le informazioni fiscali**

1.  Accedere a [Microsoft Developer Center](https://dev.windows.com/registration?accountprogram=azure) con l'account Microsoft, se necessario.
2.  Selezionare **Profilo fiscale** nel menu a sinistra.
3.  Nella pagina **Configura modulo per la tassazione**:
    - Selezionare il paese o la regione in cui si ha la residenza permanente.
    - Selezionare il paese o regione di cui si ha la cittadinanza principale.
    - Selezionare **Avanti**.
4.  Immettere i dettagli fiscali e quindi selezionare **Avanti**.

>[!WARNING]
>Non sarà possibile effettuare il push delle offerte commerciali in produzione senza fornire le informazioni fiscali e del conto bancario nell'account Microsoft Developer Center.

### <a name="developer-center-registration-issues"></a>Problemi di registrazione in Developer Center

In caso di problemi con la registrazione in Developer Center, inoltrare una richiesta di assistenza.

1.  Andare al [Supporto per gli sviluppatori](https://developer.microsoft.com/windows/support).
2.  In **Contattaci** selezionare il pulsante **Invia una richiesta di assistenza**.
    ![Aprire una richiesta](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_02.png)
3.  Selezionare "Help with Dev Center" (Problema con Dev Center) come **Tipo di problema** e selezionare "Publish and manage apps" (Gestione e pubblicazione applicazioni) come **Categoria**. Selezionare **Avvia una richiesta di assistenza**.

    ![identificare il tipo di problema](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_03.png)

4.  Viene visualizzata una pagina di accesso. Accedere con un account Microsoft. Se non si ha un account Microsoft, [crearne uno](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).

5.  Fornire informazioni dettagliate sul problema e selezionare **Invia** per inviare la richiesta.

    ![inviare una richiesta](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_05.png)

## <a name="register-your-account-in-the-cloud-partner-portal"></a>Registrare l'account nel portale Cloud Partner

Il [portale Cloud Partner](https://cloudpartner.azure.com/) consente di pubblicare e gestire le offerte.

1.  Aprire una nuova sessione in incognito di Chrome o una nuova sessione InPrivate Browsing di Internet Explorer per assicurarsi di non connettersi a un account personale.
2.  Passare al [portale Cloud Partner](https://cloudpartner.azure.com/).
3.  Un nuovo utente che esegue l'accesso al [portale Cloud Partner](https://cloudpartner.azure.com/) per la prima volta, deve accedere con lo stesso ID di posta elettronica con cui è registrato l'account Dev Center. Questo fa sì che l'account Dev Center e l'account del portale Cloud Partner siano collegati.

In un secondo momento sarà possibile aggiungere gli altri membri dell'azienda che contribuiscono all'applicazione. Possono essere aggiunti come collaboratori o proprietari nel portale Cloud partner seguendo i passaggi descritti nella sezione successiva.

Se si viene aggiunti come collaboratore/proprietario nel portale Cloud Partner, è possibile accedere con il proprio account.

>[!TIP]
>I criteri di partecipazione sono descritti nel sito Web di Azure.

## <a name="manage-users-as-owners-or-contributors-in-the-cloud-partner-portal"></a>Gestire gli utenti come proprietari o collaboratori nel portale Cloud Partner

[Passaggi per gestire gli utenti nel portale Cloud Partner](./cloud-partner-portal-manage-users.md)


## <a name="next-steps"></a>Passaggi successivi

Ora che l'account è stato creato e registrato, è possibile avviare il processo di pubblicazione in Azure Marketplace.
