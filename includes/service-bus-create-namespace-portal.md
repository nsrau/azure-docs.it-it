1. Accedere al portale di Azure classico.

2. Nel pannello di navigazione sinistro del portale fare clic su **Bus di servizio**.

3. Nel riquadro inferiore del portale fare clic su **Crea**.

    ![Selezionare Crea][1]
   
4. Nella finestra di dialogo **Add a new namespace** immettere un nome per lo spazio dei nomi. Verrà effettuato immediatamente un controllo sulla disponibilità del nome.

    ![Nome spazio dei nomi][2]
  
5. Dopo avere verificato che lo spazio dei nomi è disponibile, scegliere il paese o l'area in cui dovrà essere ospitato.

6. Non modificare i valori predefiniti negli altri campi della finestra di dialogo (**Messaggistica** e **Livello Standard**), quindi fare clic sul segno di spunta OK. A questo punto, lo spazio dei nomi verrà creato e abilitato nel sistema. Potrebbero essere necessari alcuni minuti per consentire al sistema di effettuare il provisioning delle risorse per lo spazio dei nomi creato.
 
    ![Creazione completata][3]

###Ottenere le credenziali
1. Nel pannello di navigazione sinistro fare clic sul nodo **Service Bus** per visualizzare l'elenco degli spazi dei nomi disponibili:
 
    ![Selezionare il bus di servizio][4]
  
2. Selezionare lo spazio dei nomi appena creato nell'elenco visualizzato:
 
    ![Selezionare lo spazio dei nomi][5]
 
3. Fare clic su **Connection Information**.

    ![Informazioni di connessione][6]
  
4. Nel riquadro **Accedi a informazioni di connessione** individuare la stringa di connessione che contiene la chiave della firma di accesso condiviso e il nome della chiave.

    ![Accedi a informazioni di connessione][7]
  
5. Prendere nota del valore della chiave oppure copiarlo negli Appunti.

<!--Image references-->

[1]: ./media/service-bus-create-namespace-portal/select-create.png
[2]: ./media/service-bus-create-namespace-portal/namespace-name.png
[3]: ./media/service-bus-create-namespace-portal/created-successfully.png
[4]: ./media/service-bus-create-namespace-portal/select-service-bus.png
[5]: ./media/service-bus-create-namespace-portal/select-namespace.png
[6]: ./media/service-bus-create-namespace-portal/connection-information.png
[7]: ./media/service-bus-create-namespace-portal/access-connection-information.png


<!--Reference style links - using these makes the source content way more readable than using inline links-->
[classic-portal]: https://manage.windowsazure.com

<!---HONumber=AcomDC_0608_2016-->