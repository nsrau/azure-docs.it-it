1. Fare clic sulla scheda **Applicazioni** nella pagina della directory nel [portale di gestione di Azure](https://manage.windowsazure.com/).
  
2. Fare clic sulla registrazione dell'applicazione integrata.

3. Fare clic su **Configura** nella pagina dell'applicazione, quindi scorrere verso il basso fino alla sezione **Chiavi** della pagina. 
4. Selezionare la durata di**1 anno** per una nuova chiave. Fare quindi clic su **Salva**. Il valore della nuova chiave sarà visualizzato nel portale.
5. Copiare i valori per **ID client** e **Chiave** mostrati dopo il salvataggio. Si noti che il valore della chiave sarà mostrato una sola volta dopo il salvataggio. 

    ![](./media/mobile-services-generate-aad-app-registration-access-key/client-id-and-key.png)

6. Scorrere verso il basso fino alla fine della pagina di configurazione dell'applicazione integrata e abilitare l'autorizzazione **Lettura dati directory** per l'applicazione, quindi fare clic su **Salva**.

    ![](./media/mobile-services-generate-aad-app-registration-access-key/app-perms.png)


7. Nel [portale di gestione di Azure](https://manage.windowsazure.com/) tornare al servizio mobile e fare clic sulla scheda **Configura**. Scorrere verso il basso fino alla sezione **Impostazioni app**, quindi aggiungere le impostazioni seguenti per l'app e fare clic su **Salva**. 

    <table border="1">
    <tr>
    <th>Nome dell'impostazione dell'app</th><th>Descrizione</th>
    </tr>
    <tr>
    <td>AAD_CLIENT_ID</td><td>ID client copiato dall'app integrata nei passaggi precedenti.</td>
    </tr>
    <tr>
    <td>AAD_CLIENT_KEY</td><td>Chiave dell'app generata nell'app integrata AAD nei passaggi precedenti.</td>
    </tr>
    <tr>
    <td>AAD_TENANT_DOMAIN</td><td>Nome di dominio AAD. Dovrebbe essere analogo a "mydomain.onmicrosoft.com".</td>
    </tr>
    </table><br/>

 
    ![](./media/mobile-services-generate-aad-app-registration-access-key/aad-app-settings.png)
  <!--HONumber=42-->
