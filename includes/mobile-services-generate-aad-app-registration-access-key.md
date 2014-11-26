1.  Fare clic sulla scheda **Applicazioni** nella pagina della directory nel [portale di gestione di Azure][portale di gestione di Azure].

2.  Fare clic sulla registrazione dell'applicazione integrata.

3.  Fare clic su **Configura** nella pagina dell'applicazione, quindi scorrere verso il baso fino alla sezione **chiavi** della pagina.
4.  Selezionare la durata di **1 anno** per una nuova chiave. Fare quindi clic su **Salva**. Il valore della nuova chiave sarà visualizzato nel portale.
5.  Copiare i valori per **ID client** e **Chiave** mostrati dopo il salvataggio. Si noti che il valore della chiave sarà mostrato una sola volta dopo il salvataggio.

    ![][ ]

6.  Scorrere verso il basso fino alla fine della pagina di configurazione dell'applicazione integrata e abilitare l'autorizzazione **Lettura dati directory** per l'applicazione, quindi fare clic su **Salva**.

    ![][1]

7.  Nel [portale di gestione di Azure Management][portale di gestione di Azure] tornare al servizio mobile e fare clic sulla scheda **Configura**. Scorrere verso il basso fino alla sezione **Impostazioni app**, quindi aggiungere le impostazioni seguenti per l'app e fare clic su **Salva**.

    | Nome dell'impostazione dell'app | Descrizione                                                                |
    |---------------------------------|----------------------------------------------------------------------------|
    | AAD\_CLIENT\_ID                 | ID client copiato dall'app integrata nei passaggi precedenti.              |
    | AAD\_CLIENT\_KEY                | Chiave dell'app generata nell'app integrata AAD nei passaggi precedenti.   |
    | AAD\_TENANT\_DOMAIN             | Nome di dominio AAD. Dovrebbe essere analogo a "mydomain.onmicrosoft.com". |

    ![][2]

  [portale di gestione di Azure]: https://manage.windowsazure.com/
  [ ]: ./media/mobile-services-generate-aad-app-registration-access-key/client-id-and-key.png
  [1]: ./media/mobile-services-generate-aad-app-registration-access-key/app-perms.png
  [2]: ./media/mobile-services-generate-aad-app-registration-access-key/aad-app-settings.png
