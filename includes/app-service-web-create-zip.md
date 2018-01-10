## <a name="create-a-project-zip-file"></a>Creare un file ZIP del progetto

Creare un archivio ZIP per tutti gli elementi del progetto. Il comando seguente usa lo strumento predefinito nel terminale:

```
# Bash
zip -r myAppFiles.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath myAppFiles.zip
``` 

Caricare successivamente il file ZIP in Azure e distribuirlo nel servizio app.