### Piano di servizio app

Consente di creare il piano di servizio per ospitare l'app Web. Fornire il nome del piano tramite il parametro **hostingPlanName**. Il percorso del piano è identico a quello usato per l'app Web. Il piano tariffario e le dimensioni dei processi di lavoro sono specificati nei parametri **sku** e **workerSize**

    {
       "apiVersion":"2015-04-01",
       "name":"[parameters('hostingPlanName')]",
       "type":"Microsoft.Web/serverfarms",
       "location":"[parameters('siteLocation')]",
       "properties":{
         "name":"[parameters('hostingPlanName')]",
         "sku":"[parameters('sku')]",
         "workerSize":"[parameters('workerSize')]",
         "numberOfWorkers":1
       }
    }

<!---HONumber=62-->