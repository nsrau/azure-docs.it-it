Utilizzare innanzitutto la procedura guidata Aggiungi notifica Push di
Visual Studio 2013 per registrare l'app in Windows Store, configurare
il servizio mobile in modo da abilitare le notifiche push, quindi
aggiungere codice all'app per registrare un canale di dispositivi.

1.  Attenersi alla procedura riportata in [Importazione del file di
    impostazioni di pubblicazione in Visual Studio
    2013](/en-us/documentation/articles/mobile-services-windows-how-to-import-publishsettings/),
    se questa operazione non è ancora stata completata, per importare il
    file publisher.settings in Visual Studio.
    
    Non è necessario eseguire questa operazione se è già stato
    utilizzato Visual Studio per creare o gestire servizi mobili nella
    sottoscrizione Azure.

2.  In Visual Studio 2013 aprire Esplora soluzioni, fare clic con il
    pulsante destro del mouse sul progetto, scegliere **Aggiungi** e
    quindi fare clic su **Notifica Push**.
    
    ![mobile-add-push-notifications-vs2013](../includes/media/mobile-services-create-new-push-vs2013/mobile-add-push-notifications-vs2013.png)
    
    Verrà avviata la procedura guidata Aggiungi notifica Push.

3.  Fare clic su **Avanti**, accedere al proprio account Windows Store,
    quindi specificare un nome in **Riserva un nuovo nome** e fare clic
    su **Riserva**.
    
    ![mobile-add-push-notifications-vs2013-2](../includes/media/mobile-services-create-new-push-vs2013/mobile-add-push-notifications-vs2013-2.png)
    
    Verrà creata una nuova registrazione per l'app.

4.  Fare clic sulla nuova registrazione nell'elenco **Nome
    applicazione**, quindi fare clic su **Avanti**.
    
    ![mobile-add-push-notifications-vs2013-3](../includes/media/mobile-services-create-new-push-vs2013/mobile-add-push-notifications-vs2013-3.png)

5.  Nella finestra di dialogo **Seleziona servizio** fare clic sul nome
    del servizio mobile creato con le procedure descritte in
    [Introduzione a Servizi
    mobili](/en-us/develop/mobile/tutorials/get-started/) o
    [Introduzione ai
    dati](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet/),
    quindi fare clic su **Avanti** e su **Fine**.
    
    Il servizio mobile verrà aggiornato per registrare il SID del
    pacchetto dell'app e il segreto client, quindi verrà creata una
    nuova tabella **channels**. A questo punto Servizi mobili è
    configurato per l'utilizzo con i Servizi notifica Push Windows
    (WNS) e consente l'invio di notifiche all'app.
    
    <div  class="dev-callout" markdown="1">
	 </div>
**Nota**




Se l app non è già configurata per connettersi al servizio mobile,
durante la procedura guidata verranno anche completate le attività
di configurazione illustrate in **Introduzione ai dati**.


   
<!-- URLs. -->

<!-- URLs. -->

