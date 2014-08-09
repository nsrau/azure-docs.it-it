<properties linkid="manage-services-manage-acs" urlDisplayName="Manage ACS" pageTitle="Access Control Service - Azure service management" metaKeywords="" description="Learn how to manage your Azure Access Control Service (ACS) using certificates and keys." metaCanonical="" services="active-directory" documentationCenter="" title="Managing Your ACS Namespace" authors="" solutions="" manager="" editor="" />

Gestione dello spazio dei nomi ACS
==================================

In questo argomento vengono descritte le attività di gestione che è consigliabile eseguire regolarmente affinché le applicazioni che utilizzano il servizio di controllo di accesso (ACS) di Azure continuino a funzionare correttamente e senza interruzioni. Di seguito sono illustrate queste attività di gestione:

1.  Importante: traccia della scadenza e rollover dei certificati, chiavi e password utilizzate dallo spazio dei nomi ACS, applicazioni relying party, identità del servizio, provider di identità e account del servizio di gestione ACS. Per ulteriori informazioni, vedere le linee guida per la gestione di certificati e chiavi riportate di seguito.

2.  Verificare i provider di identità, le identità del servizio, le regole e gli amministratori del portale e rimuovere quelli non aggiornati.

Per ulteriori informazioni sul servizio di controllo di accesso, vedere [Servizio di controllo di accesso 2.0](http://msdn.microsoft.com/it-it/library/gg429786.aspx).

Linee guida per la gestione di certificati e chiavi
---------------------------------------------------

Per motivi di sicurezza, per i certificati e le chiavi utilizzati nel servizio di controllo di accesso è prevista una scadenza. È importante tenere traccia delle date di scadenza al fine di rinnovare i certificati e le chiavi.

Procedura generale per la sostituzione di un certificato per la firma di token (certificato a chiave simmetrica o X.509) o di un certificato per la decrittografia di token:

1.  Configurare il nuovo certificato o chiave in ACS come chiave "secondaria" accanto alla chiave o al certificato esistente prossimo alla scadenza.
2.  Notificare ai partner che utilizzano il servizio che sarà necessario aggiornare le relative chiavi corrispondenti entro una determinata scadenza.
3.  I partner dovranno aggiornare la chiave o il certificato corrispondente per i rispettivi relying party o provider di identità. Ad esempio, importare i metadati WS-Federation aggiornati per lo spazio dei nomi ACS contenente il nuovo certificato di convalida della firma del token o configurare manualmente la chiave simmetrica nella configurazione dell'applicazione.
4.  Al termine dell'aggiornamento di tutte le applicazione (o trascorsa una determinata scadenza), contrassegnare la nuova chiave o certificato come primario nella configurazione ACS.
5.  Trascorso un periodo di tolleranza ragionevole, rimuovere la chiave o il certificato precedente dalla configurazione ACS.

	La procedura generale per la sostituzione dei certificati per la decrittografia di token è la seguente:

1.  L'utente (o i relativi partner) aggiorna la chiave o il certificato corrispondente utilizzato per la decrittografia di token nelle applicazioni relying party.
2.  Configurare il nuovo certificato per la crittografia in ACS accanto al certificato esistente prossimo alla scadenza.
3.  Rimuovere il certificato per la crittografia precedente.

	Procedura generale per la sostituzione delle chiavi del servizio di gestione o di identità del servizio:

1.  Configurare il nuovo certificato o chiave in ACS accanto alla chiave o al certificato esistente prossimo alla scadenza.
2.  L'utente (o i relativi partner) aggiorna la chiave o il certificato corrispondente utilizzato per le richieste di token nelle applicazioni client.
3.  Al termine dell'aggiornamento di tutti i client (o dopo un periodo di tolleranza ragionevole), rimuovere la chiave o il certificato precedente.

Quando un certificato o una chiave scade, il servizio ACS non riesce a rilasciare i token impedendo così al relying party di funzionare normalmente. Le chiavi e i certificati scaduti verranno ignorati dal servizio ACS, generando eccezioni come se non fosse configurato alcun certificato o chiave. Nelle sezioni seguenti verranno fornite informazioni per i certificati e le chiavi gestiti dal servizio ACS, verrà illustrato come rinnovarli e come riconoscere se sono scaduti e devono essere rinnovati.

-   Utilizzare la sezione Certificates and Keys nel portale di gestione ACS per gestire i certificati e le chiavi relativi allo spazio dei nomi servizio e alle applicazioni relying party. Per ulteriori informazioni su questi tipi di credenziali, vedere [Certificati e chiavi](http://msdn.microsoft.com/it-it/library/gg185932.aspx).
-   Utilizzare la sezione relativa alle identità del servizio nel portale di gestione ACS per gestire le credenziali (certificati, chiavi o password) relative alle identità del servizio. Per ulteriori informazioni sulle identità del servizio, vedere [Identità del servizio](http://msdn.microsoft.com/it-it/library/gg185945.aspx).
-   Utilizzare la sezione Management Service nel portale di gestione ACS per gestire le credenziali (certificati, chiavi o password) relative agli account del servizio di gestione ACS. Per ulteriori informazioni sul servizio di gestione ACS, vedere [Servizio di gestione ACS](http://msdn.microsoft.com/it-it/library/gg185972.aspx).

Alcuni tipi di certificati e chiavi non sono visibili nel portale di gestione ACS. In particolare, per i provider di identità WS-Federation, come ADFS, è necessario verificare attivamente la validità dei certificati utilizzati dai provider di identità. Attualmente, i certificati disponibili tramite i metadati del provider di identità WS-Federica non sono visibili nel portale di gestione ACS. Per verificare la validità dei certificati è necessario utilizzare il servizio di gestione per visualizzare le date di validità e di scadenza per le proprietà StartDate e EndDate di [IdentityProviderKey](http://msdn.microsoft.com/it-it/library/hh124084.aspx). Quando una chiave o un certificato scade diventando quindi non valido, il servizio ACS inizierà a generare eccezioni [Codici di errore ACS](http://msdn.microsoft.com/it-it/library/gg185949.aspx) specifici per il certificato o la chiave. Per informazioni sui codici di errore specifici, vedere le sezioni seguenti.

È possibile aggiornare i certificati e le chiavi a livello di codice tramite il [servizio di gestione ACS](http://msdn.microsoft.com/it-it/library/gg185972.aspx). È consigliabile esaminare l'esempio di codice KeyManagement disponibile per il download nell'ambito dell'[Esempio di codice: Servizio di gestione](http://msdn.microsoft.com/it-it/library/gg185970.aspx).

Certificati e chiavi disponibili
--------------------------------

Nell'elenco riportato di seguito sono visualizzati i certificati e le chiavi utilizzati in ACS per i quali occorre tenere traccia delle date di scadenza:

-   Certificati per la firma di token
-   Chiavi per la firma di token
-   Certificati di crittografia dei token
-   Certificati di decrittografia dei token
-   Credenziali per l'identità del servizio
-   Credenziali per l'account del servizio di gestione ACS
-   Certificati di crittografia e firma del provider di identità

Nella parte rimanente di questo argomento vengono illustrati in dettaglio tutti i certificati e le chiavi.

Certificati per la firma di token
---------------------------------

ACS firma tutti i token di sicurezza rilasciati. Quando si creano applicazioni che utilizzano token SAML rilasciati da ACS, per la firma vengono utilizzati certificati X.509.

È possibile gestire i certificati per la firma di token nella sezione Certificates and Keys del portale di gestione ACS.

**Per gestire i certificati per la firma di token**

1.  Aprire un browser Internet e visitare il portale di gestione di Azure all'indirizzo [http://go.microsoft.com/fwlink/?LinkId=129428](http://go.microsoft.com/fwlink/?LinkID=129428).

2.  Accedere al sito Web utilizzando un Windows Live ID. Se non si dispone di un Windows Live ID, fare clic su Iscriviti ora per crearne uno.

3.  Dopo avere eseguito l'accesso, si verrà reindirizzati alla pagina del portale di gestione. Nel lato inferiore sinistro della pagina fare clic su **Service Bus and Access Control**.

    ![](./media/manage-acs-namespace/ACS1.png)

4.  Per avviare il portale di gestione ACS, fare clic su **Access Control** nell'albero sul lato sinistro selezionare lo spazio dei nomi del servizio ACS che si desidera configurare e quindi fare clic sul pulsante **Access Control Service** sulla barra degli strumenti nella parte superiore della pagina.

    ![](./media/manage-acs-namespace/ACS2.png)

    Verrà visualizzata una schermata simile alla seguente:

    ![](./media/manage-acs-namespace/ACS3.png)

5.  Fare clic su **Certificates and Keys** nell'albero sul lato sinistro sotto la sezione Service Settings.

    ![](./media/manage-acs-namespace/ACS4.png)

    Verrà quindi visualizzata una schermata simile alla seguente:

    ![](./media/manage-acs-namespace/ACS5.png)

6.  Nella sezione Token Signing utilizzare il pulsante Add per configurare il nuovo certificato in ACS come chiave "secondaria" accanto al certificato esistente prossimo alla scadenza.

7.  Notificare ai partner che utilizzano il servizio che sarà necessario aggiornare le relative chiavi corrispondenti entro una determinata scadenza.

8.  I partner dovranno aggiornare il certificato corrispondente per i rispettivi relying party o provider di identità. Ad esempio, importare i metadati WS-Federation aggiornati per lo spazio dei nomi ACS contenente il nuovo certificato di convalida della firma del token o configurare manualmente la chiave simmetrica nella configurazione dell'applicazione.

9.  Al termine dell'aggiornamento di tutte le applicazione (o trascorsa una determinata scadenza), contrassegnare il nuovo certificato come primario nella configurazione ACS.

10. Trascorso un periodo di tolleranza ragionevole, utilizzare il pulsante Delete nella sezione Token Signing della pagina Certificates and Keys per rimuovere il certificato precedente dalla configurazione ACS.

Per ulteriori informazioni, vedere [Certificati e chiavi](http://msdn.microsoft.com/it-it/library/gg185932.aspx).

Quando i certificati di firma scadono, si riceveranno i messaggi di errore seguenti durante il tentativo di richiedere un token:

<table><tr><td><b>Codice di errore</b>
</td>
<td><b>Messaggio</b>
</td>
<td><b>Azione necessaria per correggere l'errore</b>
</td>
</tr>
<tr>
<td>ACS50004</td>
<td>Non è configurato alcun certificato di firma X.509 primario. Per SAML il certificato di firma è obbligatorio.</td>
<td>Se il relying party scelto utilizza SAML come tipo di token, verificare che sia configurato un certificato X.509 valido per il relying party o lo spazio dei nomi servizio. Il certificato deve essere impostato su primario e deve essere in corso di validità.</td>
</tr>
</table>

Chiave per la firma di token
----------------------------

ACS firma tutti i token di sicurezza rilasciati. Quando si creano applicazioni che utilizzano token SWT rilasciati da ACS, vengono utilizzate chiavi KSK simmetriche a 256 bit.

È possibile gestire le chiavi per la firma di token nella sezione Certificates and Keys del portale di gestione ACS.

**Per gestire una chiave per la firma di token**

1.  Aprire un browser Internet e visitare il portale di gestione di Azure all'indirizzo [http://go.microsoft.com/fwlink/?LinkId=129428](http://go.microsoft.com/fwlink/?LinkID=129428).

2.  Accedere al sito Web utilizzando un Windows Live ID. Se non si dispone di un Windows Live ID, fare clic su Iscriviti ora per crearne uno.

3.  Dopo avere eseguito l'accesso, si verrà reindirizzati alla pagina del portale di gestione. Nel lato inferiore sinistro della pagina fare clic su **Service Bus and Access Control**.

    ![](./media/manage-acs-namespace/ACS1.png)

4.  Per avviare il portale di gestione ACS, fare clic su **Access Control** nell'albero sul lato sinistro selezionare lo spazio dei nomi del servizio ACS che si desidera configurare e quindi fare clic sul pulsante **Access Control Service** sulla barra degli strumenti nella parte superiore della pagina.

    ![](./media/manage-acs-namespace/ACS2.png)

    Verrà visualizzata una schermata simile alla seguente:

    ![](./media/manage-acs-namespace/ACS3.png)

5.  Fare clic su **Certificates and Keys** nell'albero sul lato sinistro sotto la sezione Service Settings.

    ![](./media/manage-acs-namespace/ACS4.png)

    Verrà quindi visualizzata una schermata simile alla seguente:

    ![](./media/manage-acs-namespace/ACS5.png)

6.  Nella sezione Token Signing utilizzare il pulsante Add per configurare la nuova chiave in ACS come chiave "secondaria" accanto alla chiave esistente prossima alla scadenza.

7.  Notificare ai partner che utilizzano il servizio che sarà necessario aggiornare le relative chiavi corrispondenti entro una determinata scadenza.

8.  I partner dovranno aggiornare la chiave corrispondente per i rispettivi relying party o provider di identità. Ad esempio, importare i metadati WS-Federation aggiornati per lo spazio dei nomi ACS contenente il nuovo certificato di convalida della firma del token o configurare manualmente la chiave simmetrica nella configurazione dell'applicazione.

9.  Al termine dell'aggiornamento di tutte le applicazione (o trascorsa una determinata scadenza), contrassegnare la nuova chiave come primaria nella configurazione ACS.

10. Trascorso un periodo di tolleranza ragionevole, utilizzare il pulsante Delete nella sezione Token Signing della pagina Certificates and Keys per rimuovere la chiave precedente dalla configurazione ACS.

Per ulteriori informazioni, vedere [Certificati e chiavi](http://msdn.microsoft.com/it-it/library/gg185932.aspx).

Quando le chiavi di firma scadono, si riceveranno i messaggi di errore seguenti durante il tentativo di richiedere un token:

<table><tr><td><b>Codice di errore</b>
</td>
<td><b>Messaggio</b>
</td>
<td><b>Azione necessaria per correggere l'errore</b>
</td>
</tr>
<tr>
<td>ACS50003</td>
<td>Non è configurata alcuna chiave di firma simmetrica primaria. Per SWT la chiave di firma simmetrica è obbligatoria.</td>
<td>Se il relying party scelto utilizza SWT come tipo di token, verificare che sia configurata una chiave simmetrica per il relying party o lo spazio dei nomi servizio e che tale chiave sia impostata come primaria e sia in corso di validità.</td>
</tr>
</table>

Certificati di crittografia dei token
-------------------------------------

La crittografia dei token è necessaria se un'applicazione relying party è un servizio Web che utilizza token di prova di possesso tramite il protocollo WS-Trust. Negli altri casi è invece facoltativa.

È possibile gestire i certificati per la crittografia di token nella sezione Certificates and Keys del portale di gestione ACS.

**Per gestire i certificati per la crittografia di token**

1.  Aprire un browser Internet e visitare il portale di gestione di Azure all'indirizzo [http://go.microsoft.com/fwlink/?LinkId=129428](http://go.microsoft.com/fwlink/?LinkID=129428).

2.  Accedere al sito Web utilizzando un Windows Live ID. Se non si dispone di un Windows Live ID, fare clic su Iscriviti ora per crearne uno.

3.  Dopo avere eseguito l'accesso, si verrà reindirizzati alla pagina del portale di gestione. Nel lato inferiore sinistro della pagina fare clic su **Service Bus and Access Control**.

    ![](./media/manage-acs-namespace/ACS1.png)

4.  Per avviare il portale di gestione ACS, fare clic su **Access Control** nell'albero sul lato sinistro selezionare lo spazio dei nomi del servizio ACS che si desidera configurare e quindi fare clic sul pulsante **Access Control Service** sulla barra degli strumenti nella parte superiore della pagina.

    ![](./media/manage-acs-namespace/ACS2.png)

    Verrà visualizzata una schermata simile alla seguente:

    ![](./media/manage-acs-namespace/ACS3.png)

5.  Fare clic su **Certificates and Keys** nell'albero sul lato sinistro sotto la sezione Service Settings.

    ![](./media/manage-acs-namespace/ACS4.png)

    Verrà quindi visualizzata una schermata simile alla seguente:

    ![](./media/manage-acs-namespace/ACS7.png)

6.  L'utente (o i relativi partner) aggiorna la chiave o il certificato corrispondente utilizzato per la decrittografia di token nelle applicazioni relying party.
7.  Utilizzare il pulsante Add per configurare il nuovo certificato per la crittografia in ACS accanto al certificato esistente prossimo alla scadenza.
8.  Utilizzare il pulsante Delete per rimuovere il certificato di crittografia precedente.

Per ulteriori informazioni, vedere [Certificati e chiavi](http://msdn.microsoft.com/it-it/library/gg185932.aspx).

Quando i certificati di crittografia scadono, si riceveranno i messaggi di errore seguenti durante il tentativo di richiedere un token:

<table><tr><td><b>Codice di errore</b>
</td>
<td><b>Messaggio</b>
</td>
<td><b>Azione necessaria per correggere l'errore</b>
</td>
</tr>
<tr>
<td>ACS50005</td>
<td>La crittografia dei token è obbligatoria ma non è configurato alcun certificato di crittografia per il relying party.</td>
<td>Disabilitare la crittografia dei token per il relying party scelto o caricare un certificato X.509 da utilizzare per la crittografia dei token.</td>
</tr>
</table>

Certificati di decrittografia dei token
---------------------------------------

ACS può accettare token crittografati di provider di identità WS-Federation, ad esempio ADFS 2.0. Per la decrittografia viene utilizzato un certificato X.509 ospitato in ACS.

È possibile gestire i certificati per la decrittografia di token nella sezione Certificates and Keys del portale di gestione ACS.

**Per gestire i certificati per la decrittografia di token**

1.  Aprire un browser Internet e visitare il portale di gestione di Azure all'indirizzo [http://go.microsoft.com/fwlink/?LinkId=129428](http://go.microsoft.com/fwlink/?LinkID=129428).

2.  Accedere al sito Web utilizzando un Windows Live ID. Se non si dispone di un Windows Live ID, fare clic su Iscriviti ora per crearne uno.

3.  Dopo avere eseguito l'accesso, si verrà reindirizzati alla pagina del portale di gestione. Nel lato inferiore sinistro della pagina fare clic su **Service Bus and Access Control**.

    ![](./media/manage-acs-namespace/ACS1.png)

4.  Per avviare il portale di gestione ACS, fare clic su **Access Control** nell'albero sul lato sinistro selezionare lo spazio dei nomi del servizio ACS che si desidera configurare e quindi fare clic sul pulsante **Access Control Service** sulla barra degli strumenti nella parte superiore della pagina.

    ![](./media/manage-acs-namespace/ACS2.png)

    Verrà visualizzata una schermata simile alla seguente:

    ![](./media/manage-acs-namespace/ACS3.png)

5.  Fare clic su **Certificates and Keys** nell'albero sul lato sinistro sotto la sezione Service Settings.

    ![](./media/manage-acs-namespace/ACS4.png)

    Verrà quindi visualizzata una schermata simile alla seguente:

    ![](./media/manage-acs-namespace/ACS9.png)

6.  Nella sezione Token Decryption utilizzare il pulsante Add per configurare il nuovo certificato in ACS come chiave "secondaria" accanto al certificato esistente prossimo alla scadenza.

7.  Notificare ai partner che utilizzano il servizio che sarà necessario aggiornare le relative chiavi corrispondenti entro una determinata scadenza.

8.  I partner dovranno aggiornare il certificato corrispondente per i rispettivi relying party o provider di identità. Ad esempio, importare i metadati WS-Federation aggiornati per lo spazio dei nomi ACS contenente il nuovo certificato di convalida della firma del token o configurare manualmente la chiave simmetrica nella configurazione dell'applicazione.

9.  Al termine dell'aggiornamento di tutte le applicazione (o trascorsa una determinata scadenza), contrassegnare il nuovo certificato come primario nella configurazione ACS.

10. Trascorso un periodo di tolleranza ragionevole, utilizzare il pulsante Delete nella sezione Token Signing della pagina Certificates and Keys per rimuovere il certificato precedente dalla configurazione ACS.

Per ulteriori informazioni, vedere [Certificati e chiavi](http://msdn.microsoft.com/it-it/library/gg185932.aspx).

Quando i certificati di decrittografia scadono, si riceveranno i messaggi di errore seguenti durante il tentativo di richiedere un token:

<table><tr><td><b>Codice di errore</b>
</td>
<td><b>Messaggio</b>
</td>
</tr>
<tr>
<td>ACS10001</td>
<td>Si è verificato un errore durante l'elaborazione dell'intestazione SOAP.</td>
</tr>
<tr><td>ACS20001</td>
<td>Si è verificato un errore durante l'elaborazione di una risposta di accesso WS-Federation.</td>
</tr>
</table>

Credenziali per l'identità del servizio
---------------------------------------

Le identità del servizio sono credenziali configurate a livello globale per lo spazio dei nomi ACS, che consentono ad applicazioni o client di eseguire l'autenticazione direttamente con il servizio ACS e di ricevere un token. All'identità del servizio ACS è possibile associare tre tipi di credenziali, ossia chiave simmetrica, password e certificato X.509.

È possibile gestire le credenziali dell'identità del servizio tramite la pagina Service Identities del portale di gestione ACS.

**Per gestire le credenziali per l'identità del servizio**

1.  Aprire un browser Internet e visitare il portale di gestione di Azure all'indirizzo [http://go.microsoft.com/fwlink/?LinkId=129428](http://go.microsoft.com/fwlink/?LinkID=129428).

2.  Accedere al sito Web utilizzando un Windows Live ID. Se non si dispone di un Windows Live ID, fare clic su Iscriviti ora per crearne uno.

3.  Dopo avere eseguito l'accesso, si verrà reindirizzati alla pagina del portale di gestione. Nel lato inferiore sinistro della pagina fare clic su **Service Bus and Access Control**.

    ![](./media/manage-acs-namespace/ACS1.png)

4.  Per avviare il portale di gestione ACS, fare clic su **Access Control** nell'albero sul lato sinistro selezionare lo spazio dei nomi del servizio ACS che si desidera configurare e quindi fare clic sul pulsante **Access Control Service** sulla barra degli strumenti nella parte superiore della pagina.

    ![](./media/manage-acs-namespace/ACS2.png)

    Verrà visualizzata una schermata simile alla seguente:

    ![](./media/manage-acs-namespace/ACS3.png)

5.  Fare clic su **Service identities** nell'albero sul lato sinistro sotto la sezione Service Settings.

    ![](./media/manage-acs-namespace/ACS11.png)

6.  Fare clic sull'identità del servizio da modificare.

    ![](./media/manage-acs-namespace/ACS112.png)

7.  Nella sezione Credentials, utilizzare il pulsante Add per configurare il nuovo certificato o chiave in ACS accanto alla chiave o al certificato esistente prossimo alla scadenza.

8.  L'utente (o i relativi partner) aggiorna la chiave o il certificato corrispondente utilizzato per le richieste di token nelle applicazioni client.

9.  Al termine dell'aggiornamento di tutti i client (o dopo un periodo di tolleranza ragionevole), utilizzare il pulsante Delete per rimuovere la chiave o il certificato precedente.

Per ulteriori informazioni, vedere [Identità del servizio](http://msdn.microsoft.com/it-it/library/gg185945.aspx).

Di seguito sono riportate le eccezioni generate da ACS quando le credenziali sono scadute:

<table><tr><td><b>Credenziale</b>
</td>
<td><b>Codice di errore</b>
</td>
<td><b>Messaggio</b>
</td>
<td><b>Azione necessaria per correggere l'errore</b>
</td>
</tr>
<tr>
<td>Chiave simmetrica, password</td>
<td>ACS50006</td>
<td>Verifica della firma non riuscita. Il messaggio potrebbe contenere ulteriori dettagli.</td>
<td/>
</tr>
<tr><td>Certificato X.509</td>
<td>ACS50016</td>
<td>Il certificato X.509 con oggetto '<Nome oggetto certificato> e identificazione personale '<Identificazione personale certificato>' non corrisponde ad alcun certificato configurato.</td>
<td>Verificare che il certificato richiesto sia stato caricato in ACS.</td>
</tr>
</table>

Per verificare e aggiornare le date di scadenza di chiavi simmetriche o password o per caricare un nuovo certificato come credenziali dell'identità del servizio, seguire le istruzioni fornite in [Procedura: Aggiungere identità del servizio con un certificato X.509, una password o una chiave simmetrica](http://msdn.microsoft.com/it-it/library/gg185924.aspx). L'elenco delle credenziali delle identità del servizio è disponibile nella pagina Edit Service Identity.

Credenziali del servizio di gestione
------------------------------------

Il servizio di gestione ACS è un componente essenziale che consente di gestire e configurare a livello di codice le impostazioni in uno spazio dei nomi ACS. All'account del servizio di gestione ACS è possibile associare tre tipi di credenziali, ossia chiave simmetrica, password e certificato X.509.

È possibile gestire le credenziali del servizio di gestione tramite la pagina Management service del portale di gestione ACS.

**Per gestire le credenziali per il servizio di gestione ACS**

1.  Aprire un browser Internet e visitare il portale di gestione di Azure all'indirizzo [http://go.microsoft.com/fwlink/?LinkId=129428](http://go.microsoft.com/fwlink/?LinkID=129428).

2.  Accedere al sito Web utilizzando un Windows Live ID. Se non si dispone di un Windows Live ID, fare clic su Iscriviti ora per crearne uno.

3.  Dopo avere eseguito l'accesso, si verrà reindirizzati alla pagina del portale di gestione. Nel lato inferiore sinistro della pagina fare clic su **Service Bus and Access Control**.

    ![](./media/manage-acs-namespace/ACS1.png)

4.  Per avviare il portale di gestione ACS, fare clic su **Access Control** nell'albero sul lato sinistro selezionare lo spazio dei nomi del servizio ACS che si desidera configurare e quindi fare clic sul pulsante **Access Control Service** sulla barra degli strumenti nella parte superiore della pagina.

    ![](./media/manage-acs-namespace/ACS2.png)

    Verrà visualizzata una schermata simile alla seguente:

    ![](./media/manage-acs-namespace/ACS3.png)

5.  Fare clic su **Management service** nell'albero sul lato sinistro sotto la sezione Administration.

    ![](./media/manage-acs-namespace/ACS14.png)

6.  Fare clic sull'account del servizio di gestione.

    ![](./media/manage-acs-namespace/ACS15.png)

7.  Nella sezione Credentials, utilizzare il pulsante Add per configurare il nuovo certificato o chiave in ACS accanto alla chiave o al certificato esistente prossimo alla scadenza.

8.  L'utente (o i relativi partner) aggiorna la chiave o il certificato corrispondente utilizzato per le richieste di token nelle applicazioni client.

9.  Al termine dell'aggiornamento di tutti i client (o dopo un periodo di tolleranza ragionevole), utilizzare il pulsante Delete per rimuovere la chiave o il certificato precedente.

Per ulteriori informazioni, vedere [Servizio di gestione ACS](http://msdn.microsoft.com/it-it/library/gg185972.aspx).

Se le credenziali sono scadute, ACS genererà le eccezioni seguenti:

<table><tr><td><b>Credenziale</b>
</td>
<td><b>Codice di errore</b>
</td>
<td><b>Messaggio</b>
</td>
<td><b>Azione necessaria per correggere l'errore</b>
</td>
</tr>
<tr>
<td>Chiave simmetrica, password</td>
<td>ACS50006</td>
<td>Verifica della firma non riuscita. Il messaggio potrebbe contenere ulteriori dettagli.</td>
<td  />
</tr>
<tr><td>Certificato X.509</td>
<td>ACS50016</td>
<td>Il certificato X.509 con oggetto '<Nome oggetto certificato> e identificazione personale '<Identificazione personale certificato>' non corrisponde ad alcun certificato configurato.</td>
<td>Verificare che il certificato richiesto sia stato caricato in ACS.</td>
</tr>
</table>

L'elenco delle credenziali per l'account del servizio di gestione ACS è disponibile nella pagina Edit Management Service Account nel portale di gestione ACS.

Certificato del provider di identità WS-Federation
--------------------------------------------------

Il certificato del provider di identità WS-Federation è disponibile tramite i relativi metadati. Quando si configura il provider di identità WS-Federation, ad esempio ADFS, il certificato di firma di WS-Federation viene configurato tramite i metadati di WS-Federation disponibili via URL o come file. Per ulteriori informazioni, vedere [Provider di identità WS-Federation](http://msdn.microsoft.com/it-it/library/gg185933.aspx) e [Procedura: Configurare ADFS 2.0 come provider di identità](http://msdn.microsoft.com/it-it/library/gg185961.aspx). Dopo aver configurato il provider di identità WS-Federation in ACS, utilizzare il servizio di gestione ACS per eseguire una query e verificare la validità dei relativi certificati. Si noti che per ogni caricamento consecutivo di metadati tramite il portale di gestione ACS o il servizio di gestione ACS, le chiavi vengono sostituite.

Di seguito sono riportate le eccezioni generate da ACS quando il certificato è scaduto:

<table><tr><td><b>Codice di errore</b>
</td>
<td><b>Messaggio</b>
</td>
</tr>
<tr>
<td>ACS10001</td>
<td>Si è verificato un errore durante l'elaborazione dell'intestazione SOAP.</td>
</tr>
<tr><td>ACS20001</td>
<td>Si è verificato un errore durante l'elaborazione di una risposta di accesso WS-Federation.</td>
</tr>
<tr><td>ACS50006</td>
<td>Verifica della firma non riuscita. Il messaggio potrebbe contenere ulteriori dettagli.</td>
</tr>
</table>


