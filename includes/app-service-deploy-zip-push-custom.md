## <a name="deployment-customization"></a>Personalizzazione di distribuzione

Il processo di distribuzione presuppone che il file ZIP che si esegue il push contenga un'app ready to run. Per impostazione predefinita, non vengono eseguiti personalizzazioni. Aggiungere quanto segue per abilitare gli stessi processi di compilazione che si ottiene con l'integrazione continua, le impostazioni dell'applicazione:

    SCM_DO_BUILD_DURING_DEPLOYMENT=true 

Quando si utilizza distribuzione push con estensione zip, questa impostazione è **false** per impostazione predefinita. Il valore predefinito è **true** per le distribuzioni di integrazione continua. Se impostato su **true**, le impostazioni relative alla distribuzione vengono utilizzate durante la distribuzione. È possibile configurare queste impostazioni come impostazioni dell'app o in un file di configurazione .deployment che si trova nella radice del file ZIP. Per ulteriori informazioni, vedere [Repository e impostazioni relative alla distribuzione](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings) in riferimenti alla distribuzione.