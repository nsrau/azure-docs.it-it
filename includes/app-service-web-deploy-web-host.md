### <a name="app-service-plan"></a>Piano di servizio app
Consente di creare il piano di servizio per ospitare l'app Web. Fornire il nome del piano tramite il parametro **hostingPlanName** . Il percorso del piano è identico a quello usato per il gruppo di risorse. Il piano tariffario e le dimensioni dei processi di lavoro sono specificati nei parametri **sku** e **workerSize**.

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('hostingPlanName')]",
      "type": "Microsoft.Web/serverfarms",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "[parameters('sku')]",
        "capacity": "[parameters('workerSize')]"
      },
      "properties": {
        "name": "[parameters('hostingPlanName')]"
      }
    },



<!--HONumber=Nov16_HO3-->


