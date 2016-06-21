1. Accedere al portale di Azure classico.

2. Nel pannello di navigazione sinistro del portale fare clic su **Bus di servizio**.

3. Nel riquadro inferiore del portale fare clic su **Crea**.

    ![Selezionare Crea][select-create]
   
4. Nella finestra di dialogo **Add a new namespace** immettere un nome per lo spazio dei nomi. Verrà effettuato immediatamente un controllo sulla disponibilità del nome.

    ![Nome spazio dei nomi][namespace-name]
  
5. Dopo avere verificato che lo spazio dei nomi è disponibile, scegliere il paese o l'area in cui dovrà essere ospitato.

6. Non modificare i valori predefiniti negli altri campi della finestra di dialogo (**Messaggistica** e **Livello Standard**), quindi fare clic sul segno di spunta OK. A questo punto, lo spazio dei nomi verrà creato e abilitato nel sistema. Potrebbero essere necessari alcuni minuti per consentire al sistema di effettuare il provisioning delle risorse per lo spazio dei nomi creato.
 
    ![Creazione completata][created-successfully]

###Ottenere le credenziali
1. Nel pannello di navigazione sinistro fare clic sul nodo **Service Bus** per visualizzare l'elenco degli spazi dei nomi disponibili:
 
    ![Selezionare il bus di servizio][select-service-bus]
  
2. Selezionare lo spazio dei nomi appena creato nell'elenco visualizzato:
 
    ![Selezionare lo spazio dei nomi][select-namespace]
 
3. Fare clic su **Connection Information**.

    ![Informazioni di connessione][connection-information]
  
4. Nel riquadro **Accedi a informazioni di connessione** individuare la stringa di connessione che contiene la chiave della firma di accesso condiviso e il nome della chiave.

    ![Accedi a informazioni di connessione][access-connection-information]
  
5. Prendere nota del valore della chiave oppure copiarlo negli Appunti.

<!--Image references-->

[select-create]: ./media/service-bus-create-namespace-portal/select-create.png
[namespace-name]: ./media/service-bus-create-namespace-portal/namespace-name.png
[created-successfully]: ./media/service-bus-create-namespace-portal/created-successfully.png
[select-service-bus]: ./media/service-bus-create-namespace-portal/select-service-bus.png
[select-namespace]: ./media/service-bus-create-namespace-portal/select-namespace.png
[connection-information]: ./media/service-bus-create-namespace-portal/connection-information.png
[access-connection-information]: ./media/service-bus-create-namespace-portal/access-connection-information.png


<!--Reference style links - using these makes the source content way more readable than using inline links-->
[classic-portal]: https://manage.windowsazure.com

<!---HONumber=AcomDC_0615_2016-->