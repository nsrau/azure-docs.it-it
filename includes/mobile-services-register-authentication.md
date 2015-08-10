
* Fare clic su Portale di Azure > **Servizi mobili** > servizio mobile > **Dashboard** e prendere nota del valore **URL del servizio mobile**.

* Registrare l’app con [Google](mobile-services-how-to-register-google-authentication.md), [Facebook](mobile-services-how-to-register-facebook-authentication.md), [Twitter](mobile-services-how-to-register-twitter-authentication.md), [Microsoft](mobile-services-how-to-register-microsoft-authentication.md) o [Azure Active Directory](mobile-services-how-to-register-active-directory-authentication.md). Prendere nota dei valori di identità e segreto client generati dal provider. Non distribuire o condividere il segreto client.

* Fare clic su Portale di Azure > **Servizi mobili** > servizio mobile > **Identità** > impostazioni del provider di identità. Immettere l'ID app e il valore del segreto forniti dal provider. Il servizio mobile e l'app sono ora configurati per funzionare con il provider di autenticazione scelto. Facoltativamente, è possibile ripetere tutti i passaggi per ciascun provider di identità aggiuntivo che si vuole supportare.

    > [AZURE.IMPORTANT]Verificare di aver impostato l'URI di reindirizzamento corretto nel sito per sviluppatori del provider di identità. Come descritto nelle istruzioni collegate per ogni provider riportato sopra, l'URI di reindirizzamento può essere diverso per un servizio back-end .NET e per un servizio back-end JavaScript. Se l'URI di reindirizzamento non è configurato in modo corretto, è possibile che la schermata di accesso non venga visualizzata correttamente e che si verifichino anomalie impreviste nel funzionamento dell'applicazione.

<!---HONumber=July15_HO5-->