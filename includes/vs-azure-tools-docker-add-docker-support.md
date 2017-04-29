1. In **Esplora soluzioni** di Visual Studio fare clic con il pulsante destro del mouse sul progetto e scegliere **Aggiungi > Supporto Docker** dal menu di scelta rapida.
   
    ![Aggiungere il menu di scelta rapida Supporto Docker](media/vs-azure-tools-docker-add-docker-support/docker-support-context-menu.png)
2. L'aggiunta del supporto Docker a un progetto Web ASP.NET Core comporta l'aggiunta al progetto di vari file relativi a Docker, inclusi file di Docker-Compose, script di distribuzione Windows PowerShell e file delle proprietà Docker. 
   
    ![File Docker aggiunti al progetto](media/vs-azure-tools-docker-add-docker-support/docker-files-added.png)

> [!NOTE]
> Se si sta usando [Docker per la versione Beta di Windows](https://beta.docker.com), aprire Properties (Proprietà)\Docker.props, eliminare il valore predefinito e riavviare Visual Studio in modo che il valore abbia effetto.
> 
> ```
> <DockerMachineName Condition="'$(DockerMachineName)'=='' "></DockerMachineName>
> ```
> 

