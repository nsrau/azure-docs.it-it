4. Passare al pannello gateway dell’app per le API.

	![Fare clic sul gateway](./media/app-service-api-gateway-config-auth/gateway.png)

7. Nel pannello **Gateway** fare clic su **Impostazioni** e quindi su **Identità**.

	![Fare clic su Impostazioni](./media/app-service-api-gateway-config-auth/clicksettingsingateway.png)

	![Fare clic su Identità](./media/app-service-api-gateway-config-auth/clickidentity.png)

	Dal pannello **Identità** è possibile passare ad altri pannelli per configurare l'autenticazione con Azure Active Directory e diversi altri provider.

	![Pannello Identità](./media/app-service-api-gateway-config-auth/identityblade.png)
  
3. Scegliere il provider di identità che si vuole usare e attenersi alla procedura riportata nell'articolo corrispondente per configurare l'app per le API con tale provider. Questi articoli sono stati scritti per le app per dispositivi mobili, ma le procedure sono le stesse per le app per le API. Per alcune procedure è necessario usare il [portale di Azure]

 - [Account Microsoft](../articles/app-service-mobile/app-service-mobile-how-to-configure-microsoft-authentication-preview.md)
 - [Account di accesso di Facebook](../articles/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication-preview.md)
 - [Account di accesso di Twitter](../articles/app-service-mobile/app-service-mobile-how-to-configure-twitter-authentication-preview.md)
 - [Account di accesso di Google](../articles/app-service-mobile/app-service-mobile-how-to-configure-google-authentication-preview.md)
 - [Azure Active Directory](../articles/app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication-preview.md)

Ad esempio, le schermate seguenti mostrano cosa verrà visualizzato nelle pagine del [portale di Azure] e nei pannelli del [portale di anteprima di Azure] dopo aver configurato l'autenticazione di Azure Active Directory.

Nel portale di anteprima di Azure, il pannello **Azure Active Directory** contiene l'**ID client** dell'applicazione creata nella scheda Azure Active Directory del portale di Azure e in **Tenant consentiti** è visualizzato il tenant di Azure Active Directory (ad esempio, "contoso.onmicrosoft.com").

![Pannello Azure Active Directory](./media/app-service-api-gateway-config-auth/tdinaadblade.png)

Nel portale di Azure, la scheda **Configura** dell'applicazione creata nella scheda **Azure Active Directory** contiene l'**URL accesso**, l'**URI ID app** e l'**URL di risposta** del pannello **Azure Active Directory** del portale di anteprima di Azure.

![](./media/app-service-api-gateway-config-auth/oldportal1.png)

![](./media/app-service-api-gateway-config-auth/oldportal2.png)

![](./media/app-service-api-gateway-config-auth/oldportal3.png)

![](./media/app-service-api-gateway-config-auth/oldportal4.png)

L'URL di risposta nell'immagine è ripetuto due volte, una volta con `http:` e una volta con `https:`.

<!---HONumber=Oct15_HO3-->