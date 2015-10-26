1. Fare clic sulla scheda **Applicazioni** nella pagina della directory nel [portale di gestione di Azure](https://manage.windowsazure.com/).
  
2. Fare clic sulla registrazione dell'applicazione integrata.

3. Fare clic su **Configura** nella pagina dell'applicazione, quindi scorrere verso il baso fino alla sezione **chiavi** della pagina.
4. Selezionare la durata di **1 anno** per una nuova chiave. Fare quindi clic su **Salva**. Il valore della nuova chiave sarà visualizzato nel portale.
5. Copiare i valori per **ID client** e **Chiave** mostrati dopo il salvataggio. Si noti che il valore della chiave sarà mostrato una sola volta dopo il salvataggio. 

    ![](./media/mobile-services-generate-aad-app-registration-access-key-rbac/client-id-and-key.png)

6. Scorrere verso il basso fino alla fine della pagina di configurazione dell'applicazione integrata e abilitare l'autorizzazione **Lettura dati directory** per l'applicazione, quindi fare clic su **Salva**.

    ![](./media/mobile-services-generate-aad-app-registration-access-key-rbac/app-perms.png)


7. Nel [portale di gestione di Azure](https://manage.windowsazure.com/) tornare al servizio mobile e fare clic sulla scheda **Configura**. Scorrere verso il basso fino alla sezione **Impostazioni app**, quindi aggiungere le impostazioni seguenti per l'app e fare clic su **Salva**.

    <table border="1"> <tr> <th>Nome impostazione app</th><th>Descrizione</th> </tr> <tr> <td>AAD\_CLIENT\_ID</td><td>ID client copiato dall'app integrata nei passaggi precedenti.</td> </tr> <tr> <td>AAD\_CLIENT\_KEY</td><td>Chiave dell'app generata nell'app integrata di AAD nei passaggi precedenti.</td> </tr> <tr> <td>AAD\_TENANT\_DOMAIN</td><td>Nome di dominio di AAD. Dovrebbe essere simile a "mydomain.onmicrosoft.com"</td> </tr> <tr> <td>AAD\_GROUP\_ID</td><td>ID del gruppo annotato per il gruppo Sales nella sezione precedente</td> </tr> </table><br/>

 
    ![](./media/mobile-services-generate-aad-app-registration-access-key-rbac/aad-app-settings.png)
  

<!---HONumber=Oct15_HO3-->