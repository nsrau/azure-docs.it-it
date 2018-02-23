## <a name="export-an-api-definition"></a>Esportare una definizione API
Si ha una definizione OpenAPI per la funzione, ottenuta in [Creare una definizione OpenAPI per una funzione](../articles/azure-functions/functions-openapi-definition.md). Il passaggio successivo di questo processo consiste nell'esportare la definizione API in modo che PowerApps e Microsoft Flow possano usarla in un'API personalizzata.

> [!IMPORTANT]
> Tenere presente che è necessario essere connessi ad Azure con le stesse credenziali usate per i tenant di PowerApps e Microsoft Flow. In questo modo, Azure può creare l'API personalizzata e renderla disponibile sia per PowerApps che per Microsoft Flow.

1. Nel [portale di Azure](https://portal.azure.com) fare clic sul nome dell'app per le funzioni (ad esempio **function-demo-energy**) > **Funzionalità della piattaforma** > **Definizione API**.

    ![Definizione API](media/functions-export-api-definition/api-definition.png)

1. Fare clic su **Esporta in PowerApps e Microsoft Flow**.

    ![Origine della definizione API](media/functions-export-api-definition/export-api-1.png)

1. Nel riquadro destro usare le impostazioni specificate nella tabella.

    |Impostazione|DESCRIZIONE|
    |--------|------------|
    |**Modalità di esportazione**|Selezionare **Rapida** per generare automaticamente l'API personalizzata. Se si seleziona **Manuale**, la definizione API viene esportata, ma è quindi necessario importarla manualmente in PowerApps e Microsoft Flow. Per altre informazioni, vedere [Esportare in PowerApps e Microsoft Flow](../articles/azure-functions/app-service-export-api-to-powerapps-and-flow.md).|
    |**Environment**|Selezionare l'ambiente in cui salvare l'API personalizzata. Per altre informazioni, vedere [Panoramica degli ambienti (PowerApps)](https://powerapps.microsoft.com/tutorials/environments-overview/) o [Panoramica degli ambienti (Microsoft Flow)](https://us.flow.microsoft.com/documentation/environments-overview-admin/).|
    |**Nome dell'API personalizzata**|Immettere un nome, ad esempio `Turbine Repair`.|
    |**Nome chiave API**|Immettere il nome che i creatori di app e flussi vedranno nell'interfaccia utente dell'API personalizzata. Si noti che l'esempio include informazioni utili.|
 
    ![Esportare in PowerApps e Microsoft Flow](media/functions-export-api-definition/export-api-2.png)

1. Fare clic su **OK**. L'API personalizzata viene ora creata e aggiunta all'ambiente specificato.