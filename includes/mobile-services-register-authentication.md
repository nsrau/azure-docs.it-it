
1. Fare clic su Portale di Azure > **Servizi mobili** > servizio mobile > **Dashboard** e prendere nota del valore **URL del servizio mobile**.

2. Registrare l'app con uno o più dei seguenti provider di autenticazione:
   * [Google](mobile-services-how-to-register-google-authentication.md)
   * [Facebook](mobile-services-how-to-register-facebook-authentication.md)
   * [Twitter](mobile-services-how-to-register-twitter-authentication.md)
   * [Microsoft](mobile-services-how-to-register-microsoft-authentication.md)
   * [Azure Active Directory](mobile-services-how-to-register-active-directory-authentication.md). 
   
   Prendere nota dei valori di identità e segreto client generati dal provider. Non distribuire o condividere il segreto client.

3. Nel portale di Azure, fare clic su **Servizi mobili** > il servizio mobile > **Identità** > impostazioni del provider di identità, poi immettere l'ID client e il valore di chiave privata dal provider. 
 
Il servizio mobile e l'app sono ora configurati per funzionare con il provider di autenticazione scelto. Facoltativamente, è possibile ripetere tutti i passaggi per ciascun provider di identità aggiuntivo che si vuole supportare.

    > [AZURE.IMPORTANT] Verify that you've set the correct redirect URI on your identity provider's developer site. As described in the linked instructions for each provider above, the redirect URI may be different for a .NET backend service vs. for a JavaScript backend service. An incorrectly configured redirect URI may result in the login screen not being displayed properly and the app malfunctioning in unexpected ways.

<!---HONumber=September15_HO1-->