Il servizio Backup di Azure presenta insiemi di credenziali di due tipi: l'insieme di credenziali per il backup e l'insieme di credenziali dei servizi di ripristino. L'insieme di credenziali per il backup è stato creato per primo. L'insieme di credenziali dei servizi di ripristino è stato creato in seguito per supportare le distribuzioni Resource Manager espanse. Se non è specificamente necessaria una distribuzione classica, è consigliabile usare distribuzioni Resource Manager.

| **Distribuzione** | **Portale** | **Insieme di credenziali** |
|-----------|------|-----|
|Classico|[Classico](https://manage.windowsazure.com)|Backup|
|Gestione risorse|[Azzurro](https://portal.azure.com)|Servizi di ripristino|

> [AZURE.NOTE] Le credenziali per il backup non consentono di proteggere le soluzioni distribuite con Resource Manager. Tuttavia, per proteggere le VM e i server distribuiti in modo tradizionale, è possibile utilizzare un insieme di credenziali dei servizi di ripristino.

<!---HONumber=AcomDC_0921_2016-->