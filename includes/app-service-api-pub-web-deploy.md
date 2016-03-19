7. Fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Pubblica** per aprire nuovamente la finestra di dialogo di pubblicazione. Il profilo di pubblicazione creato in precedenza deve essere già selezionato. 

9. Fare clic su **Pubblica** per avviare il processo di distribuzione.

	![Distribuzione dell'app per le API](./media/app-service-api-pub-web-deploy/26-5-deployment-success-v3.png)

	La finestra **Attività del servizio app di Azure** mostra l'avanzamento della distribuzione.

	![Notifica sullo stato della finestra Attività del servizio app di Azure](./media/app-service-api-pub-web-deploy/26-5-deployment-success-v4.png)

	Durante questo processo di distribuzione, Visual Studio riavvia automaticamente il *gateway*. Il gateway è un'app Web che gestisce le funzioni amministrative per tutte le app per le API in un gruppo di risorse e deve essere riavviato affinché riconosca le modifiche apportate all'API in un'app per le API o a un file *apiapp.json*.
 
	Se si utilizza un altro metodo per distribuire un'app per le API o se Visual Studio non riesce a riavviare il gateway, potrebbe essere necessario riavviare manualmente il gateway. I passaggi seguenti illustrano come eseguire questa operazione.

1. Nel browser passare al [portale di anteprima di Azure](https://portal.azure.com).

2. Individuare il pannello **App per le API** per l'applicazione di API che è stato distribuito.

	Per informazioni sul pannello **App per le API** e come trovarlo, vedere [Gestione app per le API](../articles/app-service-api/app-service-api-manage-in-portal.md).

4. Scegliere il collegamento **Gateway**.

3. Nel pannello **Gateway** fare clic su **Riavvia**.

	![](./media/app-service-api-pub-web-deploy/restartgateway.png)

<!---HONumber=Oct15_HO3-->